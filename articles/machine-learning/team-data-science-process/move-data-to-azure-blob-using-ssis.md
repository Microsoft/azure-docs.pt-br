---
title: Mover dados do Armazenamento de Blobs com conectores SSIS – processo de ciência de dados de equipe
description: Saiba como mover dados de ou para o armazenamento de BLOBs do Azure usando o SQL Server Integration Services Feature Pack para o Azure.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 9572d612e7ee8e2fd72850ba14447e8449f0f371
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93321998"
---
# <a name="move-data-to-or-from-azure-blob-storage-using-ssis-connectors"></a>Mover dados para ou do Armazenamento de Blobs do Azure usando conectores SSIS
O [Feature Pack dos Serviços de Integração do SQL Server para Azure](/sql/integration-services/azure-feature-pack-for-integration-services-ssis) fornece componentes para se conectar ao Azure, transferir dados entre o Azure e fontes de dados locais e processar os dados armazenados no Azure.

[!INCLUDE [blob-storage-tool-selector](../../../includes/machine-learning-blob-storage-tool-selector.md)]

Depois que os clientes movevam dados locais para a nuvem, eles podem acessar seus dados de qualquer serviço do Azure para aproveitar todo o potencial do pacote de tecnologias do Azure. Os dados podem ser usados posteriormente, por exemplo, em Azure Machine Learning ou em um cluster HDInsight.

Exemplos para usar esses recursos do Azure estão no passo a passos do [SQL](sql-walkthrough.md) e do [HDInsight](hive-walkthrough.md) .

