### vscode-go-tools 安装失败的解决办法
VSCode 实在是一个开发利器，通过安装插件可以轻松进行 Js Python等多种语言的开发，之前安装 go 的一些插件时总提示失败，最初参照网上的一些方法解决了，但最近把 go 版本升到了 1.13。VSCode 提示插件需要升级，这一升级不要紧，又安装失败了，错误和之前的还不一样，经过排查日志最后总算解决了，记录一下解决办法。

在 VSCode 中使用快捷键 Cmd + Shift + P，输入 go 会显示 Go：Install/Update Tools，点击安装 Tools，VSCode 的输出中会显示下面的错误
```
Installing github.com/nsf/gocode SUCCEEDED
Installing github.com/uudashr/gopkgs/cmd/gopkgs SUCCEEDED
Installing github.com/ramya-rao-a/go-outline FAILED
Installing github.com/acroca/go-symbols FAILED
Installing golang.org/x/Tools/cmd/guru FAILED
Installing golang.org/x/Tools/cmd/gorename FAILED
Installing github.com/fatih/gomodifytags SUCCEEDED
Installing github.com/haya14busa/goplay/cmd/goplay SUCCEEDED
Installing github.com/josharian/impl FAILED
Installing github.com/rogpeppe/godef SUCCEEDED
Installing sourcegraph.com/sqs/goreturns FAILED
Installing github.com/golang/lint/golint FAILED
Installing github.com/cweill/gotests/... FAILED
Installing github.com/derekparker/delve/cmd/dlv SUCCEEDED
```
#### 解决办法1
[clone 此项目](https://github.com/znvy/VSCode-go-Tools.git)，把 bin 中的文件放入你的 GOPATH/bin 中，之后重启 VSCode 即可。

#### 解决办法2
看报错信息会发现这些包依赖 golang.org 上的一些包，而 golang.org 在国内是无法访问的，所以安装失败了。打开 GOPATH 的 src 目录会发现这些包是有下载下来的，所以只要把那些无法下载的包手动拷到相应位置就可以了。

在 GOPATH/src/golang.org/x 下 git clone https://github.com/golang/Tools.git, 然后打开终端，执行下面的命令

```
go install github.com/ramya-rao-a/go-outline

go install github.com/acroca/go-symbols

go install golang.org/x/Tools/cmd/guru

go install golang.org/x/Tools/cmd/gorename

go install github.com/josharian/impl

go install github.com/rogpeppe/godef

go install github.com/sqs/goreturns

go install github.com/golang/lint/golint

go install github.com/cweill/gotests/gotests

go install github.com/ramya-rao-a/go-outline

go install github.com/acroca/go-symbols

go install golang.org/x/Tools/cmd/guru

go install golang.org/x/Tools/cmd/gorename

go install github.com/josharian/impl

go install github.com/rogpeppe/godef

go install github.com/sqs/goreturns

go install github.com/golang/lint/golint

go install github.com/cweill/gotests/gotests
```
#### 解决办法3
最近把 go 版本升级到 1.13 后，打开 VSCode 后提示相关的一些插件也需要升级，点击升级后又报错了，和之前 1.12 一样，还是提示找不到 golang.org 上的包。

但查看 GOPATH 目录后发现 src 目录下并没有新增这些插件包，仔细查看后发现这些 Tools 的包被放在了 GOPATH/pkg/mod 目录下，用过 go mod 的应该知道，这个目录是用来放 go mod 安装的包，也就是说 VSCode 改用 go mod 来管理第三方包了。了解了问题所在，解决就比较简单了。只需要在 GOPATH/pkg/mod/golang.org/x 下git clone https://github.com/golang/Tools.git, 然后进入一个go mod管理的目录执行上面那些命令。
```
go install github.com/ramya-rao-a/go-outline
...
...
go install github.com/cweill/gotests/gotests
```

#### 使用 go mod 后还需要 GOPATH 吗?
我们不需要再去关注 GOPATH了。但 go mod 其实还是依赖 GOPATH 的，go mod 中安装 package 位置在 GOPATH/pkg/mod/下，在 go mod 管理的目录执行代码会根据 go.mod 文件去 GOPATH/pkg/mod 下查找对应的包

#### go install 是什么?
go install 其实就是先去对应目录 go build，如果是 main 包，把 build 生成的可执行文件再移动到 GOPATH/bin 下。

#### 为什么上面的解决办法执行 go install 位置不同?
非 go mod 目录下执行 go install 命令会去 GOPAHT/src 下找这些包，go mod 管理的目录下执行 go install 会去 GOPATH/pkg/mod 目录下查找对应的包。
