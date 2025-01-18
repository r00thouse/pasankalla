[![GitHub Issues or Pull Requests](https://img.shields.io/github/issues/r00thouse/pasankalla?link=https%3A%2F%2Fgithub.com%2Fr00thouse%2Fpasankalla%2Fissues)](https://github.com/r00thouse/pasankalla/issues)

El **Proyecto Pasankalla** es una guía para mini routers portátiles y el cómo usarlos para:
  - Mejorar el acceso a Internet que haya en una zona, o conseguirlo donde no hay.
  - Configurar Wi-Fi de alta seguridad, sean redes abiertas o privadas.
  - Plataforma para desplegar [servicios locales](https://wiki.laotra.red/) sin Internet. Usado en educación en áreas rurales.

Este sitio está mantenido por [Luis Mita](https://luismita.com/) y el Hacklab r00thouse.

## Tabla de Contenidos

  - [Hardware](#hardware)
  - [Clases](#clases)
  - [Versiones](#versiones)


## <a name="hardware"></a>Hardware

El corazón de la Pasankalla es siempre un router. Puedes comprar uno o modificar algún aparato que tengas.

## <a name="clases"></a>Clases

Las clases nos ayudan a medir de forma tentativa las capacidades que tenemos.

| Clase | Radios | Descripción | Ejemplos |
| :---- | :----- | :---------- | :------- |
| Clase 1 | 1 radio 2.4G/5G 1x1 | Mini router con una sola radio y una sola antena | [TP-Link TL-MR3020](https://www.tp-link.com/us/home-networking/3g-4g-router/tl-mr3020/), [Raspberry Pi Zzero W](https://www.raspberrypi.com/products/raspberry-pi-zero-w/) |
| Clase 2 | 1 radio 2.4G/5G 2x2 | Mini router con una sola radio y dos antenas | [Nexx WT3020](https://openwrt.org/toh/nexx/wt3020) |
| Clase 3 | 2 radios Wi-Fi 4 | Mini router con dos radios Wi-Fi 4 |  |
| Clase 4 | 1 radio 2.4G, 1 radio 5G | Mini router con 1 radio 2.4G y un radio 5G (802.11ac) | [Cudy TR1200](https://openwrt.org/toh/cudy/tr1200) |
| Clase 5 | 1 radio 2.4G, 1 radio 5G, 1 radio 2.4G/5G 1x1 | Mini router de Clase 4 con un radio adicional | [Cudy TR1200](https://openwrt.org/toh/cudy/tr1200) + adaptador USB dual band de 1 antena |
| Clase 6 | 1 radio 2.4G, 1 radio 5G, 1 radio 2.4G/5G 2x2 | Mini router de Clase 4 con un radio adicional | [Cudy TR1200](https://openwrt.org/toh/cudy/tr1200) + adaptador USB dual band de dos antenas |
| Clase 8 | 1 radio 2.4G, 1 radio 5G | Mini router Wi-Fi 6 | [Cudy TR1200](https://openwrt.org/toh/cudy/tr1200) |
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
7. Cudy tr3000
8. Cudy tr1200

Estas versiones son producto de su tiempo, y se arman según lo que se pueda comprar o sea accesible en Bolivia.

