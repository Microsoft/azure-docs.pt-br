---
title: Guia de manutenção preditiva aeroespacial - Processo de Ciência de Dados da Equipe
description: Um guia técnico para o Modelo de Solução com o Microsoft Cortana Intelligence para a manutenção preventiva no setor aeroespacial, de utilitários e de transportes.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 03/15/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=fboylu, previous-ms.author=fboylu
ms.openlocfilehash: e2f0f1e7ac8f510c4ff5be7933c55278fef74694
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60715409"
---
# <a name="technical-guide-to-the-cortana-intelligence-solution-template-for-predictive-maintenance-in-aerospace"></a>Guia técnico do Modelo de Solução do Cortana Intelligence para a manutenção preventiva no setor aeroespacial

> [!Important]
> Este artigo foi preterido. A discussão sobre a manutenção preditiva no setor aeroespacial continua relevante, mas consulte a [Visão geral da solução para empresas](https://github.com/Azure/cortana-intelligence-predictive-maintenance-aerospace) para obter informações mais recentes.


Os modelos de solução foram projetados para acelerar o processo de criação de uma demonstração E2E sobre o Cortana Intelligence Suite. Um modelo implantado fornece à sua assinatura os componentes necessários do Cortana Intelligence e cria as relações entre eles. Ele também alimenta o pipeline de dados com dados de amostra produzidos por um aplicativo gerador de dados, que você baixa e instala no computador local depois de implantar o modelo de solução. Os dados produzidos pelo gerador irrigam o pipeline de dados e iniciam a geração das previsões de aprendizado de máquina, que podem ser visualizadas no painel do Power BI.

O processo de implantação guia você pelas diversas etapas para configurar as credenciais da sua solução. Registre essas credenciais, como o nome da solução, o nome de usuário e a senha que você fornece durante a implantação. 


Os objetivos deste artigo são:
- Descrever a arquitetura de referência e os componentes provisionados em sua assinatura.
- Demonstrar como substituir os dados de exemplo pelos seus próprios dados. 
- Mostrar como modificar o modelo de solução.  

> [!TIP]
> Você pode baixar e imprimir uma [versão em PDF deste artigo](https://download.microsoft.com/download/F/4/D/F4D7D208-D080-42ED-8813-6030D23329E9/cortana-analytics-technical-guide-predictive-maintenance.pdf).
> 
> 

## <a name="overview"></a>Visão geral
![Arquitetura de manutenção preditiva](./media/cortana-analytics-technical-guide-predictive-maintenance/predictive-maintenance-architecture.png)

Quando você implanta a solução, ela ativa serviços do Azure no Cortana Analytics Suite (Hub de Eventos, Stream Analytics, HDInsight, Data Factory, Machine Learning, etc.). O diagrama da arquitetura acima mostra como a Manutenção Preventiva do Modelo de Solução Aeroespacial é criada. Você pode investigar esses serviços no portal do Azure clicando neles no diagrama do modelo de solução criado com a implantação da solução (isso não se aplica ao HDInsight, pois ele é provisionado sob demanda quando as atividades do pipeline relacionado são obrigatoriamente executadas e mais tarde excluídas).
Baixe uma [versão em tamanho original do diagrama](https://download.microsoft.com/download/1/9/B/19B815F0-D1B0-4F67-AED3-A40544225FD1/ca-topologies-maintenance-prediction.png).

As seções a seguir descrevem as partes da solução.

## <a name="data-source-and-ingestion"></a>Fonte de dados e ingestão de dados
### <a name="synthetic-data-source"></a>Fonte de dados sintética
Para esse modelo, a fonte de dados usada é gerada por meio de um aplicativo da área de trabalho que você baixa e executa localmente após a implantação bem-sucedida.

Para encontrar as instruções para baixar e instalar este aplicativo, selecione o primeiro nó, Gerador de Dados de Manutenção Preditiva, no diagrama do modelo de solução. As instruções podem ser encontradas na barra Propriedades. Esse aplicativo alimenta o serviço [Hub de Eventos do Azure](#azure-event-hub) com pontos de dados, ou eventos, usados no restante do fluxo da solução. Essa fonte de dados deriva de dados publicamente disponíveis no [repositório de dados da NASA](https://c3.nasa.gov/dashlink/resources/139/) usando o [Turbofan Engine Degradation Simulation Data Set](https://ti.arc.nasa.gov/tech/dash/groups/pcoe/prognostic-data-repository/#turbofan).

O aplicativo de geração de eventos preenche o Hub de Eventos do Azure somente enquanto ele está em execução no computador.  

### <a name="azure-event-hub"></a>Hub de Eventos do Azure  
O serviço [Hub de Eventos do Azure](https://azure.microsoft.com/services/event-hubs/) é o destinatário da entrada fornecida pela Fonte de dados sintética.

## <a name="data-preparation-and-analysis"></a>Preparação e análise de dados  
### <a name="azure-stream-analytics"></a>Stream Analytics do Azure
Use o [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) para fornecer análises praticamente em tempo real no fluxo de entrada do serviço [Hub de Eventos do Azure](#azure-event-hub). Em seguida, publique os resultados em um painel do [Power BI](https://powerbi.microsoft.com) e arquive todos os eventos de entrada não processados no serviço [Armazenamento do Azure](https://azure.microsoft.com/services/storage/) para processamento posterior no serviço [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/).

### <a name="hdinsight-custom-aggregation"></a>Agregação personalizada do HDInsight
Execute scripts do [Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) (orquestrado pelo Azure Data Factory) usando o HDInsight para fornecer as agregações nos eventos brutos arquivados usando o serviço Azure Stream Analytics.

### <a name="azure-machine-learning"></a>Azure Machine Learning
Faça previsões sobre a RUL (vida útil restante) de um motor de aeronave específico usando as entradas recebidas com o [serviço Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) (orquestrado pelo Azure Data Factory). 

## <a name="data-publishing"></a>Publicação de dados
### <a name="azure-sql-database"></a>Banco de Dados SQL do Azure
Use o [Banco de Dados SQL do Azure](https://azure.microsoft.com/services/sql-database/) para armazenar as previsões recebidas pelo serviço Azure Machine Learning e que serão consumidas no painel do [Power BI](https://powerbi.microsoft.com).

## <a name="data-consumption"></a>Consumo de dados
### <a name="power-bi"></a>Power BI
Use o [Power BI](https://powerbi.microsoft.com) para mostrar um painel com as agregações e os alertas fornecidos pelo [Stream Analytics do Azure](https://azure.microsoft.com/services/stream-analytics/), bem como as previsões de RUL armazenadas no [Banco de Dados SQL do Azure](https://azure.microsoft.com/services/sql-database/) que foram produzidas usando o [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/).

## <a name="how-to-bring-in-your-own-data"></a>Como inserir seus próprios dados
Esta seção descreve como inserir seus próprios dados no Azure e quais áreas exigem alterações nos dados trazidos para essa arquitetura.

É improvável que seu conjunto de dados corresponda ao conjunto de dados usado pelo [Turbofan Engine Degradation Simulation Data Set](https://ti.arc.nasa.gov/tech/dash/groups/pcoe/prognostic-data-repository/#turbofan) utilizado para esse modelo de solução. A compreensão dos seus dados e dos requisitos é fundamental para a forma como você modificará esse modelo para que ele funcione com seus próprios dados. 

As seções a seguir discutem as partes do modelo que exigem modificações quando um novo conjunto de dados é introduzido.

### <a name="azure-event-hub"></a>Hub de Eventos do Azure
O Hub de Eventos do Azure é genérico. Os dados podem ser publicados no hub no formato CSV ou JSON. Não ocorrerá nenhum processamento especial no Hub de Eventos do Azure, mas é importante que você compreenda os dados inseridos nele.

Esse documento não descreve como incluir seus dados, mas você pode facilmente enviar eventos ou dados para um Hub de Eventos do Azure usando as APIs do Hub de Eventos.

### <a name="azure-stream-analytics"></a>Stream Analytics do Azure
Use o serviço Stream Analytics do Azure para fornecer análise em quase tempo real ao ler fluxos de dados e produzir como saída dados para qualquer número de fontes.

Para a Manutenção Preditiva para o Modelo de Solução Aeroespacial, a consulta do Stream Analytics do Azure consiste em quatro subconsultas; cada consulta consome eventos do serviço Hub de Eventos do Azure e produz saídas para quatro locais distintos. Essas saídas consistem em três conjuntos de dados do Power BI e em um local do Armazenamento do Azure.

A consulta do Stream Analytics do Azure pode ser encontrada por meio de:

* Conectar-se ao Portal do Azure
* Localizar trabalhos do Stream Analytics ![ícone do Stream Analytics](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-stream-analytics.png) gerados quando a solução foi implantada (*por exemplo*, **maintenancesa02asapbi** e **maintenancesa02asablob** para a solução de manutenção preditiva)
* Seleção
  
  * ***INPUTS*** para exibir a entrada da consulta
  * ***QUERY*** para exibir a consulta
  * ***OUTPUTS*** para exibir as diferentes saídas

As informações sobre a criação da consulta do Stream Analytics do Azure podem ser encontradas em [Stream Analytics Query Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx) , no MSDN.

Nesta solução, as consultas produzem como saída três conjuntos de dados com informações de análise quase em tempo real sobre o fluxo de dados de entrada para um painel fornecido como parte deste modelo de solução. Como há um conhecimento implícito sobre o formato de dados de entrada, essas consultas precisam ser alteradas com base em seu formato de dados.

A consulta do segundo trabalho do Stream Analytics, **maintenancesa02asablob**, simplesmente produz como saída todos os eventos do [Hub de Eventos](https://azure.microsoft.com/services/event-hubs/) para o [Armazenamento do Azure](https://azure.microsoft.com/services/storage/) e, portanto, não requer alteração, independentemente do formato dos dados, já que as informações completas do evento são transmitidas para o armazenamento.

### <a name="azure-data-factory"></a>Fábrica de dados do Azure
O serviço [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) orquestra a movimentação e o processamento de dados. Na Manutenção Preditiva para o Modelo de Solução Aeroespacial, o data factory é composto por três [pipelines](../../data-factory/concepts-pipelines-activities.md) que movem e processam os dados usando diversas tecnologias.  Acesse o data factory abrindo o nó Data Factory na parte inferior do diagrama do modelo de solução criado com a implantação da solução. Erros em seus conjuntos de dados ocorrem devido ao data factory ter sido implantado antes que o gerador de dados fosse iniciado. Esses erros podem ser ignorados e não impedem o funcionamento do seu data factory.

![Erros do conjunto de dados do Data Factory](./media/cortana-analytics-technical-guide-predictive-maintenance/data-factory-dataset-error.png)

Esta seção analisa os [pipelines e as atividades](../../data-factory/concepts-pipelines-activities.md) necessárias contidas no [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/). Veja um modo de exibição de diagrama da solução.

![Fábrica de dados do Azure](./media/cortana-analytics-technical-guide-predictive-maintenance/azure-data-factory.png)

Dois dos pipelines desse data factory contêm scripts do [Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) usados para particionar e agregar os dados. Quando observados, os scripts estão localizados na conta do [Armazenamento do Azure](https://azure.microsoft.com/services/storage/) criada durante a instalação. A localização deles é: maintenancesascript\\\\script\\\\hive\\\\ (ou https://[nome da solução].blob.core.windows.net/maintenancesascript).

Da mesma forma como nas consultas do [Azure Stream Analytics](#azure-stream-analytics-1), os scripts do [Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) têm um conhecimento implícito sobre o formato dos dados de entrada e precisam ser alteradas com base em seu formato de dados.

#### <a name="aggregateflightinfopipeline"></a>*AggregateFlightInfoPipeline*
Esse [pipeline](../../data-factory/concepts-pipelines-activities.md) contém uma única atividade - um atividade [HDInsightHive](../../data-factory/transform-data-using-hadoop-hive.md) que usa um [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) que executa um script do [Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) para particionar os dados colocados no [Armazenamento do Azure](https://azure.microsoft.com/services/storage/) durante o trabalho do [Stream Analytics do Azure](https://azure.microsoft.com/services/stream-analytics/).

O script do [Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) para essa tarefa de particionamento é ***AggregateFlightInfo.hql***

#### <a name="mlscoringpipeline"></a>*MLScoringPipeline*
Esse [pipeline](../../data-factory/concepts-pipelines-activities.md) contém várias atividades e seu resultado final é composto pelas previsões pontuadas do experimento do [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) associado a esse modelo de solução.

As atividades incluídas são:

* A atividade [HDInsightHive](../../data-factory/transform-data-using-hadoop-hive.md) usando um [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) que executa um script do [Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) para realizar as agregações e os recursos de engenharia necessários para o experimento do [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/).
  O script do [Hive](https://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx) para essa tarefa de particionamento é ***PrepareMLInput.hql***.
* A atividade[ Copy](https://msdn.microsoft.com/library/azure/dn835035.aspx) que move os resultados da atividade [HDInsightHive](../../data-factory/transform-data-using-hadoop-hive.md) para um único blob do [Armazenamento do Azure](https://azure.microsoft.com/services/storage/), acessado pela atividade [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx).
* A atividade [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx) chama o experimento do [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/), que faz com que os resultados sejam colocados em um único blob de [Armazenamento do Azure](https://azure.microsoft.com/services/storage/).

#### <a name="copyscoredresultpipeline"></a>*CopyScoredResultPipeline*
Esse [pipeline](../../data-factory/concepts-pipelines-activities.md) contém uma única atividade: uma atividade [Copy](https://msdn.microsoft.com/library/azure/dn835035.aspx) que move os resultados do teste do [Azure Machine Learning](#azure-machine-learning) do ***MLScoringPipeline*** para o [Banco de Dados SQL do Azure](https://azure.microsoft.com/services/sql-database/) provisionado como parte da instalação do modelo de solução.

### <a name="azure-machine-learning"></a>Azure Machine Learning
O experimento do [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) usado para esse modelo de solução fornece a RUL (Vida Útil Restante) de um motor de aeronave. O experimento é específico ao conjunto de dados consumido e exige modificação ou substituição específica para os dados apresentados.

Para saber mais sobre como o experimento do Azure Machine Learning foi criado, confira [Manutenção preventiva: Etapa 1 de 3, preparação de dados e engenharia de recursos](https://gallery.cortanaanalytics.com/Experiment/Predictive-Maintenance-Step-1-of-3-data-preparation-and-feature-engineering-2).

## <a name="monitor-progress"></a>Monitorar o progresso
Depois que o Gerador de Dados é iniciado, o pipeline começa a esvaziar e os diferentes componentes de sua solução começam a entrar em ação seguindo os comandos emitidos pelo data factory. Há duas maneiras possíveis de monitorar o pipeline.

1. Um dos trabalhos do Stream Analytics grava os dados brutos de entrada no armazenamento de blobs. Se você clicar no componente Armazenamento de Blobs de sua solução na tela na qual implantou a solução com êxito e clicar em Abrir no painel à direita, será levado para o [portal do Azure](https://portal.azure.com/). No portal, clique em Blobs. No painel seguinte, você vê uma lista de Contêineres. Clique em **maintenancesadata**. O próximo painel é a pasta **rawdata**. Dentro da pasta rawdata, há pastas com nomes como hour=17 e hour=18. A presença dessas pastas indica que dados não processados estão sendo gerados no seu computador e armazenados no armazenamento de blobs. Você deve ver arquivos csv com tamanhos finitos em MB nessas pastas.
2. A última etapa do pipeline é gravar dados (por exemplo, previsões do aprendizado de máquina) no Banco de Dados SQL. Talvez seja preciso esperar até três horas para que os dados apareçam no Banco de Dados SQL. Uma forma de monitorar o volume de dados disponível no Banco de Dados SQL é pelo [portal do Azure](https://portal.azure.com/). No painel à esquerda, localize BANCOS DE DADOS SQL![ícone SQL](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-SQL-databases.png) e clique nele. Em seguida, localize o banco de dados **pmaintenancedb** e clique nele. Na próxima página, na parte inferior, clique em GERENCIAR
   
    ![Ícone Gerenciar](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-manage.png)
   
    Aqui, é possível clicar em Nova Consulta e consular o número de linhas (por exemplo, selecione count(*) em PMResult ). À medida que o banco de dados aumenta, o número de linhas na tabela deve aumentar.

## <a name="power-bi-dashboard"></a>Painel do Power BI

Configure um painel do Power BI para visualizar seus dados do Azure Stream Analytics (afunilamento) e os resultados de previsão em lote de machine learning do Azure (caminho frio).

### <a name="set-up-the-cold-path-dashboard"></a>Configurar o painel de caminho frio
No pipeline de dados do caminho frio, o objetivo é obter a RUL de previsão (vida útil restante) de cada motor da aeronave após o término de um voo (ciclo). O resultado da previsão é atualizado a cada três horas para prever os motores da aeronave que concluiu um voo durante as três últimas horas.

O Power BI se conecta a um banco de dados SQL do Azure como fonte de dados, onde os resultados de previsão são armazenados. Observação: 1) Ao implantar sua solução, uma previsão aparecerá no banco de dados dentro de três horas.
O arquivo pbix que acompanha o download do Gerador contém alguns dados de propagação para que você possa criar o painel do Power BI imediatamente. 2) Nesta etapa, o pré-requisito é baixar e instalar o software gratuito [Power BI desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/).

As etapas a seguir mostram como conectar o arquivo pbix ao Banco de Dados SQL envolvido no momento da implantação da solução com dados (por exemplo, resultados de previsão) para visualização.

1. Obtenha as credenciais do banco de dados.
   
   Você precisará do **nome do servidor de banco de dados, do nome do banco de dados, do nome de usuário e da senha** antes de passar para as próximas etapas. Veja algumas etapas para mostrar como encontrá-las.
   
   * Quando **'Banco de Dados SQL do Azure'** ficar verde no diagrama do modelo da solução, clique nele e em **'Abrir'**.
   * Você verá uma nova guia/janela do navegador exibindo a página do portal do Azure. Clique em **'Grupos de recursos'** no painel esquerdo.
   * Selecione a assinatura que você está usando para implantar a solução e selecione **'NomedaSolução\_ResourceGroup'**.
   * No novo painel pop-out, clique no ícone ![ícone do SQL](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-sql.png) para acessar o banco de dados. O nome do banco de dados fica ao lado desse ícone (por exemplo, **'pmaintenancedb'**) e o **nome do servidor de banco de dados** está listado sob a propriedade Nome do servidor e deve ser parecido com **NomedaSolução.database.windows.net**.
   * O **nome de usuário** e a **senha** do banco de dados são iguais ao nome de usuário e a senha previamente gravados durante a implantação da solução.
2. Atualize a fonte de dados do arquivo de relatório de caminho frio com o Power BI Desktop.
   
   * Na pasta em que você baixou e descompactou o arquivo Gerador, clique duas vezes no arquivo **PowerBI\\PredictiveMaintenanceAerospace.pbix**. Se houver mensagens de aviso quando você abrir o arquivo, ignore-as. Na parte superior do arquivo, clique em **'Editar Consultas'**.
     
     ![Editar Consultas](./media/cortana-analytics-technical-guide-predictive-maintenance/edit-queries.png)
   * Você verá duas tabelas, **RemainingUsefulLife** e **PMResult**. Selecione a primeira tabela e clique no ![Ícone de configurações de consulta](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-query-settings.png) ao lado de **“Fonte”** em **“ETAPAS APLICADAS”** no painel **“Configurações da Consulta”** à direita. Ignore as mensagens de aviso que aparecerem.
   * Na janela aberta, substitua **'Servidor'** e **'Banco de Dados'** por seus próprios nomes do servidor e do banco de dados e clique em **'OK'**. Para o nome do servidor, especifique a porta 1433 (**NomeSolução.database.windows.net, 1433**). Deixe o campo Banco de Dados como **pmaintenancedb**. Ignore as mensagens de aviso que aparecem na tela.
   * Na próxima janela aberta, você verá duas opções no painel à esquerda (**Windows** e **Banco de Dados**). Clique em **'Banco de Dados'**, preencha o **'Nome de usuário'** e a **'Senha'** (são o nome de usuário e a senha inseridos quando você implantou pela primeira vez a solução e criou um Banco de Dados SQL do Azure). Em ***Selecione o nível no qual aplicar essas configurações***, marque a opção do nível do banco de dados. Clique em **'Conectar'**.
   * Clique na segunda tabela **PMResult** e clique no ![Ícone de Navegação](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-navigation.png) ao lado de **“Fonte”** em **“ETAPAS APLICADAS”** no painel **“Configurações da Consulta”** à direita e atualize os nomes do servidor e do banco de dados, como nas etapas acima e clique em OK.
   * Depois de ser guiado de volta à página anterior, feche a janela. Uma mensagem é exibida; clique em **Aplicar**. Por fim, clique no botão **Salvar** para salvar as alterações. Seu arquivo do Power BI agora estabeleceu uma conexão com o servidor. Se suas visualizações estiverem vazias, limpe as seleções nas visualizações para visualizar todos os dados clicando no ícone de borracha no canto superior direito das legendas. Use o botão de atualização para refletir os novos dados nas visualizações. Inicialmente, você só vê os dados propagados em suas visualizações, já que a atualização do data factory está agendada para a cada três horas. Após três horas, você verá novas previsões refletidas nas visualizações quando atualizar os dados.
3. (Opcional) Publique o painel de caminho frio no [Power BI online](https://www.powerbi.com/). Observe que esta etapa precisa de uma conta do Power BI (ou de uma conta do Office 365).
   
   * Clique em **'Publicar'** e, depois de alguns segundos, será exibida uma janela mostrando "Êxito ao publicar no Power BI!". com uma marca de seleção verde. Clique no link abaixo, "Abrir PredictiveMaintenanceAerospace.pbix no Power BI". Para obter instruções detalhadas, consulte [Publicar do Power BI Desktop](https://support.powerbi.com/knowledgebase/articles/461278-publish-from-power-bi-desktop).
   * Para criar um novo painel: clique no sinal **+** ao lado da seção **Painéis** no painel à esquerda. Insira o nome "Demonstração de manutenção preditiva" para esse novo painel.
   * Depois de abrir o relatório, clique no ![Ícone de PIN](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-pin.png) para fixar todas as visualizações no painel. Para obter instruções detalhadas, confira [Fixar um bloco em um painel do Power BI por meio de um relatório](https://support.powerbi.com/knowledgebase/articles/430323-pin-a-tile-to-a-power-bi-dashboard-from-a-report).
     Vá para a página do painel e ajuste o tamanho e o local de suas visualizações e edite os títulos delas. Para obter instruções detalhadas sobre como editar seus blocos, confira [Editar um bloco — redimensionar, mover, renomear, fixar, excluir, adicionar hiperlink](https://powerbi.microsoft.com/documentation/powerbi-service-edit-a-tile-in-a-dashboard/#rename). Veja um painel de exemplo com algumas visualizações de caminho frio fixadas nele.  Dependendo da duração da execução do gerador de dados, os números nas visualizações poderão ser diferentes.
     <br/>
     ![Exibição final](./media/cortana-analytics-technical-guide-predictive-maintenance/final-view.png)
     <br/>
   * Para agendar a atualização dos dados, passe o mouse sobre o **PredictiveMaintenanceAerospace** conjunto de dados, clique em ![no ícone reticências](./media/cortana-analytics-technical-guide-predictive-maintenance/icon-elipsis.png) e, em seguida, escolha **agendar atualização**.
     <br/>
     **Observação:** Se for exibida uma mensagem de aviso, clique em **Editar Credenciais** e verifique se suas credenciais do banco de dados são iguais às descritas na etapa 1.
     <br/>
     ![Agendar atualização](./media/cortana-analytics-technical-guide-predictive-maintenance/schedule-refresh.png)
     <br/>
   * Expanda a seção **Agendar Atualização** . Ative "manter os dados atualizados".
     <br/>
   * Agende a atualização com base em suas necessidades. Para saber mais, confira [Atualizar dados no Power BI](https://support.powerbi.com/knowledgebase/articles/474669-data-refresh-in-power-bi).

### <a name="setup-hot-path-dashboard"></a>Painel de afunilamento de instalação
As etapas a seguir mostram como visualizar a saída de dados dos trabalhos do Stream Analytics gerados no momento da implantação da solução. Será necessária uma conta do [Power BI online](https://www.powerbi.com/) para executar as etapas a seguir. Se não tiver uma conta, você poderá [criar uma](https://powerbi.microsoft.com/pricing).

1. Adicione a saída do Power BI ao Stream Analytics (ASA).
   
   * Você deve seguir as instruções em [Azure Stream Analytics e Power BI: Um painel de análise para a visibilidade em tempo real dos dados de streaming](../../stream-analytics/stream-analytics-power-bi-dashboard.md) para configurar a saída do seu trabalho do Azure Stream Analytics como seu painel do Power BI.
   * Configure as três saídas da consulta do ASA, **aircraftmonitor**, **aircraftalert** e **flightsbyhour**. Você pode exibir a consulta clicando na guia Consulta. Correspondendo a cada uma dessas tabelas, você precisa adicionar uma saída ao ASA. Quando você adiciona a primeira saída (**aircraftmonitor**), verifique se **Alias de Saída**, **Nome do Conjunto de Dados** e **Nome da Tabela** estão iguais (**aircraftmonitor**). Repita as etapas para adicionar saídas para **aircraftalert** e **flightsbyhour**. Depois de adicionar as três tabelas de saída e de iniciar o trabalho do ASA, você obterá uma mensagem de confirmação (“Êxito ao iniciar o trabalho maintenancesa02asapbi do Stream Analytics”).
2. Faça logon no [Power BI online](https://www.powerbi.com)
   
   * No painel à esquerda, seção Conjuntos de Dados em Meu Workspace, os nomes de ***CONJUNTO DE DADOS*** **aircraftmonitor**, **aircraftalert** e **flightsbyhour** devem aparecer. Esses são os dados de streaming enviados do Stream Analytics do Azure na etapa anterior. O conjunto de dados **flightsbyhour** poderá não aparecer ao mesmo tempo que os outros dois conjuntos de dados devido à natureza da consulta SQL por trás dele. No entanto, ele deve aparecer após uma hora.
   * Verifique se o painel ***Visualizações*** está aberto e se é mostrado no lado direito da tela.
3. Depois que os dados fluírem para o Power BI, você poderá começar a visualizar os dados de streaming. Veja abaixo um painel de exemplo com algumas visualizações de afunilamento fixadas nele. É possível criar outros blocos de painel com base em conjuntos de dados apropriados. Dependendo da duração da execução do gerador de dados, os números nas visualizações poderão ser diferentes.

    ![Exibição Painel](media/cortana-analytics-technical-guide-predictive-maintenance/dashboard-view.png)

1. Veja algumas etapas para criar um dos blocos acima, o bloco "Exibição de frota do sensor 11 versus limite 48,26":
   
   * Clique no conjunto de dados **aircraftmonitor** na seção Conjuntos de dados do painel esquerdo.
   * Clique no ícone **Gráfico de Linhas** .
   * Clique em **Processado** no painel **Campos** para mostrá-lo em "Eixo" no painel **Visualizações**.
   * Clique em "s11" e em "s11\_alert" para que ambos sejam exibidos em "Valores". Clique na pequena seta ao lado de **s11** e de **s11\_alert** e altere "Soma" para "Média".
   * Clique em **SALVAR** na parte superior e chame o relatório de "aircraftmonitor". O relatório chamado "aircraftmonitor" é exibido na seção **Relatórios** do painel **Navegador** à esquerda.
   * Clique no ícone **Fixar Visual** no canto superior direito desse gráfico de linha. Uma janela "Fixar ao painel" poderá aparecer para que você escolha um painel. Selecione "Demonstração de manutenção preditiva" e clique em "Fixar".
   * Passe o mouse sobre esse bloco no painel, clique no ícone "editar" no canto superior direito para alterar o título para "Exibição de frota do sensor 11 em relação ao Limite 48,26" e o subtítulo"Média na frota ao longo do tempo".

## <a name="delete-your-solution"></a>Excluir sua solução
Não se esqueça de parar o gerador de dados quando não estiver usando ativamente a solução, pois a execução do gerador de dados incorrerá em custos mais altos. Se não estiver usando a solução, exclua-a. A exclusão da solução exclui todos os componentes que foram provisionados em sua assinatura quando você implantou a solução. Para excluir a solução, clique em seu respectivo nome no painel esquerdo do modelo de solução e clique em **Excluir**.

## <a name="cost-estimation-tools"></a>Ferramentas de estimativa de custo
As duas ferramentas a seguir estão disponíveis para ajudar você a entender melhor os custos totais envolvidos na execução da Manutenção Preditiva para o Modelo de Solução Aeroespacial em sua assinatura:

* [Ferramenta Calculadora de Preço do Microsoft Azure (online)](https://azure.microsoft.com/pricing/calculator/)
* [Ferramenta Calculadora de Preço do Microsoft Azure (área de trabalho)](https://www.microsoft.com/download/details.aspx?id=43376)

