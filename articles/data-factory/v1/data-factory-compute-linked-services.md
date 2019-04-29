---
title: Ambientes de computação com suporte do Azure Data Factory | Microsoft Docs
description: Saiba mais sobre os ambientes de computação que você pode usar em pipelines do Azure Data Factory (por exemplo, o Azure HDInsight) para transformar ou processar dados.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 6877a7e8-1a58-4cfb-bbd3-252ac72e4145
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 0e0a249c53c90d3d8d03dcdb5fbb4f11f31c54df
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60565711"
---
# <a name="compute-environments-supported-by-azure-data-factory"></a>Ambientes de computação com suporte do Azure Data Factory
> [!NOTE]
> Este artigo aplica-se à versão 1 do Azure Data Factory. Se você estiver usando a versão atual do serviço Data Factory, consulte [Compute serviços vinculados em ](../compute-linked-services.md).

Este artigo explica os ambientes de computação que você pode usar para processar ou transformar dados. Ele também fornece detalhes sobre as diferentes configurações (sob demanda versus “traga a sua própria”) que o Data Factory oferece suporte ao configurar serviços vinculados que vinculam esses ambientes de computação para um data factory do Azure.

A tabela a seguir fornece uma lista dos ambientes de computação com suporte do Data Factory e as atividades que podem ser executadas neles. 

