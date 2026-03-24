# 🌐 Investigación: Direcciones IP

## ¿Qué es una dirección IP?

Una dirección IP es simplemente el "domicilio" de tu dispositivo en internet. Así como cada casa tiene una dirección para que el cartero sepa dónde entregar, cada dispositivo tiene una IP para que los datos sepan a dónde llegar.

---

## 📊 Tipos de IP — Conceptos base

| Concepto | Definición simple | Nivel técnico | Ejemplo |
|---|---|---|---|
| **IP** — Dirección de red | El "domicilio" de tu dispositivo en la red. Permite enviar y recibir datos. | Número lógico asignado a cada dispositivo. Puede cambiar. | Tu compu en la red de tu casa. |
| **IPv4** — Versión 4 | El formato de IP más usado hasta hoy. Tiene un límite de direcciones. | 4 grupos de números (0-255). Permite ~4.000 millones de direcciones. | `192.168.1.1` (tu router en casa) |
| **IPv6** — Versión 6 | El reemplazo de IPv4. Creado porque el mundo se quedó sin direcciones IPv4. | 8 grupos en hexadecimal. Permite 340 sextillones de direcciones únicas. | `2001:0db8:85a3::8a2e:0370:7334` |

---

## 🔍 Tipos de IP — Clasificación

- **Pública vs Privada:** La pública es tu cara hacia internet; la privada es solo dentro de tu red local. Tu casa tiene una sola IP pública, pero todos los dispositivos adentro tienen su propia IP privada.

- **Estática vs Dinámica:** Los servidores usan estática porque necesitan ser siempre localizables. Tu PC o celular usan dinámica porque no importa que cambie, tú no estás "ofreciendo" un servicio, solo consumiendo.

| Tipo | ¿Qué es? | ¿Cuándo se usa? | Ejemplo real |
|---|---|---|---|
| **Pública** — visible en internet | La IP que el mundo ve cuando salís a internet. La asigna tu proveedor de Internet (ISP). | Cuando tu app hace un fetch a una API externa. | `181.45.23.10` (IP de tu casa) |
| **Privada** — solo en red local | La IP dentro de tu red local (tu casa, oficina). No es visible desde internet. | Comunicación interna: tu app hablando con la base de datos. | `192.168.1.5` (tu PC en casa) |
| **Estática** — no cambia | Una IP fija que nunca cambia. Se configura manualmente. | Servidores en producción, donde los usuarios siempre necesitan encontrarte. | El servidor de google.com siempre en la misma IP. |
| **Dinámica** — cambia cada vez | IP asignada automáticamente por el router. Puede cambiar cada vez que te conectás. | Dispositivos del hogar, celulares, laptops en redes normales. | Tu celular recibe una IP distinta cada día. |

---

## 💻 Conexión directa con desarrollo

### ¿Qué IP usa tu backend cuando está en…?

- **Local (`localhost`):** `127.0.0.1` — Solo existe en tu máquina, no sale a la red.
- **Producción:** Tu servidor tiene una IP pública estática asignada por el proveedor cloud (AWS, Railway, Render, etc.)

### ¿Por qué no puedes acceder a `localhost` desde otro computador?

Desde otra máquina, estás buscando su propio bucle interno, no el tuyo.

### ¿Qué pasa cuando haces `npm run dev` o levantás un servidor en `127.0.0.1`?

`npm run dev` levanta el servidor en `127.0.0.1:3000` por defecto, solo en tu máquina.

### ¿Qué IP usa una base de datos en la nube?

Usa una IP privada interna (ej: `10.0.0.5`), solo visible desde el mismo servidor. Nunca expuesta directamente a internet.

---

## 🐛 Caso práctico real (debugging)

**Escenario:** "Tu frontend funciona, pero no logra conectarse al backend."

### ¿Podría ser problema de IP? ¿Por qué?

- El frontend apunta a una IP incorrecta (ej: `localhost` en vez de la IP del servidor).
- El backend está escuchando en otra IP/red y no se puede comunicar.

### ¿Qué revisarías primero?

1. URL del backend (IP + puerto).
2. Que el backend esté corriendo.
3. Que el puerto esté abierto.
4. Logs del backend y consola del frontend.

### ¿Qué errores típicos ocurren?

- IP incorrecta (`localhost` vs IP real).
- Puerto equivocado.
- Backend escuchando solo en `127.0.0.1`.
- Firewall bloqueando la conexión.
- CORS mal configurado.
- HTTPS vs HTTP mezclados.

---

## 🔧 DHCP y DNS

### ¿Qué hace DHCP?

Asigna automáticamente una IP a tu dispositivo cuando te conectás a una red (además de gateway y DNS).

### ¿Qué hace DNS?

Traduce nombres de dominio (ej: `google.com`) a direcciones IP.

### ¿Qué pasa cuando escribís `google.com` en el navegador?

1. Tu PC se conecta a la red → DHCP le da una IP y DNS.
2. Escribís `google.com` en el navegador.
3. Tu PC pregunta al DNS: "¿qué IP tiene google.com?"
4. DNS responde con una IP.
5. El navegador se conecta a esa IP (HTTP/HTTPS).
6. El servidor responde y carga la página.

---

## 🐄 Analogía (con vacas)

### IP

Imaginá que tenés dos vacas: una se llama **Manchas** y la otra **Cuernos Rotos**.

Cada vaca necesita una dirección para que el granjero sepa dónde enviarle el agua o comida. Esa dirección es la IP.

- **Manchas** tiene la IP `192.168.1.10`
- **Cuernos Rotos** tiene la IP `192.168.1.25`

Es como la matrícula del coche o el DNI de la vaca. Sin IP, nadie sabe dónde está.

### DNS

Es una libreta donde está anotado dónde vive la vaca.

Vos no le decís al cartero: "lleva esta carta a `192.168.1.10`". Le decís: "lleva esto a la vaca Manchas". El DNS es el que traduce el nombre fácil (`lavacamanchas.com`) en vez de la IP (`192.168.1.10`).

### DHCP

Imaginá que llegan 50 vacas nuevas a la granja cada día. No vas una por una diciéndoles sus IPs. Para eso está el DHCP, que reparte IPs automáticamente.

Cuando **Cuernos Rotos** llega nueva a la granja:

- La vaca grita: "¡Eh! ¡Necesito una IP!"
- El servidor DHCP responde: "Tranquila, te doy la `192.168.1.67` por 24 horas. Si te portás bien, te la renuevo."
- La vaca acepta y ya tiene dirección. Todo automático.

Si no hubiera DHCP, tendrías que configurar la IP a mano en cada vaca.

---

## 📝 Resumen

| Concepto | Descripción |
|---|---|
| **IP** | La dirección de la casa donde vive cada vaca para poder encontrarla. |
| **DNS** | Una libreta donde anotás el nombre de la vaca y su dirección. |
| **DHCP** | El granjero que le asigna automáticamente una casa a cada vaca cuando llega al campo. |
