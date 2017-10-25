---
title: "Ambientes de computação com suporte do Azure Data Factory | Microsoft Docs"
description: "Saiba mais sobre os ambientes de computação que você pode usar em pipelines do Azure Data Factory (por exemplo, o Azure HDInsight) para transformar ou processar dados."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: monicar
ms.assetid: 6877a7e8-1a58-4cfb-bbd3-252ac72e4145
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/25/2017
ms.author: shlo
robots: noindex
ms.openlocfilehash: bccdfdc486f5e79f034c5a940e42c5f230283a50
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="compute-environments-supported-by-azure-data-factory"></a>Ambientes de computação com suporte do Azure Data Factory
> [!NOTE]
> Este artigo se aplica à versão 1 do Data Factory, que está com GA (disponibilidade geral). Se você estiver usando a versão 2 do serviço do Data Factory, que está em versão prévia, consulte [Compute linked services in V2](../compute-linked-services.md) (Serviços vinculados de computação na V2).

Este artigo explica diferentes ambientes de computação que você pode usar para processar ou transformar dados. Ele também fornece detalhes sobre as diferentes configurações (sob demanda versus traga a sua própria) com suporte pela Data Factory ao configurar serviços vinculados que vinculam esses ambientes de computação para uma Azure Data Factory.

A tabela a seguir fornece uma lista dos ambientes de computação com suporte do Data Factory e as atividades que podem ser executadas neles. 

