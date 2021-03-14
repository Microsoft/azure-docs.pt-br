---
title: Configurar uma instância e uma autenticação (CLI)
titleSuffix: Azure Digital Twins
description: Veja como configurar uma instância do serviço gêmeos do Azure digital usando a CLI
author: baanders
ms.author: baanders
ms.date: 7/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 7108ee956c18fbcc150b56cbcb8ae1c69841dda4
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102198567"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-cli"></a>Configurar uma instância e autenticação do gêmeos digital do Azure (CLI)

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

Este artigo aborda as etapas para **Configurar uma nova instância de gêmeos digital do Azure**, incluindo a criação da instância e a configuração da autenticação. Depois de concluir este artigo, você terá uma instância do gêmeos digital do Azure pronta para começar a programar.

Esta versão deste artigo percorre essas etapas manualmente, uma a uma, usando a CLI.
* Para percorrer essas etapas manualmente usando o portal do Azure, consulte a versão do portal deste artigo: [*como configurar uma instância e autenticação (Portal)*](how-to-set-up-instance-portal.md).
* Para executar uma configuração automatizada usando um exemplo de script de implantação, consulte a versão com script deste artigo: [*como: configurar uma instância e autenticação (script)*](how-to-set-up-instance-scripted.md).

[!INCLUDE [digital-twins-setup-steps.md](../../includes/digital-twins-setup-steps.md)]
[!INCLUDE [digital-twins-setup-permissions.md](../../includes/digital-twins-setup-permissions.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="set-up-cloud-shell-session"></a>Configurar uma sessão do Cloud Shell
[!INCLUDE [Cloud Shell for Azure Digital Twins](../../includes/digital-twins-cloud-shell.md)]

## <a name="create-the-azure-digital-twins-instance"></a>Criar a instância de gêmeos digital do Azure

Nesta seção, você **criará uma nova instância do Azure digital gêmeos** usando o comando Cloud Shell. Você precisará fornecer:
* Um grupo de recursos em que a instância será implantada. Se você ainda não tiver um grupo de recursos existente em mente, poderá criar um agora com este comando:
    ```azurecli-interactive
    az group create --location <region> --name <name-for-your-resource-group>
    ```
* Uma região para a implantação. Para ver quais regiões dão suporte ao Azure digital gêmeos, visite [*produtos do Azure disponíveis por região*](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins).
* Um nome para sua instância. Se sua assinatura tiver outra instância de gêmeos digital do Azure na região que já está usando o nome especificado, você será solicitado a escolher um nome diferente.

Use esses valores no comando a seguir para criar a instância:

```azurecli-interactive
az dt create --dt-name <name-for-your-Azure-Digital-Twins-instance> -g <your-resource-group> -l <region>
```

### <a name="verify-success-and-collect-important-values"></a>Verificar o êxito e coletar valores importantes

Se a instância foi criada com êxito, o resultado em Cloud Shell será semelhante a este, gerando informações sobre o recurso que você criou:

:::image type="content" source="media/how-to-set-up-instance/cloud-shell/create-instance.png" alt-text="janela Comando com a criação bem-sucedida do grupo de recursos e a instância do gêmeos digital do Azure":::

Anote o nome de **host** da instância do Azure digital gêmeos, **Name** e **resourcegroup** da saída. Esses são todos os valores importantes que podem ser necessários à medida que você continuar trabalhando com sua instância do gêmeos digital do Azure para configurar a autenticação e os recursos do Azure relacionados. Se outros usuários estiverem programando em relação à instância, você deverá compartilhar esses valores com eles.

> [!TIP]
> Você pode ver essas propriedades, juntamente com todas as propriedades de sua instância, a qualquer momento executando `az dt show --dt-name <your-Azure-Digital-Twins-instance>` .

Agora você tem uma instância de gêmeos digital do Azure pronta para uso. Em seguida, você dará as permissões apropriadas de usuário do Azure para gerenciá-lo.

## <a name="set-up-user-access-permissions"></a>Configurar permissões de acesso do usuário

[!INCLUDE [digital-twins-setup-role-assignment.md](../../includes/digital-twins-setup-role-assignment.md)]

Use o comando a seguir para atribuir a função (deve ser executada por um usuário com [permissões suficientes](#prerequisites-permission-requirements) na assinatura do Azure). O comando exige que você passe o *nome UPN* na conta do Azure ad para o usuário que deve receber a função. Na maioria dos casos, isso corresponderá ao email do usuário na conta do Azure AD.

```azurecli-interactive
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<Azure-AD-user-principal-name-of-user-to-assign>" --role "Azure Digital Twins Data Owner"
```

O resultado desse comando são informações de saída sobre a atribuição de função que foi criada.

> [!NOTE]
> Se esse comando retornar um erro dizendo que a CLI **não pode localizar a entidade de serviço ou usuário no banco de dados de grafo**:
>
> Em vez disso, atribua a função usando a *ID de objeto* do usuário. Isso pode acontecer para usuários em contas pessoais da [Microsoft (MSAS)](https://account.microsoft.com/account). 
>
> Use a [página portal do Azure de Azure Active Directory usuários](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers) para selecionar a conta de usuário e abrir seus detalhes. Copie o *ObjectID* do usuário:
>
> :::image type="content" source="media/includes/user-id.png" alt-text="Exibição da página do usuário no portal do Azure realçando o GUID no campo ' ID do objeto '" lightbox="media/includes/user-id.png":::
>
> Em seguida, repita o comando da lista de atribuição de função usando a *ID de objeto* do usuário para o `assignee` parâmetro acima.

### <a name="verify-success"></a>Verificar êxito

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

Agora você tem uma instância de gêmeos digital do Azure pronta para uso e atribuiu permissões para gerenciá-la.

## <a name="next-steps"></a>Próximas etapas

Teste as chamadas de API REST individuais em sua instância usando os comandos da CLI do Azure digital gêmeos: 
* [referência de AZ DT](/cli/azure/ext/azure-iot/dt)
* [*Como usar a CLI dos Gêmeos Digitais do Azure*](how-to-use-cli.md)

Ou então, consulte Como conectar um aplicativo cliente à sua instância com o código de autenticação:
* [*Como: escrever código de autenticação do aplicativo*](how-to-authenticate-client.md)