| Ambiente de computação                      | Atividades                               |
| ---------------------------------------- | ---------------------------------------- |
| [Cluster HDInsight sob demanda](#azure-hdinsight-on-demand-linked-service) ou [seu próprio cluster HDInsight](#azure-hdinsight-linked-service) | [DotNet](data-factory-use-custom-activities.md), [Hive](data-factory-hive-activity.md), [Pig](data-factory-pig-activity.md), [MapReduce](data-factory-map-reduce.md), [Hadoop Streaming](data-factory-hadoop-streaming-activity.md) |
| [Lote do Azure](#azure-batch-linked-service) | [DotNet](data-factory-use-custom-activities.md) |
| [Azure Machine Learning](#azure-machine-learning-linked-service) | [Atividades de Machine Learning: Execução de lote e Recurso de atualização](data-factory-azure-ml-batch-execution-activity.md) |
| [Azure Data Lake Analytics](#azure-data-lake-analytics-linked-service) | [U-SQL da Análise Data Lake](data-factory-usql-activity.md) |
| [Azure SQL](#azure-sql-linked-service), [Azure SQL Data Warehouse](#azure-sql-data-warehouse-linked-service), [SQL Server](#sql-server-linked-service) | [Atividade de Procedimento Armazenado](data-factory-stored-proc-activity.md) |

## <a name="supported-hdinsight-versions-in-azure-data-factory"></a>Versões do HDInsight com suporte no Data Factory
O HDInsight do Azure oferece suporte a várias versões do cluster Hadoop que podem ser implantadas a qualquer momento. Cada opção de versão cria uma versão específica da distribuição de HDP (Hortonworks Data Platform) e um conjunto de componentes contidos nessa distribuição. 

A Microsoft atualiza a lista de versões do HDInsight com suporte com os componentes e correções mais recentes do ecossistema do Hadoop. Para obter informações detalhadas, consulte [Versões do HDInsight com suporte](../../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions).

> [!IMPORTANT]
> O HDInsight com base em Linux versão 3.3 foi desativado em 31 de julho de 2017. Os clientes dos Serviços Vinculados sob demanda do HDInsight no Data Factory versão 1 têm até 15 de dezembro de 2017 para testar e atualizar para uma versão mais recente do HDInsight. O HDInsight com base no Windows será desativado em 31 de julho de 2018.
>
> 

### <a name="after-the-retirement-date"></a>Após a data de desativação 

Após 15 de dezembro de 2017:

- Não será mais possível criar clusters do HDInsight com base em Linux versão 3.3 (ou versões anteriores) usando um serviço vinculado sob demanda do HDInsight na versão 1 do Data Factory. 
- Se [**osType** e as propriedades **Version** ](https://docs.microsoft.com/azure/data-factory/v1/data-factory-compute-linked-services#azure-hdinsight-on-demand-linked-service) não estiverem especificados explicitamente na definição JSON para um serviço vinculado sob demanda do HDInsight no Data Factory versão 1, o valor padrão será alterado de **Version=3.1, osType=Windows** para  **Version=\<versão mais recente de padrão HDI\>(https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning), osType=Linux**.

Após 31 de julho de 2018:

- Não será mais possível criar nenhuma versão de clusters HDInsight com base no Windowsusando um serviço vinculado sob demanda do HDInsight na versão 1 do Data Factory. 

### <a name="recommended-actions"></a>Ações recomendadas 

- Para garantir que você possa usar os componentes e correções mais recentes do ecossistema do Hadoop, atualize as propriedades [**osType** e **Versão**](https://docs.microsoft.com/azure/data-factory/v1/data-factory-compute-linked-services#azure-hdinsight-on-demand-linked-service) de definições afetadas do serviço vinculado sob demanda do HDInsight da versão 1 do Data Factory para versões mais recentes do HDInsight com base no Linux (HDInsight 3.6). 
- Antes de 15 de dezembro de 2017, teste o Data Factory versão 1 Hive, Pig, MapReduce, e as atividades de streaming do Hadoop que referenciam o serviço vinculado afetado. Certifique-se de que eles sejam compatíveis com os novos valores padrão **osType** e **Versão** (**Versão = 3.6**, **osType=Linux**) ou a versão explícita do HDInsight e tipo de SO para o qual você estiver atualizando. 
  Para saber mais sobre a compatibilidade, consulte [Migrar de um cluster HDInsight com base no Windows para um cluster baseado em Linux](https://docs.microsoft.com/azure/hdinsight/hdinsight-migrate-from-windows-to-linux) e [Quais são os componentes do Hadoop e as versões disponíveis com o HDInsight?](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#hortonworks-release-notes-associated-with-hdinsight-versions). 
- Para continuar a usar um serviço vinculado do HDInsight sob demanda da versão 1 do Data Factory para criar clusters HDInsight com base no Windows, defina explicitamente **osType** para **Windows** antes de 15 de dezembro de 2017. É recomendável que você migre para clusters HDInsight com base em Linux antes de 31 de julho de 2018. 
- Se você estiver usando um serviço vinculado sob demanda do HDInsight para executar a versão 1 do Data Factory DotNet Custom Activity, atualize a definição do JSON do DotNet Custom Activity para, em vez disso, usar um serviço vinculado do Lote do Azure. Para obter mais informações, consulte [Usar atividades personalizadas em um pipeline do Data Factory](https://docs.microsoft.com/azure/data-factory/v1/data-factory-use-custom-activities). 

> [!Note]
> Se você usar o dispositivo vinculado HDInsight existente do seu cluster, traga seu próprio na versão 1 do Data Factory ou um serviço vinculado HDInsight sob demanda e personalizado no Azure Data Factory, nenhuma ação será necessária. Nesses cenários, a política de suporte de versão mais recente dos clusters HDInsight já é imposta. 
>
> 


## <a name="on-demand-compute-environment"></a>Ambiente de computação sob demanda
Em uma configuração sob demanda, o Data Factory gerencia totalmente o ambiente de computação. O Data Factory cria automaticamente o ambiente de computação antes que trabalho seja enviado para o processamento de dados. Quando o trabalho é concluído, o Data Factory remove o ambiente de computação. 

Você pode criar um serviço vinculado para um ambiente de computação sob demanda. Use o serviço vinculado para configurar o ambiente de computação e controlar as configurações granulares para a execução do trabalho, gerenciamento de cluster e ações de inicialização.

> [!NOTE]
> Atualmente, a configuração sob demanda tem suporte somente para clusters HDInsight.
> 

## <a name="azure-hdinsight-on-demand-linked-service"></a>Serviço vinculado do Azure HDInsight sob demanda
O Data Factory pode criar automaticamente um cluster HDInsight sob demanda com base em Linux ou Windows para processamento de dados. O cluster é criado na mesma região que a conta de armazenamento associada com o cluster. Use a propriedade JSON **linkedServiceName** para criar o cluster.

Observe os seguintes pontos *chave* sobre o serviço vinculado HDInsight sob demanda:

* O cluster HDInsight sob demanda não aparece na sua assinatura do Azure. O serviço do Azure Data Factory gerencia o cluster HDInsight sob demanda em seu nome.
* Os logs para trabalhos que são executados em um cluster HDInsight sob demanda são copiados para a conta de armazenamento associada ao cluster HDInsight. Para acessar esses logs, no portal do Azure, vá para o painel **Detalhes de Execução de Atividade**. Para obter mais informações, consulte [Monitorar e gerenciar pipelines](data-factory-monitor-manage-pipelines.md).
* Você é cobrado somente pelo tempo em que o cluster HDInsight está ativo e executando trabalhos.

> [!IMPORTANT]
> Em geral, são necessários *20 minutos* ou mais para provisionar um cluster Azure HDInsight sob demanda.
>
> 

### <a name="example"></a>Exemplo
O JSON a seguir define um serviço vinculado HDInsight sob demanda baseado em Linux. O Data Factory cria automaticamente um cluster HDInsight *com base em Linux* ao processar uma fatia de dados. 

```json
{
    "name": "HDInsightOnDemandLinkedService",
    "properties": {
        "type": "HDInsightOnDemand",
        "typeProperties": {
            "version": "3.6",
            "osType": "Linux",
            "clusterSize": 1,
            "timeToLive": "00:05:00",            
            "linkedServiceName": "AzureStorageLinkedService"
        }
    }
}
```

> [!IMPORTANT]
> O cluster HDInsight cria um *contêiner padrão* no armazenamento de Blob do Azure que você especifica na propriedade JSON **linkedServiceName**. Por design, o HDInsight não exclui esse contêiner quando o cluster é excluído. Em um serviço vinculado HDInsight sob demanda, um cluster HDInsight é criado sempre que uma fatia é processada, a menos que haja um cluster ativo existente (**timeToLive**). O cluster é excluído quando o processamento é concluído. 
>
> Quanto mais fatias forem processadas, mais contêineres você verá no armazenamento de Blobs. Se você não precisa dos contêineres para trabalhos de solução de problemas, convém excluir contêineres para reduzir o custo de armazenamento. Os nomes desses contêineres seguem um padrão: `adf<your Data Factory name>-<linked service name>-<date and time>`. Você pode usar uma ferramenta como o [Gerenciador de Armazenamento da Microsoft](https://storageexplorer.com/) para excluir contêineres no armazenamento de Blob.
>
> 

### <a name="properties"></a>propriedades
| Propriedade                     | DESCRIÇÃO                              | Obrigatório |
| ---------------------------- | ---------------------------------------- | -------- |
| tipo                         | Defina a propriedade de tipo como **HDInsightOnDemand**. | Sim      |
| clusterSize                  | O número de nós de trabalho e de dados no cluster. O cluster HDInsight é criado com 2 nós de cabeçalho, além do número de nós de trabalho que você especifica para esta propriedade. Os nós são de tamanho Standard_D3, que tem 4 núcleos. Um cluster de nó de 4-worker leva 24 núcleos (4\*4 = 16 núcleos para nós de trabalho, mais 2\*4 = 8 núcleos para nós de cabeçalho). Para obter detalhes sobre a camada de Standard_D3, consulte [Criar clusters do Hadoop com base em Linux no HDInsight](../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md). | Sim      |
| timeToLive                   | O tempo ocioso permitido para o cluster HDInsight sob demanda. Especifica quanto tempo o cluster HDInsight sob demanda fica ativo quando uma execução de atividade é concluída, se não houver nenhum outro trabalho ativo no cluster.<br /><br />Por exemplo, se uma atividade executada levar 6 minutos e o **timeToLive** é definido como 5 minutos, o cluster permanece ativo durante 5 minutos após os 6 minutos de processamento da execução da atividade. Se outra execução da atividade é executada na janela de 6 minutos, ela é processada pelo mesmo cluster.<br /><br />Criar um cluster do HDInsight sob demanda é uma operação cara (pode levar algum tempo). Use essa configuração conforme necessário para melhorar o desempenho de uma fábrica de dados com a reutilização de um cluster do HDInsight sob demanda.<br /><br />Se você definir o valor **timeToLive** como **0**, o cluster é excluído assim que terminar de executar a atividade. No entanto, se você definir um valor alto, o cluster pode permanecer ocioso, resultando desnecessariamente em altos custos. É importante definir o valor apropriado com base em suas necessidades.<br /><br />Se o valor **timeToLive** estiver definido corretamente, vários pipelines podem compartilhar a instância do cluster HDInsight sob demanda. | Sim      |
| version                      | A versão do cluster do HDInsight. Para versões de HDInsight permitidas, consulte [Versões do HDInsight com suporte](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#supported-hdinsight-versions). Se esse valor não é especificado, é usada a [versão mais recente de padrão HDI](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning). | Não        |
| linkedServiceName            | O serviço vinculado do Armazenamento do Azure a ser usado pelo cluster sob demanda para armazenar e processar dados. O cluster HDInsight é criado na mesma região que a conta de armazenamento.<p>Atualmente, não é possível criar um cluster HDInsight sob demanda que use um Azure Data Lake Store como o armazenamento. Se você deseja armazenar os dados de resultado do processo do HDInsight no Data Lake Store, use a Atividade de Cópia para copiar os dados do armazenamento de Blob para o Data Lake Store. </p> | Sim      |
| additionalLinkedServiceNames | Especifica as contas de armazenamento adicional para o serviço vinculado do HDInsight. O Data Factory registra as contas de armazenamento em seu nome. Essas contas de armazenamento devem estar na mesma região que o cluster HDInsight. O cluster HDInsight é criado na mesma região que a conta de armazenamento que é especificada pela propriedade **linkedServiceName**. | Não        |
| osType                       | O tipo de sistema operacional. Os valores permitidos são **Linux** e **Windows**. Se esse valor não for especificado, é usado **Linux**.  <br /><br />É altamente recomendável usar clusters HDInsight com base em Linux. A data de baixa do HDInsight no Windows é 31 de julho de 2018. | Não        |
| hcatalogLinkedServiceName    | O nome do serviço vinculado do SQL Azure que aponta para o banco de dados HCatalog. O cluster HDInsight sob demanda é criado usando o banco de dados SQL como o metastore. | Não        |

#### <a name="example-linkedservicenames-json"></a>Exemplo: LinkedServiceNames JSON

```json
"additionalLinkedServiceNames": [
    "otherLinkedServiceName1",
    "otherLinkedServiceName2"
  ]
```

### <a name="advanced-properties"></a>Propriedades avançadas
Para a configuração granular do cluster HDInsight sob demanda, você pode especificar as seguintes propriedades:

| Propriedade               | DESCRIÇÃO                              | Obrigatório |
| :--------------------- | :--------------------------------------- | :------- |
| coreConfiguration      | Especifica os parâmetros de configuração do núcleo (core-site.xml) para o cluster HDInsight a ser criado. | Não        |
| hBaseConfiguration     | Especifica os parâmetros de configuração HBase (hbase-site.xml) para o cluster HDInsight. | Não        |
| hdfsConfiguration      | Especifica os parâmetros de configuração HDFS (hdfs-site.xml) para o cluster HDInsight. | Não        |
| hiveConfiguration      | Especifica os parâmetros de configuração do Hive (hive-site.xml) para o cluster HDInsight. | Não        |
| mapReduceConfiguration | Especifica os parâmetros de configuração do MapReduce (mapred-site.xml) para o cluster HDInsight. | Não        |
| oozieConfiguration     | Especifica os parâmetros de configuração do Oozie (oozie-site.xml) para o cluster HDInsight. | Não        |
| stormConfiguration     | Especifica os parâmetros de configuração do Storm (storm-site.xml) para o cluster HDInsight. | Não        |
| yarnConfiguration      | Especifica os parâmetros de configuração do YARN (yarn-site.xml) para o cluster HDInsight. | Não        |

#### <a name="example-on-demand-hdinsight-cluster-configuration-with-advanced-properties"></a>Exemplo: Configuração de cluster HDInsight sob demanda com propriedades avançadas

```json
{
  "name": " HDInsightOnDemandLinkedService",
  "properties": {
    "type": "HDInsightOnDemand",
    "typeProperties": {
      "version": "3.6",
      "osType": "Linux",
      "clusterSize": 16,
      "timeToLive": "01:30:00",
      "linkedServiceName": "adfods1",
      "coreConfiguration": {
        "templeton.mapper.memory.mb": "5000"
      },
      "hiveConfiguration": {
        "templeton.mapper.memory.mb": "5000"
      },
      "mapReduceConfiguration": {
        "mapreduce.reduce.java.opts": "-Xmx4000m",
        "mapreduce.map.java.opts": "-Xmx4000m",
        "mapreduce.map.memory.mb": "5000",
        "mapreduce.reduce.memory.mb": "5000",
        "mapreduce.job.reduce.slowstart.completedmaps": "0.8"
      },
      "yarnConfiguration": {
        "yarn.app.mapreduce.am.resource.mb": "5000",
        "mapreduce.map.memory.mb": "5000"
      },
      "additionalLinkedServiceNames": [
        "datafeeds",
        "adobedatafeed"
      ]
    }
  }
}
```

### <a name="node-sizes"></a>Tamanhos dos nós
Para especificar o tamanho do cabeçalho, dados e nós ZooKeeper, use as seguintes propriedades: 

| Propriedade          | DESCRIÇÃO                              | Obrigatório |
| :---------------- | :--------------------------------------- | :------- |
| headNodeSize      | Define o tamanho do nó de cabeçalho. O valor padrão é **Standard_D3**. Para obter detalhes, consulte [Especificar tamanho de nó](#specify-node-sizes). | Não        |
| dataNodeSize      | Define o tamanho do nó de dados. O valor padrão é **Standard_D3**. | Não        |
| zookeeperNodeSize | Define o tamanho do nó ZooKeeper. O valor padrão é **Standard_D3**. | Não        |

#### <a name="specify-node-sizes"></a>Especificar tamanho de nó
Para obter valores de cadeia de caracteres que você deve especificar para as propriedades descritas na seção anterior, consulte [Tamanhos de máquina virtual](../../virtual-machines/linux/sizes.md). Os valores devem estar de acordo com os cmdlets e APIs referenciados em [Tamanhos de máquina virtual](../../virtual-machines/linux/sizes.md). O tamanho do nó de dados Grande (padrão) tem 7 GB de memória. Isso pode não ser suficiente para seu cenário. 

Se você quiser criar nós de cabeçalho e nós de trabalho com tamanho D4, especifique **Standard_D4** como o valor para as propriedades **headNodeSize** e **dataNodeSize**: 

```json
"headNodeSize": "Standard_D4",    
"dataNodeSize": "Standard_D4",
```

Se você definir um valor incorreto para essas propriedades, você verá a seguinte mensagem:

  Falha ao criar cluster. Exceção: Não foi possível concluir a operação de criação do cluster. Falha na operação com o código '400'. Cluster deixou para trás estado: “Erro”. Mensagem: 'PreClusterCreationValidationFailure'. 
  
Se você vir esta mensagem, certifique-se que você está usando o cmdlet e os nomes da API da tabela em [Tamanhos de máquina virtual ](../../virtual-machines/linux/sizes.md).  

> [!NOTE]
> Atualmente, o Azure Data Factory não dá suporte a clusters HDInsight usando o Azure Data Lake Store como o armazenamento primário. Use o Armazenamento do Azure como repositório primário para clusters HDInsight. 
>
> 


## <a name="bring-your-own-compute-environment"></a>Traga seu próprio ambiente de computação
Você pode registrar um ambiente de computação existente como um serviço vinculado na Data Factory. Você gerencia o ambiente de computação. O serviço do Data Factory usa o ambiente de computação para executar atividades.

Esse tipo de configuração tem suporte para os ambientes de computação a seguir:

* Azure HDInsight
* Lote do Azure
* Machine Learning do Azure
* Análise Azure Data Lake
* Banco de dados SQL do Azure, Azure SQL Data Warehouse, SQL Server

## <a name="azure-hdinsight-linked-service"></a>Serviço vinculado do Azure HDInsight
Você pode criar um serviço vinculado HDInsight para registrar seu próprio cluster HDInsight com o Data Factory.

### <a name="example"></a>Exemplo

```json
{
  "name": "HDInsightLinkedService",
  "properties": {
    "type": "HDInsight",
    "typeProperties": {
      "clusterUri": " https://<hdinsightclustername>.azurehdinsight.net/",
      "userName": "admin",
      "password": "<password>",
      "linkedServiceName": "MyHDInsightStoragelinkedService"
    }
  }
}
```

### <a name="properties"></a>propriedades
| Propriedade          | DESCRIÇÃO                              | Obrigatório |
| ----------------- | ---------------------------------------- | -------- |
| tipo              | Defina a propriedade de tipo como **HDInsight**. | Sim      |
| clusterUri        | A URI do cluster HDInsight.        | Sim      |
| Nome de Usuário          | O nome da conta de usuário a ser usado para se conectar a um cluster HDInsight existente. | Sim      |
| Senha          | A senha para a conta de usuário.   | Sim      |
| linkedServiceName | O nome do serviço vinculado de armazenamento que refere-se ao armazenamento de Blob usado pelo cluster HDInsight. <p>No momento, você não pode especificar um serviço vinculado do Azure Data Lake Store para essa propriedade. Se o cluster HDInsight tem acesso ao Data Lake Store, você pode acessar dados no Data Lake Store de scripts de Hive ou Pig. </p> | Sim      |

## <a name="azure-batch-linked-service"></a>Serviço vinculado do Lote do Azure
Você pode criar um serviço vinculado do Lote para registrar um pool de Lote de máquinas virtuais (VMs) para uma fábrica de dados. Você pode executar atividades personalizadas do .NET usando o Lote ou o HDInsight.

Se essa é a sua primeira vez usando o serviço de Lote:

* Saiba mais sobre [Noções básicas de Lote do Azure](../../batch/batch-technical-overview.md).
* Saiba mais sobre o cmdlet [New-AzureBatchAccount](https://msdn.microsoft.com/library/mt125880.aspx). Use este cmdlet para criar uma conta do Lote. Ou, você pode criar a conta do Lote usando o [portal do Azure](../../batch/batch-account-create-portal.md). Para obter informações detalhadas sobre como usar o cmdlet, consulte [Usando o PowerShell para gerenciar uma conta de Lote](https://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx).
* Saiba mais sobre o cmdlet [New-AzureBatchPool novo](https://msdn.microsoft.com/library/mt125936.aspx). Use este cmdlet para criar um pool do Lote.

### <a name="example"></a>Exemplo

```json
{
  "name": "AzureBatchLinkedService",
  "properties": {
    "type": "AzureBatch",
    "typeProperties": {
      "accountName": "<Azure Batch account name>",
      "accessKey": "<Azure Batch account key>",
      "poolName": "<Azure Batch pool name>",
      "linkedServiceName": "<Specify associated storage linked service reference here>"
    }
  }
}
```

Para a propriedade **accountName**, acrescente **.\<nome da região\>** ao nome da sua conta do lote. Por exemplo: 

```json
"accountName": "mybatchaccount.eastus"
```

Outra opção é fornecer a ponta de extremidade **batchUri**. Por exemplo: 

```json
"accountName": "adfteam",
"batchUri": "https://eastus.batch.azure.com",
```

### <a name="properties"></a>propriedades
| Propriedade          | DESCRIÇÃO                              | Obrigatório |
| ----------------- | ---------------------------------------- | -------- |
| Tipo              | Defina a propriedade de tipo como **AzureBatch**. | Sim      |
| accountName       | O nome da conta do Lote.         | Sim      |
| accessKey         | A chave de acesso para a conta do Lote.  | Sim      |
| poolName          | O nome do pool de máquinas virtuais.    | Sim      |
| linkedServiceName | O nome do serviço vinculado de armazenamento associado a este Lote de serviço vinculado. Esse serviço vinculado é usado para arquivos de preparo que são necessários para executar a atividade e para armazenar logs de execução da atividade. | Sim      |

## <a name="azure-machine-learning-linked-service"></a>Serviço vinculado do Azure Machine Learning
Você pode criar um serviço vinculado do Machine Learning para registrar um ponto de extremidade de pontuação do lote do Machine Learning a uma fábrica de dados.

### <a name="example"></a>Exemplo

```json
{
  "name": "AzureMLLinkedService",
  "properties": {
    "type": "AzureML",
    "typeProperties": {
      "mlEndpoint": "https://[batch scoring endpoint]/jobs",
      "apiKey": "<apikey>"
    }
  }
}
```

### <a name="properties"></a>propriedades
| Propriedade   | DESCRIÇÃO                              | Obrigatório |
| ---------- | ---------------------------------------- | -------- |
| Type       | Defina a propriedade de tipo como **AzureML**. | Sim      |
| mlEndpoint | A URL de pontuação do lote.                   | Sim      |
| apiKey     | A API do modelo de workspace publicada.     | Sim      |

## <a name="azure-data-lake-analytics-linked-service"></a>Serviço vinculado do Azure Data Lake Analytics
Você pode criar um serviço vinculado do Data Lake Analytics para vincular um serviço de computação do Data Lake Analytics a uma fábrica de dados do Azure. A atividade de U-SQL do Data Lake Analytics no pipeline se refere a esse serviço vinculado. 

A tabela a seguir descreve as propriedades genéricas que são usadas na definição JSON:

| Propriedade                 | DESCRIÇÃO                              | Obrigatório                                 |
| ------------------------ | ---------------------------------------- | ---------------------------------------- |
| Tipo                 | Defina a propriedade de tipo como **AzureDataLakeAnalytics**. | Sim                                      |
| accountName          | O nome de conta do Data Lake Analytics.  | Sim                                      |
| dataLakeAnalyticsUri | O URI do Data Lake Analytics.           | Não                                        |
| subscriptionId       | A ID de assinatura do Azure.                    | Não <br /><br />(Se não for especificada, é usada a assinatura da fábrica de dados.) |
| resourceGroupName    | O nome do grupo de recursos do Azure.                | Não <br /><br /> (Se não for especificado, é usado o grupo de recursos da fábrica de dados.) |

### <a name="authentication-options"></a>Opções de autenticação
Para o seu serviço vinculado do Data Lake Analytics, você pode escolher entre autenticação usando uma entidade de serviço ou uma credencial do usuário.

#### <a name="service-principal-authentication-recommended"></a>Autenticação de entidade de serviço (recomendada)
Para usar a autenticação de entidade de serviço, registre uma entidade de aplicativo no Azure Active Directory (Azure AD). Em seguida, conceda acesso do Azure AD ao Data Lake Store. Para encontrar as etapas detalhadas, consulte [Autenticação de serviço a serviço](../../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Anote os seguintes valores, que são usados para definir o serviço vinculado:
* ID do aplicativo
* Chave do aplicativo 
* ID do locatário

Use a autenticação de entidade de serviço especificando as seguintes propriedades:

| Propriedade                | DESCRIÇÃO                              | Obrigatório |
| :---------------------- | :--------------------------------------- | :------- |
| servicePrincipalId  | A ID do cliente do aplicativo.     | Sim      |
| servicePrincipalKey | A chave do aplicativo.           | Sim      |
| tenant              | Especifique as informações de locatário (nome de domínio ou ID do locatário) em que o aplicativo está localizado. Para obter essas informações, passe o mouse no canto superior direito do portal do Azure. | Sim      |

**Exemplo: Autenticação de entidade de serviço**
```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "adftestaccount",
            "dataLakeAnalyticsUri": "datalakeanalyticscompute.net",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<optional, subscription id of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        }
    }
}
```

#### <a name="user-credential-authentication"></a>Autenticação de credenciais de usuário
Para autenticação de credenciais de usuário para o Data Lake Analytics, especifique as seguintes propriedades:

| Propriedade          | DESCRIÇÃO                              | Obrigatório |
| :---------------- | :--------------------------------------- | :------- |
| autorização | No Editor do Data Factory, selecione o botão **Autorizar**. Insira as credenciais que atribuem a URL de autorização gerada automaticamente para essa propriedade. | Sim      |
| sessionId     | A ID de sessão OAuth da sessão de autorização OAuth. Cada ID da sessão é exclusiva e pode ser usada somente uma vez. Essa configuração é gerada automaticamente quando você usa o Editor do Data Factory. | Sim      |

**Exemplo: Autenticação da credencial do usuário**
```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "adftestaccount",
            "dataLakeAnalyticsUri": "datalakeanalyticscompute.net",
            "authorization": "<authcode>",
            "sessionId": "<session ID>", 
            "subscriptionId": "<optional, subscription id of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        }
    }
}
```

#### <a name="token-expiration"></a>Expiração do token
O código de autorização gerado usando o botão **Autorizar** expira após um determinado período. 

Talvez você veja a seguinte mensagem de erro quando o token de autenticação expirar: 

  Erro de operação de credencial: invalid_grant - AADSTS70002: Erro ao validar credenciais. AADSTS70008: A concessão de acesso fornecida expirou ou foi revogada. ID de Rastreamento: d18629e8-af88-43c5-88e3-d8419eb1fca1 ID de Correlação: fac30a0c-6be6-4e02-8d69-a776d2ffefd7 Carimbo de data/hora: 2015-12-15 21:09:31Z

A tabela a seguir mostra expirações por tipo de conta de usuário: 

| Tipo de usuário                                | Expira após                            |
| :--------------------------------------- | :--------------------------------------- |
| Contas de usuário que *não* são gerenciadas pelo Azure AD (Hotmail, Live e assim por diante) | 12 horas.                                 |
| Contas de usuário que *são* gerenciadas pelo Azure AD | 14 dias após a última execução da fatia. <br /><br />90 dias, se uma fatia com base em serviços vinculados do OAuth for executada pelo menos uma vez a cada 14 dias. |

Para evitar ou resolver este erro, reautorize usando o botão **Autorizar** quando o token expirar. Em seguida, reimplante o serviço vinculado. Você também pode gerar valores para as propriedades **sessionId** e **authorization** programaticamente usando o código a seguir:

```csharp
if (linkedService.Properties.TypeProperties is AzureDataLakeStoreLinkedService ||
    linkedService.Properties.TypeProperties is AzureDataLakeAnalyticsLinkedService)
{
    AuthorizationSessionGetResponse authorizationSession = this.Client.OAuth.Get(this.ResourceGroupName, this.DataFactoryName, linkedService.Properties.Type);

    WindowsFormsWebAuthenticationDialog authenticationDialog = new WindowsFormsWebAuthenticationDialog(null);
    string authorization = authenticationDialog.AuthenticateAAD(authorizationSession.AuthorizationSession.Endpoint, new Uri("urn:ietf:wg:oauth:2.0:oob"));

    AzureDataLakeStoreLinkedService azureDataLakeStoreProperties = linkedService.Properties.TypeProperties as AzureDataLakeStoreLinkedService;
    if (azureDataLakeStoreProperties != null)
    {
        azureDataLakeStoreProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeStoreProperties.Authorization = authorization;
    }

    AzureDataLakeAnalyticsLinkedService azureDataLakeAnalyticsProperties = linkedService.Properties.TypeProperties as AzureDataLakeAnalyticsLinkedService;
    if (azureDataLakeAnalyticsProperties != null)
    {
        azureDataLakeAnalyticsProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeAnalyticsProperties.Authorization = authorization;
    }
}
```

Para obter detalhes sobre as classes do Data Factory que são usados neste exemplo de código, consulte:
* [AzureDataLakeStoreLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx)
* [Classe AzureDataLakeAnalyticsLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx)
* [Classe AuthorizationSessionGetResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx)

Adicione uma referência a Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll para a classe **WindowsFormsWebAuthenticationDialog**. 

## <a name="azure-sql-linked-service"></a>Serviço vinculado de SQL do Azure
Você pode criar um serviço vinculado de SQL e usá-lo com a [Atividade de Procedimento Armazenado](data-factory-stored-proc-activity.md) para invocar um procedimento armazenado de um pipeline do Data Factory. Para obter mais informações, consulte o artigo [Conector de SQL do Azure](data-factory-azure-sql-connector.md#linked-service-properties).

## <a name="azure-sql-data-warehouse-linked-service"></a>Serviço vinculado do SQL Data Warehouse do Azure
Você pode criar um serviço vinculado do SQL Data Warehouse e usá-lo com a [Atividade de Procedimento Armazenado](data-factory-stored-proc-activity.md) para invocar um procedimento armazenado de um pipeline do Data Factory. Para obter mais informações, consulte [Conector do SQL Data Warehouse do Azure](data-factory-azure-sql-data-warehouse-connector.md#linked-service-properties).

## <a name="sql-server-linked-service"></a>Serviço vinculado do SQL Server
Você pode criar um serviço vinculado do SQL Server e usá-lo com a [Atividade de Procedimento Armazenado](data-factory-stored-proc-activity.md) para invocar um procedimento armazenado de um pipeline do Data Factory. Para obter mais informações, consulte [Conector do SQL Server](data-factory-sqlserver-connector.md#linked-service-properties).

