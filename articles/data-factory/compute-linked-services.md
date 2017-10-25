---
title: "Ambientes de computação com suporte do Azure Data Factory | Microsoft Docs"
description: "Saiba mais sobre os ambientes de computação que você pode usar em pipelines do Azure Data Factory (por exemplo, o Azure HDInsight) para transformar ou processar dados."
services: data-factory
documentationcenter: 
author: shengcmsft
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/10/2017
ms.author: shengc
ms.openlocfilehash: 07d702e34e1574161a64af9a4724a66879a0ba05
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="compute-environments-supported-by-azure-data-factory"></a>Ambientes de computação com suporte do Azure Data Factory
Este artigo explica diferentes ambientes de computação que você pode usar para processar ou transformar dados. Ele também fornece detalhes sobre as diferentes configurações (sob demanda versus traga a sua própria) com suporte pela Data Factory ao configurar serviços vinculados que vinculam esses ambientes de computação para uma Azure Data Factory.

A tabela a seguir fornece uma lista dos ambientes de computação com suporte do Data Factory e as atividades que podem ser executadas neles. 

| Ambiente de computação                      | atividades                               |
| ---------------------------------------- | ---------------------------------------- |
| [Cluster HDInsight sob demanda](#azure-hdinsight-on-demand-linked-service) ou [seu próprio cluster HDInsight](#azure-hdinsight-linked-service) | [Hive](transform-data-using-hadoop-hive.md), [Pig](transform-data-using-hadoop-pig.md), [Spark](transform-data-using-spark.md), [MapReduce](transform-data-using-hadoop-map-reduce.md), [Hadoop Streaming](transform-data-using-hadoop-streaming.md) |
| [Lote do Azure](#azure-batch-linked-service) | [Personalizado](transform-data-using-dotnet-custom-activity.md) |
| [Azure Machine Learning](#azure-machine-learning-linked-service) | [Atividades de Machine Learning: execução do Lote e recurso de atualização](transform-data-using-machine-learning.md) |
| [Análise Azure Data Lake](#azure-data-lake-analytics-linked-service) | [U-SQL da Análise Data Lake](transform-data-using-data-lake-analytics.md) |
| [Azure SQL](#azure-sql-linked-service), [Azure SQL Data Warehouse](#azure-sql-data-warehouse-linked-service), [SQL Server](#sql-server-linked-service) | [Procedimento armazenado](transform-data-using-stored-procedure.md) |

>  

## <a name="on-demand-compute-environment"></a>Ambiente de computação sob demanda
Nesse tipo de configuração, o ambiente de computação é totalmente gerenciado pelo serviço do Azure Data Factory. Ele é automaticamente criado pelo serviço Data Factory antes de um trabalho ser enviado a fim de processar os dados e é removido após a conclusão do trabalho. Você pode criar um serviço vinculado para o ambiente de computação sob demanda, configurá-lo e controlar as configurações granulares da execução do trabalho, gerenciamento de cluster e ações de inicialização.

> [!NOTE]
> A configuração sob demanda tem suporte somente para clusters do Azure HDInsight.
>
> 

## <a name="azure-hdinsight-on-demand-linked-service"></a>Serviço vinculado do Azure HDInsight sob demanda
O serviço Azure Data Factory pode criar automaticamente um cluster HDInsight sob demanda para processar dados. O cluster é criado na mesma região que a conta de armazenamento (propriedade linkedServiceName em JSON) associada ao cluster. A conta de armazenamento deve ser uma conta de armazenamento do Azure padrão de uso geral. 

Observe os seguintes pontos **importantes** sobre o serviço vinculado HDInsight sob demanda:

* O cluster HDInsight sob demanda é criado sob sua assinatura do Azure. Quando o cluster estiver ativo e em execução, você poderá vê-lo em seu Portal do Azure. 
* Os logs para trabalhos que são executados em um cluster HDInsight sob demanda são copiados para a conta de armazenamento associada ao cluster HDInsight. clusterUserName, clusterPassword, clusterSshUserName, clusterSshPassword determinados em sua definição de serviço vinculado são usados para fazer logon no cluster para solução de problemas detalhada durante o ciclo de vida do cluster. 
* Você é cobrado somente pelo tempo em que o cluster HDInsight estiver ativo e executando trabalhos.

> [!IMPORTANT]
> Em geral, são necessários **20 minutos** ou mais para provisionar um cluster Azure HDInsight sob demanda.
>
> 

### <a name="example"></a>Exemplo
O JSON a seguir define um serviço vinculado HDInsight sob demanda baseado em Linux. O serviço Data Factory cria automaticamente um cluster HDInsight **baseado em Linux** para processar a atividade solicitada. 

```json
{
  "name": "HDInsightOnDemandLinkedService",
  "properties": {
    "type": "HDInsightOnDemand",
    "typeProperties": {
      "clusterType": "hadoop",
      "clusterSize": 1,
      "timeToLive": "00:15:00",
      "hostSubscriptionId": "<subscription ID>",
      "servicePrincipalId": "<service principal ID>",
      "servicePrincipalKey": {
        "value": "<service principal key>",
        "type": "SecureString"
      },
      "tenant": "<tenent id>",
      "clusterResourceGroup": "<resource group name>",
      "version": "3.6",
      "osType": "Linux",
      "linkedServiceName": {
        "referenceName": "AzureStorageLinkedService",
        "type": "LinkedServiceReference"
      }
    },
    "connectVia": {
      "referenceName": "<name of Integration Runtime>",
      "type": "IntegrationRuntimeReference"
    }
  }
}
```

> [!IMPORTANT]
> O cluster HDInsight cria um **contêiner padrão** no armazenamento de blobs especificado no JSON (**nomeServiçoVinculado**). O HDInsight não exclui esse contêiner quando o cluster é excluído. Este comportamento ocorre por design. Com o serviço vinculado HDInsight sob demanda, um cluster HDInsight é criado sempre que uma fatia precisa ser processada, a menos que haja um cluster ativo existente (**timeToLive**), e é excluído quando o processamento é concluído. 
>
> Conforme mais atividades forem executadas, você verá muitos contêineres no Armazenamento de Blobs do Azure. Se você não precisa deles para solução de problemas dos trabalhos, convém excluí-los para reduzir o custo de armazenamento. Os nomes desses contêineres seguem um padrão: `adf**yourdatafactoryname**-**linkedservicename**-datetimestamp`. Use ferramentas como o [Gerenciador de Armazenamento da Microsoft](http://storageexplorer.com/) para excluir contêineres do armazenamento de blobs do Azure.
>
> 

### <a name="properties"></a>Propriedades
| Propriedade                     | Descrição                              | Obrigatório |
| ---------------------------- | ---------------------------------------- | -------- |
| type                         | A propriedade de tipo deve ser configurada como **HDInsightOnDemand**. | Sim      |
| clusterType                  | O tipo do cluster HDInsight a ser criado. Os valores permitidos são "hadoop" e "spark". Se não for especificado, o valor padrão será hadoop. | Não       |
| clusterSize                  | Número de nós de trabalho/dados no cluster. O cluster HDInsight é criado com 2 nós principais juntamente com o número de nós de trabalho que você especifica para esta propriedade. Os nós são do tamanho Standard_D3 que tem 4 núcleos; portanto, um cluster de 4 nós de trabalho usa 24 núcleos (4\*4 = 16 núcleos para nós de trabalho + 2\*4 = 8 núcleos para nós de cabeçalho). Consulte [Configurar clusters no HDInsight com Hadoop, Spark, Kafka e mais](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md) para obter detalhes. | Sim      |
| timeToLive                   | O tempo ocioso permitido para o cluster HDInsight sob demanda. Especifica quanto tempo o cluster HDInsight sob demanda permanece ativo após a conclusão de uma atividade executada se não há nenhum outro trabalho ativo no cluster. O mínimo valor permitido é de 5 minutos (00:05:00).<br/><br/>Por exemplo, se uma execução de atividade demora 6 minutos e o timetolive é definido como 5 minutos, o cluster fica ativo durante 5 minutos após a execução de 6 minutos de execução da atividade. Se outra atividade é executada com a janela de 6 minutos, ela é processada pelo mesmo cluster.<br/><br/>A criação de um cluster HDInsight sob demanda é uma operação cara (pode demorar um pouco) e, portanto, use essa configuração conforme o necessário para melhorar o desempenho de um data factory com a reutilização de um cluster HDInsight sob demanda.<br/><br/>Se você definir o valor de timetolive como 0, o cluster é excluído assim que a atividade executada é processada. Enquanto que, se você definir um valor alto, o cluster poderá permanecer ocioso para que você faça logon a fim de realizar alguma solução de problemas, mas isso poderá resultar em altos custos. Portanto, é importante que você defina o valor apropriado com base em suas necessidades.<br/><br/>Se o valor da propriedade timetolive foi definido apropriadamente, vários pipelines podem compartilhar a instância do cluster HDInsight sob demanda. | Sim      |
| version                      | Versão do cluster HDInsight Se não for especificada, será usada a versão padrão atual definida do HDInsight. | Não       |
| linkedServiceName            | Serviço vinculado do Armazenamento do Azure a ser usado pelo cluster sob demanda para armazenar e processar dados. O cluster HDInsight é criado na mesma região que essa conta de Armazenamento do Azure. O Azure HDInsight tem uma limitação para o número total de núcleos que você pode usar em cada região do Azure a que ele dá suporte. Verifique se que você tem cotas de núcleo suficientes nessa região do Azure a fim de atender o clusterSize necessário. Para obter detalhes, consulte [Configurar clusters no HDInsight com Hadoop, Spark, Kafka e mais](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md)<p>Atualmente, não é possível criar um cluster HDInsight sob demanda que use um Azure Data Lake Store como o armazenamento. Se você quiser armazenar os dados resultantes do processamento do HDInsight em um Azure Data Lake Store, use uma Atividade de Cópia para copiar os dados do Armazenamento de Blobs do Azure para o Azure Data Lake Store. </p> | Sim      |
| hostSubscriptionId           | A ID de assinatura do Azure usada para criar o cluster HDInsight. Se não for especificada, será usada a ID da assinatura de seu contexto de logon do Azure. | Não       |
| clusterResourceGroup         | O cluster HDInsight é criado nesse grupo de recursos. | Sim      |
| sparkVersion                 | A versão do Spark se o tipo de cluster for "Spark" | Não       |
| additionalLinkedServiceNames | Especifica as contas de armazenamento adicionais para o serviço vinculado do HDInsight para que o serviço do Data Factory possa registrá-los em seu nome. Essas contas de armazenamento devem estar na mesma região que o cluster HDInsight, que é criado na mesma região que a conta de armazenamento especificada por linkedServiceName. | Não       |
| osType                       | Tipo do sistema operacional. Os valores permitidos são: Linux e Windows (somente para HDInsight 3.3). O padrão é Linux. | Não       |
| hcatalogLinkedServiceName    | O nome do serviço vinculado do SQL Azure que aponta para o banco de dados HCatalog. O cluster HDInsight sob demanda é criado usando o banco de dados SQL do Azure como o metastore. | Não       |
| connectVia                   | O Integration Runtime a ser usado para distribuir as atividades a esse serviço vinculado do HDInsight. Em relação ao serviço vinculado do HDInsight sob demanda, há suporte apenas para o Integration Runtime do Azure. Se não for especificado, ele usa o Integration Runtime padrão do Azure. | Não       |

> [!IMPORTANT]
> O HDInsight dá suporte a várias versões do cluster Hadoop que podem ser implantadas. Cada opção de versão cria uma versão específica da distribuição de HDP (Hortonworks Data Platform) e um conjunto de componentes contidos nessa distribuição. A lista de versões com suporte do HDInsight continua sendo atualizada para fornecer as correções e os componentes mais recentes do ecossistema do Hadoop. Verifique sempre as informações mais recentes de [Versão e tipo de sistema operacional do HDInsight com suporte](../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions) a fim de garantir que você use uma versão do HDInsight com suporte. 
>
> 

#### <a name="additionallinkedservicenames-json-example"></a>Exemplo de JSON additionalLinkedServiceNames

```json
"additionalLinkedServiceNames": [
    "otherLinkedServiceName1": {
        "referenceName": "AzureStorageLinkedService",
        "type": "LinkedServiceReference"
    },
    "otherLinkedServiceName2": {
        "referenceName": "AzureStorageLinkedService",
        "type": "LinkedServiceReference"
    }
]
```

### <a name="service-principal-authentication"></a>Autenticação de entidade de serviço

O serviço vinculado do HDInsight sob demanda exige uma autenticação de entidade de serviço para criar clusters HDInsight em seu nome. Para usar a autenticação de entidade de serviço, registre uma entidade de aplicativo no Azure AD (Azure Active Directory) e a atribua à função **Colaborador** da assinatura ou do grupo de recursos no qual o cluster HDInsight será criado. Para conhecer etapas detalhadas, consulte [Usar o portal para criar um aplicativo e uma entidade de serviço do Azure Active Directory que possa acessar recursos](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-create-service-principal-portal). Anote os seguintes valores, que são usados para definir o serviço vinculado:

- ID do aplicativo
- Chave do aplicativo 
- ID do locatário

Use a autenticação de entidade de serviço especificando as seguintes propriedades:

| Propriedade                | Descrição                              | Obrigatório |
| :---------------------- | :--------------------------------------- | :------- |
| **servicePrincipalId**  | Especifique a ID do cliente do aplicativo.     | Sim      |
| **servicePrincipalKey** | Especifique a chave do aplicativo.           | Sim      |
| **tenant**              | Especifique as informações de locatário (domínio nome ou ID do Locatário) em que o aplicativo reside. É possível recuperá-las focalizando o mouse no canto superior direito do Portal do Azure. | Sim      |

### <a name="advanced-properties"></a>Propriedades avançadas

Você também pode especificar as seguintes propriedades para a configuração granular do cluster HDInsight sob demanda.

| Propriedade               | Descrição                              | Obrigatório |
| :--------------------- | :--------------------------------------- | :------- |
| coreConfiguration      | Especifica os parâmetros de configuração principal (como core-site. xml) para o cluster HDInsight a ser criado. | Não       |
| hBaseConfiguration     | Especifica os parâmetros de configuração HBase (hbase-site.xml) para o cluster HDInsight. | Não       |
| hdfsConfiguration      | Especifica os parâmetros de configuração HDFS (hdfs-site.xml) para o cluster HDInsight. | Não       |
| hiveConfiguration      | Especifica os parâmetros de configuração do hive (hive-site.xml) para o cluster HDInsight. | Não       |
| mapReduceConfiguration | Especifica os parâmetros de configuração do MapReduce (mapred-site.xml) para o cluster HDInsight. | Não       |
| oozieConfiguration     | Especifica os parâmetros de configuração do Oozie (oozie-site.xml) para o cluster HDInsight. | Não       |
| stormConfiguration     | Especifica os parâmetros de configuração do Storm (storm-site.xml) para o cluster HDInsight. | Não       |
| yarnConfiguration      | Especifica os parâmetros de configuração do Yarn (yarn-site.xml) para o cluster HDInsight. | Não       |

#### <a name="example--on-demand-hdinsight-cluster-configuration-with-advanced-properties"></a>Exemplo – configuração de cluster HDInsight sob demanda com propriedades avançadas

```json
{
  "name": " HDInsightOnDemandLinkedService",
  "properties": {
    "type": "HDInsightOnDemand",
    "typeProperties": {
        "clusterSize": 16,
        "timeToLive": "01:30:00",
        "hostSubscriptionId": "<subscription ID>",
        "servicePrincipalId": "<service principal ID>",
        "servicePrincipalKey": {
          "value": "<service principal key>",
          "type": "SecureString"
        },
        "tenant": "<tenent id>",
        "clusterResourceGroup": "<resource group name>",
        "version": "3.6",
        "osType": "Linux",
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
          },
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
              "otherLinkedServiceName1": {
                  "referenceName": "datafeeds1",
                  "type": "LinkedServiceReference"
              },
              "otherLinkedServiceName2": {
                  "referenceName": "datafeeds2",
                  "type": "LinkedServiceReference"
              }
          ]}
  },
    "connectVia": {
    "referenceName": "<name of Integration Runtime>",
    "type": "IntegrationRuntimeReference"
  }
}
```

### <a name="node-sizes"></a>Tamanhos dos nós
Você pode especificar os tamanhos de nós de dados, principais e zookeeper usando as seguintes propriedades: 

| Propriedade          | Descrição                              | Obrigatório |
| :---------------- | :--------------------------------------- | :------- |
| headNodeSize      | Especificar o tamanho do nó principal O valor padrão é: Standard_D3. Veja a seção **Especificando tamanhos dos nós** para obter detalhes. | Não       |
| dataNodeSize      | Especifica o tamanho do nó principal O valor padrão é: Standard_D3. | Não       |
| zookeeperNodeSize | Especifica o tamanho do nó Zoo Keeper. O valor padrão é: Standard_D3. | Não       |

#### <a name="specifying-node-sizes"></a>Especificar tamanhos de nós
Veja o artigo [Tamanhos de máquinas virtuais](../virtual-machines/linux/sizes.md) para obter valores de cadeia de caracteres que você precisa especificar para as propriedades mencionadas na seção anterior. Os valores precisam estar em conformidade com os **CMDLETs e as APIS** mencionadas no artigo. Como você pode ver neste artigo, o nó de dados de tamanho Grande (padrão) tem 7 GB de memória, que pode não ser suficiente para seu cenário. 

Se quiser criar nós de cabeçalho e nós de trabalho em tamanho D4, especifique **Standard_D4** como o valor das propriedades headNodeSize e dataNodeSize. 

```json
"headNodeSize": "Standard_D4",    
"dataNodeSize": "Standard_D4",
```

Se especificar um valor incorreto para essas propriedades, você pode receber o seguinte **erro:** falha ao criar o cluster. Exceção: Não foi possível concluir operação de criação do cluster. Falha na operação com o código '400'. Cluster deixou para trás estado: ‘Erro’. Mensagem: “PreClusterCreationValidationFailure”. Quando receber esse erro, verifique se está usando o nome **CMDLET e APIS** da tabela no artigo [Tamanhos de Máquinas Virtuais](../virtual-machines/linux/sizes.md).        

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
        "userName": "username",
        "password": {
            "value": "passwordvalue",
            "type": "SecureString"
          },
        "linkedServiceName": {
              "referenceName": "AzureStorageLinkedService",
              "type": "LinkedServiceReference"
        }
      },
      "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
      }
    }
  }
```

### <a name="properties"></a>Propriedades
| Propriedade          | Descrição                              | Obrigatório |
| ----------------- | ---------------------------------------- | -------- |
| type              | A propriedade de tipo deve ser configurada como **HDInsight**. | Sim      |
| clusterUri        | A URI do cluster HDInsight.        | Sim      |
| Nome de Usuário          | Especifique o nome do usuário a ser usado para se conectar a um cluster HDInsight existente. | Sim      |
| Senha          | Especifique a senha para a conta de usuário.   | Sim      |
| linkedServiceName | Nome do serviço vinculado do Armazenamento do Azure que faz referência ao Armazenamento de Blobs usado pelo cluster HDInsight. <p>No momento, você não pode especificar um serviço vinculado do Azure Data Lake Store para essa propriedade. Você pode acessar dados no Azure Data Lake Store usando scripts Hive/Pig se o cluster HDInsight tiver acesso ao Data Lake Store. </p> | Sim      |
| connectVia        | O Integration Runtime a ser usado para distribuir as atividades a esse serviço vinculado. Você pode usar o Integration Runtime do Azure ou Integration Runtime auto-hospedado. Se não for especificado, ele usa o Integration Runtime padrão do Azure. | Não       |

> [!IMPORTANT]
> O HDInsight dá suporte a várias versões do cluster Hadoop que podem ser implantadas. Cada opção de versão cria uma versão específica da distribuição de HDP (Hortonworks Data Platform) e um conjunto de componentes contidos nessa distribuição. A lista de versões com suporte do HDInsight continua sendo atualizada para fornecer as correções e os componentes mais recentes do ecossistema do Hadoop. Verifique sempre as informações mais recentes de [Versão e tipo de sistema operacional do HDInsight com suporte](../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions) a fim de garantir que você use uma versão do HDInsight com suporte. 
>

## <a name="azure-batch-linked-service"></a>Serviço vinculado do Lote do Azure

Você pode criar um serviço vinculado de Lote do Azure para registrar um pool de lote de máquinas virtuais (VMs) para uma fábrica de dados. Você pode executar a atividade personalizada usando o Lote do Azure.

Veja os tópicos a seguir se você for novo no serviço de Lote do Azure:

* [Noções básicas do Lote do Azure](../batch/batch-technical-overview.md) para obter uma visão geral do serviço de Lote do Azure.
* Cmdlet [New-AzureRmBatchAccount](/powershell/module/azurerm.batch/New-AzureRmBatchAccount?view=azurermps-4.3.1) para criar uma conta do Lote do Azure (ou) o [Portal do Azure](../batch/batch-account-create-portal.md) para criar a conta do Lote do Azure usando o Portal do Azure. Consulte o tópico [Usando o PowerShell para gerenciar a conta do Lote do Azure](http://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx) para obter instruções detalhadas sobre como usar este cmdlet.
* [New-AzureBatchPool](/powershell/module/azurerm.batch/New-AzureBatchPool?view=azurermps-4.3.1) para criar um pool do Lote do Azure.

### <a name="example"></a>Exemplo

```json
{
    "name": "AzureBatchLinkedService",
    "properties": {
      "type": "AzureBatch",
      "typeProperties": {
        "accountName": "batchaccount",
        "accessKey": {
          "type": "SecureString",
          "value": "access key"
        },
        "batchUri": "https://batchaccount.region.batch.azure.com",
        "poolName": "poolname",
        "linkedServiceName": {
          "referenceName": "StorageLinkedService",
          "type": "LinkedServiceReference"
        }
      },
      "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
      }
    }
  }
```


### <a name="properties"></a>Propriedades
| Propriedade          | Descrição                              | Obrigatório |
| ----------------- | ---------------------------------------- | -------- |
| type              | A propriedade de tipo deve ser definida como **AzureBatch**. | Sim      |
| accountName       | Nome da conta do Lote do Azure.         | Sim      |
| accessKey         | Tecla de acesso para a conta do Lote do Azure.  | Sim      |
| batchUri          | URL de sua conta do Lote do Azure, no formato de https://*batchaccountname.region*.batch.azure.com. | Sim      |
| poolName          | Nome do pool de máquinas virtuais.    | Sim      |
| linkedServiceName | Nome do serviço vinculado do Armazenamento do Azure associado ao serviço vinculado de Lote do Azure. Esse serviço vinculado é usado para preparar os arquivos necessários para executar a atividade. | Sim      |
| connectVia        | O Integration Runtime a ser usado para distribuir as atividades a esse serviço vinculado. Você pode usar o Integration Runtime do Azure ou Integration Runtime auto-hospedado. Se não for especificado, ele usa o Integration Runtime padrão do Azure. | Não       |

## <a name="azure-machine-learning-linked-service"></a>Serviço vinculado do Azure Machine Learning
Criar um serviço vinculado de Machine Learning do Azure para registrar um ponto de extremidade de pontuação do lote de Machine Learning a uma fábrica de dados.

### <a name="example"></a>Exemplo

```json
{
    "name": "AzureMLLinkedService",
    "properties": {
      "type": "AzureML",
      "typeProperties": {
        "mlEndpoint": "https://[batch scoring endpoint]/jobs",
        "apiKey": {
            "type": "SecureString",
            "value": "access key"
        }
     },
     "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
      }
    }
}
```

### <a name="properties"></a>Propriedades
| Propriedade               | Descrição                              | Obrigatório                                 |
| ---------------------- | ---------------------------------------- | ---------------------------------------- |
| type                   | A propriedade de tipo deve ser configurada como **AzureML**. | Sim                                      |
| mlEndpoint             | A URL de pontuação do lote.                   | Sim                                      |
| apiKey                 | A API do modelo de espaço de trabalho publicada.     | Sim                                      |
| updateResourceEndpoint | A URL do recurso de atualização para um ponto de extremidade de serviço Web do Azure ML usado para atualizar o serviço Web preditivo com o arquivo de modelo treinado | Não                                       |
| servicePrincipalId     | Especifique a ID do cliente do aplicativo.     | Necessária se a updateResourceEndpoint for especificada |
| servicePrincipalKey    | Especifique a chave do aplicativo.           | Necessária se a updateResourceEndpoint for especificada |
| locatário                 | Especifique as informações de locatário (domínio nome ou ID do Locatário) em que o aplicativo reside. É possível recuperá-las focalizando o mouse no canto superior direito do Portal do Azure. | Necessária se a updateResourceEndpoint for especificada |
| connectVia             | O Integration Runtime a ser usado para distribuir as atividades a esse serviço vinculado. Você pode usar o Integration Runtime do Azure ou Integration Runtime auto-hospedado. Se não for especificado, ele usa o Integration Runtime padrão do Azure. | Não                                       |

## <a name="azure-data-lake-analytics-linked-service"></a>Serviço vinculado do Azure Data Lake Analytics
Você cria um serviço vinculado do **Azure Data Lake Analytics** para vincular um serviço de computação do Azure Data Lake Analytics a um Azure Data Factory. A atividade de U-SQL do Data Lake Analytics no pipeline se refere a esse serviço vinculado. 

### <a name="example"></a>Exemplo

```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "adftestaccount",
            "dataLakeAnalyticsUri": "azuredatalakeanalytics URI",
            "servicePrincipalId": "service principal id",
            "servicePrincipalKey": {
                "value": "service principal key",
                "type": "SecureString"
            },
            "tenant": "tenant ID",
            "subscriptionId": "<optional, subscription id of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="properties"></a>Propriedades

