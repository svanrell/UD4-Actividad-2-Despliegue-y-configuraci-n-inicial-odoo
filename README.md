## 1. Despliegue y Configuración Inicial

### 1.1 Arranque del entorno con Docker Compose

Para este despliegue he utilizado como punto de partida la **plantilla base de Odoo 19 + PostgreSQL 18** facilitada por el profesor en Classroom.

Siguiendo las instrucciones de la plantilla, primero he preparado los archivos y carpetas locales necesarios para el entorno:

1. **Crear el archivo secreto con la contraseña de PostgreSQL:**
   ```bash
   mkdir -p secrets
   echo "odoo" > secrets/odoo-db-password.txt
   ```
2. **Crear el archivo `compose.override.yml` para desarrollo local** (con recarga en caliente `--dev=all` y los volúmenes en local):
   ```yaml
   services:
     web:
       volumes:
         - ./filestore:/var/lib/odoo/filestore
         - ./sessions:/var/lib/odoo/sessions
       command: --dev=all --workers=0
   ```
3. **Crear la configuración local de Odoo a partir de la plantilla:**
   ```bash
   cp config/odoo.conf.template config/odoo.conf
   ```
4. **Crear las carpetas locales para addons y datos:**
   ```bash
   mkdir -p addons filestore sessions
   ```
5. **Arrancar los contenedores en segundo plano:**
   ```bash
   docker compose up -d
   ```

Para verificar que ambos servicios han arrancado correctamente y están comunicados en la red interna, he ejecutado el comando `docker compose ps`:

```bash
shumi747@MacBook-Air-de-SERGI-6 Entrega Odoo % docker compose ps
NAME                IMAGE         COMMAND                  SERVICE   CREATED          STATUS                    PORTS
entregaodoo-db-1    postgres:18   "docker-entrypoint.s…"   db        49 minutes ago   Up 49 minutes (healthy)   5432/tcp
entregaodoo-web-1   odoo:19       "/entrypoint.sh --de…"   web       43 minutes ago   Up 43 minutes             0.0.0.0:8069->8069/tcp, [::]:8069->8069/tcp, 0.0.0.0:8072->8072/tcp, [::]:8072->8072/tcp
```

---

### 1.2 Creación de la base de datos `caferapido`

1. He entrado en el navegador a `http://localhost:8069/web/database/manager`.
2. He creado la base de datos con el nombre **`caferapido`**, usuario `admin`, idioma español y país España (sin marcar datos de prueba para empezar de cero).
3. He iniciado sesión con la cuenta de administrador.

![Figura 1.1: Creación de la base de datos](Entregas/capturas/01_db_manager.png)  
*Figura 1.1: Creación de la base de datos caferapido.*

![Figura 1.2: Pantalla de Login](Entregas/capturas/02_login.png)  
*Figura 1.2: Inicio de sesión con la base de datos caferapido.*

![Figura 1.3: Pantalla de inicio](Entregas/capturas/03_inicio.png)  
*Figura 1.3: Pantalla principal de Odoo 19.*

---

### 1.3 Configuración de la empresa: Café Rápido S.L.

Desde *Ajustes > Empresas*, he configurado los datos de la empresa:
* **Nombre:** Café Rápido S.L.
* **Logotipo:** Imagen de una taza y café en grano.
* **Moneda:** EUR (€).
* **País:** España.
* **Localización fiscal:** He seleccionado el paquete contable **España - PYMES (2008)**, dejando configurado el **IVA al 21%** por defecto.

![Figura 1.4: Configuración de la empresa](Entregas/capturas/04_empresa.png)  
*Figura 1.4: Datos de la empresa Café Rápido S.L. con logotipo y moneda.*

![Figura 1.5: Plan contable PYMES 2008](Entregas/capturas/05_ajustes_contabilidad.png)  
*Figura 1.5: Ajustes contables con localización fiscal española e IVA 21%.*

---

### 1.4 Usuario administrativo secundario: Gerente

En *Ajustes > Usuarios y Compañías > Usuarios*, he creado un segundo usuario administrador:
* **Nombre:** Gerente
* **Login / Correo:** `gerente`
* **Permisos:** Administrador en Ventas, Compras, Facturación, Empleados y TPV.

![Figura 1.6: Listado de usuarios](Entregas/capturas/06_usuarios.png)  
*Figura 1.6: Lista de usuarios activos con el usuario Gerente.*

![Figura 1.7: Permisos del usuario Gerente](Entregas/capturas/07_usuario_gerente.png)  
*Figura 1.7: Ficha del usuario Gerente y permisos asignados.*

---

## 2. Gestión de Facturación

### 2.1 Instalación del módulo y alta de clientes/proveedores

He instalado el módulo **Facturación** (`account`) desde el menú de aplicaciones:

