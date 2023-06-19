!> 用于参数转换，与不支持管道输入的命令搭配使用，比如 `ls`.

## Options
| options | explain |
 - | -
| -a | file 从文件中读入作为stdin |
| -e flag| 注意，有的时候可能为-E，flag必须是一个以空格分隔的标志，当分析到含有flag这个标志的时候就停止 |
| -p | 每当执行一个argument的时候会询问用户 |
| -n num| 表示命令在执行的时候一次用几个参数，默认是所有 |
| -t | 表示先打印命令，再执行 |
| -i \| -I | 参数占位符，{}，%，等都可以使用 |
| -s | |
| -r no-run-if-empty  | |
| -L num| |
| -d delim | 默认的xargs的分隔符是回车，argument的分隔符是空格。这里修改的是xargs的分隔符 |
| -x exit| 主要配合 -s使用 |
| -P | 修改最大进程数，可以并发执行 |

## CATION
```shell
cat > file <<EOF
a b c d e
f g h i j k
l m n o p q r
s t u v
w x y z
EOF

cat file | xargs -L 2 -I {} echo '{}'  # 这个命令中 -L 和 -I 指令冲突。所以不会出现预期效果，可以多加一个xargs使用,比如下面
cat file | xargs -L 2 | xargs -I {} echo '{}' # 多加后出现的效果。
```

## Reference
* https://www.runoob.com/linux/linux-comm-xargs.html
* https://www.cnblogs.com/chentiao/p/16543679.html