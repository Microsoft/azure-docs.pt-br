---
title: Acesso baseado em função granular configurações de cluster do Azure HDInsight
description: Saiba mais sobre as alterações necessárias como parte da migração para o acesso baseado em função granular para configurações de cluster HDInsight.
author: tylerfox
ms.author: tyfox
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/20/2020
ms.openlocfilehash: a30768f4904c9e5be2edc020f12260cf3a54c889
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2021
ms.locfileid: "102425882"
---
# <a name="migrate-to-granular-role-based-access-for-cluster-configurations"></a>Migrar para acesso baseado em função granular para configurações de cluster

Estamos introduzindo algumas alterações importantes para dar suporte ao acesso mais refinado baseado em função para obter informações confidenciais. Como parte dessas alterações, algumas ações podem ser necessárias **até 3 de setembro de 2019** se você estiver usando uma das [entidades/cenários afetados](#am-i-affected-by-these-changes).

## <a name="what-is-changing"></a>O que está mudando?

Anteriormente, os segredos poderiam ser obtidos por meio da API do HDInsight por usuários de cluster que possuam as funções de proprietário, colaborador ou leitor [do Azure](../role-based-access-control/rbac-and-directory-admin-roles.md), pois estavam disponíveis para qualquer pessoa com a `*/read` permissão. Os segredos são definidos como valores que podem ser usados para obter acesso mais elevado do que a função de um usuário deve permitir. Isso inclui valores como credenciais HTTP de gateway de cluster, chaves de conta de armazenamento e credenciais de banco de dados.

A partir de 3 de setembro de 2019, o acesso a esses segredos exigirá a `Microsoft.HDInsight/clusters/configurations/action` permissão, o que significa que eles não podem mais ser acessados por usuários com a função leitor. As funções que têm essa permissão são colaborador, proprietário e a nova função de operador de cluster HDInsight (mais informações sobre isso abaixo).

Também estamos introduzindo uma nova função de [operador de cluster HDInsight](../role-based-access-control/built-in-roles.md#hdinsight-cluster-operator) que poderá recuperar segredos sem receber as permissões administrativas de colaborador ou proprietário. Para resumir:

| Função                                  | Anteriormente                                                                                        | Em frente       |
|---------------------------------------|--------------------------------------------------------------------------------------------------|-----------|
| Leitor                                | -Acesso de leitura, incluindo segredos.                                                                   | -Acesso de leitura, **excluindo** segredos |           |   |   |
| Operador de Cluster do HDInsight<br>(Nova função) | N/D                                                                                              | -Acesso de leitura/gravação, incluindo segredos         |   |   |
| Colaborador                           | -Acesso de leitura/gravação, incluindo segredos.<br>-Crie e gerencie todos os tipos de recursos do Azure.<br>-Executar ações de script.     | Nenhuma alteração |
| Proprietário                                 | -Acesso de leitura/gravação, incluindo segredos.<br>-Acesso completo a todos os recursos<br>-Delegar acesso a outras pessoas.<br>-Executar ações de script. | Nenhuma alteração |

Para obter informações sobre como adicionar a atribuição de função de operador de cluster HDInsight a um usuário para conceder acesso de leitura/gravação a segredos de cluster, consulte a seção abaixo, [Adicionar a atribuição de função de operador de cluster HDInsight a um usuário](#add-the-hdinsight-cluster-operator-role-assignment-to-a-user).

## <a name="am-i-affected-by-these-changes"></a>Sou afetado por essas alterações?

As seguintes entidades e cenários são afetados:

- [API](#api): usuários que usam `/configurations` os `/configurations/{configurationName}` pontos de extremidade ou.
- [Ferramentas do Azure HDInsight para Visual Studio Code](#azure-hdinsight-tools-for-visual-studio-code) versão 1.1.1 ou inferior.
- [Azure Toolkit for IntelliJ](#azure-toolkit-for-intellij) versão 3.20.0 ou inferior.
- [Ferramentas de Azure data Lake e Stream Analytics para Visual Studio abaixo da](#azure-data-lake-and-stream-analytics-tools-for-visual-studio) versão 2.3.9000.1.
- [Azure Toolkit for Eclipse](#azure-toolkit-for-eclipse) versão 3.15.0 ou inferior.
- [SDK para .NET](#sdk-for-net)
    - [versões 1. x ou 2. x](#versions-1x-and-2x): usuários que usam `GetClusterConfigurations` os `GetConnectivitySettings` métodos,, `ConfigureHttpSettings` `EnableHttp` ou `DisableHttp` da classe ConfigurationsOperationsExtensions.
    - [versões 3. x e up](#versions-3x-and-up): usuários que usam `Get` os `Update` métodos,, `EnableHttp` ou `DisableHttp` da `ConfigurationsOperationsExtensions` classe.
- [SDK para Python](#sdk-for-python): usuários que usam `get` os `update` métodos ou da `ConfigurationsOperations` classe.
- [SDK para Java](#sdk-for-java): usuários que usam `update` os `get` métodos ou da `ConfigurationsInner` classe.
- [SDK para go](#sdk-for-go): usuários que usam `Get` os `Update` métodos ou da `ConfigurationsClient` estrutura.
- [AZ. HDInsight PowerShell](#azhdinsight-powershell) abaixo da versão 2.0.0.
Consulte as seções abaixo (ou use os links acima) para ver as etapas de migração para seu cenário.

### <a name="api"></a>API

As seguintes APIs serão alteradas ou preteridas:

- [**Obter/Configurations/{ConfigurationName}**](/rest/api/hdinsight/hdinsight-cluster#get-configuration) (informações confidenciais removidas)
    - Usado anteriormente para obter tipos de configuração individuais (incluindo segredos).
    - A partir de 3 de setembro de 2019, essa chamada à API agora retornará tipos de configuração individuais com segredos omitidos. Para obter todas as configurações, incluindo segredos, use a nova chamada POST/Configurations. Para obter apenas as configurações de gateway, use a nova chamada POST/getGatewaySettings.
- [**Obter/Configurations**](/rest/api/hdinsight/hdinsight-cluster#get-configuration) (preterido)
    - Usado anteriormente para obter todas as configurações (incluindo segredos)
    - A partir de 3 de setembro de 2019, essa chamada à API será preterida e não terá mais suporte. Para obter todas as configurações no futuro, use a nova chamada POST/Configurations. Para obter configurações com parâmetros confidenciais omitidos, use a chamada GET/configurations/{configurationName}.
- [**Post/Configurations/{ConfigurationName}**](/rest/api/hdinsight/hdinsight-cluster#update-gateway-settings) (preterido)
    - Usado anteriormente para atualizar as credenciais do gateway.
    - A partir de 3 de setembro de 2019, essa chamada à API será preterida e não terá mais suporte. Em vez disso, use a nova POSTAgem/updateGatewaySettings.

As seguintes APIs de substituição foram adicionadas:</span>

- [**POSTAR/Configurations**](/rest/api/hdinsight/hdinsight-cluster#list-configurations)
    - Use essa API para obter todas as configurações, incluindo segredos.
- [**POSTAR/getGatewaySettings**](/rest/api/hdinsight/hdinsight-cluster#get-gateway-settings)
    - Use essa API para obter as configurações de gateway.
- [**POSTAR/updateGatewaySettings**](/rest/api/hdinsight/hdinsight-cluster#update-gateway-settings)
    - Use essa API para atualizar as configurações do gateway (nome de usuário e/ou senha).

### <a name="azure-hdinsight-tools-for-visual-studio-code"></a>Ferramentas do Azure HDInsight para Visual Studio Code

Se você estiver usando a versão 1.1.1 ou inferior, atualize para a [versão mais recente das ferramentas do Azure HDInsight para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=mshdinsight.azure-hdinsight&ssr=false) para evitar interrupções.

### <a name="azure-toolkit-for-intellij"></a>Kit de Ferramentas do Azure para IntelliJ

Se você estiver usando a versão 3.20.0 ou inferior, atualize para a [versão mais recente do plug-in Azure Toolkit for IntelliJ](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij) para evitar interrupções.

### <a name="azure-data-lake-and-stream-analytics-tools-for-visual-studio"></a>Ferramentas de Azure Data Lake e Stream Analytics para Visual Studio

Atualize para a versão 2.3.9000.1 ou posterior das [ferramentas Azure data Lake e Stream Analytics para Visual Studio](https://marketplace.visualstudio.com/items?itemName=ADLTools.AzureDataLakeandStreamAnalyticsTools&ssr=false#overview) para evitar interrupções.  Para obter ajuda com a atualização, consulte nossa documentação, [Update data Lake Tools for Visual Studio](./hadoop/apache-hadoop-visual-studio-tools-get-started.md#update-data-lake-tools-for-visual-studio).

### <a name="azure-toolkit-for-eclipse"></a>Kit de ferramentas do Azure para Eclipse

Se você estiver usando a versão 3.15.0 ou inferior, atualize para a [versão mais recente do Azure Toolkit for Eclipse](https://marketplace.eclipse.org/content/azure-toolkit-eclipse) para evitar interrupções.

### <a name="sdk-for-net"></a>SDK para .NET

#### <a name="versions-1x-and-2x"></a>Versões 1. x e 2. x

Atualize para a [versão 2.1.0](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/2.1.0) do SDK do HDInsight para .net. Modificações mínimas de código poderão ser necessárias se você estiver usando um método afetado por essas alterações:

- `ClusterOperationsExtensions.GetClusterConfigurations`**não retornará mais parâmetros confidenciais** , como chaves de armazenamento (site principal) ou credenciais http (gateway).
    - Para recuperar todas as configurações, incluindo parâmetros confidenciais, use o `ClusterOperationsExtensions.ListConfigurations` futuro.  Observe que os usuários com a função ' leitor ' não poderão usar esse método. Isso permite o controle granular sobre quais usuários podem acessar informações confidenciais para um cluster.
    - Para recuperar apenas as credenciais de gateway HTTP, use `ClusterOperationsExtensions.GetGatewaySettings` .

- `ClusterOperationsExtensions.GetConnectivitySettings` Agora está preterido e foi substituído por `ClusterOperationsExtensions.GetGatewaySettings` .

- `ClusterOperationsExtensions.ConfigureHttpSettings` Agora está preterido e foi substituído por `ClusterOperationsExtensions.UpdateGatewaySettings` .

- `ConfigurationsOperationsExtensions.EnableHttp` e `DisableHttp` agora estão preteridos. O HTTP agora está sempre habilitado, portanto, esses métodos não são mais necessários.

#### <a name="versions-3x-and-up"></a>Versões 3. x e superior

Atualize para a [versão 5.0.0](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/5.0.0) ou posterior do SDK do HDInsight para .net. Modificações mínimas de código poderão ser necessárias se você estiver usando um método afetado por essas alterações:

- [`ConfigurationOperationsExtensions.Get`](/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.get)**não retornará mais parâmetros confidenciais** , como chaves de armazenamento (site principal) ou credenciais http (gateway).
    - Para recuperar todas as configurações, incluindo parâmetros confidenciais, use o [`ConfigurationOperationsExtensions.List`](/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.list) futuro.Observe que os usuários com a função ' leitor ' não poderão usar esse método. Isso permite o controle granular sobre quais usuários podem acessar informações confidenciais para um cluster. 
    - Para recuperar apenas as credenciais de gateway HTTP, use [`ClusterOperationsExtensions.GetGatewaySettings`](/dotnet/api/microsoft.azure.management.hdinsight.clustersoperationsextensions.getgatewaysettings) . 
- [`ConfigurationsOperationsExtensions.Update`](/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.update) Agora está preterido e foi substituído por [`ClusterOperationsExtensions.UpdateGatewaySettings`](/dotnet/api/microsoft.azure.management.hdinsight.clustersoperationsextensions.updategatewaysettings) . 
- [`ConfigurationsOperationsExtensions.EnableHttp`](/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.enablehttp) e [`DisableHttp`](/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.disablehttp) agora estão preteridos. O HTTP agora está sempre habilitado, portanto, esses métodos não são mais necessários.

### <a name="sdk-for-python"></a>SDK para Python

Atualize para a [versão 1.0.0](https://pypi.org/project/azure-mgmt-hdinsight/1.0.0/) ou posterior do SDK do HDInsight para Python. Modificações mínimas de código poderão ser necessárias se você estiver usando um método afetado por essas alterações:

- [`ConfigurationsOperations.get`](/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#get-resource-group-name--cluster-name--configuration-name--custom-headers-none--raw-false----operation-config-)**não retornará mais parâmetros confidenciais** , como chaves de armazenamento (site principal) ou credenciais http (gateway).
    - Para recuperar todas as configurações, incluindo parâmetros confidenciais, use o [`ConfigurationsOperations.list`](/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#list-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-) futuro.Observe que os usuários com a função ' leitor ' não poderão usar esse método. Isso permite o controle granular sobre quais usuários podem acessar informações confidenciais para um cluster. 
    - Para recuperar apenas as credenciais de gateway HTTP, use [`ClusterOperations.get_gateway_settings`](/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clustersoperations#get-gateway-settings-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-) .
- [`ConfigurationsOperations.update`](/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#update-resource-group-name--cluster-name--configuration-name--parameters--custom-headers-none--raw-false--polling-true----operation-config-) Agora está preterido e foi substituído por [`ClusterOperations.update_gateway_settings`](/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clustersoperations#update-gateway-settings-resource-group-name--cluster-name--parameters--custom-headers-none--raw-false--polling-true----operation-config-) .

### <a name="sdk-for-java"></a>SDK para Java

Atualize para a [versão 1.0.0](https://search.maven.org/artifact/com.microsoft.azure.hdinsight.v2018_06_01_preview/azure-mgmt-hdinsight/1.0.0/jar) ou posterior do SDK do HDInsight para Java. Modificações mínimas de código poderão ser necessárias se você estiver usando um método afetado por essas alterações:

- `ConfigurationsInner.get`**não retornará mais parâmetros confidenciais** , como chaves de armazenamento (site principal) ou credenciais http (gateway).
- `ConfigurationsInner.update` Agora está preterido.

### <a name="sdk-for-go"></a>SDK para go

Atualize para a [versão 27.1.0](https://github.com/Azure/azure-sdk-for-go/tree/master/services/preview/hdinsight/mgmt/2015-03-01-preview/hdinsight) ou posterior do SDK do HDInsight para go. Modificações mínimas de código poderão ser necessárias se você estiver usando um método afetado por essas alterações:

- [`ConfigurationsClient.get`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2015-03-01-preview/hdinsight#ConfigurationsClient.Get)**não retornará mais parâmetros confidenciais** , como chaves de armazenamento (site principal) ou credenciais http (gateway).
    - Para recuperar todas as configurações, incluindo parâmetros confidenciais, use o [`ConfigurationsClient.list`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2015-03-01-preview/hdinsight#ConfigurationsClient.List) futuro.Observe que os usuários com a função ' leitor ' não poderão usar esse método. Isso permite o controle granular sobre quais usuários podem acessar informações confidenciais para um cluster. 
    - Para recuperar apenas as credenciais de gateway HTTP, use [`ClustersClient.get_gateway_settings`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2015-03-01-preview/hdinsight#ClustersClient.GetGatewaySettings) .
- [`ConfigurationsClient.update`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2015-03-01-preview/hdinsight#ConfigurationsClient.Update) Agora está preterido e foi substituído por [`ClustersClient.update_gateway_settings`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2015-03-01-preview/hdinsight#ClustersClient.UpdateGatewaySettings) .

### <a name="azhdinsight-powershell"></a>AZ. HDInsight PowerShell
Atualize para [AZ PowerShell versão 2.0.0](https://www.powershellgallery.com/packages/Az) ou posterior para evitar interrupções.  Modificações mínimas de código poderão ser necessárias se você estiver usando um método afetado por essas alterações.
- `Grant-AzHDInsightHttpServicesAccess` Agora é preterido e foi substituído pelo novo `Set-AzHDInsightGatewayCredential` cmdlet.
- `Get-AzHDInsightJobOutput` foi atualizado para dar suporte ao acesso baseado em função granular para a chave de armazenamento.
    - Os usuários com as funções Operador do Cluster HDInsight, Colaborador ou Proprietário não serão afetados.
    - Os usuários com apenas a função leitor precisarão especificar o `DefaultStorageAccountKey` parâmetro explicitamente.
- `Revoke-AzHDInsightHttpServicesAccess` Agora está preterido. O HTTP agora está sempre habilitado, portanto, esse cmdlet não é mais necessário.
 Consulte [AZ. Guia de migração do HDInsight](https://github.com/Azure/azure-powershell/blob/master/documentation/migration-guides/Az.2.0.0-migration-guide.md#azhdinsight) para obter mais detalhes.

## <a name="add-the-hdinsight-cluster-operator-role-assignment-to-a-user"></a>Adicionar a atribuição de função de operador de cluster HDInsight a um usuário

Um usuário com a função de [proprietário](../role-based-access-control/built-in-roles.md#owner) pode atribuir a função de [operador de cluster hdinsight](../role-based-access-control/built-in-roles.md#hdinsight-cluster-operator) aos usuários que você deseja ter acesso de leitura/gravação a valores de configuração de clusters hdinsight confidenciais (como credenciais de gateway de cluster e chaves de conta de armazenamento).

### <a name="using-the-azure-cli"></a>Usando a CLI do Azure

A maneira mais simples de adicionar essa atribuição de função é usando o `az role assignment create` comando em CLI do Azure.

> [!NOTE]
> Esse comando deve ser executado por um usuário com a função de proprietário, pois apenas eles podem conceder essas permissões. O `--assignee` é o nome da entidade de serviço ou endereço de email do usuário ao qual você deseja atribuir a função de operador do cluster HDInsight. Se você receber um erro de permissões insuficientes, consulte as perguntas frequentes abaixo.

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

Como alternativa, você pode usar o portal do Azure para adicionar a atribuição de função de operador de cluster HDInsight a um usuário. Consulte a documentação, [atribuir funções do Azure usando o portal do Azure](../role-based-access-control/role-assignments-portal.md).

## <a name="faq"></a>Perguntas frequentes

### <a name="why-am-i-seeing-a-403-forbidden-response-after-updating-my-api-requests-andor-tool"></a>Por que estou vendo uma resposta 403 (proibido) depois de atualizar minhas solicitações de API e/ou ferramenta?

As configurações de cluster agora estão por trás do controle de acesso baseado em função granular e exigem a `Microsoft.HDInsight/clusters/configurations/*` permissão para acessá-las. Para obter essa permissão, atribua a função de proprietário, colaborador ou operador do cluster HDInsight ao usuário ou à entidade de serviço que está tentando acessar as configurações.

### <a name="why-do-i-see-insufficient-privileges-to-complete-the-operation-when-running-the-azure-cli-command-to-assign-the-hdinsight-cluster-operator-role-to-another-user-or-service-principal"></a>Por que vejo "privilégios insuficientes para concluir a operação" ao executar o comando CLI do Azure para atribuir a função de operador do cluster HDInsight a outro usuário ou entidade de serviço?

Além de ter a função proprietário, o usuário ou a entidade de serviço que executa o comando precisa ter permissões suficientes do Azure AD para pesquisar as IDs de objeto do destinatário. Essa mensagem indica permissões insuficientes do Azure AD. Tente substituir o `-–assignee` argumento por `–assignee-object-id` e forneça a ID de objeto do atribuído como o parâmetro em vez do nome (ou a ID da entidade de segurança no caso de uma identidade gerenciada). Consulte a seção parâmetros opcionais da [atribuição de função AZ Create Documentation](/cli/azure/role/assignment#az-role-assignment-create) para obter mais informações.

Se isso ainda não funcionar, entre em contato com o administrador do Azure AD para adquirir as permissões corretas.

### <a name="what-will-happen-if-i-take-no-action"></a>O que acontecerá se eu não executar nenhuma ação?

A partir de 3 de setembro de 2019, `GET /configurations` e `POST /configurations/gateway` as chamadas não retornarão mais nenhuma informação e a `GET /configurations/{configurationName}` chamada não retornará mais parâmetros confidenciais, como chaves de conta de armazenamento ou a senha do cluster. O mesmo acontece com os métodos do SDK e os cmdlets do PowerShell correspondentes.

Se você estiver usando uma versão mais antiga de uma das ferramentas para Visual Studio, VSCode, IntelliJ ou Eclipse mencionado acima, elas não funcionarão mais até que você atualize o.

Para obter informações mais detalhadas, consulte a seção correspondente deste documento para seu cenário.