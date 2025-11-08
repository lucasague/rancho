# Rancho

Este repositorio contiene la herramienta interna **Ignis Latte**, un punto de venta ligero empaquetado en un único archivo: [`rancho.html`](./rancho.html). Al abrirlo en el navegador se cargan varios paneles que ayudan a llevar el control de productos, pedidos, casas asignadas y movimientos de caja directamente desde el navegador.

## Instrucción inicial sugerida para tareas en paralelo

Cuando abras una tarea nueva en Codex, incluye este resumen para que el agente entienda el contexto sin volver a inspeccionar todo el archivo:

```
Estás editando rancho.html, un punto de venta autónomo (Ignis Latte) empaquetado en un único HTML con pestañas de Apertura, Pedidos, Cierre, Productos y Casas. Los datos se guardan en localStorage y se recalculan combos, pendientes y pagos automáticamente. Mantén la documentación en español y actualiza el README si cambias el flujo de uso.
```

## Descripción general de la página

La interfaz está dividida en pestañas que se ajustan según el flujo de trabajo diario:

- **Apertura**: permite configurar el fondo inicial en caja. El desglose por denominaciones está siempre visible para documentar con cuánto efectivo se empieza el día y, si se edita el total manualmente, limpia automáticamente los campos del desglose.
- **Pedidos**: muestra la grilla de productos vendibles, el resumen del pedido actual, sugerencias de cambio y el historial de órdenes registradas. Cada pedido se crea sin pago y desde la misma tabla se marcan los cobros (efectivo o Sinpe) y el estado de entrega de cada producto. El botón flotante abre el panel de pendientes por cliente o por producto.
- **Cierre**: calcula la diferencia entre el efectivo esperado y el efectivo contado, reutilizando el desglose por denominaciones. Además del resumen clásico incorpora una tabla con todas las órdenes sin pagar para registrarlas o moverlas a una casa antes de enviar el correo automático (vía EmailJS) con el resumen del día.
- **Productos**: expone un editor para mantener el catálogo. Cada fila permite cambiar nombre, precio, tipo (bebida/comida), icono y variantes. Los cambios se guardan en `localStorage` y se reflejan inmediatamente en la pestaña de pedidos.
- **Casas**: agrupa los pedidos asignados a las casas 1 a 6 en una cuadrícula 3×2. Desde aquí se pueden revisar los detalles, registrar el pago o retirar la orden tocando de nuevo el botón de casa para regresar al listado general.

## Lógica destacada en `rancho.html`

- **Selección de productos con variantes**: cada tile de la grilla se genera dinámicamente a partir de los datos guardados. Los productos con variantes despliegan un menú circular para elegir opciones específicas (por ejemplo, sabor de empanada). El menú flotante se cierra automáticamente al elegir una variante o al hacer clic fuera del producto, y el contador integrado permite sumar y restar unidades rápidamente.
- **Cálculo inteligente de totales**: antes de mostrar el importe se evalúa si conviene armar combos bebida+comida utilizando el valor configurado en `PRECIO_COMBO`. El resumen resultante indica las líneas cobradas como combo y las unidades restantes a precio individual.
- **Gestión de pendientes**: el historial guarda por orden la cantidad y variantes pedidas. Con esa información se construyen vistas agrupadas por cliente y por producto, listando cada unidad pendiente de forma independiente. Cada elemento puede marcarse como servido directamente desde el panel flotante o desde la tabla principal.
- **Registro de pagos**: cada pedido nace como “pendiente”. Desde la tabla principal, la pestaña de cierre o la pestaña de casas se puede registrar el cobro por efectivo o Sinpe. Los botones actúan como interruptores: si vuelves a pulsar el método elegido, la orden vuelve a estar pendiente y reaparecen todas las opciones. Las ventas pagadas se suman automáticamente al resumen diario y al correo de cierre.
- **Casas abiertas**: las órdenes asignadas a las casas 1 a 6 se almacenan junto al historial general, evitando duplicar datos. Desde la pestaña final es posible cobrar, quitar la asignación pulsando de nuevo el botón de casa o mantener cada pedido asociado a su casa correspondiente.
- **Apertura y cierre de caja**: la configuración de apertura persiste en `localStorage`, permitiendo recordar el fondo inicial configurado. Durante el cierre se calcula automáticamente la diferencia contra lo esperado usando las ventas en efectivo registradas, se detalla el conteo real de caja y se prepara el correo de reporte con dos archivos CSV incrustados (resumen general e historial de ventas).
- **Persistencia local**: productos, órdenes (incluyendo sus asignaciones a casas) y configuración de apertura se almacenan en `localStorage`. Esto permite trabajar sin conexión y conservar el estado entre recargas mientras se usa el mismo navegador.

## Flujo de uso recomendado

1. **Configura la apertura**: revisa o actualiza el fondo de caja si cambió desde el último turno, ya sea escribiendo el total directamente o ajustándolo con el desglose por denominaciones.
2. **Registra pedidos** desde la pestaña principal. Cada orden se almacena sin pago; marca los productos entregados y registra el cobro cuando corresponda. Tras escribir el nombre del cliente puedes pulsar Enter para registrar el pedido sin tocar el ratón.
3. **Clasifica los pedidos por casa** desde la pestaña de cierre (botones “Casa n”) o directamente en la pestaña final de casas para seguir el saldo de cada una.
4. **Mantén el catálogo al día** en la pestaña de productos cuando haya cambios de precios, iconografía o variantes.
5. **Realiza el cierre de caja** al finalizar la jornada: cuenta el efectivo, revisa las órdenes sin pagar desde la tabla dedicada, marca los cobros que falten o envíalos a una casa y, finalmente, envía el correo automático. Las órdenes pagadas se depuran mientras que los pendientes o asignados a casas se conservan para el siguiente turno.

## Almacenamiento local y dependencias

- Claves usadas en `localStorage`: `ignis_productos_v2`, `ordenes_v2` y `config_apertura_caja_v1`.
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
