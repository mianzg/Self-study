
# Linux basics
Reference: https://alexpetralia.com/posts/2017/6/26/learning-linux-bash-to-get-things-done
## Fundamentals
1. Shell: a text-based user interface through which commands are sent to the machine.
2. Bash: default language for shell on Linux
## Basic Concepts 
### Directory
1. present direc: .
2. parent direc: ..
3. user's home direc: ~
4. file system root: /
### Standard Input & Output
1. STDIN:
2. STDOUT:
### Piping
1. 
2. **writes/overwrites**: Take the standard out of the command on the left and writes/overwrites to a new file on the right.

   e.g.: ls > tmp.txt
3. **appends**: Take the stdout of the command on the left and appends to a new or existing file on the right.
   
   e.g.: date >> tmp.txt
### Wildcards
### Tab completion
### Quitting
1. CTRL + c
2. q
3. exit
## Common bash commands
1. ls -lha: list drectory verbose
2. vim / nano: command line editor
3. touch {file}: create a new empty file
4. cp -R {original_name} {new_name}: copy a file or directory (and all of its contents)
5. mv : move or rename a file
6. rm {file}: delete a file
7. rm -rf {file/folder}
8. pwd: print the present working directory
