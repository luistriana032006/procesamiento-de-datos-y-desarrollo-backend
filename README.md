# Procesamiento de Datos y Desarrollo Backend

Este proyecto contiene material de aprendizaje sobre técnicas de limpieza y manejo de datos nulos utilizando Python, Pandas y Scikit-learn.

---

## Descripción del Proyecto

Este repositorio documenta el aprendizaje de técnicas fundamentales para el procesamiento de datos, específicamente enfocado en la **detección, análisis y tratamiento de valores nulos** en datasets.

### Objetivos de Aprendizaje

1. Identificar y cuantificar valores nulos en datasets
2. Aplicar estrategias de eliminación de datos
3. Implementar técnicas de imputación
4. Comparar enfoques tradicionales (pandas) vs modernos (scikit-learn)

---

## Estructura del Proyecto

```
procesamiento de datos y desarrollo backend/
├── tecnicasDeLimpiezaManejoDeNulos.ipynb    # Técnicas con pandas
├── forma_con_Scikit_learn.ipynb             # Técnicas con scikit-learn
├── conceptos_nuevos.md                       # Documentación de conceptos
├── limpieza_manejo_de_nulos/                # Entorno virtual
└── README.md                                 # Este archivo
```

---

## Tecnologías Utilizadas

### Librerías Principales

- **Python 3.13**
- **pandas 2.3.3** - Manipulación de datos
- **numpy 2.4.0** - Operaciones matemáticas
- **scikit-learn 1.8.0** - Machine Learning y preprocesamiento

### Herramientas

- **Jupyter Notebook** - Entorno interactivo
- **Git** - Control de versiones
- **Entorno Virtual** - `limpieza_manejo_de_nulos`

---

## Contenido y Aprendizajes

### 1. Técnicas de Limpieza con Pandas

**Archivo:** [tecnicasDeLimpiezaManejoDeNulos.ipynb](tecnicasDeLimpiezaManejoDeNulos.ipynb)

#### Verificación de Valores Nulos

```python
# Detectar valores nulos
df.isnull()

# Contar valores nulos por columna
df.isnull().sum()

# Calcular porcentaje de valores nulos
df.isnull().sum() / len(df) * 100
```

#### Reglas para Decidir Eliminación vs Imputación

| Porcentaje de Nulos | Estrategia Recomendada |
|---------------------|------------------------|
| < 5% | Imputación simple (media, mediana, moda) |
| 5% - 30% | Imputación cuidadosa (por grupos, KNN) |
| 30% - 60% | Evaluar si vale la pena imputar o descartar |
| > 60% | Usualmente descartar (salvo que sea columna clave) |

#### Estrategias de Eliminación

**A. Eliminar filas con cualquier valor nulo**
```python
df_sin_nulos = df.dropna()  # Por defecto: how='any'
```

**B. Eliminar filas solo si TODAS las columnas son nulas**
```python
df_todas_nulas = df.dropna(how='all')
```

**C. Eliminar filas con nulos en columnas específicas**
```python
df_columna_especifica = df.dropna(subset=['edad'])
```

**D. Eliminar columnas con valores nulos**
```python
df_sin_columnas_nulas = df.dropna(axis=1)
```

#### Técnicas de Imputación con Pandas

**Media (datos numéricos)**
```python
media_edad = df['edad'].mean()
df['edad'] = df['edad'].fillna(media_edad)
```

**Mediana (más robusta para outliers)**
```python
mediana_salario = df['salario'].median()
df['salario'] = df['salario'].fillna(mediana_salario)
```

**Moda (datos categóricos)**
```python
moda_ciudad = df['ciudad'].mode()[0]
df['ciudad'] = df['ciudad'].fillna(moda_ciudad)
```

**Valor constante**
```python
df['experiencia_en_años'] = df['experiencia_en_años'].fillna(0)
```

---

### 2. Técnicas Modernas con Scikit-learn

**Archivo:** [forma_con_Scikit_learn.ipynb](forma_con_Scikit_learn.ipynb)

#### SimpleImputer

Clase de scikit-learn que imputa valores faltantes de forma automática y reutilizable.

**Ventajas sobre pandas:**
- Reutilizable en producción
- Se integra con pipelines de ML
- Guarda parámetros aprendidos (media, mediana)
- Aplica transformación a múltiples columnas automáticamente

**Sintaxis básica:**
```python
from sklearn.impute import SimpleImputer

# Crear imputador
imputer = SimpleImputer(strategy='mean')  # 'mean', 'median', 'most_frequent', 'constant'

# Fit: Aprender parámetros (ej: calcular media)
# Transform: Aplicar transformación (reemplazar NaN)
df_imputado = imputer.fit_transform(df[columnas_numericas])
```

#### Estrategias de SimpleImputer

| Estrategia | Tipo de Datos | Uso |
|------------|---------------|-----|
| `'mean'` | Numéricos | Imputación con promedio |
| `'median'` | Numéricos | Más robusto ante outliers |
| `'most_frequent'` | Categóricos/Numéricos | Imputación con moda |
| `'constant'` | Cualquiera | Valor fijo (requiere `fill_value`) |

