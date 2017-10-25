---
title: "Introdução ao Data Factory, um serviço de integração de dados | Microsoft Docs"
description: "Saiba o que é o Azure Data Factory: um serviço de integração de dados de nuvem que orquestra e automatiza a movimentação e a transformação dos dados."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: monicar
ms.assetid: cec68cb5-ca0d-473b-8ae8-35de949a009e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/14/2017
ms.author: shlo
robots: noindex
ms.openlocfilehash: 8d4d574dee4b993d8a464482b244e1f63ade8a57
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-azure-data-factory"></a>Introdução à Fábrica de Dados do Azure 
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1 – já disponível](data-factory-introduction.md)
> * [Versão 2 – Versão prévia](../introduction.md)

> [!NOTE]
> Este artigo se aplica à versão 1 do Data Factory, que está com GA (disponibilidade geral). Se você está usando a versão 2 do serviço Data Factory, que está em versão prévia, consulte [Introdução ao Data Factory V2](../introduction.md).


## <a name="what-is-azure-data-factory"></a>O que é o Data Factory do Azure?
No mundo de grandes volumes de dados, como os dados existentes são usados nos negócios? É possível enriquecer os dados gerados na nuvem usando dados de referência de fontes de dados locais ou de outras fontes de dados diferentes? Por exemplo, uma empresa de jogos coleta muitos logs gerados por jogos na nuvem. Ela deseja analisar esses logs para obter informações sobre as preferências do cliente, dados demográficos, comportamento de uso, etc., para identificar oportunidades de venda e vendas cruzadas, desenvolver novos recursos atraentes para fomentar o crescimento do negócio e proporcionar uma melhor experiência para os clientes. 

Para analisar esses logs, a empresa precisa usar os dados de referência como informações do cliente, informações sobre o jogo, informações de campanha de marketing que estão em um repositório de dados local. Portanto, a empresa quer ingerir dados de log do armazenamento de dados de nuvem e dados de referência do repositório de dados local. Em seguida, processa os dados usando o Hadoop na nuvem (Azure HDInsight) e publica os dados de resultado em um data warehouse de nuvem, como o SQL Data Warehouse do Azure ou um repositório de dados local como o SQL Server. Ela deseja executar esse fluxo de trabalho uma vez por semana. 

O que é necessário é uma plataforma que permite que a empresa crie um fluxo de trabalho que possa ingerir dados de repositórios de dados locais e em nuvem e transformar ou processar dados usando os serviços de computação existentes, como o Hadoop e publicar os resultados em um repositório de dados local ou em nuvem para consumo pelos aplicativos de BI. 

![Visão geral do Data Factory](media/data-factory-introduction/what-is-azure-data-factory.png) 

O Azure Data Factory é a plataforma para esses tipos de cenários. É um **serviço de integração de dados com baseado em nuvem que permite que você crie fluxos de trabalho orientados a dados na nuvem para orquestrar e automatizar a movimentação de dados e a transformação de dados**. Usando o Azure Data Factory, você pode criar e agendar fluxos de trabalho orientados a dados (chamados de pipelines) que podem ingerir dados de repositórios de dados diferentes, processar/transformr os dados usando serviços de computação como o Hadoop do Azure HDInsight, Spark, Azure Data Lake Analytics e Azure Machine Learning e publicar os dados de saída em repositórios de dados como o SQL Data Warehouse do Azure para consumo pelos aplicativos de business intelligence (BI).  

Essa é uma plataforma mais de Extrair e Carregar (EL) e, em seguida, Transformar e Carregar (TL) do que uma plataforma de Extrair, Carregar e Transformar (ETL) tradicional. As transformações que são executadas são para transformar/processar dados usando os serviços de computação em vez de executar transformações como aquelas para adicionar colunas derivadas, contagem do número de linhas, classificação de dados, etc. 

Atualmente, no Azure Data Factory, os dados que são consumidos e produzidos pelos fluxos de trabalho são **dados divididos pelo tempo** (por hora, dia, semana, etc.). Por exemplo, um pipeline pode ler dados de entrada, processar dados e produzir dados de saída uma vez por dia. Você também pode executar um fluxo de trabalho apenas uma vez.  
  

## <a name="how-does-it-work"></a>Como ele funciona? 
Os pipelines (fluxos de trabalho orientados a dados) no Azure Data Factory normalmente executam as três etapas a seguir:

![As três etapas do Azure Data Factory](media/data-factory-introduction/three-information-production-stages.png)

### <a name="connect-and-collect"></a>Conectar e coletar
As empresas possuem dados de diferentes tipos, localizados em diferentes fontes. A primeira etapa na criação de um sistema de produção de informações é se conectar a todas as fontes necessárias de dados e processamento, como serviços SaaS, os serviços web compartilhamentos, FTP, arquivos e mover os dados conforme necessário para um local centralizado para processamento posterior.

