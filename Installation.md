# Installation

Go 😆 to [https://golang.org/doc/install](https://golang.org/doc/install) and follow the instructions

## Setup workspace

Create the following folder structure
```
code
	|--bin
	|--pkg
	|--src
			|--github.com
						|--fewlinesofcode
									|--<Project name>
```

1. Edit `.bash_profile` using `nano .bash_profile`
3. Add the lines below and save. See this [gist](https://gist.github.com/vsouza/77e6b20520d07652ed7d)

```
export GOPATH=$HOME/golang              #correct url should be set
export GOROOT=/usr/local/opt/go/libexec #correct url should be set
export PATH=$PATH:$GOPATH/bin
export PATH=$PATH:$GOROOT/bin
```

*TODO: figure out how to setup ergonomic Workspace* 

## Read more on Initial setup
https://golang.org/cmd/go/#hdr-GOPATH_environment_variable
https://golang.org/cmd/go/#hdr-Vendor_Directories