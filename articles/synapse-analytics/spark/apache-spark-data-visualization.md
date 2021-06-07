---
title: Visualizações
description: Use os blocos de anotações do Azure Synapse para visualizar seus dados
author: midesa
ms.author: midesa
ms.reviewer: euang
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 09/13/2020
ms.openlocfilehash: 7e57cdca1d212e6077d685d95a8f869c12e546a8
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2021
ms.locfileid: "105627941"
---
# <a name="visualize-data"></a>Visualizar dados
O Azure Synapse é um serviço de análise integrado que acelera o tempo de insight, entre data warehouses e sistemas de análise de Big Data. A visualização de dados é um componente fundamental na capacidade de obter informações sobre seus dados. Ele ajuda a tornar os dados grandes e pequenos mais fáceis para os seres humanos entenderem. Ele também facilita a detecção de padrões, tendências e exceções em grupos de dados. 

Ao usar Apache Spark no Azure Synapse Analytics, há várias opções internas para ajudá-lo a visualizar seus dados, incluindo opções de gráfico do notebook Synapse, acesso a bibliotecas populares de software livre e integração com o Synapse SQL e o Power BI.

## <a name="notebook-chart-options"></a>Opções de gráfico do notebook
Ao usar um notebook Synapse do Azure, você pode transformar sua exibição de resultados tabulares em um gráfico personalizado usando opções de gráfico. Aqui, você pode visualizar seus dados sem precisar escrever nenhum código. 

### <a name="displaydf-function"></a>função display (DF)
A ```display``` função permite que você ative consultas SQL e Apache Spark quadros de dados e RDDs em visualizações de Rich Data. A ```display``` função pode ser usada em dataframes ou RDDs criado em PySpark, escala, Java e .net.

Para acessar as opções de gráfico:
1. ```%%sql```Por padrão, a saída dos comandos mágicos é exibida na exibição da tabela renderizada. Você também pode chamar ```display(df)``` o Spark Dataframes ou a função RDD (redistribuída de conjuntos de tabelas resilientes) para produzir a exibição de tabela renderizada. 
   
