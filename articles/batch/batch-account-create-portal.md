---
title: Criar uma conta no portal do Azure – Lote do Azure | Microsoft Docs
description: Aprenda a criar uma conta do Lote do Azure no portal do Azure para executar cargas de trabalho paralelas em larga escala na nuvem.
services: batch
documentationcenter: ''
author: laurenhughes
manager: gwallace
editor: ''
ms.assetid: 3fbae545-245f-4c66-aee2-e25d7d5d36db
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/26/2019
ms.author: lahugh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6e1543d578e6812fd270bd76ec18bdfe8fe5ba6a
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68324049"
---
# <a name="create-a-batch-account-with-the-azure-portal"></a>Criar uma conta do Lote com o Portal do Azure

Saiba como criar uma conta do lote do Azure no [portal do Azure][azure_portal]e escolha as propriedades da conta que se ajustam ao seu cenário de computação. Saiba onde encontrar propriedades da conta importantes, como chaves de acesso e URLs de conta.

Para saber mais sobre contas do Lote e cenários, confira a [visão geral do recurso](batch-api-basics.md).

## <a name="create-a-batch-account"></a>Criar uma conta do Batch

[!INCLUDE [batch-account-mode-include](../../includes/batch-account-mode-include.md)]

1. Entre no [Portal do Azure][azure_portal].

1. Selecione **Criar um recurso** >  **Computação** > **Serviço de lote**.

    ![Lote no Marketplace][marketplace_portal]