| Propriedade             | Descrição                              | Obrigatório                                 |
| -------------------- | ---------------------------------------- | ---------------------------------------- |
| type                 | A propriedade de tipo deve ser definida como: **AzureDataLakeAnalytics**. | Sim                                      |
| accountName          | Nome da conta da Análise Azure Data Lake.  | Sim                                      |
| dataLakeAnalyticsUri | URI da Análise Azure Data Lake.           | Não                                       |
| subscriptionId       | Id de assinatura do Azure                    | Não (se não for especificado, a assinatura do Data Factory é usada). |
| resourceGroupName    | Nome do grupo de recursos do Azure                | Não (se não for especificado, o grupo de recursos do Data Factory é usado). |
| servicePrincipalId   | Especifique a ID do cliente do aplicativo.     | Sim                                      |
| servicePrincipalKey  | Especifique a chave do aplicativo.           | Sim                                      |
| locatário               | Especifique as informações de locatário (domínio nome ou ID do Locatário) em que o aplicativo reside. É possível recuperá-las focalizando o mouse no canto superior direito do Portal do Azure. | Sim                                      |
| connectVia           | O Integration Runtime a ser usado para distribuir as atividades a esse serviço vinculado. Você pode usar o Integration Runtime do Azure ou Integration Runtime auto-hospedado. Se não for especificado, ele usa o Integration Runtime padrão do Azure. | Não                                       |



