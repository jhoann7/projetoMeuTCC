# TCC: Modelo Preditivo de Risco de Desmatamento na Amazônia

Este repositório contém os dados e o processo de desenvolvimento do Trabalho de Conclusão de Curso em Sistemas de Informação, focado na criação de um modelo de Inteligência Artificial para prever áreas com alto risco de desmatamento em um município no estado do Pará.

## 1. Objetivo do Projeto

O objetivo principal deste trabalho é desenvolver um sistema de classificação que, utilizando dados geoespaciais públicos e técnicas de Machine Learning, seja capaz de identificar as áreas (grids de 1km²) com maior probabilidade de sofrerem desmatamento no ano subsequente, servindo como uma ferramenta de apoio para o monitoramento e fiscalização ambiental.

## 2. Fontes de Dados

Os dados utilizados neste projeto foram obtidos de fontes públicas e oficiais. A lista de dados brutos inclui:

* **Desmatamento Anual (PRODES):** Polígonos de desmatamento para a Amazônia Legal.
    * **Fonte:** Instituto Nacional de Pesquisas Espaciais (INPE) - Projeto TerraBrasilis.
    * **Link:** [terrabrasilis.dpi.inpe.br/downloads/](http://terrabrasilis.dpi.inpe.br/downloads/)

* **Limites dos Imóveis Rurais (CAR):** Perímetro das propriedades rurais cadastradas.
    * **Fonte:** Secretaria de Meio Ambiente e Sustentabilidade do Pará (SEMAS/PA).
    * **Link:** [www.semas.pa.gov.br/analisecar/geoprocessamento.php](https://www.semas.pa.gov.br/analisecar/geoprocessamento.php)

* **Malha Rodoviária:** Eixos das rodovias federais e estaduais.
    * **Fonte:** OpenStreetMap via GeoFabrik (Alternativa: DNIT).
    * **Link:** [download.geofabrik.de/south-america/brazil.html](http://download.geofabrik.de/south-america/brazil.html)

* **Malha Municipal:** Limites político-administrativos dos municípios.
    * **Fonte:** Instituto Brasileiro de Geografia e Estatística (IBGE).
    * **Link:** [www.ibge.gov.br/geociencias/organizacao-do-territorio/malhas-territoriais](https://www.ibge.gov.br/geociencias/organizacao-do-territorio/malhas-territoriais)

* **Uso e Cobertura do Solo:** Classificação da paisagem (floresta, pastagem, etc.).
    * **Fonte:** MapBiomas Brasil.
    * **Link:** [mapbiomas.org/plataforma](https://mapbiomas.org/plataforma)

## 3. Metodologia: Pré-processamento de Dados Geoespaciais (QGIS)

Toda a preparação dos dados foi realizada no software QGIS. O fluxo de trabalho foi dividido em três etapas principais para garantir a consistência e a viabilidade da análise.

### Etapa 1: Padronização do Sistema de Coordenadas (CRS)

Para garantir a compatibilidade entre as diferentes fontes de dados e permitir cálculos métricos precisos (distância, área), todas as camadas vetoriais foram reprojetadas para um sistema de coordenadas único.

* **CRS Alvo:** `SIRGAS 2000 / UTM Zone 22S` (EPSG: 31982).
* **Processo:** Utilizou-se a ferramenta "Exportar -> Salvar Feições Como..." para cada camada, salvando uma nova versão com o SRC corrigido.
* **Arquivos Gerados:** `prodes_UTM.gpkg`, `car_UTM_corrigido.shp`, `rodovias_UTM.shp`.

### Etapa 2: Definição e Recorte da Área de Estudo

Com o objetivo de aprofundar a análise e garantir a viabilidade computacional, o escopo do projeto foi focado em um único município. Todos os dados padronizados na Etapa 1 foram recortados usando o limite deste município como molde.

* **Processo:** A ferramenta "Vetor -> Geoprocessamento -> Recortar (Clip)" foi utilizada.
* **Arquivos Gerados:**
    * `limite_estudo.shp`
    * `desmatamento_estudo.gpkg`
    * `car_estudo.shp`
    * `rodovias_estudo.shp`

### Etapa 3: Criação da Grade de Análise

Para transformar o problema em um formato adequado para Machine Learning, a área de estudo foi dividida em uma grade uniforme. Cada célula da grade representa uma unidade de observação.

* **Processo:** Utilizou-se a ferramenta "Vetor -> Investigar -> Criar grade".
* **Parâmetros:** Grade de retângulos (polígonos) com espaçamento de 1000x1000 metros (1km²).
* **Arquivo Gerado:** `grade_estudo_1km.shp`.

## 4. Próximos Passos

A próxima fase do projeto consiste na **Engenharia de Atributos**, onde a camada `grade_estudo_1km.shp` será enriquecida com as variáveis preditivas (distâncias, sobreposições, etc.) calculadas a partir dos dados preparados. Após essa etapa, a tabela de atributos final será exportada como um arquivo CSV para o treinamento do modelo de classificação em Python.

## 5. Estrutura de Arquivos Sugerida

```
TCC_REPOSITORIO/
|
|--- .gitignore
|--- README.md
|
|--- dados/
|    |--- limite_estudo.shp
|    |--- grade_estudo_1km.shp
|    |--- desmatamento_estudo.gpkg
|    |--- rodovias_estudo.shp
|    |--- car_estudo.shp  # (Se for pequeno, senão linkado no README)
|
|--- notebooks/
|    |--- 01_analise_exploratoria.ipynb
|    |--- 02_treinamento_modelo.ipynb
|
|--- relatorio_tcc/
     |--- tcc_final.pdf
```