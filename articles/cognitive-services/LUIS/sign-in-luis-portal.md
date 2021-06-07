---
title: Entrar no portal do LUIS
description: Se você for um novo usuário entrando no portal do LUIS, a experiência de entrada será ligeiramente diferente com base na sua conta de usuário atual.
services: cognitive-services
ms.custom: ''
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 09/08/2020
ms.topic: how-to
ms.author: nitinme
author: nitinme
ms.openlocfilehash: aeb84fca47dbf2922f17a3e8931e3158f9ee2cca
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101706768"
---
# <a name="sign-in-to-luis-portal"></a>Entrar no portal do LUIS

[!INCLUDE [LUIS Free account](includes/luis-portal-note.md)]

Use este artigo para começar a usar o portal do LUIS e criar um recurso de criação. Depois de concluir as etapas neste artigo, você poderá criar e publicar aplicativos LUIS.

## <a name="access-the-portal"></a>Acessar o portal


1. Para começar a usar o LUIS, acesse o [portal do Luis](https://www.luis.ai). Se você ainda não tiver uma assinatura, será solicitado que você crie uma [conta gratuita](https://azure.microsoft.com//free/cognitive-services/) e volte para o Portal.
2. Atualizar a página para atualizá-la com sua assinatura criada recentemente
3. Selecione sua assinatura na lista suspensa

    > [!div class="mx-imgBorder"]
    > ![selecionar assinaturas](./media/migrate-authoring-key/select-subscription-sign-in-2.png)

4. Se sua assinatura estiver em outro locatário, você não poderá alternar os locatários da janela existente. Você pode alternar locatários fechando essa janela e selecionando o avatar mais à direita contendo suas iniciais na barra superior. Clique em **escolher um recurso de criação diferente** na parte superior para abrir novamente a janela.

    > [!div class="mx-imgBorder"]
    > ![alternar diretórios](./media/migrate-authoring-key/switch-directories.png)

5. Se você tiver um recurso de criação de LUIS existente associado à sua assinatura, escolha-o na lista suspensa. Você pode exibir todos os aplicativos que são criados sob esse recurso de criação.
6. Caso contrário, clique em **criar um novo recurso de criação** na parte inferior desta janela restrita.
7.  Ao criar um novo recurso de criação, forneça as seguintes informações:

    > [!div class="mx-imgBorder"]
    > ![Criar novo recurso](./media/migrate-authoring-key/create-new-authoring-resource-2.png)

    * **Nome do locatário** -o locatário ao qual sua assinatura do Azure está associada. Não será possível alternar os locatários da janela existente. Você pode alternar locatários fechando essa janela e selecionando o avatar no canto superior direito da tela, contendo suas iniciais. Selecione **escolher um recurso de criação diferente** na parte superior para reabrir a janela.
    * **Nome do grupo de recursos do Azure** -um nome de grupo de recursos personalizado que você escolhe em sua assinatura. Os grupos de recursos permitem agrupar os recursos do Azure para acesso e gerenciamento. Se você não tiver um grupo de recursos em sua assinatura, não será permitido criar um no portal do LUIS. Acesse [portal do Azure](https://ms.portal.azure.com/#create/Microsoft.ResourceGroup) para criar um, em seguida, vá para Luis para continuar o processo de entrada.
    * **Nome do recurso do Azure** -um nome personalizado que você escolhe, usado como parte da URL para suas transações de criação. O nome do recurso pode incluir apenas caracteres alfanuméricos, `-` e não pode iniciar ou terminar com `-` . Se quaisquer outros símbolos forem incluídos no nome, a criação de um recurso falhará.
    * **Local** -escolha para criar seus aplicativos em um dos [três locais de criação](./luis-reference-regions.md) com suporte no momento por Luis, incluindo: oeste dos EUA, Europa Ocidental e leste da Austrália
    * **Tipo de preço** – por padrão, o tipo de preço de criação F0 é selecionado como é o recomendado. Crie uma [chave gerenciada pelo cliente](./encrypt-data-at-rest.md#customer-managed-keys-for-language-understanding) do portal do Azure se você estiver procurando uma camada extra de segurança.
8. Agora você entrou com êxito no LUIS. Agora você pode começar a criar aplicativos.

## <a name="troubleshooting"></a>Solução de problemas

* Ao criar um novo recurso, verifique se o nome do recurso inclui apenas caracteres alfanuméricos, '-', e não pode iniciar ou terminar com '-'. Caso contrário, ocorrerá uma falha.
* Verifique se você tem as [permissões corretas em sua assinatura para criar um recurso do Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles). Se você não tiver as permissões apropriadas, entre em contato com o administrador da sua assinatura para fornecer permissões suficientes.

## <a name="next-steps"></a>Próximas etapas

* Saiba como [Iniciar um novo aplicativo](luis-how-to-start-new-app.md)