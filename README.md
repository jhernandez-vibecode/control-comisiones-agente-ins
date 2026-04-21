# Control de Comisiones INS

App web para que agentes INS validen el pago de comisiones por quincena a partir del CSV oficial que entrega INS.

## Funcionalidades

- **Carga de CSV** (formato INS con `;` y coma decimal, codificación Latin-1)
- **Nombre y licencia del agente** persistentes en localStorage
- **Estadísticas** totales: comisión, primas, registros, duplicados
- **Desglose por línea** (AUT, IMR, PCR, VTC, etc.) con % sobre el total
- **Gráfico de barras** de comisión por línea
- **Detector de duplicados** — flag rojo a filas idénticas
- **Filtros** por línea, solo duplicados y búsqueda por nombre/póliza
- **Exportar a Excel** (resumen + detalle) respetando filtros activos
- **Botón Limpiar** para cargar la siguiente quincena

## Uso

Abrí `index.html` en el navegador (doble clic) o desplegalo en Netlify / GitHub Pages.

## Datos

Todo corre en el navegador del agente. Nada se envía a servidores. El CSV y la configuración quedan en `localStorage`.

---

**Powered by [Seguros Digitales SDI](https://segurosdigitales.com)**
