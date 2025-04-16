[![GitHub Issues or Pull Requests](https://img.shields.io/github/issues/r00thouse/pasankalla?link=https%3A%2F%2Fgithub.com%2Fr00thouse%2Fpasankalla%2Fissues)](https://github.com/r00thouse/pasankalla/issues)

El **Proyecto Pasankalla** es una guía para mini routers portátiles y el cómo usarlos para:
  - Mejorar el acceso a Internet que haya en una zona, o conseguirlo donde no hay.
  - Configurar Wi-Fi de alta seguridad, sean redes abiertas o privadas.
  - Plataforma para desplegar [servicios locales](https://wiki.laotra.red/) sin Internet. Usado en educación en áreas rurales.

Este sitio está mantenido por [Luis Mita](https://luismita.com/) y el Hacklab r00thouse.

## Tabla de Contenidos

  - [Hardware](#hardware)
  - [Arma uno](#arma-uno)
  - [Clases](#clases)
  - [Versiones](#versiones)
  - [Accesorios probados](#accesorios)
  - [Cómo Contribuir](#como-contribuir)


## <a name="hardware"></a>Hardware

El corazón de la Pasankalla es siempre un router. Puedes comprar uno o modificar algún aparato que tengas.

## <a name="arma-uno"></a>Arma uno

Para empezar, necesitas un router portátil. Necesitamos que éste soporte OpenWrt.

Cuando lo tengas, necesitas preparar un firmware en la siguiente web: https://firmware-selector.openwrt.org/
En la selección de paquetes, agrega al final de lo que ya está, algo como esto:
```
luci-proto-wireguard
luci-app-travelmate
kmod-rt2800-usb
kmod-mt76x0u
-wpad-basic-mbedtls
wpad-openssl
tcpdump-mini
ethtool
tailscale
iperf3
owut
```
Para lo más esencial, necesitas:

`luci-app-travelmate`: este programa permitirá escanear las redes cercanas a ti y conectarte a ellas.

`-wpad-basic-mbedtls wpad-openssl` se encarga de instalar el soporte Wi-Fi completo: incluye cliente para redes WPA2-EAP (necesario para conectarte a la red UMSA), además de soporte para crear una red segura WPA3 para ti.

`tcpdump`, dependencia del script para obtener IPs en redes cableadas (TODO).


Opcionalmente, revisa paquetes como:

`kmod-mt76x0u`, así se ven los drivers de tu adaptador Wi-Fi USB adicional (si usarás uno)

`luci-proto-wireguard`, soporte de túnel VPN Wireguard si deseas usar uno.

`tailscale`, soporte de túnel Tailscale.

`iperf3`, para probar la velocidad del enlace de un punto a otro.

`owut`, para actualizar fácilmente OpenWrt.

---

Listo, con el firmware compilado e instalado en el router, ya podemos empezar a [configurar Travelmate](https://openwrt.org/docs/guide-user/network/wifi/wifiextenders/travelmate) a nuestro gusto. También, luego de eso, podemos configurar [nuestra propia red](https://openwrt.org/docs/guide-quick-start/basic_wifi) para que nosotros tengamos acceso.

Esta configuración se llama [AP+STA](https://openwrt.org/docs/guide-user/network/wifi/wifiextenders/ap_sta) y es muy similar a la de un repetidor.

A continuación se ponen las configuraciones más especiales de la Pasankalla.

### <a name="red-umsa"></a>Conectándonos a la red UMSA Wi-Fi

Para que tu Pasankalla se conecte a la red UMSA por Wi-Fi, necesitas agregar esta red tal como lo harías normalmente en Travelmate. Luego debes editar los archivos de configuración de esta manera.

/etc/config/network:
```
config interface 'trm_wwan'
        option proto 'dhcp'
        option metric '100'
        option hostname '*'
        option vendorid 'android-dhcp-10'
```
Esto hace que datos como el nombre del router no se propaguen públicamente al resto de clientes. Considera también usar `option peerdns '0'` y usar otro DNS por motivos de privacidad.

/etc/config/wireless:
```
config wifi-iface 'trm_uplink5'
        option device 'radio2'
        option mode 'sta'
        option ssid 'UMSA'
        option encryption 'wpa2+ccmp'
        option eap_type 'peap'
        option auth 'EAP-MSCHAPV2'
        option identity 'usuario'
        option password 'contraseña'
        option network 'trm_wwan'
        option disabled '1'
```
Considera también usar `option macaddr 'UN:AM:AC:RA:ND:OM'` por motivos de privacidad, cambiando la MAC por otra generada aleatoriamente.

Adicionalmente, si tienes problemas para iniciar sesión (un tiempo el servidor RADIUS de la UMSA solo soportaba cifrados obsoletos), prueba con estas [instrucciones para habilitar WPA2-EAP en OpenWrt con cifrados TLS obsoletos](https://forum.openwrt.org/t/wpa2-enterprise-client-802-1x-how-to-allow-weak-ssl-ciphers/229485/4?u=enmaskarado).

### <a name="red-abierta"></a>Abrir una red abierta segura "Pasankalla gratis"
Puedes crear a mano una red abierta para que los demás se conecten. Aunque puedes hacerlo por la interfaz web LuCI, podemos hacer algo mejor mediante terminal: usar WPA3-OWE.
Con esto, la red abierta tendrá cifrado y será segura, a pesar de no tener contraseña :)

La Pasankalla original tiene esta configuración:

/etc/config/network
```
config interface 'lanclientes'
        option proto 'static'
        option device 'br-lan.10'
        option ipaddr '100.102.13.37'
        option netmask '255.255.0.0'
```

/etc/config/dhcp
```
config dhcp 'lanclientes'
        option interface 'lanclientes'
        option start '3585'
        option limit '60000'
        option leasetime '12h'
        option force '1'
```

/etc/config/wireless
```
config wifi-iface 'wifilanpa2'
        option device 'radio0'
        option mode 'ap'
        option ifname 'phy0-pa'
        option ssid 'Pasankalla gratis'
        option encryption 'none'
        option network 'lanclientes'
        option ieee80211k '1'
        option wnm_sleep_mode '1'
        option wnm_sleep_mode_no_keys '1'
        option bss_transition '1'
        option proxy_arp '1'
        option owe_transition_ifname 'phy0-paw'
        option disabled '1'

config wifi-iface 'wifilanpa5'
        option device 'radio1'
        option mode 'ap'
        option ifname 'phy1-pa'
        option ssid 'Pasankalla gratis'
        option encryption 'none'
        option isolate '1'
        option network 'lanclientes'
        option ieee80211k '1'
        option wnm_sleep_mode '1'
        option wnm_sleep_mode_no_keys '1'
        option bss_transition '1'
        option proxy_arp '1'
        option owe_transition_ifname 'phy1-paw'
        option disabled '1'

config wifi-iface 'wifilanpaw2'
        option device 'radio0'
        option mode 'ap'
        option ifname 'phy0-paw'
        option ssid 'Pasankalla gratis WPA3'
        option encryption 'owe'
        option network 'lanclientes'
        option ieee80211k '1'
        option wnm_sleep_mode '1'
        option wnm_sleep_mode_no_keys '1'
        option bss_transition '1'
        option proxy_arp '1'
        option disabled '1'
        option hidden '1'
        option owe_transition_ifname 'phy0-pa'

config wifi-iface 'wifilanpaw5'
        option device 'radio1'
        option mode 'ap'
        option ifname 'phy1-paw'
        option ssid 'Pasankalla gratis WPA3'
        option encryption 'owe'
        option isolate '1'
        option network 'lanclientes'
        option ieee80211k '1'
        option wnm_sleep_mode '1'
        option wnm_sleep_mode_no_keys '1'
        option bss_transition '1'
        option proxy_arp '1'
        option hidden '1'
        option owe_transition_ifname 'phy1-pa'
        option disabled '1'
        option ocv '0'
```
Ojo que estas interfaces están apagadas, véase el `disabled '1'`. Yo prendo esta red mediante un slider físico, continúa leyendo.


## <a name="clases"></a>Clases de Pasankalla

Las clases nos ayudan a medir de forma tentativa las capacidades que tenemos.

| Clase | Radios | Descripción | Ejemplos |
| :---- | :----- | :---------- | :------- |
| Clase 1 | 1 radio 2.4G/5G 1x1 | Mini router con una sola radio y una sola antena | [TP-Link TL-MR3020](https://www.tp-link.com/us/home-networking/3g-4g-router/tl-mr3020/), [Raspberry Pi Zero W](https://www.raspberrypi.com/products/raspberry-pi-zero-w/) |
| Clase 2 | 1 radio 2.4G/5G 2x2 | Mini router con una sola radio y dos antenas | [Nexx WT3020](https://openwrt.org/toh/nexx/wt3020) |
| Clase 3 | 2 radios Wi-Fi 4 | Mini router con dos radios Wi-Fi 4 |  |
| Clase 4 | 1 radio 2.4G, 1 radio 5G | Mini router con 1 radio 2.4G y un radio 5G (802.11ac) | [Cudy TR1200](https://openwrt.org/toh/cudy/tr1200) |
| Clase 5 | 1 radio 2.4G, 1 radio 5G, 1 radio 2.4G/5G 1x1 | Mini router de Clase 4 con un radio adicional | [Cudy TR1200](https://openwrt.org/toh/cudy/tr1200) + adaptador USB dual band de 1 antena |
| Clase 6 | 1 radio 2.4G, 1 radio 5G, 1 radio 2.4G/5G 2x2 | Mini router de Clase 4 con un radio adicional | [Cudy TR1200](https://openwrt.org/toh/cudy/tr1200) + adaptador USB dual band de dos antenas |
| Clase 8 | 1 radio 2.4G, 1 radio 5G | Mini router Wi-Fi 6 | [Cudy TR3000](https://openwrt.org/toh/cudy/tr3000) |
| Clase 9 | 1 radio 2.4G, 1 radio 5G, 1 radio 2.4G/5G 1x1 | Mini router Wi-Fi 6 | [Cudy TR3000](https://openwrt.org/toh/cudy/tr3000) + adaptador USB dual band de 1 antena |
| Clase 10 | 1 radio 2.4G, 1 radio 5G, 1 radio 2.4G/5G 2x2 | Mini router Wi-Fi 6 | [Cudy TR3000](https://openwrt.org/toh/cudy/tr3000) + adaptador USB dual band de 2 antenas |

Los ejemplos no son recomendaciones de compra.

Capacidades de las clases:

| Clase | Estándar | Velocidad Local | Capacidad |
| :---- | :------- | :-------------- | :-------- |
| Clase 1 | Wi-Fi 4 | ~20Mbps | Hasta 8 clientes + una conexión upstream |
| Clase 2 | Wi-Fi 4 | ~40Mbps | Hasta 8 clientes + una conexión upstream |
| Clase 3 | Wi-Fi 4 | ~50Mbps | Hasta 8 clientes + una conexión upstream |
| Clase 4 | Wi-Fi 5 | ~50Mbps en modo híbrido, ~500 Mbps en modo AP | 8 clientes modo híbrido, 64 clientes en modo AP |
| Clase 5 | Wi-Fi 5 | ~500 Mbps | 64 clientes en modo AP |
| Clase 6 | Wi-Fi 5 | ~500 Mbps | 64 clientes en modo AP |
| Clase 8 | Wi-Fi 6 | ~50Mbps en modo híbrido, ~900 Mbps en modo AP | 8 clientes modo híbrido, 208 clientes en modo AP |
| Clase 9 | Wi-Fi 6 | ~900 Mbps | 208 clientes en modo AP |
| Clase 10 | Wi-Fi 6 | ~900 Mbps | 208 clientes en modo AP |


## <a name="versiones"></a>Versiones

[La historia de la Pasankalla](https://taller.luismita.com/2024/07/pasankalla-gratis-la-historia-del-proyecto/).

1. Un módem 4G portátil
2. El modem 4G mas un router portátil
3. Raspberry Pi Zero con un rtl8821au
4. Raspberry Pi 3 con el rtl8812au
5. Raspberry Pi 3 con el rtl8812au y un router movil
6. Router Doméstico Confiabits
7. Cudy TR3000
8. Cudy TR1200

Estas versiones son producto de su tiempo, y se arman según lo que se pueda comprar o sea accesible en Bolivia.

## <a name="accesorios"></a>Accesorios probados


| Accesorio | Descripción | Instalación | Experiencia |
| :-------- | :---------- | :---------- | :---------- |
| TP-Link Archer T4UHP | Adaptador USB Wi-Fi 5 dual-band 2x2 | Compilar mis [drivers rtw88 para OpenWrt](https://github.com/LuisMitaHL/rtw88-openwrt) | Funcionan bien. Necesitan una batería externa con 3A o más de capacidad de corriente. En modo USB2 pierde conexión por el límite de 500mA del puerto, necesitas tenerlo en USB3. El configurador de TX power no funciona, siempre emite al máximo. |
| TP-Link TL-WDN4200 | Adaptador USB Wi-Fi 4 dual-band 3x3 | Instalar `kmod-rt2800-usb` | No tiene buena potencia de salida, me imagino por bugs del driver. Es grande e incómodo de manejar, por la falta de cable. |
| Cudy WU1300S | Adaptador USB Wi-Fi 5 dual-band 2x2 pequeño | Compilar mis [drivers rtw88 para OpenWrt](https://github.com/LuisMitaHL/rtw88-openwrt) | No funciona bien, se sobrecalienta y deja de funcionar. El configurador de TX power no funciona, siempre emite al máximo. |
| Genérico | Adaptador USB Wi-Fi 5 dual-band 1x1 pequeño | Compilar mis [drivers rtw88 para OpenWrt](https://github.com/LuisMitaHL/rtw88-openwrt) | Funciona bien hasta que se sobrecalienta y deja de funcionar. Consume poco, hasta 0.14A. El configurador de TX power no funciona, siempre emite al máximo. |

## <a name="como-contribuir"></a>Cómo Contribuir

Este proyecto es de libre uso. Hay varias formas de aportar a éste:
  
  * Mejorando las instrucciones: muchas pueden no quedar tan claras y viene bien mejorarlas.
  * Agregando tus experiencias con el uso o el armado.
  * Reportando el uso de distintos componentes como adaptadores Wi-Fi

Eres bienvenid@ a forkear este repositorio y enviarnos un pull request :)
