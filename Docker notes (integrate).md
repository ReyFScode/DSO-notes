


### **docker image/container list with --format**

--format allows you to tailor the output of docker commands (mainly used for ls commands), to output specific fields formatted to your specs (you can add input in while accessing particular fields (e.g Wahoo={{.ID}} would give you wahoo=Image_ID)

[Format command and log output | Docker Docs](https://docs.docker.com/config/formatting/)

**examples:**
`docker image ls --format 'ID= {{.ID}}   NAME:TAG= {{.Repository}}:{{.Tag}}'` == outputs docker image ls with only ID and name+tag in the format  *ID=something   Name:tag= something:something*

`docker ps -a --format 'ID= {{.ID}}   NAME= {{.Names}}   BASE_IMAGE ={{.Image}}'` == outputs docker container info with only ID and name in the format  *ID=something   Name= something*