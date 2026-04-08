# 🖧 Laboratorios de Ingeniería de Redes

Repositorio de laboratorios prácticos enfocados en la simulación de **escenarios reales de infraestructura de red** usando Cisco Packet Tracer.

Cada laboratorio aborda problemas comunes en entornos empresariales como segmentación, enrutamiento, control de acceso y servicios de red, incluyendo diseño, implementación y validación.

![Herramienta](https://img.shields.io/badge/Herramienta-Cisco%20Packet%20Tracer-1ba0d7?style=flat-square)
![Nivel](https://img.shields.io/badge/Nivel-Intermedio%20→%20Avanzado-orange?style=flat-square)
![Estado](https://img.shields.io/badge/Estado-En%20progreso-blue?style=flat-square)
![Licencia](https://img.shields.io/badge/Licencia-MIT-lightgrey?style=flat-square)
---

## Enfoque

Los laboratorios están diseñados con un enfoque práctico orientado a:

- Resolución de problemas reales de red
- Diseño de topologías funcionales y escalables
- Implementación de buenas prácticas
- Validación y troubleshooting de configuraciones

---


## Índice de laboratorios

| # | Lab | Temas | Estado |
|---|-----|-------|--------|
| 01 | [Red de Oficina - VLANs, RoaS, DHCP y VoIP](https://github.com/ItzZihk0/laboratorios-de-ingenier-a-de-redes/tree/main/lab-01-vlans-roas) | VLANs · 802.1Q · RoaS · DHCP · ACL · VoIP | Completado |

---

## Vista previa

### Lab 01 - Red de Oficina
![Topología de red](https://i.ibb.co/84NjV4sY/Topologia.png)

**Contexto:**  
Segmentación de red empresarial con VLANs, control de acceso mediante ACL y soporte de telefonía IP.

## Tecnologías y conceptos trabajados

- Segmentación de red con VLANs (capa 2)
- Trunk 802.1Q entre switches y router
- Inter-VLAN routing con Router-on-a-Stick
- DHCP centralizado por VLAN
- VLAN de voz (`switchport voice vlan`)
- ACLs extendidas nombradas
- Red inalámbrica con aislamiento de invitados

---

## Estructura del repositorio

```
laboratorios-de-ingenieria-de-redes/
│
├── lab-01-vlans-roas/
│   ├── Lab01.pkt          # Simulación en Packet Tracer
│   ├── Topologia.png      # Diagrama de red
│   └── README.md          # Documentación del lab
│
└── README.md              # Este archivo
```

---

## Cómo usar los labs

1. Tener instalado [Cisco Packet Tracer](https://www.netacad.com/resources/lab-downloads?courseLang=en-US) (versión 9.0.0 recomendada)
2. Clonar el repositorio:
   ```bash
   git clone https://github.com/ItzZihk0/laboratorios-de-ingenier-a-de-redes.git
   ```
3. Abrir el archivo `.pkt` del lab que quieras ver
4. Leer el `README.md` del lab para entender la topología y las configuraciones

---

## Objetivo

Desarrollar habilidades prácticas en diseño, segmentación y troubleshooting de redes mediante simulaciones progresivas en Cisco Packet Tracer.

---

## Próximos laboratorios

- Alta disponibilidad (HSRP / failover)
- Enrutamiento dinámico (OSPF / EIGRP)
- VPN site-to-site
- Implementación de firewall y políticas

---
*Parte del portafolio técnico de ingeniería de redes ItzZihk0*
