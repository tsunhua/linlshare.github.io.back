---
title: 从序列化到 Gson 排错记录
date: 2018-8-10 23:15:00
tags: [Java,Gson]
comments: true
---

### 写在前面

#### 序列化、反序列化和持久化

序列化的目的在于对象中携带的数据之传输（进程间通信、客户端 - 服务器通信等），反序列化的目的在于将传输过来的数据重组成对象进行调用，持久化的目的在于将对象中携带的数据持久化保存在文件或数据库中，序列化和持久化的区别就在于此。

序列化的媒介有：对象输入输出流、JSON 字符串，以及 Android 独有的 Parcel。下面简述这几种方式。

#### 第一种：对象输入输出流

```java
// Dog
public class Dog implements java.io.Serializable{
   public String name;
}
// 序列化
FileOutputStream fos = new FileOutputStream("adog.tmp");
ObjectOutputStream oos = new ObjectOutputStream(fos);
Dog dog = new Dog();
dog.name = "aDog";
oos.writeObject(dog);
oos.close();

// 反序列化
FileInputStream fis = new FileInputStream("adog.tmp");
ObjectInputStream ois = new ObjectInputStream(fis);
Dog dog = (Dog) ois.readObject();
ois.close();
```

#### 第二种：JSON 字符串

以广为使用的 GSON 库为例，GSON 是一个很常用的序列化和反序列化对象的 JSON 库。其最简单的使用方式是：

```java
// Cat
public class Cat {
   public String name;
}

Gson gson = new Gson();

// 序列化对象
Cat cat = new Cat();
cat.name = "aCat";
String catStr = gson.toJson(cat);

// 反序列化对象
Cat cat = gson.fromJson(catStr, Cat.Class);
```

#### 第三种：Parcel

Android 中的 Parcel，官方的文档中表明是为了提高 IPC 效率而生的，不建议进行持久化，因为一旦类的成员变量发生改变，旧的数据将不再可读。Parcel 对象存在于一段多进程共享的内存中，没有进行文件的读写操作，高效是显而易见的。

```java
// Hawk
import android.os.Parcel;
import android.os.Parcelable;

public class Hawk implements Parcelable {
  public String name;

  protected Hawk(Parcel in) {
    // 反序列化
    name = in.readString();
  }

  public static final Creator<Hawk> CREATOR = new Creator<Hawk>() {
    @Override
    public Hawk createFromParcel(Parcel in) {
      return new Hawk(in);
    }

    @Override
    public Hawk[] newArray(int size) {
      return new Hawk[size];
    }
  };

  @Override
  public int describeContents() {
    return 0;
  }

  @Override
  public void writeToParcel(Parcel dest, int flags) {
    // 序列化
    dest.writeString(name);
  }
}
```

Github 上提供了[一种方式](https://gist.github.com/jacklt/6711967)将 Parcelable 对象字节化和反字节化，以便进行持久化，摘取如下：

```java
public class ParcelableUtil {
    public static byte[] marshall(Parcelable parceable) {
        Parcel parcel = Parcel.obtain();
        parceable.writeToParcel(parcel, 0);
        byte[] bytes = parcel.marshall();
        parcel.recycle(); // not sure if needed or a good idea
        return bytes;
    }

    public static <T extends Parcelable> T unmarshall(byte[] bytes, Parcelable.Creator<T> creator) {
        Parcel parcel = unmarshall(bytes);
        return creator.createFromParcel(parcel);
    }

    public static Parcel unmarshall(byte[] bytes) {
        Parcel parcel = Parcel.obtain();
        parcel.unmarshall(bytes, 0, bytes.length);
        parcel.setDataPosition(0); // this is extremely important!
        return parcel;
    }
}
```

对象是千奇百怪的，导致我们序列化和反序列化的时候可能会出现一些不可思议的错误信息。下面就我遇到的一些情况为例，书写排错的血泪史。

### 1. AssertionError：Impossible

#### 背景

在对某广告 SDK 的 Ad 对象进行序列化时出现该错误，瞬间惊呆了，序列化一个实现了简单的对象怎么不可能了？