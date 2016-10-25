# global schema
##  Istall Shadowsocks-qt5
* sudo add-apt-repository ppa:hzwhuang/ss-qt5
* sudo apt-get update
* sudo apt-get install shadowsocks-qt5

## System settings 
* System settings > Network > Network Proxy
* Method -> Manual
* Socks: 127.0.0.1:1080

# pac schema
## Istall genpac
* sudo apt-get install python-pip
* sudo pip install genpac

## use genpac to creat pac
* genpac -p "SOCKS5 127.0.0.1:1080" --gfwlist-proxy="SOCKS5 127.0.0.1:1080" --gfwlist-url=https://raw.githubusercontent.com/gfwlist/gfwlist/master/gfwlist.txt --output="autoproxy.pac"
* [more](https://github.com/JinnLynn/genpac#usage)

## System settings 
* System settings > Network > Network Proxy
* Method -> Automatic
* Configuration URL: /home/user/autoproxy.pac

# Terminal broken wall
## WHY
Shadowsocks only support socks4 or socks5 don`t support http or https,But Terminal only support http or hppts,so need Privoxy.

## Install privoxy
* sudo apt-get install privoxy

## CONFIGURE
* sudo vim /etc/privoxy/config
  * Find 4.1. listen-address like :listen-address  localhost:8118
* Find 5.2. forward-socks4, forward-socks4a, forward-socks5 and forward-socks5t // under this add
  * forward-socks5 / 127.0.0.1:1080  .
  
## REBOOT
* sudo /etc/init.d/privoxy restart
* export http_proxy="127.0.0.1:8118"
* export https_proxy="127.0.0.1:8118"

## SPEEDY
* sudo vim /etc/rc.local // before exit 0 add 
  * sudo /etc/init.d/privoxy start
* sudo vim /etc/profile // end of add
  * export http_proxy="127.0.0.1:8118"
  * export https_proxy="127.0.0.1:8118"

