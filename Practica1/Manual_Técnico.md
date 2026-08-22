<div align="center">

# Manual Técnico Proyecto 1
## QuetzalDev S.A.
### Jorge Ivan Samayoa SIan
### 202307506
### Universidad de San Carlos de Guatemala
### Ingenieria en Sistemas
### Laboratorio de Redes de Computadoras 1

</div>

<br>

---

<br>

# Contenido

1. ### Introducción
2. ### Inventario de equipos
3. ### Justificación del cuarto MDF
4. ### Justificación de la topología para cada departamento
5. ### Tabla de tipo y categoría de cable
6. ### Tabla de distancias estimadas
7. ### Justificación de cada elemento de equipo activo
8. ### Justificación de del medio de transmisión
9. ### Justificación del tipo de canalización
10. ### Justificación del tipo de rack o gabinete
11. ### Estimación de consumo eléctrico
12. ### Tabla straight-through/crossover
13. ### Disposicion de pines
14. ### Etiquetado de cables
15. ### Comparación entre etiquetado usado y estándar TIA/EIA-606
16. ### Descripción del flujo de conexión end-to-end
17. ### Presupuesto estimado del equipo y materiales.

<br>

## 1.Introducción

El presente manual técnico detalla el diseño de la infraestructura de red física (Capa 1 del modelo OSI) para el nuevo edificio corporativo de QuetzalDev S.A.. El Documento establece las topologías físicas, medios de transmisión, esquemas de cableado y normativas seleccionadas para garantizar escalabilidad y orden en las instalaciones. 

## 2. Inventario de equipos:

| Equipo | Cantidad | 
| ------ | -------- | 
| Switches | 8 unidades, uno para cada área | 
| Patch Panel | 1 unidad de 48 puertos |   
| Dispositivos Finales | 30 computadoras de escritorio, 12 laptops y 6 servidores | 
| Racks/Gabinetes | 1 Rack de piso para el MDF y 7 gabinetes de pared pequeños para los switches departamentales | 
| UPS | 1 unidad de respaldo de energía centralizada para el Cuarto de Telecomunicaciones | 
| Tomas de Red | 48 tomas de red distribuidas entre unitarias y dobles según la concentración de equipos en cada oficina. | 

## 3. Justificación del cuarto MDF

El Cuarto de Telecomunicaciones **MDF** se ubicó en el Data Center. Esta ubicación se justifica ya que centraliza el cableado troncal hacia los pasillos principales, minimiza la distancia hacia los departamentos con mayor carga y proporciona el espacio físico suficiente para alojar los 3 servidores principales. 

## 4. Justificación de la topología para cada departamento

La topología usada para cada departamento es la **Topología de estrella**. Esta desición asegura que cada host tenga un enlace dedicado a su switch local, lo cual proporciona tolerancia a posibles fallos; si un cable se daña solo se pierde la comunicación con el dispositivo conectado con ese cable.

Otra de las razones por las cuales se eligió este tipo de topología, es que a nivel global, el edificio termina usando una **Topología de estrella extendida**, conectando el switch de cada departamento al switch principal en el **MDF**

## 5. Tabla de tipo y categoría de cable

| Segmento | Tipo de Medio | Categoria | Justificacion |
| -------- | ------------- | --------- | ------------- | 
| **Cableado Troncal** | Cobre (UTP) | Cat 6A | Soporta velocidades de hasta 10 Gbps para los enlaces uplinks de los switchers, siendo ideal para distancias cortas como 22 metros.
|  **Cableado Horizontal** | Cobre (UTP) | Cat 6 | Ofrece un ancho de banda suficiente para las tareas diarias de las computadoras y laptops, manteniendo un balance óptimo entre costo y rendimiento. 

## 6. Tabla de distancias estimadas

| Departamento | Dispositivos | Distancia Promedio (m) | Total de Cable Estimado (m) |
| :--- | :--- | :--- | :--- |
| Recepción | 4 | 15 | 60 |
| Recursos Humanos | 8 | 14 | 112 |
| Legal | 4 | 14 | 56 |
| Capacitación | 10 | 12 | 120 |
| Diseño e Innovación | 8 | 15 | 120 |
| Dirección General | 4 | 12 | 48 |
| Backend | 7 | 10 | 70 |
| Data Center | 3 | 6 | 18 |
| **TOTAL** | **48 hosts** | | **~604 metros + 20% holgura = 725 metros** |

**Cálculo de Bobinas:**
* Requerimiento total estimado: 725 metros.
* Longitud estándar de bobina UTP: 305 metros.
* 885 m / 305 m = 2.37 bobinas.
* **Cantidad requerida:** Se estipula la compra de **3 bobinas completas** para tener margen de error y mantenimiento futuro.

