# ScalaQuestion
问题一: 伴生对象构造函数使用private修饰, 编译后变为public
=========================================================
伴生对象示例:<br>
```scala
class Marker private(val color: String){
  println("Creating..." + this)
  override def toString(): String = "marker color " + color
}

object Marker {
  private val markers = Map(
    "red" -> new Marker("red"),
    "blue" -> new Marker("blue"),
    "green" -> new Marker("green")
  )

  def getMarker(color: String) = if (markers.contains(color))  markers(color) else null
}
```
如上代码所示, 已将`class Marker`构造函数声明为private类型. 我原本以为经过编译后的class也是`private`类型, 可事实不是这样.

```
javap -c Marker.class
```

执行操作后, 发现:
```java
 public com.fuxiao.Marker(java.lang.String);
    Code:
       0: aload_0
       1: aload_1
       2: putfield      #23                 // Field color:Ljava/lang/String;
       5: aload_0
       6: invokespecial #44                 // Method java/lang/Object."<init>":()V
       9: getstatic     #49                 // Field scala/Predef$.MODULE$:Lscala/Predef$;
      12: new           #28                 // class scala/collection/mutable/StringBuilder
      15: dup
      16: invokespecial #32                 // Method scala/collection/mutable/StringBuilder."<init>":()V
      19: ldc           #51                 // String Creating...
      21: invokevirtual #38                 // Method scala/collection/mutable/StringBuilder.append:(Ljava/lang/Object;)Lscala/collection/mutable/StringBuilder;
      24: aload_0
      25: invokevirtual #38                 // Method scala/collection/mutable/StringBuilder.append:(Ljava/lang/Object;)Lscala/collection/mutable/StringBuilder;
      28: invokevirtual #42                 // Method scala/collection/mutable/StringBuilder.toString:()Ljava/lang/String;
      31: invokevirtual #55                 // Method scala/Predef$.println:(Ljava/lang/Object;)V
      34: return
```
Marker的构造函数还是`public`类型. 为了验证我的想法, 在Java代码下调用其构造函数. 
```java
public class Test {
    public static void main(String[] args) {
        Marker m = new Marker("test");
        System.out.println(m.toString());
    }
}
```
编译成功, 可以运行. 所以我这里就产生疑问了? 是Scala本身的设计就是这样, 还是说Scala的Bug?
