---
title: Azure Data Factory - Perguntas frequentes | Microsoft Docs
description: Perguntas frequentes sobre o Azure Data Factory.
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: monicar
ms.assetid: 532dec5a-7261-4770-8f54-bfe527918058
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/23/2017
ms.author: shlo
ms.openlocfilehash: 95b088d2fdc331dc33e973172d32892693fcb648
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-data-factory---frequently-asked-questions"></a>Azure Data Factory - Perguntas frequentes
Este artigo se aplica à versão 2 do serviço do Azure Data Factory. Ele fornece uma lista de perguntas frequentes (FAQ), com respostas.  

## <a name="what-is-azure-data-factory"></a>O que é o Data Factory do Azure? 
O Data Factory é um serviço de integração de dados baseado em nuvem totalmente gerenciado que automatiza a movimentação e a transformação dos dados. Assim como uma fábrica que usa equipamentos para pegar matérias-primas e transformá-las em produtos, o Azure Data Factory orquestra serviços existentes que coletam dados brutos e os transformam em informações para uso imediato. O Azure Data Factory permite que você crie fluxos de trabalho orientados a dados para mover dados entre armazenamentos de dados local e na nuvem, além de processar/transformar dados usando serviços de computação, como Azure HDInsight, Azure Data Lake Analytics e tempo de execução de integração do SQL Server Integration Services (SSIS). O Data Factory dá a opção de executar o processamento de dados em um serviço de nuvem baseado no Azure ou utilizar o seu próprio ambiente de computação auto-hospedado como SSIS, SQL Server e Oracle. Depois de criar um pipeline que executa a ação que você precisa, você pode programá-lo para ser executado periodicamente (por hora, diariamente, semanalmente etc) ou acionar o pipeline em uma ocorrência de evento. Para obter mais informações, consulte [Introdução ao Azure Data Factory](introduction.md).

## <a name="whats-different-in-version-2"></a>O que é diferente na versão 2?
O Azure Data Factory versão 2 foi criado com base no serviço de transformação e na movimentação de dados do Azure Data Factory original, se expandindo a um conjunto mais amplo de cenários de integração de dados em nuvem. O Azure Data Factory Versão 2 oferece os seguintes recursos:

- Fluxo de controle e Escala
- Implantação e execução de pacotes SSIS no Azure

Após o lançamento da versão 1, reconhecemos que os clientes precisam criar cenários de integração de dados híbridos complexos que exijam a movimentação de dados e o processamento local, na nuvem e nas VMs em nuvem. Esses requisitos trouxeram a necessidade de transferir e processar dados em ambientes seguros de VNET e de escalar horizontalmente com capacidade de processamento sob demanda.

À medida que os pipelines de dados se tornam uma parte fundamental de uma estratégia de análise de negócios, percebemos que essas atividades de dados críticas exigem agendamento flexível para dar suporte aos carregamentos de dados incrementais e às execuções disparadas por eventos. Por fim, conforme as complexidades dessas orquestrações aumentam, o mesmo acontece com os requisitos do serviço para haver suporte aos paradigmas de fluxo de trabalho comuns, incluindo ramificação, looping e processamento condicional.

Com a versão 2, você também pode migrar os pacotes existentes do SSIS (SQL Server Integration Services) para a nuvem com a finalidade de migrar o SSIS por lift-and-shift como um serviço do Azure gerenciado no ADF, utilizando um novo recurso de “IR” (Tempos de Execução de Integração). Ao criar um IR do SSIS na versão 2, você terá a capacidade de executar, gerenciar, monitorar e criar pacotes do SSIS na nuvem.

### <a name="control-flow-and-scale"></a>Fluxo de controle e escala 
Para dar suporte aos fluxos e padrões de integração diversos no moderno data warehouse, o Data Factory habilitou um novo modelo de pipeline de dados flexível que não está mais vinculado aos dados de série temporal. Com esta versão, você pode modelar condicionais e ramificações no controle de fluxo de um pipeline de dados e transmitir parâmetros de modo preciso nos fluxos e entre eles.

