# java随笔

#### i++和++i的区别

- i++返回原来的值，++i返回加1后的值（a = i++ ->a = i; i = i + 1.  a = ++i ->i = i + 1, a = i;)

- i++不能作为左值，++i可以

- i++前者是先赋值，后自增；++i后者是先自增，后赋值

- 例如：i = 1；i = i++；此时i还是1；

- ```java
  public static void main(String[] args) {
      int i = 1;
      i = i++;  //i = 1
      int j = i++;  //j = 1
      int k = i + ++i * i++;  // 2 + 3 * 3
      System.out.println("i:" + i);
      System.out.println("j:" + j);
      System.out.println("k:" + k);
  }  // i = 4, j = 1, k = 11
  ```