---

## Conceptos Clave Aprendidos

### 1. `dropna()` y el Concepto de Copias

**Descubrimiento importante:**
- `dropna()` devuelve una **copia nueva** del DataFrame por defecto
- No es necesario hacer `.copy()` antes de usar `dropna()`

```python
# ❌ Redundante (crea 2 copias):
df_sin_nulos = df.copy()
df_sin_nulos = df_sin_nulos.dropna()

# ✅ Optimizado (1 sola copia):
df_sin_nulos = df.dropna()

# Alternativa con inplace:
df_sin_nulos = df.copy()
df_sin_nulos.dropna(inplace=True)  # Modifica el DataFrame directamente
```

### 2. Sistema de Importaciones en Python

**Diferencia con Java:**
- Python requiere importaciones **explícitas** para TODO
- Java auto-importa clases del mismo paquete
- Filosofía Python: "Explícito es mejor que implícito"

```python
# Siempre necesitas importar:
from sklearn.impute import SimpleImputer

# Equivalente a:
import sklearn.impute
imputer = sklearn.impute.SimpleImputer()
```

**Ventajas:**
- Mayor claridad sobre el origen de cada clase
- Control fino sobre qué se carga en memoria
- Evita conflictos de nombres
- Los IDEs modernos (VSCode) auto-importan

### 3. `fit_transform()` - El Patrón de Scikit-learn

**Dos pasos separados:**

1. **`fit()`** - Aprender parámetros
   ```python
   imputer.fit(datos)  # Calcula media, mediana, etc.
   ```

2. **`transform()`** - Aplicar transformación
   ```python
   datos_transformados = imputer.transform(datos)  # Reemplaza NaN
   ```

**Un solo paso:**
```python
datos_transformados = imputer.fit_transform(datos)  # fit() + transform()
```

**¿Por qué es necesario transformar?**
- `SimpleImputer` no modifica datos directamente
- Permite reutilizar parámetros aprendidos en datos nuevos
- Esencial para modelos de ML en producción

**Ejemplo de reutilización:**
```python
# Entrenamiento (una vez)
imputer.fit(datos_entrenamiento)  # Aprende: media_edad = 30

# Producción (miles de veces)
datos_nuevos_limpios = imputer.transform(datos_nuevos)  # Usa media = 30
```

---

## Dataset de Ejemplo

El proyecto trabaja con un dataset de ejemplo con las siguientes características:

```python
data = {
    'edad': [25, 30, np.nan, 45, 50, np.nan, 35],
    'salario': [30000, 45000, 50000, np.nan, 60000, 55000, np.nan],
    'ciudad': ['madrid', 'barcelona', None, 'valencia', 'madrid', 'barcelona', 'valencia'],
    'experiencia_en_años': [2, 5, 7, np.nan, 10, 8, 4]
}
```

**Distribución de nulos:**
- edad: 2 nulos (28.57%)
- salario: 2 nulos (28.57%)
- ciudad: 1 nulo (14.29%)
- experiencia_en_años: 1 nulo (14.29%)

---

## Configuración del Entorno

### Instalación del Entorno Virtual

```bash
# Crear entorno virtual
python -m venv limpieza_manejo_de_nulos

# Activar entorno (Windows)
limpieza_manejo_de_nulos\Scripts\activate

# Instalar dependencias
pip install pandas numpy scikit-learn jupyter ipykernel
```

### Registrar Kernel en Jupyter

```bash
python -m ipykernel install --user --name=limpieza_manejo_de_nulos
```

---

## Comparación: Pandas vs Scikit-learn

| Característica | Pandas (`fillna()`, `dropna()`) | Scikit-learn (`SimpleImputer`) |
|----------------|--------------------------------|--------------------------------|
| **Facilidad inicial** | ✅ Más simple | ❌ Más complejo (fit/transform) |
| **Reutilización** | ❌ Difícil | ✅ Fácil (guarda parámetros) |
| **Producción** | ❌ Complicado | ✅ Ideal |
| **Pipelines ML** | ❌ No integrado | ✅ Totalmente integrado |
| **Múltiples columnas** | ❌ Manual | ✅ Automático |
| **Casos de uso** | Análisis exploratorio rápido | Proyectos de ML en producción |

---

## Próximos Pasos

- [ ] Explorar `KNNImputer` (imputación con K-Nearest Neighbors)
- [ ] Implementar pipelines completos de preprocesamiento
- [ ] Trabajar con datasets reales más grandes
- [ ] Integrar técnicas de imputación con modelos de ML
- [ ] Aprender sobre `IterativeImputer` (imputación iterativa)

---

## Recursos y Referencias

- [Documentación de pandas](https://pandas.pydata.org/docs/)
- [Documentación de scikit-learn - Imputation](https://scikit-learn.org/stable/modules/impute.html)
- [Documentación de NumPy](https://numpy.org/doc/)

---

## Autor

Master en Inteligencia Artificial - Procesamiento de Datos y Desarrollo Backend

---

## Licencia

Este proyecto es material educativo de uso libre.
