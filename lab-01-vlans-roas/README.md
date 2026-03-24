# Lab 01 — Red de Oficina con VLANs, Router-on-a-Stick, DHCP y VoIP

**Herramienta:** Cisco Packet Tracer | **Nivel:** Intermedio  
**Temas:** VLANs · 802.1Q Trunk · Router-on-a-Stick · DHCP · Inter-VLAN Routing · VoIP · ACL

---

## ¿Qué se simuló?

Una empresa pequeña necesitaba organizar su red por departamentos. Tenían todo en una sola red plana — sin segmentación, sin control — y querían mejorar la seguridad sin gastar en infraestructura costosa.

El reto: hacer todo eso con **1 router, 2 switches y 1 access point**, para unos 20 dispositivos entre PCs, laptops, teléfonos IP, una impresora y un servidor.

Los requerimientos principales eran:

- Cada área en su propia red (Administración, Ventas, Tecnología, Recepción, Invitados)
- Las IPs se asignan solas — nada de configurar cada equipo a mano
- El equipo de Tecnología puede acceder a todo porque da soporte
- Ventas no puede meterse a la red de Administración
- Los invitados solo pueden navegar en internet, nada de ver recursos internos
- Telefonía IP en una VLAN separada

---

## Diseño de la red

### VLANs

| VLAN | Nombre | Red | Gateway |
|------|--------|-----|---------|
| 10 | Administración | 192.168.10.0/28 | 192.168.10.1 |
| 20 | Ventas | 192.168.20.0/28 | 192.168.20.1 |
| 30 | Tecnología | 192.168.30.0/28 | 192.168.30.1 |
| 40 | Recepción | 192.168.40.0/28 | 192.168.40.1 |
| 50 | Invitados | 192.168.50.0/24 | 192.168.50.1 |
| 100 | VoIP | 192.168.100.0/28 | 192.168.100.1 |

Las subnets /28 dan 14 hosts usables por departamento, suficiente para una oficina pequeña. La VLAN de Invitados usa /24 porque es inalámbrica y puede crecer rápido con dispositivos móviles.

### Dispositivos por área

| VLAN | Equipos |
|------|---------|
| 10 — Administración | PCA1, PCA2, PCA3, PCA3, LaptopA5, LaptopA6, PrinterpA7 |
| 20 — Ventas | PCV1, PCV2, PCV3, PCV4, PCV5, PCV6, PCV7, PCV8 |
| 30 — Tecnología | LaptopT1, LaptopT2, LaptopT3, ServersT4 |
| 40 — Recepción | PCR1 (conectado a través del teléfono IP) |
| 50 — Invitados | LaptopI2, LaptopI3, Laptop7I4 (Wi-Fi) PCI1 (Switch) |
| 100 — VoIP | IP RT1, IP RT2 |

---

## Topología

