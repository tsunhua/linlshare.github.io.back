---
title: MVP架构下解决 RxJava 自动解绑问题
date: 2018-9-10 17:25:00
tags: [Android]
comments: true
---

## 背景

MVP 模式下使用 RxJava 处理网络访问的回调，当数据返回时 Presenter 调用绑定的 View 的方法。

定义 BasePresenter 如下：

```java
public class BasePresenter<T extends MvpView> implements Presenter<T> {

  private T mMvpView;

  @Override
  public void attachView(T mvpView) {
    mMvpView = mvpView;
  }

  @Override
  public void detachView() {
    mMvpView = null;
  }

  public boolean isViewAttached() {
    return mMvpView != null;
  }

  public T getMvpView() {
    return mMvpView;
  }
}
```

定义 MvpView 如下：

```java
public interface MvpView {
}
```

举一个具体的实现，有记录页为 RecordActivity，定义如下：

```java
public class RecordRecordActivity extends BaseActivity
    implements RecordMvpView {
      @Inject
  	  RecordPresenter presenter;
      @Override
      protected void onCreate(Bundle savedInstanceState) {
          super.onCreate(savedInstanceState);
          //...
          presenter.attachView(this);
          //...
          onRefresh();
      }
      
      public void onRefresh(){
          presenter.queryReocrd();
	  }
      
      @Override
      protected void onDestroy() {
        presenter.detachView();
        super.onDestroy();
      }
        @Override
      public void showRecord(List<RecordResponse> data) {
        //...
      }
}
```

RecordMvpView 定义如下：

```java
public interface RecordMvpView extends MvpView {
	void showRecord(List<RecordResponse> data);
}
```

RecordPresenter 的定义如下：

```java
public class RecordPresenter extends BasePresenter<RecordMvpView> {

  private DataManager dataManager;
  private Disposable disposable;

  @Inject
  public RecordPresenter(DataManager dataManager) {
    this.dataManager = dataManager;
  }

  public void queryRecord(int page, int count) {
    RxUtil.dispose(disposable);
    dataManager.queryRecord(page, count)
               .subscribeOn(Schedulers.io())
               .observeOn(AndroidSchedulers.mainThread())
               .subscribe(new SingleObserver<List<RecordResponse>>() {
                 @Override
                 public void onSubscribe(Disposable d) {
                   disposable = d;
                 }

                 @Override
                 public void onSuccess(List<RecordResponse> resp) {
                   getMvpView().showRecord(resp);
                 }

                 @Override
                 public void onError(Throwable e) {
                   Timber.e(e);
                   getMvpView().showRecord(null);
                 }
               });
  }
}
```

以上实现具有一个重大问题，当 Activity 处于 destroy 状态时调用 onRefresh 方法去加载数据，会导致 Presenter 中处理数据返回后调用 getMvpView 方法返回 null，从而导致 NPE。抑或，在 create 状态请求的数据在未返回前 Activity 就进入了 destroy 状态从而导致 NPE。

## 解决方案

### （1）最简单的也是最复杂的解决方案

在调用 getMvpView 前进行判空，即：

```java
if(isViewAttached()){
  getMvpView().showRecord(resp);
}
```

是不是很简单？是的，看起来简单，其实是最复杂的，应该它需要在每个回调的地方小心翼翼地包上这层判断，工作量大还容易出错，代码也不好看。更关键的是，**它到底是执行了，并没有在 View 销毁后立即停止订阅**。

### （2）使用第三方库 RxLifecycle 或 AutoDispose

这两个都是较出名的用于解决 RxJava 与Android 生命周期问题的第三方库。可以自行 Github 一下。

**RxLifecycle**

This library allows one to automatically complete sequences based on a second lifecycle stream.

This capability is useful in Android, where incomplete subscriptions can cause memory leaks.

该库允许在接收到第二个生命周期流时自动结束订阅。

此种能力对于解决因未完成的订阅导致的Android 内存泄漏问题很有用。

RxLifecycle 的局限性：

1. **需要继承自 RxActivity 或 RxFragment 等；**

2. **其核心步骤需要 RxActivity 或 RxFragment 的引用。**

   ```java
   .compose(this.<T>bindUntilEvent(ActivityEvent.PAUSE))
   ```


**AutoDispose**

AutoDispose is an RxJava 2 tool for automatically binding the execution of RxJava 2 streams to a provided scope via disposal/cancellation.

AutoDispose 是 RxJava2 中的一款工具，能通过解绑或取消操作，使得 RxJava2 流执行到在给定的域中为止。

其受 RxLifecycle 启发。

优势：

1. **将生命周期相关的从 Activity 或 Fragment 中分离出来，独立成 LifecycleOwner，可扩展。**

### （3）结合项目情况自定义解决方案

RxLifecycle 的原理是：

1. BehaviorSubject 在订阅后会发送前一个数据值；
2. ObservableTransformer、SingleTransformer 等等可以对整个流进行操作；
3. takeUntil 操作符可以在第二个被观察者发送事件时自动停止订阅。

结合 MVP 架构和 RxLifecycle 的原理，我的解决方案是：

1. **使用 BehaviorSubject 发送 View 的绑定情况；**
2. **在所有跟 View 相关的流中使用 compose 操作符，compose 一个自定义的 LifecycleTransformer 操作整个流，使用 takeUntil 操作符在观察到 BehaviorSubject 发送解绑消息后使用停止订阅。**

