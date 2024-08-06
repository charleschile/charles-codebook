1. 初始化 Go 模块
首先，确保你在项目的根目录下。然后运行以下命令来初始化一个新的 Go 模块，这将创建一个 go.mod 文件：

bash
复制代码
go mod init pdf-cpu
这里的 pdf-cpu 是模块的名称，你可以根据需要更改。

2. 添加依赖项
接下来，运行以下命令来添加 pdfcpu 库作为依赖项：

bash
复制代码
go get github.com/pdfcpu/pdfcpu/pkg/api
这个命令将下载并安装 pdfcpu 库的 api 包以及它的所有依赖项，并自动更新你的 go.mod 文件。

你还可以运行：

bash
复制代码
go get github.com/pdfcpu/pdfcpu/pkg/pdfcpu
来确保你获取到了你需要的 pdfcpu 包。


2. 使用 go mod tidy
你可以运行 go mod tidy 命令来自动添加和移除依赖项。这个命令会检查你的代码，添加任何缺失的依赖项，并移除未使用的依赖项。

bash
复制代码
go mod tidy
go mod tidy 将确保你的 go.mod 文件是最新的，并且你可以直接运行你的程序而不需要手动管理依赖。

3. 运行你的 Go 文件
在正确安装依赖项并初始化 Go 模块后，你应该能够成功运行你的 Go 文件：

bash
复制代码
go run pdf-cpu.go
总结步骤
初始化 Go 模块：go mod init pdf-cpu
添加依赖项：go get github.com/pdfcpu/pdfcpu/pkg/api
运行代码：go run pdf-cpu.go
如果你之后需要更新或管理依赖项，可以使用 go mod tidy 清理未使用的依赖项，或者使用 go mod vendor 将依赖项本地化。