---
title: "Guia técnico de previsão de demanda em energia | Microsoft Docs"
description: "Um guia técnico para o Modelo de Solução com o Microsoft Cortana Intelligence para previsão de demanda de energia"
services: cortana-analytics
documentationcenter: 
author: yijichen
manager: ilanr9
editor: yijichen
ms.assetid: 7f1a866b-79b7-4b97-ae3e-bc6bebe8c756
ms.service: cortana-analytics
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2016
ms.author: inqiu;yijichen;ilanr9
ms.openlocfilehash: ed2a17fd735c1b0e67cbf5d08450d36620d4c857
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="technical-guide-to-the-cortana-intelligence-solution-template-for-demand-forecast-in-energy"></a>Guia técnico para o Modelo de Solução do Cortana Intelligence para previsão de demanda em energia
## <a name="overview"></a>**Visão geral**
Os Modelos de Solução foram projetados para acelerar o processo de criação de uma demonstração E2E sobre o Cortana Intelligence Suite. Um modelo implantado provisiona a sua assinatura com os componentes necessários do Cortana Intelligence e cria as relações entre eles. Ele também alimenta o pipeline de dados com amostras de dados geradas em um aplicativo de simulação de dados. Baixe o simulador de dados do link fornecido e instale-o em seu computador local. Consulte o arquivo readme.txt para obter instruções sobre o uso do simulador. Os dados gerados pelo simulador hidratam o pipeline de dados e começam a gerar previsões de aprendizado de máquina, que podem ser visualizadas no painel do Power BI.

