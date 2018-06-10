```flow
st=>start: 开始
e=>end: 结束
op1=>operation: 操作
sub1=>subroutine: 子程序
cond=>condition: 是 或 否
io=>inputoutput: 输入输出

st->op1->cond
cond(yes)->io->e
cond(no)->sub1(right)->op1

```

