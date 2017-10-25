---
title: "Introdução ao Azure Data Factory | Microsoft Docs"
description: "Saiba mais sobre o Azure Data Factory, um serviço de integração de dados de nuvem que orquestra e automatiza a movimentação e a transformação dos dados."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/29/2017
ms.author: shlo
ms.openlocfilehash: ef7055342a04057acfba9dad350f654aa4de6096
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-azure-data-factory"></a>Introdução à Fábrica de Dados do Azure 
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1 – já disponível](v1/data-factory-introduction.md)
> * [Versão 2 – Versão prévia](introduction.md)

No mundo de Big Data, os dados brutos e desorganizados geralmente são armazenados em sistemas de armazenamento relacionais, não relacionais e de outros tipos. No entanto, por conta própria, os dados brutos não têm o contexto ou o significado apropriado para fornecerem informações significativas aos analistas, aos cientistas de dados e aos tomadores de decisões corporativas. É necessário que haja um serviço que possa habilitar a orquestração e a operacionalização de processos para refinar esses imensos armazenamentos de dados brutos em informações empresariais acionáveis. O Azure Data Factory é um serviço de nuvem gerenciado, desenvolvido para estes processos híbridos e complexos de ETL (extração, transformação e carregamento), ELT (extração, carregamento e transformação) e para projetos de integração de dados.

Por exemplo, imagine uma empresa de jogos que coleta petabytes dos logs produzidos pelos jogos na nuvem. Ela deseja analisar esses logs para obter informações sobre as preferências do cliente, os dados demográficos e o comportamento de uso para identificar oportunidades de vendas suplementares e cruzadas, desenvolver novos recursos atraentes, fomentar o crescimento do negócio e proporcionar uma experiência melhor para os clientes.

Para analisar esses logs, a empresa precisa usar os dados de referência, como as informações sobre o cliente, sobre o jogo e sobre a campanha de marketing, que estão em um armazenamento de dados local. A empresa deseja utilizar esses dados do armazenamento de dados local e combiná-los com os dados de log adicionais que ela tem em um armazenamento de dados na nuvem. Para extrair as informações, ela espera processar os dados recolhidos usando um cluster Spark na nuvem (HDInsight) e, por fim, publicar os dados transformados em um data warehouse na nuvem, como o SQL Data Warehouse do Azure, para criar um relatório sobre ele com facilidade. Este fluxo de trabalho deve ser automatizado, monitorado e gerenciado em uma agenda diária e executado no momento em que os arquivos forem descarregados em um contêiner de repositório de blobs.

O Azure Data Factory é a plataforma que resolve esses cenários de dados. É um **serviço de integração de dados com baseado em nuvem que permite que você crie fluxos de trabalho orientados a dados na nuvem para orquestrar e automatizar a movimentação de dados e a transformação de dados**. Usando o Azure Data Factory, você pode criar e agendar fluxos de trabalho orientados a dados (chamados de pipelines) que podem ingerir dados de repositórios de dados diferentes, processar/transformr os dados usando serviços de computação como o Hadoop do Azure HDInsight, Spark, Azure Data Lake Analytics e Azure Machine Learning e publicar os dados de saída em repositórios de dados como o SQL Data Warehouse do Azure para consumo pelos aplicativos de business intelligence (BI). Por fim, por meio do Azure Data Factory, os dados brutos podem ser organizados em armazenamentos de dados e em data lakes importantes para possibilitar melhores decisões corporativas.

![exibição de nível superior do Data Factory](media/introduction/big-picture.png)

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em versão prévia. Se você estiver usando a versão 1 do serviço Data Factory, que normalmente está disponível (GA), consulte [Introduction to Data Factory version 1](v1/data-factory-introduction.md) (Introdução ao Data Factory versão 1).

## <a name="how-does-it-work"></a>Como ele funciona?
Os pipelines (fluxos de trabalho controlados por dados) no Azure Data Factory normalmente executam as quatro etapas a seguir:

![Quatro etapas de um fluxo de trabalho controlado por dados](media/introduction/four-steps-of-a-workflow.png)

### <a name="connect-and-collect"></a>Conectar e coletar

As empresas têm dados de vários tipos localizados em diferentes fontes locais, na nuvem, estruturadas, não estruturadas e semi-estruturadas, todos chegando em intervalos e velocidades diferentes. A primeira etapa na criação de um sistema de produção de informações é se conectar a todas as fontes de dados e de processamento necessárias, como serviços de SaaS (software como serviço), bancos de dados, compartilhamentos de arquivos e serviços Web de FTP, e mover os dados, conforme necessário, para um local centralizado para processamento posterior.

Sem o Data Factory, as empresas devem criar componentes de movimentação de dados personalizados ou gravar serviços personalizados para integrar essas fontes de dados e processamento. É caro e difícil integrar e manter esses sistemas, além disso, geralmente, eles não possuem o monitoramento e os alertas de nível corporativo e os controles que podem oferecer um serviço totalmente gerenciado.

