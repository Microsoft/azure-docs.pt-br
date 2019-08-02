---
title: Gerenciar recursos da conta com a biblioteca de cliente para .NET - Lote do Azure | Microsoft Docs
description: Criar, excluir e modificar recursos de conta do Lote do Azure em seus aplicativos com a biblioteca .NET de Gerenciamento do Lote.
services: batch
documentationcenter: .net
author: laurenhughes
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 16279b23-60ff-4b16-b308-5de000e4c028
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/24/2017
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: 41f3eecb1b3f488c355b1bad65b90dae8c76126e
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68323447"
---
# <a name="manage-batch-accounts-and-quotas-with-the-batch-management-client-library-for-net"></a>Gerenciar contas e cotas do Lote com a biblioteca de clientes do Gerenciamento de Lotes para .NET

> [!div class="op_single_selector"]
> * [Portal do Azure](batch-account-create-portal.md)
> * [.NET de Gerenciamento do Lote](batch-management-dotnet.md)
> 
> 

Você pode reduzir a sobrecarga de manutenção em seus aplicativos do lote do Azure usando a biblioteca [.net de gerenciamento do lote][api_mgmt_net] para automatizar a criação, a exclusão, o gerenciamento de chaves e a descoberta de cotas do lote.

* **Criar e excluir contas do Lote** em qualquer região. Se como um ISV (fornecedor independente de software), por exemplo, você fornece um serviço para os clientes no qual cada um é atribuído a uma conta do Lote separada para fins de cobrança, pode adicionar recursos de criação e exclusão de conta no portal do cliente.
* **Recuperar e regenerar chaves de conta** programaticamente para qualquer uma de suas contas do Lote. Isso pode ajudá-lo a atender às políticas de segurança que impõem substituição periódica ou expiração de chaves de conta. Quando você tiver várias contas do Lote em várias regiões do Azure, a automação desse processo de substituição aumentará a eficiência da solução.
* **Verificar cotas de conta** e eliminar as suposições de tentativa e erro na determinação dos limites de cada conta do Lote. Ao verificar suas cotas de conta antes de iniciar trabalhos, de criar pools ou de adicionar nós de computação, você poderá ajustar proativamente quando ou onde esses recursos de computação serão criados. Você pode determinar quais contas exigem aumento de cota antes da alocação de recursos adicionais a elas.
* **Combine recursos de outros serviços do Azure** para uma experiência de gerenciamento com recursos completos, usando o .net de gerenciamento do lote, [Azure Active Directory][aad_about] , and the [Azure Resource Manager][resman_overview] juntos no mesmo aplicativo. Usando esses recursos e suas APIs, você pode fornecer uma experiência de autenticação, a capacidade de criação e exclusão de grupos de recursos ininterrupta, além dos recursos descritos acima para uma solução de gerenciamento de ponta a ponta.

> [!NOTE]
> Embora este artigo se concentre no gerenciamento programático de suas contas, chaves e cotas do lote, você pode executar muitas dessas atividades usando o [portal do Azure][azure_portal]. Confira [Criar uma conta do Lote do Azure no Portal do Azure](batch-account-create-portal.md) e [Cotas e limites do serviço do Lote do Azure](batch-quota-limit.md) para saber mais.
> 
> 

## <a name="create-and-delete-batch-accounts"></a>Criar e excluir contas do Lote
Como mencionado, um dos principais recursos da API de Gerenciamento do Lote é criar e excluir contas do Lote em uma região do Azure. Para fazer isso, use [BatchManagementClient. Account. createasync][net_create] and [DeleteAsync][net_delete]ou suas contrapartes síncronas.

O snippet de código a seguir cria uma conta, obtém a conta recém-criada do serviço de Lote, então, a exclui. Neste trecho de código e os outros neste artigo, `batchManagementClient` há uma instância totalmente inicializada do [BatchManagementClient][net_mgmt_client].

```csharp
// Create a new Batch account
await batchManagementClient.Account.CreateAsync("MyResourceGroup",
    "mynewaccount",
    new BatchAccountCreateParameters() { Location = "West US" });

// Get the new account from the Batch service
AccountResource account = await batchManagementClient.Account.GetAsync(
    "MyResourceGroup",
    "mynewaccount");

// Delete the account
await batchManagementClient.Account.DeleteAsync("MyResourceGroup", account.Name);
```

> [!NOTE]
> Os aplicativos que usam a biblioteca do .NET de Gerenciamento do Lote e sua classe BatchManagementClient exigem o acesso de **administrador de serviços** ou de **coadministrador** à assinatura que possui a conta do Lote a ser gerenciada. Para obter mais informações, consulte a seção Azure Active Directory e o exemplo de código [AccountManagement][acct_mgmt_sample] .
> 
> 

## <a name="retrieve-and-regenerate-account-keys"></a>Recuperar e regenerar chaves de conta
Obtenha chaves de conta primárias e secundárias de qualquer conta do lote em sua assinatura usando o [ListKeysAsync][net_list_keys]. You can regenerate those keys by using [RegenerateKeyAsync][net_regenerate_keys].

