
# Validación Masiva de CUIT con SAP Build Process Automation + Python

Automatización desarrollada como caso real de uso en **SAP Build Process Automation (BPA)**, integrando lectura de Excel, construcción dinámica de archivos, ejecución de un script de **Python**, análisis masivo de información del padrón y envío automático de resultados por correo electrónico.

Este caso demuestra una arquitectura completa de automatización híbrida BPA + Python que puede aplicarse a validaciones masivas, transformaciones de datos, procesos batch y automatización asistida.

---

# 📘 Contenido

- [Descripción General](#descripción-general)
- [Arquitectura del Proceso](#arquitectura-del-proceso)
- [Workflow Completo](#workflow-completo)
- [Lectura del Excel](#lectura-del-excel)
- [Iteración con For Each](#iteración-con-for-each)
- [Generación del Archivo de Entrada](#generación-del-archivo-de-entrada)
- [Ejecución del Script Python](#ejecución-del-script-python)
- [Archivos de Entrada y Salida](#archivos-de-entrada-y-salida)
- [Logs de Ejecución](#logs-de-ejecución)
- [Email Automático](#email-automático)
- [Snippets Técnicos](#snippets-técnicos)
- [Buenas Prácticas](#buenas-prácticas)

---

# 📄 Descripción General

La automatización realiza:

1. Lectura dinámica de un archivo Excel que contiene CUITs y razones sociales.
2. Determinación automática del rango real a procesar.
3. Construcción de un archivo `cuit_input.txt` usando lógica de concatenación.
4. Ejecución de un script Python (`consulta_padron_masivo.py`) que consulta el padrón.
5. Lectura del archivo resultante generado por Python.
6. Envío de un correo electrónico con una tabla HTML consolidada.

Este flujo permite procesar cientos o miles de CUITs de manera masiva y eficiente.

---

# 🏗️ Arquitectura del Proceso

```
Excel → BPA (Read + For Each + Append File) → Python Script → BPA (Read File) → Outlook Email
```

---

# 🔁 Workflow Completo

![Workflow Overview](workflow-overview.png)

Diagrama general del proceso en SAP Build Process Automation.

---

# 📥 Lectura del Excel

## **1. Get Row From Data**

Determina la última fila con datos para crear un rango dinámico.

![Get Row From Data](get-row-from-data.png)

- `referenceCell: A1`
- `verticalDirection: xlDown`
- Output: `rowIndex`

---

## **2. Get Values (Cells)**

Extrae CUIT y Razón Social en base al rango dinámico.

![Get Values](get-values-cells.png)

La expresión del rango:

```txt
"A2:B" + Step4.rowIndex
```

---

# 🔄 Iteración con For Each

![For Each + Append File](foreach-details.png)

- Lista utilizada: `returnedValues`
- Parámetro del loop: `fila`

El proceso recorre cada fila del Excel.

---

# 📄 Generación del Archivo de Entrada

Dentro del For Each se construye el archivo `cuit_input.txt`.

Expresión utilizada:

```txt
Step7.fila[0].split("").join('') + ";" + Step7.fila[1].split("").join('') + "\n"
```

Esta expresión genera una línea por registro.

Archivo resultante:

```
30525390086;COCA COLA FEMSA DE BUENOS AIRES S.A.
30714928836;GAJATE S.A.
...
```

---

# 🐍 Ejecución del Script Python

![Execute Command](execute-command.png)

La automatización ejecuta el script Python con parámetros:

```
command: C:\Users\juan.yniguez\AppData\Local\Programs\Python\Python314\python.exe
param: "C:\BPA\padron\consulta_padron_masivo.py"
dir: C:\BPA\padron
```

El script genera resultados en formato JSON y HTML.

---

# 📁 Archivos de Entrada y Salida

## **Input Folder**

![Input Folder](input-folder.png)

Ubicación esperada:  
`C:\BPA\pendientes\excel_ejemplo.xlsx`

---

## **Output Folder**

![Output Folder](output-folder.png)

Archivos generados:

- `cuit_input.txt`
- `padron.json`
- `resultado.html`
- `resultado.json`

---

# 📊 Logs de Ejecución

![Run Logs](run-logs.png)

Aquí se observa:

- Iteraciones FOReach (ej: 5 registros procesados)
- Una sola ejecución del comando Python
- Envío de correo automático

---

# 📧 Email Automático

![Email Sent](email-sent-example.png)

El flujo envía un correo HTML con:

- CUIT
- Razón social Excel
- Razón social del padrón
- Indicador de coincidencia
- Mensaje de error cuando aplica

---

# 🧩 Snippets Técnicos

## **For Each – Content Expression**

```txt
Step7.fila[0].split("").join('') + ";" + Step7.fila[1].split("").join('') + "\n"
```

## **Get Values – Range Expression**

```txt
"A2:B" + Step4.rowIndex
```

## **Execute Command**

```txt
command: C:\Users\juan.yniguez\AppData\Local\Programs\Python\Python314\python.exe
param: "C:\BPA\padron\consulta_padron_masivo.py"
dir: C:\BPA\padron
```

---

# 🧠 Buenas Prácticas

- Usar rangos dinámicos al leer Excel.
- Construir el archivo TXT una sola vez fuera del script.
- Evitar operar Python dentro del For Each.
- Estandarizar encoding UTF-8.
- Mantener BPA y Python desacoplados mediante intercambio de archivos.
- Documentar rutas, carpetas y salidas de forma clara.

---

# ✔️ Caso de Uso Completo

Este repositorio está listo para servir como:

- Ejemplo técnico en tu portfolio  
- Caso de uso real en entrevistas  
- Demostración de automatización híbrida BPA + Python  
- Material de capacitación interna  

---

**Desarrollado por Juan Manuel Yñiguez**
