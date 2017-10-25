---
title: Migrar para as ferramentas do Azure Resource Manager do HDInsight | Microsoft Docs
description: Como migrar para as ferramentas de desenvolvimento do Azure Resource Manager para clusters HDInsight
services: hdinsight
editor: cgronlun
manager: jhubbard
author: nitinme
documentationcenter: 
ms.assetid: 05efedb5-6456-4552-87ff-156d77fbe2e1
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: nitinme
ms.openlocfilehash: 9fec253fbf9136cf86a84a6c7b407ea522996226
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="migrating-to-azure-resource-manager-based-development-tools-for-hdinsight-clusters"></a>Migrando para ferramentas de desenvolvimento baseadas no Azure Resource Manager dos clusters HDInsight

O HDInsight está preterindo as ferramentas baseadas em ASM (Azure Service Manager) para o HDInsight. Se você usa o Azure PowerShell, a CLI do Azure ou o SDK do .NET do HDInsight para trabalhar com clusters HDInsight, nós o incentivamos a usar as versões baseadas em ARM (Azure Resource Manager) do PowerShell, da CLI e do SDK do .NET no futuro. Este artigo fornece sugestões sobre como migrar para a nova abordagem baseada em ARM. Sempre que aplicável, este artigo também destaca as diferenças entre as abordagens do ASM e ARM em relação ao HDInsight.

> [!IMPORTANT]
> O suporte para o PowerShell, a CLI e o SDK do .NET baseado em ASM será descontinuado em **1º de janeiro de 2017**.
> 
> 

## <a name="migrating-azure-cli-to-azure-resource-manager"></a>Migrando a CLI do Azure para o Azure Resource Manager
Agora a CLI do Azure usa como padrão o modo ARM (Azure Resource Manager), a menos que você esteja atualizando uma instalação anterior; nesse caso, talvez seja necessário usar o comando `azure config mode arm` para alternar para o modo ARM.

Os comandos básicos que a CLI do Azure fornecidos para trabalhar com o HDInsight usando o ASM (Gerenciamento de Serviços do Azure) são os mesmos que os usados no ARM; no entanto, alguns parâmetros e opções podem ter novos nomes, e há muitos novos parâmetros disponíveis ao usar o ARM. Por exemplo, agora é possível usar `azure hdinsight cluster create` para especificar a Rede Virtual do Azure na qual um cluster deve ser criado ou usar as informações de metastore do Hive e Oozie.

Os comandos básicos para trabalhar com o HDInsight por meio do Azure Resource Manager são:

* `azure hdinsight cluster create` - cria um novo cluster HDInsight
* `azure hdinsight cluster delete` - exclui um cluster HDInsight existente
* `azure hdinsight cluster show` - exibe informações sobre um cluster existente
* `azure hdinsight cluster list` - lista os clusters HDInsight de sua assinatura do Azure

Use a opção `-h` para inspecionar os parâmetros e as opções disponíveis para cada comando.

### <a name="new-commands"></a>Novos comandos
Os novos comandos disponíveis no Azure Resource Manager são:

* `azure hdinsight cluster resize` - altera dinamicamente o número de nós de trabalho no cluster
* `azure hdinsight cluster enable-http-access` - habilita o acesso HTTPs ao cluster (ativado por padrão)
* `azure hdinsight cluster disable-http-access` - desabilita o acesso HTTPs ao cluster
* `azure hdinsight script-action` - fornece comandos para criar/gerenciar as Ações de Script em um cluster
* `azure hdinsight config` - fornece comandos para criar um arquivo de configuração que pode ser usado com o comando `hdinsight cluster create` para fornecer informações de configuração.

### <a name="deprecated-commands"></a>Comandos preteridos
Se você usar os comandos `azure hdinsight job` para enviar trabalhos ao cluster HDInsight, eles não estarão disponíveis por meio dos comandos ARM. Se você precisar enviar trabalhos ao HDInsight por meio de scripts de forma programática, será necessário usar as APIs REST fornecidas pelo HDInsight. Para obter mais informações sobre como enviar trabalhos usando as APIs REST, confira os seguintes documentos.