![Figura 2.0: Instalación de Facturación](Entregas/capturas/10_apps.png)  
*Figura 2.0: Módulo Facturación instalado.*

A continuación, he registrado los cuatro contactos solicitados:
* **Clientes:**
  * **Marta López** (particular, clienta habitual del local).
  * **Repartos Palma S.L.** (empresa de reparto que nos encarga desayunos).
* **Proveedores:**
  * **Tostadores Balear S.A.** (proveedor del café en grano).
  * **Panadería San Jorge** (proveedor de croissants y bollería).

![Figura 2.1: Clientes creados](Entregas/capturas/11_clientes.png)  
*Figura 2.1: Clientes: Marta López y Repartos Palma S.L.*

![Figura 2.2: Proveedores creados](Entregas/capturas/12_proveedores.png)  
*Figura 2.2: Proveedores: Tostadores Balear S.A. y Panadería San Jorge.*

---

### 2.2 Factura emitida a Repartos Palma S.L. (Pendiente de pago)

En *Facturación > Clientes > Facturas*:
1. He creado una nueva factura para el cliente **Repartos Palma S.L.** por servicio de desayunos.
2. He confirmado la factura, generándose el número `INV/2026/00001`.
3. La he dejado **sin registrar pago**, quedando en estado **Publicado / No pagado**.

![Figura 2.3: Factura emitida a Repartos Palma](Entregas/capturas/14_factura_repartos.png)  
*Figura 2.3: Factura INV/2026/00001 emitida y pendiente de pago.*

![Figura 2.4: Listado de facturas emitidas](Entregas/capturas/13_facturas_emitidas.png)  
*Figura 2.4: Listado de facturas de clientes con el estado de pago.*

---

### 2.3 Factura recibida de Panadería San Jorge (Pagada)

En *Facturación > Proveedores > Facturas*:
1. He registrado la factura del proveedor **Panadería San Jorge** con el número `FACTU/2026/11/0001` por compra de bollería.
2. He confirmado la factura y he pulsado en **Registrar Pago**.
3. He seleccionado el diario de **Banco** y validado el pago. La factura ha pasado a estado **Pagado**.

![Figura 2.5: Factura de proveedor pagada](Entregas/capturas/16_factura_panaderia.png)  
*Figura 2.5: Factura de Panadería San Jorge marcada como Pagado.*

![Figura 2.6: Listado de facturas recibidas](Entregas/capturas/15_facturas_recibidas.png)  
*Figura 2.6: Listado de facturas de proveedores.*

---

### 2.4 Informes de facturación

En *Facturación > Informes > Facturas* he revisado los informes generados por Odoo tanto en tabla pivote dinámica como en gráfico de barras:

![Figura 2.7: Informe en tabla pivote](Entregas/capturas/17_informe_facturas.png)  
*Figura 2.7: Tabla pivote del análisis de facturas por cliente y período.*

![Figura 2.8: Informe gráfico](Entregas/capturas/18_informe_grafico.png)  
*Figura 2.8: Vista gráfica comparativa de facturación.*

---

## 3. Gestión de Empleados

### 3.1 Creación de departamentos

He instalado el módulo **Empleados** (`hr`) y en *Empleados > Configuración > Departamentos* he creado los tres departamentos de la empresa:
1. **Cocina**
2. **Reparto**
3. **Administración**

![Figura 3.1: Departamentos creados](Entregas/capturas/20_departamentos.png)  
*Figura 3.1: Departamentos: Cocina, Reparto y Administración.*

---

### 3.2 Alta de empleados

En *Empleados > Empleados* he dado de alta a los tres trabajadores con su correo, puesto y departamento:

* **Laura Gómez:** Jefa de Cocina · Departamento de Cocina (`laura.gomez@caferapido.es`).
* **David Pérez:** Repartidor · Departamento de Reparto (`david.perez@caferapido.es`).
* **Carmen Ruiz:** Responsable de Administración · Departamento de Administración (`carmen.ruiz@caferapido.es`).

![Figura 3.2: Ficha de Laura Gómez](Entregas/capturas/23_empleado_laura.png)  
*Figura 3.2: Ficha de Laura Gómez (Cocina).*

![Figura 3.3: Ficha de David Pérez](Entregas/capturas/24_empleado_david.png)  
*Figura 3.3: Ficha de David Pérez (Reparto).*

![Figura 3.4: Ficha de Carmen Ruiz](Entregas/capturas/25_empleado_carmen.png)  
*Figura 3.4: Ficha de Carmen Ruiz (Administración).*

---

### 3.3 Vistas de empleados (Kanban y Lista)

![Figura 3.5: Vista Kanban de empleados](Entregas/capturas/21_empleados_kanban.png)  
*Figura 3.5: Vista general de los empleados en formato tarjetas Kanban.*

