## GDB as Hex (Offset)-Calculator:
gdb --> print 0x1234 - 0x4321 --> <decimal result>

##Special Char in Terminal
press ctrl+shift+u --> enter unicode value --> enter

##Linux default filedescriptors
0 = stdin, 1=stdout, 2=stderr
Can be found at */proc/\<pid\>/fd/*

##Linux use script substitution in command
./some_program $(*put any command, e.g. perl, here*)

##xxd
xxd data.txt > data.hex --> creates hexdump of data.txt
xxd -r data.hex --> reverses the operation

##Remove Github Secrets
Use (bfg Repo Cleaner)[http://rtyley.github.io/bfg-repo-cleaner/] with command
bfg --delete-files *File*
and after that push repo (maybe need to use --force switch)

##Add 20 GB to Qemu Hard drive
qemu-img resize *File*.qcow2 +20G

# Reverse Engineering

## Identify Encryption

## Identify Compression

## Identify Hashing
