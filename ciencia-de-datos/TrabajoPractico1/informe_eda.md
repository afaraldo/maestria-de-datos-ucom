# Informe EDA - DJBR CGR

## 1. Comprension del problema

El dataset contiene una muestra didactica de declaraciones juradas de bienes y rentas (DJBR) de funcionarios publicos. Cada fila representa una declaracion con informacion de:

- identificacion del registro;
- anio y tipo de presentacion;
- institucion y cargo;
- activos, pasivos y patrimonio neto;
- ingresos y egresos.

Este tipo de informacion permite apoyar decisiones de control, auditoria y priorizacion de revisiones patrimoniales.

## 2. Exploracion inicial

- Dataset `raw`: 246 filas y 15 columnas.
- Dataset luego de la limpieza reproducida: 182 filas y 15 columnas base para el EDA.
- Variables numericas principales: `activos_gs`, `pasivos_gs`, `patrimonio_neto_gs`, `ingreso_mensual_gs`, `ingreso_anual_gs`, `egreso_mensual_gs`, `egreso_anual_gs`.
- Variables categoricas principales: `institucion`, `cargo`, `tipo_presentacion`, `fuente`.

## 3. Limpieza de datos aplicada

Sobre el archivo `cgr_djbr_muestra_taller_raw.csv` se identificaron los siguientes problemas:

- 6 duplicados exactos.
- 4 nulos en `tipo_presentacion`.
- 4 nulos en `cargo`.
- 118 nulos en columnas numericas, concentrados en `egreso_anual_gs`.
- Montos monetarios en formato local con separador de miles y coma decimal.

### 3.1 Eliminacion de duplicados

Se eliminaron las filas repetidas de forma exacta para trabajar con una base sin observaciones redundantes.

### 3.2 Normalizacion de texto

Se aplico limpieza de espacios con `strip` en columnas de texto y se completaron los faltantes de `tipo_presentacion` y `cargo` con `No especificado`.

### 3.3 Conversion numerica e imputacion de nulos

Los montos se convirtieron desde formato local a tipo numerico. Luego, los faltantes en columnas numericas se rellenaron con `0`.

### 3.4 Outliers

Se identificaron outliers en las variables numericas usando el criterio IQR y luego se eliminaron del `dataframe` antes del analisis.

- outliers en `activos_gs`: 31 registros.
- outliers en `pasivos_gs`: 25 registros.
- outliers en `patrimonio_neto_gs`: 28 registros.
- outliers en `ingreso_mensual_gs`: 15 registros.
- outliers en `ingreso_anual_gs`: 2 registros.
- outliers en `egreso_mensual_gs`: 8 registros.
- outliers en `egreso_anual_gs`: 9 registros.

En total se eliminaron 58 filas al considerar la union de registros atipicos en las columnas numericas.

### 3.5 Control de calidad

Resultado final de la limpieza reproducida:

- base final: 182 filas;
- sin duplicados;
- sin nulos en `tipo_presentacion` ni `cargo`;
- sin nulos en columnas numericas.

## 4. EDA

### 4.1 Estadistica descriptiva

- `activos_gs` promedio: Gs. 9,909,028.15.
- `activos_gs` mediana: Gs. 7,803,896.96.
- `patrimonio_neto_gs` promedio: Gs. 8,503,421.27.
- `patrimonio_neto_gs` mediana: Gs. 6,347,299.54.
- `ingreso_mensual_gs` promedio: Gs. 5,420,059.16.
- `ingreso_mensual_gs` mediana: Gs. 5,256,355.24.

Las instituciones con mas registros en la base analizada son:

- Ministerio de Salud Publica y Bienestar Social: 38.
- Ministerio de Educacion y Ciencias: 31.
- Instituto de Prevision Social: 25.
- ESSAP: 17.
- Universidad Nacional de Asuncion: 15.

### 4.2 Analisis univariado

- `activos_gs` presenta asimetria positiva de 1.12, con promedio mayor que mediana.
- `patrimonio_neto_gs` presenta asimetria positiva de 1.32, aun mas marcada que en activos.
- `ingreso_mensual_gs` muestra menor sesgo, con asimetria de 0.49.
- La distribucion individual confirma que activos y patrimonio concentran pocos valores altos frente a la mayor parte de los registros.

### 4.3 Analisis bivariado

