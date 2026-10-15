# legalize-mx

Legislación de México en formato Markdown, versionada como repositorio git.

Cada ley es un archivo; cada reforma es un commit con la fecha real de publicación oficial. El `git log` de cada ley te muestra su historia completa — cuándo se sancionó, qué artículos se modificaron y por qué norma.

Legislación federal, de la Ciudad de México, de los 31 estados y ordenamientos municipales, tal como la compila la Suprema Corte de Justicia de la Nación. Para cada reforma la SCJN publica el texto consolidado vigente tras ella, por lo que cada archivo lleva su historia completa: un commit por fecha de publicación en el Diario Oficial de la Federación, la Gaceta Oficial o el Periódico Oficial del Estado.

## Qué contiene

- **Leyes federales** (`mx/xx/{ABREVIATURA}.md`) — `mx/??/CPEUM.md`, `mx/??/LFT.md`
- **Otra normativa federal** (`mx/xx/FED-{titulo}-{IdOrd}.md`) — `mx/??/FED-codigo-civil-federal-antes-codigo-civil-para-el-distrito-641.md`
- **Legislación estatal** (`mx-{estado}/xx/{EST}-{titulo}-{IdOrd}.md`) — `mx-agu/??/AGU-codigo-penal-para-el-estado-de-aguascalientes-94225.md`
- **Normativa municipal** (`mx-{estado}-{municipio}/xx/{EST}-{titulo}-{IdOrd}.md`) — `mx-agu-005/??/AGU-codigo-de-conducta-del-municipio-de-jesus-maria-140968.md`

## Fuente de los datos

- **Suprema Corte de Justicia de la Nación — Sistema de Consulta de Ordenamientos; Cámara de Diputados; Diario Oficial de la Federación**
  - SCJN, Sistema de Consulta de Ordenamientos: https://legislacion.scjn.gob.mx/Buscador/Paginas/Buscar.aspx
  - Cámara de Diputados, Leyes Federales Vigentes: https://www.diputados.gob.mx/LeyesBiblio/index.htm
  - Diario Oficial de la Federación, datos abiertos: https://sidof.segob.gob.mx/datos_abiertos
  - INEGI, Marco Geoestadístico (claves municipales): https://gaia.inegi.org.mx/wscatgeo/v2/mgem/

## Atribución

> Fuente: Suprema Corte de Justicia de la Nación, Sistema de Consulta de Ordenamientos (https://legislacion.scjn.gob.mx). Las leyes son de dominio público en México.

## Otros países

Este repositorio es parte del proyecto **Legalize**, que mantiene legislación de múltiples países como repos git. Ver https://legalize.dev para el catálogo completo.

## Apoyar

Legalize es libre y abierto. Si este trabajo te resulta útil, puedes ayudar a sostener su alojamiento y desarrollo: [Apoya este proyecto](https://buymeacoffee.com/legalizedev).

## Licencia

- **Código del pipeline**: MIT (https://github.com/legalize-dev/legalize-pipeline)
- **Datos**: Dominio público (Ley Federal del Derecho de Autor, art. 14, fracción VIII)
