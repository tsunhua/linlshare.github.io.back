## Kotlin的特性

1. time
2. streams
3. try-with-resources
4. 函数扩展，给types、classes或者interfaces新增方法
5. null safe
6. 不需要new，后缀声明类型
7. 自动转换有getters和setters综合属性的类型，例如自动替换getDay()为day，看起来像个field，但实际上是property-getter和setter的概念的融合
8. 函数表达式lambdas，it：单个参数的隐式名称
9. Higher-order函数，一个参数式函数或者返回时函数的函数
10. 扩展函数表达式 = 扩展函数 + 函数表达式 + 高阶函数

 ```kotlin
fun SQLiteDatabase.inTransaction(func: (SQLiteDatabase) -> Unit) {
  beginTransaction()
  try {
    func(this)
    setTransactionSuccessful()
  } finally {
    endTransaction()
  }
}

db.inTransaction {
  it.db.delete("users", "first_name = ?", arrayOf("Jake"))
}
 ```

11. in-line函数
12. Anko 定义UI