- La correlacion entre `activos_gs` e `ingreso_mensual_gs` es baja: 0.26.
- La correlacion entre `patrimonio_neto_gs` e `ingreso_mensual_gs` tambien es baja: 0.27.
- Las relaciones con `egreso_mensual_gs` e `ingreso_anual_gs` son aun menores, entre 0.12 y 0.18.
- Esto sugiere que el ingreso corriente no explica por si solo el nivel patrimonial declarado.

Por tipo de presentacion, la mediana de `patrimonio_neto_gs` mas alta se observa en:

- `No especificado`: Gs. 9,608,070.96.
- `ACTUALIZACION`: Gs. 6,814,956.28.
- `CESE`: Gs. 6,063,007.01.

Por institucion, las medianas mas altas de `patrimonio_neto_gs` se observan en:

- `poder judicial`: Gs. 31,926,150.71.
- `MINISTERIO DE OBRAS PUBLICAS Y COMUNICACIONES`: Gs. 17,693,259.74.
- `Ministerio de Economia y Finanzas`: Gs. 14,540,307.29.

## 5. Visualizacion

Las visualizaciones del notebook quedaron ordenadas asi:

- Histograma de `activos_gs`.
- Boxplot de `patrimonio_neto_gs`.
- Grafico de barras con top 10 instituciones por cantidad de registros.
- Scatter de `ingreso_mensual_gs` vs `activos_gs`.

Este orden acompana el esquema pedido y separa la lectura visual del analisis textual.

## 6. Anomalias

Se identificaron valores extremos sobre variables base del dataset, sin usar todavia el indicador final.

- `activos_gs`: 4 outliers, limite superior Gs. 28,419,924.65.
- `patrimonio_neto_gs`: 7 outliers, limite superior Gs. 24,713,383.08.
- `ingreso_mensual_gs`: 4 outliers, limite superior Gs. 10,106,351.55.

Los registros mas altos en `activos_gs` incluyen casos como:

- `REG-100046`, Ministerio de Economia y Finanzas, Gs. 35,517,709.35.
- `REG-100220`, poder judicial, Gs. 31,926,150.71.
- `REG-100229`, Ministerio de Salud Publica y Bienestar Social, Gs. 29,376,024.47.

Estos casos no necesariamente son errores, pero si son candidatos naturales para revision puntual.

## 7. Indicadores

Al final del flujo se construyo el indicador solicitado:

- `indice_riqueza = bienes / ingresos`
- implementacion usada en el notebook: `activos_gs / ingreso_mensual_gs`

Resultados del indicador:

- promedio: 1.98.
- mediana: 1.67.
- percentil 95: 4.96.
- maximo: 8.45.

Aplicando IQR sobre `indice_riqueza` se detectaron 5 registros atipicos, con limite superior de 5.76.

Los casos mas altos por `indice_riqueza` son:

- `REG-100044`, ESSAP, `indice_riqueza` 8.45.
- `REG-100129`, ESSAP, `indice_riqueza` 7.33.
- `REG-100019`, Ministerio de Salud Publica y Bienestar Social, `indice_riqueza` 6.68.

## 8. Conclusiones

- La limpieza previa sigue siendo necesaria para obtener una base analizable y comparable.
- Aun despues de depurar outliers iniciales, activos y patrimonio mantienen una distribucion sesgada a la derecha.
- Las relaciones lineales entre ingresos y patrimonio son bajas, por lo que conviene complementar el analisis con indicadores derivados.
- Separar `Visualizacion`, `Anomalias` e `Indicadores` mejora la lectura del notebook y respeta el orden metodologico solicitado.
- El `indice_riqueza`, presentado al final, permite identificar con claridad los casos patrimoniales mas desproporcionados respecto al ingreso mensual.

## 9. Recomendaciones

- Revisar manualmente los registros con mayor `indice_riqueza` y tambien los mayores valores absolutos de `activos_gs`.
- Estandarizar nombres de instituciones, ya que aparecen variantes de mayusculas, minusculas y acentos.
- Mantener un pipeline de limpieza reproducible antes de cualquier analisis posterior.
- Documentar la definicion de indicadores derivados al final del flujo analitico para evitar mezclar limpieza con interpretacion.

## 10. Entregables

- Notebook: `TrabajoPractico1.ipynb`
- Informe: `informe_eda.md`
- Visualizaciones: carpeta `visualizaciones/`
