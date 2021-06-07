---
title: Detectar descompasso de dados em DataSets (visualização)
titleSuffix: Azure Machine Learning
description: Saiba como configurar a detecção de descompasso de dados no Azure Learning. Crie monitores de conjuntos de dados (versão prévia), monitore para descompasso de dado e configure alertas.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: sgilley
ms.author: copeters
author: lostmygithubaccount
ms.date: 06/25/2020
ms.topic: conceptual
ms.custom: how-to, data4ml, contperf-fy21q2
ms.openlocfilehash: 5a3d16445c5a4276f07f4ed502b9830a10c4ff72
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102518901"
---
# <a name="detect-data-drift-preview-on-datasets"></a>Detectar descompasso de dados (versão prévia) em conjuntos

Saiba como monitorar a descompasso de dados e definir alertas quando o descompasso estiver alto.  

Com os monitores Azure Machine Learning DataSet (versão prévia), você pode:
* **Analise a descompasso em seus dados** para entender como ele muda ao longo do tempo.
* **Monitore os dados do modelo** para obter diferenças entre o treinamento e o fornecimento de conjuntos.  Comece [coletando dados de modelo de modelos implantados](how-to-enable-data-collection.md).
* **Monitore novos dados** para obter diferenças entre qualquer linha de base e um DataSet de destino.
* **Recursos de perfil em dados** para controlar como as propriedades estatísticas são alteradas com o passar do tempo.
* **Configure alertas sobre descompasso de dados** para avisos antecipados a problemas potenciais. 
* **[Criar uma nova versão de conjunto de dados] (como-para-Version-Track-DataSets** ao determinar que os dados foram descompassos demais).

Um [conjunto de informações do Azure Machine Learning](how-to-create-register-datasets.md) é usado para criar o monitor. O DataSet deve incluir uma coluna timestamp.

Você pode exibir as métricas de descompasso de dados com o SDK do Python ou no Azure Machine Learning Studio.  Outras métricas e informações estão disponíveis por meio do recurso de [informações aplicativo Azure](../azure-monitor/app/app-insights-overview.md) associadas ao espaço de trabalho Azure Machine Learning.

> [!IMPORTANT]
> Atualmente, a detecção de descompasso de dados para DataSets está em visualização pública.
> A versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

Para criar e trabalhar com monitores de conjunto de trabalho, você precisa de:
* Uma assinatura do Azure. Caso não tenha uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente hoje mesmo a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree).
* Um [espaço de trabalho Azure Machine Learning](how-to-manage-workspace.md).
* O [SDK do Azure Machine Learning para Python instalado](/python/api/overview/azure/ml/install), que inclui o pacote de conjuntos de linhas do azureml.
* Dados estruturados (tabulares) com um carimbo de data/hora especificado no caminho do arquivo, nome do arquivo ou coluna nos dados.

## <a name="what-is-data-drift"></a>O que é descompasso de dados?

A descompasso de dados é uma das principais razões pelas quais a precisão do modelo diminui com o passar do tempo.  Para modelos de aprendizado de máquina, a descompasso de dados é a alteração nos dados de entrada do modelo que leva à degradação do desempenho do modelo.  O monitoramento de descompasso de dados ajuda a detectar esses problemas de desempenho do modelo.

As causas de descompasso de dados incluem:

- Alterações no processo de upstream, como um sensor sendo substituído que altera as unidades de medida de polegadas para centímetros. 
- Problemas de qualidade de dados, como um sensor quebrado sempre lendo 0.
- Descompasso natural nos dados, como a mudança de temperatura média com as estações.
- Alteração em relação entre recursos ou deslocamento de covariable. 

Azure Machine Learning simplifica a detecção de descompasso, computando uma única métrica, abstraindo a complexidade dos conjuntos de valores que estão sendo comparados.  Esses conjuntos de registros podem ter centenas de recursos e dezenas de milhares de linhas. Depois que o descompasso for detectado, você fará uma busca detalhada de quais recursos estão causando o descompasso.  Em seguida, inspecione as métricas de nível de recurso para depurar e isolar a causa raiz da descompasso.