## 7. Justificación de cada elemento de equipo activo
* **Switch Principal (MDF):** Dimensionado a 48 puertos para soportar los enlaces troncales de todos los departamentos y conexiones directas del Data Center
* **Patch Panel:** Dimensionado con la misma capacidad del switch principal para determinar estructuradamente todos los enlaces troncoales y los host locales del MDF.
* **Switches Departamentales:** Actuán como equipos de acceso para distribuirla red localmente en cada oficina, reduciendo la cantidad de cables que deben viajar hasta el MDF.

## 8. Justificación del medio de transmisión troncal

Se eligió cable UTP Cat 6A en lugar de fibra óptica debido a que el edificio no solo es de un único nivel, sino que las dimensiones son muy pequeñas (22m x 22m). El cobre en estas distancias garantiza el ancho de banda requerido para los enlaecs ascendentes a un costo significativamente menor que implementar transceptores SFP y fibra. 

## 9. Justificación del tipo de canalización

Se utilizará escalerilla metálica abierta suspendida sobre el cielofalso a lo largo del **Hall Central** y el **Pasillo Secundario**. Esto para facilitar el enrutamiento ordenados del cable troncal, además de que permite una óptima disipación de calor y hace que la adición futura de cables sea mucho más accesible y escalable. 

## 10. Justificación del tipo de rack o gabinete

* **Para el MDF (Data Center)** sse propone un Rack de piso abierto de 42U, ya que debe alojar los 3 servidores principales de la empresa, el UPS central, el switch principal y el patch panel, requiriendo estabilidad y buena ventilación.
* **Para los departamentos**, se utilizarán gabinetes de pared (wall-mount) de 6U a 9U, los cuales son suficientes para resguardar el switch local de acceso y organizar el cableado horizontal, ahorrando espacio en el piso de las oficinas.
* **Para las áreas de Recepción, Diseño e Innovación, y Backend**, que cuentan con un servidor local cada una, se estipula el uso de servidores en formato Torre (Tower). Estos equipos no se montarán en los gabinetes de telecomunicaciones, sino que se ubicarán físicamente en el área de trabajo conectándose a una toma de red estándar en la pared, tal como las estaciones de trabajo de los usuarios.

## 11. Estimación de consumo eléctrico

* **Estimando un consumo promedio** de 50W por switch de departamento (8 switches = 400W) y 100W para el switch principal, además del consmo de los 3 servidores principales y los 3 servidores ubicados en las áreas de Recepión, Diseño y Backend, con un promedio de 400W cada uno, se estima que el total de consumo eléctrico es de aproximadamente 2900W - 3000W
* **Capacidad de UPS requerida** para soportar una carga real de 3000W y mantener un margen de seguridad del 25% para picos de arranque y crecimiento futuro, se estima la necesidad de un UPS con capacidad de 5000 VA (**5 kVA**). Este equipo se ubicará centralizado en el MDF para respaldar las insfraestructura principal y los servidores del Data Center. 

## 12. Tabla straight-through / crossover

| Enlace | Tipo de Cable | Estándar en Extremos | Justificación técnica |
| --- | --- | --- | --- |
| PC/Servidor a Switch (Horizontal) | Straight-Through | T568B - T568B | Se utiliza cable directo porque interconecta dispositivos de diferentes capas del Modelo OSI (Host a Switch) |
| Switch a Switch (Troncal)  | Crossover | T568A - T568B | Se utiliza cable cruzado porque interconecta dispositivos de la misma capa, por lo que es necesario cruzar físicamente los pines de Tx y Rx (Capa 1) |

## 13. Disposiciones de pines

* **T568B (Usado en ambos extremos para Straight-Through):**
    1. Blanco/Naranja
    2. Naranja
    3. Blanco/Verde
    4. Azul
    5. Blanco/Azul
    6. Verde
    7. Blanco/Café
    8. Café

* **T568A (Usado en el extremo 2 para Crossover):**
    1. Blanco/Verde
    2. Verde
    3. Blanco/Naranja
    4. Azul
    5. Blanco/Azul
    6. Naranja
    7. Blanco/Café
    8. Café

El cableado horizontal se ponchará bajo el estándar T568B tanto en la toma de red como en el patch panel correspondiente.

## 14. Etiquetado de cables

* **Cableado Troncal:** `MDF-[Área/Departamento]` (Ejemplo: `MDF-Recepción`)
* **Cableado Horizontal:** `[Área/Departamento]-[Número de Punto de Red]` (Ejemplo: `Recepcion-PR01`)

## 15. Comparación entre etiquetado usado y estándar TIA/EIA-606

