---
title: Migrar para as ferramentas do Azure Resource Manager do Azure HDInsight
description: Como migrar para as ferramentas de desenvolvimento do Azure Resource Manager para clusters HDInsight
ms.service: hdinsight
ms.custom: hdinsightactive, devx-track-azurecli
ms.topic: how-to
ms.date: 02/21/2018
ms.openlocfilehash: a8f808cd43f96f26db0de28e8059d02d9488320a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102434654"
---
# <a name="migrating-to-azure-resource-manager-based-development-tools-for-hdinsight-clusters"></a>Migrando para ferramentas de desenvolvimento baseadas no Azure Resource Manager dos clusters HDInsight

O HDInsight está preterindo as ferramentas baseadas em ASM (Azure Service Manager) para o HDInsight. Se você usa o Azure PowerShell, a CLI Clássica do Azure ou o SDK do .NET do HDInsight para trabalhar com clusters HDInsight, nós o incentivamos a usar as versões do Azure Resource Manager do PowerShell, da CLI e do SDK do .NET no futuro. Este artigo fornece sugestões sobre como migrar para a nova abordagem baseada no Resource Manager. Sempre que aplicável, este documento destaca as diferenças entre as abordagens do ASM e do Resource Manager em relação ao HDInsight.

> [!IMPORTANT]  
> O suporte para o PowerShell, a CLI e o SDK do .NET baseado em ASM será descontinuado em **1º de janeiro de 2017**.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="migrating-azure-classic-cli-to-azure-resource-manager"></a>Migrando a CLI Clássica do Azure para o Azure Resource Manager

> [!IMPORTANT]  
> A CLI do Azure não oferece suporte para trabalhar com clusters HDInsight. Você ainda pode usar a CLI Clássica do Azure com HDInsight, no entanto, a CLI Clássica do Azure foi preterida.

A seguir, há comandos básicos para trabalhar com o HDInsight por meio da CLI clássica do Azure:

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
Se você usar os comandos `azure hdinsight job` para enviar trabalhos para o cluster HDInsight, esses comandos não estarão disponíveis por meio dos comandos do Resource Manager. Se você precisar enviar trabalhos ao HDInsight por meio de scripts de forma programática, será necessário usar as APIs REST fornecidas pelo HDInsight. Para obter mais informações sobre como enviar trabalhos usando as APIs REST, confira os seguintes documentos.

* [Executar trabalhos do MapReduce com o Hadoop no HDInsight usando a cURL](hadoop/apache-hadoop-use-mapreduce-curl.md)
* [Executar consultas do Apache Hive com Apache Hadoop no HDInsight usando cURL](hadoop/apache-hadoop-use-hive-curl.md)


Para saber mais sobre outras maneiras de executar o MapReduce do Apache Hadoop, Apache Hive e Apache Pig de forma interativa, veja [Usar o MapReduce do Apache Hadoop com Hadoop no HDInsight](hadoop/hdinsight-use-mapreduce.md), [Usar o Apache Hive com Apache Hadoop no HDInsight](hadoop/hdinsight-use-hive.md) e [Usar o Apache Pig com Apache Hadoop no HDInsight](./index.yml).

### <a name="examples"></a>Exemplos
**Criar um cluster**

* Comando antigo (ASM) - `azure hdinsight cluster create myhdicluster --location northeurope --osType linux --storageAccountName mystorage --storageAccountKey <storagekey> --storageContainer mycontainer --userName admin --password mypassword --sshUserName sshuser --sshPassword mypassword`
* Novo comando – `azure hdinsight cluster create myhdicluster -g myresourcegroup --location northeurope --osType linux --clusterType hadoop --defaultStorageAccountName mystorage --defaultStorageAccountKey <storagekey> --defaultStorageContainer mycontainer --userName admin -password mypassword --sshUserName sshuser --sshPassword mypassword`

**Excluindo um cluster**

* Comando antigo (ASM) - `azure hdinsight cluster delete myhdicluster`
* Novo comando – `azure hdinsight cluster delete mycluster -g myresourcegroup`