Essa abordagem de cima para baixo facilita o monitoramento de dados em vez de técnicas tradicionais baseadas em regras. Técnicas baseadas em regras, como o intervalo de dados permitido ou valores exclusivos permitidos, podem ser demoradas e sujeitas a erros.

No Azure Machine Learning, você usa os monitores de conjunto de dados para detectar e alertar a descompasso do dado.
  
### <a name="dataset-monitors"></a>Monitores de DataSet 

Com um monitor de conjunto de um DataSet, você pode:

* Detecte e Alerte a descompasso de dados em novos dados em um conjunto.
* Analise os dados históricos para descompasso.
* Criar perfil de novos dados ao longo do tempo.

O algoritmo de descompasso de dados fornece uma medida geral de alteração nos dados e a indicação de quais recursos são responsáveis por investigações adicionais. Os monitores de conjunto de dados produzem várias outras métricas por meio da criação de perfil de novos dados no `timeseries` DataSet. 

Os alertas personalizados podem ser configurados em todas as métricas geradas pelo monitor por meio do [aplicativo Azure insights](../azure-monitor/app/app-insights-overview.md). Os monitores de conjunto de dados podem ser usados para detectar rapidamente problemas de dado e reduzir o tempo para depurar o problema identificando as causas prováveis.  

Conceitualmente, há três cenários principais para configurar monitores de conjuntos de conjunto de Azure Machine Learning.

Cenário | Descrição
---|---
Monitorar os dados de serviço de um modelo para descompasso dos dados de treinamento | Os resultados desse cenário podem ser interpretados como monitoramento de um proxy para a precisão do modelo, uma vez que a precisão do modelo é prejudicada quando o fornecimento de dados se descompasso dos dados de treinamento.
Monitore um conjunto de uma série temporal para descompasso de um período de tempo anterior. | Esse cenário é mais geral e pode ser usado para monitorar conjuntos de linhas envolvidos no upstream ou no downstream da construção do modelo.  O DataSet de destino deve ter uma coluna de carimbo de data/hora. O conjunto de linhas da linha de base pode ser qualquer conjunto de um DataSet que tenha recursos em comum com o DataSet de destino.
Execute a análise nos dados anteriores. | Esse cenário pode ser usado para entender dados históricos e informar decisões em configurações para monitores de conjuntos de dados.

Os monitores de conjunto de conjuntos dependem dos seguintes serviços do Azure.

|Serviço do Azure  |Descrição  |
|---------|---------|
| *Conjunto de dados* | Os descompassos usam conjuntos de dados Machine Learning para recuperar e comparar dados de treinamento de modelo.  A geração do perfil de dados é usada para gerar algumas das métricas relatadas, como min, Max, valores distintos, contagem de valores distintos. |
| *Pipeline e computação do Azureml* | O trabalho de cálculo de descompasso é hospedado no pipeline do azureml.  O trabalho é disparado sob demanda ou por agendamento para ser executado em uma computação configurada no momento da criação do monitor de descompasso.
| *Application Insights*| A descompasso emite métricas para Application Insights pertencentes ao espaço de trabalho do Machine Learning.
| *Armazenamento de Blobs do Azure*| A descompasso emite métricas no formato JSON para o armazenamento de BLOBs do Azure.

### <a name="baseline-and-target-datasets"></a>Conjuntos de linha de base e destino 

Você monitora os conjuntos de dados do [Azure Machine Learning](how-to-create-register-datasets.md) para descompasso. Ao criar um monitor de conjunto de um DataSet, você fará referência a:
* Conjunto de linhas de linha de base – geralmente o conjunto de os de treinamento para um modelo.
* Conjunto de dados de destino-geralmente, o modelo é dado de entrada-é comparado ao longo do tempo para seu conjunto de informações Essa comparação significa que o DataSet de destino deve ter uma coluna de carimbo de data/hora especificada.

O monitor comparará os conjuntos de linha de base e de destino.

## <a name="create-target-dataset"></a>Criar conjunto de origem de destino