```csharp
// Get and print the primary and secondary keys
BatchAccountListKeyResult accountKeys =
    await batchManagementClient.Account.ListKeysAsync(
        "MyResourceGroup",
        "mybatchaccount");
Console.WriteLine("Primary key:   {0}", accountKeys.Primary);
Console.WriteLine("Secondary key: {0}", accountKeys.Secondary);

// Regenerate the primary key
BatchAccountRegenerateKeyResponse newKeys =
    await batchManagementClient.Account.RegenerateKeyAsync(
        "MyResourceGroup",
        "mybatchaccount",
        new BatchAccountRegenerateKeyParameters() {
            KeyName = AccountKeyType.Primary
            });
```

> [!TIP]
> Você pode criar um fluxo de trabalho simplificado de conexão para os aplicativos de gerenciamento. Primeiro, obtenha uma chave de conta para a conta do lote que você deseja gerenciar com a classe [ListKeysAsync][net_list_keys] . Then, use this key when initializing the Batch .NET library's [BatchSharedKeyCredentials][net_sharedkeycred] , que é usada ao inicializar [BatchClient][net_batch_client].
> 
> 

## <a name="check-azure-subscription-and-batch-account-quotas"></a>Verificar a assinatura e cotas da conta do Lote do Azure
As assinaturas do Azure e os serviços Azure individuais, como o Lote, têm cotas padrão limitando o número de determinadas entidades neles. Para obter as cotas padrão das assinaturas do Azure, veja [Assinatura do Azure e limites de serviço, cotas e restrições](../azure-subscription-service-limits.md). Para obter as cotas padrão do serviço do Lote, veja [Cotas e limites para o serviço do Lote do Azure](batch-quota-limit.md). Usando a biblioteca .NET de Gerenciamento de Lotes, você pode verificar essas cotas em seus aplicativos. Isso permite que você tome decisões de alocação antes de adicionar contas ou recursos de computação, como pools e nós de computação.

### <a name="check-an-azure-subscription-for-batch-account-quotas"></a>Verificar uma assinatura do Azure para cotas de conta do Lote
Antes de criar uma conta do Lote em uma região, verifique a sua assinatura do Azure para ver se é possível adicionar uma conta nessa região.

No trecho de código abaixo, usamos primeiro [BatchManagementClient. Account. ListAsync][net_mgmt_listaccounts] to get a collection of all Batch accounts that are within a subscription. Once we've obtained this collection, we determine how many accounts are in the target region. Then we use [BatchManagementClient.Subscriptions][net_mgmt_subscriptions] para obter a cota da conta do lote e determinar quantas contas (se houver) podem ser criadas nessa região.

```csharp
// Get a collection of all Batch accounts within the subscription
BatchAccountListResponse listResponse =
        await batchManagementClient.Account.ListAsync(new AccountListParameters());
IList<AccountResource> accounts = listResponse.Accounts;
Console.WriteLine("Total number of Batch accounts under subscription id {0}:  {1}",
    creds.SubscriptionId,
    accounts.Count);

// Get a count of all accounts within the target region
string region = "westus";
int accountsInRegion = accounts.Count(o => o.Location == region);

// Get the account quota for the specified region
SubscriptionQuotasGetResponse quotaResponse = await batchManagementClient.Subscriptions.GetSubscriptionQuotasAsync(region);
Console.WriteLine("Account quota for {0} region: {1}", region, quotaResponse.AccountQuota);

// Determine how many accounts can be created in the target region
Console.WriteLine("Accounts in {0}: {1}", region, accountsInRegion);
Console.WriteLine("You can create {0} accounts in the {1} region.", quotaResponse.AccountQuota - accountsInRegion, region);
```

No trecho acima, `creds` é uma instância do exemplo de código [TokenCloudCredentials][azure_tokencreds] . To see an example of creating this object, see the [AccountManagement][acct_mgmt_sample] no github.

### <a name="check-a-batch-account-for-compute-resource-quotas"></a>Verificar as cotas de recursos de computação na conta do Lote
Antes de aumentar os recursos de computação em sua solução do Lote, você poderá garantir que os recursos que deseja alocar não excedam as cotas da conta. No snippet de código abaixo, imprimimos as informações de cota para a conta do Lote chamada `mybatchaccount`. Em seu próprio aplicativo, você pode usar essas informações para determinar se a conta pode lidar com os recursos adicionais a serem criados.

```csharp
// First obtain the Batch account
BatchAccountGetResponse getResponse =
    await batchManagementClient.Account.GetAsync("MyResourceGroup", "mybatchaccount");
AccountResource account = getResponse.Resource;

// Now print the compute resource quotas for the account
Console.WriteLine("Core quota: {0}", account.Properties.CoreQuota);
Console.WriteLine("Pool quota: {0}", account.Properties.PoolQuota);
Console.WriteLine("Active job and job schedule quota: {0}", account.Properties.ActiveJobAndJobScheduleQuota);
```