Para conferir uma discussão de cenários canônicos que usam o SSIS para atender às necessidades comerciais comuns em cenários de integração de dados híbridos, consulte o blog [Realizando mais com o Feature Pack dos serviços de integração do SQL Server para o Azure](https://techcommunity.microsoft.com/t5/sql-server-integration-services/doing-more-with-sql-server-integration-services-feature-pack-for/ba-p/388238) .

> [!NOTE]
> Para obter uma introdução completa ao Armazenamento de Blobs do Azure, consulte [Noções básicas do Blob do Azure](../../storage/blobs/storage-quickstart-blobs-dotnet.md) e [Serviço de Blob do Azure](/rest/api/storageservices/Blob-Service-Concepts).
> 
> 

## <a name="prerequisites"></a>Pré-requisitos
Para executar as tarefas descritas neste artigo, você deve ter uma assinatura do Azure e uma conta de armazenamento do Azure configurada. Você precisa do nome da conta de armazenamento do Azure e da chave de conta para carregar ou baixar dados.

* Para configurar uma **assinatura do Azure**, consulte [Avaliação gratuita de um mês](https://azure.microsoft.com/pricing/free-trial/).
* Para obter instruções sobre como criar uma **conta de armazenamento** e obter informações de conta e chave, consulte [sobre contas de armazenamento do Azure](../../storage/common/storage-account-create.md).

Para usar os **conectores SSIS**, você deverá baixar:

* **SQL Server 2014 ou 2016 Standard (ou mais recente)**: a instalação inclui os SQL Server Integration Services.
* **Microsoft SQL Server 2014 ou 2016 Integration Services Feature Pack para Azure**: esses conectores podem ser baixados, respectivamente, das páginas [SQL Server 2014 Integration Services](https://www.microsoft.com/download/details.aspx?id=47366) e [SQL Server 2016 Integration Services](https://www.microsoft.com/download/details.aspx?id=49492) .

> [!NOTE]
> O SSIS é instalado com o SQL Server, mas não está incluído na versão Express. Para obter informações sobre quais aplicativos estão incluídos nas várias edições do SQL Server, veja [Edições do SQL Server](https://www.microsoft.com/en-us/server-cloud/products/sql-server-editions/)
> 
> 

Para obter os materiais de treinamento sobre o SSIS, veja [Treinamento prático sobre o SSIS](https://www.microsoft.com/sql-server/training-certification)

Para obter informações sobre como começar a usar o SISS para compilar pacotes ETL (extração, transformação e carregamento) simples, veja [Tutorial do SSIS: criando um pacote ETL simples](/sql/integration-services/ssis-how-to-create-an-etl-package).

## <a name="download-nyc-taxi-dataset"></a>Baixar o conjunto de dados NYC Taxi
O exemplo descrito aqui usa um conjunto de dados disponível publicamente, o conjunto de dados [NYC Taxi Trips](https://www.andresmh.com/nyctaxitrips/) . O conjunto de dados é formado por cerca de 173 milhões de corridas de táxi em NYC no ano de 2013. Há dois tipos de dados: dados sobre detalhes da corrida e dados sobre tarifas. Como há um arquivo para cada mês, temos 24 arquivos, cada um com cerca de 2 GB descompactados.

## <a name="upload-data-to-azure-blob-storage"></a>Carregar dados no armazenamento de blob do Azure
Para mover dados do local para o Armazenamento de Blobs do Azure usando o pacote de recursos do SSIS, podemos usar uma instância da [**Tarefa de Carregamento de Blob do Azure**](/sql/integration-services/control-flow/azure-blob-upload-task), como mostrado aqui:

![configure-data-science-vm](./media/move-data-to-azure-blob-using-ssis/ssis-azure-blob-upload-task.png)

Os parâmetros usados pela tarefa são descritos aqui:

| Campo | Descrição |
| --- | --- |
| **AzureStorageConnection** |Especifica um Gerenciador de conexões de armazenamento do Azure existente ou cria um novo que se refere a uma conta de armazenamento do Azure que aponta para onde os arquivos de blob estão hospedados. |
| **BlobContainer** |Especifica o nome do contêiner de BLOB que contém os arquivos carregados como BLOBs. |
| **BlobDirectory** |Especifica o diretório de blob em que o arquivo carregado é armazenado como um blob de blocos. O diretório de blob é uma estrutura hierárquica virtual. Se o blob já existir, ele será substituído. |
| **LocalDirectory** |Especifica o diretório local que contém os arquivos a serem carregados. |
| **FileName** |Especifica um filtro de nome para selecionar arquivos com o padrão de nome especificado. Por exemplo, MySheet\*.xls\* inclui arquivos como MySheet001.xls e MySheetABC.xlsx |
| **TimeRangeFrom/TimeRangeTo** |Especifica um filtro de intervalo de tempo. Arquivos modificados após *TimeRangeFrom* e antes de *TimeRangeTo* são incluídos. |

> [!NOTE]
> As credenciais do **AzureStorageConnection** precisam estar corretas e o **BlobContainer** deve existir antes de tentar a transferência.
> 
> 

## <a name="download-data-from-azure-blob-storage"></a>Baixar dados do armazenamento de blob do Azure
Para baixar dados do armazenamento de Blobs do Azure para o armazenamento local com SSIS, use uma instância da [Tarefa de Download de Blob do Azure](/sql/integration-services/control-flow/azure-blob-download-task).

## <a name="more-advanced-ssis-azure-scenarios"></a>Cenários mais avançados de SSIS-Azure
O pacote de recursos do SSIS permite que fluxos mais complexos sejam tratados em conjunto por tarefas de empacotamento. Por exemplo, os dados de blob poderiam ser inseridos diretamente em um cluster do HDInsight cuja saída pudesse ser baixada de volta para um blob e então para o armazenamento local. O SSIS pode executar trabalhos de Hive e Pig em um cluster HDInsight usando conectores adicionais do SSIS:

* Para executar um script Hive em um cluster HDInsight do Azure com SSIS, use a [Tarefa Hive de HDInsight do Azure](/sql/integration-services/control-flow/azure-hdinsight-hive-task).
* Para executar um script Pig em um cluster HDInsight do Azure com SSIS, use a [Tarefa Pig de HDInsight do Azure](/sql/integration-services/control-flow/azure-hdinsight-pig-task).