**Listar clusters**

* Comando antigo (ASM) - `azure hdinsight cluster list`
* Novo comando – `azure hdinsight cluster list`

> [!NOTE]  
> Para o comando “list”, a especificação do grupo de recursos usando `-g` retornará apenas os clusters no grupo de recursos especificado.

**Mostrar informações de cluster**

* Comando antigo (ASM) - `azure hdinsight cluster show myhdicluster`
* Novo comando – `azure hdinsight cluster show myhdicluster -g myresourcegroup`

## <a name="migrating-azure-powershell-to-azure-resource-manager"></a>Migrando o Azure PowerShell para o Azure Resource Manager
As informações gerais sobre o Azure PowerShell no modo Azure Resource Manager podem ser encontradas em [Usando o Azure PowerShell com o Azure Resource Manager](../azure-resource-manager/management/manage-resources-powershell.md).

Os cmdlets do Resource Manager do Azure PowerShell podem ser instalados lado a lado com os cmdlets do ASM. Os cmdlets dos dois modos podem ser diferenciados por seus nomes.  O modo do Gerenciador de recursos tem *AzHDInsight* nos nomes de cmdlets que se comparam ao *AzureHDInsight* no modo de gerenciamento de serviços do Azure mais antigo.  Por exemplo, *New-AzHDInsightCluster* versus *New-AzureHDInsightCluster*. Os parâmetros e as opções podem ter nomes novos, e há muitos novos parâmetros disponíveis ao usar o Resource Manager.  Por exemplo, vários cmdlets exigem uma nova opção chamada *-ResourceGroupName*.

Antes de usar os cmdlets do HDInsight, é necessário se conectar à sua conta do Azure e criar um novo grupo de recursos:

* [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount)
* [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)

### <a name="renamed-cmdlets"></a>Cmdlets renomeados
Para listar os cmdlets do ASM do HDInsight no console do Windows PowerShell:

```powershell
help *azurehdinsight*
```

A tabela a seguir lista os cmdlets do ASM e seus nomes no modo Resource Manager:

| Cmdlets do ASM | Cmdlets do Resource Manager |
| --- | --- |
| Add-AzureHDInsightConfigValue |[Add-AzHDInsightConfigValue](/powershell/module/az.hdinsight/add-azhdinsightconfigvalue) |
| Add-AzureHDInsightMetastore |[Add-AzHDInsightMetastore](/powershell/module/az.hdinsight/add-azhdinsightmetastore) |
| Add-AzureHDInsightScriptAction |[Add-AzHDInsightScriptAction](/powershell/module/az.hdinsight/add-azhdinsightscriptaction) |
| Add-AzureHDInsightStorage |[Add-AzHDInsightStorage](/powershell/module/az.hdinsight/add-azhdinsightstorage) |
| Get-AzureHDInsightCluster |[Get-AzHDInsightCluster](/powershell/module/az.hdinsight/get-azhdinsightcluster) |
| Get-AzureHDInsightJob |[Get-AzHDInsightJob](/powershell/module/az.hdinsight/get-azhdinsightjob) |
| Get-AzureHDInsightJobOutput |[Get-AzHDInsightJobOutput](/powershell/module/az.hdinsight/get-azhdinsightjoboutput) |
| Get-AzureHDInsightProperty |[Get-AzHDInsightProperty](/powershell/module/az.hdinsight/get-azhdinsightproperty) |
| Grant-AzureHDInsightHttpServicesAccess |[Grant-AzureRmHDInsightHttpServicesAccess](/powershell/module/azurerm.hdinsight/grant-azurermhdinsighthttpservicesaccess) |
| Grant-AzureHdinsightRdpAccess |[Grant-AzHDInsightRdpServicesAccess](/powershell/module/az.hdinsight/grant-azhdinsightrdpservicesaccess) |
| Invoke-AzureHDInsightHiveJob |[Invoke-AzHDInsightHiveJob](/powershell/module/az.hdinsight/invoke-azhdinsighthivejob) |
| New-AzureHDInsightCluster |[New-AzHDInsightCluster](/powershell/module/az.hdinsight/new-azhdinsightcluster) |
| New-AzureHDInsightClusterConfig |[New-AzHDInsightClusterConfig](/powershell/module/az.hdinsight/new-azhdinsightclusterconfig) |
| New-AzureHDInsightHiveJobDefinition |[New-AzHDInsightHiveJobDefinition](/powershell/module/az.hdinsight/new-azhdinsighthivejobdefinition) |
| New-AzureHDInsightMapReduceJobDefinition |[New-AzHDInsightMapReduceJobDefinition](/powershell/module/az.hdinsight/new-azhdinsightmapreducejobdefinition) |
| New-AzureHDInsightPigJobDefinition |[New-AzHDInsightPigJobDefinition](/powershell/module/az.hdinsight/new-azhdinsightpigjobdefinition) |
| New-AzureHDInsightSqoopJobDefinition |[New-AzHDInsightSqoopJobDefinition](/powershell/module/az.hdinsight/new-azhdinsightsqoopjobdefinition) |
| New-AzureHDInsightStreamingMapReduceJobDefinition |[New-AzHDInsightStreamingMapReduceJobDefinition](/powershell/module/az.hdinsight/new-azhdinsightstreamingmapreducejobdefinition) |
| Remove-AzureHDInsightCluster |[Remove-AzHDInsightCluster](/powershell/module/az.hdinsight/remove-azhdinsightcluster) |
| Revoke-AzureHDInsightHttpServicesAccess |[Revoke-AzHDInsightHttpServicesAccess](/powershell/module/azurerm.hdinsight/revoke-azurermhdinsighthttpservicesaccess) |
| Revoke-AzureHdinsightRdpAccess |[Revoke-AzHDInsightRdpServicesAccess](/powershell/module/az.hdinsight/revoke-azhdinsightrdpservicesaccess) |
| Set-AzureHDInsightClusterSize |[Set-AzHDInsightClusterSize](/powershell/module/az.hdinsight/set-azhdinsightclustersize) |
| Set-AzureHDInsightDefaultStorage |[Set-AzHDInsightDefaultStorage](/powershell/module/az.hdinsight/set-azhdinsightdefaultstorage) |
| Start-AzureHDInsightJob |[Start-AzHDInsightJob](/powershell/module/az.hdinsight/start-azhdinsightjob) |
| Stop-AzureHDInsightJob |[Stop-AzHDInsightJob](/powershell/module/az.hdinsight/stop-azhdinsightjob) |
| Use-AzureHDInsightCluster |[Use-AzHDInsightCluster](/powershell/module/az.hdinsight/use-azhdinsightcluster) |
| Wait-AzureHDInsightJob |[Wait-AzHDInsightJob](/powershell/module/az.hdinsight/wait-azhdinsightjob) |

### <a name="new-cmdlets"></a>Novos cmdlets
A seguir há os novos cmdlets que estão disponíveis no modo Resource Manager. 

**Cmdlets relacionados à ação de script:**

* **Get-AzHDInsightPersistedScriptAction**: Obtém as ações de script persistentes para um cluster e lista-as em ordem cronológica ou obtém detalhes de uma ação de script persistente especificada. 
* **Get-AzHDInsightScriptActionHistory**: Obtém o histórico de ações de script para um cluster e o lista em ordem cronológica inversa ou obtém detalhes de uma ação de script executada anteriormente. 
* **Remove-AzHDInsightPersistedScriptAction**: Remove uma ação de script persistente de um cluster HDInsight.
* **Set-AzHDInsightPersistedScriptAction**: define uma ação de script executada anteriormente como uma ação de script persistente.
* **Submit-AzHDInsightScriptAction**: envia uma nova ação de script para um cluster do Azure HDInsight. 

Para obter mais informações de uso, veja [Personalizar clusters HDInsight baseados em Linux usando a Ação de Script](hdinsight-hadoop-customize-cluster-linux.md).

