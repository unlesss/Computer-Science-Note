
# 区分一下length和length()

length不是方法，是属性，数组的属性；
length()是字符串String的一个方法；

进入length()方法看一下实现

private final char value[];
 
public int length() {
        return value.length;
    }
注释中的解释是

@return     the length of the sequence of characters represented by this object.

即由该对象所代表的字符序列的长度，所以归根结底最后要找的还是length这个底层的属性；

# size()方法，是List集合的一个方法

在List的方法中，是没有length()方法的；

也看一段ArrayList的源码

private final E[] a;
 
ArrayList(E[] array) {
       if (array==null)
             throw new NullPointerException();
       a = array;
}
 
public int size() {
       return a.length;
}
由这段就可以看出list的底层实现其实就是数组，size()方法最后要找的其实还是数组的length属性；

另外，除了List，Set和Map也有size()方法，所以准确说size()方法是针对集合而言。