## <a name="azure-sql-database-linked-service"></a>Serviço vinculado para o Banco de Dados SQL do Azure
Você pode criar um serviço vinculado SQL do Azure e usá-lo com a [Atividade de Procedimento Armazenado](transform-data-using-stored-procedure.md) para invocar um procedimento armazenado de um pipeline do Data Factory. Confira o artigo [Conector SQL do Azure](connector-azure-sql-database.md#linked-service-properties) para saber mais sobre esse serviço vinculado.

## <a name="azure-sql-data-warehouse-linked-service"></a>Serviço vinculado do SQL Data Warehouse do Azure
Você pode criar um serviço vinculado do SQL Data Warehouse do Azure e usá-lo com a [Atividade de Procedimento Armazenado](transform-data-using-stored-procedure.md) para invocar um procedimento armazenado de um pipeline do Data Factory. Confira o artigo [Conector SQL Data Warehouse do Azure](connector-azure-sql-data-warehouse.md#linked-service-properties) para saber mais sobre esse serviço vinculado.

## <a name="sql-server-linked-service"></a>Serviço vinculado do SQL Server
Você pode criar um serviço vinculado do SQL Server e usá-lo com a [Atividade de Procedimento Armazenado](transform-data-using-stored-procedure.md) para invocar um procedimento armazenado de um pipeline do Data Factory. Confira o artigo [Conector SQL Server](connector-sql-server.md#linked-service-properties) para saber mais sobre esse serviço vinculado.

## <a name="azure-data-factory---naming-rules"></a>Azure Data Factory - regras de nomenclatura
A tabela a seguir fornece regras de nomenclatura para artefatos Data Factory.

| Nome                             | Exclusividade do nome                          | Verificações de validação                        |
| :------------------------------- | :--------------------------------------- | :--------------------------------------- |
| Data Factory                     | Exclusivo em todo o Microsoft Azure. Os nomes não diferenciam maiúsculas de minúsculas, isto é, `MyDF` e `mydf` referem-se ao mesmo data factory. | <ul><li>Cada data factory está vinculado a exatamente uma assinatura do Azure.</li><li>Os nomes do objeto devem começar com uma letra ou número e podem conter apenas letras, números e o caractere traço (-).</li><li>Cada caractere traço (-) deve ser imediatamente precedido e seguido por uma letra ou um número. Não há permissão para traços consecutivos em nomes de contêiner.</li><li>O nome pode ter de 3 a 63 caracteres.</li></ul> |
| Serviços/tabelas/pipelines vinculados | Exclusivo em um mesmo data factory. Os nomes não diferenciam maiúsculas de minúsculas. | <ul><li>Número máximo de caracteres em um nome de tabela: 260.</li><li>Os nomes de objetos devem começar com uma letra, um número ou um sublinhado (_).</li><li>Os seguintes caracteres não são permitidos: “.”, “+”, “?”, “/”, “<”, ”>”,”*”,”%”,”&”,”:”,”\\”</li></ul> |
| Grupo de recursos                   | Exclusivo em todo o Microsoft Azure. Os nomes não diferenciam maiúsculas de minúsculas. | <ul><li>Número máximo de caracteres: 1000.</li><li>O nome pode conter letras, dígitos e os seguintes caracteres: “-”, “_”, “,” e “.”</li></ul> |

## <a name="next-steps"></a>Próximas etapas
Para obter uma lista das atividades de transformação com suporte do Azure Data Factory, consulte [Transformar dados](transform-data.md).