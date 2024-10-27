---
description: https://go.dev/doc/code
---

# How to write go code

### Code organization

* A repository contains one or more modules.
* A module is a collection of Go packages that are released together.
* A package is a collection of source files in the same directory that are compiled together.

### go.mod

* A file named `go.mod` there declares the module path: the **import path prefix** for all packages within the module.&#x20;
* Each module's path not only serves as an import path prefix for its packages, but also indicates where the `go` command should look to download it.&#x20;
* An import path is a string used to import a package. A package's import path is its module path joined with its subdirectory within the module.

### Go tools

```shell
# Declare a module path (example/user/hello) and create new go.mod
$ go mod init example/user/hello

# vim hello.go

# Builds the `hello` command, producing an executable binary (bin/).
$ go install example/user/hello

# Add the install directory to PATH
$ export PATH=$PATH:$(dirname $(go list -f '{{.Target}}' .))

# Run the program 
$ hello
```

Commands like `go install` apply within the context of the module containing the current working directory. If the working directory is not within the `example/user/hello` module, `go install` may fail.

#### Import packages from your module

```shell
# Write the packages.

# Then compile with go build within pwd
$ cd $HOME/hello/morestrings
$ go build

# Import the package in main.go

# Then re-install.
$ go install example/user/hello
$ hello
```

#### Import packages from remote modules

```shell
# Import the remote module in main.go

# Add missing modules 
$ go mod tidy
$ go install example/user/hello
$ hello
```

\-----------------------------------------------

Update: Some common used commands

```bash
# Build and running
go run main.go    # Compile and run in one step
go build            # Build the package in current directory
go build main.go    # Build a specific file
go install        # Compile and install packages

# Package management
go mod init <name>    # Initialize a new module
go mod tidy            # Add missing and remove unused modules
go get <package>        # Download and install package
go get -u             # Update all dependencies
go mod vendor         # Create vendor directory
go list -m all        # List all dependencies

# Testing
go test            # Run tests in current directory
go test ./...        # Run all tests in current directory and sub-directories
go test -v         # Run tests with verbose output
go test -cover        # Run tests with coverage analysis

# Code quality
go fmt            # Format go source code
go vet            # Report likely mistakes in packages
go clean            # Remove object files and cached files

# Information
go version    
go env
go list
go doc <package-name>

# Cross-compilation

# Build for Windows from Linux/Mac
GOOS=windows GOARCH=amd64 go build

# Build for Linux from Windows/Mac
GOOS=linux GOARCH=amd64 go build

# Build for Mac from Linux/Windows
GOOS=darwin GOARCH=amd64 go build
```