1. Insira as configurações da **Nova conta do Lote**. Confira os seguintes detalhes.

    ![Criar uma conta do Batch][account_portal]

    a. **Assinatura**: a assinatura na qual a conta do Lote será criada. Se você tiver somente uma assinatura, ela será selecionada por padrão.

    b. **Grupo de recursos**: selecione um grupo de recursos existente para sua nova conta do Lote ou, opcionalmente, crie um novo.

    c. **Nome da conta**: o nome que você escolher deve ser exclusivo dentro da região do Azure na qual a conta é criada (confira **Local** abaixo). O nome da conta pode conter apenas minúsculas ou números e deve ter 3 a 24 caracteres de comprimento.

    d. **Localização**: a região do Azure na qual a conta do Lote será criada. Somente as regiões com suporte da sua assinatura e do seu grupo de recursos são exibidas como opções.

    e. **Conta de armazenamento**: Uma conta opcional do Armazenamento do Azure associada à sua conta do Lote. Uma conta de armazenamento de uso geral v2 é recomendada para o melhor desempenho. Para obter todas as opções de conta de armazenamento no Lote, confira a [Visão geral do recurso do Lote](batch-api-basics.md#azure-storage-account). No portal, selecione uma conta de armazenamento existente ou crie uma.

      ![Criar uma conta de armazenamento][storage_account]

    f. **Modo de alocação de pools**: Na guia de configurações **Avançadas**, especifique o modo de alocação de pool como **Serviço em lotes** ou **Assinatura de usuário**. para a maioria dos cenários, aceite o **Serviço de Lote** padrão.

      ![Modo de alocação de pool do Lote][pool_allocation]

1. Selecione **Criar** para criar a conta.

## <a name="view-batch-account-properties"></a>Exibir propriedades de conta do Lote

Após a criação da conta, selecione a conta para acessar suas configurações e propriedades. Você pode acessar todas as propriedades e configurações de conta usando o menu à esquerda.

![Página Conta do Lote no portal do Azure][account_blade]

* **Nome da conta, URL e chaves do Lote**: ao desenvolver um aplicativo com as [APIs do Lote](batch-apis-tools.md#azure-accounts-for-batch-development), você precisa de uma URL da conta para acessar os recursos do Lote. (O Lote também dá suporte à autenticação do Azure Active Directory.)

    Para exibir as informações de acesso da conta do Lote, selecione **Chaves**.

    ![Chaves de conta do Lote no portal do Azure][account_keys]

* Para exibir o nome e as chaves da conta de armazenamento associados à sua conta de lote, selecione **Conta de armazenamento**.

* Para exibir as cotas de recursos que se aplicam à conta do Lote, selecione **Cotas**. Para obter detalhes, consulte [Cotas e limites de serviço do Lote](batch-quota-limit.md).

## <a name="additional-configuration-for-user-subscription-mode"></a>Configuração adicional para o modo de assinatura do usuário

Se optar por criar uma conta do Lote no modo de assinatura do usuário, execute as etapas adicionais a seguir antes de criá-la.

### <a name="allow-azure-batch-to-access-the-subscription-one-time-operation"></a>Permitir que o Lote do Azure acesse a assinatura (operação única)

Ao criar sua primeira conta do Lote no modo de assinatura do usuário, é preciso registrar sua assinatura com o Lote. (Se você fez isso anteriormente, pule para a próxima seção.)

1. Entre no [Portal do Azure][azure_portal].

1. Selecione **Todos os serviços** > **Assinaturas**e selecione a assinatura que você deseja usar para a conta do Lote.

1. Na página **Assinatura**, selecione **Provedores de recursos** e procure **Microsoft.Batch**. Verifique se o provedor de recursos **Microsoft.Batch** está registrado na assinatura. Se não estiver registrado, selecione o link **Registrar**.

    ![Registrar o provedor Microsoft.Batch][register_provider]

1. Na página **Assinatura**, selecione **Controle de acesso (IAM)**  > **Atribuições de função** > **Adicionar atribuição de função**.

    ![Controle de acesso de assinatura][subscription_access]

1. Na página **Adicionar atribuição de função**, selecione a função **Colaborador**, procure a API do Lote. Procure cada uma dessas cadeias de caracteres até encontrar a API:
    1. **MicrosoftAzureBatch**.
    1. **Lote do Microsoft Azure**. Os locatários mais recentes do Azure AD podem usar esse nome.
    1. **ddbf3205-c6bd-46ae-8127-60eb93363864** é a ID para a API do Lote.

1. Depois de encontrar a API do Lote, selecione a API e, depois, **Salvar**.

    ![Adicionar permissões do Lote][add_permission]

### <a name="create-a-key-vault"></a>Criar um cofre de chave

No modo de assinatura do usuário, é necessário ter um Azure Key Vault que pertença ao mesmo grupo de recursos da conta do Lote a ser criada. Verifique se o grupo de recursos está em uma região em que o Lote esteja [disponível](https://azure.microsoft.com/regions/services/) e que tenha suporte pela assinatura.

1. Na [portal do Azure][azure_portal], selecione **novo** > **Key Vault**de**segurança** > .

1. Na página **Criar Key Vault**, insira um nome para o cofre de chaves e crie um grupo de recursos na região desejada para a conta do Lote. Deixe as configurações restantes com valores padrão e selecione **Criar**.

Ao criar a conta do lote no modo de assinatura do usuário, use o grupo de recursos para o cofre de chaves. Especifique a **assinatura do usuário** como o modo de alocação do pool, selecione o cofre de chaves e marque a caixa para conceder acesso ao lote do Azure ao cofre de chaves. 

Se você preferir conceder acesso ao key Vault manualmente, vá para a seção **políticas de acesso** do cofre de chaves e selecione **lote do Microsoft Azure**. Configure as **permissões secretas** usando o menu suspenso. O lote do Azure deve receber, no mínimo, as permissões **obter**, **listar**, **definir**e **excluir** .

![Permissões secretas para o lote do Azure](./media/batch-account-create-portal/secret-permissions.png)

### <a name="configure-subscription-quotas"></a>Configurar cotas de assinatura

As cotas de núcleo não são definidas por padrão em contas de Lote de assinatura de usuário. As cotas de núcleos precisam ser definidas manualmente, porque as cotas de núcleo padrão do Lote não se aplicam às contas no modo de assinatura de usuário.

1. Na [portal do Azure][azure_portal], selecione a conta do lote do modo de assinatura do usuário para exibir suas configurações e propriedades.

1. No menu à esquerda, selecione **Cotas** para exibir e configurar as cotas de núcleo associadas com sua conta do Lote.

Veja os [limites e cotas do serviço de Lote](batch-quota-limit.md) para obter mais informações sobre cotas de núcleo do modo de assinatura de usuário.

## <a name="other-batch-account-management-options"></a>Outras opções de gerenciamento de conta do Lote

Além de usar o portal do Azure, você pode criar e gerenciar contas do Lote com diferentes ferramentas, incluindo:

* [Cmdlets do PowerShell do Lote](batch-powershell-cmdlets-get-started.md)
* [CLI do Azure](batch-cli-get-started.md)
* [.NET de Gerenciamento do Lote](batch-management-dotnet.md)

## <a name="next-steps"></a>Próximas etapas

* Veja a [visão geral dos recursos do Lote](batch-api-basics.md) para saber mais sobre os conceitos e os recursos do serviço do Lote. O artigo aborda os recursos principais do Lote, como pools, nós de computação, trabalhos e tarefas, e fornece uma visão geral dos recursos do serviço para cargas de trabalho de computação em larga escala.
* Obtenha as noções básicas sobre o desenvolvimento de um aplicativo habilitado para o Lote usando a [biblioteca de cliente .NET do Lote](quick-run-dotnet.md) ou do [Python](quick-run-python.md). O artigo de início rápido orienta você por meio de um aplicativo de exemplo que usa o serviço em Lotes para executar uma carga de trabalho em vários nós de computação e que inclui o uso do Armazenamento do Azure para preparação e recuperação de um arquivo de carga de trabalho.

[azure_portal]: https://portal.azure.com
[batch_pricing]: https://azure.microsoft.com/pricing/details/batch/

[marketplace_portal]: ./media/batch-account-create-portal/marketplace-batch.png
[account_blade]: ./media/batch-account-create-portal/batch_blade.png
[account_portal]: ./media/batch-account-create-portal/batch-account-portal.png
[pool_allocation]: ./media/batch-account-create-portal/batch-pool-allocation.png
[account_keys]: ./media/batch-account-create-portal/batch-account-keys.png
[storage_account]: ./media/batch-account-create-portal/storage_account.png
[subscription_access]: ./media/batch-account-create-portal/subscription_iam.png
[add_permission]: ./media/batch-account-create-portal/add_permission.png
[register_provider]: ./media/batch-account-create-portal/register_provider.png
