# 🌐 Managing IP Addresses — Actividad Grupal

Guía práctica sobre direcciones IP y su gestión, conectado directamente con el trabajo de un desarrollador: APIs, servidores, despliegues y debugging.

> *"Cada vez que haces un fetch, levantas un backend o usas una base de datos… estás trabajando con direcciones IP, aunque no lo veas. Si no entiendes esto, no puedes diagnosticar errores reales en producción."*

---

## 🎯 Objetivo

- Comprender qué es una dirección IP y sus tipos
- Entender cómo impacta directamente en el desarrollo
- Diagnosticar errores de red relacionados con IPs como un developer profesional

---

## 1. ¿Qué es una dirección IP?

Una dirección IP es el identificador único de un dispositivo dentro de una red. Sin ella, los paquetes de datos no sabrían a dónde ir. Es el equivalente a la dirección de una casa: sin dirección, el correo no llega.

| Concepto | Definición simple | Nivel técnico breve | Ejemplo |
|---|---|---|---|
| **IP** | Identificador único de un dispositivo en una red. | Número que permite enrutar paquetes entre dispositivos dentro y fuera de una red. | `192.168.1.5` (privada) / `142.250.80.46` (pública) |
| **IPv4** | La versión más usada. Formato de 4 números separados por puntos. | 32 bits. Permite ~4.300 millones de direcciones únicas. Se están agotando. | `192.168.0.1`, `8.8.8.8` |
| **IPv6** | La versión nueva. Formato más largo con letras y números. | 128 bits. Permite una cantidad prácticamente infinita de direcciones. | `2001:0db8:85a3::8a2e:0370:7334` |

---

## 2. Tipos de IP

- **IP pública vs privada:** La pública es visible desde internet y la asigna tu ISP o proveedor cloud. La privada existe solo dentro de una red local y no es accesible desde afuera.
- **IP estática vs dinámica:** La estática no cambia nunca. La dinámica se asigna automáticamente cada vez que el dispositivo se conecta y puede cambiar.

| Tipo | ¿Qué es? | ¿Cuándo se usa? | Ejemplo real |
|---|---|---|---|
| **Pública** | Visible desde internet. La asigna el ISP o el proveedor cloud. | Servidores en producción, APIs accesibles desde fuera. | Servidor en AWS con IP pública `54.23.11.80`. |
| **Privada** | Solo existe dentro de una red local. No accesible desde internet. | Dispositivos en red doméstica, servicios internos en data center. | Laptop en casa: `192.168.1.5`. Base de datos interna: `10.0.0.4`. |
| **Estática** | No cambia. Se configura manualmente o se reserva. | Servidores que necesitan siempre la misma dirección. | Base de datos siempre en `10.0.0.10` para que la app siempre lo encuentre. |
| **Dinámica** | Se asigna automáticamente y puede cambiar en cada conexión. | Dispositivos de usuario, instancias cloud temporales. | Laptop recibe IP distinta cada vez que se conecta al WiFi. |

---

## 3. Conexión directa con desarrollo

| Entorno | IP | Qué significa |
|---|---|---|
| Local (`localhost`) | `127.0.0.1` | Dirección de loopback. Solo tu propia máquina puede acceder. |
| Red local | `192.168.x.x` | Tu máquina dentro de la red local. Otros en la misma red pueden acceder. |
| Producción | IP pública (ej: `54.23.11.80`) | Accesible desde internet. Asignada por el proveedor cloud o ISP. |

### ¿Por qué no puedes acceder al `localhost` de otro computador?
Porque `127.0.0.1` es una dirección de loopback: solo existe dentro de tu propia máquina. No hay paquete que salga a la red. Es como intentar llamar al número de tu propio teléfono desde otro teléfono.

### ¿Qué pasa cuando haces `npm run dev`?
El servidor escucha peticiones solo en tu máquina. Si quieres que otros en tu red local también puedan acceder, debes levantar el servidor en `0.0.0.0` — eso significa "escuchar en todas las interfaces de red disponibles".

### ¿Qué IP usa una base de datos en la nube?
Casi siempre una IP privada dentro del VPC — por seguridad, no debería ser accesible desde internet. Tu servidor de app se conecta a ella usando esa IP privada interna (ej: `10.0.0.4:5432`).

