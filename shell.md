###### 1、/dev/null

> 表示垃圾桶，将不想显示的输出扔进去

- 用法一：`xxx > /dev/null`，表示将标准输出 stdout 扔进垃圾桶

- 用法二：`xxx > /dev/null 2>&1`，表示将标准输出 stdout 和标准错误输出 stderr 都扔进垃圾桶（其中2>&1，表示2的输出重定向等同于1，即将 stderr（2）看成 stdout（1），而 stdout 已经扔到垃圾桶了，等同于 stderr 也被扔进垃圾桶里了）

  ```bash
  ls xxx > /dev/null #当xxx不存在时，仍有错误输出：ls: cannot access 'xxx': No such file or directory
  ls xxx > /dev/null 2>&1	#当xxx不存在时，也没有错误输出信息
  ```

  