Agora você tem a liberdade de modelar qualquer estilo de fluxo necessário para a integração de dados e que será despachado sob demanda ou repetidamente de acordo com um cronograma. Alguns fluxos comuns que estão habilitados agora, mas que não eram possíveis anteriormente, são:   

- Fluxo de Controle:
    - Atividades de encadeamento em uma sequência em um pipeline
    - Ramificação de atividades em um pipeline
    - parâmetros
        - Os parâmetros podem ser definidos no nível do pipeline, e os argumentos podem ser transmitidos durante a invocação do pipeline sob demanda ou de um gatilho
        - As atividades podem consumir os argumentos transmitidos para o pipeline
    - Passagem de estado personalizada
        - Os resultados da atividade, incluindo o estado, podem ser consumidos por uma atividade subsequente no pipeline
    - Contêineres de Looping
        - For-each 
- Fluxos baseados em gatilho
    - Os pipelines podem ser acionados por hora ou sob demanda
- Fluxos delta
    - Use parâmetros e defina seus limites para cópia de delta ao mover tabelas de referência ou dimensão de um relational store local ou na nuvem para carregar os dados no lake 

Para obter mais informações, consulte o [tutorial: fluxo de controle](tutorial-control-flow.md).

### <a name="deploy-ssis-packages-to-azure"></a>Implantar pacotes do SSIS no Azure 
Se desejar mover suas cargas de trabalho de SSIS, você poderá criar um data factory versão 2 e provisionar um IR (tempo de execução de integração) do SSIS do Azure. O IR do SSIS do Azure é um cluster totalmente gerenciado das VMs do Azure (nós) dedicado para executar os pacotes de SSIS na nuvem. Para obter instruções passo a passo, consulte o tutorial: [Implantar pacotes do SSIS no Azure](tutorial-deploy-ssis-packages-azure.md). 
 

### <a name="sdks"></a>SDKs
Se você for um usuário avançado e estiver procurando por uma interface programática, a versão 2 fornecerá um conjunto avançado de SDKs que podem ser usados para criar, gerenciar e monitorar pipelines usando seu IDE favorito.

- SDK .NET - O SDK .NET é atualizado para a versão 2. 
- PowerShell - Os cmdlets do PowerShell são atualizados para a versão 2. Os cmdlets da versão 2 têm **DataFactoryV2** no nome. Por exemplo: Get-AzureRmDataFactoryV2. 
- Python SDK - Este SDK é novo na versão 2.
- API REST - A API REST é atualizada para a versão 2.  

Os SDKs que são atualizados para a versão 2 não são retrocompatíveis com clientes na versão 1. 

### <a name="monitoring"></a>Monitoramento
Atualmente, a versão 2 oferece suporte ao monitoramento de data factories usando apenas SDKs. O portal ainda não é compatível com monitoramento de data factories da versão 2. 

## <a name="what-is-integration-runtime"></a>O que é tempo de execução de integração?
O IR (tempo de execução de integração) é a infraestrutura de computação usada pelo Azure Data Factory para fornecer as seguintes funcionalidades de integração de dados entre diferentes ambientes de rede:

- **Movimentação de dados**: mover dados entre armazenamentos de dados em rede pública e armazenamentos de dados em rede privada (rede privada local ou virtual). Ele fornece suporte para conectores internos, conversão de formato, mapeamento de coluna e transferência de dados de alto desempenho e escalonáveis.
- **Expedição de atividades**: expedir e monitorar atividades de transformação executadas em uma variedade de serviços de computação, como o Azure HDInsight, o Azure Machine Learning, o Banco de Dados SQL do Azure, o SQL Server e muitos mais.
- **Execução de pacote do SSIS**: executar nativamente pacotes do SSIS (SQL Server Integration Services) em um ambiente de computação gerenciada do Azure.

