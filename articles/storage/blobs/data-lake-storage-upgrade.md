---
title: Atualize suas soluções de análise de Big Data do Azure Data Lake Storage Gen1 para o Azure Data Lake Storage Gen2
description: Atualize sua solução para usar o Azure Data Lake Storage Gen2
services: storage
author: normesta
ms.topic: conceptual
ms.author: normesta
ms.date: 02/07/2019
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 4d5bf318a86e989ce66bffbd2aa72638ea477ab1
ms.sourcegitcommit: 80aaf27e3ad2cc4a6599a3b6af0196c6239e6918
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67673918"
---
# <a name="upgrade-your-big-data-analytics-solutions-from-azure-data-lake-storage-gen1-to-azure-data-lake-storage-gen2"></a>Atualize suas soluções de análise de Big Data do Azure Data Lake Storage Gen1 para o Azure Data Lake Storage Gen2

Se você estiver usando o Azure Data Lake Storage Gen1 em suas soluções de análise de Big Data, este guia ajudará a atualizar as soluções para usar o Azure Data Lake Storage Gen2. Você pode usar este documento para avaliar as dependências que sua solução tem do Data Lake Storage Gen1. Este guia também mostra como planejar e executar a atualização.

Ajudaremos você com as seguintes tarefas:

:heavy_check_mark: Avaliar sua preparação para atualização

:heavy_check_mark: Planejar uma atualização

:heavy_check_mark: Realizar a atualização

## <a name="assess-your-upgrade-readiness"></a>Avaliar sua preparação para atualização

Nosso objetivo é que todos os recursos que estão presentes no Data Lake Storage Gen1 sejam disponibilizados no Data Lake Storage Gen2. O modo como esses recursos são expostos, por exemplo, no SDK, na CLI, etc., pode ser diferente entre o Data Lake Storage Gen1 e o Data Lake Storage Gen2. Aplicativos e serviços que funcionam com o Data Lake Storage Gen1 precisam ser capazes de funcionar da mesma forma com o Data Lake Storage Gen2. Por fim, algumas das funcionalidades não estarão disponíveis imediatamente no Data Lake Storage Gen2. Assim que estiverem disponíveis, as anunciaremos neste documento.

Estas seções a seguir ajudarão você a decidir a melhor maneira de atualizar para o Data Lake Storage Gen2 e o melhor momento para fazê-lo.

### <a name="data-lake-storage-gen1-solution-components"></a>Componentes da solução do Data Lake Storage Gen1

Provavelmente, quando você usa o Data Lake Storage Gen1 em seus pipelines ou soluções de análise, há muitas tecnologias adicionais que você pode empregar para obter a funcionalidade geral de ponta a ponta. Este [artigo](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-data-scenarios) descreve vários componentes de fluxo de dados que incluem ingestão, processamento e consumo de dados.

Além disso, há componentes abrangentes para provisionar, gerenciar e monitorar esses componentes. Cada um dos componentes opera com o Data Lake Storage Gen1 usando uma interface mais adequada para tal componente. Quando estiver planejando atualizar sua solução para o Data Lake Storage Gen2, você precisará estar ciente das interfaces que são usadas. Você precisará atualizar tanto a interfaces de gerenciamento como as interfaces de dados, já que cada interface tem requisitos distintos.

![Componentes da solução do Data Lake Storage](./media/data-lake-storage-upgrade/solution-components.png)

A **figura 1** acima mostra os componentes de funcionalidade que você veria na maioria das soluções de análise.

A **figura 2** mostra um exemplo de como esses componentes serão implementados por meio de tecnologias específicas.

A funcionalidade de armazenamento na **Figura 1** é fornecida pelo Data Lake Storage Gen1 (**Figura 2**). Observe como os vários componentes no fluxo de dados interagem com o Data Lake Storage Gen1 usando APIs REST ou SDK do Java. Observe também como os componentes de funcionalidade abrangentes interagem com o Data Lake Storage Gen1. O componente de provisionamento usa modelos de recursos do Azure, enquanto que o componente de monitoramento que usa os logs do Azure Monitor utiliza dados operacionais que vem do Data Lake armazenamento Gen1.

Para atualizar uma solução usando o Data Lake Storage Gen1 para o Data Lake Storage Gen2, você precisará copiar os dados e metadados, conectar novamente os fluxos de dados e, em seguida, todos os componentes precisarão ser capazes de funcionar com o Data Lake Storage Gen2.

As seções abaixo fornecem informações para ajudá-lo a tomar decisões melhores:

:heavy_check_mark: Funcionalidades da plataforma

:heavy_check_mark: Interfaces de programação

:heavy_check_mark: Ecossistema do Azure

:heavy_check_mark: Ecossistema de parceiros

:heavy_check_mark: Informações operacionais