---

## 4. Caso práctico — frontend no se conecta al backend

**¿Podría ser problema de IP?** Sí, y es uno de los errores más comunes. El frontend está apuntando a `localhost` o a una IP incorrecta. En producción, `localhost` no funciona porque el frontend y el backend corren en máquinas distintas.

### ¿Qué revisarías primero?
- ¿La URL del backend en el frontend apunta a la IP/dominio correcto en producción?
- ¿El backend está escuchando en `0.0.0.0` y no solo en `127.0.0.1`?
- ¿El puerto está abierto en el firewall?
- ¿La IP del servidor cambió? (IP dinámica sin dominio fijo)

### Errores típicos

| Error | Causa |
|---|---|
| `Connection refused` | El backend no está corriendo o escucha en IP incorrecta. |
| `ERR_NAME_NOT_RESOLVED` | El dominio o IP en la URL del fetch no existe o está mal escrito. |
| `Network timeout` | El puerto está bloqueado por firewall o la IP no es alcanzable. |
| Frontend apuntando a `localhost` en prod | El dev olvidó cambiar la URL de desarrollo a producción. |

---

## 5. DHCP y DNS

### ¿Qué hace DHCP?
DHCP (Dynamic Host Configuration Protocol) es el servicio que asigna IPs automáticamente a los dispositivos cuando se conectan a una red. Sin él, tendrías que configurar manualmente la IP de cada dispositivo.

### ¿Qué hace DNS?
DNS traduce nombres de dominio legibles (como `google.com`) a IPs numéricas (como `142.250.80.46`). Sin DNS tendrías que memorizar IPs para acceder a cada sitio.

### ¿Qué pasa cuando escribes `google.com` en el navegador?

```
1. Tu navegador busca en su caché si ya conoce la IP de google.com
2. Si no, pregunta al DNS Resolver de tu ISP
3. El resolver pregunta al servidor raíz → servidor TLD (.com) → servidor de Google
4. Obtiene la IP: 142.250.80.46
5. Tu navegador se conecta directamente a esa IP
6. El servidor de Google responde con la página

Todo esto ocurre en milisegundos.
```

---

## 6. Analogías

| Concepto | Analogía |
|---|---|
| **IP** | La dirección de tu casa: sin ella, el correo no sabe dónde llegar. |
| **DNS** | La agenda de contactos: no memorizas el número, buscas el nombre y la agenda te da el número. |
| **DHCP** | El administrador de un edificio que asigna automáticamente un departamento a cada nuevo inquilino cuando llega. |

---

## 7. Bonus — NAT, Docker y AWS EC2

### NAT (Network Address Translation)
Permite que múltiples dispositivos con IPs privadas compartan una sola IP pública para salir a internet. El router lleva un registro de qué dispositivo interno hizo cada petición para devolver la respuesta correcta. Sin NAT, necesitaríamos una IP pública por cada dispositivo del mundo.

### IPs en Docker
Cada contenedor Docker recibe su propia IP privada dentro de una red virtual interna (ej: `172.17.0.2`). Los contenedores se comunican entre sí usando esas IPs o por nombres de servicio en Docker Compose. Para que el exterior acceda, se mapea un puerto del host al puerto del contenedor.

### IPs en AWS EC2
Una instancia EC2 tiene siempre una IP privada (dentro del VPC). Si necesita ser accesible desde internet, se le asigna una IP pública que cambia cada vez que la instancia se reinicia. Para tener una IP pública fija se usa una **Elastic IP**: una IP estática que puedes asociar permanentemente a tu instancia.

---

## 8. Glosario de términos