Sem o Data Factory, as empresas devem criar componentes de movimentação de dados personalizados ou gravar serviços personalizados para integrar essas fontes de dados e processamento. É caro e difícil integrar e manter esses sistemas, além disso, geralmente, eles não possuem o monitoramento e os alertas de nível corporativo e os controles que podem oferecer um serviço totalmente gerenciado.

Com o Data Factory, você pode usar a Atividade de Cópia em um pipeline de dados para mover dados de repositórios de dados locais e na nuvem para um repositório de dados centralizado na nuvem para análise posterior. Por exemplo, você pode coletar dados em um Azure Data Lake Store e transformar os dados posteriormente usando um serviço de computação do Azure Data Lake Analytics. Ou, coletar dados em Armazenamento de Blobs do Azure e transformam dados posteriormente usando um cluster de Hadoop do Azure HDInsight.

### <a name="transform-and-enrich"></a>Transformar e enriquecer
Depois que os dados estiverem presentes em um repositório centralizado de dados na nuvem, você deseja que os dados coletados, sejam processados ou transformados usando serviços de computação como Hadoop do HDInsight, Spark, Data Lake Analytics e Machine Learning. Você quer produzir confiavelmente os dados transformados em uma agenda controlada e passível de manutenção para alimentar os ambientes de produção com dados confiáveis. 

### <a name="publish"></a>Publicar 
Forneça dados transformados de fontes na nuvem para locais como o SQL Server, ou os mantenha em suas fontes de armazenamento em nuvem para o consumo por BI (business intelligence), ferramentas analíticas e outros aplicativos.

## <a name="key-components"></a>Principais componentes
Uma assinatura do Azure pode ter um ou mais instâncias do Azure Data Factory (ou fábricas de dados). O Azure Data Factory é composto por quatro componentes principais que trabalham juntos para fornecer a plataforma na qual você pode compor fluxos de trabalho orientados a dados com etapas para movimentação e transformação dos dados. 

### <a name="pipeline"></a>Pipeline
Um data factory pode ter um ou mais pipelines. Um pipeline é um grupo de atividades. Juntas, as atividades em um pipeline executam uma tarefa. Por exemplo, um pipeline pode conter um grupo de atividades que recebe dados de um blob do Azure e, em seguida, executar uma consulta de Hive em um cluster de HDInsight para particionar os dados. A vantagem disso é que o pipeline permite que você gerencie atividades como um conjunto, em vez de cada uma individualmente. Por exemplo, você pode implantar e agendar o pipeline, em vez de atividades de forma independente. 

### <a name="activity"></a>Atividade
Um pipeline pode ter uma ou mais atividades. As Atividades definem as ações a serem realizadas em seus dados. Por exemplo, você pode usar uma atividade de cópia para copiar dados de um repositório de dados para outro. Da mesma forma, você pode usar uma atividade do Hive que executa uma consulta de Hive em um cluster do Azure HDInsight para transformar ou analisar seus dados. O Data Factory dá suporte a dois tipos de atividades: atividades de movimentação de dados e atividades de transformação de dados.

### <a name="data-movement-activities"></a>Atividades de movimentação de dados
A Atividade de Cópia no Data Factory copia os dados de um repositório de dados de origem para um repositório de dados de coletor. A Data Factory dá suporte aos repositórios de dados a seguir. Os dados de qualquer origem podem ser gravados em qualquer coletor. Clique em um repositório de dados para saber como copiar dados dentro e fora do repositório.

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

Para obter mais informações, confira o artigo [Atividades de movimentação de dados](data-factory-data-movement-activities.md).

### <a name="data-transformation-activities"></a>Atividades de transformação de dados
[!INCLUDE [data-factory-transformation-activities](../../../includes/data-factory-transformation-activities.md)]

Para obter mais informações, confira o artigo [Atividades de transformação de dados](data-factory-data-transformation-activities.md).

### <a name="custom-net-activities"></a>Atividades personalizadas do .NET
Se você precisar mover dados de/para um repositório de dados a que a Atividade de Cópia não dê suporte, ou transformar seus dados usando sua própria lógica, crie uma **atividade personalizada do .NET**. Para obter detalhes sobre como criar e usar uma atividade personalizada, confira [Usar atividades personalizadas em um pipeline do Azure Data Factory](data-factory-use-custom-activities.md).

### <a name="datasets"></a>Conjunto de dados
Uma atividade aceita zero ou mais conjuntos de dados como entrada e produz um ou mais conjuntos de dados como saídas. Os conjuntos de dados representam as estruturas de dados nos repositórios de dados, que simplesmente apontam ou fazem referência aos dados que você deseja usar em suas atividades como entradas ou saídas. Por exemplo, um conjunto de dados de Blob do Azure especifica o contêiner de blobs e a pasta no Armazenamento de Blobs do Azure, de onde o pipeline deve ler os dados. Ou, um conjunto de dados de tabela do Azure SQL especifica a tabela na qual os dados de saída são gravados pela atividade. 

