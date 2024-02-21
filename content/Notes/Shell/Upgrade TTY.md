https://security.stackexchange.com/questions/251326/upgrading-a-reverse-shell-with-stty-raw-echo-doesnt-work  

Simple Upgrade:  
``` bash
python3 -c 'import pty;pty.spawn("/bin/bash")'
```

```bash
export TERM=xterm
```