| Aspecto | Práctica Actual | Estándar TIA/EIA-606 Completo |
| :--- | :--- | :--- |
| **Identificador** | El diseño usado simplifica los nombres o el etiquetado por departamento. | El estándar exige un formato alfanumérico estricto que detalla piso, cuarto, rack, panel y puerto (Ej. `1A-A01-05`). |
| **Documentación de espacios** | El diseño usado solo documenta los cables terminales | El estándar exige documentar de manera formal los espacios, rutas de canalización y uniones a tierra. |

**Justificación de su uso en entorno real:**
En un entorno real (Data Center) se optaría por el estándar completo para asegurar que técnicos de terceros puedan rastrear de forma unívoca un puerto físico desde el escritorio hasta el servidor, reduciendo tiempos de caída

## 16. Descripción de flujo de conexión end-to-end

1. El dispositivo del usuario se conecta a la toma de red en la pared mediante un patch cord directo.  
2. La señal viaja a través del cableado horizontal hacia el switch del departamento.  
3. El switch departamental dirige la señal a través del cableado troncal (cruzado) que viaja por la escalerilla metálica.  
4. La señal aterriza en la parte posterior del patch panel del MDF.  
5. Un patch cord corto conecta el puerto frontal del patch panel hacia el switch principal del edificio.  
6. Finalmente, el switch principal transfiere los datos hacia el servidor destino en el Data Center.  

## 17. Presupuesto estimado del equipo y materiales.

| Ítem | Cantidad | Precio Unitario Estimado (Q) | Total Estimado (Q) |
| :--- | :--- | :--- | :--- |
| Nexxt Bobina de Cable Cat 6 UTP para Interiores - 305m (Cableado horizontal y troncal) | 3 | Q1,950.00 | Q5,850.00 |
| Cisco Catalyst 1200-48P-4G Smart Switch, 48 Port GE, PoE (Core MDF) | 1 | Q11,714.00 | Q11,714.00 |
| Switch TP-Link 16 Puertos Gigabit Administrable PoE (Switches Departamentales) | 8 | Q1,250.00 | Q10,000.00 |
| Patch Panel Nexxt 48 Puertos Cat 6 para montaje en Rack | 1 | Q1,100.00 | Q1,100.00 |
| EPA171 Gabinete de Piso 600X800X42U (Para el MDF y Servidores) | 1 | Q12,300.00 |Q12,300.00 |
| Gabinete De Pared, 9U 19 Pulgadas, Color Negro Epoxy (Para los Departamentos) | 7 | Q1,589.00 | Q11,123.00 |
| UPS APC Smart RT 5000VA 208V 4 Salidas (Respaldo centralizado MDF) | 1 | Q17,745.00 | Q17,745.00 |
| Nodos de Red (Placas, Jacks Cat 6 Keystone, Patch Cords de 3ft y 7ft) | 48 (Lotes) | Q10.00 | Q5,280.00 |
| Canalización: Tramos de Escalerilla Metálica Abierta y Accesorios de Fijación | 1 (Lote) | Q3,500.00 | Q3,500.00 |
| **Presupuesto Total** | | | **Q78,612.00** |

---
## Links de Referencia

Nexxt Bobina de Cable Cat 6 UTP Interiores: https://www.kemik.gt/nexxt-bobina-de-cable-cat6-utp-interior-305m

Cisco Catalyst 1200-48P-4G Smart Switch: https://www.pacifiko.com/compras-en-linea/cisco-catalyst-1200-48p-4g-smart-switch-48-port-ge-poe-4x1ge-sfp-limited-lifetime-protection-c1200-48p-4g-nombre-de-estilo-cisco-catalyst-1200-switch-48-port-ge-poe-375w-4-x-ge-uplinks&pid=MTY3YzQ3Nj

Switch TP-Link 16 Puertos Gigabit: https://www.intelaf.com/precios_stock_detallado/sw-16-tplink-gigabit

Patch Panel Nexxt 48 Puertos: https://oxdea.gt/product/patch-panel-nexxt-48-puertos-cat6/

EPA171 Gabinete de Piso 42U: https://camarasdeseguridad.com.gt/epa171-gabinete-de-piso-600x800x42u.html

Gabinete De Pared 9U: https://www.kemik.gt/gabinete-de-pared-9u-19-pulgadas-color-negro

UPS APC Smart RT 5000VA: https://www.pacifiko.com/compras-en-linea/ups-apc-smart-rt-5000va-208v-4-salidas&pid=NGU1YjZmNz

Accesorios de Red (Placas, Jacks, Patch Cords):

* https://oxdea.gt/product/patch-cord-cat6-de-30-cm-nexxt-pcgpcc6cm01gr/

* https://www.kemik.gt/jack-rj45-de-8-contactos-a-90-cat-6-tipo-keystone-blanco

* https://oxdea.gt/product/placa-de-montaje-de-6-puertos-color-blanca-sprywire-sw-fp06a/