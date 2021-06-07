---
title: Usar a biblioteca .NET de Gerenciamento do Lotes para gerenciar os recursos da conta
description: Criar, excluir e modificar recursos de conta do Lote do Azure em seus aplicativos com a biblioteca .NET de Gerenciamento do Lote.
ms.topic: how-to
ms.date: 01/26/2021
ms.custom: seodec18, has-adal-ref, devx-track-csharp
ms.openlocfilehash: f6acf23742b8d4c5c31a5ea952aba5c76b64cae0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98896724"
---
# <a name="manage-batch-accounts-and-quotas-with-the-batch-management-client-library-for-net"></a>Gerenciar contas e cotas do Lote com a biblioteca de clientes do Gerenciamento de Lotes para .NET

É possível diminuir a sobrecarga de manutenção em seus aplicativos do Lote do Azure usando a biblioteca do [Batch Management .NET](/dotnet/api/overview/azure/batch) para automatizar a criação, a exclusão, o gerenciamento de chaves e a descoberta de cotas da conta do Lote.

- **Criar e excluir contas do Lote** em qualquer região. Se como um ISV (fornecedor independente de software), por exemplo, você fornece um serviço para os clientes no qual cada um é atribuído a uma conta do Lote separada para fins de cobrança, pode adicionar recursos de criação e exclusão de conta no portal do cliente.
- **Recuperar e regenerar chaves de conta** programaticamente para qualquer uma de suas contas do Lote. Isso pode ajudá-lo a atender às políticas de segurança que impõem substituição periódica ou expiração de chaves de conta. Quando você tiver várias contas do Lote em várias regiões do Azure, a automação desse processo de substituição aumentará a eficiência da solução.
- **Verificar cotas de conta** e eliminar as suposições de tentativa e erro na determinação dos limites de cada conta do Lote. Ao verificar suas cotas de conta antes de iniciar trabalhos, de criar pools ou de adicionar nós de computação, você poderá ajustar proativamente quando ou onde esses recursos de computação serão criados. Você pode determinar quais contas exigem aumento de cota antes da alocação de recursos adicionais a elas.
- **Combine recursos de outros serviços do Azure** para uma experiência de gerenciamento completa usando o .net de gerenciamento do lote, [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md)e o [Azure Resource Manager](../azure-resource-manager/management/overview.md) juntos no mesmo aplicativo. Usando esses recursos e suas APIs, você pode fornecer uma experiência de autenticação, a capacidade de criação e exclusão de grupos de recursos ininterrupta, além dos recursos descritos acima para uma solução de gerenciamento de ponta a ponta.

> [!NOTE]
> Embora este artigo se concentre no gerenciamento programático de suas contas, chaves e cotas do lote, você também pode executar muitas dessas atividades [usando o portal do Azure](batch-account-create-portal.md).

## <a name="create-and-delete-batch-accounts"></a>Criar e excluir contas do Lote

Um dos principais recursos da API de gerenciamento do lote é criar e excluir [contas do lote](accounts.md) em uma região do Azure. Para fazer isso, use [BatchManagementClient.Account.CreateAsync](/dotnet/api/microsoft.azure.management.batch.batchaccountoperationsextensions.createasync) e [DeleteAsync](/dotnet/api/microsoft.azure.management.batch.batchaccountoperationsextensions.deleteasync) ou suas correspondentes síncronas.

O snippet de código a seguir cria uma conta, obtém a conta recém-criada do serviço de Lote, então, a exclui. Neste e em outros trechos deste artigo, `batchManagementClient` é uma instância completamente inicializada de [BatchManagementClient](/dotnet/api/microsoft.azure.management.batch.batchmanagementclient).

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
> Os aplicativos que usam a biblioteca do .NET de Gerenciamento do Lote e sua classe BatchManagementClient exigem o acesso de administrador de serviços ou de coadministrador à assinatura que possui a conta do Lote a ser gerenciada. Para saber mais, confira a seção Azure Active Directory abaixo e o exemplo de código [AccountManagement](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp/AccountManagement).

