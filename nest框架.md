created



有关一些固定的并且没有可视界面操作的数据可以封装到一个service里面，根据传入key来get固定的数据

这样上了生产环境以后如果固定的数据需要变更只需要修改代码而不需要再入库进行手动变更



对于操作日志，可以使用拦截器来做



nest start 找不到main module

首先nest start 走的是tsc编译，

引入了外层的内容，所以外层的整个目录结构会被tsc引入进来否则import无法转义。

nest start 过程做的事情

看nest-cli 源码

```
    let outputFilePath = join(outDirName, sourceRoot, entryFile);
    if (!fs.existsSync(outputFilePath + '.js')) {
      outputFilePath = join(outDirName, entryFile);
    }
    
   //先找 dist/src/main.js
   如果没有，找 dist/main.js
```