![Figura 3.6: Vista en lista](Entregas/capturas/22_empleados_lista.png)  
*Figura 3.6: Vista en lista detallada con departamento, cargo y correo.*

---

## 4. Circuito de Compras y Ventas

He instalado los módulos de **Compras** (`purchase`) y **Ventas** (`sale_management`) para realizar los flujos comerciales completos.

### 4.1 Compras: Presupuesto y orden de compra a Tostadores Balear S.A.

1. He creado el presupuesto de compra `P00001` al proveedor **Tostadores Balear S.A.** por **20 kg de Café en grano (arábica)**.
2. He pulsado en **Confirmar Pedido**, pasando de presupuesto a **Orden de Compra**.

![Figura 4.1: Presupuesto de compra](Entregas/capturas/30_compra_presupuesto.png)  
*Figura 4.1: Solicitud de presupuesto P00001 por 20 kg de café.*

![Figura 4.2: Confirmación de orden de compra](Entregas/capturas/31_compra_confirmar.png)  
*Figura 4.2: Orden de compra confirmada P00001.*

![Figura 4.3: Listado de compras](Entregas/capturas/32_compra_lista.png)  
*Figura 4.3: Listado de pedidos de compra.*

---

### 4.2 Ventas: Presupuesto, pedido y factura a Marta López

1. He creado el presupuesto de venta `S00001` para la clienta **Marta López** por **5 Desayunos completos**.
2. He pulsado en **Confirmar**, convirtiéndose en **Pedido de Venta**.
3. Desde el pedido he pulsado en **Crear Factura** y la he validado, completando el ciclo de facturación.

![Figura 4.4: Presupuesto de venta](Entregas/capturas/33_venta_presupuesto.png)  
*Figura 4.4: Presupuesto de venta S00001 para Marta López.*

![Figura 4.5: Pedido de venta confirmado](Entregas/capturas/34_venta_confirmada.png)  
*Figura 4.5: Pedido de venta confirmado S00001.*

![Figura 4.6: Creación de factura](Entregas/capturas/35_venta_crear_factura.png)  
*Figura 4.6: Asistente para crear la factura desde el pedido.*

![Figura 4.7: Factura borrador](Entregas/capturas/36_factura_borrador.png)  
*Figura 4.7: Factura borrador generada desde el pedido.*

![Figura 4.8: Factura de venta validada](Entregas/capturas/37_factura_venta.png)  
*Figura 4.8: Factura de venta validada.*

![Figura 4.9: Pedido totalmente facturado](Entregas/capturas/38_venta_pedido.png)  
*Figura 4.9: Pedido con estado de facturación Totalmente Facturado.*

---

## 5. Punto de Venta (TPV)

### 5.1 Catálogo de productos de cafetería

He instalado el módulo **Punto de Venta** (`point_of_sale`) y he creado los tres productos con la opción *Disponible en TPV* activada:
* **Café con leche:** 1,80 €
* **Croissant:** 1,50 €
* **Zumo natural:** 2,50 €

![Figura 5.1: Productos del TPV](Entregas/capturas/40_productos_tpv.png)  
*Figura 5.1: Productos dados de alta en el Punto de Venta.*

![Figura 5.2: Panel del TPV](Entregas/capturas/41_tpv_panel.png)  
*Figura 5.2: Panel de sesiones del TPV.*

---

### 5.2 Venta en TPV y emisión de recibo

1. He abierto una nueva sesión de TPV en la caja de barra.
2. He registrado la comanda: **2 Cafés con leche** (3,60 €) y **1 Croissant** (1,50 €), total: **5,61 €** con IVA.
3. He cobrado el importe en **Efectivo** y he emitido el recibo/ticket para el cliente.

![Figura 5.2b: Apertura de sesión](Entregas/capturas/42_tpv_abrir.png)  
*Figura 5.2b: Apertura de la sesión del TPV.*

![Figura 5.3: Comanda en TPV](Entregas/capturas/44_tpv_pedido.png)  
*Figura 5.3: Selección de los productos (2 cafés con leche y 1 croissant).*

![Figura 5.4: Pago en efectivo](Entregas/capturas/45_tpv_pago.png)  
*Figura 5.4: Registro del cobro en efectivo.*

![Figura 5.5: Ticket emitido](Entregas/capturas/46_tpv_recibo.png)  
*Figura 5.5: Ticket emitido con el detalle de artículos e IVA.*

---

### 5.3 Cierre de sesión y cuadre de caja

He cerrado la sesión del TPV comprobando que el efectivo real en caja coincide con los 5,61 € recaudados (**Diferencia: 0,00 €**), cerrando la sesión correctamente sin descuadre.

![Figura 5.6: Cuadre y cierre de sesión](Entregas/capturas/43_tpv_apertura.png)  
*Figura 5.6: Control de efectivo con diferencia cero y cierre de sesión.*