![Topología de red](https://i.ibb.co/84NjV4sY/Topologia.png)

### Por qué se diseñó así

**Topología en estrella con un switch central** — en una empresa pequeña no siempre es viable comprar un switch por piso o por área. Centralizar todo en un switch administrable es una solución real y funcional. Además, para este lab sirve para practicar toda la configuración en un solo dispositivo.

**Router-on-a-Stick** — el router se conecta al switch central por un solo enlace trunk (G0/1). Cada VLAN tiene su propia subinterfaz en el router, lo que permite el ruteo entre VLANs sin necesidad de un switch de capa 3.

**Switch1 en modo trunk** — este switch secundario no solo maneja la VLAN de Invitados. Está en trunk y transporta todas las VLANs hacia el switch central, además de conectar el Access Point para el Wi-Fi de invitados.

**Teléfonos IP como paso intermedio** — los Cisco 7960 tienen un puerto de datos integrado. En Recepción, la PC está conectada detrás del teléfono. El teléfono etiqueta el tráfico de voz a VLAN 100 y deja pasar el tráfico de datos a VLAN 40.

**DHCP en el router** — cada subinterfaz tiene su propio pool DHCP. Sin servidor dedicado, sin configuración manual en los equipos.

---

## Configuraciones principales

### Router — Subinterfaces

```
interface FastEthernet0/0
 no shutdown

interface FastEthernet0/0.10
 encapsulation dot1Q 10
 ip address 192.168.10.1 255.255.255.240

interface FastEthernet0/0.20
 encapsulation dot1Q 20
 ip address 192.168.20.1 255.255.255.240

interface FastEthernet0/0.30
 encapsulation dot1Q 30
 ip address 192.168.30.1 255.255.255.240

interface FastEthernet0/0.40
 encapsulation dot1Q 40
 ip address 192.168.40.1 255.255.255.240

interface FastEthernet0/0.50
 encapsulation dot1Q 50
 ip address 192.168.50.1 255.255.255.0

interface FastEthernet0/0.100
 encapsulation dot1Q 100
 ip address 192.168.100.1 255.255.255.240
```

### Router — DHCP

```
ip dhcp excluded-address 192.168.10.1 192.168.10.2
ip dhcp excluded-address 192.168.20.1
ip dhcp excluded-address 192.168.30.1 192.168.30.2
ip dhcp excluded-address 192.168.40.1
ip dhcp excluded-address 192.168.50.1
ip dhcp excluded-address 192.168.100.1

ip dhcp pool VLAN10
 network 192.168.10.0 255.255.255.240
 default-router 192.168.10.1

ip dhcp pool VLAN20
 network 192.168.20.0 255.255.255.240
 default-router 192.168.20.1

ip dhcp pool VLAN30
 network 192.168.30.0 255.255.255.240
 default-router 192.168.30.1

ip dhcp pool VLAN40
 network 192.168.40.0 255.255.255.240
 default-router 192.168.40.1

ip dhcp pool VLAN50
 network 192.168.50.0 255.255.255.0
 default-router 192.168.50.1

ip dhcp pool VLAN100
 network 192.168.100.0 255.255.255.240
 default-router 192.168.100.1
```

### Switch central — VLANs y trunk

```
vlan 10
 name Administracion
vlan 20
 name Ventas
vlan 30
 name Tecnologia
vlan 40
 name Recepcion
vlan 50
 name Invitados
vlan 100
 name VozIP

interface GigabitEthernet0/1
 switchport mode trunk
 switchport trunk allowed vlan all
```

### Switch central — Puertos de acceso

```
interface range FastEthernet0/1 - 7
 switchport mode access
 switchport access vlan 10

interface range FastEthernet0/8 - 15
 switchport mode access
 switchport access vlan 20

! Puerto con teléfono IP (voz + datos)
interface range FastEthernet0/20 - 21
 switchport mode access
 switchport access vlan 40
 switchport voice vlan 100

```

### ACLs — Control de acceso

**Invitados solo pueden salir a internet:**

```
ip access-list extended INVITADOS_SOLO_INTERNET
 deny ip 192.168.50.0 0.0.0.255 192.168.10.0 0.0.0.15
 deny ip 192.168.50.0 0.0.0.255 192.168.20.0 0.0.0.15
 deny ip 192.168.50.0 0.0.0.255 192.168.30.0 0.0.0.15
 deny ip 192.168.50.0 0.0.0.255 192.168.40.0 0.0.0.15
 deny ip 192.168.50.0 0.0.0.255 192.168.100.0 0.0.0.15
 permit ip any any

interface FastEthernet0/0.50
 ip access-group INVITADOS_SOLO_INTERNET in
```

**Ventas no puede acceder a Administración:**

```
ip access-list extended VENTAS_NO_ADMINISTRACION
 deny ip 192.168.20.0 0.0.0.15 192.168.10.0 0.0.0.15
 permit ip any any

interface FastEthernet0/0.20
 ip access-group VENTAS_RESTRICT in
```

---

## Resumen de políticas de seguridad

| Regla | Origen | Destino | Resultado |
|-------|--------|---------|-----------|
| Invitados aislados | VLAN 50 | VLANs internas | Bloqueado |
| Invitados a internet | VLAN 50 | Internet | Permitido |
| Ventas sin acceso a Admin | VLAN 20 | VLAN 10 | Bloqueado |
| Tecnología accede a todo | VLAN 30 | Todas | Permitido |

---

## Lo que practiqué con este lab

- Creación y nomenclatura de VLANs en switches capa 2
- Configuración de trunk 802.1Q entre switch y router
- Router-on-a-Stick con subinterfaces por VLAN
- DHCP centralizado en el router con pools por VLAN
- VLAN de voz con `switchport voice vlan`
- ACLs extendidas nombradas aplicadas por subinterfaz
- Red inalámbrica para invitados con aislamiento total

---

## Archivos

| Archivo | Descripción |
|---------|-------------|
| `Topologia.png` | Diagrama de la topología |
| `Lab01.pkt` | Archivo de simulación en Cisco Packet Tracer |

---

*Parte del repositorio [laboratorios-de-ingenier-a-de-redes](https://github.com/ItzZihk0/laboratorios-de-ingenier-a-de-redes) — laboratorios de redes.*