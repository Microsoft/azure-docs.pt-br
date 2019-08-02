---
title: Comparar o Azure Data Factory com a versão 1 do Data Factory | Microsoft Docs
description: Este artigo compara o Azure Data Factory com a versão 1 do Azure Data Factory.
services: data-factory
documentationcenter: ''
author: kromerm
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: overview
ms.date: 04/09/2018
ms.author: makromer
ms.openlocfilehash: 4cdb517e644d55504bfdafbd3bacdfd4bfa0b36c
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479294"
---
# <a name="compare-azure-data-factory-with-data-factory-version-1"></a>Comparar o Azure Data Factory com a versão 1 do Data Factory
Este artigo compara o Data Factory com a versão 1 do Data Factory. Para obter uma introdução ao Data Factory, consulte [Introdução ao Data Factory](introduction.md). Para obter uma introdução à versão 1 do Data Factory, consulte [Introdução ao Azure Data Factory](v1/data-factory-introduction.md). 

## <a name="feature-comparison"></a>Comparação de recursos
A tabela a seguir compara os recursos do Data Factory com os recursos da versão 1 do Data Factory. 

| Recurso | Versão 1 | Versão atual | 
| ------- | --------- | --------- | 
| Conjunto de dados | Uma exibição nomeada de dados que faz referência aos dados que você deseja usar em suas atividades, como entradas e saídas. Conjuntos de dados identificam dados em armazenamentos de dados diferentes, como tabelas, arquivos, pastas e documentos. Por exemplo, um conjunto de dados de Blob do Azure especifica o contêiner de blobs e a pasta no armazenamento de Blobs do Azure dos quais a atividade deve ler os dados.<br/><br/>**Disponibilidade** define o modelo de divisão da janela de processamento do conjunto de dados (por exemplo, por hora ou diária, e assim por diante). | Os conjuntos de dados são os mesmos na versão atual. No entanto, você não precisa definir agendas de **disponibilidade** para os conjuntos de dados. Você pode definir um recurso de gatilho que pode agendar pipelines de um paradigma de agendador de relógio. Para saber mais, confira [Gatilhos](concepts-pipeline-execution-triggers.md#triggers) e [Conjuntos de dados](concepts-datasets-linked-services.md). | 
| Serviços vinculados | Os serviços vinculados são como cadeias de conexão, que definem as informações de conexão necessárias para que o Data Factory se conecte aos recursos externos. | Os serviços vinculados são os mesmos que os do Data Factory V1, mas com uma nova propriedade **connectVia**, a fim de usar o ambiente de computação do Integration Runtime da versão atual do Data Factory. Para saber mais, confira [Tempo de execução de integração no Azure Data Factory](concepts-integration-runtime.md) e [Propriedades de serviço vinculado para o Armazenamento de Blobs do Azure](connector-azure-blob-storage.md#linked-service-properties). |
| Pipelines | Uma fábrica de dados pode ter um ou mais pipelines. Um pipeline é um agrupamento lógico de atividades que juntas executam uma tarefa. Você utiliza startTime, endTime, isPaused para agendar e executar pipelines. | Pipelines são grupos de atividades que são realizadas nos dados. No entanto, o agendamento das atividades no pipeline foi separado em novos recursos de gatilho. Pense em pipelines na versão atual do Data Factory mais como "unidades de fluxo de trabalho" que você agenda separadamente por meio de gatilhos. <br/><br/>Os pipelines não possuem “períodos” de execução de tempo na versão atual do Data Factory. Os conceitos de startTime, endTime e isPaused do Data Factory V1 não estão mais presentes na versão atual do Data Factory. Para saber mais, consulte [Execução e gatilhos de pipelines](concepts-pipeline-execution-triggers.md) e [Pipelines e atividades](concepts-pipelines-activities.md). |
| Atividades | Atividades definem ações a serem executadas em seus dados dentro de um pipeline. Há suporte para atividades de movimentação de dados (atividade de cópia) e atividades de transformação de dados (como Hive, Pig e MapReduce). | Na versão atual do Data Factory, as atividades ainda são ações definidas em um pipeline. A versão atual do data Factory introduz novas [atividades de fluxo de controle](concepts-pipelines-activities.md#control-activities). Você pode usar essas atividades no fluxo de controle (loop e ramificação). Atividades de movimentação de dados e de transformação de dados com suporte na V1 também têm suporte na versão atual. Você pode definir as atividades de transformação sem usar conjuntos de dados na versão atual. |
| Movimentação de dados híbridos e distribuição de atividades | Agora chamado de Integration Runtime, o [Gateway de Gerenciamento de Dados](v1/data-factory-data-management-gateway.md), oferecia suporte para a movimentação de dados entre o ambiente local e a nuvem.| O Gateway de Gerenciamento de Dados agora é chamado de Integration Runtime auto-hospedado. Ele fornece a mesma funcionalidade da versão 1. <br/><br/> O Integration Runtime do Azure-SSIS na versão atual do Data Factory também dá suporte à implantação e execução de pacotes SSIS (SQL Server Integration Services) na nuvem. Para obter mais informações, consulte [Tempo de execução de integração no Azure Data Factory](concepts-integration-runtime.md).|
| parâmetros | ND | Parâmetros são pares de chave-valor de configurações somente leitura definidos em pipelines. Você pode passar argumentos para os parâmetros ao executar manualmente o pipeline. Se você estiver usando um gatilho de agendador, o gatilho também poderá passar valores para os parâmetros. As atividades no pipeline consomem os valores de parâmetro.  |
| Expressões | O Data Factory V1 permite que você use funções e variáveis de sistema em consultas de seleção de dados e propriedades de atividade/conjunto de dados. | Na versão atual do Data Factory, você pode usar expressões em qualquer lugar em um valor de cadeia de caracteres JSON. Para saber mais, confira [Expressões e funções na versão atual do Data Factory](control-flow-expression-language-functions.md).|
| Execuções de pipeline | ND | Uma instância única da uma execução do pipeline. Por exemplo, digamos que você tem um pipeline que é executado às 8h, 9h e 10h. Haveria três execuções de pipeline separadas nesse caso. Cada execução de pipeline possui uma ID de execução de pipeline exclusiva. A ID de execução de pipeline é um GUID que define exclusivamente essa execução de pipeline específica. As execuções de pipeline normalmente são instanciadas por meio da passagem de argumentos para parâmetros que são definidos nos pipelines. |
| Execuções de atividade | ND | Uma instância de uma execução de atividade em um pipeline. | 
| Execuções de gatilho | ND | Uma instância de execução de gatilho. Para saber mais, confira [Gatilhos](concepts-pipeline-execution-triggers.md). |
| Agendamento | O agendamento é baseado em horários de início/término do pipeline e na disponibilidade do conjunto de dados. | Gatilho de agendador ou execução por meio do agendador externo. Para obter mais informações, consulte [Execução e gatilhos de pipelines](concepts-pipeline-execution-triggers.md). |

As seções a seguir fornecem mais informações sobre os recursos da versão atual. 

## <a name="control-flow"></a>Controlar fluxo  
Para dar suporte a diferentes fluxos e padrões de integração no data warehouse moderno, a versão atual do Data Factory habilitou um novo modelo de pipeline de dados flexível que não está mais vinculado aos dados de série temporal. Alguns dos fluxos comuns que agora estão disponíveis antes não eram possíveis. Eles estão descritos nas seções a seguir.   

### <a name="chaining-activities"></a>Encadeando atividades
Na versão 1, você precisava configurar a saída de uma atividade como uma entrada de outra atividade para encadeá-las. Na versão atual, você pode encadear atividades em uma sequência dentro de um pipeline. Você pode usar a propriedade **dependsOn** em uma definição de atividade para encadeá-la com uma atividade de upstream. Para saber mais e obter um exemplo, confira os artigos [Pipelines e atividades](concepts-pipelines-activities.md#multiple-activities-in-a-pipeline) e [Ramificação e encadeamento de atividades](tutorial-control-flow.md). 

### <a name="branching-activities"></a>Atividades de ramificação
Na versão atual, você pode ramificar atividades dentro de um pipeline. A [atividade If-Condition](control-flow-if-condition-activity.md) fornece a mesma funcionalidade que uma instrução `if` fornece em linguagens de programação. Ela avalia um conjunto de atividades quando a condição é avaliada como `true` e outro conjunto de atividades quando a condição é avaliada como `false`. Para obter exemplos de ramificação de atividades, confira o tutorial [Ramificação e encadeamento de atividades](tutorial-control-flow.md).

### <a name="parameters"></a>parâmetros 
É possível definir parâmetros no nível do pipeline e passar argumentos enquanto você invoca o pipeline sob demanda ou de um gatilho. As atividades podem consumir os argumentos passados para o pipeline. Para saber mais, consulte [Pipelines e gatilhos](concepts-pipeline-execution-triggers.md). 

### <a name="custom-state-passing"></a>Passagem de estado personalizada
As saídas da atividade incluindo o estado podem ser consumidas por uma atividade posterior no pipeline. Por exemplo, na definição de JSON de uma atividade, você pode acessar a saída da atividade anterior usando a seguinte sintaxe: `@activity('NameofPreviousActivity').output.value`. Ao usar esse recurso você consegue compilar fluxos de trabalho nos quais os valores podem passar pelas atividades.

### <a name="looping-containers"></a>Contêineres de looping
A [atividade ForEach](control-flow-for-each-activity.md) define um fluxo de controle repetitivo no seu pipeline. Essa atividade é iterada em uma coleção e executa atividades especificadas em um loop. A implementação dessa atividade em loop é semelhante à estrutura em loop Foreach nas linguagens de programação. 

A atividade [Until](control-flow-until-activity.md) fornece a mesma funcionalidade que uma estrutura de loop do-until fornece em linguagens de programação. Ela executa um conjunto de atividades em um loop até que a condição associada à atividade seja avaliada como `true`. Especifique um valor de tempo limite para a atividade Until no Data Factory.  

### <a name="trigger-based-flows"></a>Fluxos baseados em gatilhos
Os pipelines podem ser disparados sob demanda (com base em evento, ou seja, postagem de blob) ou conforme um horário. O artigo [pipelines e gatilhos](concepts-pipeline-execution-triggers.md) possui informações detalhadas sobre gatilhos. 

### <a name="invoking-a-pipeline-from-another-pipeline"></a>Invocar um pipeline de outro pipeline
A atividade [Executar atividade de pipeline](control-flow-execute-pipeline-activity.md) permite que um pipeline do Data Factory invoque outro pipeline.

### <a name="delta-flows"></a>Fluxos delta
Um caso de uso importante nos padrões de ETL são os "carregamentos delta", nos quais somente os dados que foram alterados desde a última iteração de um pipeline são carregados. Novos recursos na versão atual, como [atividade de pesquisa](control-flow-lookup-activity.md), planejamento flexível e fluxo de controle, permitem esse caso de uso de uma maneira natural. Para obter um tutorial com instruções passo a passo, confira [Tutorial: Cópia incremental](tutorial-incremental-copy-powershell.md).

### <a name="other-control-flow-activities"></a>Outras atividades de fluxo de controle
A seguir estão outras atividades de fluxo de controle com suporte da versão atual do Data Factory. 

Atividade de controle | DESCRIÇÃO
---------------- | -----------
[Atividade ForEach](control-flow-for-each-activity.md) | Define um fluxo de controle repetitivo em seu pipeline. Esta atividade é usada para iterar em uma coleção e executa atividades especificadas em um loop. A implementação dessa atividade em loop é semelhante à estrutura em loop Foreach nas linguagens de programação.
[Atividade da Web](control-flow-web-activity.md) | Chama um ponto de extremidade REST personalizado de um pipeline do Data Factory. Você pode passar conjuntos de dados e serviços vinculados a serem consumidos e acessados pela atividade. 
[Atividade de pesquisa](control-flow-lookup-activity.md) | Lê ou pesquisa um valor de nome de tabela ou registro de qualquer fonte externa. Essa saída pode referenciada pelas atividades com êxito. 
[Atividade de obtenção de metadados](control-flow-get-metadata-activity.md) | Recupera os metadados de quaisquer dados no Azure Data Factory. 
[Atividade de espera](control-flow-wait-activity.md) | Pausa o pipeline por um período de tempo especificado.

## <a name="deploy-ssis-packages-to-azure"></a>Implantar pacotes do SSIS no Azure 
Você utiliza o SSIS do Azure se desejar mover suas cargas de trabalho de SSIS para a nuvem, criar um data factory usando a versão atual e provisionar um Integration Runtime do SSIS do Azure.

O Integration Runtime do SSIS do Azure é um cluster totalmente gerenciado de VMs do Azure (nós) dedicados à execução dos seus pacotes de SSIS na nuvem. Quando você provisiona o Integration Runtime do SSIS do Azure, pode usar as mesmas ferramentas que costuma usar para implantar pacotes SSIS em um ambiente de SSIS local. 

Por exemplo, você pode usar o SQL Server Data Tools ou o SQL Server Management Studio para implantar pacotes do SSIS nesse tempo de execução no Azure. Para obter as instruções passo a passo, confira o tutorial [Implantar os pacotes de serviços de integração do SQL Server no Azure](tutorial-create-azure-ssis-runtime-portal.md). 

## <a name="flexible-scheduling"></a>Agendamento flexível
Na versão atual do Data Factory, você não precisa definir agendas de disponibilidade do conjunto de dados. Você pode definir um recurso de gatilho que pode agendar pipelines de um paradigma de agendador de relógio. Você também pode passar parâmetros para pipelines de um gatilho para um modelo de agendamento e execução flexíveis. 

Os pipelines não possuem “períodos” de execução de tempo na versão atual do Data Factory. Os conceitos de startTime, endTime e isPaused do Data Factory V1 não existem na versão atual do Data Factory. Para saber mais informações sobre como compilar e depois agendar um pipeline na versão atual do Data Factory, confira [Execução e gatilhos de pipeline](concepts-pipeline-execution-triggers.md).

## <a name="support-for-more-data-stores"></a>Suporte para mais armazenamentos de dados
A versão atual oferece suporte à cópia de dados entre mais armazenamentos de dados do que a versão 1. Para obter uma lista de armazenamentos de dados com suporte, consulte os seguintes artigos:

- [Versão 1 - armazenamentos de dados com suporte](v1/data-factory-data-movement-activities.md#supported-data-stores-and-formats)
- [Versão atual - armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats)

## <a name="support-for-on-demand-spark-cluster"></a>Suporte a cluster do Spark sob demanda
A versão atual oferece suporte à criação de um cluster do Azure HDInsight Spark sob demanda. Para criar um cluster do Spark sob demanda, especifique o tipo de cluster como Spark em sua definição de serviço vinculado do HDInsight sob demanda. Em seguida, configure a atividade do Spark em seu pipeline para usar esse serviço vinculado. 

Em tempo de execução, quando a atividade é executada, o serviço do Data Factory cria automaticamente o cluster do Spark para você. Para obter mais informações, consulte os seguintes artigos:

- [Atividade do Spark na versão atual do Data Factory](transform-data-using-spark.md)
- [Serviço vinculado do Azure HDInsight sob demanda](compute-linked-services.md#azure-hdinsight-on-demand-linked-service)

## <a name="custom-activities"></a>Atividades personalizadas
Na versão 1, o código da atividade DotNet (personalizada) é implementado por meio da criação de um projeto de biblioteca de classes .NET, com uma classe que implementa o método Execute da interface IDotNetActivity. Portanto, é necessário escrever seu código personalizado no .NET Framework 4.5.2 e executá-lo em nós do Pool do Lote do Azure baseado no Windows. 

Para uma atividade personalizada na versão atual, não é necessário implementar uma interface .NET. É possível executar diretamente comandos, scripts e seu próprio código personalizado compilado como um executável. 

Para saber mais, consulte [Diferença entre a atividade personalizada no Data Factory e na versão 1](transform-data-using-dotnet-custom-activity.md#compare-v2-v1).

## <a name="sdks"></a>SDKs
 A versão atual do Data Factory fornece um conjunto mais completo de SDKs que podem ser usados para criar, gerenciar e monitorar pipelines.

- **SDK .NET**: O SDK do .NET está atualizado na versão atual.

- **PowerShell**: Os cmdlets do PowerShell estão atualizados na versão atual. Os cmdlets da versão atual têm **DataFactoryV2** no nome, por exemplo: Get-AzDataFactoryV2. 

- **SDK do Python**: Esse SDK é novo na versão atual.

- **API REST**: A API REST está atualizada na versão atual. 

Os SDKs que foram atualizados para a versão atual não são retrocompatíveis com clientes na versão 1. 

## <a name="authoring-experience"></a>Experiência de criação

| &nbsp; | V2 | V1 |
| ------ | -- | -- | 
| Portal do Azure | [Sim](quickstart-create-data-factory-portal.md) | Não |
| Azure PowerShell | [Sim](quickstart-create-data-factory-powershell.md) | [Sim](data-factory-build-your-first-pipeline-using-powershell.md) |
| SDK .NET | [Sim](quickstart-create-data-factory-dot-net.md) | [Sim](data-factory-build-your-first-pipeline-using-vs.md) |
| API REST | [Sim](quickstart-create-data-factory-rest-api.md) | [Sim](data-factory-build-your-first-pipeline-using-rest-api.md) |
| SDK do Python | [Sim](quickstart-create-data-factory-python.md) | Não |
| Modelo do Resource Manager | [Sim](quickstart-create-data-factory-resource-manager-template.md) | [Sim](data-factory-build-your-first-pipeline-using-arm.md) | 

## <a name="roles-and-permissions"></a>Funções e permissões

A função de Colaborador de Data Factory versão 1 pode ser usada para criar e gerenciar recursos da versão atual do Data Factory. Para obter mais informações, consulte [Colaborador do Data Factory](../role-based-access-control/built-in-roles.md#data-factory-contributor).

## <a name="monitoring-experience"></a>Experiência de monitoramento
Na versão atual, você também pode monitorar os data factories usando o [Azure Monitor](monitor-using-azure-monitor.md). Os novos cmdlets do PowerShell dão suporte ao monitoramento de [tempos de execução de integração](monitor-integration-runtime.md). A versão 1 e a versão 2 oferecem suporte ao monitoramento visual por meio do aplicativo de monitoramento, que pode ser iniciado no Portal do Azure.


## <a name="next-steps"></a>Próximas etapas
Saiba como criar um data factory seguindo as instruções passo a passo dos seguintes inícios rápidos: [PowerShell](quickstart-create-data-factory-powershell.md), [.NET](quickstart-create-data-factory-dot-net.md), [Python](quickstart-create-data-factory-python.md) e [API REST](quickstart-create-data-factory-rest-api.md). 
