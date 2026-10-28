# Clasificador de flujos de caja

Clasifica automáticamente los movimientos bancarios del mes (DEPOSITOS, ITF, PAGO PROVEEDORES, TRANSF. ENTRE CIAS, etc.)
a partir de los **libros bancos** de cada empresa y arma un Excel por empresa con el formato del RESUMEN FLUJO.

**👉 App: https://luisfelipeabriojo.github.io/EMSA-FLUJOS-AUTOMATIZADOS/**

## Cómo se usa (sin instalar nada)
1. Abre la app en Chrome o Edge. La primera carga tarda ~15 s (descarga el motor).
2. **Modelo:** carga `historico.csv` (entrena en ~1 min) o `modelo_web.pkl` si ya lo tienes (instantáneo). Ambos están en el disco compartido.
3. **Libros bancos:** arrastra los `.xlsx` del mes (uno por empresa, con la empresa en el nombre: `ICP OCT 2026.xlsx`),
   elige el mes y pulsa *Clasificar*. Descarga un Excel por empresa o un .zip con todos.
4. **Revisa** en cada Excel la hoja MOVIMIENTOS: las filas amarillas tienen confianza baja. La columna CATEGORIA tiene lista desplegable.
5. **(Opcional) Aprende de las correcciones:** sube los Excel revisados en el paso 3 de la app; descarga el `historico.csv`
   actualizado y reemplaza el del disco compartido.

## Privacidad
Todo se procesa **dentro del navegador** (Python vía [Pyodide](https://pyodide.org)). Los libros, el histórico y los resultados
**no se envían a ningún servidor**. Internet solo se usa para descargar el motor (cdn.jsdelivr.net y pypi.org).
Este repositorio contiene solo código: ningún dato de las empresas (ver `.gitignore`).

## Resultados
Validado con agosto 2026 contra la clasificación del equipo: **99,1 %** de acierto en 12.623 movimientos.
Con el umbral de 90 % se revisa ~2,5 % de las filas, y el resto acierta 99,6 %.

## Cómo funciona
- **Memoria:** si un concepto (misma empresa y tipo) siempre tuvo la misma categoría, se repite.
- **Modelo:** regresión logística sobre el texto del concepto, códigos, empresa, banco, moneda y rango de monto.
  Solo propone categorías que esa empresa ya usó.
- El ITF que viene en la misma fila (BBVA y otros) se separa y va siempre a la categoría ITF.
- En ABONO / CARGO TRANSF. CTAS ordena por tipo de operación (overnight → BCR → cambio de moneda → fondos mutuos → otros)
  y luego por fecha. El orden se cambia en `TIPOS_OPERACION` de `comun.py`.
- Verifica que los saldos cuadren por cuenta.

| Archivo | Qué hace |
|---|---|
| `index.html` | La app web (generada; no editar a mano) |
| `web/plantilla.html` | Interfaz de la app |
| `app_web.py` | Puente entre la página y el clasificador |
| `leer_libro.py` | Lee el libro bancos y extrae los movimientos y saldos del mes |
| `modelo.py` | Clasificador (memoria + regresión logística) |
| `salida.py` | Escribe el Excel por empresa |
| `clasificar_mes.py`, `actualizar.py`, `entrenar.py` | Versión de línea de comandos |
| `extraer_historico.py` | Arma `historico.csv` desde RESUMEN FLUJO cerrados |
| `construir_web.py` | Regenera `index.html` |

## Para desarrollo
- Si cambias algún `.py`, regenera la app: `python construir_web.py`, luego commit y push.
- Versión de escritorio (requiere Python 3.10+): ver [LEEME.md](LEEME.md).

# Clasificador de flujos de caja (corre en tu laptop, sin internet)

## Instalar (una sola vez)
1. Instalar Python 3.10 o superior (python.org, marcar "Add Python to PATH").
2. En una terminal, dentro de esta carpeta:  pip install pandas openpyxl scikit-learn scipy

## Uso mensual (ej.: en noviembre se procesa octubre)
1. Copiar los libros bancos de cada empresa (ECSA OCT 2026.xlsx, ICP ..., etc.) a la carpeta LIBROS.
2. Doble clic en CLASIFICAR_MES.bat  (o:  python clasificar_mes.py LIBROS --salida SALIDA)
   - Toma automáticamente el mes anterior al actual. Para otro mes:  --mes 2026-09
   - La primera vez entrena el modelo (1-2 minutos).
3. En SALIDA queda un Excel por empresa:
   - RESUMEN: saldos por cuenta (rojo = el libro no cuadra) y totales por categoría.
   - MOVIMIENTOS: todo el mes; filas amarillas = revisar. La columna CATEGORIA tiene lista desplegable.
   - Una hoja por cuenta, agrupada por categorías como en los RESUMEN FLUJO.
4. Después de revisar y corregir en MOVIMIENTOS:
   python actualizar.py "SALIDA\ICP FLUJO OCTUBRE 2026 CLASIFICADO.xlsx"
   python entrenar.py historico.csv
   (rehace las hojas por cuenta y el modelo aprende de las correcciones)

## Archivos
- leer_libro.py: lee el libro bancos y se queda solo con el mes (soporta fechas escritas "1-Set-26").
- modelo.py: memoria de conceptos ya vistos + modelo de texto; solo propone categorías que esa empresa usa.
- comun.py: unifica variantes de nombre de categoría ("CARGO TRASNF CTAS" -> "CARGO TRANSF. CTAS").
- extraer_historico.py: arma historico.csv a partir de RESUMEN FLUJO ya cerrados (para agregar más meses).
- historico.csv: julio y agosto 2026 ya categorizados (25.698 movimientos).

## Versión web (sin instalar Python)
`index.html` es la misma herramienta corriendo dentro del navegador (Pyodide): no hace falta instalar nada
y los archivos no salen de la computadora. Se puede abrir con doble clic desde el disco compartido o publicar
en GitHub Pages. Pasos: 1) cargar `historico.csv` o `modelo_web.pkl`, 2) arrastrar los libros bancos del mes,
3) (opcional) subir los Excel revisados para actualizar el histórico.
- Si cambias algún .py, vuelve a generar la página: `python construir_web.py`
- El repositorio NO debe incluir datos (historico.csv, libros, salidas, modelos): ver `.gitignore`.
