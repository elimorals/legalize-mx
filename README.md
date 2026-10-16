# legalize-mx — La legislación de México como repositorio git

Leyes, códigos, reglamentos, acuerdos y demás normas **federales, de la Ciudad de México,
de los 31 estados y de cientos de municipios**, convertidas a Markdown y versionadas con git:

- **cada norma es un archivo**;
- **cada reforma es un commit**, fechado con su publicación oficial en el Diario Oficial de la
  Federación, la Gaceta Oficial o el Periódico Oficial del estado;
- el `git log` de un archivo es la historia legislativa de esa norma, desde su texto original
  hasta el vigente.

Está pensado para personas que investigan, para periodistas y desarrolladores y, sobre todo, para
**agentes de IA**: texto limpio, metadatos estructurados en cada archivo e historial consultable
con las herramientas de git de siempre.

| | |
|---|---|
| **Normas** | 33,974 archivos |
| **Commits** | 108,984 (33,974 textos originales + 75,009 reformas) |
| **Ámbitos** | federal, CDMX, 31 estados, 568 municipios |
| **Fechas** | de 1814 a 2026 |
| **Formato** | [Legalize Format Spec v0.4](https://github.com/legalize-dev/legalize/blob/main/SPEC.md) |
| **Fuente** | [Suprema Corte de Justicia de la Nación — Buscador Jurídico](https://legislacion.scjn.gob.mx) |

> Cifras al 2026-10-16. Crecen con cada publicación.

---

## Índice

1. [Inicio rápido](#inicio-rápido)
2. [Qué contiene](#qué-contiene)
3. [Estructura del repositorio](#estructura-del-repositorio)
4. [Identificadores](#identificadores)
5. [Formato de cada archivo](#formato-de-cada-archivo)
6. [El historial: commits y reformas](#el-historial-commits-y-reformas)
7. [Recetas](#recetas)
8. [Uso con agentes de IA y RAG](#uso-con-agentes-de-ia-y-rag)
9. [Qué no contiene y limitaciones conocidas](#qué-no-contiene-y-limitaciones-conocidas)
10. [Datasets relacionados](#datasets-relacionados)
11. [Fuentes, actualización y licencia](#fuentes-actualización-y-licencia)

---

## Inicio rápido

```bash
# Clon completo: todo el texto y toda la historia (~2 GB de descarga)
git clone https://github.com/elimorals/legalize-mx.git

# Clon ligero: historia completa, textos bajo demanda
git clone --filter=blob:none https://github.com/elimorals/legalize-mx.git

# Solo un estado (ejemplo: Jalisco y sus municipios)
git clone --filter=blob:none --sparse https://github.com/elimorals/legalize-mx.git
cd legalize-mx
git sparse-checkout set mx-jal 'mx-jal-*'
```

Leer la Constitución y su historia:

```bash
cat mx/7e/CPEUM.md                      # texto vigente
git log --oneline -- mx/7e/CPEUM.md     # sus 245 versiones, de 1917 a hoy
```

Si solo quieres el texto vigente en tablas (Parquet), sin git, usa el dataset de Hugging Face
[`elimoralsmendox/legalize-mx`](https://huggingface.co/datasets/elimoralsmendox/legalize-mx):

```python
from datasets import load_dataset
leyes = load_dataset("elimoralsmendox/legalize-mx", "laws", split="train")
```

---

## Qué contiene

### Por orden de gobierno

| Orden | Carpetas | Normas |
|---|---|---|
| Federal (incluye tratados internacionales) | `mx/` | 8,402 |
| Estatal y CDMX | `mx-{estado}/` | 23,557 |
| Municipal | `mx-{estado}-{municipio}/` | 2,015 |

### Por tipo de norma (`rank`)

| `rank` | Tipo | Normas |
|---|---|---|
| `reglamento` | Reglamento | 10,406 |
| `ley` | Ley | 8,003 |
| `acuerdo` | Acuerdo | 5,942 |
| `ley_de_ingresos` | Ley de ingresos (estatal o municipal) | 3,774 |
| `codigo` | Código | 2,913 |
| `estatuto` | Estatuto | 1,028 |
| `ley_organica` | Ley orgánica | 769 |
| `otro` | Otro | 381 |
| `lineamientos` | Lineamientos | 219 |
| `decreto` | Decreto | 177 |
| `ley_general` | Ley general | 125 |
| `ley_federal` | Ley federal | 71 |
| `presupuesto` | Presupuesto de egresos | 59 |
| `manual` | Manual | 51 |
| `constitucion` | Constitución (federal y estatales) | 34 |
| `reglas` | Reglas | 13 |
| `norma` | Norma | 5 |
| `bando` | Bando municipal | 4 |

### Por vigencia (`status`)

| `status` | Significado | Normas |
|---|---|---|
| `in_force` | Vigente | 20,118 |
| `repealed` | Abrogada | 13,332 |
| `annulled` | Invalidada por la SCJN | 522 |
| `expired` | Perdió vigencia por plazo | 2 |

Las normas abrogadas **se conservan**: su archivo muestra el último texto que tuvieron y su
historia queda completa. Para trabajar solo con derecho vigente, filtra por `status: "in_force"`.

### Por estado

| Clave | Entidad | Carpeta estatal | Normas estatales | Municipios | Normas municipales |
|---|---|---|---|---|---|
| `AGU` | Aguascalientes | `mx-agu/` | 869 | 11 | 133 |
| `BCN` | Baja California | `mx-bcn/` | 635 | 5 | 166 |
| `BCS` | Baja California Sur | `mx-bcs/` | 464 | 4 | 26 |
| `CAM` | Campeche | `mx-cam/` | 539 | 8 | 16 |
| `CHH` | Chihuahua | `mx-chh/` | 676 | 11 | 18 |
| `CHP` | Chiapas | `mx-chp/` | 668 | 55 | 173 |
| `CMX` | Ciudad de México | `mx-cmx/` | 1,228 | 0 | 0 |
| `COA` | Coahuila | `mx-coa/` | 788 | 14 | 48 |
| `COL` | Colima | `mx-col/` | 513 | 10 | 39 |
| `DUR` | Durango | `mx-dur/` | 620 | 30 | 46 |
| `GRO` | Guerrero | `mx-gro/` | 657 | 1 | 1 |
| `GUA` | Guanajuato | `mx-gua/` | 646 | 43 | 112 |
| `HID` | Hidalgo | `mx-hid/` | 802 | 13 | 20 |
| `JAL` | Jalisco | `mx-jal/` | 722 | 2 | 7 |
| `MEX` | Estado de México | `mx-mex/` | 1,182 | 10 | 12 |
| `MIC` | Michoacán | `mx-mic/` | 887 | 78 | 146 |
| `MOR` | Morelos | `mx-mor/` | 982 | 23 | 87 |
| `NAY` | Nayarit | `mx-nay/` | 609 | 8 | 15 |
| `NLE` | Nuevo León | `mx-nle/` | 758 | 50 | 540 |
| `OAX` | Oaxaca | `mx-oax/` | 1,156 | 15 | 15 |
| `PUE` | Puebla | `mx-pue/` | 1,226 | 4 | 9 |
| `QUE` | Querétaro | `mx-que/` | 710 | 8 | 14 |
| `ROO` | Quintana Roo | `mx-roo/` | 592 | 7 | 43 |
| `SIN` | Sinaloa | `mx-sin/` | 504 | 16 | 36 |
| `SLP` | San Luis Potosí | `mx-slp/` | 782 | 12 | 14 |
| `SON` | Sonora | `mx-son/` | 657 | 21 | 31 |
| `TAB` | Tabasco | `mx-tab/` | 563 | 14 | 87 |
| `TAM` | Tamaulipas | `mx-tam/` | 623 | 2 | 2 |
| `TLA` | Tlaxcala | `mx-tla/` | 0 | 0 | 0 |
| `VER` | Veracruz | `mx-ver/` | 1,024 | 59 | 71 |
| `YUC` | Yucatán | `mx-yuc/` | 734 | 30 | 80 |
| `ZAC` | Zacatecas | `mx-zac/` | 741 | 4 | 8 |

### Las normas con más historia

| Versiones | Norma | Archivo |
|---|---|---|
| 245 | CONSTITUCION POLITICA DE LOS ESTADOS UNIDOS MEXICANOS | [`mx/7e/CPEUM.md`](mx/7e/CPEUM.md) |
| 211 | CONSTITUCION POLITICA DEL ESTADO DE TAMAULIPAS | [`mx-tam/ff/TAM-constitucion-politica-del-estado-de-tamaulipas-8069.md`](mx-tam/ff/TAM-constitucion-politica-del-estado-de-tamaulipas-8069.md) |
| 206 | ACUERDO GENERAL 3/2013 DEL PLENO DEL CONSEJO DE LA JUDICATURA FEDERAL [RELATIVO … | [`mx/14/FED-acuerdo-general-3-2013-del-pleno-del-consejo-de-la-92277.md`](mx/14/FED-acuerdo-general-3-2013-del-pleno-del-consejo-de-la-92277.md) |
| 197 | CONSTITUCION POLITICA DEL ESTADO LIBRE Y SOBERANO DE OAXACA | [`mx-oax/f0/OAX-constitucion-politica-del-estado-libre-y-soberano-de-oaxaca-6921.md`](mx-oax/f0/OAX-constitucion-politica-del-estado-libre-y-soberano-de-oaxaca-6921.md) |
| 188 | CONSTITUCION POLITICA DEL ESTADO DE SINALOA | [`mx-sin/1f/SIN-constitucion-politica-del-estado-de-sinaloa-7101.md`](mx-sin/1f/SIN-constitucion-politica-del-estado-de-sinaloa-7101.md) |
| 178 | CONSTITUCION POLITICA DEL ESTADO LIBRE Y SOBERANO DE NUEVO LEON | [`mx-nle/4b/NLE-constitucion-politica-del-estado-libre-y-soberano-de-nuevo-7472.md`](mx-nle/4b/NLE-constitucion-politica-del-estado-libre-y-soberano-de-nuevo-7472.md) |
| 176 | CODIGO PENAL PARA EL ESTADO DE BAJA CALIFORNIA | [`mx-bcn/c2/BCN-codigo-penal-para-el-estado-de-baja-california-6370.md`](mx-bcn/c2/BCN-codigo-penal-para-el-estado-de-baja-california-6370.md) |
| 176 | CONSTITUCION POLITICA DEL ESTADO LIBRE Y SOBERANO DE MEXICO | [`mx-mex/a0/MEX-constitucion-politica-del-estado-libre-y-soberano-de-mexico-5949.md`](mx-mex/a0/MEX-constitucion-politica-del-estado-libre-y-soberano-de-mexico-5949.md) |
| 176 | CODIGO ADMINISTRATIVO DEL ESTADO DE MEXICO | [`mx-mex/52/MEX-codigo-administrativo-del-estado-de-mexico-22882.md`](mx-mex/52/MEX-codigo-administrativo-del-estado-de-mexico-22882.md) |
| 171 | CONSTITUCION POLITICA DEL ESTADO DE CHIAPAS | [`mx-chp/be/CHP-constitucion-politica-del-estado-de-chiapas-4183.md`](mx-chp/be/CHP-constitucion-politica-del-estado-de-chiapas-4183.md) |
| 168 | CONSTITUCION POLITICA DEL ESTADO DE AGUASCALIENTES | [`mx-agu/e4/AGU-constitucion-politica-del-estado-de-aguascalientes-2653.md`](mx-agu/e4/AGU-constitucion-politica-del-estado-de-aguascalientes-2653.md) |
| 166 | CONSTITUCION POLITICA DEL ESTADO LIBRE Y SOBERANO DE YUCATAN | [`mx-yuc/e8/YUC-constitucion-politica-del-estado-libre-y-soberano-de-yucatan-8141.md`](mx-yuc/e8/YUC-constitucion-politica-del-estado-libre-y-soberano-de-yucatan-8141.md) |
| 165 | CODIGO PENAL PARA EL ESTADO DE NUEVO LEON | [`mx-nle/2e/NLE-codigo-penal-para-el-estado-de-nuevo-leon-7411.md`](mx-nle/2e/NLE-codigo-penal-para-el-estado-de-nuevo-leon-7411.md) |
| 163 | CODIGO MUNICIPAL PARA EL ESTADO DE CHIHUAHUA | [`mx-chh/db/CHH-codigo-municipal-para-el-estado-de-chihuahua-4610.md`](mx-chh/db/CHH-codigo-municipal-para-el-estado-de-chihuahua-4610.md) |
| 161 | LEY ORGANICA MUNICIPAL DEL ESTADO DE MEXICO | [`mx-mex/a5/MEX-ley-organica-municipal-del-estado-de-mexico-6081.md`](mx-mex/a5/MEX-ley-organica-municipal-del-estado-de-mexico-6081.md) |

---

## Estructura del repositorio

```
legalize-mx/
├── .legalize.yml                 ← cómo se construye la ruta de cada norma (spec v0.4)
├── README.md
├── LICENSE
├── mx/                           ← federal: leyes, reglamentos, acuerdos, tratados
│   ├── 7e/CPEUM.md
│   ├── 5a/LFT.md
│   └── …
├── mx-jal/                       ← Jalisco: legislación estatal
│   └── b9/JAL-codigo-civil-del-estado-de-jalisco-5751.md
├── mx-jal-039/                   ← Jalisco, municipio 039 (Guadalajara)
│   └── …
└── …
```

La ruta de cada archivo sigue la plantilla declarada en `.legalize.yml`:

```
{directory}/{id_sha1_2}/{identifier}.md
```

| Parte | Qué es |
|---|---|
| `{directory}` | La jurisdicción: `mx` (federal), `mx-{estado}` o `mx-{estado}-{municipio}`. |
| `{id_sha1_2}` | Los 2 primeros caracteres hexadecimales de `sha1(identifier)`. Reparte los archivos en 256 subcarpetas para que ninguna crezca demasiado. |
| `{identifier}` | El identificador de la norma (ver abajo). |

La subcarpeta se calcula a partir del identificador, así que puedes construir la ruta sin buscarla:

```python
import hashlib
def ruta(identifier: str, directory: str) -> str:
    return f"{directory}/{hashlib.sha1(identifier.encode()).hexdigest()[:2]}/{identifier}.md"

ruta("LFT", "mx")   # 'mx/5a/LFT.md'
```

O simplemente: `find . -name 'LFT.md'`.

### Claves de las carpetas

- **Estado:** la clave de 3 letras en minúsculas (`agu`, `bcn`, …, `cmx` para la Ciudad de México).
  La tabla completa está en [Por estado](#por-estado).
- **Municipio:** los 3 últimos dígitos de la clave geoestadística del INEGI.
  `mx-jal-039` es la clave INEGI `14039`, Guadalajara. El nombre y la clave completa están en
  el frontmatter de cada archivo (`municipality`, `municipality_inegi`).
- Las demarcaciones territoriales (alcaldías) de la CDMX no tienen carpeta propia: su normativa
  está en `mx-cmx/`.

---

## Identificadores

El `identifier` es estable: no cambia cuando la norma se reforma, y es la llave para cruzar con
los datasets de Hugging Face.

| Forma | Ejemplo | Se usa para |
|---|---|---|
| `{ABREVIATURA}` | `CPEUM`, `LFT`, `LISR`, `PEF_2026` | Las ~292 leyes federales que la Cámara de Diputados publica con abreviatura oficial |
| `FED-{título}-{IdOrd}` | `FED-codigo-civil-federal-antes-codigo-civil-para-el-distrito-641` | El resto de la normativa federal (reglamentos, acuerdos, lineamientos…) |
| `{EST}-{título}-{IdOrd}` | `AGU-codigo-penal-para-el-estado-de-aguascalientes-94225` | Normas estatales y municipales |

- `{título}` es el título de la norma en minúsculas, sin acentos, recortado.
- `{IdOrd}` es el número de ordenamiento de la SCJN, el mismo que aparece en `scjn_id` y en la
  URL de `source`. Garantiza que dos normas con el mismo título no choquen.
- Las normas municipales llevan el prefijo de su estado y se distinguen por la carpeta.

---

## Formato de cada archivo

Cada archivo tiene un **frontmatter YAML** con los metadatos y, debajo, el **texto en Markdown**.

```markdown
---
title: "LEY FEDERAL DEL TRABAJO"
identifier: "LFT"
country: "mx"
rank: "ley_federal"
publication_date: "1970-04-01"
last_updated: "2026-05-14"
status: "in_force"
source: "https://legislacion.scjn.gob.mx/Buscador/Paginas/wfOrdenamientoDetalle.aspx?IdOrd=410&Vig=1&TPub=6"
department: "Diario Oficial de la Federación"
scjn_id: "410"
scjn_status: "VIGENTE"
gazette: "Diario Oficial de la Federación"
original_category: "LEY"
original_gazette_section: "26 SECCION SEGUNDA"
reform_count: "60"
scjn_last_update: "2026-05-14"
issue_date: "1969-12-23"
abbreviation: "LFT"
---
# LEY FEDERAL DEL TRABAJO

…

## TITULO PRIMERO. Principios Generales

###### Artículo 1°.

La presente Ley es de observancia general en toda la República…
```

### Campos del frontmatter

**Obligatorios** (los define el spec y vienen en todos los archivos):

| Campo | Contenido |
|---|---|
| `title` | Título oficial, como lo publica la SCJN. |
| `identifier` | Identificador estable (ver [Identificadores](#identificadores)). |
| `country` | Siempre `mx`. |
| `rank` | Tipo de norma, normalizado (ver [tabla](#por-tipo-de-norma-rank)). |
| `publication_date` | Fecha de publicación del texto original en el periódico oficial. |
| `last_updated` | Fecha de publicación de la última reforma incorporada al texto. |
| `status` | `in_force` (vigente), `repealed` (abrogada), `annulled` (invalidada por la SCJN) o `expired` (perdió vigencia por plazo). |
| `source` | Ficha de la norma en el Buscador Jurídico de la SCJN. |

**Propios de México:**

| Campo | Contenido | Presente en |
|---|---|---|
| `jurisdiction` | `mx-{estado}` o `mx-{estado}-{municipio}`. Falta en las federales. | estatales y municipales |
| `state` | Clave de 3 letras del estado (`JAL`). | estatales y municipales |
| `municipality` | Nombre del municipio (`Guadalajara`). | municipales |
| `municipality_inegi` | Clave geoestadística INEGI de 5 dígitos (`14039`). | municipales |
| `department` / `gazette` | Periódico oficial donde se publica. | todas |
| `scjn_id` | Número de ordenamiento de la SCJN (`IdOrd`). | todas |
| `scjn_status` | Estado según la SCJN, sin normalizar (`VIGENTE`, `ABROGADO`…). | todas |
| `scjn_last_update` | Última actualización registrada por la SCJN. | todas |
| `original_category` | Categoría original de la SCJN (`LEY`, `REGLAMENTO`, `ACUERDO`…). | todas |
| `original_gazette_section` | Sección del periódico oficial. | casi todas |
| `issue_date` | Fecha de expedición (firma), cuando es distinta de la publicación. | casi todas |
| `reform_count` | Número de versiones que publica la SCJN. | todas |
| `abbreviation` | Abreviatura oficial de la Cámara de Diputados. | leyes federales con abreviatura |

### El cuerpo

- `#` es el título de la norma.
- `##` a `#####` son las divisiones: libro, título, capítulo, sección…
- `######` es cada **artículo** (`###### Artículo 1°.`), así que puedes dividir por artículos
  con una sola expresión regular.
- Las notas de la SCJN (`[N. DE E. …]`) se conservan como citas (`>`): explican transitorios,
  fe de erratas y entradas en vigor.
- El texto es el de la SCJN, sin reescribir. Las tablas se convierten a tablas Markdown cuando es
  posible.

---

## El historial: commits y reformas

Para cada norma, la SCJN publica el **texto consolidado vigente después de cada reforma**. Cada uno
de esos textos es un commit:

| Tipo | Significado | Commits |
|---|---|---|
| `[bootstrap]` | Texto original de la norma (primera versión conocida) | 33,974 |
| `[reform]` | Texto después de una reforma | 75,009 |
| `[fix-pipeline]` | Metadatos del repositorio (README, `.legalize.yml`); nunca toca una norma | 1 |

### Anatomía de un commit

```
[reform] LEY FEDERAL DEL TRABAJO — arts. 303, 304, 305 and 7 more

Norm: LFT
Disposition: DOF-5787418
Date: 2026-05-14
Source: https://legislacion.scjn.gob.mx/Buscador/Paginas/wfOrdenamientoDetalle.aspx?IdOrd=410&Vig=1&TPub=6

Affected articles: Artículo 303., CAPITULO XI, Artículo 304., …
Change: DECRETO 124/2026

Source-Id: DOF-5787418
Norm-Id: LFT
Source-Date: 2026-05-14
```

| Línea | Qué es |
|---|---|
| Asunto | Tipo, título de la norma y artículos que cambian. |
| `Affected articles` | Las partes del texto que cambian respecto a la versión anterior (calculado comparando ambos textos). |
| `Change` | El decreto o acuerdo que hizo la reforma, como lo registra la SCJN. |
| `Source-Id` | Identificador de la publicación oficial: `DOF-{código de nota}` cuando la reforma federal se pudo ligar a su nota del DOF; `PO-{EST}-{fecha}-{IdOrd}-{versión}` para los periódicos oficiales de los estados. |
| `Norm-Id` | El `identifier` de la norma. Sirve para filtrar con `git log --grep`. |
| `Source-Date` | **Fecha real de publicación** de esa versión. |

### Fechas

- La **fecha del commit** (`author date`) es la fecha de publicación oficial de esa versión. Por eso
  `git log` ordena las reformas cronológicamente y `git log --before=…` reconstruye el derecho de
  cualquier día.
- **Antes de 1970:** git no guarda fechas anteriores al 1 de enero de 1970, así que esos commits
  quedan fechados el `1970-01-02`. La fecha verdadera está siempre en el trailer `Source-Date` y,
  para el texto original, en `publication_date`. Usa `Source-Date` cuando necesites fechas exactas.

### Autor

Todos los commits llevan la identidad del pipeline, `Legalize <legalize@legalize.dev>`, la misma que
usan todos los repositorios del proyecto [Legalize](https://legalize.dev). Por eso GitHub los
muestra asociados a la cuenta dueña de ese correo. Los commits los genera el pipeline a partir de
la fuente oficial; ninguna persona edita el texto de las leyes a mano.

---

## Recetas

**Ver una ley tal como estaba en una fecha:**

```bash
git log -1 --format=%H --before=2019-05-01 -- mx/5a/LFT.md   # commit vigente ese día
git show $(git log -1 --format=%H --before=2019-05-01 -- mx/5a/LFT.md):mx/5a/LFT.md
```

**Qué cambió en una reforma:**

```bash
git log --oneline -- mx/5a/LFT.md             # elegir el commit
git show --word-diff <commit> -- mx/5a/LFT.md
```

**Comparar dos momentos:**

```bash
git diff --word-diff \
  $(git log -1 --format=%H --before=2012-11-30 -- mx/5a/LFT.md) \
  $(git log -1 --format=%H --before=2019-05-02 -- mx/5a/LFT.md) -- mx/5a/LFT.md
```

**Todas las reformas publicadas un día (por fecha real):**

```bash
git log --format='%s' --grep='^Source-Date: 2024-06-10$'
```

**Todas las reformas que hizo un decreto del DOF:**

```bash
git log --oneline --grep='^Source-Id: DOF-5787418$'
```

**Historia de una norma por su identificador, aunque no sepas la ruta:**

```bash
git log --oneline --grep='^Norm-Id: LFT$'
```

**Todas las normas vigentes de un municipio:**

```bash
grep -l '^status: "in_force"' mx-jal-039/*/*.md
```

**Buscar un tema en todo el país:**

```bash
git grep -l -i 'violencia digital' -- 'mx*/*/*.md'
```

**Leer los metadatos en Python:**

```python
import pathlib, yaml

for f in pathlib.Path("mx-nle").glob("*/*.md"):
    texto = f.read_text(encoding="utf-8")
    meta, cuerpo = texto[4:].split("\n---\n", 1)
    meta = yaml.safe_load(meta)
    if meta["rank"] == "codigo" and meta["status"] == "in_force":
        print(meta["identifier"], meta["last_updated"], meta["title"])
```

**Dividir una norma por artículos:**

```python
import re
articulos = re.split(r"^###### ", cuerpo, flags=re.M)[1:]
```

---

## Uso con agentes de IA y RAG

- **Unidad de recuperación:** el artículo (`######`). Conserva como metadatos de cada fragmento
  `identifier`, `title`, `rank`, `jurisdiction`, `status`, `last_updated` y el encabezado de su
  capítulo.
- **Cita:** `source` lleva a la ficha oficial de la SCJN; `Source-Id` y `Source-Date` del commit
  identifican la publicación exacta de esa versión.
- **Vigencia:** filtra por `status: "in_force"` salvo que la pregunta sea histórica. Para preguntas
  del tipo "¿qué decía la ley en 2015?", usa `git log --before` en lugar del texto vigente.
- **Jerarquía:** `rank` y la carpeta permiten priorizar constitución > leyes > reglamentos >
  acuerdos, y federal > estatal > municipal, cuando aplique.
- **Actualizaciones:** un `git pull` trae solo las reformas nuevas; `git diff ORIG_HEAD --stat`
  lista qué normas cambiaron para reindexar solo esas.

---

## Qué no contiene y limitaciones conocidas

- **Normas sin texto en Word.** La SCJN publica el texto editable (Word) de una parte de sus
  ordenamientos; del resto solo tiene el PDF escaneado del periódico oficial. Esas normas, sobre
  todo acuerdos, decretos y bandos municipales, **todavía no están aquí**. Su ficha completa (título,
  vigencia, todas sus reformas con enlaces a los PDF) está en la configuración `catalog` del
  [dataset de Hugging Face](https://huggingface.co/datasets/elimoralsmendox/legalize-mx), y los PDF
  originales se están publicando en
  [`legalize-mx-raw`](https://huggingface.co/datasets/elimoralsmendox/legalize-mx-raw).
- **Cobertura municipal parcial.** Están los 568 municipios cuyas normas compila la SCJN
  con texto. México tiene 2,478 municipios, y muchos publican su normativa solo en sus propios
  portales o gacetas. Se están integrando nuevas fuentes.
- **Textos de la SCJN, no del periódico oficial.** El texto es la compilación de la SCJN y conserva
  sus notas (`N. DE E.`). Ante una discrepancia, el valor jurídico lo tiene la publicación oficial.
- **Fechas anteriores a 1970** en el historial de git: ver [Fechas](#fechas).
- **Versiones sin Word en la historia.** Si una versión intermedia solo existe en PDF, la historia
  salta de la versión anterior a la siguiente que sí tiene texto; el commit siguiente acumula ambos
  cambios.
- **Esto no es asesoría jurídica.** Es una copia fiel de fuentes públicas, hecha para facilitar su
  consulta y análisis.

---

## Datasets relacionados

| Dónde | Qué | Para qué |
|---|---|---|
| **Este repo** | Markdown + historial git | Historia de reformas, diffs, derecho en una fecha |
| [`elimoralsmendox/legalize-mx`](https://huggingface.co/datasets/elimoralsmendox/legalize-mx) · `laws` | Texto vigente de cada norma de este repo, en Parquet | Cargar todo en pandas, Spark o un índice vectorial |
| [`elimoralsmendox/legalize-mx`](https://huggingface.co/datasets/elimoralsmendox/legalize-mx) · `catalog` | Ficha de **todas** las normas que registra la SCJN (109,426), tengan o no texto | Saber qué existe, qué está vigente y dónde están sus PDF |
| [`elimoralsmendox/legalize-mx-raw`](https://huggingface.co/datasets/elimoralsmendox/legalize-mx-raw) | PDF originales del periódico oficial, por estado (en construcción) | Verificar contra el original y extraer texto de lo que falta |

El `identifier` es el mismo en los tres.

---

## Fuentes, actualización y licencia

### Fuentes

- **Suprema Corte de Justicia de la Nación — Buscador Jurídico / Sistema de Consulta de
  Ordenamientos** (https://legislacion.scjn.gob.mx): textos, versiones y metadatos.
- **Cámara de Diputados — Leyes Federales Vigentes**
  (https://www.diputados.gob.mx/LeyesBiblio/): abreviaturas oficiales de las leyes federales.
- **Diario Oficial de la Federación — datos abiertos** (https://sidof.segob.gob.mx): para ligar
  cada reforma federal con su publicación (`Source-Id: DOF-…`).
- **INEGI — Marco Geoestadístico** (https://gaia.inegi.org.mx/wscatgeo/v2/mgem/): claves y nombres
  de los municipios.

### Cómo se genera y actualiza

El repositorio lo produce un pipeline automatizado, basado en
[legalize-pipeline](https://github.com/legalize-dev/legalize-pipeline), que consulta las fuentes
oficiales en línea. No usa copias de terceros. Cada publicación solo **agrega** commits: la
historia publicada no se reescribe. Las normas nuevas entran con su historia completa, y las
reformas nuevas como commits nuevos.

Si encuentras un error (un texto que no coincide con la SCJN, una fecha mal ligada o una norma mal
clasificada), abre un [issue](https://github.com/elimorals/legalize-mx/issues) con el identificador
y la URL de `source`.

### Licencia

- **Textos legales:** de dominio público. Las leyes, reglamentos y demás disposiciones oficiales no
  son objeto de protección del derecho de autor (Ley Federal del Derecho de Autor, art. 14,
  fracción VIII).
- **Estructura, metadatos y organización de este repositorio:** ver [`LICENSE`](LICENSE).

> Fuente: Suprema Corte de Justicia de la Nación, Sistema de Consulta de Ordenamientos
> (https://legislacion.scjn.gob.mx).

### Créditos

Este repositorio forma parte del ecosistema [Legalize](https://legalize.dev), que mantiene la
legislación de varios países como repositorios git con un formato común. Formato:
[Legalize Format Spec v0.4](https://github.com/legalize-dev/legalize/blob/main/SPEC.md).
