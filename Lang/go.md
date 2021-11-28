## Go

### Install linters

~~~
$ go install golang.org/x/tools/go/analysis/passes/shadow/cmd/shadow@latest
$ mv ~/go/bin/shadow ~/bin/
$ go install github.com/golangci/golangci-lint/cmd/golangci-lint@v1.43.0
$ mv ~/go/bin/golangci-lint ~/bin/
$ go install golang.org/x/tools/gopls@latest
$ mv ~/go/bin/gopls ~/bin/
~~~