| Ambiente de computação | atividades |
| --- | --- |
| [Cluster HDInsight sob demanda](#azure-hdinsight-on-demand-linked-service) ou [seu próprio cluster HDInsight](#azure-hdinsight-linked-service) |[DotNet](data-factory-use-custom-activities.md), [Hive](data-factory-hive-activity.md), [Pig](data-factory-pig-activity.md), [MapReduce](data-factory-map-reduce.md), [Hadoop Streaming](data-factory-hadoop-streaming-activity.md) |
| [Lote do Azure](#azure-batch-linked-service) |[DotNet](data-factory-use-custom-activities.md) |
| [Azure Machine Learning](#azure-machine-learning-linked-service) |[Atividades de Machine Learning: execução do Lote e recurso de atualização](data-factory-azure-ml-batch-execution-activity.md) |
| [Análise Azure Data Lake](#azure-data-lake-analytics-linked-service) |[U-SQL da Análise Data Lake](data-factory-usql-activity.md) |
| [Azure SQL](#azure-sql-linked-service), [Azure SQL Data Warehouse](#azure-sql-data-warehouse-linked-service), [SQL Server](#sql-server-linked-service) |[Procedimento armazenado](data-factory-stored-proc-activity.md) |

## <a name="supported-hdinsight-versions-in-azure-data-factory"></a>Versões com suporte do HDInsight no Azure Data Factory
O HDInsight do Azure dá suporte a várias versões do cluster Hadoop que podem ser implantadas a qualquer momento. Cada opção de versão cria uma versão específica da distribuição de HDP (Hortonworks Data Platform) e um conjunto de componentes contidos nessa distribuição. A Microsoft segue atualizando a lista das versões com suporte do HDInsight para fornecer as correções e componentes mais recentes do ecossistema do Hadoop. O HDInsight 3.2 foi preterido no dia 1 de abril de 2017. Para saber mais, confira [Versões do HDInsight com suporte](../../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions).

Isso afeta os Azure Data Factories existentes que têm atividades em execução em clusters HDInsight 3.2. Recomendamos que os usuários adotem as diretrizes na seção abaixo para atualizar os Data Factories afetados:

### <a name="for-linked-services-pointing-to-your-own-hdinsight-clusters"></a>Para serviços vinculados que apontam para seus próprios clusters HDInsight
* **Serviços vinculados HDInsight que apontam para seus próprios clusters HDInsight 3.2 ou inferiores:**

  O Azure Data Factory dá suporte ao envio de trabalhos para clusters HDInsight do HDI 3.1 até [a versão mais recente do HDInsight com suporte](../../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions). No entanto, você não pode mais criar um cluster HDInsight 3.2 após dia 1 de abril de 2017 com base na política de substituição documentada em [versões do HDInsight com suporte](../../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions).  

  **Recomendações:** 
  * Execute testes para garantir a compatibilidade das atividades que fazem referência a esses serviços vinculados para [a versão mais recente do HDInsight com suporte](../../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions) com informações documentadas em [Componentes do Hadoop disponíveis com diferentes versões do HDInsight](../../hdinsight/hdinsight-component-versioning.md#hadoop-components-available-with-different-hdinsight-versions) e [Notas de versão do Hortonworks associadas a versões do HDInsight](../../hdinsight/hdinsight-component-versioning.md#hortonworks-release-notes-associated-with-hdinsight-versions).
  * Atualize seu cluster HDInsight 3.2 para [a versão mais recente do HDInsight com suporte](../../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions) para obter as correções e componentes do ecossistema do Hadoop mais recentes. 

* **Serviços vinculados HDInsight que apontam para seus próprios clusters HDInsight 3.3 ou superiores:**

  O Azure Data Factory dá suporte ao envio de trabalhos para clusters HDInsight do HDI 3.1 até [a versão mais recente do HDInsight com suporte](../../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions). 
  
  **Recomendações:** 
  * Nenhuma ação é necessária da perspectiva do Data Factory. No entanto, se você estiver em uma versão inferior do HDInsight, ainda é recomendável atualizar para [a versão mais recente do HDInsight com suporte](../../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions) para obter as correções e componentes do ecossistema do Hadoop mais recentes.

### <a name="for-hdinsight-on-demand-linked-services"></a>Para serviços vinculados do HDInsight sob demanda
* **A versão 3.2 ou inferior é especificada na definição de JSON de serviços vinculados do HDInsight sob demanda:**
  
  O Azure Data Factory dará suporte à criação de clusters HDInsight sob demanda de versão 3.3 ou mais a partir de **15/05/2017**. Além disso, o término do suporte a serviços vinculados do HDInsight 3.2 sob demanda existentes é estendido para **15/07/2017**.  

  **Recomendações:** 
  * Execute testes para garantir a compatibilidade das atividades que fazem referência a esses serviços vinculados para [a versão mais recente do HDInsight com suporte](../../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions) com informações documentadas em [Componentes do Hadoop disponíveis com diferentes versões do HDInsight](../../hdinsight/hdinsight-component-versioning.md#hadoop-components-available-with-different-hdinsight-versions) e [Notas de versão do Hortonworks associadas a versões do HDInsight](../../hdinsight/hdinsight-component-versioning.md#hortonworks-release-notes-associated-with-hdinsight-versions).
  * Antes de **15/07/2017**, atualize a propriedade Version na definição de JSON do serviço vinculado HDI sob demanda para [a versão mais recente do HDInsight com suporte](../../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions) para obter as correções e componentes do ecossistema do Hadoop mais recentes. Para obter uma definição de JSON detalhada, consulte a [Amostra de serviço vinculado do HDInsight sob demanda do Azure](#azure-hdinsight-on-demand-linked-service). 

* **Versão não especificada em serviços vinculados do HDInsight sob demanda:**
  
  O Azure Data Factory dará suporte à criação de clusters HDInsight sob demanda de versão 3.3 ou mais a partir de **15/05/2017**. Além disso, o término do suporte a serviços vinculados do HDInsight 3.2 sob demanda existentes é estendido para **15/07/2017**. 

  Antes de **15/07/2017**, se deixados em branco, os valores padrão para as propriedades version e osType são: 

  | Propriedade | Valor Padrão | Obrigatório |
  | --- | --- | --- |
  Versão   | HDI 3.1 para o cluster do Windows e HDI 3.2 para o cluster do Linux.| Não
  osType | O padrão é Windows | Não

  Após **15/07/2017**, se deixados em branco, os valores padrão para as propriedades version e osType são:

  | Propriedade | Valor Padrão | Obrigatório |
  | --- | --- | --- |
  Versão   | HDI 3.3 para o cluster do Windows e HDI 3.5 para o cluster do Linux.    | Não
  osType | O padrão é o Linux | Não

  **Recomendações:** 
  * Antes de **15/07/2017**, execute testes para garantir a compatibilidade das atividades que fazem referência a esses serviços vinculados para [a versão mais recente do HDInsight com suporte](../../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions) com informações documentadas em [Componentes do Hadoop disponíveis com diferentes versões do HDInsight](../../hdinsight/hdinsight-component-versioning.md#hadoop-components-available-with-different-hdinsight-versions) e [Notas de versão do Hortonworks associadas a versões do HDInsight](../../hdinsight/hdinsight-component-versioning.md#hortonworks-release-notes-associated-with-hdinsight-versions).  
  * Depois de **15/07/2017**, verifique se você especificou explicitamente os valores osType e de versão se desejar substituir as configurações padrão. 

>[!Note]
>Atualmente, o Azure Data Factory não dá suporte a clusters HDInsight usando o Azure Data Lake Store como repositório primário. Use o Armazenamento do Azure como repositório primário para clusters HDInsight. 
>  
>  

## <a name="on-demand-compute-environment"></a>Ambiente de computação sob demanda
Nesse tipo de configuração, o ambiente de computação é totalmente gerenciado pelo serviço do Azure Data Factory. Ele é automaticamente criado pelo serviço Data Factory antes de um trabalho ser enviado a fim de processar os dados e é removido após a conclusão do trabalho. Você pode criar um serviço vinculado para o ambiente de computação sob demanda, configurá-lo e controlar as configurações granulares da execução do trabalho, gerenciamento de cluster e ações de inicialização.

> [!NOTE]
> A configuração sob demanda tem suporte somente para clusters do Azure HDInsight.
> 
> 

## <a name="azure-hdinsight-on-demand-linked-service"></a>Serviço vinculado do Azure HDInsight sob demanda
O serviço Azure Data Factory pode criar automaticamente um cluster HDInsight sob demanda baseado em Windows/Linux para processar dados. O cluster é criado na mesma região que a conta de armazenamento (propriedade linkedServiceName em JSON) associada ao cluster.

Observe os seguintes pontos **importantes** sobre o serviço vinculado HDInsight sob demanda:

* Você não vê o cluster HDInsight sob demanda criado na sua assinatura do Azure. O serviço do Azure Data Factory gerencia o cluster HDInsight sob demanda em seu nome.
* Os logs para trabalhos que são executados em um cluster HDInsight sob demanda são copiados para a conta de armazenamento associada ao cluster HDInsight. Você pode acessar esses logs do Portal do Azure na folha **Detalhes de execução da atividade** . Consulte o artigo [Monitorar e gerenciar pipelines](data-factory-monitor-manage-pipelines.md) para obter detalhes.
* Você é cobrado somente pelo tempo em que o cluster HDInsight estiver ativo e executando trabalhos.

> [!IMPORTANT]
> Em geral, são necessários **20 minutos** ou mais para provisionar um cluster Azure HDInsight sob demanda.
> 
> 

### <a name="example"></a>Exemplo
O JSON a seguir define um serviço vinculado HDInsight sob demanda baseado em Linux. O serviço Data Factory cria automaticamente um cluster HDInsight **baseado em Linux** ao processar uma fatia de dados. 

```json
{
    "name": "HDInsightOnDemandLinkedService",
    "properties": {
        "type": "HDInsightOnDemand",
        "typeProperties": {
            "version": "3.5",
            "clusterSize": 1,
            "timeToLive": "00:05:00",
            "osType": "Linux",
            "linkedServiceName": "AzureStorageLinkedService"
        }
    }
}
```

Para usar um cluster HDInsight baseado em Windows, defina **osType** como **windows** ou não use a propriedade pois o valor padrão é: windows.  

> [!IMPORTANT]
> O cluster HDInsight cria um **contêiner padrão** no armazenamento de blobs especificado no JSON (**nomeServiçoVinculado**). O HDInsight não exclui esse contêiner quando o cluster é excluído. Este comportamento ocorre por design. Com o serviço vinculado HDInsight sob demanda, um cluster HDInsight é criado sempre que uma fatia precisa ser processada, a menos que haja um cluster ativo existente (**timeToLive**), e é excluído quando o processamento é concluído. 
> 
> Quanto mais fatias forem processadas, você verá muitos contêineres no armazenamento de blobs do Azure. Se você não precisa deles para solução de problemas dos trabalhos, convém excluí-los para reduzir o custo de armazenamento. Os nomes desses contêineres seguem um padrão: `adf**yourdatafactoryname**-**linkedservicename**-datetimestamp`. Use ferramentas como o [Gerenciador de Armazenamento da Microsoft](http://storageexplorer.com/) para excluir contêineres do armazenamento de blobs do Azure.
> 
> 

### <a name="properties"></a>Propriedades
| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| type |A propriedade de tipo deve ser configurada como **HDInsightOnDemand**. |Sim |
| clusterSize |Número de nós de trabalho/dados no cluster. O cluster HDInsight é criado com 2 nós principais juntamente com o número de nós de trabalho que você especifica para esta propriedade. Os nós são do tamanho Standard_D3 que tem 4 núcleos; portanto, um cluster de 4 nós de trabalho usa 24 núcleos (4\*4 = 16 núcleos para nós de trabalho + 2\*4 = 8 núcleos para nós de cabeçalho). Veja [Criar clusters do Hadoop baseados em Linux no HDInsight](../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md) para obter detalhes sobre a camada Standard_D3. |Sim |
| timeToLive |O tempo ocioso permitido para o cluster HDInsight sob demanda. Especifica quanto tempo o cluster HDInsight sob demanda permanece ativo após a conclusão de uma atividade executada se não há nenhum outro trabalho ativo no cluster.<br/><br/>Por exemplo, se uma execução de atividade demora 6 minutos e o timetolive é definido como 5 minutos, o cluster fica ativo durante 5 minutos após a execução de 6 minutos de execução da atividade. Se outra atividade é executada com a janela de 6 minutos, ela é processada pelo mesmo cluster.<br/><br/>A criação de um cluster HDInsight sob demanda é uma operação cara (pode demorar um pouco) e, portanto, use essa configuração conforme o necessário para melhorar o desempenho de um data factory com a reutilização de um cluster HDInsight sob demanda.<br/><br/>Se você definir o valor de timetolive como 0, o cluster é excluído assim que a atividade executada é processada. Por outro lado, se você definir um valor alto, o cluster pode permanecer ocioso desnecessariamente resultando em altos custos. Portanto, é importante que você defina o valor apropriado com base em suas necessidades.<br/><br/>Se o valor da propriedade timetolive foi definido apropriadamente, vários pipelines podem compartilhar a instância do cluster HDInsight sob demanda.  |Sim |
| version |Versão do cluster HDInsight O valor padrão é 3.1 para cluster do Windows e 3.2 para o cluster do Linux. |Não |
| linkedServiceName | Serviço vinculado do Armazenamento do Azure a ser usado pelo cluster sob demanda para armazenar e processar dados. O cluster HDInsight é criado na mesma região que essa conta de Armazenamento do Azure.<p>Atualmente, não é possível criar um cluster HDInsight sob demanda que use um Azure Data Lake Store como o armazenamento. Se você quiser armazenar os dados resultantes do processamento do HDInsight em um Azure Data Lake Store, use uma Atividade de Cópia para copiar os dados do Armazenamento de Blobs do Azure para o Azure Data Lake Store. </p>  | Sim |
| additionalLinkedServiceNames |Especifica as contas de armazenamento adicionais para o serviço vinculado do HDInsight para que o serviço do Data Factory possa registrá-los em seu nome. Essas contas de armazenamento devem estar na mesma região que o cluster HDInsight, que é criado na mesma região que a conta de armazenamento especificada por linkedServiceName. |Não |
| osType |Tipo do sistema operacional. Valores permitidos são: Windows (padrão) e Linux |Não |
| hcatalogLinkedServiceName |O nome do serviço vinculado do SQL Azure que aponta para o banco de dados HCatalog. O cluster HDInsight sob demanda é criado usando o banco de dados SQL do Azure como o metastore. |Não |

#### <a name="additionallinkedservicenames-json-example"></a>Exemplo de JSON additionalLinkedServiceNames

```json
"additionalLinkedServiceNames": [
    "otherLinkedServiceName1",
    "otherLinkedServiceName2"
  ]
```

### <a name="advanced-properties"></a>Propriedades avançadas
Você também pode especificar as seguintes propriedades para a configuração granular do cluster HDInsight sob demanda.

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| coreConfiguration |Especifica os parâmetros de configuração principal (como core-site. xml) para o cluster HDInsight a ser criado. |Não |
| hBaseConfiguration |Especifica os parâmetros de configuração HBase (hbase-site.xml) para o cluster HDInsight. |Não |
| hdfsConfiguration |Especifica os parâmetros de configuração HDFS (hdfs-site.xml) para o cluster HDInsight. |Não |
| hiveConfiguration |Especifica os parâmetros de configuração do hive (hive-site.xml) para o cluster HDInsight. |Não |
| mapReduceConfiguration |Especifica os parâmetros de configuração do MapReduce (mapred-site.xml) para o cluster HDInsight. |Não |
| oozieConfiguration |Especifica os parâmetros de configuração do Oozie (oozie-site.xml) para o cluster HDInsight. |Não |
| stormConfiguration |Especifica os parâmetros de configuração do Storm (storm-site.xml) para o cluster HDInsight. |Não |
| yarnConfiguration |Especifica os parâmetros de configuração do Yarn (yarn-site.xml) para o cluster HDInsight. |Não |

#### <a name="example--on-demand-hdinsight-cluster-configuration-with-advanced-properties"></a>Exemplo – configuração de cluster HDInsight sob demanda com propriedades avançadas

```json
{
  "name": " HDInsightOnDemandLinkedService",
  "properties": {
    "type": "HDInsightOnDemand",
    "typeProperties": {
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
Você pode especificar os tamanhos de nós de dados, principais e zookeeper usando as seguintes propriedades: 

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| headNodeSize |Especificar o tamanho do nó principal O valor padrão é: Standard_D3. Veja a seção **Especificando tamanhos dos nós** para obter detalhes. |Não |
| dataNodeSize |Especifica o tamanho do nó principal O valor padrão é: Standard_D3. |Não |
| zookeeperNodeSize |Especifica o tamanho do nó Zoo Keeper. O valor padrão é: Standard_D3. |Não |

#### <a name="specifying-node-sizes"></a>Especificar tamanhos de nós
Veja o artigo [Tamanhos de máquinas virtuais](../../virtual-machines/linux/sizes.md) para obter valores de cadeia de caracteres que você precisa especificar para as propriedades mencionadas na seção anterior. Os valores precisam estar em conformidade com os **CMDLETs e as APIS** mencionadas no artigo. Como você pode ver neste artigo, o nó de dados de tamanho Grande (padrão) tem 7 GB de memória, que pode não ser suficiente para seu cenário. 

Se quiser criar nós de cabeçalho e nós de trabalho em tamanho D4, especifique **Standard_D4** como o valor das propriedades headNodeSize e dataNodeSize. 

```json
"headNodeSize": "Standard_D4",    
"dataNodeSize": "Standard_D4",
```

Se especificar um valor incorreto para essas propriedades, você pode receber o seguinte **erro:** falha ao criar o cluster. Exceção: Não foi possível concluir operação de criação do cluster. Falha na operação com o código '400'. Cluster deixou para trás estado: ‘Erro’. Mensagem: “PreClusterCreationValidationFailure”. Quando receber esse erro, verifique se está usando o nome **CMDLET e APIS** da tabela no artigo [Tamanhos de Máquinas Virtuais](../../virtual-machines/linux/sizes.md).  

## <a name="bring-your-own-compute-environment"></a>Traga seu próprio ambiente de computação
Nesse tipo de configuração, os usuários podem registrar um ambiente de computação já existente como um serviço vinculado no Data Factory. O ambiente de computação é gerenciado pelo usuário e o serviço Data Factory o utiliza para executar as atividades.

Esse tipo de configuração tem suporte para os ambientes de computação a seguir:

* Azure HDInsight
* Lote do Azure
* Machine Learning do Azure
* Análise Azure Data Lake
* Banco de Dados SQL do Azure, SQL DW do Azure, SQL Server

## <a name="azure-hdinsight-linked-service"></a>Serviço vinculado do Azure HDInsight
Você pode criar um serviço vinculado Azure HDInsight para registrar seu próprio cluster HDInsight com o Data Factory.

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

### <a name="properties"></a>Propriedades
| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| type |A propriedade de tipo deve ser configurada como **HDInsight**. |Sim |
| clusterUri |A URI do cluster HDInsight. |Sim |
| Nome de Usuário |Especifique o nome do usuário a ser usado para se conectar a um cluster HDInsight existente. |Sim |
| Senha |Especifique a senha para a conta de usuário. |Sim |
| linkedServiceName | Nome do serviço vinculado do Armazenamento do Azure que faz referência ao Armazenamento de Blobs usado pelo cluster HDInsight. <p>No momento, você não pode especificar um serviço vinculado do Azure Data Lake Store para essa propriedade. Você pode acessar dados no Azure Data Lake Store usando scripts Hive/Pig se o cluster HDInsight tiver acesso ao Data Lake Store. </p>  |Sim |

## <a name="azure-batch-linked-service"></a>Serviço vinculado de Lote do Azure
Você pode criar um serviço vinculado de Lote do Azure para registrar um pool de lote de máquinas virtuais (VMs) para uma fábrica de dados. Você pode executar atividades personalizadas do .NET usando o Lote do Azure ou o Azure HDInsight.

Veja os tópicos a seguir se você for novo no serviço de Lote do Azure:

* [Noções básicas do Lote do Azure](../../batch/batch-technical-overview.md) para obter uma visão geral do serviço de Lote do Azure.
* cmdlet [New-AzureBatchAccount](https://msdn.microsoft.com/library/mt125880.aspx) para criar uma conta do Lote do Azure (ou) o [portal do Azure](../../batch/batch-account-create-portal.md) para criar a conta do Lote do Azure usando o portal do Azure. Consulte o tópico [Usando o PowerShell para gerenciar a conta do Lote do Azure](http://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx) para obter instruções detalhadas sobre como usar este cmdlet.
* [New-AzureBatchPool](https://msdn.microsoft.com/library/mt125936.aspx) para criar um pool do Lote do Azure.

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

Acrescente “**.\<region name\>**” ao nome de sua conta do Lote para a propriedade **accountName**. Exemplo:

```json
"accountName": "mybatchaccount.eastus"
```

Outra opção é fornecer o ponto de extremidade batchUri, conforme mostrado no exemplo a seguir:

```json
"accountName": "adfteam",
"batchUri": "https://eastus.batch.azure.com",
```

### <a name="properties"></a>Propriedades
| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| type |A propriedade de tipo deve ser definida como **AzureBatch**. |Sim |
| accountName |Nome da conta do Lote do Azure. |Sim |
| accessKey |Tecla de acesso para a conta do Lote do Azure. |Sim |
| poolName |Nome do pool de máquinas virtuais. |Sim |
| linkedServiceName |Nome do serviço vinculado do Armazenamento do Azure associado ao serviço vinculado de Lote do Azure. Esse serviço vinculado é usado para arquivos de teste necessários para executar a atividade e armazenar os logs de execução da atividade. |Sim |

## <a name="azure-machine-learning-linked-service"></a>Serviço Vinculado de Machine Learning do Azure
Criar um serviço vinculado de Machine Learning do Azure para registrar um ponto de extremidade de pontuação do lote de Machine Learning a uma fábrica de dados.

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

### <a name="properties"></a>Propriedades
| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| type |A propriedade de tipo deve ser configurada como **AzureML**. |Sim |
| mlEndpoint |A URL de pontuação do lote. |Sim |
| apiKey |A API do modelo de espaço de trabalho publicada. |Sim |

## <a name="azure-data-lake-analytics-linked-service"></a>Serviço Vinculado da Análise Azure Data Lake
Você cria um serviço vinculado do **Azure Data Lake Analytics** para vincular um serviço de computação do Azure Data Lake Analytics a um Azure Data Factory. A atividade de U-SQL do Data Lake Analytics no pipeline se refere a esse serviço vinculado. 

A tabela a seguir apresenta as descrições das propriedades genéricas usadas na definição JSON. Você ainda pode escolher entre a autenticação de credencial do usuário e entidade de serviço.

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| **tipo** |A propriedade de tipo deve ser definida como: **AzureDataLakeAnalytics**. |Sim |
| **accountName** |Nome da conta da Análise Azure Data Lake. |Sim |
| **dataLakeAnalyticsUri** |URI da Análise Azure Data Lake. |Não |
| **subscriptionId** |Id de assinatura do Azure |Não (se não for especificado, a assinatura do Data Factory é usada). |
| **resourceGroupName** |Nome do grupo de recursos do Azure |Não (se não for especificado, o grupo de recursos do Data Factory é usado). |

### <a name="service-principal-authentication-recommended"></a>Autenticação de entidade de serviço (recomendada)
Para usar a autenticação de entidade de serviço, registre uma entidade de aplicativo no Azure AD (Azure Active Directory) e conceda a ela o acesso ao Data Lake Store. Para encontrar as etapas detalhadas, consulte [Autenticação de serviço a serviço](../../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Anote os seguintes valores, que são usados para definir o serviço vinculado:
* ID do aplicativo
* Chave do aplicativo 
* ID do locatário

Use a autenticação de entidade de serviço especificando as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| **servicePrincipalId** | Especifique a ID do cliente do aplicativo. | Sim |
| **servicePrincipalKey** | Especifique a chave do aplicativo. | Sim |
| **tenant** | Especifique as informações de locatário (domínio nome ou ID do Locatário) em que o aplicativo reside. É possível recuperá-las focalizando o mouse no canto superior direito do Portal do Azure. | Sim |

**Exemplo: autenticação de entidade de serviço**
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

### <a name="user-credential-authentication"></a>Autenticação de credenciais de usuário
Como alternativa, você pode usar a autenticação de credenciais do usuário do Data Lake Analytics especificando as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| **authorization** | Clique no botão **Autorizar** no Editor do Data Factory e insira as suas credenciais, que atribuem a URL de autorização gerada automaticamente a essa propriedade. | Sim |
| **sessionId** | A ID de sessão OAuth da sessão de autorização OAuth. Cada ID da sessão é exclusiva e pode ser usada somente uma vez. Essa configuração é gerada automaticamente quando você usa o Editor do Data Factory. | Sim |

**Exemplo: autenticação de credenciais do usuário**
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
O código de autorização gerado usando o botão **Autorizar** expira após algum tempo. Confira a tabela a seguir para ver os tempos de expiração para os diferentes tipos de contas de usuário. Talvez você veja a mensagem de erro a seguir quando o **token de autenticação expirar**: Erro de operação de credencial: invalid_grant - AADSTS70002: erro ao validar as credenciais. AADSTS70008: a concessão de acesso fornecida expirou ou foi revogada. ID do Rastreamento: d18629e8-af88-43c5-88e3-d8419eb1fca1 ID da Correlação: fac30a0c-6be6-4e02-8d69-a776d2ffefd7 Carimbo de data/hora: 2015-12-15 21:09:31Z

| Tipo de usuário | Expira após |
|:--- |:--- |
| Contas de usuário NÃO gerenciadas pelo Azure Active Directory (@hotmail.com, @live.com etc.) |12 horas |
| Contas de usuários gerenciadas pelo AAD (Azure Active Directory) |14 dias após a última execução da fatia. <br/><br/>90 dias, se uma fatia com base em serviços vinculados do OAuth for executada pelo menos uma vez a cada 14 dias. |

Para evitar/resolver este erro, reautorize usando o botão **Autorizar** quando o **token expirar** e reimplante o serviço vinculado. Você também pode gerar valores para as propriedades **sessionId** e **authorization** de modo programático usando o código da seguinte maneira:

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

Veja os tópicos [Classe AzureDataLakeStoreLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx), [Classe AzureDataLakeAnalyticsLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx) e [Classe AuthorizationSessionGetResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx) para obter detalhes sobre as classes do Data Factory usadas no código. Adicione uma referência a: Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll para a classe WindowsFormsWebAuthenticationDialog. 

## <a name="azure-sql-linked-service"></a>Serviço Vinculado do SQL do Azure
Você pode criar um serviço vinculado SQL do Azure e usá-lo com a [Atividade de Procedimento Armazenado](data-factory-stored-proc-activity.md) para invocar um procedimento armazenado de um pipeline do Data Factory. Confira o artigo [Conector SQL do Azure](data-factory-azure-sql-connector.md#linked-service-properties) para saber mais sobre esse serviço vinculado.

## <a name="azure-sql-data-warehouse-linked-service"></a>Serviço vinculado do SQL Data Warehouse do Azure
Você pode criar um serviço vinculado do SQL Data Warehouse do Azure e usá-lo com a [Atividade de Procedimento Armazenado](data-factory-stored-proc-activity.md) para invocar um procedimento armazenado de um pipeline do Data Factory. Confira o artigo [Conector SQL Data Warehouse do Azure](data-factory-azure-sql-data-warehouse-connector.md#linked-service-properties) para saber mais sobre esse serviço vinculado.

## <a name="sql-server-linked-service"></a>Serviço vinculado do SQL Server
Você pode criar um serviço vinculado do SQL Server e usá-lo com a [Atividade de Procedimento Armazenado](data-factory-stored-proc-activity.md) para invocar um procedimento armazenado de um pipeline do Data Factory. Confira o artigo [Conector SQL Server](data-factory-sqlserver-connector.md#linked-service-properties) para saber mais sobre esse serviço vinculado.

