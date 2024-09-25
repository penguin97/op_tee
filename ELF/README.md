ELF (Executable and Linkable Format) 是一种常见的二进制文件格式，用于可执行文件、目标文件、共享库等。以下是一些与 ELF 文件相关的常用命令：  

file：这个命令可以显示文件的类型。对于 ELF 文件，它会显示文件是 32 位还是 64 位，是可执行文件还是共享库等。  
`file your_program`  
readelf：这个命令可以显示 ELF 文件的详细信息，如头部信息、节信息、符号表等。  
`readelf -a your_program`  
objdump：这个命令可以显示 ELF 文件的汇编代码、节信息、符号表等。  
`objdump -d your_program`  
nm：这个命令可以显示 ELF 文件的符号表。  
`nm your_program`  
ldd：这个命令可以显示 ELF 可执行文件或共享库的动态库依赖。  
`ldd your_program`  
strip：这个命令可以从 ELF 文件中删除符号表和调试信息，以减小文件大小。  
`strip your_program`    



[ELF manual page](https://man7.org/linux/man-pages/man5/elf.5.html)