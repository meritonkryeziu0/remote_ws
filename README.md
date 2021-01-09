#Websocket client remote controlls Groundig TV.

I recently bought a not so smart TV from Groundig.

Shortly after connecting the smart TV wifi I started scanning the local network for open ports and got the following results.
```
[foo@foobar:~/portscanner/]-$ nmap -A -oA nscan 192.168.0.102
Nmap scan report for 192.168.0.102
Host is up (0.0036s latency).

PORT     STATE SERVICE     VERSION
443/tcp  open  ssl/https?
8080/tcp open  http-proxy?
8085/tcp open  tcpwrapped

```

The https port was not interesting to look at becaus it didnt sent any data back.
Port 8080 returned "ok" to whaterver data I was sending
```
[foo@foobar:~/portscanner/]-$ nc 192.168.0.102 8080
hello
ok

```
The cherry on top of te cake was the 8085 tcwrapped port which was running a websocket server that sends basic remote controll commands via wifi ( I belive this service was left open when testing basic functionality of the TV-s because It can be cofigured to controll a bunch of TV-s simultaneously)
Entering https://192.168.0.102:8085/ sends you to the websocket client

![screencap.jpg](http://github.com/meritonkryeziu0/remote_ws/screencap.jpg)

When looking the inspect elements in the index file we see the main functions of the websocket server written in javascript

```javascript
   function mute() {
        if(websocket) {
            websocket.send("{\"event\":\"mute\"}");
        }
    }
    function volume_up() {
        if(websocket) {
            websocket.send("{\"event\":\"v+\"}");
        }
    }
    function volume_down() {
        if(websocket) {
            websocket.send("{\"event\":\"v-\"}");
        }
    }
    function memc() {
        if(websocket) {
            websocket.send("{\"event\":\"memc\"}");
        }
    }
    function backlight_up() {
        if(websocket) {
            websocket.send("{\"event\":\"bl+\"}");
        }
    }
    function backlight_down() {
        if(websocket) {
            websocket.send("{\"event\":\"bl-\"}");
        }
    }
    function power() {
        if(websocket) {
            websocket.send("{\"event\":\"power\"}");
        }
    }
    
    function init() {
        if(localStorage.ipaddress)       
            document.getElementById("ipaddress").value = localStorage.ipaddress;
        else
            document.getElementById("ipaddress").value = window.location.host;
            
        connectWS();
    }
```

