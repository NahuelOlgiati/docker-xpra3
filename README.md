## Pluggable X11 GUI for docker images  

*Run GUI applications in Docker with the same client([Xpra](https://xpra.org/)) on Windows, GNU/Linux, MacOS or web-browser over ssh or tcp.*

### Try It!
``` bash
docker run -d \
 --name x11-bridge \
 -e MODE="tcp" \
 -e XPRA_HTML="yes" \
 -e DISPLAY=:14 \
 -p 10000:10000 \
 nahuelolgiati/xpra3

docker run -d \
 --name emacs-1 \
 --volumes-from x11-bridge \
 -e DISPLAY=:14 \
 jare/emacs emacs

docker run -d \
 --name emacs-2 \
 --volumes-from x11-bridge \
 -e DISPLAY=:14 \
 jare/emacs emacs
```
Then visit `http://localhost:10000/index.html?encoding=rgb32`
See `http://localhost:10000/connect.html` for options

### Or via ssh:
``` bash
docker run -d \
 --name x11-bridge \
 -e MODE="ssh" \
 -p 22:22 \
 -v ~/.ssh/pub_rsa:/etc/pub-keys/me.pub \
 -e DISPLAY=:14 \
 nahuelolgiati/xpra3

docker run -d \
 --name emacs-1 \
 --volumes-from x11-bridge \
 -e DISPLAY=:14 \
 jare/emacs emacs

docker run -d \
 --name emacs-2 \
 --volumes-from x11-bridge \
 -e DISPLAY=:14 \
 jare/emacs emacs
```
Then attach to the Xpra server via cmd tool or Xpra's GUI app:
`xpra attach --encoding=rgb --ssh="ssh -o StrictHostKeyChecking=no -p 22" ssh:xpra@localhost:14`

 - Note `-o StrictHostKeyChecking=no` - it allows us to ignore server's identity instead adding it to `known_hosts`. If you want to rely on the identity you should use a privately built image.
 - All names of the pub keys should end with `.pub` i.e. `/etc/pub-keys/<key_name>.pub`
 - If Xpra lags, select `Lowest Bandwidth` when using Xpra app with a local Xpra host.
 - With Windows client you'll need [`Pageant`](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) you can convert your rsa keys to `Pageant`'s format with [`PuTTYgen`](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)
 - GUI app in your image may require`libGL.so.1` (provided by libgl1-mesa-glx on Ubuntu) and font
 - It is good idea to use [ssl proxy](https://github.com/JrCs/docker-letsencrypt-nginx-proxy-companion) or mount a certificate (see Xpra start-up message) if you want to use tcp or html mode beyond local network


*Webcam and sound are currently disabled*
