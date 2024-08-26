ADD exec command - [bing.com/ck/a?!&&p=3390f0fd7c006bdea4672d562be4f6055433186a9e8877add8130d0361879f20JmltdHM9MTcyNDI4NDgwMCZpZ3VpZD0zNDdiZWFiMS00NWU5LTZiYTYtMzQ0ZS1mZTEzNDQzZjZhZWEmaW5zaWQ9NTUxOA&ptn=3&ver=2&hsh=4&fclid=347beab1-45e9-6ba6-344e-fe13443f6aea&psq=what+is+exec+in+bash&u=a1aHR0cHM6Ly9zdGFja292ZXJmbG93LmNvbS9xdWVzdGlvbnMvMTgzNTExOTgvd2hhdC1hcmUtdGhlLXVzZXMtb2YtdGhlLWV4ZWMtY29tbWFuZC1pbi1zaGVsbC1zY3JpcHRz&ntb=1](https://www.bing.com/search?pglt=673&q=what+is+exec+in+bash&cvid=1d4bb09633e646d384c71a9a0701669d&gs_lcrp=EgZjaHJvbWUyBggAEEUYOTIGCAEQABhAMgYIAhAAGEAyBggDEAAYQDIGCAQQABhAMgYIBRAAGEAyBggGEAAYQDIGCAcQABhAMgYICBAAGEAyCAgJEOkHGPxV0gEINzM3OWowajGoAgCwAgA&FORM=ANNAB1&PC=U531)

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
can be used to replace/delete characters e.g = `echo "hello" | tr 'hell' 'bell'` output "bello" or you can use `tr "\n " "` to eliminate newlines in a file e.g. `ls | tr " " "\n"` will print the ls command with each entry on a newline

sed v TR
- If you need to perform complex text transformations involving patterns or conditions, `sed` is the better choice. 
-  If you are looking to perform simple character-level replacements or deletions, `tr` is a more lightweight and efficient option.



tricks:

 combining multi-command output in the same line, use **echo -n** e.g. `echo -n "$(pwd)" ; echo "/somefile"` to display the pwd + custom filename on the same line.


add setfacl for adding access to a directory .../ file?