> [!IMPORTANT]
> Embora haja cotas padrão para assinaturas e serviços do Azure, muitos desses limites podem ser gerados emitindo uma solicitação na [portal do Azure][azure_portal]. Por exemplo, veja [Cotas e limites para o serviço do Lote do Azure](batch-quota-limit.md) para obter instruções sobre como aumentar suas cotas da conta do Lote.
> 
> 

## <a name="use-azure-ad-with-batch-management-net"></a>Usar o Azure AD com o .NET de Gerenciamento de Lote

A biblioteca .NET de gerenciamento do lote é um cliente do provedor de recursos do Azure e é usada junto com [Azure Resource Manager][resman_overview] para gerenciar recursos de conta programaticamente. O Azure AD é necessário para autenticar solicitações feitas por meio de qualquer cliente do provedor de recursos do Azure, incluindo a biblioteca .NET de gerenciamento do lote e por meio de [Azure Resource Manager][resman_overview]. Para obter informações sobre como usar o Azure AD com a biblioteca do .NET de Gerenciamento de Lote, consulte [Usar o Azure Active Directory para autenticar soluções em Lote](batch-aad-auth.md). 

## <a name="sample-project-on-github"></a>Projeto de exemplo no GitHub

Para ver o .NET de gerenciamento do lote em ação, confira o projeto de exemplo [AccountManagement][acct_mgmt_sample] no github. O aplicativo de exemplo AccountManagment demonstra as operações a seguir:

1. Adquirir um token de segurança do Azure AD usando a [Adal][aad_adal]. Se o usuário ainda não estiver conectado, será solicitado que ele forneça suas credenciais do Azure.
2. Com o token de segurança obtido do Azure AD, crie um [SubscriptionClient][resman_subclient] para consultar o Azure para obter uma lista de assinaturas associadas à conta. O usuário poderá selecionar uma assinatura na lista se ela contiver mais de uma assinatura.
3. Associe as credenciais à assinatura selecionada.
4. Crie um objeto [ResourceManagementClient][resman_client] usando as credenciais.
5. Use um objeto [ResourceManagementClient][resman_client] para criar um grupo de recursos.
6. Use um objeto [BatchManagementClient][net_mgmt_client] para executar várias operações de conta do lote:
   * Crie uma conta do Lote no novo grupo de recursos.
   * Obtenha a conta recém-criada no serviço do Lote.
   * Imprima as chaves de conta da nova conta.
   * Regenere uma nova chave primária para a conta.
   * Imprima as informações de cota para a conta.
   * Imprima as informações de cota para a assinatura.
   * Imprima todas as contas na assinatura.
   * Exclua a conta recém-criada.
7. Exclua o grupo de recursos.

Antes de excluir a conta e o grupo de recursos do lote recém-criado, você pode exibi-las no [portal do Azure][azure_portal]:

Para executar o aplicativo de exemplo com êxito, primeiro é necessário registrá-lo no locatário do Azure AD no portal do Azure e conceder permissões à API do Azure Resource Manager. Siga as etapas fornecidas em [Autenticar soluções de gerenciamento do lote com o Active Directory](batch-aad-auth-management.md).


[aad_about]:../active-directory/fundamentals/active-directory-whatis.md "O que é o Azure Active Directory?"
[aad_adal]: ../active-directory/active-directory-authentication-libraries.md
[aad_auth_scenarios]:../active-directory/develop/authentication-scenarios.md "Cenários de autenticação do Azure AD"
[aad_integrate]:../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md "Integrando aplicativos com o Azure Active Directory"
[acct_mgmt_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/AccountManagement
[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_mgmt_net]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[azure_portal]: https://portal.azure.com
[azure_storage]: https://azure.microsoft.com/services/storage/
[azure_tokencreds]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.tokencloudcredentials.aspx
[batch_explorer_project]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[net_batch_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_list_keys]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.listkeysasync.aspx
[net_create]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.createasync.aspx
[net_delete]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.deleteasync.aspx
[net_regenerate_keys]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.regeneratekeyasync.aspx
[net_sharedkeycred]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.auth.batchsharedkeycredentials.aspx
[net_mgmt_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.batchmanagementclient.aspx
[net_mgmt_subscriptions]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.batchmanagementclient.subscriptions.aspx
[net_mgmt_listaccounts]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.iaccountoperations.listasync.aspx
[resman_api]: https://msdn.microsoft.com/library/azure/mt418626.aspx
[resman_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.resourcemanagementclient.aspx
[resman_subclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.subscriptions.subscriptionclient.aspx
[resman_overview]: ../azure-resource-manager/resource-group-overview.md

[1]: ./media/batch-management-dotnet/portal-01.png
[2]: ./media/batch-management-dotnet/portal-02.png
[3]: ./media/batch-management-dotnet/portal-03.png