* [Executar trabalhos do MapReduce com o Hadoop no HDInsight usando a cURL](hdinsight-hadoop-use-mapreduce-curl.md)
* [Executar consultas do Hive com o Hadoop no HDInsight usando a cURL](hdinsight-hadoop-use-hive-curl.md)
* [Executar trabalhos do Pig com o Hadoop no HDInsight usando a cURL](hdinsight-hadoop-use-pig-curl.md)

Para obter informações sobre outras maneiras de executar o MapReduce, Hive e Pig de forma interativa, veja [Usar o MapReduce com o Hadoop no HDInsight](hdinsight-use-mapreduce.md), [Usar o Hive com o Hadoop no HDInsight](hdinsight-use-hive.md) e [Usar o Pig com o Hadoop no HDInsight](hdinsight-use-pig.md).

### <a name="examples"></a>Exemplos
**Criando um cluster**

* Comando antigo (ASM) - `azure hdinsight cluster create myhdicluster --location northeurope --osType linux --storageAccountName mystorage --storageAccountKey <storagekey> --storageContainer mycontainer --userName admin --password mypassword --sshUserName sshuser --sshPassword mypassword`
* Novo comando (ARM) - `azure hdinsight cluster create myhdicluster -g myresourcegroup --location northeurope --osType linux --clusterType hadoop --defaultStorageAccountName mystorage --defaultStorageAccountKey <storagekey> --defaultStorageContainer mycontainer --userName admin -password mypassword --sshUserName sshuser --sshPassword mypassword`

**Excluindo um cluster**

* Comando antigo (ASM) - `azure hdinsight cluster delete myhdicluster`
* Novo comando (ARM) - `azure hdinsight cluster delete mycluster -g myresourcegroup`

**Listar clusters**

* Comando antigo (ASM) - `azure hdinsight cluster list`
* Novo comando (ARM) - `azure hdinsight cluster list`

> [!NOTE]
> Para o comando “list”, a especificação do grupo de recursos usando `-g` retornará apenas os clusters no grupo de recursos especificado.
> 
> 

**Mostrar informações de cluster**

* Comando antigo (ASM) - `azure hdinsight cluster show myhdicluster`
* Novo comando (ARM) - `azure hdinsight cluster show myhdicluster -g myresourcegroup`

## <a name="migrating-azure-powershell-to-azure-resource-manager"></a>Migrando o Azure PowerShell para o Azure Resource Manager
As informações gerais sobre o Azure PowerShell no modo ARM (Azure Resource Manager) podem ser encontradas em [Usando o Azure PowerShell com o Azure Resource Manager](../powershell-azure-resource-manager.md).

Os cmdlets do ARM do Azure PowerShell podem ser instalados lado a lado com os cmdlets do ASM. Os cmdlets dos dois modos podem ser diferenciados por seus nomes.  O modo ARM contém *AzureRmHDInsight* nos nomes de cmdlet, em comparação com *AzureHDInsight* no modo ASM.  Por exemplo, *New-AzureRmHDInsightCluster* versus *New-AzureHDInsightCluster*. Os parâmetros e as opções podem ter nomes novos, e há muitos novos parâmetros disponíveis ao usar o ARM.  Por exemplo, vários cmdlets exigem uma nova opção chamada *-ResourceGroupName*. 

Antes de usar os cmdlets do HDInsight, é necessário se conectar à sua conta do Azure e criar um novo grupo de recursos:

* Login-AzureRmAccount ou [Select-AzureRmProfile](https://msdn.microsoft.com/library/mt619310.aspx). Veja [Autenticando uma entidade de serviço com o Azure Resource Manager](../azure-resource-manager/resource-group-authenticate-service-principal.md)
* [New-AzureRmResourceGroup](https://msdn.microsoft.com/library/mt603739.aspx)

### <a name="renamed-cmdlets"></a>Cmdlets renomeados
Para listar os cmdlets do ASM do HDInsight no console do Windows PowerShell:

    help *azurermhdinsight*

A tabela a seguir lista os cmdlets do ASM e seus nomes no modo ARM:

| Cmdlets do ASM | Cmdlets do ARM |
| --- | --- |
| Add-AzureHDInsightConfigValues |[Add-AzureRmHDInsightConfigValues](https://msdn.microsoft.com/library/mt603530.aspx) |
| Add-AzureHDInsightMetastore |[Add-AzureRmHDInsightMetastore](https://msdn.microsoft.com/library/mt603670.aspx) |
| Add-AzureHDInsightScriptAction |[Add-AzureRmHDInsightScriptAction](https://msdn.microsoft.com/library/mt603527.aspx) |
| Add-AzureHDInsightStorage |[Add-AzureRmHDInsightStorage](https://msdn.microsoft.com/library/mt619445.aspx) |
| Get-AzureHDInsightCluster |[Get-AzureRmHDInsightCluster](https://msdn.microsoft.com/library/mt619371.aspx) |
| Get-AzureHDInsightJob |[Get-AzureRmHDInsightJob](https://msdn.microsoft.com/library/mt603590.aspx) |
| Get-AzureHDInsightJobOutput |[Get-AzureRmHDInsightJobOutput](https://msdn.microsoft.com/library/mt603793.aspx) |
| Get-AzureHDInsightProperties |[Get-AzureRmHDInsightProperties](https://msdn.microsoft.com/library/mt603546.aspx) |
| Grant-AzureHDInsightHttpServicesAccess |[Grant-AzureRmHDInsightHttpServicesAccess](https://msdn.microsoft.com/library/mt619407.aspx) |
| Grant-AzureHdinsightRdpAccess |[Grant-AzureRmHDInsightRdpServicesAccess](https://msdn.microsoft.com/library/mt603717.aspx) |
| Invoke-AzureHDInsightHiveJob |[Invoke-AzureRmHDInsightHiveJob](https://msdn.microsoft.com/library/mt603593.aspx) |
| New-AzureHDInsightCluster |[New-AzureRmHDInsightCluster](https://msdn.microsoft.com/library/mt619331.aspx) |
| New-AzureHDInsightClusterConfig |[New-AzureRmHDInsightClusterConfig](https://msdn.microsoft.com/library/mt603700.aspx) |
| New-AzureHDInsightHiveJobDefinition |[New-AzureRmHDInsightHiveJobDefinition](https://msdn.microsoft.com/library/mt619448.aspx) |
| New-AzureHDInsightMapReduceJobDefinition |[New-AzureRmHDInsightMapReduceJobDefinition](https://msdn.microsoft.com/library/mt603626.aspx) |
| New-AzureHDInsightPigJobDefinition |[New-AzureRmHDInsightPigJobDefinition](https://msdn.microsoft.com/library/mt603671.aspx) |
| New-AzureHDInsightSqoopJobDefinition |[New-AzureRmHDInsightSqoopJobDefinition](https://msdn.microsoft.com/library/mt608551.aspx) |
| New-AzureHDInsightStreamingMapReduceJobDefinition |[New-AzureRmHDInsightStreamingMapReduceJobDefinition](https://msdn.microsoft.com/library/mt603626.aspx) |
| Remove-AzureHDInsightCluster |[Remove-AzureRmHDInsightCluster](https://msdn.microsoft.com/library/mt619431.aspx) |
| Revoke-AzureHDInsightHttpServicesAccess |[Revoke-AzureRmHDInsightHttpServicesAccess](https://msdn.microsoft.com/library/mt619375.aspx) |
| Revoke-AzureHdinsightRdpAccess |[Revoke-AzureRmHDInsightRdpServicesAccess](https://msdn.microsoft.com/library/mt603523.aspx) |
| Set-AzureHDInsightClusterSize |[Set-AzureRmHDInsightClusterSize](https://msdn.microsoft.com/library/mt603513.aspx) |
| Set-AzureHDInsightDefaultStorage |[Set-AzureRmHDInsightDefaultStorage](https://msdn.microsoft.com/library/mt603486.aspx) |
| Start-AzureHDInsightJob |[Start-AzureRmHDInsightJob](https://msdn.microsoft.com/library/mt603798.aspx) |
| Stop-AzureHDInsightJob |[Stop-AzureRmHDInsightJob](https://msdn.microsoft.com/library/mt619424.aspx) |
| Use-AzureHDInsightCluster |[Use-AzureRmHDInsightCluster](https://msdn.microsoft.com/library/mt619442.aspx) |
| Wait-AzureHDInsightJob |[Wait-AzureRmHDInsightJob](https://msdn.microsoft.com/library/mt603834.aspx) |

### <a name="new-cmdlets"></a>Novos cmdlets
Apresentamos a seguir os novos cmdlets que só estão disponíveis no modo ARM. 

**Cmdlets relacionados à ação de script:**

* **Get-AzureRmHDInsightPersistedScriptAction**: obtém as ações de script persistentes de um cluster e as lista em ordem cronológica ou obtém detalhes de uma ação de script persistente especificada. 
* **Get-AzureRmHDInsightScriptActionHistory**: obtém o histórico de ação de script de um cluster e o lista em ordem cronológica inversa ou obtém detalhes de uma ação de script executada anteriormente. 
* **Remove-AzureRmHDInsightPersistedScriptAction**: remove uma ação de script persistente de um cluster HDInsight.
* **Set-AzureRmHDInsightPersistedScriptAction**: define uma ação de script executada anteriormente como uma ação de script persistente.
* **Submit-AzureRmHDInsightScriptAction**: envia uma nova ação de script para um cluster Azure HDInsight. 

Para obter mais informações de uso, veja [Personalizar clusters HDInsight baseados em Linux usando a Ação de Script](hdinsight-hadoop-customize-cluster-linux.md).

**Cmdlets relacionados à identidade do cluster:**

* **Add-AzureRmHDInsightClusterIdentity**: adiciona uma identidade de cluster a um objeto de configuração de cluster para que o cluster HDInsight possa acessar Repositórios Azure Data Lake. Veja [Criar um cluster HDInsight com o Repositório Data Lake usando o Azure PowerShell](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md).

### <a name="examples"></a>Exemplos
**Criar cluster**

Comando antigo (ASM): 

    New-AzureHDInsightCluster `
        -Name $clusterName `
        -Location $location `
        -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $storageAccountKey `
        -DefaultStorageContainerName $containerName `
        -ClusterSizeInNodes 2 `
        -ClusterType Hadoop `
        -OSType Linux `
        -Version "3.2" `
        -Credential $httpCredential `
        -SshCredential $sshCredential

Novo comando (ARM):

    New-AzureRmHDInsightCluster `
        -ClusterName $clusterName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $storageAccountKey `
        -DefaultStorageContainer $containerName  `
        -ClusterSizeInNodes 2 `
        -ClusterType Hadoop `
        -OSType Linux `
        -Version "3.2" `
        -HttpCredential $httpcredentials `
        -SshCredential $sshCredentials


**Excluir cluster**

Comando antigo (ASM):

    Remove-AzureHDInsightCluster -name $clusterName 

Novo comando (ARM):

    Remove-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $clusterName 

**Listar clusters**

Comando antigo (ASM):

    Get-AzureHDInsightCluster

Novo comando (ARM):

    Get-AzureRmHDInsightCluster 

**Mostrar cluster**

Comando antigo (ASM):

    Get-AzureHDInsightCluster -Name $clusterName

Novo comando (ARM):

    Get-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -clusterName $clusterName


#### <a name="other-samples"></a>Outras amostras
* [Criar clusters HDInsight](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)
* [Enviar trabalhos Hive](hdinsight-hadoop-use-hive-powershell.md)
* [Enviar trabalhos do Pig](hdinsight-hadoop-use-pig-powershell.md)
* [Enviar trabalhos do Sqoop](hdinsight-hadoop-use-sqoop-powershell.md)

## <a name="migrating-to-the-arm-based-hdinsight-net-sdk"></a>Migrando para o SDK do .NET do HDInsight baseado em ARM
O [SDK do .NET do HDInsight](https://msdn.microsoft.com/library/azure/mt416619.aspx) baseado em ASM (Gerenciamento de Serviços do Azure) foi preterido. Nós o incentivamos a usar o [SDK do .NET do HDInsight](https://msdn.microsoft.com/library/azure/mt271028.aspx)baseado em ARM (Gerenciamento de Recursos do Azure). Os seguintes pacotes HDInsight baseados em ASM estão sendo preteridos.

* `Microsoft.WindowsAzure.Management.HDInsight`
* `Microsoft.Hadoop.Client`

Esta seção fornece sugestões de mais informações sobre como executar determinadas tarefas usando o SDK baseado em ARM.

| Como usar o SDK do HDInsight baseado em ARM | Links |
| --- | --- |
| Criar clusters HDInsight usando o SDK do .NET |Veja [Criar clusters HDInsight usando o SDK do .NET](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md) |
| Personalizar um cluster usando a Ação de Script com o SDK do .NET |Veja [Personalizar os clusters HDInsight do Linux usando a Ação de Script](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md#use-script-action) |
| Autenticar aplicativos de forma interativa usando o Azure Active Directory com o SDK do .NET |Veja [Executar consultas do Hive usando o SDK do .NET](hdinsight-hadoop-use-hive-dotnet-sdk.md). O trecho de código neste artigo usa o método de autenticação interativa. |
| Autenticar aplicativos de forma não interativa usando o Azure Active Directory com o SDK do .NET |Veja [Criar aplicativos não interativos para o HDInsight](hdinsight-create-non-interactive-authentication-dotnet-applications.md) |
| Enviar um trabalho do Hive usando o SDK do .NET |Veja [Enviar trabalhos do Hive](hdinsight-hadoop-use-hive-dotnet-sdk.md) |
| Enviar um trabalho do Pig usando o SDK do .NET |Veja [Enviar trabalhos do Pig](hdinsight-hadoop-use-pig-dotnet-sdk.md) |
| Enviar um trabalho do Sqoop usando o SDK do .NET |Veja [Enviar trabalhos do Sqoop](hdinsight-hadoop-use-sqoop-dotnet-sdk.md) |
| Listar clusters HDInsight usando o SDK do .NET |Veja [Listar os clusters HDInsight](hdinsight-administer-use-dotnet-sdk.md#list-clusters) |
| Escalar clusters HDInsight usando o SDK do .NET |Veja [Escalar clusters HDInsight](hdinsight-administer-use-dotnet-sdk.md#scale-clusters) |
| Conceder/revogar acesso aos clusters HDInsight usando o SDK do .NET |Veja [Conceder/revogar acesso aos clusters HDInsight](hdinsight-administer-use-dotnet-sdk.md#grantrevoke-access) |
| Atualizar credenciais de usuário HTTP de clusters HDInsight usando o SDK do .NET |Veja [Atualizar credenciais de usuário HTTP de clusters HDInsight](hdinsight-administer-use-dotnet-sdk.md#update-http-user-credentials) |
| Encontrar a conta de armazenamento padrão para clusters HDInsight usando o SDK do .NET |Veja [Encontrar a conta de armazenamento padrão para clusters HDInsight](hdinsight-administer-use-dotnet-sdk.md#find-the-default-storage-account) |
| Excluir clusters HDInsight usando o SDK do .NET |Veja [Excluir clusters HDInsight](hdinsight-administer-use-dotnet-sdk.md#delete-clusters) |

### <a name="examples"></a>Exemplos
Estes são alguns exemplos sobre como uma operação é executada usando o SDK baseado em ASM e o trecho de código equivalente para o SDK baseado em ARM.

**Criando um cliente CRUD do cluster**

* Comando antigo (ASM)
  
        //Certificate auth
        //This logs the application in using a subscription administration certificate, which is not offered in Azure Resource Manager (ARM)
  
        const string subid = "454467d4-60ca-4dfd-a556-216eeeeeeee1";
        var cred = new HDInsightCertificateCredential(new Guid(subid), new X509Certificate2(@"path\to\certificate.cer"));
        var client = HDInsightClient.Connect(cred);
* Novo comando (ARM) (Autorização de entidade de serviço)
  
        //Service principal auth
        //This will log the application in as itself, rather than on behalf of a specific user.
        //For details, including how to set up the application, see:
        //   https://azure.microsoft.com/en-us/documentation/articles/hdinsight-create-non-interactive-authentication-dotnet-applications/
  
        var authFactory = new AuthenticationFactory();
  
        var account = new AzureAccount { Type = AzureAccount.AccountType.ServicePrincipal, Id = clientId };
  
        var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
  
        var accessToken = authFactory.Authenticate(account, env, tenantId, secretKey, ShowDialog.Never).AccessToken;
  
        var creds = new TokenCloudCredentials(subId.ToString(), accessToken);
  
        _hdiManagementClient = new HDInsightManagementClient(creds);
* Novo comando (ARM) (Autorização de usuário)
  
        //User auth
        //This will log the application in on behalf of the user.
        //The end-user will see a login popup.
  
        var authFactory = new AuthenticationFactory();
  
        var account = new AzureAccount { Type = AzureAccount.AccountType.User, Id = username };
  
        var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
  
        var accessToken = authFactory.Authenticate(account, env, AuthenticationFactory.CommonAdTenant, password, ShowDialog.Auto).AccessToken;
  
        var creds = new TokenCloudCredentials(subId.ToString(), accessToken);
  
        _hdiManagementClient = new HDInsightManagementClient(creds);

**Criando um cluster**

* Comando antigo (ASM)
  
        var clusterInfo = new ClusterCreateParameters
                    {
                        Name = dnsName,
                        DefaultStorageAccountKey = key,
                        DefaultStorageContainer = defaultStorageContainer,
                        DefaultStorageAccountName = storageAccountDnsName,
                        ClusterSizeInNodes = 1,
                        ClusterType = type,
                        Location = "West US",
                        UserName = "admin",
                        Password = "*******",
                        Version = version,
                        HeadNodeSize = NodeVMSize.Large,
                    };
        clusterInfo.CoreConfiguration.Add(new KeyValuePair<string, string>("config1", "value1"));
        client.CreateCluster(clusterInfo);
* Novo comando (ARM)
  
        var clusterCreateParameters = new ClusterCreateParameters
            {
                Location = "West US",
                ClusterType = "Hadoop",
                Version = "3.1",
                OSType = OSType.Linux,
                DefaultStorageAccountName = "mystorage.blob.core.windows.net",
                DefaultStorageAccountKey =
                    "O9EQvp3A3AjXq/W27rst1GQfLllhp0gUeiUUn2D8zX2lU3taiXSSfqkZlcPv+nQcYUxYw==",
                UserName = "hadoopuser",
                Password = "*******",
                HeadNodeSize = "ExtraLarge",
                RdpUsername = "hdirp",
                RdpPassword = ""*******",
                RdpAccessExpiry = new DateTime(2025, 3, 1),
                ClusterSizeInNodes = 5
            };
        var coreConfigs = new Dictionary<string, string> {{"config1", "value1"}};
        clusterCreateParameters.Configurations.Add(ConfigurationKey.CoreSite, coreConfigs);

**Habilitando o acesso HTTP**

* Comando antigo (ASM)
  
        client.EnableHttp(dnsName, "West US", "admin", "*******");
* Novo comando (ARM)
  
        var httpParams = new HttpSettingsParameters
        {
               HttpUserEnabled = true,
               HttpUsername = "admin",
               HttpPassword = "*******",
        };
        client.Clusters.ConfigureHttpSettings(resourceGroup, dnsname, httpParams);

**Excluindo um cluster**

* Comando antigo (ASM)
  
        client.DeleteCluster(dnsName);
* Novo comando (ARM)
  
        client.Clusters.Delete(resourceGroup, dnsname);

