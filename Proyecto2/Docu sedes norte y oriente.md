# Documentación Técnica: Sedes Norte y Oriente (Grupo 9)

**Proyecto 2**

---

## 1. Resumen de Direccionamiento y VLANs Implementadas

En este documento se detalla la segmentación de red, esquemas de subredes calculadas por VLSM y direccionamiento asignado a las áreas Norte y Oriente:

| Sede        | VLAN | Nombre     | Subred Asignada  | Máscara de Red    | Gateway (Default IP)     | Rango Hosts Útiles |
| :---------- | :--- | :--------- | :--------------- | :---------------- | :----------------------- | :----------------- |
| **Norte**   | 59   | Analisis   | `192.168.5.0/26` | `255.255.255.192` | `192.168.5.1`            | `.2` - `.62`       |
| **Norte**   | 69   | Auditoria  | `192.168.6.0/27` | `255.255.255.224` | `192.168.6.1`            | `.2` - `.30`       |
| **Norte**   | 79   | Legal      | `192.168.7.0/27` | `255.255.255.224` | `192.168.7.1`            | `.2` - `.30`       |
| **Oriente** | 89   | Boveda     | `192.168.8.0/26` | `255.255.255.192` | `192.168.8.1` (VIP HSRP) | `.10` - `.62`      |
| **Oriente** | 99   | Plataforma | `192.168.9.0/26` | `255.255.255.192` | `192.168.9.1` (VIP HSRP) | `.10` - `.62`      |

### Enlaces Punto a Punto de Frontera e Interconexión (WAN Core)

- **Núcleo - Sede Norte (`Router2` Gig0/0/2 - `R-Norte` Gig0/0/2):** `192.168.13.12/30`
  - `Router2`: `192.168.13.13/30`
  - `R-Norte` (Router6): `192.168.13.14/30`
- **Núcleo - Sede Oriente Enlace Principal (`Router0` Gig0/0/0 - `MS0-Oriente` Gig0/1):** `192.168.13.16/30`
  - `Router0`: `192.168.13.17/30`
  - `MS0-Oriente`: `192.168.13.18/30`
- **Núcleo - Sede Oriente Enlace Redundante (`Router0` Gig0/0/2 - `MS1-Oriente` Gig0/2):** `192.168.13.20/30`
  - `Router0`: `192.168.13.21/30`
  - `MS1-Oriente`: `192.168.13.22/30`
- **Enlace Serial Interno del Núcleo (`Router0` Se0/1/0 - `Router2` Se0/1/0):** `192.168.13.24/30`
  - `Router0`: `192.168.13.25/30`
  - `Router2`: `192.168.13.26/30`

---

## 2. Configuración Sede Norte (Centro de Autorización de Créditos)

### Capa 2: Malla de Conmutación y Spanning Tree

- **Protocolo STP:** Usé **Rapid PVST+** (`spanning-tree mode rapid-pvst`) en todos los switches para garantizar convergencia rápida ante caídas físicas en los enlaces de mi topologia híbrida anillo/diamante.
- **Root Bridge:** **Switch11** como el puente raíz principal para todas las VLANs mediante el comando `spanning-tree vlan 59,69,79 root primary`.
- **Troncales:** Se configuraron enlaces troncales IEEE en las interfaces inter-switch (`Fa0/1 - 4`) y en el uplink hacia el router (`Gig0/1`).
- **Acceso y PortFast:** Los puertos conectados a estaciones de trabajo finales (PCs) se dejaron en modo acceso y con `spanning-tree portfast` habilitado para optimizar tiempos de negociación:
  - `Switch12`: Fa0/22 (VLAN 59 - PC4), Fa0/23 (VLAN 69 - PC3), Fa0/24 (VLAN 79 - PC5).
  - `Switch13`: Fa0/24 (VLAN 79 - PC8).
  - `Switch14`: Fa0/1 (VLAN 59 - PC6), Fa0/2 (VLAN 69 - PC7).

### Capa 3: Router-on-a-Stick y EIGRP

- **Enrutamiento Inter-VLAN:** En **R-Norte (Router6)** se levantaron subinterfaces sobre la interfaz física `GigabitEthernet0/0/1`:
  - `GigabitEthernet0/0/1.59` -> IP `192.168.5.1/26`
  - `GigabitEthernet0/0/1.69` -> IP `192.168.6.1/27`
  - `GigabitEthernet0/0/1.79` -> IP `192.168.7.1/27`
- **Protocolo de Enrutamiento Dinámico:** Se configuró **EIGRP**:
  - Publica las tres redes LAN de usuario y el enlace WAN hacia el núcleo (`192.168.13.12/30`).
  - Se configuraron como `passive-interface` las tres subinterfaces LAN para suprimir tráfico de difusión innecesario hacia los usuarios.

