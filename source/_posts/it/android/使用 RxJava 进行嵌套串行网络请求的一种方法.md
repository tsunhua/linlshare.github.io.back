## 需求

有这样一个列表数据，它包含了商店+订单的信息，获取订单列表时，订单实体中会包含商店的 ID，而列表显示时需要商店的名称和 logo，这时候就需要进行嵌套串行网络请求了。

## 关键词

`flatMap` 、`缓存` 、`Retrofit`、`RxJava`

## 动手

### （1）使用 Retrofit 定义网络接口

```java
// RemoteService.java
// 请求订单信息
@POST("/order/v1/order_history")
Single<List<OrderResponse>> queryOrderList(@Body FetchOrderHistoryRequest request);
// 请求商店信息
@POST("/store/v1/store_query")
Single<StoreResponse> queryStore(@Body StoreQueryRequest request);
```

### （2）使用 DataManager 管理数据

```java
// DataManager.java
// 请求订单信息
public Single<List<OrderResponse>> queryOrderList(String status) {
    FetchOrderHistoryRequest request = new FetchOrderHistoryRequest();
    request.setStatus(status);
    return mRemoteService.queryOrderList(request);
}

// 请求商店信息，并缓存 5min，如果不作缓存可能导致多次重复请求同一数据
public static final int DEFAULT_CACHE_TIME_MILLIS = 5 * 60 * 1000; // 5min

public Single<StoreResponse> queryStore(String storeId) {
    String storeKey = PrefConstant.getStoreKey(storeId);
    String storeJson = mMemberPref.getString(storeKey, null);
    Single<StoreResponse> storeSingle;
    if (!TextUtils.isEmpty(storeJson)) {
        storeSingle = Single.just(Json.fromJson(storeJson, StoreResponse.class));
    } else {
        StoreQueryRequest request = new StoreQueryRequest();
        request.setId(storeId);
        storeSingle = mRemoteService.queryStore(request)
            .doOnSuccess(storeResponse -> mMemberPref.put(storeKey,
                                                          Json.toJson(
                                                              storeResponse),
                                                          DEFAULT_CACHE_TIME_MILLIS));
    }
    return storeSingle;
}
```

注：

1. `mMemberPref` 是我写的一个使用 `SharedPreferences` 进行数据缓存的类，详情查看 [Pref.java](https://gist.github.com/LinLshare/39ecd935e980fe1f4a729bb24b43a639)



### （3）多次FlatMap

```java
dataManager.queryOrderList(status)
           .flatMapObservable((Function<List<OrderResponse>, ObservableSource<OrderResponse>>) Observable::fromIterable)
           .flatMap((Function<OrderResponse, ObservableSource<OrderHolder>>) orderResponse -> {
             OrderHolder orderHolder = new OrderHolder();
             orderHolder.setOrder(orderResponse);
             return dataManager.queryStore(orderResponse.getStoreId())
                               .flatMapObservable((Function<StoreResponse, ObservableSource<OrderHolder>>) storeResponse -> {
                                 orderHolder.setStore(storeResponse);
                                 return Observable.just(orderHolder);
                               });
           })
           .toList()
           .observeOn(AndroidSchedulers.mainThread())
           .subscribeOn(Schedulers.io())
           .subscribe(new SingleObserver<List<OrderHolder>>() {
             @Override
             public void onSubscribe(Disposable d) {
               disposable = d;
             }

             @Override
             public void onSuccess(List<OrderHolder> orderHolders) {
               if (orderHolders != null && !orderHolders.isEmpty()) {
                 getMvpView().showOrderList(orderHolders);
               } else {
                 getMvpView().showEmpty();
               }
             }

             @Override
             public void onError(Throwable e) {
               Timber.e(e);
               getMvpView().showError();
             }
           });
  }
```

说明：

1. 第一次 `flatMapObservable` ，将 `List<OrderResponse>` 转为 `ObservableSource<OrderResponse>`；
2. 第二次 `flatMap`，将 `OrderResponse` 转为 `ObservableSource<OrderHolder>`；
3. 第三次  `flatMapObservable` ，将 `StoreResponse` 合并到 `OrderHolder`，再转为`ObservableSource<OrderHolder>`。