Com o Data Factory, você pode usar a [Atividade de Cópia](copy-activity-overview.md) em um pipeline de dados para mover os dados que estão em armazenamentos de dados de origem locais e na nuvem para um armazenamento de dados centralizado na nuvem para análise posterior. Por exemplo, você pode coletar dados em um Azure Data Lake Store e transformar os dados posteriormente usando um serviço de computação do Azure Data Lake Analytics. Ou, coletar dados em Armazenamento de Blobs do Azure e transformam dados posteriormente usando um cluster de Hadoop do Azure HDInsight.

### <a name="transform-and-enrich"></a>Transformar e enriquecer
Depois que os dados estiverem presentes em um repositório centralizado de dados na nuvem, você deseja que os dados coletados, sejam processados ou transformados usando serviços de computação como Hadoop do HDInsight, Spark, Data Lake Analytics e Machine Learning. Você quer produzir confiavelmente os dados transformados em uma agenda controlada e passível de manutenção para alimentar os ambientes de produção com dados confiáveis.

### <a name="publish"></a>Publicar
Agora que os dados brutos foram refinados em formato consumível pronto para negócios, carregue os dados no Data Warehouse do Azure, no BD SQL do Azure, no Azure CosmosDB ou em qualquer mecanismo de análise que seus usuários corporativos possam indicar de suas ferramentas de business intelligence.

### <a name="monitor"></a>Monitoramento
Depois de ter criado e implantado seu pipeline de integração de dados com êxito, gerando resultados nos negócios com base nos dados refinados, talvez você queira monitorar as atividades agendadas e os pipelines para obter as taxas de êxito e de falha. O Azure Data Factory tem suporte interno para monitoramento de pipelines por meio do Azure Monitor, da API, do PowerShell, do Microsoft Operations Management Suite (OMS) e dos painéis de integridade no portal do Azure.

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

## <a name="load-the-data-into-a-lake"></a>Carregar os dados em um lake
O Data Factory conta com mais de 30 conectores para permitir que você carregue dados de ambientes híbridos e heterogêneos no Azure.  Consulte o [Guia de desempenho e ajuste](copy-activity-performance.md) para obter os resultados de desempenho dos testes internos mais recentes e as sugestões de ajuste. Além disso, recentemente habilitamos a Alta Disponibilidade e a Escalabilidade para o Integration Runtime autônomo que você instala em um ambiente de rede privada para atender aos requisitos de clientes empresariais da camada 1 para melhorar a disponibilidade e a escalabilidade.

## <a name="top-level-concepts-in-version-2"></a>Conceitos de nível superior na versão 2
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

### <a name="control-flow"></a>Fluxo de Controle
Orquestração de atividades do pipeline que inclui o encadeamento de atividades em uma sequência, em uma ramificação e em parâmetros que podem ser definidos no nível do pipeline e os argumentos transmitidos, durante a invocação do pipeline sob demanda ou de um gatilho. Também inclui transmissão de estado personalizada e contêineres de looping, ou seja, iteradores for-each.


Para obter mais informações sobre os conceitos do Data Factory, confira os seguintes artigos:

- [Conjuntos de dados e serviços vinculados](concepts-datasets-linked-services.md)
- [Pipelines e atividades](concepts-pipelines-activities.md)
- [Tempo de execução de integração](concepts-integration-runtime.md)

## <a name="supported-regions"></a>Regiões com suporte:

No momento, você pode criar data factories nas regiões Leste dos EUA e Leste dos EUA 2. No entanto, uma fábrica de dados pode acessar repositórios de dados e serviços de computação em outras regiões do Azure para mover dados entre repositórios de dados ou processar dados usando serviços de computação.

O Azure Data Factory em si não armazena dados. Ele permite que você crie fluxos de trabalho controlados por dados para orquestrar a movimentação de dados entre os armazenamentos de dados com suporte e o processamento de dados usando serviços de computação em outras regiões ou em um ambiente local. Ele também permite que você monitore e gerencie fluxos de trabalho usando mecanismos programáticos e de interface do usuário.

Embora o Data Factory só esteja disponível nas regiões Leste dos EUA e Leste dos EUA 2, o serviço que capacita a movimentação de dados no Data Factory está disponível globalmente em várias regiões. Se um armazenamento de dados estiver por trás de um firewall, um Gateway de Gerenciamento de Dados instalado no ambiente local moverá os dados.

Por exemplo, digamos que seus ambientes de computação, como o cluster Azure HDInsight e o Azure Machine Learning, estejam ficando sem a região Europa Ocidental. Você pode criar e usar uma instância do Azure Data Factory na Europa Setentrional e usá-la para agendar trabalhos em seus ambientes de computação na Europa Ocidental. Leva alguns milissegundos para o Data Factory disparar o trabalho em seu ambiente de computação, mas o tempo de execução do trabalho em seu ambiente de computação não é alterado.

## <a name="next-steps"></a>Próximas etapas
Saiba como criar um data factory seguindo as instruções passo a passo nos seguintes Guias de Início Rápido: [PowerShell](quickstart-create-data-factory-powershell.md), [.NET](quickstart-create-data-factory-dot-net.md), [Python](quickstart-create-data-factory-python.md), [API REST](quickstart-create-data-factory-rest-api.md) e no Portal do Azure. 
