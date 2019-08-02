---
title: Migrar para o acesso granular baseado em função para configurações de cluster – Azure HDInsight
description: Saiba mais sobre as alterações necessárias como parte da migração para o acesso baseado em função granular para configurações de cluster HDInsight.
author: tylerfox
ms.author: tyfox
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: ebb1723a9a2b2d069a1766d4f78151f2b684c5b9
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68464674"
---
# <a name="migrate-to-granular-role-based-access-for-cluster-configurations"></a>Migrar para acesso baseado em função granular para configurações de cluster

Estamos introduzindo algumas alterações importantes para dar suporte ao acesso mais refinado baseado em função para obter informações confidenciais. Como parte dessas alterações, algumas **ações poderão ser necessárias** se você estiver usando uma das [entidades/cenários afetados](#am-i-affected-by-these-changes).

## <a name="what-is-changing"></a>O que está mudando?

Anteriormente, os segredos poderiam ser obtidos por meio da API do HDInsight por usuários de cluster que possuam as funções de proprietário, colaborador ou de [RBAC](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles)do leitor, pois `*/read` estavam disponíveis para qualquer pessoa com a permissão.
No futuro, o acesso a esses segredos exigirá a permissão, o `Microsoft.HDInsight/clusters/configurations/*` que significa que eles não podem mais ser acessados por usuários com a função leitor. Os segredos são definidos como valores que podem ser usados para obter acesso mais elevado do que a função de um usuário deve permitir. Isso inclui valores como credenciais HTTP de gateway de cluster, chaves de conta de armazenamento e credenciais de banco de dados.

Também estamos introduzindo uma nova função de [operador de cluster HDInsight](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#hdinsight-cluster-operator) que poderá recuperar segredos sem receber as permissões administrativas de colaborador ou proprietário. Resumidamente:

| Role                                  | Possíveis                                                                                       | Em frente       |
|---------------------------------------|--------------------------------------------------------------------------------------------------|-----------|
| Leitor                                | -Acesso de leitura, incluindo segredos                                                                   | -Acesso de leitura  , excluindo segredos |           |   |   |
| Operador de cluster HDInsight<br>(Nova função) | N/D                                                                                              | -Acesso de leitura/gravação, incluindo segredos         |   |   |
| Contribuidor                           | -Acesso de leitura/gravação, incluindo segredos<br>-Crie e gerencie todos os tipos de recursos do Azure.     | Nenhuma alteração |
| Proprietário                                 | -Acesso de leitura/gravação, incluindo segredos<br>-Acesso completo a todos os recursos<br>-Delegar acesso a outras pessoas | Nenhuma alteração |

Para obter informações sobre como adicionar a atribuição de função de operador de cluster HDInsight a um usuário para conceder acesso de leitura/gravação a segredos de cluster, consulte a seção abaixo, [Adicionar a atribuição de função de operador de cluster HDInsight a um usuário](#add-the-hdinsight-cluster-operator-role-assignment-to-a-user).

## <a name="am-i-affected-by-these-changes"></a>Sou afetado por essas alterações?

As seguintes entidades e cenários são afetados:

- [API](#api): Usuários que usam `/configurations` os `/configurations/{configurationName}` pontos de extremidade ou.
- [Ferramentas do Azure HDInsight para Visual Studio Code](#azure-hdinsight-tools-for-visual-studio-code) versão 1.1.1 ou inferior.
- [Azure Toolkit for IntelliJ](#azure-toolkit-for-intellij) versão 3.20.0 ou inferior.
- [Ferramentas de Azure data Lake e Stream Analytics para Visual Studio abaixo da](#azure-data-lake-and-stream-analytics-tools-for-visual-studio) versão 2.3.9000.1.
- [Azure Toolkit for Eclipse](#azure-toolkit-for-eclipse) versão 3.15.0 ou inferior.
- [SDK para .NET](#sdk-for-net)
    - [versões 1. x ou 2. x](#versions-1x-and-2x): Usuários que usam `GetClusterConfigurations`os `GetConnectivitySettings`métodos `ConfigureHttpSettings`, `EnableHttp` , ou`DisableHttp` da classe ConfigurationsOperationsExtensions.
    - [versões 3. x e superior](#versions-3x-and-up): Usuários que usam `Get`os `Update`métodos `EnableHttp`,, `DisableHttp` ou da `ConfigurationsOperationsExtensions` classe.
- [SDK para Python](#sdk-for-python): Usuários que usam `get` os `update` métodos ou da `ConfigurationsOperations` classe.
- [SDK para Java](#sdk-for-java): Usuários que usam `update` os `get` métodos ou da `ConfigurationsInner` classe.
- [SDK para go](#sdk-for-go): Usuários que usam `Get` os `Update` métodos ou da `ConfigurationsClient` estrutura.
- [AZ. HDInsight PowerShell](#azhdinsight-powershell) abaixo da versão 2.0.0.
Veja as seções abaixo (ou use os links acima) para ver as etapas de migração para seu cenário.

### <a name="api"></a>API

As seguintes APIs serão alteradas ou preteridas:

- [**Obter/Configurations/{ConfigurationName}** ](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-configuration) (informações confidenciais removidas)
    - Usado anteriormente para obter tipos de configuração individuais (incluindo segredos).
    - Essa chamada à API agora retornará tipos de configuração individuais com segredos omitidos. Para obter todas as configurações, incluindo segredos, use a nova chamada POST/Configurations. Para obter apenas as configurações de gateway, use a nova chamada POST/getGatewaySettings.
- [**Obter/Configurations**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-configuration) preterido
    - Usado anteriormente para obter todas as configurações (incluindo segredos)
    - Esta chamada à API não terá mais suporte. Para obter todas as configurações no futuro, use a nova chamada POST/Configurations. Para obter configurações com parâmetros confidenciais omitidos, use a chamada GET/configurations/{configurationName}.
- [**Postar/Configurations/{ConfigurationName}** ](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#update-gateway-settings) preterido
    - Usado anteriormente para atualizar as credenciais do gateway.
    - Esta chamada à API será preterida e não terá mais suporte. Em vez disso, use a nova POSTAgem/updateGatewaySettings.

As seguintes APIs de substituição foram adicionadas:</span>

- [**POSTAR/Configurations**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#list-configurations)
    - Use essa API para obter todas as configurações, incluindo segredos.
- [**POST /getGatewaySettings**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-gateway-settings)
    - Use essa API para obter as configurações de gateway.
- [**POST /updateGatewaySettings**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#update-gateway-settings)
    - Use essa API para atualizar as configurações do gateway (nome de usuário e/ou senha).

### <a name="azure-hdinsight-tools-for-visual-studio-code"></a>Ferramentas do Azure HDInsight para Visual Studio Code

Se você estiver usando a versão 1.1.1 ou inferior, atualize para a [versão mais recente das ferramentas do Azure HDInsight para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=mshdinsight.azure-hdinsight&ssr=false) para evitar interrupções.

### <a name="azure-toolkit-for-intellij"></a>Kit de Ferramentas do Azure para IntelliJ

Se você estiver usando a versão 3.20.0 ou inferior, atualize para a [versão mais recente do plug-in Azure Toolkit for IntelliJ](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij) para evitar interrupções.

### <a name="azure-data-lake-and-stream-analytics-tools-for-visual-studio"></a>Ferramentas de Azure Data Lake e Stream Analytics para Visual Studio

Atualize para a versão 2.3.9000.1 ou posterior das [ferramentas Azure data Lake e Stream Analytics para Visual Studio](https://marketplace.visualstudio.com/items?itemName=ADLTools.AzureDataLakeandStreamAnalyticsTools&ssr=false#overview) para evitar interrupções.  Para obter ajuda com a atualização, consulte nossa documentação, [Update data Lake Tools for Visual Studio](https://docs.microsoft.com/azure/hdinsight/hadoop/apache-hadoop-visual-studio-tools-get-started#update-data-lake-tools-for-visual-studio).

### <a name="azure-toolkit-for-eclipse"></a>Kit de ferramentas do Azure para Eclipse

Se você estiver usando a versão 3.15.0 ou inferior, atualize para a [versão mais recente do Azure Toolkit for Eclipse](https://marketplace.eclipse.org/content/azure-toolkit-eclipse) para evitar interrupções.

### <a name="sdk-for-net"></a>SDK para .NET

#### <a name="versions-1x-and-2x"></a>Versões 1. x e 2. x

Atualize para a [versão 2.1.0](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/2.1.0) do SDK do HDInsight para .net. Modificações mínimas de código poderão ser necessárias se você estiver usando um método afetado por essas alterações:

- `ClusterOperationsExtensions.GetClusterConfigurations`**não retornará mais parâmetros confidenciais** , como chaves de armazenamento (site principal) ou credenciais http (gateway).
    - Para recuperar todas as configurações, incluindo parâmetros confidenciais, `ClusterOperationsExtensions.ListConfigurations` use o futuro.  Observe que os usuários com a função ' leitor ' não poderão usar esse método. Isso permite o controle granular sobre quais usuários podem acessar informações confidenciais para um cluster.
    - Para recuperar apenas as credenciais de gateway HTTP `ClusterOperationsExtensions.GetGatewaySettings`, use.

- `ClusterOperationsExtensions.GetConnectivitySettings`Agora está preterido e foi substituído por `ClusterOperationsExtensions.GetGatewaySettings`.

- `ClusterOperationsExtensions.ConfigureHttpSettings`Agora está preterido e foi substituído por `ClusterOperationsExtensions.UpdateGatewaySettings`.

- `ConfigurationsOperationsExtensions.EnableHttp`e `DisableHttp` agora estão preteridos. O HTTP agora está sempre habilitado, portanto, esses métodos não são mais necessários.

#### <a name="versions-3x-and-up"></a>Versões 3. x e superior

Atualize para a [versão 5.0.0](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/5.0.0) ou posterior do SDK do HDInsight para .net. Modificações mínimas de código poderão ser necessárias se você estiver usando um método afetado por essas alterações:

- [`ConfigurationOperationsExtensions.Get`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.get?view=azure-dotnet)**não retornará mais parâmetros confidenciais** , como chaves de armazenamento (site principal) ou credenciais http (gateway).
    - Para recuperar todas as configurações, incluindo parâmetros confidenciais, [`ConfigurationOperationsExtensions.List`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.list?view=azure-dotnet) use o futuro.  Observe que os usuários com a função ' leitor ' não poderão usar esse método. Isso permite o controle granular sobre quais usuários podem acessar informações confidenciais para um cluster. 
    - Para recuperar apenas as credenciais de gateway HTTP [`ClusterOperationsExtensions.GetGatewaySettings`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.clustersoperationsextensions.getgatewaysettings?view=azure-dotnet), use. 
- [`ConfigurationsOperationsExtensions.Update`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.update?view=azure-dotnet)Agora está preterido e foi substituído por [`ClusterOperationsExtensions.UpdateGatewaySettings`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.clustersoperationsextensions.updategatewaysettings?view=azure-dotnet). 
- [`ConfigurationsOperationsExtensions.EnableHttp`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.enablehttp?view=azure-dotnet)e [`DisableHttp`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.disablehttp?view=azure-dotnet) agora estão preteridos. O HTTP agora está sempre habilitado, portanto, esses métodos não são mais necessários.

### <a name="sdk-for-python"></a>SDK para Python

Atualize para a [versão 1.0.0](https://pypi.org/project/azure-mgmt-hdinsight/1.0.0/) ou posterior do SDK do HDInsight para Python. Modificações mínimas de código poderão ser necessárias se você estiver usando um método afetado por essas alterações:

- [`ConfigurationsOperations.get`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#get-resource-group-name--cluster-name--configuration-name--custom-headers-none--raw-false----operation-config-)**não retornará mais parâmetros confidenciais** , como chaves de armazenamento (site principal) ou credenciais http (gateway).
    - Para recuperar todas as configurações, incluindo parâmetros confidenciais, [`ConfigurationsOperations.list`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#list-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-) use o futuro.  Observe que os usuários com a função ' leitor ' não poderão usar esse método. Isso permite o controle granular sobre quais usuários podem acessar informações confidenciais para um cluster. 
    - Para recuperar apenas as credenciais de gateway HTTP [`ClusterOperations.get_gateway_settings`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clustersoperations#get-gateway-settings-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-), use.
- [`ConfigurationsOperations.update`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#update-resource-group-name--cluster-name--configuration-name--parameters--custom-headers-none--raw-false--polling-true----operation-config-)Agora está preterido e foi substituído por [`ClusterOperations.update_gateway_settings`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clustersoperations#update-gateway-settings-resource-group-name--cluster-name--parameters--custom-headers-none--raw-false--polling-true----operation-config-).

### <a name="sdk-for-java"></a>SDK para Java

Atualize para a [versão 1.0.0](https://search.maven.org/artifact/com.microsoft.azure.hdinsight.v2018_06_01_preview/azure-mgmt-hdinsight/) ou posterior do SDK do HDInsight para Java. Modificações mínimas de código poderão ser necessárias se você estiver usando um método afetado por essas alterações:

- [`ConfigurationsInner.get`](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018__06__01__preview.implementation._configurations_inner.get)**não retornará mais parâmetros confidenciais** , como chaves de armazenamento (site principal) ou credenciais http (gateway).
    - Para recuperar todas as configurações, incluindo parâmetros confidenciais, [`ConfigurationsInner.list`](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018_06_01_preview.implementation.configurationsinner.list?view=azure-java-stable) use o futuro.  Observe que os usuários com a função ' leitor ' não poderão usar esse método. Isso permite o controle granular sobre quais usuários podem acessar informações confidenciais para um cluster. 
    - Para recuperar apenas as credenciais de gateway HTTP [`ClustersInner.getGatewaySettings`](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018_06_01_preview.implementation.clustersinner.getgatewaysettings?view=azure-java-stable), use.
- [`ConfigurationsInner.update`](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018__06__01__preview.implementation._configurations_inner.update)Agora está preterido e foi substituído por [`ClustersInner.updateGatewaySettings`](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018_06_01_preview.implementation.clustersinner.updategatewaysettings?view=azure-java-stable).

### <a name="sdk-for-go"></a>SDK para go

Atualize para a [versão 27.1.0](https://github.com/Azure/azure-sdk-for-go/tree/master/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight) ou posterior do SDK do HDInsight para go. Modificações mínimas de código poderão ser necessárias se você estiver usando um método afetado por essas alterações:

- [`ConfigurationsClient.get`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.Get)**não retornará mais parâmetros confidenciais** , como chaves de armazenamento (site principal) ou credenciais http (gateway).
    - Para recuperar todas as configurações, incluindo parâmetros confidenciais, [`ConfigurationsClient.list`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.List) use o futuro.  Observe que os usuários com a função ' leitor ' não poderão usar esse método. Isso permite o controle granular sobre quais usuários podem acessar informações confidenciais para um cluster. 
    - Para recuperar apenas as credenciais de gateway HTTP [`ClustersClient.get_gateway_settings`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ClustersClient.GetGatewaySettings), use.
- [`ConfigurationsClient.update`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.Update)Agora está preterido e foi substituído por [`ClustersClient.update_gateway_settings`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ClustersClient.UpdateGatewaySettings).

### <a name="azhdinsight-powershell"></a>Az.HDInsight PowerShell
Atualize para [AZ PowerShell versão 2.0.0](https://www.powershellgallery.com/packages/Az) ou posterior para evitar interrupções.  Modificações mínimas de código poderão ser necessárias se você estiver usando um método afetado por essas alterações.
- `Grant-AzHDInsightHttpServicesAccess`Agora é preterido e foi substituído pelo novo `Set-AzHDInsightGatewayCredential` cmdlet.
- `Get-AzHDInsightJobOutput`foi atualizado para dar suporte ao acesso baseado em função granular para a chave de armazenamento.
    - Os usuários com as funções Operador do Cluster HDInsight, Colaborador ou Proprietário não serão afetados.
    - Os usuários com apenas a função leitor precisarão especificar o `DefaultStorageAccountKey` parâmetro explicitamente.
- `Revoke-AzHDInsightHttpServicesAccess`Agora está preterido. O HTTP agora está sempre habilitado, portanto, esse cmdlet não é mais necessário.
 Consulte [AZ. Guia de migração do HDInsight](https://github.com/Azure/azure-powershell/blob/master/documentation/migration-guides/Az.2.0.0-migration-guide.md#azhdinsight) para obter mais detalhes.

## <a name="add-the-hdinsight-cluster-operator-role-assignment-to-a-user"></a>Adicionar a atribuição de função de operador de cluster HDInsight a um usuário

Um usuário com a função [colaborador](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) ou [proprietário](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) pode atribuir a função de [operador de cluster hdinsight](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#hdinsight-cluster-operator) aos usuários que você deseja ter acesso de leitura/gravação a valores de configuração de clusters hdinsight confidenciais (como credenciais de gateway de cluster e chaves de conta de armazenamento).

### <a name="using-the-azure-cli"></a>Usando a CLI do Azure

A maneira mais simples de adicionar essa atribuição de função é usando o `az role assignment create` comando em CLI do Azure.

> [!NOTE]
> Esse comando deve ser executado por um usuário com as funções de colaborador ou proprietário, pois somente eles podem conceder essas permissões. O `--assignee` é o endereço de email do usuário ao qual você deseja atribuir a função de operador de cluster HDInsight.

#### <a name="grant-role-at-the-resource-cluster-level"></a>Conceder função no nível de recurso (cluster)

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee <user@domain.com> --scope /subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.HDInsight/clusters/<ClusterName>
```

#### <a name="grant-role-at-the-resource-group-level"></a>Conceder função no nível do grupo de recursos

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee user@domain.com -g <ResourceGroupName>
```

#### <a name="grant-role-at-the-subscription-level"></a>Conceder função no nível da assinatura

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee user@domain.com
```

### <a name="using-the-azure-portal"></a>Usando o portal do Azure

Como alternativa, você pode usar o portal do Azure para adicionar a atribuição de função de operador de cluster HDInsight a um usuário. Consulte a documentação, [gerenciar o acesso aos recursos do Azure usando o RBAC e a Portal do Azure-adicionar uma atribuição de função](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#add-a-role-assignment).
