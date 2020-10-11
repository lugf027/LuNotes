# C Traps and Pitfalls

1. '='与'==' 并 涉及到优先级时

```c
while (c = ' ' || c == '\t' || c == '\n')
    c = getc(f);
```

自己误分析为先将空格' '赋值给c，在while一直循环时，c值为空格的ASCII值32，实际是1.

```c
while (c = (' ' || c == '\t' || c == '\n'));
// 亦即
while (c = ( 1 ));
```

