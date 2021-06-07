---
title: Configurar uma instância e uma autenticação (CLI)
titleSuffix: Azure Digital Twins
description: Confira de que modo configurar uma instância do serviço dos Gêmeos Digitais do Azure usando a CLI
author: baanders
ms.author: baanders
ms.date: 7/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 2969ee5339306e30044a6a80260f912d45d0c8a2
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105044"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-cli"></a>Configurar uma instância e uma autenticação (CLI) dos Gêmeos Digitais do Azure

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

Este artigo abordará etapas para **configurar uma nova instância dos Gêmeos Digitais do Azure**, incluindo a criação da instância e a configuração da autenticação. Após concluir este artigo, uma instância dos Gêmeos Digitais do Azure estará pronta para você começar a programar nela.

Esta versão do artigo vai explorar cada uma das etapas de modo manual usando a CLI.
* Para explorar as etapas de modo manual usando o portal do Azure, confira a versão do portal neste artigo: [*como configurar uma instância e uma autenticação (portal)* ](how-to-set-up-instance-portal.md).
* Para executar uma configuração automatizada usando um exemplo de script de implantação, confira a versão com script neste artigo: [*como configurar uma instância e uma autenticação (com script)* ](how-to-set-up-instance-scripted.md).

[!INCLUDE [digital-twins-setup-steps.md](../../includes/digital-twins-setup-steps.md)]
[!INCLUDE [digital-twins-setup-permissions.md](../../includes/digital-twins-setup-permissions.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="set-up-cloud-shell-session"></a>Configurar uma sessão do Cloud Shell
[!INCLUDE [Cloud Shell for Azure Digital Twins](../../includes/digital-twins-cloud-shell.md)]

## <a name="create-the-azure-digital-twins-instance"></a>Criar a instância dos Gêmeos Digitais do Azure

Nesta seção, você **criará uma instância dos Gêmeos Digitais do Azure** usando o comando do Cloud Shell. Será preciso fornecer:
* O grupo de recursos em que a instância será implantada. Caso ainda não tenha um grupo de recursos existente, será possível criar um agora com este comando:
    ```azurecli-interactive
    az group create --location <region> --name <name-for-your-resource-group>
    ```
* Uma região para executar a implantação. Para conferir quais regiões são compatíveis com os Gêmeos Digitais do Azure, acesse a página de [*produtos do Azure disponíveis por região*](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins).
* Um nome para a instância. Caso sua assinatura tenha outra instância dos Gêmeos Digitais do Azure na região que já está usando o nome especificado, você receberá uma solicitação para escolher um nome diferente.

Use esses valores no seguinte comando para criar a instância:

```azurecli-interactive
az dt create --dt-name <name-for-your-Azure-Digital-Twins-instance> -g <your-resource-group> -l <region>
```

### <a name="verify-success-and-collect-important-values"></a>Verificar êxito e coletar valores importantes

Caso a instância tenha sido criada com êxito, gerando informações sobre o recurso criado, o resultado será semelhante a este no Cloud Shell:

:::image type="content" source="media/how-to-set-up-instance/cloud-shell/create-instance.png" alt-text="Janela Comando mostrando a criação com êxito do grupo de recursos e da instância dos Gêmeos Digitais do Azure":::

Anote o **hostName** da instância dos Gêmeos Digitais do Azure, bem como o **name** e o **resourceGroup** da saída. Todos esses valores são importantes e talvez você precise deles ao continuar trabalhando com sua instância dos Gêmeos Digitais do Azure para configurar uma autenticação e os recursos do Azure relacionados. Caso outros usuários estejam executando uma programação na instância, esses valores deverão ser compartilhados com eles.

> [!TIP]
> É possível conferir essas propriedades, juntamente com todas as propriedades de sua instância, a qualquer momento executando `az dt show --dt-name <your-Azure-Digital-Twins-instance>`.

Agora você tem uma instância dos Gêmeos Digitais do Azure pronta para uso. Depois, você concederá permissões apropriadas ao usuário do Azure para gerenciar a instância.

## <a name="set-up-user-access-permissions"></a>Configurar as permissões de acesso do usuário

[!INCLUDE [digital-twins-setup-role-assignment.md](../../includes/digital-twins-setup-role-assignment.md)]

Use o comando a seguir para atribuir a função (ela deverá ser executada por um usuário com [permissões suficientes](#prerequisites-permission-requirements) na assinatura do Azure). O comando exige que você insira o *nome UPN* na conta do Azure AD para o usuário que deverá receber a função. Na maioria dos casos, esse nome corresponderá ao email do usuário indicado na conta do Azure AD.

```azurecli-interactive
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<Azure-AD-user-principal-name-of-user-to-assign>" --role "Azure Digital Twins Data Owner"
```

O resultado desse comando mostrará informações geradas sobre uma atribuição de função criada.

> [!NOTE]
> Caso esse comando retorne um erro indicando que a CLI **não pode localizar o usuário nem a entidade de serviço no banco de dados de grafo**:
>
> Atribua a função usando a *ID de objeto* do usuário como alternativa. Isso poderá ocorrer no caso de usuários com [MSAs (contas Microsoft pessoais)](https://account.microsoft.com/account). 
>
> Use a [página do portal do Azure de usuários do Azure Active Directory](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers) para selecionar a conta de usuário e abrir os respectivos detalhes. Copie a *ObjectID* do usuário:
>
> :::image type="content" source="media/includes/user-id.png" alt-text="Exibição da página do usuário no portal do Azure realçando o GUID no campo &quot;ID de objeto&quot;" lightbox="media/includes/user-id.png":::
>
> Depois, repita o comando da lista de atribuição de função usando a *ID de objeto* do usuário para o parâmetro `assignee` mostrado acima.

### <a name="verify-success"></a>Verificar êxito

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

Agora você tem uma instância dos Gêmeos Digitais do Azure pronta para uso, bem como atribuiu permissões para gerenciá-la.

## <a name="next-steps"></a>Próximas etapas

Teste chamadas individuais à API REST em sua instância usando comandos da CLI dos Gêmeos Digitais do Azure: 
* [az dt reference](/cli/azure/dt)
* [*Como usar a CLI dos Gêmeos Digitais do Azure*](how-to-use-cli.md)

Como alternativa, confira de que modo conectar um aplicativo cliente à sua instância usando o código de autenticação:
* [*Como escrever um código de autenticação do aplicativo*](how-to-authenticate-client.md)