## <a name="retrieve-and-regenerate-account-keys"></a>Recuperar e regenerar chaves de conta

Obtenha chaves de conta primárias e secundárias de qualquer conta do lote em sua assinatura usando o [GetKeysAsync](/dotnet/api/microsoft.azure.management.batch.batchaccountoperationsextensions.getkeysasync). É possível gerar essas chaves novamente usando [RegenerateKeyAsync](/dotnet/api/microsoft.azure.management.batch.batchaccountoperationsextensions.regeneratekeyasync).

```csharp
// Get and print the primary and secondary keys
BatchAccountGetKeyResult accountKeys =
    await batchManagementClient.Account.GetKeysAsync(
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
> Você pode criar um fluxo de trabalho simplificado de conexão para os aplicativos de gerenciamento. Primeiro, obtenha uma chave de conta para a conta do lote que você deseja gerenciar com o [GetKeysAsync](/dotnet/api/microsoft.azure.management.batch.batchaccountoperationsextensions.getkeysasync). Em seguida, use essa chave ao inicializar a classe [BatchSharedKeyCredentials](/dotnet/api/microsoft.azure.batch.auth.batchsharedkeycredentials) da biblioteca .NET do Lote, que é usada ao inicializar [BatchClient](/dotnet/api/microsoft.azure.batch.batchclient).

## <a name="check-azure-subscription-and-batch-account-quotas"></a>Verificar a assinatura e cotas da conta do Lote do Azure

As assinaturas do Azure e os serviços Azure individuais, como o Lote, têm cotas padrão limitando o número de determinadas entidades neles. Para obter as cotas padrão das assinaturas do Azure, veja [Assinatura do Azure e limites de serviço, cotas e restrições](../azure-resource-manager/management/azure-subscription-service-limits.md). Para obter as cotas padrão do serviço do Lote, veja [Cotas e limites para o serviço do Lote do Azure](batch-quota-limit.md). Usando a biblioteca .NET de Gerenciamento de Lotes, você pode verificar essas cotas em seus aplicativos. Isso permite que você tome decisões de alocação antes de adicionar contas ou recursos de computação, como pools e nós de computação.

### <a name="check-an-azure-subscription-for-batch-account-quotas"></a>Verificar uma assinatura do Azure para cotas de conta do Lote

Antes de criar uma conta do Lote em uma região, verifique a sua assinatura do Azure para ver se é possível adicionar uma conta nessa região.

No trecho de código abaixo, usamos primeiro **ListAsync** para obter uma coleção de todas as contas do lote que estão dentro de uma assinatura. Depois que obtivemos essa coleção, podemos determinar quantas contas estão na região de destino. Em seguida, usamos **GetQuotasAsync** para obter a cota da conta do lote e determinar quantas contas (se houver) podem ser criadas nessa região.

```csharp
// Get a collection of all Batch accounts within the subscription
BatchAccountListResponse listResponse =
        await batchManagementClient.BatchAccount.ListAsync(new AccountListParameters());
IList<AccountResource> accounts = listResponse.Accounts;
Console.WriteLine("Total number of Batch accounts under subscription id {0}:  {1}",
    creds.SubscriptionId,
    accounts.Count);

// Get a count of all accounts within the target region
string region = "westus";
int accountsInRegion = accounts.Count(o => o.Location == region);

// Get the account quota for the specified region
SubscriptionQuotasGetResponse quotaResponse = await batchManagementClient.Location.GetQuotasAsync(region);
Console.WriteLine("Account quota for {0} region: {1}", region, quotaResponse.AccountQuota);