### <a name="linked-services"></a>Serviços vinculados
Serviços vinculados são como cadeias de conexão, que definem as informações de conexão necessárias para o Data Factory para se conectar a recursos externos. Pense dessa maneira - um serviço vinculado define a conexão à fonte de dados e um conjunto de dados representa a estrutura dos dados. Por exemplo, um serviço vinculado do Azure Storage especifica a cadeia de conexão para conectar-se à conta do Data Factory. E, um conjunto de dados de Blob do Azure especifica o contêiner de blob e a pasta que contém os dados.   

Serviços vinculados são usados para duas finalidades no Data Factory:

* Para representar um **repositório de dados** , incluindo, mas não se limitando a um SQL Server local, banco de dados Oracle, compartilhamento de arquivos ou uma conta de Armazenamento de Blobs do Azure. Confira a seção [Atividades de movimentação de dados](#data-movement-activities) para obter uma lista de repositórios de dados com suporte.
* Para representar um **recurso de computação** que pode hospedar a execução de uma atividade. Por exemplo, a atividade HDInsightHive é executada em um cluster Hadoop do HDInsight. Confira a seção [Atividades de transformação de dados](#data-transformation-activities) para obter uma lista de ambientes de computação com suporte.

### <a name="relationship-between-data-factory-entities"></a>Relação entre entidades de Data Factory
![Diagrama: Data Factory, um serviço de integração de dados de nuvem - conceitos principais](./media/data-factory-introduction/data-integration-service-key-concepts.png)
**Figure 2.** Relações entre o Conjunto de dados, Atividade, Pipeline e Serviço vinculado

## <a name="supported-regions"></a>Regiões com suporte
No momento, você pode criar fábricas de dados nas regiões **Oeste dos EUA**, **Leste dos EUA** e **Europa Setentrional**. No entanto, uma fábrica de dados pode acessar repositórios de dados e serviços de computação em outras regiões do Azure para mover dados entre repositórios de dados ou processar dados usando serviços de computação.

O Azure Data Factory em si não armazena dados. Ele permite criar fluxos de trabalho controlados por dados para orquestrar a movimentação de dados entre [armazenamentos de dados com suporte](#data-movement-activities) e o processamento de dados usando [serviços de computação](#data-transformation-activities) em outras regiões ou em um ambiente local. Ele também permite [monitorar e gerenciar fluxos de trabalho](data-factory-monitor-manage-pipelines.md) usando mecanismos programáticos e de interface do usuário.

Embora o Data Factory só esteja disponível nas regiões **Oeste dos EUA**, **Leste dos EUA** e **Europa Setentrional**, o serviço que capacita a movimentação de dados no Data Factory está disponível [globalmente](data-factory-data-movement-activities.md#global) em várias regiões. Se um repositório de dados estiver por trás de um firewall, um [Gateway de Gerenciamento de Dados](data-factory-move-data-between-onprem-and-cloud.md) instalado no ambiente local moverá os dados.

Por exemplo, digamos que seus ambientes de computação, como o cluster Azure HDInsight e o Azure Machine Learning, estejam ficando sem a região Europa Ocidental. Você pode criar e usar uma instância do Azure Data Factory na Europa Setentrional e usá-la para agendar trabalhos em seus ambientes de computação na Europa Ocidental. Leva alguns milissegundos para o Data Factory disparar o trabalho em seu ambiente de computação, mas o tempo de execução do trabalho em seu ambiente de computação não é alterado.

## <a name="get-started-with-creating-a-pipeline"></a>Introdução à criação de um pipeline
Você pode usar uma dessas ferramentas ou APIs para criar pipelines de dados no Azure Data Factory: 

- Portal do Azure
- Visual Studio
- PowerShell
- API do .NET
- API REST
- Modelo do Azure Resource Manager. 

Para saber como criar data factories com pipeline de dados, siga as instruções passo a passo nos tutoriais a seguir:

| Tutorial | Descrição |
| --- | --- |
| [Mover dados entre dois armazenamentos de dados em nuvem](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) |Neste tutorial, você cria um data factory com um pipeline que **move dados** do Armazenamento de Blobs para o banco de dados SQL. |
| [Transformar dados usando o cluster Hadoop](data-factory-build-your-first-pipeline.md) |Neste tutorial, você cria seu primeiro data factory no Azure com um pipeline de dados que **processa os dados** executando o script do Hive em um cluster Azure HDInsight (Hadoop). |
| [Mover dados entre um armazenamento de dados local e um armazenamento de dados em nuvem usando o Gateway de Gerenciamento de Dados](data-factory-move-data-between-onprem-and-cloud.md) |Neste tutorial, você cria um data factory com um pipeline que **move dados** de um banco de dados SQL Server **local** para um blob do Azure. Como parte do passo a passo, você instala e configura o Gateway de Gerenciamento de Dados em seu computador. |
