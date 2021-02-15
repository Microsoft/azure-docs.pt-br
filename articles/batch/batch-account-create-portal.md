---
title: Criar uma conta no portal do Azure
description: Aprenda a criar uma conta do Lote do Azure no portal do Azure para executar cargas de trabalho paralelas em larga escala na nuvem.
ms.topic: how-to
ms.date: 01/26/2021
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: bd9dd9b6d6d3a8d6938427b83528746ae5d09318
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100368499"
---
# <a name="create-a-batch-account-with-the-azure-portal"></a>Criar uma conta do Lote com o Portal do Azure

Este tópico mostra como criar uma [conta do lote do Azure](accounts.md) no [portal do Azure](https://portal.azure.com), escolhendo as propriedades da conta que se ajustam ao seu cenário de computação. Você também aprenderá onde encontrar Propriedades de conta importantes, como chaves de acesso e URLs de conta.

Para saber mais sobre contas e cenários do Lote, confira [Fluxo de trabalho e recursos do serviço de lote](batch-service-workflow-features.md).

## <a name="create-a-batch-account"></a>Criar uma conta do Batch

[!INCLUDE [batch-account-mode-include](../../includes/batch-account-mode-include.md)]

1. Entre no [portal do Azure](https://portal.azure.com).

1. No home page, selecione **criar um recurso**.

1. Na caixa de pesquisa, insira **serviço de lote**. Selecione **serviço de lote** nos resultados e, em seguida, selecione **criar**.

1. Insira os detalhes a seguir.

    :::image type="content" source="media/batch-account-create-portal/batch-account-portal.png" alt-text="Captura da tela da nova conta do lote.":::

    a. **Assinatura**: a assinatura na qual a conta do Lote será criada. Se você tiver somente uma assinatura, ela será selecionada por padrão.

    b. **Grupo de recursos**: selecione um grupo de recursos existente para sua nova conta do Lote ou, opcionalmente, crie um novo.

    c. **Nome da conta**: o nome que você escolher deve ser exclusivo dentro da região do Azure na qual a conta é criada (confira **Local** abaixo). O nome da conta pode conter apenas minúsculas ou números e deve ter 3 a 24 caracteres de comprimento.

    d. **Localização**: a região do Azure na qual a conta do Lote será criada. Somente as regiões com suporte da sua assinatura e do seu grupo de recursos são exibidas como opções.

    e. **Conta de armazenamento**: uma [conta de armazenamento do Azure](accounts.md#azure-storage-accounts) opcional que você associa à sua conta do lote. Você pode selecionar uma conta de armazenamento existente ou criar uma nova. Uma conta de armazenamento de uso geral v2 é recomendada para o melhor desempenho.

    :::image type="content" source="media/batch-account-create-portal/storage_account.png" alt-text="Captura de tela das opções ao criar uma conta de armazenamento.":::

1. Se desejar, selecione **avançado** para especificar o **tipo de identidade**, o **acesso à rede pública** ou o **modo de alocação de pool**. Para a maioria dos cenários, as opções padrão são bem.

1. Selecione **revisar + criar** e, em seguida, selecione **criar** para criar a conta.

## <a name="view-batch-account-properties"></a>Exibir propriedades de conta do Lote

Após a criação da conta, selecione a conta para acessar suas configurações e propriedades. Você pode acessar todas as propriedades e configurações de conta usando o menu à esquerda.

> [!NOTE]
> O nome da conta do Lote é sua ID e não pode ser alterado. Se for preciso alterar o nome de uma conta do Lote, será necessário excluir a conta e criar uma nova com o nome pretendido.

:::image type="content" source="media/batch-account-create-portal/batch_blade.png" alt-text="Captura de tela da página conta do lote no portal do Azure.":::

ao desenvolver um aplicativo com as [APIs do Lote](batch-apis-tools.md#azure-accounts-for-batch-development), você precisa de uma URL da conta para acessar os recursos do Lote. (O lote também dá suporte à autenticação Azure Active Directory.) Para exibir as informações de acesso da conta do lote, selecione **chaves**.

:::image type="content" source="media/batch-account-create-portal/batch-account-keys.png" alt-text="Captura de tela das chaves de conta do lote no portal do Azure.":::

Para exibir o nome e as chaves da conta de armazenamento associados à sua conta de lote, selecione **Conta de armazenamento**.

Para exibir as [cotas de recursos](batch-quota-limit.md) que se aplicam à conta do lote, selecione **cotas**.

## <a name="additional-configuration-for-user-subscription-mode"></a>Configuração adicional para o modo de assinatura do usuário

Se optar por criar uma conta do Lote no modo de assinatura do usuário, execute as etapas adicionais a seguir antes de criá-la.

> [!IMPORTANT]
> O usuário que cria a conta do lote no modo de assinatura do usuário precisa ter a atribuição de função de colaborador ou proprietário para a assinatura na qual a conta do lote será criada.

### <a name="allow-azure-batch-to-access-the-subscription-one-time-operation"></a>Permitir que o Lote do Azure acesse a assinatura (operação única)

Ao criar sua primeira conta do Lote no modo de assinatura do usuário, é preciso registrar sua assinatura com o Lote. (Se você já fez isso, pule para a próxima seção.)

1. Entre no [portal do Azure](https://portal.azure.com).

1. Selecione **Todos os serviços** > **Assinaturas** e selecione a assinatura que você deseja usar para a conta do Lote.

1. Na página **Assinatura**, selecione **Provedores de recursos** e procure **Microsoft.Batch**. Verifique se o provedor de recursos **Microsoft.Batch** está registrado na assinatura. Se não estiver, selecione o link **registrar** próximo à parte superior da tela.

    :::image type="content" source="media/batch-account-create-portal/register_provider.png" alt-text="Captura de tela mostrando o provedor de recursos Microsoft.Batch.":::

1. Retorne à página de **assinatura** e selecione atribuições de função de **controle de acesso (iam)**  >    >  **Adicionar**  >  **Adicionar atribuição de função**.

    :::image type="content" source="media/batch-account-create-portal/subscription_iam.png" alt-text="Captura de tela da página de atribuições de função para uma assinatura.":::

1. Na página **Adicionar atribuição de função** , selecione a função **colaborador** ou **proprietário** e procure a API do lote. Procure **lote do Microsoft Azure** ou **MicrosoftAzureBatch** para localizar a API. (**ddbf3205-c6bd-46ae-8127-60eb93363864** é a ID do aplicativo para a API do lote.)

1. Depois de encontrar a API do Lote, selecione a API e, depois, **Salvar**.

### <a name="create-a-key-vault"></a>Criar um cofre de chaves

No modo de assinatura do usuário, é necessário um [Azure Key Vault](../key-vault/general/overview.md) . O Key Vault deve estar na mesma assinatura e região que a conta do lote a ser criada.

1. Na home page do [portal do Azure](https://portal.azure.com), selecione **criar um recurso**.
1. Digite **Key Vault** na caixa Pesquisar. Selecione **Key Vault** nos resultados e, em seguida, selecione **criar**.
1. Na página **criar cofre de chaves** , insira um nome para o Key Vault e crie um novo grupo de recursos na mesma região que você deseja para sua conta do lote. Deixe as configurações restantes com valores padrão e selecione **Criar**.

Ao criar a conta do lote no modo de assinatura do usuário, especifique a **assinatura do usuário** como o modo de alocação do pool, selecione o Key Vault que você criou e marque a caixa para conceder acesso ao lote do Azure para o Key Vault.

Se você preferir conceder acesso ao Key Vault manualmente, vá para a seção **políticas de acesso** do Key Vault e selecione **Adicionar política de acesso**. Selecione o link ao lado de **selecionar entidade de segurança** e procure **lote do Microsoft Azure** (ID do aplicativo **ddbf3205-c6bd-46ae-8127-60eb93363864**). Selecione essa entidade de segurança e configure as **permissões secretas** usando o menu suspenso. O Lote do Azure deve ter no mínimo permissões de **Obter**, **Lista**, **Definir** e **Excluir**.

:::image type="content" source="media/batch-account-create-portal/secret-permissions.png" alt-text="Captura de tela das seleções de permissões secretas para o lote do Azure":::

Selecione **Adicionar** e verifique se as caixas de seleção **máquinas virtuais do Azure para implantação** e **Azure Resource Manager para implantação de modelo** estão selecionadas para o recurso de **Key Vault** vinculado. Selecione **salvar** para confirmar suas alterações.

:::image type="content" source="media/batch-account-create-portal/key-vault-access-policy.png" alt-text="Captura da tela da política de acesso.":::

### <a name="configure-subscription-quotas"></a>Configurar cotas de assinatura

Para contas do lote de assinatura de usuário, as [cotas de núcleo](batch-quota-limit.md) devem ser definidas manualmente. Lote Standard cotas de núcleo não se aplicam a contas no modo de assinatura do usuário, e as [cotas em sua assinatura](../azure-resource-manager/management/azure-subscription-service-limits.md) para núcleos de computação regionais, núcleos de computação por série e outros recursos são usados e impostos.

1. No [portal do Azure](https://portal.azure.com), selecione sua conta do Lote no modo de assinatura de usuário para exibir as respectivas propriedades e configurações.
1. No menu à esquerda, selecione **Cotas** para exibir e configurar as cotas de núcleo associadas com sua conta do Lote.

## <a name="other-batch-account-management-options"></a>Outras opções de gerenciamento de conta do Lote

Além de usar o portal do Azure, você pode criar e gerenciar contas do Lote com diferentes ferramentas, incluindo:

- [Cmdlets do PowerShell do Lote](batch-powershell-cmdlets-get-started.md)
- [CLI do Azure](batch-cli-get-started.md)
- [.NET de Gerenciamento do Lote](batch-management-dotnet.md)

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [Fluxo de trabalho e recursos primários do serviço de lote](batch-service-workflow-features.md) como pools, nós, trabalhos e tarefas.
- Obtenha as noções básicas sobre o desenvolvimento de um aplicativo habilitado para o Lote usando a [biblioteca de cliente .NET do Lote](quick-run-dotnet.md) ou do [Python](quick-run-python.md). Esses guias de início rápido orientam você por meio de um aplicativo de exemplo que usa o serviço de lote para executar uma carga de trabalho em vários nós de computação, usando o armazenamento do Azure para preparo e recuperação de arquivo de carga de trabalho.