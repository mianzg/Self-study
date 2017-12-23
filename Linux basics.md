
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
5. mv {original_name} {new_name}: move or rename a file
6. rm {file}: delete a file
7. rm -rf {file/folder}
8. pwd: print the present working directory
9. cat / less / tail / head -n10 {file}: STDOUT contents of a file
10. mkdir {direc}: make an empty directory
11. grep -inr {String}: find a string in any files in this directory or child directories
12. column -s, -t <delimited_file>: display a comma-delimited file in columnar format
13. ssh {username}@{hostname}: connect to a remote machine
14 tree -LhaC 3: show directory struture 3 levels down (with file sizes and including hidden directories):
15. htop (or top): task manager // What's this?
16. pip install --user {pip_package}: Python package manager to install packages to !/.local/bin
17. pushd . : push directories onto the stack 
18. popd : pop directories onto the stack
19. dirs : view directories on the stack
20. cd - : change back to last directory
21. sed -i "s/{find}/{replace}/g" {file}: replace a string in a file
22. find
23. tmux new -s session
24. tmux attach -t session: c
25. wget {link} : download a webpage or web resource
26. curl -X POST -d "{key: value"} "http://..." : send an HTTP request to a web server
27
## Infrequent commands
1. 