Você pode implantar uma ou mais instâncias do IR conforme necessário para movimentar e transformar os dados.  O IR pode ser executado na rede pública do Azure ou em uma rede privada (local, Rede Virtual do Azure ou nuvem privada virtual (VPC) do Amazon Web Services). 

Para obter mais informações, consulte [Tempo de execução de integração no Azure Data Factory](concepts-integration-runtime.md).

## <a name="what-is-the-limit-on-the-number-of-integration-runtimes"></a>O que é o limite do número de tempos de execução de integração?
Não há nenhum limite rígido de quantas instâncias de tempos de execução de integração você pode ter em um data factory. Entretanto, há um limite de número de núcleos de máquina virtual (VM) que o tempo de execução de integração pode usar por assinatura para a execução de pacotes do SSIS. Para saber mais, confira [Limites do Data Factory](../azure-subscription-service-limits.md#data-factory-limits).

## <a name="when-to-use-version-2-rather-than-version-1"></a>Quando usar a versão 2 em vez da versão 1? 
Se você for novo no Azure Data Factory, inicie diretamente com a versão 2. Se você já estiver usando a versão 1, recrie seus data factories na versão 2.

> [!WARNING]
> A versão 2 o Data Factory é uma versão prévia, não disponível para o público geral. Portanto, ela não se enquadra nos mesmos compromissos de Contrato de Nível de Serviço (SLA) da versão 1 do Data Factory, que está disponível para o público geral. 

## <a name="what-are-the-top-level-concepts-of-version-2"></a>Quais são os conceitos de nível superior da versão 2?
Uma assinatura do Azure pode ter um ou mais instâncias do Azure Data Factory (ou fábricas de dados). O Azure Data Factory é composto por quatro componentes principais que trabalham juntos para fornecer a plataforma na qual você pode compor fluxos de trabalho orientados a dados com etapas para movimentação e transformação dos dados.

### <a name="pipeline"></a>Pipeline
Um data factory pode ter um ou mais pipelines. O pipeline é um agrupamento lógico de atividades para realizar uma unidade de trabalho. Juntas, as atividades em um pipeline executam uma tarefa. Por exemplo, um pipeline pode conter um grupo de atividades que recebe dados de um blob do Azure e, em seguida, executar uma consulta de Hive em um cluster de HDInsight para particionar os dados. A vantagem disso é que o pipeline permite que você gerencie atividades como um conjunto, em vez de cada uma individualmente. As atividades em um pipeline podem ser encadeadas para operarem de modo sequencial ou de forma independente em paralelo

### <a name="activity"></a>Atividade
As atividades representam uma etapa de processamento em um pipeline. Por exemplo, você pode usar uma atividade de cópia para copiar dados de um repositório de dados para outro. Da mesma forma, você pode usar uma atividade do Hive que executa uma consulta de Hive em um cluster do Azure HDInsight para transformar ou analisar seus dados. O Data Factory dá suporte a três tipos de atividades: atividades de movimentação de dados, atividades de transformação de dados e atividades de controle

### <a name="datasets"></a>CONJUNTOS DE DADOS
Os conjuntos de dados representam as estruturas de dados nos repositórios de dados, que simplesmente apontam ou fazem referência aos dados que você deseja usar em suas atividades como entradas ou saídas. 

### <a name="linked-services"></a>Serviços vinculados
Serviços vinculados são como cadeias de conexão, que definem as informações de conexão necessárias para o Data Factory para se conectar a recursos externos. Pense dessa maneira - um serviço vinculado define a conexão à fonte de dados e um conjunto de dados representa a estrutura dos dados. Por exemplo, um serviço vinculado do Azure Storage especifica a cadeia de conexão para conectar-se à conta do Data Factory. E, um conjunto de dados de Blob do Azure especifica o contêiner de blob e a pasta que contém os dados.

Serviços vinculados são usados para duas finalidades no Data Factory:

- Para representar um **repositório de dados** , incluindo, mas não se limitando a um SQL Server local, banco de dados Oracle, compartilhamento de arquivos ou uma conta de Armazenamento de Blobs do Azure. Para obter uma lista dos armazenamentos de dados com suporte, consulte o artigo [Copy activity](copy-activity-overview.md) (Atividade de cópia).
- Para representar um **recurso de computação** que pode hospedar a execução de uma atividade. Por exemplo, a atividade HDInsightHive é executada em um cluster Hadoop do HDInsight. Para obter uma lista das atividades de transformação e dos ambientes de computação com suporte, consulte o artigo [Transform data](transform-data.md) (Transformar dados).

### <a name="triggers"></a>Gatilhos
Os gatilhos representam a unidade de processamento que determina quando uma execução de pipeline precisa ser inicializada. Há diferentes tipos de gatilhos para diferentes tipos de eventos: para a versão prévia, há suporte para o gatilho do agendador cronometrado. 


### <a name="pipeline-runs"></a>Execuções de pipeline
Uma execução de pipeline é uma instância da execução do pipeline. As execuções de pipeline normalmente são instanciadas por meio da transmissão de argumentos para os parâmetros definidos em pipelines. Os argumentos podem ser transmitidos manualmente ou na definição do gatilho

### <a name="parameters"></a>parâmetros
Os parâmetros são pares chave-valor da configuração somente leitura.  Os parâmetros são definidos no pipeline e os argumentos para os parâmetros definidos são transmitidos durante a execução por um contexto de execução criado por um gatilho ou por um pipeline executado manualmente. As atividades no pipeline consomem os valores de parâmetro.
Um conjunto de dados é um parâmetro fortemente tipado e uma entidade reutilizável/referenciável. Uma atividade pode fazer referência a conjuntos de dados e pode consumir as propriedades estabelecidas na definição do conjunto de dados

Um serviço vinculado também é um parâmetro fortemente tipado que contém as informações de conexão para um armazenamento de dados ou para um ambiente de computação. Ele também é uma entidade reutilizável/referenciável.

### <a name="control-flow"></a>Controlar fluxo
Orquestração de atividades do pipeline que inclui o encadeamento de atividades em uma sequência, em uma ramificação e em parâmetros que podem ser definidos no nível do pipeline, e os argumentos transmitidos, durante a invocação do pipeline sob demanda ou de um gatilho. Também inclui transmissão de estado personalizada e contêineres de looping, ou seja, iteradores for-each.


Para obter mais informações sobre os conceitos do Data Factory, confira os seguintes artigos:

- [Conjuntos de dados e serviços vinculados](concepts-datasets-linked-services.md)
- [Pipelines e atividades](concepts-pipelines-activities.md)
- [Tempo de execução de integração](concepts-integration-runtime.md)

## <a name="what-is-the-pricing-model-for-data-factory"></a>O que é o modelo de preços Data Factory?
Confira a página [Detalhes de preços do Data Factory](https://azure.microsoft.com/pricing/details/data-factory/) para ver os detalhes de preços para o Azure Data Factory.

## <a name="what-regions-support-azure-data-factory-version-2"></a>Quais regiões oferecem suporte ao Azure Data Factory versão 2?
No momento, você pode criar data factories da versão 2 nas regiões Leste dos EUA e Leste dos EUA 2. No entanto, um data factory pode usar o tempo de execução de integração em outra região para mover dados entre armazenamentos de dados, expedir atividades para serviços de computação ou expedir pacotes do SSIS.  Para saber mais, confira [Locais do Data Factory](concepts-integration-runtime.md#integration-runtime-location).

## <a name="how-can-i-stay-up-to-date-with-information-about-data-factory"></a>Como posso me manter informado sobre o Data Factory?
Use os seguintes sites para se manter informado sobre o Azure Data Factory:

- [Blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Home page da documentação](/azure/data-factory)
- [Home page do produto](https://azure.microsoft.com/services/data-factory/)

## <a name="technical-deep-dive"></a>Análise técnica aprofundada 

### <a name="can-i-have-version-1-and-version-2-pipelines-run-side-by-side"></a>É possível ter pipelines da versão 1 e da versão 2 executados lado a lado?
Não. Os data factories da versão 2 ou da versão 1 não podem ter entidades (serviços vinculados, conjuntos de dados, pipelines, etc.) da outra versão.   

### <a name="do-i-still-need-to-define-datasets-in-version-2"></a>Ainda preciso definir conjuntos de dados na versão 2?
O conjunto de dados não é mais uma entidade obrigatória para a maioria das atividades. Ele é necessário para Cópia, Machine Learning, Pesquisa, Validação e atividades personalizadas que usem o esquema e outras informações de metadados no conjunto de dados para a transformação. O restante das atividades não exigem mais conjuntos de dados.

### <a name="can-i-chain-two-activities-without-a-dataset-in-version-2"></a>Posso encadear duas atividades sem um conjunto de dados na versão 2?
Sim. Você pode encadear atividades na versão 2 sem a necessidade de conjuntos de dados. O encadeamento é feito usando a propriedade **dependsOn** na definição de JSON do pipeline. 

### <a name="are-all-the-version-1-activities-supported-in-version-2"></a>Todas as atividades da versão 1 são compatíveis com a versão 2? 
Sim, há suporte para todas as atividades da versão 1

### <a name="how-can-i-schedule-a-version-2-pipeline"></a>Como faço para agendar um pipeline da versão 2? 
Você pode usar o disparador do agendador para agendar um pipeline da versão 2. Ele usa um agendamento de calendário de relógio e permite que usuários agendem pipelines periodicamente ou usando padrões recorrentes baseados no calendário (por exemplo, toda segunda-feira às 18h e toda quinta-feira às 21h). Para obter mais informações, consulte [Execução e gatilhos de pipelines](concepts-pipeline-execution-triggers.md).

### <a name="can-i-pass-parameters-to-a-pipeline-run-in-version-2"></a>Posso passar parâmetros para uma execução de pipeline na versão 2?
Sim, os parâmetros são conceito de primeira classe de nível superior na versão 2. Você pode definir parâmetros no nível do pipeline e passar argumentos durante a execução do pipeline sob demanda ou usando um gatilho.  

### <a name="can-i-define-default-values-for-the-pipeline-parameters"></a>É possível definir valores padrão para os parâmetros do pipeline? 
Sim. Você pode definir valores padrão para os parâmetros nos pipelines. 

### <a name="can-an-activity-in-a-pipeline-consume-arguments-passed-to-a-pipeline-run"></a>Uma atividade em um pipeline pode consumir argumentos passados para uma execução de pipeline? 
Sim. Cada atividade no pipeline pode consumir o valor do parâmetro passado para a execução do pipeline com o constructo `@parameter`. 

### <a name="can-an-activity-output-property-be-consumed-in-another-activity"></a>Uma propriedade de saída de uma atividade pode ser consumida em outra atividade? 
Sim. Uma saída de atividade pode ser consumida em uma atividade subsequente com o constructo @activity.
 
### <a name="how-do-i-gracefully-handle-null-values-in-an-activity-output"></a>Como tratar normalmente o valores nulos em uma saída de atividade? 
Você pode usar o constructo `@coalesce` nas expressões para manipular valores nulos normalmente. 

### <a name="can-i-use-retries-timeouts-at-the-activity-level-in-version-2"></a>É possível usar novas tentativas, tempos limite no nível da atividade na versão 2?
Sim. Você pode configurar novas tentativas e tempo limite no nível da atividade para controlar a execução de atividades na versão 2, como na versão 1. 

## <a name="next-steps"></a>Próximas etapas
Para obter instruções passo a passo para criar um data factory da versão 2, consulte os tutoriais a seguir:

- [Início rápido: criar um data factory](quickstart-create-data-factory-dot-net.md)
- [Tutorial: copiar dados na nuvem](tutorial-copy-data-dot-net.md)

