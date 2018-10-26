```shell
# ${android_sdk}/tools/proguard/proguard-android.txt
# Understand the @Keep support annotation.
-keep class android.support.annotation.Keep

-keep @android.support.annotation.Keep class * {*;}

-keepclasseswithmembers class * {
    @android.support.annotation.Keep <methods>;
}

-keepclasseswithmembers class * {
    @android.support.annotation.Keep <fields>;
}

-keepclasseswithmembers class * {
    @android.support.annotation.Keep <init>(...);
}
```



```java
// android/support/annotation/Keep.java
/**
 * Denotes that the annotated element should not be removed when
 * the code is minified at build time. This is typically used
 * on methods and classes that are accessed only via reflection
 * so a compiler may think that the code is unused.
 * <p>
 * Example:
 * <pre><code>
 *  @Keep
 *  public void foo() {
 *      ...
 *  }
 * </code></pre>
 */
@Retention(CLASS)
@Target({PACKAGE,TYPE,ANNOTATION_TYPE,CONSTRUCTOR,METHOD,FIELD})
public @interface Keep {
}
```



// https://www.guardsquare.com/en/products/proguard/manual/usage

### File filters

 Like general [filters](https://www.guardsquare.com/proguard/manual/usage#filters), a file filter is a comma-separated list of file names that can contain wildcards. Only files with matching file names are read (in the case of input jars), or written (in the case of output jars). The following wildcards are supported:

| **?**   | matches any single character in a file name.                 |
| ------- | ------------------------------------------------------------ |
| *****   | matches any part of a filename not containing the directory separator. |
| ***\*** | matches any part of a filename, possibly containing any number of directory separators. |

For example, "`java/**.class,javax/**.class`" matches all class files in the `java` and `javax`.

Furthermore, a file name can be preceded by an exclamation mark '**!**' to *exclude* the file name from further attempts to match with *subsequent* file names.

For example, "`!**.gif,images/**`" matches all files in the `images` directory, except gif files.



###  Class specifications

 A class specification is a template of classes and class members (fields and methods). It is used in the various `-keep` options and in the `-assumenosideeffects` option. The corresponding option is only applied to classes and class members that match the template.

The template was designed to look very Java-like, with some extensions for wildcards. To get a feel for the syntax, you should probably look at the [examples](https://www.guardsquare.com/proguard/manual/examples), but this is an attempt at a complete formal definition:

```java
[@annotationtype] [[!]public|final|abstract|@ ...] [!]interface|class|enum classname
    [extends|implements [@annotationtype] classname]
[{
    [@annotationtype] [[!]public|private|protected|static|volatile|transient ...] <fields> |
                                                                      (fieldtype fieldname);
    [@annotationtype] [[!]public|private|protected|static|synchronized|native|abstract|strictfp ...] <methods> |
                                                                                           <init>(argumenttype,...) |
                                                                                           classname(argumenttype,...) |
                                                                                           (returntype methodname(argumenttype,...));
    [@annotationtype] [[!]public|private|protected|static ... ] *;
    ...
}]
```

Square brackets "[ ]" mean that their contents are optional. Ellipsis dots "..." mean that any number of the preceding items may be specified. A vertical bar "|" delimits two alternatives. Non-bold parentheses "()" just group parts of the specification that belong together. The indentation tries to clarify the intended meaning, but white-space is irrelevant in actual configuration files.

- The `class` keyword refers to any interface or class. The `interface` keyword restricts matches to interface classes. The `enum` keyword restricts matches to enumeration classes. Preceding the `interface` or `enum` keywords by a `!` restricts matches to classes that are not interfaces or enumerations, respectively.

- Every classname must be fully qualified, e.g. java.lang.String.Inner classes are separated by a dollar sign "\$", e.g. `java.lang.Thread$State`

  . Class names may be specified as regular expressions containing the following wildcards:

  | **?**   | matches any single character in a class name, but not the package separator. For example, "`com.example.Test?`" matches "`com.example.Test1`" and "`com.example.Test2`", but not "`com.example.Test12`". |
  | ------- | ------------------------------------------------------------ |
  | *****   | matches any part of a class name not containing the package separator. For example, "`com.example.*Test*`" matches "`com.example.Test`" and "`com.example.YourTestApplication`", but not "`com.example.mysubpackage.MyTest`". Or, more generally, "`com.example.*`" matches all classes in "`com.example`", but not in its subpackages. |
  | ***\*** | matches any part of a class name, possibly containing any number of package separators. For example, "`**.Test`" matches all `Test` classes in all packages except the root package. Or, "`com.example.**`" matches all classes in "`com.example`" and in its subpackages. |
  | **<n>** | matches the *n*'th matched wildcard in the same option. For example, "`com.example.*Foo<1>`" matches "`com.example.BarFooBar`". |

  For additional flexibility, class names can actually be comma-separated lists of class names, with optional **!** negators, just like file name filters. This notation doesn't look very Java-like, so it should be used with moderation.

  For convenience and for backward compatibility, the class name **\*** refers to any class, irrespective of its package.