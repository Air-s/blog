---
layout: post
title: CODE Q&A Solved 一些有趣的问答（IO 与 集合）
tags: [基础]
excerpt_separator: <!--more-->
---

不想干正事时的好去处

<!--more-->

### I/O

1. [java java按行写入文件 java读取文件第一行 - 如何使用Java逐行读取大型文本文件？](https://code.i-harness.com/zh-CN/q/598b51)

   > 其中一个回答记录并测试了[10种不同的方式来读取Java文件](https://funnelgarden.com/java_read_file/) ，可参考

2. [【官方示例】File IO（NIO.2）：（不同场景下）读、写并创建文件](https://blog.csdn.net/hhx0626/article/details/78183928)

   > [官方文档](https://docs.oracle.com/javase/tutorial/essential/io/file.html) 出品，必属精品。介绍了不同场景下应该使用何种 API

3. [java 判断文件夹是否存在 java删除文件 - 如何检查Java中是否存在文件？](https://code.i-harness.com/zh-CN/q/1bb861)

4. [java inputstream读取字符串 inputstream转为string - 如何在Java中将字符串转换为InputStream？](https://code.i-harness.com/zh-CN/q/bef62)

5. [如何将 **java.io.File** 转换为 **byte[]** ？](https://code.i-harness.com/zh-CN/q/d1b64)

6. [读取文件中的特定的行？](https://code.i-harness.com/zh-CN/q/234a34)

7. [java遍历目录下的所有文件](https://code.i-harness.com/zh-CN/q/1c25d0)

8. [java删除指定目录中所有文件](https://code.i-harness.com/zh-CN/q/be4ff)

9. [Java InputStream 的内容写入 OutputStream](https://code.i-harness.com/zh-CN/q/a895)

   > **Spring 框架**提供不关闭流的 **StreamUtils.copy(in, out)** 和复制后关闭流的 **FileCopyUtils.copy(in, out)**

10. [将输出从java.io.OutputStream传递到Java中的String的最佳方式是什么？](https://code.i-harness.com/zh-CN/q/34f3e)

11. [使用scanner java的scanner类与换行符问题](https://code.i-harness.com/zh-CN/q/c7ebdd)

12. [如何在Java中获得依赖于平台的换行符？](https://code.i-harness.com/zh-CN/q/32c4b)

13. [在Java中安全地将**long**转换为**int**](https://code.i-harness.com/zh-CN/q/18462f)

14. [java separator 用法 - File.separator 或 File.pathSeparator](https://code.i-harness.com/zh-CN/q/5b1ffc#header)

15. [如何获得 Java 文件的文件扩展名？](https://code.i-harness.com/zh-CN/q/367e17)

### 集合

1. [java list教程 list用法 - 如何在Java中创建一个新的List](https://code.i-harness.com/zh-CN/q/d19cc)

2. [list合并 - 我如何在Java中加入两个列表？](https://code.i-harness.com/zh-CN/q/2e477)

3. [【相互转换】java数组转set set转string - 如何将数组转换为Java中的Set](https://code.i-harness.com/zh-CN/q/2ec267)

4. [ 如何将 **List<**Object**>** 转换为 **List<**MyClass**>**](https://code.i-harness.com/zh-CN/q/1d4394)

5. [在Java中将 List 转换为 Set 的最简单方法是什么？](https://code.i-harness.com/zh-CN/q/15d164)

6. [如何在迭代集合时安全地从集合中移除元素](https://code.i-harness.com/zh-CN/q/12422a)

      > 
      > ```java
      > for(Iterator<String> i = names.iterator(); i.hasNext();) {
      >        String name = i.next(); // must be called before you can call i.remove()
      >        //Do Something
      >        i.remove();
      > }
      > ```
      > Tips：另外一种删除重复项的方法是将列表转储到 LinkedHashSet（如果需要，然后返回到列表中）。 这可以在删除重复项时保留插入顺序。

7. [所有迭代列表的方式以及每种方式的优缺点](https://code.i-harness.com/zh-CN/q/118ea33)

8. [各类循环方式消耗时间对比](https://code.i-harness.com/zh-CN/q/fdd606)

      > 在文中对于迭代 **List<**Integer**>** 集合，**List.stream().forEach()** 最具效率。

9. [为什么 Java 中没有 SortedList？](https://code.i-harness.com/zh-CN/q/85238b)

10. [**Integer.valueOf** 和 **Integer.parseInt** 区别](https://code.i-harness.com/zh-CN/q/7c2f9)

      > 查看源码即可。都可接收 **String** 类型的入参，**ValueOf** 返回包装类型，**parseInt** 返回基本类型

11. [**int[]** 如何轻松转换为 **Integer[]**？](https://code.i-harness.com/zh-CN/q/d6fc5)

12. [如何将 **List<**Type**>** 转换为 **Type[]**？](https://code.i-harness.com/zh-CN/q/9211bb)

13. [在Java中如何将**List <**Integer**>**转换为**int[]**？](https://code.i-harness.com/zh-CN/q/ea7af)

       > ```java
       > public int[] toIntArray(List<Integer> intList){
       >        return intList.stream().mapToInt(Integer::intValue).toArray();
       > }
       > ```

14. [是否有一个好的可用（标准Java）数据结构来表示Java中的树？](https://code.i-harness.com/zh-CN/q/35bf96)

15. [ 如何从ArrayList中删除重复的元素？](https://code.i-harness.com/zh-CN/q/31cd0)
    > ```java
    > Set<Entity> s = new LinkedHashSet<Entity>(entities); // List<Entity> entities
    > entities.clear();
    > entities.addAll(s);
    > ```

16. [**List<**String**>** 合并为一个 **String** ](https://code.i-harness.com/zh-CN/q/1abb24)

    > ```java
    > // Bill and Bob and Steve
    > String.join(" and ", Arrays.asList("Bill", "Bob", "Steve"));
    > ```

17. [将 ArrayList 中内容合并为字符串](https://code.i-harness.com/zh-CN/q/92479)

18. [逗号分隔字符串存储至ArrayList集合中](https://code.i-harness.com/zh-CN/q/724483)

    > ```java
    > String commaSeparated = "item1 , item2 , item3";
    > ArrayList<String> items = new ArrayList<String>(Arrays.asList(commaSeparated.split(",")));
    > ```

19. [如何检测链表中的循环？](https://code.i-harness.com/zh-CN/q/28a2cb)

20. [统计字符串中某一字符出现的个数](https://code.i-harness.com/zh-CN/q/435e8)

    > ```java
    > int res = "abd$asda$asad$sas".chars().reduce(0, (a, c) -> a + (c == '$' ? 1 : 0));
    >    System.out.println(res);
    > ```

21. [检测重复String的简单方法](https://code.i-harness.com/zh-CN/q/12d8eb)

22. [Java 8中的map和flatMap方法有什么区别？](https://code.i-harness.com/zh-CN/q/1972c92)

	> [flatmap将多个Stream连接成一个Stream](https://blog.csdn.net/andyzhaojianhui/article/details/79047825)，换言之它可以接收类似于list<list<E>>之类的集合，每个元素都可以迭代。之后对每一个元素进行迭代，每一次迭代进行一次方法

23. [为什么在尝试从列表中删除元素时会遇到UnsupportedOperationException？](https://code.i-harness.com/zh-CN/q/2d40f3)

    > Arrays.asList()返回的列表是不可变的。
    > ```java
    > 使用 List<String> list = new ArrayList(Arrays.asList(split))
    > ```


18. [如何在Java中填充字符串](https://code.i-harness.com/zh-CN/q/5ed6d)

    > ① 使用 String.format 方法 ② Apache [StringUtils](https://commons.apache.org/proper/commons-lang/javadocs/api-2.6/org/apache/commons/lang/StringUtils.html) 有几种方法可使用：leftPad，rightPad，center 和 repeat

19. [如何在Java中反转字符串](https://code.i-harness.com/zh-CN/q/737fb7)

    > ```java
    > new StringBuilder(hi).reverse().toString()
    > ```

20. [java如何初始化二维数组](https://code.i-harness.com/zh-CN/q/baa31d)

21. [在java中如何比较日期?](https://code.i-harness.com/zh-CN/q/278ef5)

    > 使用 **Date.before()**，**Date.after()** 和 **Date.equals()**
    > ```java
    > if(todayDate.after(historyDate) && todayDate.before(futureDate)){...}
    > ```
    
22. [如何在 HashMap 中保留插入顺序？](https://code.i-harness.com/zh-CN/q/a36cb1)

    > 建议并使用 LinkedHashMap 或其他有序集合（它会按照插入顺序迭代元素）， [此链接可以帮助您找到最适合使用的集合](http://www.javapractices.com/topic/TopicAction.do?Id=65)。如果想找其他保留插入顺序的集合，可以看看 [这个讨论帖](https://code.i-harness.com/zh-CN/q/a6dfe)




<video controls="" autoplay="" name="media" style="clear:both;display:block;margin:auto"><source src="http://dl.stream.qqmusic.qq.com/M500000pNA1e4c8zx1.1mp31?vkey=CC5F80ADFF396464208628B20D6E9719662EC8167EB0AC25943BB5A9A9C76F1F1E355F1A8F9399CE26FFA87C208A8BF12E00E573864F0292&amp;guid=5150825362&amp;fromtag=1" type="audio/mpeg"></video>