Em cada seção, você poderá determinar os elementos indispensáveis para sua atualização. Depois que você tiver certeza de que as funcionalidades estão disponíveis ou então tiver certeza de que há soluções alternativas razoáveis em vigor, vá para a seção [Planejando uma atualização](#planning-for-an-upgrade) deste guia.

### <a name="platform-capabilities"></a>Funcionalidades da plataforma

Esta seção descreve quais funcionalidades da plataforma Data Lake Storage Gen1 estão atualmente disponíveis no Data Lake Storage Gen2.

| | Armazenamento do Data Lake Gen1 | Data Lake Storage Gen2 – meta | Data Lake Storage Gen2 – status de disponibilidade  |
|-|------------------------|-------------------------------|-----------------------------------------------|
| Organização de dados| Dá suporte a dados armazenados como arquivos e pastas | Dá suporte a dados armazenados como objetos/blobs, bem como pastas e arquivos – [Link](https://docs.microsoft.com/azure/storage/data-lake-storage/namespace) | Dá suporte a dados armazenados como arquivo e pastas – *Disponível agora* <br><br> Dá suporte a dados armazenados como objetos/blobs – *Ainda não disponível* |
| Namespace| Hierárquico | Hierárquico |  *Disponível agora*  |
| API  | API REST sobre HTTPS | API REST sobre HTTP/HTTPS| *Disponível agora* |
| API no servidor| [API REST compatível com WebHDFS](https://msdn.microsoft.com/library/azure/mt693424.aspx) | API REST do Serviço Blob do Azure [API REST do Data Lake Storage Gen2](https://docs.microsoft.com/rest/api/storageservices/data-lake-storage-gen2) | API REST do Data Lake Storage Gen2 – *Disponível agora* <br><br> API REST do Serviço Blob do Azure – *Ainda não está disponível*       |
| Cliente do sistema de arquivos Hadoop | Sim ([Azure Data Lake Storage](https://hadoop.apache.org/docs/current/hadoop-azure-datalake/index.html)) | Sim ([ABFS](https://jira.apache.org/jira/browse/HADOOP-15407))  | *Disponível agora*  |  
| Operações de dados – autorização  | ACLs (listas de controle de acesso) POSIX no nível de arquivo e de pasta baseadas em identidades do Azure Active Directory  | ACLs (listas de controle de acesso) POSIX no nível de arquivo e de pasta baseadas em [chave de compartilhamento](https://docs.microsoft.com/rest/api/storageservices/authorize-with-shared-key) de identidades do Azure Active Directory para [RBAC](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac) (controle de acesso baseado em função) de autorização de nível de conta para acesso a contêineres | *Disponível agora* |
| Operações de Dados – Logs  | Sim | Solicitações únicas para logs por uma duração específica usando o tíquete de suporte de integração do Monitoramento do Azure | Solicitações únicas para logs por uma duração específica usando o tíquete de suporte – *Disponível agora*<br><br> Integração de Monitoramento do Azure – *Ainda não disponível* |
| Dados de criptografia em repouso | Transparente, do lado do servidor, com chaves gerenciadas pelo serviço e com chaves gerenciadas pelo cliente no Azure Key Vault | Transparente, do lado do servidor, com chaves gerenciadas pelo serviço e com chaves gerenciadas pelo cliente no Azure Key Vault | Chaves gerenciadas por serviço – *Disponível agora*<br><br> Chaves gerenciadas por clientes – *Disponível agora*  |
| Operações de gerenciamento (por exemplo, Criar Conta) | [Controle de acesso baseado em função](https://docs.microsoft.com/azure/role-based-access-control/overview) ) fornecido pelo Azure para gerenciamento de contas | [Controle de acesso baseado em função](https://docs.microsoft.com/azure/role-based-access-control/overview) ) fornecido pelo Azure para gerenciamento de contas | *Disponível agora*|
| SDKs de desenvolvedor | .NET, Java, Python, Node.js  | .NET, Java, Python, Node.js, C++, Ruby, PHP, Go, Android, iOS| *Ainda não está disponível* |
| |Desempenho otimizado para cargas de trabalho de análise paralela. Alta taxa de transferência e IOPS. | Desempenho otimizado para cargas de trabalho de análise paralela. Alta taxa de transferência e IOPS. | *Disponível agora* |
| Suporte de rede virtual (VNet)  | [Usando a integração de Rede Virtual](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-network-security)  | [Usar Ponto de Extremidade de Serviço para Armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-network-security?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) | *Disponível agora* |
| Limites de tamanho | Sem limites para tamanhos de conta, tamanhos de arquivo ou número de arquivos | Sem limites para tamanhos de conta ou número de arquivos. Tamanho do arquivo limitado a 5 TB. | *Disponível agora*|
| Redundância geográfica| Com redundância local (LRS) | LRS (com redundância local), ZRS (com redundância de zona), GRS (com redundância global), RA-GRS (redundância global com acesso de leitura). Consulte [aqui](https://docs.microsoft.com/azure/storage/common/storage-redundancy) para obter mais informações| *Disponível agora* |
| Disponibilidade regional | Veja [aqui](https://azure.microsoft.com/regions/) | Todas as [regiões do Azure](https://azure.microsoft.com/global-infrastructure/regions/)                                                                                                                                                                                                                                                                                                                                       | *Disponível agora*                                                                                                                           |
| Preço                                       | Veja [preços](https://azure.microsoft.com/pricing/details/data-lake-store/)                                                                            | Veja [preços](https://azure.microsoft.com/pricing/details/storage/data-lake/)                                                                                                                                                                                                                                                                                                                                         |                                                                                                                                           |
| Contrato de Nível de Serviço de Disponibilidade                            | [Ver SLA](https://azure.microsoft.com/support/legal/sla/data-lake-store/v1_0/)                                                                   | [Ver SLA](https://azure.microsoft.com/support/legal/sla/storage/v1_3/)                                                                                                                                                                                                                                                                                                                                                | *Disponível agora*                                                                                                                           |
| Gerenciamento de Dados                             | Validade do arquivo                                                                                                                                        | Políticas de ciclo de vida                                                                                                                                                                                                                                                                                                                                                                                                          | *Ainda não está disponível*                                                                                                                       |

### <a name="programming-interfaces"></a>Interfaces de programação

Esta tabela descreve quais conjuntos de APIs estão disponíveis para seus aplicativos personalizados. Para tornar as coisas um pouco mais claras, estamos tenha separado esses conjuntos de API em 2 tipos: APIs de sistema de arquivos e as APIs de gerenciamento.

APIs de gerenciamento ajudam você a gerenciar contas, enquanto as APIs ajudam você a operar nos arquivos e pastas do sistema de arquivos.

|  Conjunto de APIs                           |  Armazenamento do Data Lake Gen1                                                                                                                                                                                                                                                                                                   | Disponibilidade para o Data Lake Storage Gen2 – com autenticação de chave compartilhada | Disponibilidade para o Data Lake Storage Gen2 – com autenticação de OAuth                                                                                                  |
|----------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------|
| SDK do .NET – gerenciamento                  | [Link](https://docs.microsoft.com/dotnet/api/overview/azure/datalakestore/management?view=azure-dotnet)                                                                                                                                                                                                                 | *Sem suporte*                                                      | *Disponível agora –* [Link](https://docs.microsoft.com/rest/api/storageservices/operations-on-the-account--blob-service-)                                    |
| SDK do .NET – sistema de arquivos                  | [Link](https://docs.microsoft.com/dotnet/api/overview/azure/datalakestore/client?view=azure-dotnet)                                                                                                                                                                                                                     | *Ainda não está disponível*                                                | *Ainda não está disponível*                                                                                                                                             |
| SDK do Java – gerenciamento                  | [Link](https://docs.microsoft.com/java/api/overview/azure/datalakestore/management)                                                                                                                                                                                                                                     | *Sem suporte*                                                      | *Disponível agora –* [Link](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob?view=azure-java-stable)                                     |
| Java SDK – sistema de arquivos                  | [Link](https://docs.microsoft.com/java/api/overview/azure/datalake)                                                                                                                                                                                                                                         | *Ainda não está disponível*                                                | *Ainda não está disponível*                                                                                                                                             |
| Node.js – gerenciamento                   | [Link](https://www.npmjs.com/package/azure-arm-datalake-store)                                                                                                                                                                                                                                                                | Sem suporte                                                      | *Disponível agora –* [Link](https://azure.github.io/azure-storage-node/)                                                                                            |
| Node. js - sistema de arquivos                   | [Link](https://www.npmjs.com/package/azure-arm-datalake-store)                                                                                                                                                                                                                                                                | *Ainda não está disponível*                                                | *Ainda não está disponível*                                                                                                                                             |
| Python – gerenciamento                    | [Link](https://docs.microsoft.com/python/api/overview/azure/datalakestore/management?view=azure-python)                                                                                                                                                                                                                 | *Sem suporte*                                                      | *Disponível agora –* [Link](https://docs.microsoft.com/python/api/overview/azure/storage/management?view=azure-python)                                       |
| Python – sistema de arquivos                    | [Link](https://azure-datalake-store.readthedocs.io/en/latest/)                                                                                                                                                                                                                                                                 | *Ainda não está disponível*                                                | *Ainda não está disponível*                                                                                                                                             |
| API REST – gerenciamento                  | [Link](https://docs.microsoft.com/rest/api/datalakestore/accounts)                                                                                                                                                                                                                                                      | *Sem suporte*                                                      | *Disponível agora –*                                                                                                                                               |
| API REST – sistema de arquivos                  | [Link](https://docs.microsoft.com/rest/api/datalakestore/webhdfs-filesystem-apis)                                                                                                                                                                                                                                       | *Disponível agora*                                                    | *Disponível agora –* [Link](https://docs.microsoft.com/rest/api/storageservices/data-lake-storage-gen2)                                                      |
| PowerShell - gerenciamento e o sistema de arquivos | [Link](https://docs.microsoft.com/powershell/module/az.datalakestore)                                                                                                                                                                                                                        | Gerenciamento – não tem suporte a sistema de arquivos - *ainda não está disponível*        | Gerenciamento – *Disponível agora –* [Link](https://docs.microsoft.com/powershell/module/az.storage) <br><br>Sistema de arquivos – *ainda não está disponível* |
| CLI – gerenciamento                       | [Link](https://docs.microsoft.com/cli/azure/dls/account?view=azure-cli-latest)                                                                                                                                                                                                                                          | *Sem suporte*                                                      | *Disponível agora –* [Link](https://docs.microsoft.com/cli/azure/storage?view=azure-cli-latest)                                                              |
| CLI - sistema de arquivos                       | [Link](https://docs.microsoft.com/cli/azure/dls/fs?view=azure-cli-latest)                                                                                                                                                                                                                                               | *Ainda não está disponível*                                                | *Ainda não está disponível*                                                                                                                                             |
| Modelos do Azure Resource Manager – gerenciamento             | [Modelo1](https://azure.microsoft.com/resources/templates/101-data-lake-store-no-encryption/)  [Modelo2](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-adls/)  [Modelo3](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-key-vault/)  | *Sem suporte*                                                      | *Disponível agora –* [Link](https://docs.microsoft.com/azure/templates/microsoft.storage/2018-07-01/storageaccounts)                                         |

### <a name="azure-ecosystem"></a>Ecossistema do Azure

Ao usar o Data Lake Storage Gen1, você pode usar uma variedade de produtos e serviços Microsoft em seus pipelines de ponta a ponta. Esses produtos e serviços funcionam com o Data Lake Storage Gen1 direta ou indiretamente. Esta tabela mostra uma lista dos serviços que modificamos para funcionarem com o Data Lake Storage Gen1 e mostra quais deles são atualmente compatíveis com o Data Lake Storage Gen2.

| **Área**             | **Disponibilidade para o Data Lake Storage Gen1**                                                                                                                                    | **Disponibilidade para o Data Lake Storage Gen2 – com autenticação de chave compartilhada**                                                                                                           | **Disponibilidade para o Data Lake Storage Gen2 – com OAuth**                                                                                        |
|----------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------|
| Estrutura de análise  | [Apache Hadoop](https://hadoop.apache.org/docs/current/hadoop-azure-datalake/index.html)                                                                                       | *Disponível agora*                                                                                                                                                              | *Disponível agora*                                                                                                                                 |
|                      | [HDInsight](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-hdinsight-hadoop-use-portal)                                                               | [HDInsight](https://docs.microsoft.com/azure/storage/data-lake-storage/quickstart-create-connect-hdi-cluster) 3.6 – *disponível agora* HDInsight 4.0 – *ainda não disponível*      | HDInsight 3.6 ESP – *Disponível agora* <br><br>  HDInsight 4.0 ESP – *Ainda não disponível*                                                                 |
|                      | Databricks Runtime 3.1 e superior                                                                                                                                               | [Databricks Runtime 5.1 e superior](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html#azure-data-lake-storage-gen2) *- Disponível agora* | [Databricks Runtime 5.1 e superior](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html#azure-data-lake-storage-gen2) – *Disponível agora*                                                                                              |
|                      | [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store)                                            | *Sem suporte*                                                                                                                                                              | *Disponível agora* [Link](https://docs.microsoft.com/sql/t-sql/statements/create-external-data-source-transact-sql?view=sql-server-2017) |
| Integração de dados     | [Fábrica de dados](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-store)                                                                                | [Versão 2](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage) – *Disponível agora* Versão 1 – *Sem suporte*                               | [Versão 2](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage) – *Disponível agora* <br><br> Versão 1 – *Sem suporte*  |
|                      | [AdlCopy](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob)                                                                 | *Sem suporte*                                                                                                                                                              | *Sem suporte*                                                                                                                                 |
|                      | [SQL Server Integration Services](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-store-connection-manager?view=sql-server-2017) | *Ainda não está disponível*                                                                                                                                                          | *Ainda não está disponível*                                                                                                                             |
|                      | [Catálogo de Dados](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-with-data-catalog)                                                                       | *Ainda não está disponível*                                                                                                                                                          | *Ainda não está disponível*                                                                                                                             |
|                      | [Aplicativos Lógicos](https://docs.microsoft.com/connectors/azuredatalake/)                                                                                                      | *Ainda não está disponível*                                                                                                                                                          | *Ainda não está disponível*                                                                                                                             |
| IoT                  | [Hubs de Eventos – capturar](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-archive-eventhub-capture)                                                       | *Ainda não está disponível*                                                                                                                                                          | *Ainda não está disponível*                                                                                                                             |
|                      | [Stream Analytics](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-stream-analytics)                                                                   | *Ainda não está disponível*                                                                                                                                                          | *Ainda não está disponível*                                                                                                                             |
| Consumo          | [Power BI Desktop](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-power-bi)                                                                           | *Ainda não está disponível*                                                                                                                                                          | *Ainda não está disponível*                                                                                                                             |
|                      | [Excel](https://techcommunity.microsoft.com/t5/Excel-Blog/Announcing-the-Azure-Data-Lake-Store-Connector-in-Excel/ba-p/91677)                                                 | *Ainda não está disponível*                                                                                                                                                          | *Ainda não está disponível*                                                                                                                             |
|                      | [Serviços de Análise](https://blogs.msdn.microsoft.com/analysisservices/2017/09/05/using-azure-analysis-services-on-top-of-azure-data-lake-storage/)                            | *Ainda não está disponível*                                                                                                                                                          | *Ainda não está disponível*                                                                                                                             |
| Produtividade         | [Portal do Azure](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal)                                                                      | *Sem suporte*                                                                                                                                                              | Gerenciamento de conta *– Disponível agora* <br><br>Operações de dados *–* *Ainda não disponível*                                                                   |
|                      | [Ferramentas do Data Lake para Visual Studio](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-data-lake-tools-install)                                   | *Ainda não está disponível*                                                                                                                                                          | *Ainda não está disponível*                                                                                                                             |
|                      | [Gerenciador de Armazenamento do Azure](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-in-storage-explorer)                                                          | *Disponível agora*                                                                                                                                                              | *Disponível agora*                                                                                                                                 |
|                      | [Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=usqlextpublisher.usql-vscode-ext)                                                                     | *Ainda não está disponível*                                                                                                                                                          | *Ainda não está disponível*                                                                                                                             |

### <a name="partner-ecosystem"></a>Ecossistema de parceiros

Esta tabela mostra uma lista dos serviços de terceiros e produtos que foram modificados para funcionar com o Data Lake Storage Gen1. Ele também mostra quais delas são atualmente compatíveis com o Data Lake Storage Gen2.

| Área            | Parceiro  | Produto/Serviço  | Disponibilidade para o Data Lake Storage Gen1                                                                                                                                               | Disponibilidade para o Data Lake Storage Gen2 – com autenticação de chave compartilhada                                                   | Disponibilidade para o Data Lake Storage Gen2 – com OAuth                                                             |
|---------------------|--------------|----------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------|
| Estrutura de análise | Cloudera     | CDH                  | [Link](https://www.cloudera.com/documentation/enterprise/5-11-x/topics/admin_adls_config.html)                                                                                            | *Ainda não está disponível*                                                                                                  | [Link](https://www.cloudera.com/documentation/enterprise/latest/topics/admin_adls2_config.html)                                                                                                  |
|                     | Cloudera     | Altus                | [Link](https://www.cloudera.com/more/news-and-blogs/press-releases/2017-09-27-cloudera-introduces-altus-data-engineering-microsoft-azure-hybrid-multi-cloud-data-analytic-workflows.html) | *Sem suporte*                                                                                                                  | *Ainda não está disponível*                                                                                                  |
|                     | HortonWorks  | HDP 3.0              | [Link](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.3/bk_cloud-data-access/content/adls-get-started.html)                                                                       | *Ainda não está disponível*                                                                                                  | *Ainda não está disponível*                                                                                                  |
|                     | Qubole       |                      | [Link](https://www.qubole.com/blog/big-data-analytics-microsoft-azure-data-lake-store-qubole/)                                                                                            | *Ainda não está disponível*                                                                                                  | *Ainda não está disponível*                                                                                                  |
| ETL                 | StreamSets   |                      | [Link](https://streamsets.com/blog/ingest-data-azure)                                                                                                                                     | *Ainda não está disponível*                                                                                                  | *Ainda não está disponível*                                                                                                  |
|                     | Informatica  |                      | [Link](https://kb.informatica.com/proddocs/Product%20Documentation/6/IC_Spring2017_MicrosoftAzureDataLakeStoreV2ConnectorGuide_en.pdf)                                                    | *Ainda não está disponível*                                                                                                  | *Ainda não está disponível*                                                                                                  |
|                     | Attunity     |                      | [Link](https://www.attunity.com/company/press-releases/microsoft-and-attunity-announce-strategic-partnership-for-data-migration/)                                                         | *Ainda não está disponível*                                                                                                  | *Ainda não está disponível*                                                                                                  |
|                     | Alteryx      |                      | [Link](https://help.alteryx.com/2018.2/DataSources/AzureDataLake.htm)                                                                                                                     | *Ainda não está disponível*                                                                                                  | *Ainda não está disponível*                                                                                                  |
|                     | ImanisData   |                      | [Link](https://www.imanisdata.com/expansion-azure-support-azure-data-lake-store-integration/)                                                                                             | *Ainda não está disponível*                                                                                                  | *Ainda não está disponível*                                                                                                  |
|                     | WANdisco     |                      | [Link](https://azure.microsoft.com/blog/globally-replicated-data-lakes-with-livedata-using-wandisco-on-azure/)                                                                      | [Link](https://azure.microsoft.com/blog/globally-replicated-data-lakes-with-livedata-using-wandisco-on-azure/) | [Link](https://azure.microsoft.com/blog/globally-replicated-data-lakes-with-livedata-using-wandisco-on-azure/) |

### <a name="operational-information"></a>Informações operacionais

O Data Lake Storage Gen1 envia por push dados e informações específicas para outros serviços, o que ajuda você a colocar seus pipelines em operação. Esta tabela mostra a disponibilidade de suporte correspondente no Data Lake Storage Gen2.

| Tipo de dados                                                                                       | Disponibilidade para o Data Lake Storage Gen1                                                                 | Disponibilidade para o Data Lake Storage Gen2                                                                                               |
|--------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------|
| Dados de cobrança – medidores que são enviados para a equipe de comércio para cobrança e, em seguida, disponibilizados para os clientes  | *Disponível agora*                                                                                             | *Disponível agora*                                                                                                                           |
| Logs de atividade                                                                                          | [Link](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-diagnostic-logs#audit-logs)   | Solicitações únicas para logs por uma duração específica usando o tíquete de suporte – *disponível agora* Integração do Monitoramento do Azure – *ainda não disponível* |
| Logs de diagnóstico                                                                                        | [Link](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-diagnostic-logs#request-logs) | Solicitações únicas para logs por uma duração específica usando o tíquete de suporte – *disponível agora* Integração do Monitoramento do Azure – *ainda não disponível* |
| metrics                                                                                                | *Sem suporte*                                                                                               | *Disponível agora –* [Link](https://docs.microsoft.com/azure/storage/common/storage-metrics-in-azure-monitor)                          |

## <a name="planning-for-an-upgrade"></a>Planejando uma atualização

Esta seção pressupõe que você analisou a seção [Avaliar sua preparação para atualização](#assess-your-upgrade-readiness) deste guia e que todas as suas dependências foram atendidas. Se houver recursos que ainda não estão disponíveis no Data Lake Storage Gen2, prossiga somente se você conhecer as soluções alternativas correspondentes. As seções a seguir fornecem diretrizes sobre como você pode planejar a atualização de seus pipelines. A realização da atualização propriamente dita será descrita na seção [Realizando a atualização](#performing-the-upgrade) deste guia.

### <a name="upgrade-strategy"></a>Estratégia de atualização

A parte mais importante da atualização é decidir a estratégia. Essa decisão determina as opções que estão disponíveis para você.

Esta tabela lista algumas estratégias bem conhecidas que foram usadas para migrar bancos de dados, clusters do Hadoop, etc. Adotaremos estratégias semelhantes em nossas diretrizes e as adaptaremos para o nosso contexto.

| Estratégia                   | Prós                                                                                  | Contras                                                           | Quando usar?                                                                                                                                                                                             |
|--------------------------------|-------------------------------------------------------------------------------------------|--------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Lift-and-shift                 | Mais simples.                                                                                 | Requer tempo de inatividade para cópia de dados, movimentação de trabalhos e movimentação de entrada e de saída                                             | Para soluções mais simples, em que não há várias soluções acessando a mesma conta Gen1 e, portanto, elas podem ser movidas juntas, de modo rápido e controlado.                                                  |
| Cópia única e cópia incremental | Reduza o tempo de inatividade ao executar a cópia em segundo plano enquanto o sistema de origem ainda está ativo. | Requer tempo de inatividade para movimentação de entrada e de saída.                   | A quantidade de dados a serem copiados é grande e o tempo de inatividade associado com lift-and-shift não é aceitável. Podem ser necessários testes com os dados de produção significativos no sistema de destino antes da transição. |
| Adoção paralela              | Menor tempo de inatividade. Permite que haja tempo para que os aplicativos migrem a seu critério.           | É mais elaborada, já que a sincronização biderecional é necessária entre os dois sistemas. | Para cenários complexos, nos quais os aplicativos criados no Data Lake Storage Gen1 não podem ser todos substituídos simultaneamente e precisam ser movidos de forma incremental.                                                      |

Abaixo estão mais detalhes sobre as etapas envolvidas em cada uma das estratégias. As etapas listam o que você faria com os componentes envolvidos na atualização. Isso inclui o sistema de origem geral, o sistema de destino geral, as origens de entrada para o sistema de origem, os destinos de saída para o sistema de origem e os trabalhos em execução no sistema de origem.

Essas etapas não se destinam a ser prescritivas. Elas devem definir as linhas gerais ao idealizarmos cada estratégia. Forneceremos estudos de caso de cada estratégia conforme testemunharmos sua implementação.

#### <a name="lift-and-shift"></a>Lift-and-shift

1. Pause o sistema de origem – origens de entrada, trabalhos, destinos de saída.
2. Copie todos os dados do sistema de origem para o sistema de destino.
3. Aponte todas as origens de entrada para o sistema de destino. Aponte, do sistema de destino, para o destino de saída.
4. Mover, modificar, executar todos os trabalhos para o sistema de destino.
5. Desligar o sistema de origem.

#### <a name="copy-once-and-copy-incremental"></a>Cópia única e cópia incremental

1. Copie os dados do sistema de origem para o sistema de destino.
2. Copie os dados incrementais do sistema de origem para o sistema de destino em intervalos regulares.
3. Aponte, do sistema de destino, para o destino de saída.
4. Mova, modifique, execute todos os trabalhos no sistema de destino.
5. Aponte as origens de entrada incrementalmente para o sistema de destino, segundo sua conveniência.
6. Depois que todas as origens de entrada estiverem apontando para o sistema de destino.
    1. Desligue a cópia incremental.
    2. Desligar o sistema de origem.

#### <a name="parallel-adoption"></a>Adoção paralela

1. Configure o sistema de destino.
2. Configure uma replicação bidirecional entre o sistema de origem e o sistema de destino.
3. Aponte as origens de entrada incrementalmente para o sistema de destino.
4. Mova, modifique, execute trabalhos de forma incremental para o sistema de destino.
5. Aponte incrementalmente do sistema de destino para destinos de saída.
6. Depois que todas as origens de entrada, trabalhos e destino de saída originais estiverem funcionando com o sistema de destino, desligue o sistema de origem.

### <a name="data-upgrade"></a>Atualização de dados

A estratégia geral que você usa para executar a atualização (descrita na seção [Estratégia de atualização](#upgrade-strategy) deste guia) determinará as ferramentas que você pode usar para a atualização de dados. As ferramentas listadas abaixo são baseadas nas informações atuais e são sugestões. 

#### <a name="tools-guidance"></a>Diretrizes de ferramentas

| Estratégia                       | Ferramentas                                                                                                             | Prós                                                                                                                             | Considerações                                                                                                                                                                                                                                                                                                                |
|------------------------------------|-----------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Lift-and-shift**                 | [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2-from-gen1) | Serviço de nuvem gerenciado                                                                                                                | Dados e as ACLs podem ser copiadas no momento.                                                                                                                                                                                                                                                                      |
|                                    | [Distcp](https://hadoop.apache.org/docs/r1.2.1/distcp.html)                                                           | Permissões de ferramenta conhecidas fornecidas pelo Hadoop, ou seja, ACLs, podem ser copiadas com essa ferramenta                                                   | Requer um cluster que possa se conectar ao Data Lake Storage Gen1 e Gen2 ao mesmo tempo.                                                                                                                                                                                   |
| **Cópia única e cópia incremental** | Fábrica de dados do Azure                                                                                                    | Serviço de nuvem gerenciado                                                                                                                | Dados e as ACLs podem ser copiadas no momento. Para compatibilidade com a cópia incremental no ADF, os dados precisam ser organizados na forma de série temporal. O intervalo mais curto entre as cópias incrementais é de [15 minutos](https://docs.microsoft.com/azure/data-factory/how-to-create-tumbling-window-trigger). |
| **Adoção paralela**              | [WANdisco](https://docs.wandisco.com/bigdata/wdfusion/adls/)                                                           | Replicação consistente com o suporte. Se estiver usando um ambiente puro do Hadoop conectado ao Azure Data Lake Storage, dará suporte à replicação bidirecional | Se não usar um ambiente puro do Hadoop, poderá haver um atraso na replicação.                                                                                                                                                                                                                                                  |

Observe que há terceiros que podem manipular a atualização do Data Lake Storage Gen1 para o Gen2 sem envolver as ferramentas de cópia de dados/metadados acima (por exemplo: [Cloudera](https://blog.cloudera.com/blog/2017/08/use-amazon-s3-with-cloudera-bdr/)). Eles fornecem uma experiência de "loja de conveniência" que realiza a migração de dados, bem como a migração da carga de trabalho. Talvez você precise executar uma atualização fora de banda para qualquer ferramenta que está fora do respectivo ecossistema.

#### <a name="considerations"></a>Considerações

* Você precisará primeiro criar manualmente a conta do Data Lake Storage Gen2 antes de iniciar qualquer parte da atualização, uma vez que as diretrizes atuais não incluem nenhum processo automático de conta do Gen2 com base em suas informações de conta do Gen1. Verifique se você comparou os processos de criação de contas para [Gen1](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal) e [Gen2](https://docs.microsoft.com/azure/storage/data-lake-storage/quickstart-create-account).

* Data Lake Storage Gen2, só é compatível com arquivos de até 5 TB. Para atualizar para o Data Lake Storage Gen2, talvez seja necessário redimensionar os arquivos no Data Lake Storage Gen1 para que tenham um tamanho menor que 5 TB.

* Se usar uma ferramenta que não copia as ACLs ou se não deseja copiar as ACLs, você precisará definir manualmente as ACLs no destino, no nível superior apropriado. Você pode fazer isso usando o Gerenciador de Armazenamento. Verifique se essas ACLs são as ACLs padrão, para que sejam herdadas pelos arquivos e pastas que você copiar.

* Na Data Lake Storage Gen1, o nível mais alto no qual você pode definir ACLs é na raiz da conta. No Data Lake armazenamento Gen2, no entanto, o nível mais alto, você pode definir ACLs está na pasta raiz em um sistema de arquivos, não a conta inteira. Portanto, se você quiser definir ACLs padrão no nível da conta, você precisará duplicá-las em todos os sistemas de arquivos em sua conta do Data Lake Storage Gen2.

* As restrições de nomenclatura de arquivo são diferentes entre os dois sistemas de armazenamento. Essas diferenças são especialmente preocupantes ao copiar do Data Lake Storage Gen2 para o Gen1, já que esse último tem restrições mais estritas.

### <a name="application-upgrade"></a>Atualização de aplicativo

Quando precisar criar aplicativos no Data Lake Storage Gen1 ou Gen2, você precisará primeiro escolher uma interface de programação apropriada. Ao chamar uma API nessa interface, você precisará fornecer o URI e as credenciais apropriados. A representação desses três elementos, a API, o URI e o modo como as credenciais são fornecidas, é diferente entre o Data Lake Storage Gen1 e o Gen2. Então, como parte da atualização do aplicativo, você precisará mapear esses três constructos adequadamente.

#### <a name="uri-changes"></a>Alterações do URI

A principal tarefa é converter o URI que têm um prefixo de `adl://` no URI que têm um `abfss://` prefixo.

O esquema de URI para o Data Lake Storage Gen1 é mencionado [aqui](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-store) em detalhes, mas falando genericamente, ele é *adl://meudatalakestore.azuredatalakestore.net/\<caminho_do_arquivo\>.*

O esquema URI para acessar arquivos do Data Lake armazenamento Gen2 é explicado [aqui](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md) em detalhes, mas falando genericamente, ele é `abfss://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.widows.net/<PATH>`.

Você precisará passar por seus aplicativos existentes e verificar se alterou os URIs adequadamente para apontarem para aqueles do Data Lake Storage Gen2. Além disso, você precisará adicionar as credenciais apropriadas. Por fim, o modo como você desativa os aplicativos originais e os substitui pelo novo aplicativo deverá ser estreitamente alinhado à sua estratégia geral de atualização.

#### <a name="custom-applications"></a>Aplicativos personalizados

Dependendo da interface do que seu aplicativo usar com o Data Lake Storage Gen1, você precisará modificá-lo para adaptá-lo para o Data Lake Storage Gen2.

##### <a name="rest-apis"></a>APIs REST

Se o aplicativo usar APIs REST do Data Lake Storage, você precisará modificá-lo para usar as APIs REST do Data Lake Storage Gen2. Links são fornecidos na seção *Interfaces de programação*.

##### <a name="sdks"></a>SDKs

Conforme informado na seção *Avaliar sua preparação para atualização*, os SDKs não estão disponíveis no momento. Se você quer realizar a portabilidade dos aplicativos para o Data Lake Storage Gen2, é recomendável aguardar até que os SDKs com suporte estejam disponíveis.

##### <a name="powershell"></a>PowerShell

Conforme informado na seção Avaliar sua preparação para atualização, o suporte ao PowerShell não está disponível atualmente para o plano de dados.

Você poderia substituir commandlets do plano de gerenciamento pelos adequados no Data Lake Storage Gen2. Links são fornecidos na seção *Interfaces de programação*.

##### <a name="cli"></a>CLI

Conforme informado na seção *Avaliar sua preparação para atualização*, o suporte à CLI não está disponível atualmente para o plano de dados.

Você poderia substituir comandos do plano de gerenciamento pelos adequados no Data Lake Storage Gen2. Links são fornecidos na seção *Interfaces de programação*.

### <a name="analytics-frameworks-upgrade"></a>Atualização de estruturas de análise

Se o aplicativo criar metadados sobre informações no repositório, tais como caminhos explícitos de arquivos e de pastas, você precisará realizar ações adicionais depois de atualizar os repositório de dados/metadados. Isso é especialmente verdadeiro para estruturas de análise tais como o Azure HDInsight, Databricks, etc., que geralmente criam dados de catálogo no repositório de dados.

Estruturas de análise trabalham com os dados e metadados armazenados nos repositórios remotos, tais como o Data Lake Storage Gen1 e Gen2. Portanto, em teoria, os mecanismos podem ser efêmeros e ser ativados apenas quando os trabalhos precisam ser executados nos dados armazenados.

No entanto, para otimizar o desempenho, as estruturas de análise podem criar referências explícitas aos arquivos e pastas armazenados no armazenamento remoto e, em seguida, criar um cache para mantê-los. Se o URI dos dados remotos sofrer alterações, por exemplo, um cluster que estava armazenando dados no Data Lake Storage Gen1 anteriormente agora desejar armazenar no Data Lake Storage Gen2, o URI para o mesmo conteúdo copiado será diferente. Portanto, após a atualização dos dados e metadados, os caches desses mecanismos também precisam ser atualizados ou reinicializados

Como parte do processo de planejamento, você precisará identificar seu aplicativo e descobrir como informações de metadados podem ser reinicializadas para apontar para dados agora armazenados no Data Lake Storage Gen2. Abaixo estão as diretrizes para estruturas de análise frequentemente adotadas para ajudá-lo com suas etapas de atualização.

#### <a name="azure-databricks"></a>Azure Databricks

Dependendo da estratégia de atualização que você escolher, as etapas serão diferentes. A seção atual presume que você escolheu a estratégia de "Lift-and-shift". Além disso, espera-se que o workspace do Databricks existente, que é usado para acessar dados em uma conta do Data Lake Storage Gen1, funcione com aqueles dados que são copiados para a conta do Data Lake Storage Gen2.

Primeiro, verifique se você criou a conta do Gen2 e, em seguida, copiou os dados e metadados do Gen1 para o Gen2 usando uma ferramenta adequada. Essas ferramentas são destacadas na seção [Atualização de dados](#data-upgrade) deste guia.

Em seguida, [atualize](https://docs.azuredatabricks.net/user-guide/clusters/index.html) seu cluster existente do Databricks para começar a usar o Databricks Runtime 5.1 ou superior, que deve ser compatível com o Data Lake Storage Gen2.

Depois disso, as etapas são baseadas em como o workspace existente do Databricks acessa os dados na conta do Data Lake Storage Gen1. Isso pode ser feito chamando URIs adl:// [diretamente](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake.html#access-azure-data-lake-store-directly) de notebooks ou por meio [pontos de montagem](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake.html#mount-azure-data-lake-store-with-dbfs).

Se estiver acessando diretamente de notebooks, fornecendo os URIs adl:// completos, você precisará passar por cada notebook e alterar a configuração para acessar o URI do Data Lake Storage Gen2 correspondente.

Mais adiante, você precisará reconfigurá-lo para apontar para a conta do Data Lake Storage Gen2. Nenhuma outra alteração é necessária e os notebooks devem ser capazes de funcionar como antes.

Se estiver usando qualquer uma das outras estratégias de atualização, você poderá criar uma variação das etapas acima para atender às suas necessidades.

### <a name="azure-ecosystem-upgrade"></a>Atualização de ecossistema do Azure

Cada uma das ferramentas e serviços indicados na seção [ecossistema do Azure](#azure-ecosystem) deste guia precisará ser configurado para funcionar com o Data Lake Storage Gen2.

Primeiro, verifique se a integração com o Data Lake Storage Gen2 está disponível.

Em seguida, os elementos informados acima (por exemplo: URI e credenciais) precisarão ser alterados. Você poderia modificar a instância existente que funciona com o Data Lake Storage Gen1 ou poderia criar uma nova instância que funcionaria com o Data Lake Storage Gen2.

### <a name="partner-ecosystem-upgrade"></a>Atualização de ecossistema de parceiros

Trabalhe com o parceiro, fornecendo os componentes e ferramentas para garantir que ele possa trabalhar com o Data Lake Storage Gen2. 

## <a name="performing-the-upgrade"></a>Realizar a atualização

### <a name="pre-upgrade"></a>Pré-atualização

Como parte desse processo, você teria passado pela seção *Avalie sua preparação para atualização* e a seção [Planejando uma atualização](#planning-for-an-upgrade) deste guia, recebido todas as informações necessárias e criado um plano que atenderia às suas necessidades. Você provavelmente terá uma tarefa de teste durante esta fase.

### <a name="in-upgrade"></a>Na atualização

Dependendo da estratégia escolhida e das complexidades da sua solução, essa fase pode ser curta ou estendida, sendo que nesse último caso há várias cargas de trabalho aguardando para serem movidas incrementalmente para o Data Lake Storage Gen2. Isso será a parte mais importante da atualização.

### <a name="post-upgrade"></a>Pós-atualização

Depois de concluir a operação de transição, as etapas finais envolverão uma verificação meticulosa. Isso seria incluem, mas não se limitam a verificando os dados tiverem sido copiado com confiança, verificando ACLs tiverem sido definidas corretamente, verificando pipelines de ponta a ponta estão funcionando corretamente etc. Depois que as verificações foram concluídas, você pode agora desligar seus pipelines antigos, excluir as contas de origem do Data Lake Storage Gen1 e aproveitar ao máximo suas soluções baseadas no Data Lake Storage Gen2.

## <a name="conclusion"></a>Conclusão

As diretrizes fornecidas neste documento devem ajudaram a atualizar sua solução para usar o Data Lake Storage Gen2. 

Se você tiver mais dúvidas ou se tiver comentários, forneça comentários abaixo ou forneça comentários no [Fórum de comentários do Azure](https://feedback.azure.com/forums/327234-data-lake).
