title: "erlang:闭包与伪递归"
date: "2015-05-02 13:01"
categories：
    -erlang
tags:
    
---


通过2个例子：实现矩阵（访问二维数组）和 求和 来讲述闭包与尾递归的优点。
```
#!/usr/bin/escript
main(_) ->
  X=make_matrix({1,2,3,4},2),
  Y=make_matrix({2,3,4,5},2),
  Z=mul_matrix(X,Y,2),
  print_matrix(X,2),io:format("* ~n"),
  print_matrix(Y,2),io:format("= ~n"),
  print_matrix(Z,2).


%% generate a matrix

make_matrix(Data,Size) when erlang:is_list(Data) ->  make_matrix(list_to_tuple(Data),Size);
make_matrix(Data,Size) ->
  fun(I,J) when I =< Size , J =< Size -> element(I*Size+J-Size,Data) end.

%% X , Y: matrix  -> matrix

mul_matrix(X,Y,Size) ->
  make_matrix([ lists:sum([ X(X1,T)*Y(T,Y1)|| T <- lists:seq(1,Size) ])
    || X1 <- lists:seq(1,Size), Y1 <- lists:seq(1,Size) ],Size).

print_matrix([],Size) -> ok;
print_matrix(Matrix,Size) when is_function(Matrix) ->
  print_matrix([Matrix(X1,Y1) || X1 <- lists:seq(1,Size), Y1 <- lists:seq(1,Size) ],Size);
print_matrix(Matrix,Size) ->
  {L,R}=lists:split(Size,Matrix),
  io:format("~w ~n",[L]),
  print_matrix(R,Size).


sum(Start,End) ->
  sum(Start+1,End,Start).
sum(End,End,Sum) ->
  Sum+End;
sum(Start,End,Sum) ->
  sum(Start+1,End,Sum+Start).

```

### 闭包：

由于erlang只有2种数据容器（list和tuple），2者都是一维的。在访问上tuple是顺序访问的，而链表需要遍历节点。 因此使用tuple优于list。2维数组退化为一维数组也不是什么难事，只要知道Size然后使用`t=i*size+j`进行转化。 这样可以很自然地得到`Xij =element(I*Size+J-Size,Data)`。可是这样我们需要使用4个变量来访问矩阵，并且必须时刻记住Data 与Size ，十分不方便也容易犯错。

```
 make_matrix(Data,Size) ->
  fun(I,J) when I =< Size , J =< Size -> element(I*Size+J-Size,Data) end.
```

采用闭包将Data与Size存入函数体内部，隐藏细节。

访问矩阵就变成了：
```
Matrix=make_matrix(Data,Size),
Matrix(i,j)
```

### 尾递归：

尾调用指的是一个函数最后一次执行的方法。而尾递归是 尾调用为递归函数时的特殊情况。当一个函数为尾递归时，在将参数复制给下一个函数后，原函数可以安全地退出，因而能够充分地利用堆栈。但也并不是说尾递归更好。如果返回的结果简单，就放心的使用尾递归吧。如果是这样

```
q_sort([]) -> [];
q_sort([H|R]) ->
  q_sort([X || X <-R ,X =< H])
  ++ [H] ++
  q_sort([X || X <-R ,X > H]).
```

挖空心思来实现尾递归并不太明智。

