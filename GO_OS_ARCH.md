# Golang supported OS/Arch table

|Architecture                    |&nbsp;&nbsp;GOOS&nbsp;→<br>GOARCH↓|`aix`|`android`|`darwin`|`dragonfly`|`freebsd`|`js`|`linux`|`nacl`|`netbsd`|`openbsd`|`plan9`|`solaris`|`windows`|
|:-------------------------------|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
|x86 32-bit                      |`386`     |　|√|√|　|√|　|√|√|√|√|√|　|√|
|x86 64-bit                      |`amd64`   |　|√|√|√|√|　|√|√|√|√|√|√|√|
|x86 64-bit,<br>32-bit-address   |`amd64p32`|　|　|　|　|　|　|　|√|　|　|　|　|　|
|ARM 32-bit                      |`arm`     |　|√|√|　|√|　|√|√|√|√|√|　|√|
|ARM 64-bit                      |`arm64`   |　|√|√|　|　|　|√|　|　|　|　|　|　|
|MIPS 32-bit,<br>big-endian      |`mips`    |　|　|　|　|　|　|√|　|　|　|　|　|　|
|MIPS 64-bit,<br>big-endian      |`mips64`  |　|　|　|　|　|　|√|　|　|　|　|　|　|
|MIPS 64-bit,<br>little-endian   |`mips64le`|　|　|　|　|　|　|√|　|　|　|　|　|　|
|MIPS 32-bit,<br>little-endian   |`mipsle`  |　|　|　|　|　|　|√|　|　|　|　|　|　|
|PowerPC 64-bit,<br>big-endian   |`ppc64`   |√|　|　|　|　|　|√|　|　|　|　|　|　|
|PowerPC 64-bit,<br>little-endian|`ppc64le` |　|　|　|　|　|　|√|　|　|　|　|　|　|
|IBM ESA/390                     |`s390x`   |　|　|　|　|　|　|√|　|　|　|　|　|　|
|WebAssmbly                      |`wasm`    |　|　|　|　|　|√|　|　|　|　|　|　|　|

# script for print table

```bash
#!/bin/bash

DIST=$(go tool dist list)
OSes=$(go tool dist list | awk -F '/' '{print $1}' | sort | uniq)
ARCHes=$(go tool dist list | awk -F '/' '{print $2}' | sort | uniq)

# print table header
echo '|&nbsp;&nbsp;GOOS&nbsp;→<br>GOARCH↓|`'$OSes'`|' | sed 's/\ /`|`/g'

# print separator
for o in $OSes; do
	echo -n '|:---:'
done
echo '|:---:|'

# print table body
for a in $ARCHes; do
	printf '|%-10s' '`'$a'`'
	for o in $OSes; do
		if `echo $DIST | grep "$o/$a" &> /dev/null`; then
			echo -n '|√'
		else
			echo -n '|　'
		fi
	done
	echo '|'
done
```
