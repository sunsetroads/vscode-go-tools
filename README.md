# vscode-go-tools

使用vscode进行go开发时需要安装一些插件，但安装go-tools时会报下面的错
```
Installing github.com/nsf/gocode SUCCEEDED
Installing github.com/uudashr/gopkgs/cmd/gopkgs SUCCEEDED
Installing github.com/ramya-rao-a/go-outline FAILED
Installing github.com/acroca/go-symbols FAILED
Installing golang.org/x/tools/cmd/guru FAILED
Installing golang.org/x/tools/cmd/gorename FAILED
Installing github.com/fatih/gomodifytags SUCCEEDED
Installing github.com/haya14busa/goplay/cmd/goplay SUCCEEDED
Installing github.com/josharian/impl FAILED
Installing github.com/rogpeppe/godef SUCCEEDED
Installing sourcegraph.com/sqs/goreturns FAILED
Installing github.com/golang/lint/golint FAILED
Installing github.com/cweill/gotests/... FAILED
Installing github.com/derekparker/delve/cmd/dlv SUCCEEDED
```
#### 简单的解决办法
[clone 此项目](https://github.com/znvy/vscode-go-tools.git)，把bin中的文件放入你的GOPATH/bin中(仅适用于mac环境),之后重启vscode即可

#### go 1.12解决办法

看报错信息会发现这些包依赖golang.org上的一些包，而golang.org在国内是无法访问的，所以安装失败了。打开GOPATH的src目录会发现这些包是有下载下来的，所以只要把那些无法下载的包手动拷到相应位置就可以了。

在$GOPATH/src/golang.org/x下git clone https://github.com/golang/tools.git, 然后打开终端，进入非go mod管理的目录执行下面的命令

```
go install github.com/ramya-rao-a/go-outline

go install github.com/acroca/go-symbols

go install golang.org/x/tools/cmd/guru

go install golang.org/x/tools/cmd/gorename

go install github.com/josharian/impl

go install github.com/rogpeppe/godef

go install github.com/sqs/goreturns

go install github.com/golang/lint/golint

go install github.com/cweill/gotests/gotests

go install github.com/ramya-rao-a/go-outline

go install github.com/acroca/go-symbols

go install golang.org/x/tools/cmd/guru

go install golang.org/x/tools/cmd/gorename

go install github.com/josharian/impl

go install github.com/rogpeppe/godef

go install github.com/sqs/goreturns

go install github.com/golang/lint/golint

go install github.com/cweill/gotests/gotests
```
#### go 1.13解决办法
最近把go版本升级到1.13后，打开vscode后提示相关的一些插件也需要升级，点击升级后毫无疑外的报错了，和之前1.12一样，还是提示找不到golang.org上的包。

查看GOPATH目录后发现src目录下并没有新增这些包，这些包被放在了GOPATH/pkg/mod目录下，这时需要在$GOPATH/pkg/mod/golang.org/x下git clone https://github.com/golang/tools.git, 然后进入一个go mod管理的目录执行下面这些命令。
```
go install github.com/ramya-rao-a/go-outline
...
...
go install github.com/cweill/gotests/gotests
```

### go mod tips
用惯了npm、pip、cocoapods，初学go时看到它五花八门的包管理，着实头疼了一番，可能官方也看不下去了，在go 1.11的版本推出了go mod，学了下之后，马上放弃了其他包管理工具

##### 使用go mod后还需要GOPATH吗?
我们不需要再去关注GOPATH了。但go mod其实还是依赖GOPATH的，go mod 中安装package位置在 GOPATH/pkg/mod/下，在GOPATH目录外执行代码会根据go.mod文件去GOPATH/pkg/mod下查找对应的包

##### 为什么上面的解决办法go1.12和1.13执行go install位置不同?
非go mod目录下执行go install命令会去GOPAHT/src下找这些包, go mod管理的目录下执行go install会去GOPATH/pkg/mod目录下查找对应的包。

##### go install和go build的区别?
go install其实就是先去对应目录go build，如果是main包，把build生成的可执行文件再移动到GOPATH/bin下。

[go标准命令详解](http://wiki.jikexueyuan.com/project/go-command-tutorial/0.0.html)

go的使用方法我就不复制粘贴了，网上有太多的教程了。记得使用**go mod help**查看相关命令
