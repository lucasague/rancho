# Rancho

Este repositorio contiene la herramienta interna **Ignis Latte**, un punto de venta ligero empaquetado en un único archivo: [`rancho.html`](./rancho.html). Al abrirlo en el navegador se cargan varios paneles que ayudan a llevar el control de productos, pedidos, cuentas abiertas y movimientos de caja directamente desde el navegador.

## Instrucción inicial sugerida para tareas en paralelo

Cuando abras una tarea nueva en Codex, incluye este resumen para que el agente entienda el contexto sin volver a inspeccionar todo el archivo:

```
Estás editando rancho.html, un punto de venta autónomo (Ignis Latte) empaquetado en un único HTML con pestañas de Apertura, Pedidos, Cierre y Productos. Los datos se guardan en localStorage y se recalculan combos, pendientes y cuentas abiertas automáticamente. Mantén la documentación en español y actualiza el README si cambias el flujo de uso.
```

## Descripción general de la página

La interfaz está dividida en pestañas que se ajustan según el flujo de trabajo diario:

- **Apertura**: permite registrar a la persona responsable del turno y configurar el fondo inicial en caja. También ofrece un desglose opcional por denominaciones para documentar con cuánto efectivo se empieza el día.
- **Pedidos**: muestra la grilla de productos vendibles, el resumen del pedido actual, sugerencias de cambio y el historial de órdenes registradas. Desde aquí se crean cuentas abiertas, se marca el estado de entrega de cada producto y se abre un panel flotante con los pendientes por cliente o por producto.
- **Cierre**: calcula la diferencia entre el efectivo esperado y el efectivo contado, reutilizando el desglose por denominaciones. La tabla de conteo muestra el fondo de caja, el efectivo realmente recaudado, el esperado y la diferencia. Desde esta pestaña se envía un correo de cierre (vía EmailJS) con el resumen del día —incluyendo el efectivo real— y se limpian las órdenes pagadas del almacenamiento local.
- **Productos**: expone un editor para mantener el catálogo. Cada fila permite cambiar nombre, precio, tipo (bebida/comida), icono y variantes. Los cambios se guardan en `localStorage` y se reflejan inmediatamente en la pestaña de pedidos.

## Lógica destacada en `rancho.html`

- **Selección de productos con variantes**: cada tile de la grilla se genera dinámicamente a partir de los datos guardados. Los productos con variantes despliegan un menú circular para elegir opciones específicas (por ejemplo, sabor de empanada). El contador integrado permite sumar y restar unidades rápidamente.
- **Cálculo inteligente de totales**: antes de mostrar el importe se evalúa si conviene armar combos bebida+comida utilizando el valor configurado en `PRECIO_COMBO`. El resumen resultante indica las líneas cobradas como combo y las unidades restantes a precio individual.
- **Gestión de pendientes**: el historial guarda por orden la cantidad, método de pago y variantes pedidas. Con esa información se construyen vistas agrupadas por cliente y por producto. Cada línea puede marcarse como servida directamente desde el panel de pendientes o desde la tabla principal.
- **Cuentas abiertas**: los pedidos pendientes se agrupan por nombre de cliente. Cuando se cierra una cuenta, el sistema consolida los productos acumulados, solicita el método de pago y transforma el registro en una orden pagada que se incluye en los resúmenes.
- **Apertura y cierre de caja**: la configuración de apertura persiste en `localStorage`, permitiendo recordar el fondo y el responsable elegido. Durante el cierre se calcula automáticamente la diferencia contra lo esperado usando las ventas en efectivo registradas, se detalla el conteo real de caja y se prepara el correo de reporte con dos archivos CSV incrustados (resumen general e historial de ventas).
- **Persistencia local**: productos, órdenes, cuentas abiertas y configuración de apertura se almacenan en `localStorage`. Esto permite trabajar sin conexión y conservar el estado entre recargas mientras se usa el mismo navegador.

## Flujo de uso recomendado

1. **Configura la apertura**: selecciona a la persona responsable y actualiza el fondo de caja si cambió desde el último turno.
2. **Registra pedidos** desde la pestaña principal. Usa “Enviar” para cobros inmediatos o “Pendiente” para acumular en una cuenta abierta. Marca cada producto como servido para mantener el panel de pendientes al día.
3. **Administra las cuentas abiertas**: desde la sección lateral, añade productos a cuentas existentes, cierra las que se paguen o elimínalas si se ingresaron por error.
4. **Mantén el catálogo al día** en la pestaña de productos cuando haya cambios de precios, iconografía o variantes.
5. **Realiza el cierre de caja** al finalizar la jornada: cuenta el efectivo, registra el monto, revisa la diferencia calculada y envía el correo automático. Las órdenes pagadas se depuran mientras que las cuentas abiertas se conservan para el siguiente turno.

## Almacenamiento local y dependencias

- Claves usadas en `localStorage`: `ignis_productos_v2`, `ordenes_v2`, `cuentas_abiertas_v2` y `config_apertura_caja_v1`.
- Dependencia externa: [`@emailjs/browser`](https://www.emailjs.com/docs/sdk/send-form/) para enviar el correo de cierre sin necesidad de backend.
- Para restablecer el estado a valores de fábrica basta con limpiar el almacenamiento local del navegador (Herramientas de desarrollador → Aplicación → Almacenamiento → `localStorage`).

## Cómo empezar

1. Clona este repositorio o descarga el archivo.
2. Abre `rancho.html` en tu navegador preferido (Chrome, Firefox, Edge o Safari funcionan sin configuración extra).
3. Opcional: si quieres trabajar con datos limpios, limpia el `localStorage` del dominio donde abras el archivo.

## Contribuciones

1. Crea una rama nueva para tus cambios.
2. Mantén toda la documentación en español y actualiza este README si modificas el flujo de uso.
3. Envía un pull request describiendo los cambios realizados.
