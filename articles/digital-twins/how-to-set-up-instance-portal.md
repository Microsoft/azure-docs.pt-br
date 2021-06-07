---
title: Configurar uma instância e uma autenticação (portal)
titleSuffix: Azure Digital Twins
description: Consulte como configurar uma instância do serviço gêmeos do Azure digital usando o portal do Azure
author: baanders
ms.author: baanders
ms.date: 7/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.custom: contperf-fy21q2
ms.openlocfilehash: 8263e0805f48976222e66922be8c04e0902101d6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102201831"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-portal"></a>Configurar uma instância e autenticação do gêmeos digital do Azure (Portal)

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

Este artigo aborda as etapas para **Configurar uma nova instância de gêmeos digital do Azure**, incluindo a criação da instância e a configuração da autenticação. Depois de concluir este artigo, você terá uma instância do gêmeos digital do Azure pronta para começar a programar.

Esta versão deste artigo percorre essas etapas manualmente, uma a uma, usando o portal do Azure. O portal do Azure é um console unificado baseado na Web que fornece uma alternativa para as ferramentas de linha de comando.
* Para percorrer essas etapas manualmente usando a CLI, consulte a versão da CLI deste artigo: [*como configurar uma instância e autenticação (CLI)*](how-to-set-up-instance-cli.md).
* Para executar uma configuração automatizada usando um exemplo de script de implantação, consulte a versão com script deste artigo: [*como: configurar uma instância e autenticação (script)*](how-to-set-up-instance-scripted.md).

[!INCLUDE [digital-twins-setup-steps.md](../../includes/digital-twins-setup-steps.md)]
[!INCLUDE [digital-twins-setup-permissions.md](../../includes/digital-twins-setup-permissions.md)]

## <a name="create-the-azure-digital-twins-instance"></a>Criar a instância de gêmeos digital do Azure

