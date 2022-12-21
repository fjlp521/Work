##### 1、函数

```bash
function test() {
    file=$1
    shift
    args=$@
    echo $file
    echo $args
}
test 1 2 
test 1 2 3
test 1 2 3 4
```

- $1、$2、$3...：取传入的第一、二、三个参数...
- $@：取传入的所有参数
- shift：这个有意思，相当于跳过一个位置，取从下一位开始取参数