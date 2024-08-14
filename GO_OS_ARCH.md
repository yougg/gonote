# Golang supported OS/Arch table `go1.23`

## GOOS GOARCH

|Architecture                    |&#32;&#32;GOOS&#32;→<br>GOARCH↓|`aix`|`android`|`darwin`|`dragonfly`|`freebsd`|`illumos`|`ios`|`js`|`linux`|`netbsd`|`openbsd`|`plan9`|`solaris`|`wasip1`|`windows`|
|:-------------------------------|:--------:|:------:|:------:|:------:|:------:|:------:|:------:|:------:|:------:|:------:|:------:|:------:|:------:|:------:|:------:|:------:|
|x86 32-bit                      |`386`     | &nbsp; |&#10004;| &nbsp; | &nbsp; |&#10004;| &nbsp; | &nbsp; | &nbsp; |&#10004;|&#10004;|&#10004;|&#10004;| &nbsp; | &nbsp; |&#10004;|
|x86 64-bit                      |`amd64`   | &nbsp; |&#10004;|&#10004;|&#10004;|&#10004;|&#10004;|&#10004;| &nbsp; |&#10004;|&#10004;|&#10004;|&#10004;|&#10004;| &nbsp; |&#10004;|
|ARM 32-bit                      |`arm`     | &nbsp; |&#10004;|&#10004;| &nbsp; |&#10004;| &nbsp; |&#10004;| &nbsp; |&#10004;|&#10004;|&#10004;|&#10004;| &nbsp; | &nbsp; |&#10004;|
|ARM 64-bit                      |`arm64`   | &nbsp; |&#10004;|&#10004;| &nbsp; |&#10004;| &nbsp; |&#10004;| &nbsp; |&#10004;|&#10004;|&#10004;| &nbsp; | &nbsp; | &nbsp; |&#10004;|
|LoongArch 64-bit                |`loong64` | &nbsp; | &nbsp; | &nbsp; | &nbsp; | &nbsp; | &nbsp; | &nbsp; | &nbsp; |&#10004;| &nbsp; | &nbsp; | &nbsp; | &nbsp; | &nbsp; | &nbsp; |
|MIPS 32-bit,<br>big-endian      |`mips`    | &nbsp; | &nbsp; | &nbsp; | &nbsp; | &nbsp; | &nbsp; | &nbsp; | &nbsp; |&#10004;| &nbsp; | &nbsp; | &nbsp; | &nbsp; | &nbsp; | &nbsp; |
|MIPS 64-bit,<br>big-endian      |`mips64`  | &nbsp; | &nbsp; | &nbsp; | &nbsp; | &nbsp; | &nbsp; | &nbsp; | &nbsp; |&#10004;| &nbsp; | &nbsp; | &nbsp; | &nbsp; | &nbsp; | &nbsp; |
|MIPS 64-bit,<br>little-endian   |`mips64le`| &nbsp; | &nbsp; | &nbsp; | &nbsp; | &nbsp; | &nbsp; | &nbsp; | &nbsp; |&#10004;| &nbsp; | &nbsp; | &nbsp; | &nbsp; | &nbsp; | &nbsp; |
|MIPS 32-bit,<br>little-endian   |`mipsle`  | &nbsp; | &nbsp; | &nbsp; | &nbsp; | &nbsp; | &nbsp; | &nbsp; | &nbsp; |&#10004;| &nbsp; | &nbsp; | &nbsp; | &nbsp; | &nbsp; | &nbsp; |
|PowerPC 64-bit,<br>big-endian   |`ppc64`   |&#10004;| &nbsp; | &nbsp; | &nbsp; | &nbsp; | &nbsp; | &nbsp; | &nbsp; |&#10004;| &nbsp; |&#10004;| &nbsp; | &nbsp; | &nbsp; | &nbsp; |
|PowerPC 64-bit,<br>little-endian|`ppc64le` | &nbsp; | &nbsp; | &nbsp; | &nbsp; | &nbsp; | &nbsp; | &nbsp; | &nbsp; |&#10004;| &nbsp; | &nbsp; | &nbsp; | &nbsp; | &nbsp; | &nbsp; |
|RISC-V 64-bit                   |`riscv64` | &nbsp; | &nbsp; | &nbsp; | &nbsp; |&#10004;| &nbsp; | &nbsp; | &nbsp; |&#10004;| &nbsp; |&#10004;| &nbsp; | &nbsp; | &nbsp; | &nbsp; |
|IBM ESA/390                     |`s390x`   | &nbsp; | &nbsp; | &nbsp; | &nbsp; | &nbsp; | &nbsp; | &nbsp; | &nbsp; |&#10004;| &nbsp; | &nbsp; | &nbsp; | &nbsp; | &nbsp; | &nbsp; |
|WebAssembly                     |`wasm`    | &nbsp; | &nbsp; | &nbsp; | &nbsp; | &nbsp; | &nbsp; | &nbsp; |&#10004;| &nbsp; | &nbsp; | &nbsp; | &nbsp; | &nbsp; |&#10004;| &nbsp; |

## script for print Markdown table

```bash
#!/bin/bash

DIST=$(go tool dist list)
OSes=$(go tool dist list | awk -F '/' '{print $1}' | sort -u)
ARCHes=$(go tool dist list | awk -F '/' '{print $2}' | sort -u)

# print table header
echo '|&#32;&#32;GOOS&#32;→<br>GOARCH↓|`'$OSes'`|' | sed 's/\ /`|`/g'

# print separator
echo -n '|:--------:|'
for o in $OSes; do
	echo -n ':------:|'
done
echo

# print table body
for a in $ARCHes; do
	printf '|%-10s' '`'$a'`'
	for o in $OSes; do
		if `echo $DIST | grep "$o/$a" &> /dev/null`; then
			echo -n '|&#10004;'
		else
			echo -n '| &nbsp; '
		fi
	done
	echo '|'
done
```