// Determine how many accounts can be created in the target region
Console.WriteLine("Accounts in {0}: {1}", region, accountsInRegion);
Console.WriteLine("You can create {0} accounts in the {1} region.", quotaResponse.AccountQuota - accountsInRegion, region);
```

No trecho acima, `creds` é uma instância de **TokenCredentials**. Para ver um exemplo de como criar esse objeto, confira o exemplo de código [AccountManagement](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp/AccountManagement) no GitHub.

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
> Embora haja cotas padrão para assinaturas e serviços do Azure, muitos desses limites podem ser gerados por meio da [solicitação de um aumento de cota no portal do Azure](batch-quota-limit.md#increase-a-quota).

## <a name="use-azure-ad-with-batch-management-net"></a>Usar o Azure AD com o .NET de Gerenciamento de Lote

A biblioteca do .NET de Gerenciamento de Lote é um cliente de provedor de recursos do Azure e é usada junto com o [Azure Resource Manager](../azure-resource-manager/management/overview.md) para gerenciar recursos de conta de forma programática. O Azure AD é necessário para autenticar solicitações feitas por meio de qualquer cliente de provedor de recursos do Azure, incluindo a biblioteca Batch Management .NET e por meio do Azure Resource Manager. Para obter informações sobre como usar o Azure AD com a biblioteca do .NET de Gerenciamento de Lote, consulte [Usar o Azure Active Directory para autenticar soluções em Lote](batch-aad-auth.md).

## <a name="sample-project-on-github"></a>Projeto de exemplo no GitHub

Para ver o Batch Management .NET em ação, confira o projeto de exemplo [AccountManagement](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/AccountManagement) no GitHub. O aplicativo de exemplo AccountManagment demonstra as operações a seguir:

1. Adquira um token de segurança no Azure AD usando a [ADAL](../active-directory/azuread-dev/active-directory-authentication-libraries.md). Se o usuário ainda não estiver conectado, será solicitado que ele forneça suas credenciais do Azure.
2. Com o token de segurança obtido no Azure AD, crie um [SubscriptionClient](/dotnet/api/microsoft.azure.management.resourcemanager.subscriptionclient) para consultar o Azure e obter uma lista de assinaturas associadas à conta. O usuário poderá selecionar uma assinatura na lista se ela contiver mais de uma assinatura.
3. Associe as credenciais à assinatura selecionada.
4. Crie um objeto [ResourceManagementClient](/dotnet/api/microsoft.azure.management.resourcemanager.resourcemanagementclient) usando as credenciais.
5. Use um objeto [ResourceManagementClient](/dotnet/api/microsoft.azure.management.resourcemanager.resourcemanagementclient) para criar um grupo de recursos.
6. Use um objeto [BatchManagementClient](/dotnet/api/microsoft.azure.management.batch.batchmanagementclient) para executar várias operações de conta do Lote:
   - Crie uma conta do Lote no novo grupo de recursos.
   - Obtenha a conta recém-criada no serviço do Lote.
   - Imprima as chaves de conta da nova conta.
   - Regenere uma nova chave primária para a conta.
   - Imprima as informações de cota para a conta.
   - Imprima as informações de cota para a assinatura.
   - Imprima todas as contas na assinatura.
   - Exclua a conta recém-criada.
7. Exclua o grupo de recursos.

Para executar o aplicativo de exemplo com êxito, primeiro é necessário registrá-lo no locatário do Azure AD no portal do Azure e conceder permissões à API do Azure Resource Manager. Siga as etapas fornecidas em [Autenticar soluções de gerenciamento do lote com o Active Directory](batch-aad-auth-management.md).

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [Fluxo de trabalho e recursos primários do serviço de lote](batch-service-workflow-features.md) como pools, nós, trabalhos e tarefas.
- Obtenha as noções básicas sobre o desenvolvimento de um aplicativo habilitado para o Lote usando a [biblioteca de cliente .NET do Lote](quick-run-dotnet.md) ou do [Python](quick-run-python.md). Esses guias de início rápido orientam você por meio de um aplicativo de exemplo que usa o serviço de lote para executar uma carga de trabalho em vários nós de computação, usando o armazenamento do Azure para preparo e recuperação de arquivos de carga de trabalho. git pus
