# cppclean-handle-shell
### 1.使用方法

用于处理cppclean插件中的多余include的头文件。

使用的是cleanheader.sh脚本，主要两种用法：

1. 将cppclean扫描的结果文件作为输入

```bash
> ./cleanheader.sh -f filename
```

2. 使用指令的输出作为输入

```bash
> ./cleanheader.sh -c "cppclean --include-path=base AuctionServer/Auction.h|grep already"
```

execppclean.sh是我们项目扫描的脚本，方便填入比较多的include-path。

### 2.注意事项

脚本会将输入的每一行中提到的对应行注释掉，所以需要对cppclean的输出筛选。

cppclean和多余include有关的输出有这几种：

```bash
1. 重复include
GDataServer/DisneyMusicManager.cpp:13: 'xLog.h' already #included in 'GDataServer/DisneyMusicManager.h'
2. 未使用具体成员，仅使用类声明，可以使用前置声明替代
GDataServer/DisneyMusicManager.h:9: 'DisneyActivity.pb.h' does not need to be #included; use a forward declaration instead
3. 未使用头文件中的内容
GDataServer/DisneyMusicManager.h:10: 'GDataServer.h' does not need to be #included
```

1. 对于1这种情况，可以直接注释掉没有任何问题。

2. 对于2，不能直接去掉，去掉需要自己进行前置声明。
3. 对于3，部分可以去掉，目前发现的有两种情况会出问题：A文件引用了H文件，虽然没使用H中的内容，但使用了H文件中引入的H2文件，这种情况也会输出3；还有有些作用域在代码中省略了，由编译器自动补全的情况，cppclean也会判断没用到头文件的内容，也会输出3