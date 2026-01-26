# 🚕 Uber Pickups & Clima em NYC — Abril/2014

Este projeto realiza um **pipeline completo de ETL + análise exploratória (EDA)** usando dados de corridas da Uber em Nova York durante **abril de 2014**, integrando **dados geoespaciais** (SHP / GeoJSON) e **dados climáticos** para permitir análises temporais e espaciais mais ricas.

O foco principal é:

* Limpar e enriquecer dados brutos de corridas Uber usando **GeoPandas**
* Associar cada corrida a um **distrito (borough)** de NYC
* Gerar **datasets finais em CSV** prontos para análise estatística e séries temporais

---

## 🗺️ Visão Geral do Pipeline

O fluxo do projeto segue o diagrama apresentado:

* **Entradas**:

  * `nyc_uber_raw.csv` — dados brutos de corridas Uber (data/hora, latitude, longitude, base)
  * `nyc_border.shp` — fronteira oficial da cidade de Nova York
  * `nyc_boroughs.geojson` — bairros e distritos de NYC
  * Open-Meteo API — dados climáticos horários

* **Processamento**:

  * ETL geoespacial com **GeoPandas**
  * ETL climático com **Requests + Pandas**

* **Saídas**:

  * `nyc_uber_boroughs.csv` — corridas Uber limpas e associadas a distritos
  * `nyc_uber_weather.csv` — séries temporais climáticas

* **Análises**:

  * EDA estatística
  * Análise temporal de corridas

---

## 📦 Dados Utilizados

### 🚕 Uber Pickups

* Período: **Abril de 2014**
* Resolução temporal: **horas e minutos**
* Campos principais:

  * `date/time` (datetime)
  * `lat`, `lon`
  * `base` (5 bases de veículos Uber)

### 🗺️ Dados Geoespaciais

* `nyc_border.shp`: usado para definir o limite oficial da cidade
* `nyc_boroughs.geojson`: contém múltiplos bairros, posteriormente agregados

### 🌦️ Clima

* Fonte: **Open-Meteo API**
* Variáveis típicas:

  * Temperatura
  * Precipitação
* Resolução: **horária**

---

## 🧹 ETL Geoespacial (GeoPandas)

### 1️⃣ Conversão para GeoDataFrame

Os dados brutos da Uber são convertidos em um `GeoDataFrame` usando coordenadas (`lat`, `lon`) no CRS **EPSG:4326**.

### 2️⃣ Remoção de corridas fora de NYC

* É realizado um **Spatial Join** entre:

  * `nyc_uber_raw` (pontos)
  * `nyc_border` (polígono da cidade)
* Corridas fora da fronteira oficial são descartadas
* Aproximadamente **2,35%** do dataset original é removido

### 3️⃣ Associação aos distritos (boroughs)

* O arquivo `nyc_boroughs.geojson` contém vários bairros menores
* É aplicado um `.dissolve(by="borough")` para formar os **5 distritos oficiais**:

  * Manhattan
  * Brooklyn
  * Queens
  * Bronx
  * Staten Island
* Um novo **Spatial Join** associa cada corrida a um distrito
* Uma nova coluna `borough` é adicionada ao dataset

### 4️⃣ Exportação final

* Colunas geoespaciais (`geometry`) e intermediárias são removidas
* Dataset final salvo como:

```
nyc_uber_boroughs.csv
```

---

## 🌦️ ETL Climático

* Dados climáticos são coletados via **Open-Meteo API**
* Processamento feito com **Requests + Pandas**
* Os dados são organizados por:

  * Data
  * Hora
* Resultado salvo como:

```
nyc_uber_weather.csv
```

Esse arquivo é compatível com análises de **séries temporais** e pode ser facilmente combinado com agregações horárias das corridas Uber.

---

## 📊 Análise Exploratória (EDA)

A partir de `nyc_uber_boroughs.csv`, são realizadas diversas análises:

### 🔢 Contagens

* Número total de corridas:

  * Por **borough**
  * Por **base**
  * Por **dia** ao longo do mês
* Contagens combinadas:

  * `borough + base`

### 📅 Análises Temporais

* Contagem de corridas:

  * Por dia
  * Por intervalos de tempo (horas)
* Identificação de padrões diários e semanais

### 📈 Estatísticas Agregadas

* **Média diária de corridas**:

  * Por distrito
  * Por base de veículo
* Cálculo de métricas como média e desvio padrão

---

## 📉 Séries Temporais

Os dados de corridas agregados no tempo podem ser combinados com `nyc_uber_weather.csv` para:

* Avaliar correlações entre:

  * Volume de corridas
  * Temperatura
  * Precipitação
* Investigar efeitos climáticos na demanda por corridas

---

## 🛠️ Tecnologias Utilizadas

* **Python**
* **Pandas**
* **GeoPandas**
* **Shapely**
* **Matplotlib / Seaborn**
* **Requests**
* **Jupyter Notebook (.ipynb)**

---

## 📁 Estrutura de Arquivos (simplificada)

```
├── data/
│   ├── nyc_uber_raw.csv
│   ├── nyc_uber_boroughs.csv
│   ├── nyc_uber_weather.csv
│   ├── nyc_border.shp
│   └── nyc_boroughs.geojson
├── Uber_NYC_Weather.ipynb
├── README.md
```

---

## ✅ Resultados

O projeto entrega um **dataset limpo, enriquecido e pronto para análise**, conectando informações espaciais, temporais e climáticas. Ele serve como base sólida para:

* Análises urbanas
* Estudos de mobilidade
* Modelagem estatística
* Séries temporais

---

📌 *Projeto focado em ETL geoespacial e análise exploratória aplicada a dados reais de mobilidade urbana e clima.*