---

## 3. Configuración Sede Oriente (Centro Financiero Legado)

### Capa 2 y Capa 3: Distribución Multicapa

- **Equipos de Núcleo/Distribución:** Dos switches multicapa (`MS0-Oriente` y `MS1-Oriente`).
- **Ruteo Local:** Ambos switches tienen activado `ip routing` y ejecutan el enrutamiento inter-VLAN directamente a través de sus SVIs (Switch Virtual Interfaces).
- **Puertos de Conexión WAN:** Se convirtieron los puertos `Gig0/1` (en MS0) y `Gig0/2` (en MS1) en interfaces enrutadas de Capa 3 mediante el comando `no switchport`.

### Alta Disponibilidad de Primer Salto (HSRP)

Para garantizar la continuidad operativa ante la caída física de uno de los switches de distribución, se implementó **HSRP** (Hot Standby Router Protocol) en las SVIs:

- **VLAN 89:**
  - IP Virtual (VIP): `192.168.8.1`
  - Switch Activo (`MS0-Oriente`): IP Real `192.168.8.2`, Prioridad `110`, Preempt activado.
  - Switch Standby (`MS1-Oriente`): IP Real `192.168.8.3`, Prioridad `100`, Preempt activado.
- **VLAN 99:**
  - IP Virtual (VIP): `192.168.9.1`
  - Switch Activo (`MS0-Oriente`): IP Real `192.168.9.2`, Prioridad `110`, Preempt activado.
  - Switch Standby (`MS1-Oriente`): IP Real `192.168.9.3`, Prioridad `100`, Preempt activado.

### Protocolo de Enrutamiento: RIPv2

- En ambos switches multicapa se habilitó `router rip` en su versión 2 con `no auto-summary`.
- Se declararon las redes de usuarios (`192.168.8.0`, `192.168.9.0`) y las redes de interconexión con el núcleo (`192.168.13.0`).
- Las SVIs de usuario quedaron marcadas como `passive-interface`.

---

## 4. Integración y Redistribución en el Núcleo

```
[Sede Norte] --(EIGRP 10)-- [Router2] ==(OSPF 1 Area 0)== [Router0] --(RIPv2)-- [Sede Oriente]
```

1. **En `Router2` (Frontera EIGRP - OSPF):**
   - Se redistribuye EIGRP hacia el núcleo: `redistribute eigrp 10 subnets` bajo el proceso `router ospf 1`.
   - Se inyectan las rutas de OSPF hacia Norte: `redistribute ospf 1 metric 100000 100 255 1 1500` bajo `router eigrp 10`.
2. **En `Router0` (Frontera RIPv2 - OSPF):**
   - Se redistribuye RIP hacia el núcleo: `redistribute rip subnets` bajo el proceso `router ospf 1`.
   - Se inyectan las rutas de OSPF hacia Oriente: `redistribute ospf 1 metric 2` bajo `router rip`.
3. **Enlace Backbone `Router0` - `Router2`:**
   - Se configuró el enlace serial `Serial0/1/0` (`192.168.13.24/30`) con encapsulación OSPF en el Área 0, permitiendo que la información de enrutamiento viaje entre el norte y el sur del núcleo.

---

## 5. Pruebas de Validación y Diagnóstico

Comandos de verificación ejecutados y validados durante la puesta en marcha:

- **En Switches de Capa 2 (Sede Norte):**
  - `show spanning-tree vlan 59`: Valida que Switch11 reporte `This bridge is the root`.
  - `show interfaces trunk`: Valida que los troncales inter-switch transporten activamente las VLANs 59, 69 y 79.
- **En Switches Multicapa (Sede Oriente):**
  - `show standby brief`: Confirma que MS0 esté en estado `Active` y MS1 en `Standby` con sus correspondientes IPs virtuales (`192.168.8.1` y `192.168.9.1`).
  - `show ip route`: Comprueba la tabla de ruteo y el aprendizaje de subredes externas.
- **En Routers de Frontera (`R-Norte` y Núcleo):**
  - `show ip route eigrp`: En Router2, confirma las rutas aprendidas desde el router de la Sede Norte.
  - `show ip route rip`: En Router0, confirma las rutas aprendidas desde los switches multicapa.
- **Prueba de Extremo a Extremo (Ping ICMP):**
  - Se validó conectividad exitosa (con respuesta de 4 paquetes) realizando ping desde **PC4 (Sede Norte - VLAN 59: `192.168.5.10`)** hacia **PC16 (Sede Oriente - VLAN 89: `192.168.8.10`)**.
