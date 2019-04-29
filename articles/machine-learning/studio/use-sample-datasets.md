---
title: Usar os exemplos de conjuntos de dados
titleSuffix: Azure Machine Learning Studio
description: Descrições dos conjuntos de dados usados em modelos de exemplo incluídos no Machine Learning Studio. É possível usar esses conjuntos de dados de exemplo para seus testes.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 01/19/2018
ms.openlocfilehash: f86ae4977621927a09d9b83287a00dfa3bc17196
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60736608"
---
# <a name="use-the-sample-datasets-in-azure-machine-learning-studio"></a>Usar os conjuntos de dados de amostra no Azure Machine Learning Studio
[top]: #machine-learning-sample-datasets

Quando você cria um novo espaço de trabalho no Azure Machine Learning Studio, um número de conjuntos de dados de exemplo e testes é incluído por padrão. Muitos desses conjuntos de dados de exemplo são usados pelos modelos de exemplo da [Galeria de IA do Azure](https://gallery.azure.ai/). Outros são incluídos como exemplos de vários tipos de dados usados no aprendizado de máquina.

Alguns desses conjuntos de dados estão disponíveis no armazenamento de Blobs do Azure. A tabela a seguir fornece um link direto para esses conjuntos de dados. É possível utilizar esses conjuntos de dados em seus testes usando o módulo [Importar Dados][import-data].

O restante desses conjuntos de dados de exemplo está disponível no workspace em **Conjuntos de dados salvos**. Você pode encontrá-lo na paleta de módulo à esquerda da tela de experimento no Machine Learning Studio.
Você pode usar qualquer um desses conjuntos de dados em seu próprio teste arrastando-o para a tela do teste.

## <a name="datasets"></a>Conjunto de dados

<table>

<tr>
  <th>Nome do conjunto de dados</th>
  <th>Descrição do conjunto de dados</th>
</tr>

<tr>
  <td>Conjunto de dados de classificação binária de receita no recenseamento adulto</td>
  <td>
Um subconjunto do banco de dados do recenseamento de 1994, usando adultos em fase de trabalho, com idade acima de 16 anos com um índice de receita ajustado de > 100.
<p></p>
<b>Uso:</b> classificar pessoas usando dados demográficos para prever se uma pessoa recebe acima de 50 mil por ano.
<p></p>
<b>Pesquisa Relacionada:</b> Kohavi, R., Becker, B., (1996). UCI Machine Learning Repository <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>. Irvine, CA: University of California, School of Information and Computer Science </td>
</tr>

<tr>
  <td>Conjunto de dados de códigos do aeroporto</td>
  <td>
Códigos de aeroporto dos EUA.
<p></p>
Este conjunto de dados contém uma linha para cada aeroporto dos EUA, fornecendo o número de ID do aeroporto e o nome junto com a cidade do local e estado.
  </td>
</tr>

<tr>
  <td>Dados de preço de automóvel (Brutos)</td>
  <td>
Informações sobre automóveis por marca e modelo, incluindo o preço, recursos como número de cilindradas e MPG, bem como uma pontuação de risco de seguro.
<p></p>
A pontuação de risco é inicialmente associada com preço automático. Depois ela é ajustada ao risco real em um processo conhecido pelos atuários como valor simbólico. Um valor de +3 indica que o automóvel apresenta risco e um valor de -3 indica que ele provavelmente é seguro.
<p></p>
<b>Uso:</b> prever a pontuação de risco por recursos, usando a regressão ou a classificação multivariada. 
<p></p>
<b>Pesquisa Relacionada:</b> Schlimmer, J.C. (1987). UCI Machine Learning Repository <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>. Irvine, CA: University of California, School of Information and Computer Science </td>
</tr>

<tr>
  <td>Conjunto de dados UCI de locação de bicicletas</td>
  <td>
Conjunto de dados UCI Bike Rental que é baseado em dados reais da empresa Capital Bikeshare que mantém uma rede de aluguel de bicicletas em Washington DC.
<p></p>
O conjunto de dados possui uma linha por cada hora de cada dia em 2011 e 2012, no total de 17.379 linhas. O intervalo de aluguéis de bicicletas por hora é de 1 a 977.

  </td>
</tr>

<tr>
  <td>Imagem RGB de Bil Gates</td>
  <td>
Arquivo de imagem publicamente disponível convertido para dados CSV.
<p></p>
O código para converter a imagem é fornecido na página de detalhes do modelo <strong>Quantização de cores usando o clustering K-Means</strong>.
  </td>
</tr>

<tr>
  <td>Dados de doação de sangue</td>
  <td>
Um subconjunto de dados do banco de dados de doadores de sangue do Centro de Serviços de Transfusão de Sangue da Cidade de Hsin-Chu, Taiwan.
<p></p>
Os dados do doador incluem os meses desde a última doação e a frequência, ou o número total de doações, data da última doação e a quantidade de sangue doado.
<p></p>
<b>Uso:</b> o objetivo é prever, por meio de classificação, se o doador doou sangue em março de 2007, em que 1 indica um doador durante o período de meta e 0, um não doador. 
<p></p>
<b>Pesquisa Relacionada:</b> Yeh, I.C., (2008). UCI Machine Learning Repository <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>. Irvine, CA: University of California, School of Information and Computer Science
<p></p>
Yeh, I-Cheng, Yang, King-Jang, e Ting, Tao-Ming, "Knowledge discovery on RFM model using Bernoulli sequence, "Expert Systems with Applications, 2008, <a href="https://dx.doi.org/10.1016/j.eswa.2008.07.018">https://dx.doi.org/10.1016/j.eswa.2008.07.018</a>
  </td>
</tr>

<tr>
  <td>Dados de câncer de mama</td>
  <td>
Um dos três conjuntos de dados relacionados a câncer fornecidos pelo Instituto de Oncologia que aparece frequentemente na literatura de aprendizado de máquina. Ele combina informações de diagnóstico com recursos de análise de laboratório de aproximadamente 300 amostras de tecido.
<p></p>
<b>Uso:</b> classificar o tipo de câncer, com base em 9 atributos, alguns dos quais são lineares e outros, categóricos. 
<p></p>
<b>Pesquisa Relacionada:</b> Wohlberg, W.H., Street, W.N., & Mangasarian, O.L. (1995). UCI Machine Learning Repository <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>. Irvine, CA: University of California, School of Information and Computer Science </td>
</tr>

<tr>
  <td>Recursos de câncer de mama <td>
O conjunto de dados contém informações de 102 mil regiões suspeitas (candidatas) de imagens de raios-X, cada uma descrita por 117 recursos. Os recursos são proprietários e seu significado não será revelado por criadores de conjunto de dados (Siemens Healthcare). 
  </td>
</tr>

<tr>
  <td>Informações de Câncer de Mama</td>
  <td>
O conjunto de dados contém informações adicionais sobre cada região suspeita da imagem de raio-X. Cada exemplo fornece informações (por exemplo, rótulo, ID de paciente, coordenadas de patch em relação à imagem inteira) sobre o número da linha correspondente no conjunto de dados de recursos de câncer de mama. Cada paciente tem um número de exemplos. Para pacientes que têm um câncer, alguns exemplos são positivos e outros negativos. Para pacientes que não têm câncer, todos os exemplos são negativos. O conjunto de dados tem 102 mil exemplos. O conjunto de dados é tendencioso, 0,6% dos pontos são positivos e o restante é negativo. O conjunto de dados foi disponibilizado pela Siemens Healthcare.
  </td>
</tr>

<tr>
  <td>Rótulos de apetência CRM compartilhados</td>
  <td>
Rótulos do desafio de previsão de relacionamento com o cliente KDD Cup 2009 (<a href="http://www.sigkdd.org/site/2009/files/orange_small_train_appetency.labels">orange_small_train_appetency.labels</a>).
  </td>
</tr>

<tr>
  <td>Rótulos de variação CRM compartilhados</td>
  <td>
Rótulos do desafio de previsão de relacionamento com o cliente KDD Cup 2009 (<a href="http://www.sigkdd.org/site/2009/files/orange_small_train_churn.labels">orange_small_train_churn.labels</a>).
  </td>
</tr>

<tr>
  <td>Conjunto de dados CRM compartilhado</td>
  <td>
Estes dados foram obtidos do desafio de previsão de relacionamento com o cliente KDD Cup 2009 (<a href="http://www.sigkdd.org/site/2009/files/orange_small_train.data.zip">orange_small_train.data.zip</a>).
<p></p>
Esse conjunto de dados contém 50 mil clientes da empresa francesa de telecomunicações Orange. Cada cliente possui 230 recursos anônimos, dos quais 190 são numéricos e 40 categóricos. Os recursos são muito esparsos.
  </td>
</tr>

<tr>
  <td>Rótulos de vendas agregadas CRM compartilhados</td>
  <td>
Rótulos do desafio de previsão de relacionamento do cliente KDD Cup 2009 (<a href="http://www.sigkdd.org/site/2009/files/orange_large_train_upselling.labels">orange_large_train_upselling.labels</a>).
  </td>
</tr>

<tr>
  <td>Dados de regressão de eficiência de energia</td>
  <td>
Uma coleção de perfis de energia simulados, com base em 12 formatos de construções diferentes. Os edifícios são diferenciados por oito recursos. O que inclui uma área envidraçada, a distribuição de área envidraçada e orientação.
<p></p>
<b>Uso:</b> usar a regressão ou a classificação para prever a classificação de eficiência de energia com base em uma das duas respostas de valor real. Para classificação de multiclasse, a variável da resposta é arredondada para o número inteiro mais próximo. 
<p></p>
<b>Pesquisa Relacionada:</b> Xifara, A. & Tsanas, A. (2012). UCI Machine Learning Repository <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>. Irvine, CA: University of California, School of Information and Computer Science </td>
</tr>

<tr>
  <td>Dados de atrasos de voo</td>
  <td>
Dados de desempenho pontual do voo do passageiro obtidos da coleção de dados TranStats do Departamento de Transportes dos EUA (<a href="https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time">On-Time</a>).
<p></p>
O conjunto de dados abrange o período de abril a outubro de 2013. Antes de ser carregado no Azure Machine Learning Studio, o conjunto de dados foi processado da seguinte maneira:
<ul>
  <li>O conjunto de dados foi filtrado para cobrir os 70 aeroportos mais movimentados dos EUA continentais.</li>
  <li>Os voos cancelados foram rotulados como atrasados por mais de 15 minutos</li>
  <li>Voos desviados foram retirados.</li>
  <li>As seguintes colunas foram selecionadas: Year, Month, DayofMonth, DayOfWeek, Carrier, OriginAirportID, DestAirportID, CRSDepTime, DepDelay, DepDel15, CRSArrTime, ArrDelay, ArrDel15, Canceled</li>
</ul>
</td>
</tr>

<tr>
  <td>Desempenho pontual de voo (Bruto)</td>
  <td>
Registros de pousos e decolagens nos Estados Unidos desde outubro de 2011.
<p></p>
<b>Uso:</b> Prever atrasos nos voos. 
<p></p>
<b>Pesquisa Relacionada:</b> Do departamento de transportes dos EUA <a href="https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time">https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time</a>.
  </td>
</tr>

<tr>
  <td>Dados de incêndios florestais</td>
  <td>
Contém dados de clima, como índices de temperatura e umidade e velocidade do vento. Os dados foram extraídos de uma área do nordeste de Portugal, combinados com os registros de incêndios florestais.
<p></p>
<b>Uso:</b> essa é uma tarefa de regressão difícil, em que o objetivo é prever a área queimada de incêndios florestais. 
<p></p>
<b>Pesquisa Relacionada:</b> Cortez, P., & Morais, A. (2008). UCI Machine Learning Repository <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>. Irvine, CA: University of California, School of Information and Computer Science
<p></p>
[Cortez and Morais, 2007] P. Cortez and A. Morais. A Data Mining Approach to Predict Forest Fires using Meteorological Data. Em J. Neves, M. F. Santos e J. Machado Eds., New Trends in Artificial Intelligence, Proceedings of the 13th EPIA 2007 - Portuguese Conference on Artificial Intelligence, December, Guimarães, Portugal, pp. 512-523, 2007. APPIA, ISBN-13 978-989-95618-0-9. Disponível em: <a href="http://www.dsi.uminho.pt/~pcortez/fires.pdf">http://www.dsi.uminho.pt/~pcortez/fires.pdf</a>.
  </td>
</tr>

<tr>
  <td>Conjunto de dados do cartão de crédito alemão UCI</td>
  <td>
O conjunto de dados UCI Statlog (cartão de crédito alemão) (<a href="https://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)">Statlog+German+Credit+Data</a>), usando o arquivo german.data.
<p></p>
O conjunto de dados classifica pessoas, descritas como um conjunto de atributos, como baixos ou altos riscos de crédito. Cada exemplo representa uma pessoa. Há 20 recursos, ambos numéricos e categóricos, e um rótulo binário (o valor de risco de crédito). Entradas de risco de crédito alto têm o rótulo = 2, entradas de risco de crédito baixo têm o rótulo = 1. O custo de classificar incorretamente um exemplo de risco baixo como alto é 1, considerando que o custo de classificar incorretamente um exemplo de risco alto como baixo é 5.
  </td>
</tr>

<tr>
  <td>Títulos de filmes no IMDB</td>
  <td>
O conjunto de dados contém informações sobre filmes que foram classificados em tweets do Twitter: ID do filme no IMDB, nome do filme, gênero e ano de produção. Há 17 mil filmes no conjunto de dados. O conjunto de dados foi introduzido no artigo “S. Dooms, T. De Pessemier e L. Martens. MovieTweetings: um conjunto de dados de classificação de filmes coletado do Twitter. Oficina de crowdsourcing and computação humana para sistemas recomendados, CrowdRec em RecSys 2013."
  </td>
</tr>

<tr>
  <td>Dados da íris classe dois</td>
  <td>
Esse é provavelmente o banco de dados mais conhecido encontrado na literatura de reconhecimento padrão. O conjunto de dados é relativamente pequeno, contendo 50 exemplos de cada medida em pétalas das três variáveis da íris.
<p></p>
<b>Uso:</b> Prever o tipo de íris com base nas medições.  
<p></p>
<b>Pesquisa Relacionada:</b> Fisher, R.A. (1988). UCI Machine Learning Repository <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>. Irvine, CA: University of California, School of Information and Computer Science </td>
</tr>

<tr>
  <td>Tweets de Filmes</td>
  <td>
O conjunto de dados é uma versão estendida do conjunto de dados de Movie Tweetings. O conjunto de dados tem as classificações de 170 mil de filmes, extraídos de tweets bem estruturados no Twitter. Cada instância representa uma tweet e é uma tupla: ID de usuário, ID de filme no IMDB, classificação, carimbo de hora, número de Favoritos para este tweet e número de retweets desse tweet. O conjunto de dados foi disponibilizado por A. Said, S. Dooms, B. Loni e D. Tikk para desafio de sistemas de recomendação 2014.
  </td>
</tr>

<tr>
  <td>Dados MPG para vários automóveis</td>
  <td>
Esse conjunto de dados é uma versão um pouco modificada do conjunto de dados fornecido pela Biblioteca StatLib da Universidade de Carnegie Mellon. O conjunto de dados foi usado na exposição da Associação Estatística Americana de 1983.
<p></p>
Os dados listam o consumo de combustível para vários automóveis em milhas por galão. Também incluem informações como o número de cilindros, cilindradas, potência, peso total e aceleração.
<p></p>
<b>Uso:</b> prever a economia de combustível com base em três atributos discretos de múltiplos valores e cinco atributos contínuos. 
<p></p>
<b>Pesquisa Relacionada:</b> StatLib, Carnegie Mellon University, (1993). UCI Machine Learning Repository <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>. Irvine, CA: University of California, School of Information and Computer Science </td>
</tr>

<tr>
  <td>Conjunto de dados de classificação binária de diabetes da população indiana de Pima</td>
  <td>
Um subconjunto de dados do Instituto Nacional de Diabetes e o banco de dados de Doenças Digestivas e Renais. O conjunto de dados foi filtrado para se concentrar nas pacientes mulheres com hereditariedade indiana de Pima. Os dados incluem registros médicos como níveis de glicose e insulina, bem como fatores do estilo de vida.
<p></p>
<b>Uso:</b> prever se o indivíduo tem diabetes (classificação binária). 
<p></p>
<b>Pesquisa Relacionada:</b> Sigillito, V. (1990). UCI Machine Learning Repository <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml"</a>. Irvine, CA: University of California, School of Information and Computer Science </td>
</tr>

<tr>
  <td>Dados de consumidores de restaurantes</td>
  <td>
Um conjunto de metadados sobre consumidores, incluindo demografia e preferências.
<p></p>
<b>Uso:</b> use esse conjunto de dados, em combinação com os outros dois conjuntos de dados de restaurantes, para treinar e testar um sistema de recomendação. 
<p></p>
<b>Pesquisa Relacionada:</b> Bache, K. e Lichman, M. (2013). UCI Machine Learning Repository <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>. Irvine, CA: University of California, School of Information and Computer Science.
  </td>
</tr>

<tr>
  <td>Dados de recurso de restaurante</td>
  <td>
Um conjunto de metadados sobre restaurantes e seus recursos, como tipo de comida, estilo de jantar e localização.
<p></p>
<b>Uso:</b> use esse conjunto de dados, em combinação com os outros dois conjuntos de dados de restaurantes, para treinar e testar um sistema de recomendação. 
<p></p>
<b>Pesquisa Relacionada:</b> Bache, K. e Lichman, M. (2013). UCI Machine Learning Repository <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>. Irvine, CA: University of California, School of Information and Computer Science.
  </td>
</tr>

<tr>
  <td>Classificação de restaurantes</td>
  <td>
Contém a classificação fornecida por usuários de restaurantes em uma escala de 0 a 2.
<p></p>
<b>Uso:</b> use esse conjunto de dados, em combinação com os outros dois conjuntos de dados de restaurantes, para treinar e testar um sistema de recomendação. 
<p></p>
<b>Pesquisa Relacionada:</b> Bache, K. e Lichman, M. (2013). UCI Machine Learning Repository <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>. Irvine, CA: University of California, School of Information and Computer Science.
  </td>
</tr>

<tr>
  <td>Conjunto de dados multiclasses de recozimento de aço</td>
  <td>
Esse conjunto de dados contém uma série de registros de avaliações de recozimento de aço. Ele contém os atributos físicos (largura, espessura, tipos – bobina, planilha, etc.) dos tipos de aço resultantes.
<p></p>
<b>Uso:</b> prever um dos dois atributos de classe numérica: resistência ou força. Você também pode analisar correlações entre os atributos.
<p></p>
Os graus de aço seguem um padrão definido pela SAE e outras organizações. Você está procurando um “grau” específico (a variável de classe) e deseja entender os valores necessários. 
<p></p>
<b>Pesquisa Relacionada:</b> Sterling, D. & Buntine, W. (NA). UCI Machine Learning Repository <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>. Irvine, CA: University of California, School of Information and Computer Science
<p></p>
Um guia útil para graus de aço pode ser encontrado aqui: <a href="https://otk-sitecore-prod-v2-cdn.azureedge.net/-/media/from-sharepoint/documents/product/outokumpu-steel-grades-properties-global-standards.pdf">https://otk-sitecore-prod-v2-cdn.azureedge.net/-/media/from-sharepoint/documents/product/outokumpu-steel-grades-properties-global-standards.pdf</a>
  </td>
</tr>

<tr>
  <td>Dados de telescópio</td>
  <td>
Registro de explosões de partículas gama de alta energia com ruídos de fundo, ambos simulados usando o processo de Monte Carlo.
<p></p>
A intenção da simulação foi melhorar a precisão de telescópios atmosféricos Cherenkov de gama em terra. Isso é feito usando métodos estatísticos para diferenciar entre o sinal desejado (expositores de radiação de Cherenkov) e o ruído de fundo (expositores hadrônicos iniciados por raios cósmicos na atmosfera superior).
<p></p>
Os dados foram pré-processados para criar um conjunto elíptico com o eixo longo direcionado para o centro da câmera. As características dessa elipse, geralmente chamada de parâmetros de Hillas, estão entre os parâmetros de imagem que podem ser usados para discriminação.
<p></p>
<b>Uso:</b> prever se a imagem de um chuveiro representa ruído de fundo ou sinal.
<p></p>
<b>Observações:</b> a precisão da classificação simples não é significativa para esses dados, já que classificar um evento de fundo como sinal é pior do que classificar um evento de sinal como de fundo. Para obter uma comparação dos diferentes classificadores, deve ser usado o gráfico ROC. A probabilidade de aceitar um evento de fundo como sinal deve estar abaixo de um destes limites: 0,01, 0,02, 0,05, 0,1 ou 0,2.
<p></p>
Além disso, observe que o número de eventos em segundo plano (h, para expositores hadrônicos) é subestimado. Em medidas reais, a classe h ou de ruído representa a maioria dos eventos. 
<p></p>
<b>Pesquisa Relacionada:</b> Bock, R.K. (1995). UCI Machine Learning Repository <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>. Irvine, CA: University of California, School of Information </td>
</tr>

<tr>
  <td>Conjunto de dados de clima</td>
  <td>
Observações climáticas terrestres de hora em hora da NOAA (<a href="https://az754797.vo.msecnd.net/data/WeatherDataset.csv">dados mesclados de 201304 para 201310</a>).
<p></p>
Os dados de clima abrangem observações feitas de estações meteorológica de aeroportos, que abrangem o período de abril a outubro de 2013. Antes de ser carregado no Azure Machine Learning Studio, o conjunto de dados foi processado da seguinte maneira:
<ul>
  <li>As IDs da estação meteorológica foram mapeadas para as IDs do aeroporto correspondentes.</li>
  <li>As estações meteorológicas não associadas aos 70 aeroportos mais movimentados foram filtradas e retiradas</li>
  <li>A coluna de Data foi dividida em colunas separadas para Ano, Mês e Dia</li>
  <li>As seguintes colunas foram selecionadas: AirportID, Year, Month, Day, Time, TimeZone, SkyCondition, Visibility, WeatherType, DryBulbFarenheit, DryBulbCelsius, WetBulbFarenheit, WetBulbCelsius, DewPointFarenheit, DewPointCelsius, RelativeHumidity, WindSpeed, WindDirection, ValueForWindCharacter, StationPressure, PressureTendency, PressureChange, SeaLevelPressure, RecordType, HourlyPrecip, Altimeter</li>
</ul>
  </td>
</tr>

<tr>
  <td>Conjunto de dados da SP 500 da Wikipédia</td>
  <td>
Os dados foram extraídos do Wikipedia (<a href="https://www.wikipedia.org/">https://www.wikipedia.org/</a>), com base em artigos de cada empresa S&P 500, armazenados como dados XML.
<p></p>
Antes de ser carregado no Azure Machine Learning Studio, o conjunto de dados foi processado da seguinte maneira:
<ul>
  <li>Extraia o conteúdo do texto para cada empresa específica</li>
  <li>Remova a formatação wiki</li>
  <li>Remova caracteres não alfanuméricos</li>
  <li>Converta todo o texto para minúsculas</li>
  <li>Categorias de empresas conhecidas foram adicionadas</li>
</ul>
<p></p>
Observe que, para algumas empresas, um artigo não foi encontrado, assim, o número de registros é menor que 500.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/direct_marketing.csv">direct_marketing.csv</a></td>
  <td>
O conjunto de dados contém os dados do cliente e indicações sobre sua resposta a uma campanha de mala direta. Cada linha representa um cliente. O conjunto de dados contém nove recursos sobre dados demográficos do usuário e comportamento passado e três colunas de rótulo (visita, conversão e gasto).  Visita é uma coluna binária que indica que um cliente visitou depois da campanha de marketing. Conversão indica que um cliente comprou algo. Gasto é o valor que foi gasto.  O conjunto de dados foi disponibilizado por Kevin Hillstrom para análises de email MineThatData e desafio de Data Mining.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/lyrl2004_tokens_test.csv">lyrl2004_tokens_test.csv</a></td>
  <td>
Recursos de exemplos de teste no conjunto de dados de notícias Reuters RCV1-V2. O conjunto de dados tem 781 mil artigos de notícias juntamente com suas IDs (primeira coluna do conjunto de dados). Cada artigo é marcado, recebe stopwords e é interrompido. O conjunto de dados foi disponibilizado por David. D. Lewis.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/lyrl2004_tokens_train.csv">lyrl2004_tokens_train.csv</a></td>
  <td>
Recursos de exemplos de treinamento no conjunto de dados de notícias Reuters RCV1-V2. O conjunto de dados tem 23 mil artigos de notícias juntamente com suas IDs (primeira coluna do conjunto de dados). Cada artigo é marcado, recebe stopwords e é interrompido. O conjunto de dados foi disponibilizado por David. D. Lewis.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/network_intrusion_detection.csv">network_intrusion_detection.csv</a><br></td>
  <td>
Conjunto de dados do Concurso de Ferramentas de Mineração de Dados e Descoberta de Conhecimento KDD Cup 1999 (<a href="https://kdd.ics.uci.edu/databases/kddcup99/kddcup99.html">kddcup99.html</a>).
<p></p>
O conjunto de dados foi baixado e armazenado no Armazenamento de Blobs do Azure (<a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/network_intrusion_detection.csv">network_intrusion_detection.csv</a>) e inclui os conjuntos de dados de treinamento e teste. O conjunto de dados de treinamento possui aproximadamente 126K de linhas e 43 colunas, incluindo os rótulos. Três colunas são parte das informações de rótulo e 40 colunas, consistindo de recursos numéricos e de cadeia/categóricos, estão disponíveis para o treinamento do modelo. Os dados de teste possuem aproximadamente 22,5 mil exemplos de teste com as mesmas 43 colunas que nos dados de treinamento.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/rcv1-v2.topics.qrels.csv">rcv1-v2.topics.qrels.csv</a></td>
  <td>
As atribuições de tópico para artigos de notícias no conjunto de dados de notícias Reuters RCV1 V2. Um artigo de notícias pode ser atribuído a vários tópicos. O formato de cada linha é "&lt;nome do tópico&gt; &lt;id do documento&gt; 1". O conjunto de dados contém 2,6 milhões de atribuições de tópico. O conjunto de dados foi disponibilizado por David. D. Lewis.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/student_performance.txt">student_performance.txt</a></td>
  <td>
Estes dados vêm do desafio de avaliação de desempenho de aluno do KDD Cup 2010 (<a href="https://www.kdd.org/kdd-cup/view/kdd-cup-2010-student-performance-evaluation">student performance evaluation</a>). Os dados usados são o conjunto de treinamento Algebra_2008_2009 (Stamper, J., Niculescu-Mizil, A., Ritter, S., Gordon, G.J., & Koedinger, K.R. (2010). Algebra I 2008-2009. Conjunto de dados de desafio do KDD Cup 2010 Educational Data Mining Challenge. Encontre-o em <a href="https://pslcdatashop.web.cmu.edu/KDDCup/downloads.jsp">downloads.jsp</a>.
<p></p>
O conjunto de dados foi baixado e armazenado no Armazenamento de Blobs do Azure (<a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/student_performance.txt">student_performance.txt</a>) e contém os arquivos de log de um sistema de aulas particulares para alunos. Os recursos fornecidos incluem a ID do problema e uma breve descrição dele, a ID do aluno, o carimbo de data/hora e quantas tentativas o aluno fez antes de solucionar o problema da maneira correta. O conjunto de dados original tem 8,9M registros; esse conjunto de dados foi reduzido para as primeiras 100 mil linhas. O conjunto de dados possui 23 colunas separadas por tabulação de vários tipos: numérico, categórico e carimbo de data/hora.
  </td>
</tr>

</table>

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Início rápido seus experimentos com exemplos](sample-experiments.md)

<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
