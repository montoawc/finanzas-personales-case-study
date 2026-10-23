# Decisiones de producto

[← Volver al README](../README.md)

Cada decisión importante se documentó con lo que se descartó y el riesgo aceptado. Cuando el documento de alcance y una decisión se contradecían, se señalaba y se resolvía explícitamente, nunca por cuenta del asistente de IA.

| Tema | Decisión | Alternativas descartadas | Por qué | Riesgo aceptado |
|---|---|---|---|---|
| **Backend** | Supabase (Postgres, Auth, RLS, Storage) | Hosting compartido (suele ser MySQL/PHP, sin RLS ni Auth) · Internet Computer (obliga a rediseñar el esquema, sin SQL) | Cubre de fábrica aislamiento por usuario, almacenamiento privado, migraciones y backups | Límites del plan gratuito (pausa por inactividad); se evalúa plan pago |
| **Construcción de la UI** | Solo Claude Code (React + Vite) | Herramienta low-code | Control del código, pruebas y arquitectura | Más trabajo inicial |
| **Cuotas** | Gasto total al comprar; las cuotas son **compromisos de caja** | Gasto por cuota (se había elegido primero y se revirtió) | Evita doble conteo, siempre que el modelo lo garantice; coincide con el documento de alcance | Los presupuestos requieren un criterio propio (ver abajo) |
| **Presupuestos** | Criterio mixto: compras comunes por fecha de compra, cuotas en el mes en que vencen | A) por reconocimiento, como el documento · C) todo por mes de pago | Refleja cómo se vive un presupuesto mensual | Dos números distintos en el mismo mes (resultado vs. consumo del presupuesto); la pantalla lo aclara. Volver a A es cambiar una sola función, sin migrar datos |
| **Alertas** | Una sola por categoría, mes y umbral (50/75/90/100 %) | Re-alertar cada vez que se cruza | Evita fatiga de avisos | Solo dentro de la app; sin push ni mail |
| **Reservas** | El usuario **confirma** cuánto descontar al pagar el gasto real | Descuento automático | Evita descuentos erróneos en un caso con muchas variantes | Un paso manual más |
| **Reintegros** | Pendiente ≠ disponible; al cobrar se registra como ingreso vinculado al gasto original | Restar el reintegro del gasto | Honestidad del saldo: no se cuenta plata que no llegó | Una categoría adicional (ahorro/reintegro) para no mezclar |
| **Dólares** | Compra/venta de USD como operación explícita con las **dos cantidades reales**; la cotización se calcula | Conversión implícita · cotización elegida a mano | Cero ambigüedad y sin redondeos; refleja el circuito real (gasto en USD → compra de dólares → pago del resumen) | Solo une ARS con USD; sin comisiones ni impuestos |
| **Saldos** | Siempre etiquetados **calculado / conciliado / estimado** | Mostrar un único "saldo real" | Nunca afirmar algo que no se verificó con el banco | "Podés gastar" necesita una cuenta conciliada |
| **Borrado** | Anulación lógica con auditoría; excepción: "Restablecer datos" con frase de confirmación y contraseña reciente | Borrado libre | Trazabilidad contable | La excepción se aprobó explícitamente |
| **Entorno de pruebas** | Proyecto cloud de desarrollo con datos sintéticos | Supabase local con Docker | La máquina no soportaba virtualización | Sin `supabase test db` nativo; se resolvió con un script propio |
| **Fuera de alcance** | Conexión bancaria, OCR, scraping, pagos y asesoramiento financiero | — | Confiabilidad contable por encima de cantidad de funciones | Carga manual de datos |

## Lo que cambié de opinión

- **Cuotas:** primero elegí "gasto por cuota". Después de ver que el modelo puede evitar el doble conteo, volví a "gasto total al comprar", más fiel a la contabilidad.
- **Presupuestos:** el documento original pedía un criterio; evalué tres y elegí el mixto, documentando sus riesgos en lugar de esconderlos.
- **Mi propio error:** una migración de seguridad reabrió permisos que ya estaban cerrados. Lo detecté con los avisos de seguridad de Supabase, lo corregí y dejé una prueba que lo vigila.