O conjunto de dados de destino precisa do `timeseries` conjunto de características, especificando a coluna timestamp de uma coluna nos dados ou de uma coluna virtual derivada do padrão de caminho dos arquivos. Crie o conjunto de um com um carimbo de data/hora por meio do [SDK do Python](#sdk-dataset) ou do [Azure Machine Learning Studio](#studio-dataset). Uma coluna que representa um "timestamp" deve ser especificada para adicionar uma `timeseries` característica ao conjunto de um. Se os dados forem particionados na estrutura de pastas com informações de tempo, como ' {YYYY/MM/DD} ', crie uma coluna virtual por meio da configuração de padrão de caminho e defina-a como "carimbo de data/hora de partição" para melhorar a importância da funcionalidade de série temporal.

# <a name="python"></a>[Python](#tab/python)
<a name="sdk-dataset"></a>

O [`Dataset`](/python/api/azureml-core/azureml.data.tabulardataset#with-timestamp-columns-timestamp-none--partition-timestamp-none--validate-false----kwargs-) método de classe [`with_timestamp_columns()`](/python/api/azureml-core/azureml.data.tabulardataset#with-timestamp-columns-timestamp-none--partition-timestamp-none--validate-false----kwargs-)  define a coluna de carimbo de data/hora para o conjunto de um.

```python 
from azureml.core import Workspace, Dataset, Datastore

# get workspace object
ws = Workspace.from_config()

# get datastore object 
dstore = Datastore.get(ws, 'your datastore name')

# specify datastore paths
dstore_paths = [(dstore, 'weather/*/*/*/*/data.parquet')]

# specify partition format
partition_format = 'weather/{state}/{date:yyyy/MM/dd}/data.parquet'

# create the Tabular dataset with 'state' and 'date' as virtual columns 
dset = Dataset.Tabular.from_parquet_files(path=dstore_paths, partition_format=partition_format)

# assign the timestamp attribute to a real or virtual column in the dataset
dset = dset.with_timestamp_columns('date')

# register the dataset as the target dataset
dset = dset.register(ws, 'target')
```

> [!TIP]
> Para obter um exemplo completo de como usar a `timeseries` característica de conjuntos de valores, consulte o [bloco de anotações de exemplo](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/timeseries-datasets/tabular-timeseries-dataset-filtering.ipynb) ou a documentação do SDK dos conjuntos de [valores](/python/api/azureml-core/azureml.data.tabulardataset#with-timestamp-columns-timestamp-none--partition-timestamp-none--validate-false----kwargs-).

# <a name="studio"></a>[Estúdio](#tab/azure-studio)

<a name="studio-dataset"></a>

Se você criar seu conjunto de dados usando o Azure Machine Learning Studio, certifique-se de que o caminho para os data contém informações de carimbo de data/hora, inclua todas as subpastas com dados e defina o formato da partição.

No exemplo a seguir, todos os dados na subpasta *NoaaIsdFlorida/2019* são tirados e o formato de partição especifica o ano, o mês e o dia do carimbo de data/hora.

[![Formato de partição](./media/how-to-monitor-datasets/partition-format.png)](media/how-to-monitor-datasets/partition-format-expand.png)

Nas configurações de **esquema** , especifique a coluna timestamp de uma coluna virtual ou real no conjunto de espaço especificado:

:::image type="content" source="media/how-to-monitor-datasets/timestamp.png" alt-text="Definir o carimbo de data/hora":::

Se os dados forem particionados por data, como é o caso aqui, você também poderá especificar o partition_timestamp.  Isso permite um processamento mais eficiente de datas.

:::image type="content" source="media/how-to-monitor-datasets/timeseries-partitiontimestamp.png" alt-text="Carimbo de hora da partição":::

---

## <a name="create-dataset-monitor"></a>Criar monitor de conjunto de um

Crie um monitor de conjunto de dados para detectar e alertar a descompasso de dado em um novo DataSet.  Use o [SDK do Python](#sdk-monitor) ou o [Azure Machine Learning Studio](#studio-monitor).

# <a name="python"></a>[Python](#tab/python)
<a name="sdk-monitor"></a> Consulte a [documentação de referência do SDK do Python sobre descompasso de dados](/python/api/azureml-datadrift/azureml.datadrift) para obter detalhes completos. 

O exemplo a seguir mostra como criar um monitor de conjunto de um DataSet usando o SDK do Python

```python
from azureml.core import Workspace, Dataset
from azureml.datadrift import DataDriftDetector
from datetime import datetime

# get the workspace object
ws = Workspace.from_config()

# get the target dataset
dset = Dataset.get_by_name(ws, 'target')

# set the baseline dataset
baseline = target.time_before(datetime(2019, 2, 1))

# set up feature list
features = ['latitude', 'longitude', 'elevation', 'windAngle', 'windSpeed', 'temperature', 'snowDepth', 'stationName', 'countryOrRegion']

# set up data drift detector
monitor = DataDriftDetector.create_from_datasets(ws, 'drift-monitor', baseline, target, 
                                                      compute_target='cpu-cluster', 
                                                      frequency='Week', 
                                                      feature_list=None, 
                                                      drift_threshold=.6, 
                                                      latency=24)

# get data drift detector by name
monitor = DataDriftDetector.get_by_name(ws, 'drift-monitor')

# update data drift detector
monitor = monitor.update(feature_list=features)

# run a backfill for January through May
backfill1 = monitor.backfill(datetime(2019, 1, 1), datetime(2019, 5, 1))

# run a backfill for May through today
backfill1 = monitor.backfill(datetime(2019, 5, 1), datetime.today())

# disable the pipeline schedule for the data drift detector
monitor = monitor.disable_schedule()

# enable the pipeline schedule for the data drift detector
monitor = monitor.enable_schedule()
```

> [!TIP]
> Para obter um exemplo completo de como configurar um `timeseries` conjunto de dados e um detector de descompasso de dado, consulte nosso [bloco de anotações de exemplo](https://aka.ms/datadrift-notebook).


# <a name="studio"></a>[Estúdio](#tab/azure-studio)
<a name="studio-monitor"></a>

1. Navegue até a [página inicial do estúdio](https://ml.azure.com).
1. Selecione a guia **conjuntos de valores** à esquerda. 
1. Selecione **monitores de DataSet**.
   ![Lista de monitores](./media/how-to-monitor-datasets/monitor-list.png)

1. Clique no botão **+ criar monitor** e prossiga com o assistente clicando em **Avançar**.  

:::image type="content" source="media/how-to-monitor-datasets/wizard.png" alt-text="Criar um assistente de monitor":::

* **Selecione DataSet de destino**.  O conjunto de dados de destino é um conjunto de dados de tabela com a coluna timestamp especificada, que será analisada para descompasso. O conjunto de dados de destino deve ter recursos em comum com o conjunto de dados de linha de base, e deve ser um `timeseries` conjunto de dados para o qual são anexados novos. Os dados de histórico no DataSet de destino podem ser analisados ou novos dados podem ser monitorados.

* **Selecione conjunto de linha de base.**  Selecione o conjunto de conjuntos de tabelas a ser usado como a linha de base para comparação do conjunto de origem de destino ao longo do tempo.  O conjunto de linha de base deve ter recursos em comum com o DataSet de destino.  Selecione um intervalo de tempo para usar uma fatia do conjunto de origem de destino ou especifique um conjunto de uma diferente para usar como a linha de base.

* **Configurações do monitor**.  Essas configurações são para o pipeline monitor do conjunto de DataSet agendado, que será criado. 

    | Configuração | Descrição | Dicas | Mutável | 
    | ------- | ----------- | ---- | ------- |
    | Nome | Nome do monitor de DataSet. | | Não |
    | Recursos | Lista de recursos que serão analisados para descompasso de dados ao longo do tempo. | Definido como um ou mais recursos de saída do modelo para medir a descompasso de conceito. Não inclua recursos que naturalmente se descompassom ao longo do tempo (mês, ano, índice, etc.). Você pode aterrar e monitorar o descompasso de dados existente depois de ajustar a lista de recursos. | Sim | 
    | Destino de computação | Azure Machine Learning o destino de computação para executar os trabalhos do monitor de conjunto de trabalho. | | Sim | 
    | Habilitar | Habilitar ou desabilitar a agenda no pipeline do monitor de conjunto de um | Desabilite a agenda para analisar os dados históricos com a configuração de aterramento. Ele pode ser habilitado após a criação do monitor de conjunto de um. | Sim | 
    | Frequência | A frequência que será usada para agendar o trabalho de pipeline e analisar os dados históricos se estiver executando um aterramento. As opções incluem diário, semanal ou mensal. | Cada execução compara dados no DataSet de destino de acordo com a frequência: <li>Diário: comparar o dia completo mais recente no conjunto de entrada de destino com linha de base <li>Semanalmente: comparar a semana concluída mais recente (segunda-feira a domingo) no conjunto de entrada de destino com linha de base <li>Mensal: comparar o mês completo mais recente no conjunto de entrada de destino com linha de base | Não | 
    | Latency | Tempo, em horas, leva para que os dados cheguem no DataSet. Por exemplo, se demorar três dias para que os dados cheguem no BD SQL encapsulado, defina a latência como 72. | Não pode ser alterado após a criação do monitor de conjunto de um | Não | 
    | Endereços de email | Endereços de email para alertas com base na violação do limite de porcentagem de descompasso de dados. | Os emails são enviados por meio de Azure Monitor. | Sim | 
    | Limite | Limite de porcentagem de descompasso de dados para alerta de email. | Alertas e eventos adicionais podem ser definidos em muitas outras métricas no recurso de Application Insights associado do espaço de trabalho. | Sim |

Depois de concluir o assistente, o monitor do conjunto de resultados resultante será exibido na lista. Selecione-o para ir para a página de detalhes do monitor.

---

## <a name="understand-data-drift-results"></a>Entender os resultados de descompasso de dados

Esta seção mostra os resultados do monitoramento de um conjunto de um DataSet, encontrados na página monitores de conjunto de **valores de conjuntos**  /   de resultados no Azure Studio.  Você pode atualizar as configurações e analisar os dados existentes por um período de tempo específico nesta página.  

Comece com as informações de nível superior sobre a magnitude da descompasso de dados e um realce dos recursos a serem investigados.

:::image type="content" source="media/how-to-monitor-datasets/drift-overview.png" alt-text="Visão geral do descompasso":::


| Métrica | Descrição | 
| ------ | ----------- | 
| Magnitude da descompasso de dados | Uma porcentagem de descompasso entre a linha de base e o conjunto de origem de destino ao longo do tempo Variando de 0 a 100, 0 indica conjuntos de dados idênticos e 100 indica que o modelo de descompasso de Azure Machine Learning pode contar totalmente com os dois conjuntos. O ruído na porcentagem exata medida é esperado devido a técnicas de aprendizado de máquina que estão sendo usadas para gerar essa magnitude. | 
| Principais recursos de descompasso | Mostra os recursos do conjunto de um que foram mais desnecessários e, portanto, contribuindo mais para a métrica de magnitude de descompasso. Devido ao deslocamento covariado, a distribuição subjacente de um recurso não necessariamente precisa ser alterada para ter uma importância de recurso relativamente alta. |
| Limite | A magnitude do descompasso de dados além do limite definido irá disparar alertas. Isso pode ser definido nas configurações do monitor. | 

### <a name="drift-magnitude-trend"></a>Tendência de magnitude de descompasso

Veja como o conjunto de pontos difere do conjunto de entrada de destino no período de tempo especificado.  Quanto mais perto de 100%, mais dois conjuntos de valores diferem.

:::image type="content" source="media/how-to-monitor-datasets/drift-magnitude.png" alt-text="Tendência de magnitude de descompasso":::

### <a name="drift-magnitude-by-features"></a>Magnitude de descompasso por recursos

Esta seção contém informações em nível de recurso sobre a alteração na distribuição do recurso selecionado, bem como outras estatísticas, ao longo do tempo.

O conjunto de ponto de origem de destino também é criado com o passar do tempo. A distância estatística entre a distribuição de linha de base de cada recurso é comparada com o conjunto de recursos de destino ao longo do tempo.  Conceitualmente, isso é semelhante à magnitude de descompasso de dados.  No entanto, essa distância estatística é para um recurso individual, em vez de todos os recursos. Mín., máx. e média também estão disponíveis.

No Azure Machine Learning Studio, clique em uma barra no grafo para ver os detalhes de nível de recurso dessa data. Por padrão, você vê a distribuição do conjunto de linhas de base e a distribuição da execução mais recente do mesmo recurso.

:::image type="content" source="media/how-to-monitor-datasets/drift-by-feature.gif" alt-text="Magnitude de descompasso por recursos":::

Essas métricas também podem ser recuperadas no SDK do Python por meio do `get_metrics()` método em um `DataDriftDetector` objeto.

### <a name="feature-details"></a>Detalhes do recurso

Por fim, role para baixo para exibir detalhes de cada recurso individual.  Use as listas suspensas acima do gráfico para selecionar o recurso e, além disso, selecione a métrica que você deseja exibir.

:::image type="content" source="media/how-to-monitor-datasets/numeric-feature.gif" alt-text="Grafo de recursos numéricos e comparação":::

As métricas no gráfico dependem do tipo de recurso.

* Recursos numéricos

    | Métrica | Descrição |  
    | ------ | ----------- |  
    | Distância Wasserstein | Quantidade mínima de trabalho para transformar a distribuição de linha de base na distribuição de destino. |
    | Valor médio | Valor médio do recurso. |
    | Valor mínimo | Valor mínimo do recurso. |
    | Valor máximo | Valor máximo do recurso. |

* Recursos categóricos
    
    | Métrica | Descrição |  
    | ------ | ----------- |  
    | Distância euclidiana     |  Calculado para colunas categóricas. A distância euclidiana é calculada em dois vetores, gerados da distribuição empírica da mesma coluna categórica de dois conjuntos de valores. 0 indica que não há nenhuma diferença nas distribuições de empírica.  Quanto mais se desviar de 0, mais esta coluna se descompassou. As tendências podem ser observadas em uma plotagem de série temporal dessa métrica e podem ser úteis para descobrir um recurso de descompasso.  |
    | Valores únicos | Número de valores exclusivos (cardinalidade) do recurso. |

Neste gráfico, selecione uma única data para comparar a distribuição de recursos entre o destino e essa data para o recurso exibido. Para recursos numéricos, isso mostra duas distribuições de probabilidade.  Se o recurso for numérico, um gráfico de barras será mostrado.

:::image type="content" source="media/how-to-monitor-datasets/select-date-to-compare.gif" alt-text="Selecione uma data para comparar com o destino":::

## <a name="metrics-alerts-and-events"></a>Métricas, alertas e eventos

As métricas podem ser consultadas no recurso do [aplicativo Azure insights](../azure-monitor/app/app-insights-overview.md) associado ao seu espaço de trabalho do Machine Learning. Você tem acesso a todos os recursos do Application Insights incluindo configurar para regras de alerta personalizadas e grupos de ação para disparar uma ação como, por exemplo, um email/SMS/Push/voz ou Azure function. Consulte a documentação completa Application Insights para obter detalhes. 

Para começar, navegue até a [portal do Azure](https://portal.azure.com) e selecione a página **visão geral** do espaço de trabalho.  O recurso de Application Insights associado está na extrema direita:

[![Visão geral do portal do Azure](./media/how-to-monitor-datasets/ap-overview.png)](media/how-to-monitor-datasets/ap-overview-expanded.png)

Selecione logs (análise) em monitoramento no painel esquerdo:

![Visão geral do Application insights](./media/how-to-monitor-datasets/ai-overview.png)

As métricas do monitor de conjunto de um DataSet são armazenadas como `customMetrics` . Você pode escrever e executar uma consulta depois de configurar um monitor de conjunto de um DataSet para exibi-los:

[![Consulta do log Analytics](./media/how-to-monitor-datasets/simple-query.png)](media/how-to-monitor-datasets/simple-query-expanded.png)

Depois de identificar as métricas para configurar regras de alerta, crie uma nova regra de alerta:

![Nova regra de alerta](./media/how-to-monitor-datasets/alert-rule.png)

Você pode usar um grupo de ações existente ou criar um novo para definir a ação a ser tomada quando as condições definidas forem atendidas:

![Novo grupo de ação](./media/how-to-monitor-datasets/action-group.png)


## <a name="troubleshooting"></a>Solução de problemas

Limitações e problemas conhecidos para monitores de descompasso de dados:

* O intervalo de tempo durante a análise de dados históricos é limitado a 31 intervalos da configuração de frequência do monitor. 
* Limitação de 200 recursos, a menos que uma lista de recursos não seja especificada (todos os recursos usados).
* O tamanho da computação deve ser grande o suficiente para lidar com os dados.
* Certifique-se de que o conjunto de dados tenha os dados dentro da data de início e de término de uma determinada execução do monitor.
* Os monitores de conjunto de registros só funcionarão em conjuntos de valores que contenham 50 linhas ou mais.
* Colunas ou recursos, no conjunto de linhas, são classificados como categóricos ou numéricos com base nas condições na tabela a seguir. Se o recurso não atender a essas condições, por exemplo, uma coluna do tipo cadeia de caracteres com >valores exclusivos de 100-o recurso será descartado do nosso algoritmo de descompasso de dados, mas ainda será criado um perfil. 

    | Tipo de recurso | Tipo de dados | Condição | Limitações | 
    | ------------ | --------- | --------- | ----------- |
    | Categóricos | Cadeia de caracteres, bool, int, float | O número de valores exclusivos no recurso é menor que 100 e menor que 5% do número de linhas. | NULL é tratado como sua própria categoria. | 
    | Numérico | int, float | Os valores no recurso são de um tipo de dados numérico e não atendem à condição de um recurso categórico. | Recurso Descartado se >15% dos valores forem nulos. | 

* Quando você tiver criado um monitor de descompasso de dados, mas não puder ver os dados na página de **monitores do DataSet** no Azure Machine Learning Studio, tente o seguinte.

    1. Verifique se você selecionou o intervalo de datas correto na parte superior da página.  
    1. Na guia **monitores do conjunto** de testes, selecione o link experimento para verificar o status da execução.  Esse link está na extrema direita da tabela.
    1. Se a execução for concluída com êxito, verifique os logs de driver para ver quantas métricas foram geradas ou se há alguma mensagem de aviso.  Localize os logs de driver na guia **saída + logs** depois de clicar em um experimento.

* Se a função SDK não `backfill()` gerar a saída esperada, isso pode ser devido a um problema de autenticação.  Quando você cria a computação para passar para essa função, não use `Run.get_context().experiment.workspace.compute_targets` .  Em vez disso, use [ServicePrincipalAuthentication](/python/api/azureml-core/azureml.core.authentication.serviceprincipalauthentication) como o seguinte para criar a computação que você passa para essa `backfill()` função: 

  ```python
   auth = ServicePrincipalAuthentication(
          tenant_id=tenant_id,
          service_principal_id=app_id,
          service_principal_password=client_secret
          )
   ws = Workspace.get("xxx", auth=auth, subscription_id="xxx", resource_group="xxx")
   compute = ws.compute_targets.get("xxx")
   ```

* No coletor de dados de modelo, pode levar até (mas geralmente menos de) 10 minutos para que os dados cheguem em sua conta de armazenamento de BLOBs. Em um script ou notebook, aguarde 10 minutos para garantir que as células abaixo sejam executadas.

    ```python
    import time
    time.sleep(600)
    ```

## <a name="next-steps"></a>Próximas etapas

* Vá para o [Azure Machine Learning Studio](https://ml.azure.com) ou o [Notebook Python](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datadrift-tutorial/datadrift-tutorial.ipynb) para configurar um monitor de conjunto de um DataSet.
* Veja como configurar a descompasso de dados em [modelos implantados no serviço kubernetes do Azure](./how-to-enable-data-collection.md).
* Configurar monitores de descompasso de conjunto de um com a [grade de eventos](how-to-use-event-grid.md).