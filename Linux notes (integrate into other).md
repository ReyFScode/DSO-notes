

alias = sets a different name for a command / sequence of commands
[How to Create Bash Aliases | Linuxize](https://linuxize.com/post/how-to-create-bash-aliases/)
[How to list all aliases on Linux - Linux Tutorials - Learn Linux Configuration](https://linuxconfig.org/how-to-list-all-aliases-on-linux)
to list all aliases : `alias -p`
add an alias: `alias alias_name="command_to_run"`
remove an alias `unalias [name]`

ADD info about chaining commands with ';' (for chains that do not require all the previous command to complete successfully ) and with '&&' (for chaining commands that require the previous command to execute successfully)


SED
sed replace is (-i replaces permanently in file, no -i only replaces in output) `sed -i 's/something/somethingElse/(g if you want global [all instances])'

sed delete : `sed '1d` specify line number + d to delete that line

TR
[Tr Command in Linux with Examples | Linuxize](https://linuxize.com/post/linux-tr-command/)
can be used to replace/delete characters e.g = `echo "hello" | tr 'hell' 'bell'` output "bello" or you can use `tr "\n " "` to eliminate newlines in a file e.g. `ls | tr "\n" " "` will print the ls command in one single line.


sed v TR
- If you need to perform complex text transformations involving patterns or conditions, `sed` is the better choice. 
-  If you are looking to perform simple character-level replacements or deletions, `tr` is a more lightweight and efficient option.



tricks:

 combining multi-command output in the same line, use **echo -n** e.g. `echo -n "$(pwd)" ; echo "/somefile"` to display the pwd + custom filename on the same line.


add setfacl for adding access to a directory .../ file?