2. Quando você tiver uma exibição de tabela renderizada, alterne para a exibição de gráfico.
   ![gráficos internos](./media/apache-spark-development-using-notebooks/synapse-built-in-charts.png#lightbox)

3. Agora você pode personalizar sua visualização especificando os seguintes valores:

   | Configuração | Descrição |
   |--|--| 
   | Tipo de Gráfico | A ```display``` função dá suporte a uma ampla variedade de tipos de gráfico, incluindo gráficos de barras, dispersão, gráficos de linhas e muito mais |
   | Chave | Especificar o intervalo de valores para o eixo x|
   | Valor | Especificar o intervalo de valores para os valores do eixo y |
   | Grupo de Série | Usado para determinar os grupos para a agregação | 
   | Agregação | Método para agregar dados em sua visualização| 
   
   
    > [!NOTE]
    > Por padrão, a ```display(df)``` função usará apenas as primeiras 1000 linhas dos dados para renderizar os gráficos. Verifique a **agregação em todos os resultados** e clique no botão **aplicar** , você aplicará a geração de gráfico do conjunto de um inteiro. Um trabalho do Spark será disparado quando a configuração do gráfico for alterada. Observe que pode levar vários minutos para concluir o cálculo e renderizar o gráfico.
   
4. Depois de terminar, você pode exibir e interagir com sua visualização final!

### <a name="displaydf-statistic-details"></a>Exibir detalhes da estatística (DF)
Você pode usar <code>display(df, summary = true)</code> para verificar o resumo de estatísticas de um determinado Apache Spark dataframe que inclui o nome da coluna, tipo de coluna, valores exclusivos e valores ausentes para cada coluna. Você também pode selecionar uma coluna específica para ver seu valor mínimo, valor máximo, valor médio e desvio padrão.
    ![Resumo de gráficos internos](./media/apache-spark-development-using-notebooks/synapse-built-in-charts-summary.png#lightbox)
   
### <a name="displayhtmldf-option"></a>opção displayHTML (DF)
Os blocos de anotações do Azure Synapse Analytics dão suporte a gráficos HTML usando a ```displayHTML``` função.

A imagem a seguir é um exemplo de criação de visualizações usando [D3.js](https://d3js.org/).

   ![D3-js-exemplo](./media/apache-spark-data-viz/d3-boxplot.png#lightbox)

Execute o código a seguir para criar a visualização acima.

```python
displayHTML("""<!DOCTYPE html>
<meta charset="utf-8">

<!-- Load d3.js -->
<script src="https://d3js.org/d3.v4.js"></script>

<!-- Create a div where the graph will take place -->
<div id="my_dataviz"></div>
<script>

// set the dimensions and margins of the graph
var margin = {top: 10, right: 30, bottom: 30, left: 40},
  width = 400 - margin.left - margin.right,
  height = 400 - margin.top - margin.bottom;

// append the svg object to the body of the page
var svg = d3.select("#my_dataviz")
.append("svg")
  .attr("width", width + margin.left + margin.right)
  .attr("height", height + margin.top + margin.bottom)
.append("g")
  .attr("transform",
        "translate(" + margin.left + "," + margin.top + ")");

// Create Data
var data = [12,19,11,13,12,22,13,4,15,16,18,19,20,12,11,9]

// Compute summary statistics used for the box:
var data_sorted = data.sort(d3.ascending)
var q1 = d3.quantile(data_sorted, .25)
var median = d3.quantile(data_sorted, .5)
var q3 = d3.quantile(data_sorted, .75)
var interQuantileRange = q3 - q1
var min = q1 - 1.5 * interQuantileRange
var max = q1 + 1.5 * interQuantileRange

// Show the Y scale
var y = d3.scaleLinear()
  .domain([0,24])
  .range([height, 0]);
svg.call(d3.axisLeft(y))

// a few features for the box
var center = 200
var width = 100

// Show the main vertical line
svg
.append("line")
  .attr("x1", center)
  .attr("x2", center)
  .attr("y1", y(min) )
  .attr("y2", y(max) )
  .attr("stroke", "black")

// Show the box
svg
.append("rect")
  .attr("x", center - width/2)
  .attr("y", y(q3) )
  .attr("height", (y(q1)-y(q3)) )
  .attr("width", width )
  .attr("stroke", "black")
  .style("fill", "#69b3a2")

// show median, min and max horizontal lines
svg
.selectAll("toto")
.data([min, median, max])
.enter()
.append("line")
  .attr("x1", center-width/2)
  .attr("x2", center+width/2)
  .attr("y1", function(d){ return(y(d))} )
  .attr("y2", function(d){ return(y(d))} )
  .attr("stroke", "black")
</script>

"""
)

```

## <a name="popular-libraries"></a>Bibliotecas populares
Quando se trata de visualização de dados, o Python oferece várias bibliotecas de gráficos que vêm incluídas com muitos recursos diferentes. Por padrão, cada pool de Apache Spark no Azure Synapse Analytics contém um conjunto de bibliotecas de software livre organizadas e populares. Você também pode adicionar ou gerenciar bibliotecas adicionais & versões usando os recursos de gerenciamento de biblioteca do Azure Synapse Analytics. 

### <a name="matplotlib"></a>Matplotlib
Você pode renderizar bibliotecas de plotagem padrão, como matplotlib, usando as funções de renderização internas para cada biblioteca.

A imagem a seguir é um exemplo de criação de um gráfico de barras usando **matplotlib**.
   ![Exemplo de grafo de linha.](./media/apache-spark-data-viz/matplotlib-example.png#lightbox)

Execute o código de exemplo a seguir para desenhar a imagem acima.

```python
# Bar chart

import matplotlib.pyplot as plt

x1 = [1, 3, 4, 5, 6, 7, 9]
y1 = [4, 7, 2, 4, 7, 8, 3]

x2 = [2, 4, 6, 8, 10]
y2 = [5, 6, 2, 6, 2]

plt.bar(x1, y1, label="Blue Bar", color='b')
plt.bar(x2, y2, label="Green Bar", color='g')
plt.plot()

plt.xlabel("bar number")
plt.ylabel("bar height")
plt.title("Bar Chart Example")
plt.legend()
plt.show()
```


### <a name="bokeh"></a>Bokeh
Você pode renderizar bibliotecas HTML ou interativas, como **bokeh**, usando o ```displayHTML(df)``` . 

A imagem a seguir é um exemplo de como plotar glifos em um mapa usando **bokeh**.

   ![bokeh-example](./media/apache-spark-development-using-notebooks/synapse-bokeh-image.png#lightbox)

Execute o código de exemplo a seguir para desenhar a imagem acima.

```python
from bokeh.plotting import figure, output_file
from bokeh.tile_providers import get_provider, Vendors
from bokeh.embed import file_html
from bokeh.resources import CDN
from bokeh.models import ColumnDataSource

tile_provider = get_provider(Vendors.CARTODBPOSITRON)

# range bounds supplied in web mercator coordinates
p = figure(x_range=(-9000000,-8000000), y_range=(4000000,5000000),
           x_axis_type="mercator", y_axis_type="mercator")
p.add_tile(tile_provider)

# plot datapoints on the map
source = ColumnDataSource(
    data=dict(x=[ -8800000, -8500000 , -8800000],
              y=[4200000, 4500000, 4900000])
)

p.circle(x="x", y="y", size=15, fill_color="blue", fill_alpha=0.8, source=source)

# create an html document that embeds the Bokeh plot
html = file_html(p, CDN, "my plot1")

# display this html
displayHTML(html)
```


### <a name="plotly"></a>Plotly
Você pode renderizar bibliotecas HTML ou interativas como **plotais**, usando **displayHTML ()**.

Execute o seguinte código de exemplo para desenhar a imagem abaixo.

   ![gráfico de exemplo](./media/apache-spark-development-using-notebooks/synapse-plotly-image.png#lightbox)


```python
from urllib.request import urlopen
import json
with urlopen('https://raw.githubusercontent.com/plotly/datasets/master/geojson-counties-fips.json') as response:
    counties = json.load(response)

import pandas as pd
df = pd.read_csv("https://raw.githubusercontent.com/plotly/datasets/master/fips-unemp-16.csv",
                   dtype={"fips": str})

import plotly.express as px

fig = px.choropleth(df, geojson=counties, locations='fips', color='unemp',
                           color_continuous_scale="Viridis",
                           range_color=(0, 12),
                           scope="usa",
                           labels={'unemp':'unemployment rate'}
                          )
fig.update_layout(margin={"r":0,"t":0,"l":0,"b":0})

# create an html document that embeds the Plotly plot
h = plotly.offline.plot(fig, output_type='div')

# display this html
displayHTML(h)
```


### <a name="additional-libraries"></a>Bibliotecas adicionais 
Além dessas bibliotecas, o tempo de execução do Azure Synapse Analytics também inclui o seguinte conjunto de bibliotecas que geralmente são usadas para visualização de dados:
- [Matplotlib](https://matplotlib.org/)
- [Bokeh](https://bokeh.org/)
- [Seaborn](https://seaborn.pydata.org/) 
- [Plotly](https://plotly.com/)

Você pode visitar a [documentação](./spark/../apache-spark-version-support.md) de tempo de execução do Azure Synapse Analytics para obter as informações mais atualizadas sobre as bibliotecas e versões disponíveis.

## <a name="connect-to-power-bi-using-apache-spark--sql-on-demand"></a>Conectar-se a Power BI usando Apache Spark & SQL sob demanda
O Azure Synapse Analytics se integra profundamente com Power BI permitindo que os engenheiros de dados criem soluções de análise.

O Azure Synapse Analytics permite que os diferentes mecanismos computacionais do espaço de trabalho compartilhem bancos de dados e tabelas entre seus pools do Spark e o pool SQL sem servidor. Usando o [modelo de metadados compartilhado](../metadata/overview.md), você pode consultar suas tabelas de Apache Spark usando o SQL sob demanda. Depois de concluído, você pode conectar seu ponto de extremidade do SQL sob demanda para Power BI para consultar facilmente suas tabelas do Spark sincronizadas.


## <a name="next-steps"></a>Próximas etapas
- Para obter mais informações sobre como configurar o conector do SQL DW do Spark: [conector do SQL Synapse](./spark/../synapse-spark-sql-pool-import-export.md)
- Exibir as bibliotecas padrão: [tempo de execução do Azure Synapse Analytics](../spark/apache-spark-version-support.md)