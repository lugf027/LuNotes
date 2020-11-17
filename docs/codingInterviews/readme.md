# CondingInterviews (《剑指Offer》)

1. 为了节省内存，C/C++把常量字符串放到单独的-一个内存区域。当几个
   指针赋值给相同的常量字符串时，它们实际上会指向相同的内存地址。但
   用常量内存初始化数组，情况却有所不同。

```c
char str1[] = "hello world";
char str2[] = "hello world";

char *str3 = "hello world";
char *str4 = "hello world";

printf("str1 addr:%p\nstr2 addr:%p\nstr3 addr:%p\nstr4 addr:%p\n", str1, str2, str3, str4);
```

解释：str1和str2是两个字符串数组，我们会为它们分配两个长度为12字节的空间，并把"hello world"的内容分别复制到数组中去。这是两个初始地址不同的数组，因此strl和str2的值也不相同，所以输出的第一- 行是"strl andstr2 are not same"。
str3和str4是两个指针，我们无须为它们分配内存以存储字符串的内容，而只需要把它们指向"hello world" 在内存中的地址就可以了。由于"helloworld"是常量字符串，它在内存中只有一个拷贝，因此str3和str4指向的是同一个地址。所以比较str3 和str4的值得到的结果是相同的，输出的第二行是"str3 and str4 are same"。