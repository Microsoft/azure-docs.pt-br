---
title: Cenários de dados envolvendo o Azure Data Lake Storage Gen2 | Microsoft Docs
description: Entenda os diferentes cenários e as ferramentas usando quais dados podem ser ingeridos, processados, baixados e visualizados em um Azure Data Lake Storage Gen2 (anteriormente conhecido como Azure Data Lake Store)
services: storage
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: normesta
ms.openlocfilehash: 010b7bc38caf83c12dd0d8b8e731fdbad6e45256
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68422871"
---
# <a name="using-azure-data-lake-storage-gen2-for-big-data-requirements"></a>Usando o Azure Data Lake Storage Gen2 para exigências de big data

Há quatro estágios principais no processamento de big data:

> [!div class="checklist"]
> * Ingestão de grandes quantidades de dados em um repositório de dados, em tempo real ou em lotes
> * Processamento dos dados
> * Download dos dados
> * Visualização dos dados

Comece criando uma conta de armazenamento e um sistema de arquivos. Em seguida, permita acesso aos dados. As primeiras seções deste artigo ajudarão a realizar essas tarefas. Nas seções restantes, destacaremos as opções e ferramentas para cada fase do processamento.

## <a name="create-a-data-lake-storage-gen2-account"></a>Criar uma conta do Data Lake Storage Gen2

Uma conta do Data Lake Storage Gen2 é uma conta de armazenamento que tem um namespace hierárquico. 