| Término | ¿Qué es? (en simple) | Ejemplo de uso |
|---|---|---|
| **IP** | Identificador único de un dispositivo en una red. Como la dirección de una casa. | Tu laptop: `192.168.1.5`. Servidor AWS: `54.23.11.80`. |
| **IPv4** | Versión clásica de IP. Formato de 4 números separados por puntos. | `192.168.0.1`, `8.8.8.8` (DNS de Google). |
| **IPv6** | Versión nueva de IP con más capacidad. Formato largo con letras y números. | `2001:0db8::8a2e:0370:7334`. Cada dispositivo puede tener dirección única. |
| **IP pública** | Dirección visible desde internet. La asigna tu ISP o proveedor cloud. | Servidor en AWS con IP `54.23.11.80`. Cualquiera en internet puede conectarse. |
| **IP privada** | Dirección solo dentro de una red local. No accesible desde internet. | Laptop en casa: `192.168.1.5`. Base de datos en VPC: `10.0.0.4`. |
| **IP estática** | Dirección que no cambia. Se configura manualmente o se reserva. | Servidor de BD siempre en `10.0.0.10` para que la app siempre lo encuentre. |
| **IP dinámica** | Dirección asignada automáticamente que puede cambiar en cada conexión. | Tu laptop recibe IP distinta cada vez que se conecta al WiFi. |
| **localhost** | Nombre especial que apunta siempre a tu propia máquina. Equivale a `127.0.0.1`. | `npm run dev` levanta tu app en `http://localhost:3000`. Solo tú puedes acceder. |
| **127.0.0.1** | Dirección de loopback. Todo lo que se envía aquí vuelve a tu propia máquina. | `http://127.0.0.1:5000` solo funciona en tu computador. Nadie más puede verlo. |
| **0.0.0.0** | Significa escuchar en todas las interfaces de red disponibles. | Backend en `0.0.0.0:3000` permite acceso tanto local como desde la red. |
| **Puerto** | Número que identifica un servicio dentro de un servidor. | Puerto `3000` = app local. Puerto `5432` = PostgreSQL. Puerto `443` = HTTPS. |
| **DHCP** | Servicio que asigna IPs automáticamente cuando un dispositivo se conecta a la red. | Al conectar tu laptop al WiFi, el router le asigna automáticamente `192.168.1.8`. |
| **DNS** | Sistema que traduce nombres de dominio a IPs. Sin él tendrías que memorizar números. | `api.miapp.com` → DNS lo convierte en `54.23.11.80` → tu app se conecta. |
| **NAT** | Permite que dispositivos con IPs privadas compartan una IP pública para salir a internet. | 5 dispositivos en tu casa salen a internet con la misma IP pública del router. |
| **Loopback** | Interfaz de red virtual que apunta siempre a tu propia máquina. No sale a la red. | Probar tu backend localmente. Todo lo que va a `127.0.0.1` se queda en tu máquina. |
| **Elastic IP** | IP pública fija en AWS que no cambia aunque reinicies tu servidor. | Instancia EC2 con Elastic IP siempre tiene la misma dirección pública. |
| **VPC** | Red privada virtual dentro de un proveedor cloud. Como tu red local, pero en la nube. | Servidor y base de datos en el mismo VPC con IPs privadas, aislados del exterior. |
| **Firewall** | Sistema que filtra el tráfico de red según reglas. Bloquea conexiones no autorizadas. | Backend no accesible aunque esté corriendo: el firewall puede bloquear el puerto `3000`. |
| **Contenedor (Docker)** | Entorno aislado donde corre una app con su propia IP privada interna. | App en Docker con IP `172.17.0.2`. Se mapea un puerto del host para acceder desde fuera. |

---

## 💡 TL;DR

| Concepto | En simple |
|---|---|
| **IP** | Identificador único de un dispositivo en red — sin ella los paquetes no saben a dónde ir |
| **IPv4 / IPv6** | Formatos de IP — IPv4 se está agotando, IPv6 es el reemplazo con más capacidad |
| **Pública / Privada** | Pública = visible en internet. Privada = solo en red local |
| **Estática / Dinámica** | Estática = nunca cambia. Dinámica = puede cambiar en cada conexión |
| **localhost** | Solo accesible desde tu propia máquina — en producción necesitas IP pública o dominio |
| **DHCP** | Asigna IPs automáticamente cuando un dispositivo se conecta |
| **DNS** | Traduce dominios a IPs para que no tengas que memorizar números |
| **Elastic IP** | IP pública fija en AWS — no cambia aunque reinicies el servidor |

---

> *"Saber programar sin entender IPs es como saber escribir cartas sin saber direcciones… puedes hacer el mensaje perfecto, pero nunca va a llegar."*