具体代码实现如下：

BasePresenter 修改为：

```java
public class BasePresenter<T extends MvpView> implements Presenter<T> {

  private T mMvpView;
  private CompositeDisposable compositeDisposable = new CompositeDisposable();
  private BehaviorSubject<Boolean> behaviorSubject = BehaviorSubject.createDefault(false);

  @Override
  public void attachView(T mvpView) {
    mMvpView = mvpView;
    behaviorSubject.subscribe();
    behaviorSubject.onNext(true); // TRUE 表示 View 绑定了
  }

  @Override
  public void detachView() {
    mMvpView = null;
    behaviorSubject.onNext(false); // FALSE 表示 View 解绑了
    compositeDisposable.clear();
  }

  public void addDisposable(Disposable... disposables) {
    for (Disposable d : disposables) {
      compositeDisposable.add(d);
    }
  }

  protected void deleteDispoable(Disposable disposable) {
    compositeDisposable.delete(disposable);
  }

  protected <R> LifecycleTransformer<R> bindLifeCycle() {
    return new LifecycleTransformer<>(behaviorSubject, this);
  }

  public boolean isViewAttached() {
    return mMvpView != null;
  }

  public T getMvpView() {
    return mMvpView;
  }
}
```

其中 LifecycleTransformer 定义为：

```java
public final class LifecycleTransformer<T>
    implements ObservableTransformer<T, T>, SingleTransformer<T, T>, MaybeTransformer<T, T>, CompletableTransformer {
  private final Observable<Boolean> observable;
  private BasePresenter presenter;

  public LifecycleTransformer(Observable<Boolean> observable, BasePresenter presenter) {
    this.observable = observable;
    this.presenter = presenter;
  }

  @Override
  public ObservableSource<T> apply(Observable<T> upstream) {
    return upstream.takeUntil(getFilterFalseObservable()) //当收到 View 解绑消息时自动解除订阅
                   .subscribeOn(Schedulers.io())
                   .observeOn(AndroidSchedulers.mainThread())
                   .doOnSubscribe(disposable -> {
                     if (!presenter.isViewAttached()) { // 当订阅时，若 View 解绑则自动解除订阅
                       Timber.v("dispose");
                       disposable.dispose();
                       return;
                     }
                     presenter.addDisposable(disposable);
                   });
  }

  @Override
  public SingleSource<T> apply(Single<T> upstream) {
    return upstream.takeUntil(getFilterFalseObservable().firstOrError())
                   .subscribeOn(Schedulers.io())
                   .observeOn(AndroidSchedulers.mainThread())
                   .doOnSubscribe(disposable -> {
                     if (!presenter.isViewAttached()) {
                       Timber.v("dispose");
                       disposable.dispose();
                       return;
                     }
                     presenter.addDisposable(disposable);
                   });

  }

  @Override
  public CompletableSource apply(Completable upstream) {
    return Completable.ambArray(upstream,
                                getFilterFalseObservable().flatMapCompletable(CANCEL_COMPLETABLE))
                      .subscribeOn(Schedulers.io())
                      .observeOn(AndroidSchedulers.mainThread())
                      .doOnSubscribe(disposable -> {
                        if (!presenter.isViewAttached()) {
                          Timber.v("dispose");
                          disposable.dispose();
                          return;
                        }
                        presenter.addDisposable(disposable);
                      });
  }

  @Override
  public MaybeSource<T> apply(Maybe<T> upstream) {
    return upstream.takeUntil(getFilterFalseObservable().firstElement())
                   .subscribeOn(Schedulers.io())
                   .observeOn(AndroidSchedulers.mainThread())
                   .doOnSubscribe(disposable -> {
                     if (!presenter.isViewAttached()) {
                       Timber.v("dispose");
                       disposable.dispose();
                       return;
                     }
                     presenter.addDisposable(disposable);
                   });

  }

  private Observable<Boolean> getFilterFalseObservable() {
    return observable.filter(aBoolean -> !aBoolean);
  }

  private static final Function<Object, Completable> CANCEL_COMPLETABLE =
      ignore -> Completable.error(new CancellationException());

}
```

这样原先的 RecordPresenter 就可以修改为：

```java
public class RecordPresenter extends BasePresenter<RecordMvpView> {

  private DataManager dataManager;

  @Inject
  public RecordPresenter(DataManager dataManager) {
    this.dataManager = dataManager;
  }

  public void queryRecord(int page, int count) {
    dataManager.queryRecord(page, count)
               .compose(bindLifeCycle()) // 关键代码
               .subscribe(new LifecycleSingleObserver<List<RecordResponse>>() {
                 @Override
                 public void onSuccess(List<RecordResponse> resp) {
                   Timber.i("onSuccess --------------");
                   getMvpView().showRecord(resp);
                 }

                 @Override
                 public void onError(Throwable e) {
                   Timber.i(e, "onError --------------");
                   getMvpView().showRecord(null);
                 }
               });
  }
}
```

其中 LifecycleSingleObserver 是为了简化 SingleObserver 引入的，定义如下：

```java
public abstract class LifecycleSingleObserver<T> implements SingleObserver<T> {
  @Override
  public void onSubscribe(Disposable d) {
  }
}
```

至此，关于订阅的绑定及生命周期的问题已经在基类进行解决，具体使用时的代码大大简化（至少少了 5 行代码），只需加上一句 `.compose(bindLifeCycle()) ` 即可。