Para criar uma, confira o [Início Rápido: Criar uma conta de armazenamento do Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-quickstart-create-account?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="create-a-file-system"></a>Criar um sistema de arquivos

Um *sistema de arquivos* é um contêiner para arquivos e pastas. Você precisa de pelo menos um deles para iniciar a ingestão de dados em sua conta de armazenamento.  Aqui está uma lista de ferramentas que você pode usar para criá-los.

|Ferramenta | Diretrizes |
|---|--|
|Gerenciador de Armazenamento do Azure | [Criar um sistema de arquivos usando o Gerenciador de Armazenamento](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-explorer#create-a-file-system) |
|AzCopy | [Criar um contêiner de Blobs ou um Compartilhamento de Arquivo usando AzCopyV10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10#transfer-files)|
|CLI (interface de linha de comando) do HDFS (Sistema de Arquivos do Hadoop) com o HDInsight |[Criar um sistema de arquivos usando o HDFS com o HDInsight](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-use-hdfs-data-lake-storage?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#create-a-file-system) |
|Escrever código em um Notebook do Azure Databricks|[Criar sistema de arquivos da conta de armazenamento (Scala)](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-quickstart-create-databricks-account?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#create-storage-account-file-system) <br><br> [Criar um sistema de arquivos e montá-lo (Python)](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-use-databricks-spark?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#create-a-file-system-and-mount-it)|

É mais fácil criar sistemas de arquivos usando o Gerenciador de Armazenamento ou o AzCopy. É um pouco mais trabalhoso criar sistemas de arquivos usando o HDInsight e o Databricks. No entanto, se você estiver planejando usar clusters do HDInsight ou do Databricks para processar seus dados, crie os clusters primeiro e use a CLI do HDFS para criar os sistemas de arquivos.  

## <a name="grant-access-to-the-data"></a>Permitir acesso aos dados

Defina as permissões de acesso corretas à sua conta e aos dados em sua conta antes de começar a ingestão de dados.

Há três maneiras de conceder acesso:

* Atribua uma dessas funções a um usuário, grupo, identidade gerenciada pelo usuário ou entidade de serviço:

  [Leitor de Dados do Blob de Armazenamento](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader)

  [Colaborador de dados de blob de armazenamento](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-queue-data-contributor)

  [Proprietário de Dados do Blob de Armazenamento](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)

* Use um token SAS (Assinatura de Acesso Compartilhado).

* Use uma chave de conta de armazenamento.

Esta tabela mostra como conceder acesso a cada serviço ou ferramenta do Azure.

|Ferramenta | Para conceder acesso | Diretrizes |
|---|--|---|
|Gerenciador de Armazenamento| Atribuir uma função a usuários e grupos | [Atribuir funções de administrador e não administrador aos usuários com Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal) |
|AzCopy| Atribuir uma função a usuários e grupos <br>**or**<br> Usar um token SAS| [Atribuir funções de administrador e não administrador aos usuários com Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal)<br><br>[Criar facilmente um SAS para baixar um arquivo do Armazenamento do Azure, usando o Gerenciador de Armazenamento do Azure](https://blogs.msdn.microsoft.com/jpsanders/2017/10/12/easily-create-a-sas-to-download-a-file-from-azure-storage-using-azure-storage-explorer/)|
|Apache DistCp | Atribuir uma função a uma identidade gerenciada atribuída pelo usuário | [Criando um cluster do HDInsight com o Data Lake Storage Gen2](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2) |
|Azure Data Factory| Atribuir uma função a uma identidade gerenciada atribuída pelo usuário<br>**or**<br> Atribuir uma função a uma entidade de serviço<br>**or**<br> Usar uma chave de conta de armazenamento | [Propriedades do serviço vinculado](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#linked-service-properties) |
|Azure HDInsight| Atribuir uma função a uma identidade gerenciada atribuída pelo usuário | [Criando um cluster do HDInsight com o Data Lake Storage Gen2](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2)|
|Azure Databricks| Atribuir uma função a uma entidade de serviço | [Como: Usar o portal para criar um aplicativo e uma entidade de serviço do Azure AD que possa acessar recursos](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)|

Para permitir acesso a pastas e arquivos específicos, confira estes artigos.

* [Definir permissões no nível do arquivo e do diretório usando o Gerenciador de Armazenamento do Azure com o Azure Data Lake Storage Gen2](https://review.docs.microsoft.com/azure/storage/blobs/data-lake-storage-how-to-set-permissions-storage-explorer)

* [Listas de controle de acesso em arquivos e diretórios](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control#access-control-lists-on-files-and-directories)

Para saber como configurar outros aspectos de segurança, confira [Guia de segurança do Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/common/storage-data-lake-storage-security-guide?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="ingest-the-data"></a>Ingerir os dados

Esta seção destaca as diferentes fontes de dados e as diferentes maneiras que os dados podem ser ingeridos em uma conta do Data Lake Storage Gen2.

![Ingestão de dados no Data Lake Storage Gen2](./media/data-lake-storage-data-scenarios/ingest-data.png "Ingestão de dados no Data Lake Storage Ge21")

### <a name="ad-hoc-data"></a>Dados ad hoc

Representam conjuntos de dados menores que são usados para criar protótipos de um aplicativo de big data. Há diferentes maneiras de ingerir dados ad hoc, dependendo da fonte dos dados. 

Aqui está uma lista de ferramentas que você pode usar para a ingestão de dados ad hoc.

| Fonte de Dados | Ingeri-la usando |
| --- | --- |
| Computador local |[Gerenciador de Armazenamento](https://azure.microsoft.com/features/storage-explorer/)<br><br>[Ferramenta AzCopy](../common/storage-use-azcopy-v10.md)|
| Blob de Armazenamento do Azure |[Azure Data Factory](../../data-factory/connector-azure-data-lake-store.md)<br><br>[Ferramenta AzCopy](../common/storage-use-azcopy-v10.md)<br><br>[DistCp em execução no cluster HDInsight](data-lake-storage-use-distcp.md)|

### <a name="streamed-data"></a>Dados transmitidos

Representam os dados que podem ser gerados por várias fontes, como aplicativos, dispositivos, sensores, etc. Esses dados podem ser ingeridos em um Data Lake Storage Gen2 por uma variedade de ferramentas. Essas ferramentas geralmente capturam e processam os dados em um evento em tempo real e gravam os eventos em lotes no Data Lake Storage Gen2 para que depois eles possam ser processados.

Aqui está uma lista de ferramentas que você pode usar para a ingestão de dados transmitidos.

|Ferramenta | Diretrizes |
|---|--|
|Azure HDInsight Storm | [Gravar no HDFS do Apache Hadoop usando o Apache Storm no HDInsight](https://docs.microsoft.com/azure/hdinsight/storm/apache-storm-write-data-lake-store) |

### <a name="relational-data"></a>Dados relacionais

Você também pode originar dados nos bancos de dados relacionais. Durante um período, os bancos de dados relacionais coletam grandes volumes de dados que podem fornecer informações importantes se processados por meio de um pipeline de big data. É possível usar as ferramentas a seguir para mover tais dados para o Data Lake Storage Gen2.

Aqui está uma lista de ferramentas que você pode usar para a ingestão de dados relacionais.

|Ferramenta | Diretrizes |
|---|--|
|Azure Data Factory | [Atividade Copiar no Azure Data Factory](https://docs.microsoft.com/azure/data-factory/copy-activity-overview) |

### <a name="web-server-log-data-upload-using-custom-applications"></a>Dados de log do servidor Web (carregar usando aplicativos personalizados)

Esse tipo de conjunto de dados é especificamente chamado porque a análise dos dados do log do servidor Web é um caso de uso comum para aplicativos de Big Data e requer que grandes volumes de arquivos de log sejam carregados no Azure Data Lake Storage Gen2. Você pode usar qualquer uma das ferramentas a seguir para escrever seus próprios scripts ou aplicativos para carregar esses dados.

Aqui está uma lista de ferramentas que você pode usar para a ingestão de dados de log de servidor Web.

|Ferramenta | Diretrizes |
|---|--|
|Azure Data Factory | [Atividade Copiar no Azure Data Factory](https://docs.microsoft.com/azure/data-factory/copy-activity-overview)  |

Para carregar dados de log do servidor Web, e também para carregar outros tipos de dados (por exemplo, dados de sentimentos sociais), é uma boa abordagem escrever seus próprios scripts/aplicativos personalizados, pois eles proporcionam a flexibilidade para incluir seus dados carregando o componente como parte do aplicativo maior de big data. Em alguns casos, esse código pode assumir a forma de um script ou um utilitário simples de linha de comando. Em outros casos, o código pode ser usado para integrar o processamento de big data em um aplicativo ou uma solução de negócios.

### <a name="data-associated-with-azure-hdinsight-clusters"></a>Dados associados aos clusters Azure HDInsight

A maioria dos tipos de cluster HDInsight (Hadoop, HBase, Storm) é compatível com o Data Lake Storage Gen2 como um repositório de armazenamento de dados. Os clusters HDInsight acessam dados dos WASB (Blobs de Armazenamento do Azure). Para obter melhor desempenho, você pode copiar os dados do WASB em uma conta do Azure Data Lake Storage Gen2 associada ao cluster. Você pode usar as ferramentas a seguir para copiar os dados.

Aqui está uma lista de ferramentas que você pode usar para a ingestão de dados associados a clusters do HDInsight.

|Ferramenta | Diretrizes |
|---|--|
|Apache DistCp | [Use o DistCp para copiar dados entre os Azure Storage Blobs e o Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-use-distcp) |
|Ferramenta AzCopy | [Transferir dados com AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10) |
|Azure Data Factory | [Copiar dados de ou para Azure Data Lake Storage Gen2 usando Azure Data Factory](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2) |

### <a name="data-stored-in-on-premises-or-iaas-hadoop-clusters"></a>Dados armazenados localmente ou em clusters Hadoop da IaaS

É possível armazenar grandes quantidades de dados em clusters de Hadoop existentes, localmente em computadores que usam o HDFS. Os clusters Hadoop podem estar em uma implantação local ou em um cluster da IaaS no Azure. Pode haver requisitos para copiar esses dados no Azure Data Lake Storage Gen2 para uma abordagem única ou de forma recorrente. Há várias opções que podem ser usadas para conseguir isso. Veja abaixo uma lista de alternativas e as compensações associadas.

| Abordagem | Detalhes | Vantagens | Considerações |
| --- | --- | --- | --- |
| Usar o ADF (Azure Data Factory) para copiar dados diretamente de clusters Hadoop para o Azure Data Lake Storage Gen2 |[O ADF oferece suporte ao HDFS como uma fonte de dados](../../data-factory/connector-hdfs.md) |O ADF fornece suporte nativo para HDFS e gerenciamento e monitoramento de primeira classe completo |Ele exige que um gateway de gerenciamento de dados seja implantado localmente ou em um cluster da IaaS |
| Use Distcp para copiar dados do Hadoop para o Armazenamento do Azure. Em seguida, copie os dados no Armazenamento do Microsoft Azure para o Data Lake Storage Gen2 usando o mecanismo apropriado. |Você pode copiar dados do Armazenamento do Microsoft Azure para o Data Lake Storage Gen2 usando: <ul><li>[Azure Data Factory](../../data-factory/copy-activity-overview.md)</li><li>[Ferramenta AzCopy](../common/storage-use-azcopy-v10.md)</li><li>[Apache DistCp em execução em clusters do HDInsight](data-lake-storage-use-distcp.md)</li></ul> |Você pode usar ferramentas de software livre. |Processo de várias etapas que envolve várias tecnologias |

### <a name="really-large-datasets"></a>Conjuntos de dados realmente grandes

Carregar conjuntos de dados que incluem vários terabytes usando os métodos descritos acima, às vezes, pode ser uma tarefa lenta e onerosa. Nesses casos, você pode usar o Azure ExpressRoute.  

O Azure ExpressRoute permite criar conexões privadas entre os datacenters do Azure e a infraestrutura local presente. Isso proporciona uma opção confiável para transferir grandes quantidades de dados. Para saber mais, confira [documentação do Azure ExpressRoute](../../expressroute/expressroute-introduction.md).

## <a name="process-the-data"></a>Processar os dados

Depois que os dados estiverem disponíveis no Azure Data Lake Storage Gen2, você poderá executar uma análise nesses dados usando os aplicativos de Big Data compatíveis. 

![Analisar dados no Data Lake Storage Gen2](./media/data-lake-storage-data-scenarios/analyze-data.png "Analisar dados no Data Lake Storage Gen2")

Aqui está uma lista de ferramentas que você pode usar para executar trabalhos de análise de dados em dados armazenados no Data Lake Storage Gen2.

|Ferramenta | Diretrizes |
|---|--|
|Azure HDInsight | [Usar Gen2 de armazenamento do Azure Data Lake com clusters de HDInsight do Azure](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2) |
|Azure Databricks | [Azure Data Lake Storage Gen2](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html)<br><br>[Início Rápido: Analisar dados no Azure Data Lake Storage Gen2 usando o Azure Databricks](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-quickstart-create-databricks-account?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)<br><br>[Tutorial: Extrair, transformar e carregar dados usando o Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/databricks-extract-load-sql-data-warehouse?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|

## <a name="visualize-the-data"></a>Visualizar os dados

Você pode usar uma combinação de serviços para criar representações visuais de dados armazenados no Data Lake Storage Gen2.

![Visualizar dados no Azure Data Lake Storage Gen2](./media/data-lake-storage-data-scenarios/visualize-data.png "Visualizar dados no Azure Data Lake Storage Gen2")

* É possível começar usando o [Azure Data Factory para mover dados do Azure Data Lake Storage Gen2 para um SQL Data Warehouse do Azure](../../data-factory/copy-activity-overview.md)
* Depois disso, você pode [integrar o Power BI ao SQL Data Warehouse do Azure](../../sql-data-warehouse/sql-data-warehouse-get-started-visualize-with-power-bi.md) para criar a representação visual dos dados.

## <a name="download-the-data"></a>Baixar os dados

Você também pode querer baixar ou mover dados do Azure Data Lake Storage Gen2 para cenários como:

* Mover dados para outros repositórios para fazer interface com os pipelines de processamento de dados existentes. Por exemplo, você pode querer mover os dados do Azure Data Lake Storage Gen2 para o Banco de Dados SQL do Azure ou SQL Server local.

* Baixar dados no computador local para processamento em ambientes IDE durante a criação de protótipos de aplicativo.

![Saída de dados no Azure Data Lake Storage Gen2](./media/data-lake-storage-data-scenarios/egress-data.png "Saída de dados no Azure Data Lake Storage Gen2")

Aqui está uma lista de ferramentas que você pode usar para baixar dados do Data Lake Storage Gen2.

|Ferramenta | Diretrizes |
|---|--|
|Azure Data Factory | [Atividade Copiar no Azure Data Factory](https://docs.microsoft.com/azure/data-factory/copy-activity-overview) |
|Apache DistCp | [Use o DistCp para copiar dados entre os Azure Storage Blobs e o Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-use-distcp) |