**Cmdlets relacionados à identidade do cluster:**

* **Add-AzHDInsightClusterIdentity**: adiciona uma identidade de cluster a um objeto de configuração de cluster para que o cluster HDInsight possa acessar Azure data Lake Storage. Veja [Criar um cluster HDInsight com o Data Lake Storage usando o Azure PowerShell](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md).

### <a name="examples"></a>Exemplos
**Criar cluster**

Comando antigo (ASM): 

```azurepowershell
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
```

Novo comando:

```azurepowershell
New-AzHDInsightCluster `
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
```

**Excluir cluster**

Comando antigo (ASM):

```azurepowershell
Remove-AzureHDInsightCluster -name $clusterName 
```

Novo comando:

```azurepowershell
Remove-AzHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $clusterName
```

**Listar clusters**

Comando antigo (ASM):

```azurepowershell
Get-AzureHDInsightCluster
```

Novo comando:

```azurepowershell
Get-AzHDInsightCluster
```

**Mostrar cluster**

Comando antigo (ASM):

```azurepowershell
Get-AzureHDInsightCluster -Name $clusterName
```

Novo comando:

```azurepowershell
Get-AzHDInsightCluster -ResourceGroupName $resourceGroupName -clusterName $clusterName
```

#### <a name="other-samples"></a>Outras amostras
* [Criar clusters do HDInsight](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)
* [Enviar trabalhos do Apache Hive](hadoop/apache-hadoop-use-hive-powershell.md)
* [Enviar trabalhos do Apache Sqoop](hadoop/apache-hadoop-use-sqoop-powershell.md)

## <a name="migrating-to-the-new-hdinsight-net-sdk"></a>Migrando para o novo SDK do .NET do HDInsight
O [SDK do .NET do HDInsight](/previous-versions/azure/reference/mt416619(v=azure.100)) baseado em ASM (Gerenciamento de Serviços do Azure) foi preterido. Nós o incentivamos a usar o [SDK do .NET do HDInsight baseado no Resource Manager](/dotnet/api/overview/azure/hdinsight) baseado no Gerenciamento de Recursos do Azure. Os seguintes pacotes HDInsight baseados em ASM estão sendo preteridos.

* `Microsoft.WindowsAzure.Management.HDInsight`
* `Microsoft.Hadoop.Client`

Esta seção fornece sugestões de mais informações sobre como executar determinadas tarefas usando o SDK baseado no Resource Manager.

| Como usar o SDK do HDInsight baseado no Resource Manager | Links |
| --- | --- |
| SDK do Azure HDInsight para .NET|Consulte [SDK do Azure HDInsight para .net](/dotnet/api/overview/azure/hdinsight) |
| Autenticar aplicativos de forma interativa usando o Azure Active Directory com o SDK do .NET |Consulte [Executar consultas do Apache Hive usando o .NET SDK](hadoop/apache-hadoop-use-hive-dotnet-sdk.md). O snippet de código neste artigo usa o método de autenticação interativa. |
| Autenticar aplicativos de forma não interativa usando o Azure Active Directory com o SDK do .NET |Veja [Criar aplicativos não interativos para o HDInsight](hdinsight-create-non-interactive-authentication-dotnet-applications.md) |
| Enviar um trabalho do Apache Hive usando SDK do .NET |Consulte [Enviar trabalhos do Apache Hive](hadoop/apache-hadoop-use-hive-dotnet-sdk.md) |
| Enviar um trabalho do Apache Sqoop usando SDK do .NET |Consulte [Enviar trabalhos do Apache Sqoop](hadoop/apache-hadoop-use-sqoop-dotnet-sdk.md) |
| Listar clusters HDInsight usando o SDK do .NET |Veja [Listar os clusters HDInsight](hdinsight-administer-use-dotnet-sdk.md#list-clusters) |
| Escalar clusters HDInsight usando o SDK do .NET |Veja [Escalar clusters HDInsight](hdinsight-administer-use-dotnet-sdk.md#scale-clusters) |
| Conceder/revogar acesso aos clusters HDInsight usando o SDK do .NET |Veja [Conceder/revogar acesso aos clusters HDInsight](hdinsight-administer-use-dotnet-sdk.md#grantrevoke-access) |
| Atualizar credenciais de usuário HTTP de clusters HDInsight usando o SDK do .NET |Veja [Atualizar credenciais de usuário HTTP de clusters HDInsight](hdinsight-administer-use-dotnet-sdk.md#update-http-user-credentials) |
| Encontrar a conta de armazenamento padrão para clusters HDInsight usando o SDK do .NET |Veja [Encontrar a conta de armazenamento padrão para clusters HDInsight](hdinsight-administer-use-dotnet-sdk.md#find-the-default-storage-account) |
| Excluir clusters HDInsight usando o SDK do .NET |Veja [Excluir clusters HDInsight](hdinsight-administer-use-dotnet-sdk.md#delete-clusters) |

### <a name="examples"></a>Exemplos
Estes são alguns exemplos sobre como uma operação é executada usando o SDK baseado em ASM e o snippet de código equivalente para o SDK baseado no Resource Manager.

**Criando um cliente CRUD do cluster**

* Comando antigo (ASM)

  ```azurecli
  //Certificate auth
  //This logs the application in using a subscription administration certificate, which is not offered in Azure Resource Manager
  
  const string subid = "454467d4-60ca-4dfd-a556-216eeeeeeee1";
  var cred = new HDInsightCertificateCredential(new Guid(subid), new X509Certificate2(@"path\to\certificate.cer"));
  var client = HDInsightClient.Connect(cred);
  ```
* Novo comando (Autorização de entidade de serviço)

  ```azurecli
  //Service principal auth
  //This will log the application in as itself, rather than on behalf of a specific user.
  //For details, including how to set up the application, see:
  //   https://azure.microsoft.com/documentation/articles/hdinsight-create-non-interactive-authentication-dotnet-applications/
  
  var authFactory = new AuthenticationFactory();
  
  var account = new AzureAccount { Type = AzureAccount.AccountType.ServicePrincipal, Id = clientId };
  
  var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
  
  var accessToken = authFactory.Authenticate(account, env, tenantId, secretKey, ShowDialog.Never).AccessToken;
  
  var creds = new TokenCloudCredentials(subId.ToString(), accessToken);
  
  _hdiManagementClient = new HDInsightManagementClient(creds);
  ```

* Novo comando (Autorização de usuário)

  ```azurecli
  //User auth
  //This will log the application in on behalf of the user.
  //The end-user will see a login popup.
  
  var authFactory = new AuthenticationFactory();
  
  var account = new AzureAccount { Type = AzureAccount.AccountType.User, Id = username };
  
  var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
  
  var accessToken = authFactory.Authenticate(account, env, AuthenticationFactory.CommonAdTenant, password, ShowDialog.Auto).AccessToken;
  
  var creds = new TokenCloudCredentials(subId.ToString(), accessToken);
  
  _hdiManagementClient = new HDInsightManagementClient(creds);
  ```

**Criar um cluster**

* Comando antigo (ASM)

  ```azurecli
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
  ```

* Novo comando

  ```azurecli
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
  ```

**Habilitando o acesso HTTP**

* Comando antigo (ASM)

  ```azurecli
  client.EnableHttp(dnsName, "West US", "admin", "*******");
  ```

* Novo comando
  
  ```azurecli
  var httpParams = new HttpSettingsParameters
  {
         HttpUserEnabled = true,
         HttpUsername = "admin",
         HttpPassword = "*******",
  };
  client.Clusters.ConfigureHttpSettings(resourceGroup, dnsname, httpParams);
  ```

**Excluindo um cluster**

* Comando antigo (ASM)

  ```azurecli
  client.DeleteCluster(dnsName);
  ```

* Novo comando

  ```azurecli
  client.Clusters.Delete(resourceGroup, dnsname);
  ```