O modelo de solução pode ser encontrado [aqui](https://gallery.cortanaintelligence.com/SolutionTemplate/Demand-Forecasting-for-Energy-1)

O processo de implantação guia você pelas diversas etapas para configurar as credenciais da sua solução. Registre essas credenciais, como o nome da solução, o nome de usuário e a senha fornecidos durante a implantação.

A meta deste documento é explicar a arquitetura de referência e os diferentes componentes provisionados em sua assinatura como parte deste Modelo de Solução. O documento também fala sobre como substituir as amostras de dados por dados reais, para que você possa ver informações/previsões obtidas de seus próprios dados. Além disso, o documento discute as partes do Modelo de Solução que precisarão ser modificadas caso você queira personalizar a solução com seus próprios dados. As instruções sobre como criar o painel do Power BI para esse Modelo de Solução serão fornecidas ao final.

## <a name="details"></a>**Detalhes**
![](media/cortana-analytics-technical-guide-demand-forecast/ca-topologies-energy-forecasting.png)

### <a name="architecture-explained"></a>Arquitetura explicada
Quando a solução é implantada, vários serviços do Azure no Cortana Analytics Suite são ativados (ou seja, Hub de Eventos, Stream Analytics, HDInsight, Data Factory, Machine Learning, *etc.*). O diagrama da arquitetura mostra, em alto nível, como o Modelo de solução de previsão de demanda de energia é criado de ponta a ponta. Você pode investigar esses serviços clicando neles no diagrama do modelo de solução criado com a implantação da solução. As seções a seguir descrevem cada parte.

## <a name="data-source-and-ingestion"></a>**Fonte de dados e ingestão**
### <a name="synthetic-data-source"></a>Fonte de dados sintética
Para esse modelo, a fonte de dados usada é gerada por meio de um aplicativo da área de trabalho que você baixa e executa localmente após a implantação bem-sucedida. Você encontra as instruções para baixar e instalar esse aplicativo na barra de propriedades, ao selecionar o primeiro nó chamado Simulador de dados de previsão de energia no diagrama do modelo de solução. Esse aplicativo alimenta o serviço [Hub de Eventos do Azure](#azure-event-hub) com pontos de dados ou eventos, que são usados no restante do fluxo da solução.

O aplicativo de geração de eventos preenche o Hub de Eventos do Azure somente enquanto ele está em execução no computador.

### <a name="azure-event-hub"></a>Hub de Eventos do Azure
O serviço [Hub de Eventos do Azure](https://azure.microsoft.com/services/event-hubs/) é o destinatário da entrada fornecida pela Fonte de dados sintética descrita.

## <a name="data-preparation-and-analysis"></a>**Preparação e análise de dados**
### <a name="azure-stream-analytics"></a>Stream Analytics do Azure
O serviço [Stream Analytics do Azure](https://azure.microsoft.com/services/stream-analytics/) é usado para fornecer uma análise quase em tempo real no fluxo de entrada do serviço [Hub de Eventos do Azure](#azure-event-hub) e para publicar os resultados em um painel do [Power BI](https://powerbi.microsoft.com), bem como para arquivar todos os eventos brutos de entrada no serviço [Armazenamento do Azure](https://azure.microsoft.com/services/storage/) para um processamento posterior pelo serviço [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/).

### <a name="hdinsight-custom-aggregation"></a>Agregação personalizada do HDInsight
O serviço Azure HDInsight é usado para executar scripts do [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) (orquestrado pelo Azure Data Factory), de modo a fornecer as agregações nos eventos brutos arquivados usando o serviço Azure Stream Analytics.

### <a name="azure-machine-learning"></a>Azure Machine Learning
O serviço [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) é usado (orquestrado pelo Azure Data Factory) para fazer previsões sobre o consumo futuro de energia de uma região específica com base nos dados recebidos.

## <a name="data-publishing"></a>**Publicação de dados**
### <a name="azure-sql-database-service"></a>Serviço Banco de Dados SQL do Azure
O serviço [Banco de Dados SQL do Azure](https://azure.microsoft.com/services/sql-database/) é usado para armazenar (gerenciado pelo Azure Data Factory) as previsões recebidas pelo serviço Azure Machine Learning e que são consumidas no painel do [Power BI](https://powerbi.microsoft.com).

## <a name="data-consumption"></a>**Consumo de dados**
### <a name="power-bi"></a>Power BI
O serviço [Power BI](https://powerbi.microsoft.com) é usado para mostrar um painel que contém as agregações fornecidas pelo serviço [Stream Analytics do Azure](https://azure.microsoft.com/services/stream-analytics/), bem como os resultados das previsões de demanda no [Banco de Dados SQL do Azure](https://azure.microsoft.com/services/sql-database/) que foram produzidos usando o serviço [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/). Para obter instruções sobre como criar o painel do Power BI para esse Modelo de solução, consulte a seção a seguir.

## <a name="how-to-bring-in-your-own-data"></a>**Como inserir seus próprios dados**
Esta seção descreve como inserir seus próprios dados no Azure e quais áreas exigiriam alterações nos dados trazidos para essa arquitetura.

É improvável que algum conjunto de dados que você coloque corresponda ao conjunto de dados usado para esse modelo de solução. A compreensão dos seus dados e dos requisitos é fundamental para a forma como você modificará esse modelo para que ele funcione com seus próprios dados. Se você for novo no serviço Azure Machine Learning, você poderá obter uma introdução a ele usando o exemplo em [Como criar seu primeiro experimento](machine-learning/studio/create-experiment.md).

As seções a seguir discutem as seções do modelo que exigem modificações quando um novo conjunto de dados é introduzido.

### <a name="azure-event-hub"></a>Hub de Eventos do Azure
O serviço [Hub de Eventos do Azure](https://azure.microsoft.com/services/event-hubs/) é genérico, já que esses dados podem ser publicados no hub no formato CSV ou JSON. Não ocorrerá qualquer processamento especial no Hub de Eventos do Azure, mas é importante que você compreenda os dados inseridos nele.

Este documento não descreve como ingerir seus dados, mas é possível enviar com facilidade eventos ou dados para um Hub de Eventos do Azure usando a [API do Hub de Eventos](event-hubs/event-hubs-programming-guide.md).

### <a name="azure-stream-analytics"></a>Stream Analytics do Azure
O serviço [Stream Analytics do Azure](https://azure.microsoft.com/services/stream-analytics/) é usado para fornecer uma análise quase em tempo real ao ler os fluxos de dados e produzir como saída dados para várias fontes.

Para o Modelo de solução de previsão de demanda de energia, a consulta do Azure Stream Analytics é formada por duas subconsultas, cada uma consumindo evento do serviço Hub de Eventos do Azure e produzindo saídas para dois locais distintos. Essas saídas são formadas por um conjunto de dados do Power BI e em um local do Armazenamento do Azure.

A consulta [Stream Analytics do Azure](https://azure.microsoft.com/services/stream-analytics/) pode ser encontrada:

* Fazendo logon no [Portal do Azure](https://manage.windowsazure.com/)
* Localizando os trabalhos do Stream Analytics ![](media/cortana-analytics-technical-guide-demand-forecast/icon-stream-analytics.png) que foram gerados durante a implantação da solução. Um serve para enviar dados por push ao armazenamento de blobs (por exemplo, mytest1streaming432822asablob) e o outro é para publicar dados no Power BI (por exemplo, mytest1streaming432822asapbi).
* Seleção

  * ***INPUTS*** para exibir a entrada da consulta
  * ***QUERY*** para exibir a consulta
  * ***OUTPUTS*** para exibir as diferentes saídas

As informações sobre a criação da consulta do Stream Analytics do Azure podem ser encontradas em [Stream Analytics Query Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx) , no MSDN.

Nesta solução, o trabalho do Azure Stream Analytics, que produz um conjunto de dados com informações de análise quase em tempo real sobre o fluxo de dados de entrada para um painel do Power BI, é fornecido como parte desse modelo de solução. Como há um conhecimento implícito sobre o formato de dados de entrada, essas consultas precisariam ser alteradas com base em seu formato de dados.

O outro trabalho do Stream Analytics do Azure produz todos os eventos do [Hub de Eventos](https://azure.microsoft.com/services/event-hubs/) no [Armazenamento do Azure](https://azure.microsoft.com/services/storage/) e, portanto, não exige alteração, independentemente do formato dos dados, já que as informações completas do evento são transmitidas para o armazenamento.

### <a name="azure-data-factory"></a>Fábrica de dados do Azure
O serviço [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) orquestra a movimentação e o processamento de dados. No Modelo de previsão de demanda para solução de energia, o data factory é composto por 12 [pipelines](data-factory/concepts-pipelines-activities.md) que movem e processam os dados usando diversas tecnologias.

  Você pode acessar seu data factory abrindo o nó Data Factory na parte inferior do diagrama do modelo de solução criado com a implantação da solução. Você pode ver o data factory no Portal do Azure. Se você encontrar erros em seus conjuntos de dados, poderá ignorá-los, já que eles ocorrem porque a implantação do data factory foi anterior ao início do gerador de dados. Esses erros não impedem o funcionamento do seu data factory.

Esta seção analisa os [pipelines](data-factory/concepts-pipelines-activities.md) e as [atividades](data-factory/concepts-pipelines-activities.md) necessários contidos no [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/). A imagem a seguir é a exibição de diagrama da solução:

![](media/cortana-analytics-technical-guide-demand-forecast/ADF2.png)

Cinco dos pipelines desse data factory contêm scripts do [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) usados para particionar e agregar os dados. Quando observados, os scripts estão localizados na conta do [Armazenamento do Azure](https://azure.microsoft.com/services/storage/) criada durante a instalação. A localização deles é: demandforecasting\\\\script\\\\hive\\\\ (ou https://[Nome da sua solução].blob.core.windows.net/demandforecasting).

Da mesma forma como nas consultas do [Stream Analytics do Azure](#azure-stream-analytics-1), os scripts do [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) têm um conhecimento implícito sobre o formato dos dados de entrada. Essas consultas precisariam ser alteradas com base em seu formato de dados e nos requisitos da [engenharia de recursos](machine-learning/team-data-science-process/create-features.md).

#### <a name="aggregatedemanddatato1hrpipeline"></a>*AggregateDemandDataTo1HrPipeline*
Esse [pipeline](data-factory/concepts-pipelines-activities.md) contém uma única atividade – uma atividade [HDInsightHive](data-factory/transform-data-using-hadoop-hive.md) usando um [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) que executa um script do [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) para agregar os dados transmitidos sob demanda a cada 10 segundos no nível da subestação ao nível da região por hora e colocá-los no [Armazenamento do Azure](https://azure.microsoft.com/services/storage/) por meio do trabalho do Azure Stream Analytics.

O script do [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) para essa tarefa de particionamento é ***AggregateDemandRegion1Hr.hql***

#### <a name="loadhistorydemanddatapipeline"></a>*LoadHistoryDemandDataPipeline*
Esse [pipeline](data-factory/concepts-pipelines-activities.md) contém duas atividades:

* A atividade [HDInsightHive](data-factory/transform-data-using-hadoop-hive.md) usando um [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) que executa um script do Hive para agregar os dados de demanda de histórico por hora no nível da subestação ao nível da região por hora e colocá-los no Armazenamento do Azure durante o trabalho do Stream Analytics do Azure
* [Copy](https://msdn.microsoft.com/library/azure/dn835035.aspx) que move os dados agregados do Blob de Armazenamento do Azure para o Banco de Dados SQL do Azure provisionado como parte da instalação do modelo de solução.

O script do [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) para essa tarefa é ***AggregateDemandHistoryRegion.hql***.

#### <a name="mlscoringregionxpipeline"></a>*MLScoringRegionXPipeline*
Esses [pipelines](data-factory/concepts-pipelines-activities.md) contêm várias atividades cujos resultados finais são as previsões pontuadas do experimento do Azure Machine Learning associadas a esse modelo de solução. Eles são quase idênticos, exceto pelo fato de que cada um trata apenas da região diferente, que está sendo concluída por uma RegionID diferente passada no pipeline do ADF e no script do Hive para cada região.  
As atividades contidas neste pipeline são:

* A atividade [HDInsightHive](data-factory/transform-data-using-hadoop-hive.md) usando um [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) que executa um script do Hive para realizar as agregações e os recursos de engenharia necessários para o experimento do Azure Machine Learning. Os scripts do Hive para essa tarefa são os respectivos ***PrepareMLInputRegionX.hql***.
* A atividade [Copy](https://msdn.microsoft.com/library/azure/dn835035.aspx), que move os resultados da atividade [HDInsightHive](data-factory/transform-data-using-hadoop-hive.md) para um único Azure Storage Blob, que pode ser acessado pela atividade [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx).
* A atividade [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx), que chama o experimento do Azure Machine Learning, que faz com que os resultados sejam colocados em um único Azure Storage Blob.

#### <a name="copyscoredresultregionxpipeline"></a>*CopyScoredResultRegionXPipeline*
Esse [pipeline](data-factory/concepts-pipelines-activities.md) contém uma única atividade – uma atividade [Copy](https://msdn.microsoft.com/library/azure/dn835035.aspx) que move os resultados do experimento do Azure Machine Learning do respectivo ***MLScoringRegionXPipeline*** para o Banco de Dados SQL do Azure que foi provisionado como parte da instalação do modelo de solução.

#### <a name="copyaggdemandpipeline"></a>*CopyAggDemandPipeline*
Esse [pipeline](data-factory/concepts-pipelines-activities.md) contém uma única atividade – uma atividade [Copy](https://msdn.microsoft.com/library/azure/dn835035.aspx) que move os dados de demanda contínua agregados do ***LoadHistoryDemandDataPipeline*** para o Banco de Dados SQL do Azure que foi provisionado como parte da instalação do modelo de solução.

#### <a name="copyregiondatapipeline-copysubstationdatapipeline-copytopologydatapipeline"></a>*CopyRegionDataPipeline, CopySubstationDataPipeline, CopyTopologyDataPipeline*
Esse [pipeline](data-factory/concepts-pipelines-activities.md) contém uma única atividade – uma atividade [Copy](https://msdn.microsoft.com/library/azure/dn835035.aspx) que move os dados de referência da Região/Subestação/Geotopologia que são carregados no Azure Storage Blob como parte da instalação do modelo de solução para o Banco de Dados SQL que foi provisionado como parte da instalação do modelo de solução.

### <a name="azure-machine-learning"></a>Azure Machine Learning
O experimento do [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) usado para esse modelo de solução fornece a previsão de demanda da região. O experimento é específico ao conjunto de dados consumido e, portanto, exige modificação ou substituição específica para os dados trazidos.

## <a name="monitor-progress"></a>**Monitorar o progresso**
Depois que o Gerador de Dados é iniciado, o pipeline começa a obter os hidratados e os diferentes componentes de sua solução começam a entrar em ação seguindo os comandos emitidos pelo Data Factory. Há duas maneiras possíveis de monitorar o pipeline.

1. Verificar os dados do Armazenamento de Blobs do Azure

    Um dos trabalhos do Stream Analytics grava os dados brutos de entrada no armazenamento de blobs. Se você clicar no componente **Armazenamento de Blobs do Azure** de sua solução na tela na qual implantou a solução com êxito e clicar em **Abrir** no painel à direita, será levado para o [Portal do Azure](https://portal.azure.com). Uma vez lá, clique em **Blobs**. No painel seguinte, você vê uma lista de Contêineres. Clique em **"energysadata"**. No painel seguinte, você vê a pasta **"demandongoing"**. Dentro da pasta rawdata, você vê pastas com nomes como date=2016-01-28 etc. Caso você visualize essas pastas, isso indica que os dados brutos estão sendo gerados com êxito no computador e armazenados no armazenamento de blobs. Você deverá ver arquivos com tamanhos finitos em MB nessas pastas.
2. Verifique os dados do Banco de Dados SQL do Azure.

    A última etapa do pipeline é gravar dados (por exemplo, previsões do aprendizado de máquina) no Banco de Dados SQL. Talvez seja necessário esperar até duas horas no máximo para que os dados apareçam no Banco de Dados SQL. Uma forma de monitorar o volume de dados disponível no Banco de Dados SQL é pelo [Portal do Azure](https://manage.windowsazure.com/). No painel à esquerda, localize BANCOS DE DADOS SQL![](media/cortana-analytics-technical-guide-demand-forecast/SQLicon2.png) e clique. Em seguida, localize seu banco de dados (ou seja, demo123456db) e clique nele. Na próxima página, na seção **"Conectar seu banco de dados"**, clique em **"Executar Consultas Transact-SQL em seu Banco de Dados SQL"**.

    Aqui, você pode clicar em Nova Consulta e consultar o número de linhas (por exemplo, "select count(*) de DemandRealHourly)". À medida que seu banco de dados cresce, o número de linhas na tabela aumenta).
3. Verifique os dados no painel do Power BI.

    Você pode configurar o painel de afunilamento do Power BI para monitorar os dados brutos de entrada. Siga as instruções na seção "Painel do Power BI".

## <a name="power-bi-dashboard"></a>**Painel do Power BI**
### <a name="overview"></a>Visão geral
Esta seção descreve como configurar o painel do Power BI para visualizar os dados em tempo real do Azure Stream Analytics (afunilamento), bem como os resultados de previsão do aprendizado de máquina do Azure (caminho frio).

### <a name="setup-hot-path-dashboard"></a>Configurar o painel de afunilamento
As etapas a seguir mostram como visualizar a saída de dados em tempo real dos trabalhos do Stream Analytics gerados no momento da implantação da solução. Será necessária uma conta do [Power BI online](http://www.powerbi.com/) para executar as etapas a seguir. Se não tiver uma conta, você poderá [criar uma](https://powerbi.microsoft.com/pricing).

1. Adicione a saída do Power BI ao Stream Analytics (ASA).

   * Você precisa seguir as instruções em [Azure Stream Analytics e Power BI: um painel de análise em tempo real para a visibilidade em tempo real dos dados de streaming](stream-analytics/stream-analytics-power-bi-dashboard.md) para configurar a saída do seu trabalho do Azure Stream Analytics como seu painel do Power BI.
   * Localize o trabalho do Stream Analytics em seu [Portal do Azure](https://manage.windowsazure.com). O nome do trabalho deve ser: NomeSolução+"trabalhostreaming"+número aleatório+"asapbi" (ou seja, demostreamingjob123456asapbi).
   * Adicione uma saída do Power BI para o trabalho do ASA. Defina o **Alias de Saída** como **‘PBIoutput’**. Defina o **Nome do Conjunto de Dados** e o **Nome da Tabela** como **‘EnergyStreamData’**. Depois de adicionar a saída, clique em **"Iniciar"** na parte inferior da página para iniciar o trabalho do Stream Analytics. Você deverá receber uma mensagem de confirmação (por exemplo, "Iniciando o trabalho do Stream Analytics myteststreamingjob12345asablob com êxito").
2. Faça logon no [Power BI online](http://www.powerbi.com)

   * Na seção Conjuntos de Dados do painel esquerdo em Meu Espaço de Trabalho, você deverá ver um novo conjunto de dados no painel esquerdo do Power BI. Esses são os dados de streaming enviados do Stream Analytics do Azure na etapa anterior.
   * Verifique se o painel ***Visualizações*** está aberto e se é mostrado no lado direito da tela.
3. Crie o bloco "Demanda por Carimbo de Data/Hora":

   * Clique no conjunto de dados **'EnergyStreamData'** na seção Conjuntos de Dados do painel à esquerda.
   * Clique no ícone **"Gráfico de Linhas"**![](media/cortana-analytics-technical-guide-demand-forecast/PowerBIpic8.png).
   * Clique em 'EnergyStreamData' no painel **Campos** .
   * Clique em **“Timestamp”** e verifique se isso é exibido em "Eixo". Clique em **"Carregar"** e verifique se isso é exibido em "Valores".
   * Clique em **SALVAR** na parte superior e nomeie o relatório como "EnergyStreamDataReport". O relatório chamado "EnergyStreamDataReport" é exibido na seção Relatórios do painel Navegador à esquerda.
   * Clique no ícone **"Fixação Visual"**![](media/cortana-analytics-technical-guide-demand-forecast/PowerBIpic6.png) no canto superior direito deste gráfico de linha; uma janela "Fixar no Painel" pode aparecer para que você escolha um painel. Selecione "EnergyStreamDataReport" e clique em "Fixar".
   * Passe o mouse sobre esse bloco no painel, clique no ícone "editar" no canto superior direito para alterar seu título para "Demandar por Carimbo de Data/Hora"
4. Crie outros blocos de painel com base em conjuntos de dados apropriados. A exibição do painel final: ![](media/cortana-analytics-technical-guide-demand-forecast/PBIFullScreen.png)

### <a name="setup-cold-path-dashboard"></a>Configurar o painel de caminho frio
No pipeline de dados de caminho frio, o principal objetivo é obter a previsão de demanda de cada região. O Power BI se conecta a um banco de dados SQL do Azure como fonte de dados, onde os resultados de previsão são armazenados.

> [!NOTE]
> 1) Demora algumas horas para coletar resultados de previsão suficientes para o painel. Recomendamos o início desse processo duas a três horas após a inicialização do gerador de dados. 2) Nesta etapa, o pré-requisito é baixar e instalar o software gratuito [Power BI desktop](https://powerbi.microsoft.com/desktop).
>
>

1. Obtenha as credenciais do banco de dados.

   Você precisa do **nome do servidor de banco de dados, do nome do banco de dados, do nome de usuário e da senha** antes de passar para as próximas etapas. Veja algumas etapas para mostrar como encontrá-las.

   * Quando **"Banco de Dados SQL do Azure"** ficar verde no diagrama do modelo da solução, clique nele e em **"Abrir"**. Você é guiado ao Portal do Azure e sua página de informações do banco de dados também é aberta.
   * Na página, você pode encontrar uma seção "Banco de dados". Ela lista o banco de dados que você criou. O nome de seu banco de dados deve ser **"Nome da sua solução + Número aleatório + db"** (por exemplo, "mytest12345db").
   * Clique em seu banco de dados e, no novo painel pop-up, você encontrará o nome do servidor de banco de dados na parte superior. O nome do seu servidor de banco de dados deve ser `"Your Solution Name + Random Number + 'database.windows.net,1433'"` (por exemplo, "mytest12345.database.windows.net,1433").
   * O **nome de usuário** e a **senha** do banco de dados são iguais ao nome de usuário e a senha previamente gravados durante a implantação da solução.
2. Atualizar a fonte de dados do arquivo de dados em lote (caminho frio) do Power BI

   * Verifique se você instalou a versão mais recente do [Power BI desktop](https://powerbi.microsoft.com/desktop).
   * Na pasta **"DemandForecastingDataGeneratorv1.0"** que você baixou, clique duas vezes no arquivo **"Power BI Template\DemandForecastPowerBI.pbix"**. As visualizações inicias se baseiam em dados fictícios. **Observação:** se você vir uma mensagem de erro, verifique se instalou a versão mais recente do Power BI Desktop.

     Depois de abri-lo, na parte superior do arquivo, clique em **’Editar Consultas’**. Na janela pop-up, clique duas vezes em **"Fonte"** no painel à direita.
     ![](media/cortana-analytics-technical-guide-demand-forecast/PowerBIpic1.png)
   * Na janela aberta, substitua **"Servidor"** e **"Banco de Dados"** por seus próprios nomes do servidor e do banco de dados e clique em **"OK"**. Para o nome do servidor, especifique a porta 1433 (**NomeSolução.database.windows.net, 1433**). Ignore as mensagens de aviso que aparecem na tela.
   * Na próxima janela aberta, você verá duas opções no painel à esquerda (**Windows** e **Banco de Dados**). Clique em **"Banco de Dados"**, preencha o **"Nome de usuário"** e a **"Senha"** (são o nome de usuário e a senha inseridos quando você implantou pela primeira vez a solução e criou um Banco de Dados SQL do Azure). Em ***Selecione o nível no qual aplicar essas configurações***, marque a opção do nível do banco de dados. Clique em **"Conectar"**.
   * Depois de ser guiado de volta à página anterior, feche a janela. Uma mensagem é exibida – clique em **Aplicar**. Por fim, clique no botão **Salvar** para salvar as alterações. Seu arquivo do Power BI agora estabeleceu uma conexão com o servidor. Se suas visualizações estiverem vazias, limpe as seleções nas visualizações para visualizar todos os dados clicando no ícone de borracha no canto superior direito das legendas. Use o botão de atualização para refletir os novos dados nas visualizações. Inicialmente, você só vê os dados propagados em suas visualizações, já que a atualização do data factory está agendada para a cada três horas. Após três horas, você verá as novas previsões refletidas nas visualizações ao atualizar os dados.
3. (Opcional) Publique o painel de caminho frio no [Power BI online](http://www.powerbi.com/). Observe que esta etapa precisa de uma conta do Power BI (ou de uma conta do Office 365).

   * Clique em **"Publicar"** e, depois de alguns segundos, será exibida uma janela mostrando "Êxito ao publicar no Power BI!". com uma marca de seleção verde. Clique no link a seguir "Abrir demoprediction.pbix abrir no Power BI". Para obter instruções detalhadas, consulte [Publicar do Power BI Desktop](https://support.powerbi.com/knowledgebase/articles/461278-publish-from-power-bi-desktop).
   * Para criar um novo painel: clique no sinal **+** ao lado da seção **Painéis** no painel à esquerda. Insira o nome "Demonstração de previsão de demanda" para esse novo painel.
   * Depois de abrir o relatório, clique em ![](media/cortana-analytics-technical-guide-demand-forecast/PowerBIpic6.png) para fixar todas as visualizações no painel. Para obter instruções detalhadas, confira [Fixar um bloco em um painel do Power BI por meio de um relatório](https://support.powerbi.com/knowledgebase/articles/430323-pin-a-tile-to-a-power-bi-dashboard-from-a-report).
     Vá para a página do painel e ajuste o tamanho e o local de suas visualizações e edite os títulos delas. Para obter instruções detalhadas sobre como editar seus blocos, confira [Editar um bloco — redimensionar, mover, renomear, fixar, excluir, adicionar hiperlink](https://powerbi.microsoft.com/documentation/powerbi-service-edit-a-tile-in-a-dashboard/#rename). Veja um painel de exemplo com algumas visualizações de caminho frio fixadas nele.

     ![](media/cortana-analytics-technical-guide-demand-forecast/PowerBIpic7.png)
4. (Opcional) Agendar a atualização da fonte de dados.

   * Para agendar a atualização dos dados, passe o mouse sobre o conjunto de dados **EnergyBPI-Final**, clique em ![](media/cortana-analytics-technical-guide-demand-forecast/PowerBIpic3.png) e escolha **Agendar Atualização**.
     **Observação:** se for exibida uma mensagem de aviso, clique em **Editar Credenciais** e verifique se suas credenciais do banco de dados são iguais às descritas na etapa 1.

     ![](media/cortana-analytics-technical-guide-demand-forecast/PowerBIpic4.png)
   * Expanda a seção **Agendar Atualização** . Ative "manter os dados atualizados".
   * Agende a atualização com base em suas necessidades. Para saber mais, consulte [Atualizar dados no Power BI](https://powerbi.microsoft.com/documentation/powerbi-refresh-data/).

## <a name="how-to-delete-your-solution"></a>**Como excluir a solução**
Não se esqueça de parar o gerador de dados quando não estiver usando ativamente a solução, pois a execução dele incorrerá em custos mais altos. Se não estiver usando a solução, exclua-a. A exclusão da solução exclui todos os componentes que foram provisionados em sua assinatura quando você implantou a solução. Para excluir a solução, clique com botão no nome da solução no painel esquerdo do modelo de solução e clique em Excluir.

## <a name="cost-estimation-tools"></a>**Ferramentas de estimativa de custo**
As duas ferramentas a seguir estão disponíveis para ajudar você a entender melhor os custos totais envolvidos do Modelo de Solução de Previsão de demanda para energia em sua assinatura:

* [Ferramenta Calculadora de Preço do Microsoft Azure (online)](https://azure.microsoft.com/pricing/calculator/)
* [Ferramenta Calculadora de Preço do Microsoft Azure (área de trabalho)](http://www.microsoft.com/download/details.aspx?id=43376)

## <a name="acknowledgements"></a>**Confirmações**
Este artigo foi escrito pelo cientista de dados Yijing Chen e pelo engenheiro de software Qiu Min da Microsoft.