Nesta seção, você **criará uma nova instância do Azure digital gêmeos** usando o [portal do Azure](https://ms.portal.azure.com/). Navegue até o portal e faça logon com suas credenciais.

Uma vez no portal, comece selecionando _criar um recurso_ no menu Home Page de serviços do Azure.

:::image type="content" source= "media/how-to-set-up-instance/portal/create-resource.png" alt-text="Selecionando &quot;criar um recurso&quot; na home page da portal do Azure":::

Pesquise *gêmeos do Azure digital* na caixa de pesquisa e escolha o serviço **gêmeos digital do Azure** nos resultados. Selecione o botão _criar_ para criar uma nova instância do serviço.

:::image type="content" source= "media/how-to-set-up-instance/portal/create-azure-digital-twins.png" alt-text="Selecionando ' criar ' na página do serviço de gêmeos digital do Azure":::

Na página **criar recurso** a seguir, preencha os valores fornecidos abaixo:
* **Assinatura**: a assinatura do Azure que você está usando
  - **Grupo de recursos**: um grupo de recursos no qual implantar a instância. Se você ainda não tiver um grupo de recursos existente em mente, poderá criar um aqui selecionando o link *criar novo* e inserindo um nome para um novo grupo de recursos
* **Local**: uma região do Azure digital gêmeos habilitada para a implantação. Para obter mais detalhes sobre o suporte regional, visite [*produtos do Azure disponíveis por região (Azure digital gêmeos)*](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins).
* **Nome do recurso**: um nome para sua instância de gêmeos digital do Azure. Se sua assinatura tiver outra instância de gêmeos digital do Azure na região que já está usando o nome especificado, você será solicitado a escolher um nome diferente.

:::image type="content" source= "media/how-to-set-up-instance/portal/create-azure-digital-twins-2.png" alt-text="Preenchendo os valores descritos para criar um recurso de gêmeos digital do Azure":::

Quando tiver terminado, você poderá selecionar **revisar + criar** se não quiser definir mais configurações para sua instância do. Isso levará você a uma página de resumo, na qual você pode examinar os detalhes da instância que você inseriu e concluiu com **Create**. 

Se você quiser configurar mais detalhes para sua instância, a próxima seção descreverá as guias de configuração restantes.

### <a name="additional-setup-options"></a>Opções de configuração adicionais

Aqui estão as opções adicionais que podem ser definidas durante a instalação, usando as outras guias do processo de **criação de recursos** .

* **Rede**: nessa guia, você pode habilitar pontos de extremidade privados com o [link privado do Azure](../private-link/private-link-overview.md) para eliminar a exposição da rede pública à sua instância. Para obter instruções, consulte [*como habilitar o acesso privado com o link privado (visualização)*](./how-to-enable-private-link-portal.md#add-a-private-endpoint-during-instance-creation).
* **Avançado**: nessa guia, você pode habilitar uma [identidade gerenciada pelo sistema](../active-directory/managed-identities-azure-resources/overview.md) para sua instância do que pode ser usada ao encaminhar eventos para [pontos de extremidade](concepts-route-events.md). Para obter instruções, consulte [*como: habilitar identidades gerenciadas para eventos de roteamento (versão prévia)*](./how-to-enable-managed-identities-portal.md#add-a-system-managed-identity-during-instance-creation).
* **Marcas**: nesta guia, você pode adicionar marcas à sua instância para ajudá-lo a organizá-las entre os recursos do Azure. Para saber mais sobre as marcas de recurso do Azure, confira [*recursos de marca, grupos de recursos e assinaturas para a organização lógica*](../azure-resource-manager/management/tag-resources.md).

### <a name="verify-success-and-collect-important-values"></a>Verificar o êxito e coletar valores importantes

Depois de concluir a configuração da instância selecionando **criar**, você pode exibir o status da implantação da sua instância em suas notificações do Azure ao longo da barra de ícones do Portal. A notificação indicará quando a implantação foi bem-sucedida e você poderá selecionar o botão _ir para o recurso_ para exibir a instância criada.

:::image type="content" source="media/how-to-set-up-instance/portal/notifications-deployment.png" alt-text="Exibição de notificações do Azure mostrando uma implantação bem-sucedida e realçando o botão ' ir para recurso '":::

Como alternativa, se a implantação falhar, a notificação indicará o porquê. Observe o aviso da mensagem de erro e tente criar a instância novamente.

>[!TIP]
>Depois que sua instância for criada, você poderá retornar para sua página a qualquer momento pesquisando o nome da sua instância na barra de pesquisa portal do Azure.

Na página *visão geral* da instância, anote seu *nome*, *grupo de recursos* e *nome do host*. Esses são os valores importantes que podem ser necessários à medida que você continuar trabalhando com sua instância de gêmeos digital do Azure. Se outros usuários estiverem programando em relação à instância, você deverá compartilhar esses valores com eles.

:::image type="content" source="media/how-to-set-up-instance/portal/instance-important-values.png" alt-text="Realçando os valores importantes da página de visão geral da instância":::

Agora você tem uma instância de gêmeos digital do Azure pronta para uso. Em seguida, você dará as permissões apropriadas de usuário do Azure para gerenciá-lo.

## <a name="set-up-user-access-permissions"></a>Configurar permissões de acesso do usuário

[!INCLUDE [digital-twins-setup-role-assignment.md](../../includes/digital-twins-setup-role-assignment.md)]

Primeiro, abra a página da instância do gêmeos digital do Azure no portal do Azure. No menu da instância, selecione *controle de acesso (iam)*. Selecione o botão  **+ Adicionar** para adicionar uma nova atribuição de função.

:::image type="content" source="media/how-to-set-up-instance/portal/add-role-assignment-1.png" alt-text="Selecionando para adicionar uma atribuição de função da página ' controle de acesso (IAM) '":::

Na página *Adicionar atribuição de função* a seguir, preencha os valores (deve ser concluído por um usuário com [permissões suficientes](#prerequisites-permission-requirements) na assinatura do Azure):
* **Função**: selecione *Azure digital gêmeos proprietário dos dados* no menu suspenso
* **Atribuir acesso a**: usar *usuário, grupo ou entidade de serviço*
* **Selecione**: Procure o nome ou endereço de email do usuário a ser atribuído. Quando você seleciona o resultado, o usuário aparecerá em uma seção de *Membros selecionados* .

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-set-up-instance/portal/add-role-assignment-2.png" alt-text="Preenchendo os campos listados na caixa de diálogo ' Adicionar atribuição de função '":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Quando tiver terminado de inserir os detalhes, clique no botão *salvar* .

### <a name="verify-success"></a>Verificar êxito

Você pode exibir a atribuição de função que configurou sob o *controle de acesso (iam) > atribuições de função*. O usuário deve aparecer na lista com uma função do proprietário de *dados do gêmeos digital do Azure*. 

:::image type="content" source="media/how-to-set-up-instance/portal/verify-role-assignment.png" alt-text="Exibição das atribuições de função para uma instância do gêmeos digital do Azure no portal do Azure":::

Agora você tem uma instância de gêmeos digital do Azure pronta para uso e atribuiu permissões para gerenciá-la.

## <a name="next-steps"></a>Próximas etapas

Teste as chamadas de API REST individuais em sua instância usando os comandos da CLI do Azure digital gêmeos: 
* [referência de AZ DT](/cli/azure/ext/azure-iot/dt)
* [*Como usar a CLI dos Gêmeos Digitais do Azure*](how-to-use-cli.md)

Ou então, consulte Como conectar um aplicativo cliente à sua instância com o código de autenticação:
* [*Como: escrever código de autenticação do aplicativo*](how-to-authenticate-client.md)