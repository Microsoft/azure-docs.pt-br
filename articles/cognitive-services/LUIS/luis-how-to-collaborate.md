---
title: Colaborar com outras pessoas – LUIS
titleSuffix: Azure Cognitive Services
description: Um proprietário de aplicativo pode adicionar colaboradores ao recurso de criação. Esses colaboradores podem modificar o modelo, treinar e publicar o aplicativo.
services: cognitive-services
author: aahill
ms.author: aahi
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 01/21/2021
ms.openlocfilehash: 5ca13784fe2f9a6a5b448bc838bf508f01b0a9fe
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/18/2021
ms.locfileid: "101095188"
---
# <a name="add-contributors-to-your-app"></a>Adicionar colaboradores ao seu aplicativo

Um proprietário de aplicativo pode adicionar colaboradores a aplicativos. Esses colaboradores podem modificar o modelo, treinar e publicar o aplicativo. Depois de [migrar](luis-migration-authoring.md) sua conta, os _colaboradores_ são gerenciados no portal do Azure para o recurso de criação, usando a página de **controle de acesso (iam)** . Adicione um usuário, usando o endereço de email da colaboração e a função de _colaborador_ .

## <a name="add-contributor-to-azure-authoring-resource"></a>Adicionar colaborador ao recurso de criação do Azure

Você migrou se sua experiência de criação do LUIS está vinculada a um recurso de criação na página **gerenciar recursos do Azure->** no portal do Luis.

1. Na portal do Azure, localize o recurso de criação de Reconhecimento vocal (LUIS). Ele tem o tipo `LUIS.Authoring` .
1. Na página controle de **acesso deste recurso (iam)** , selecione **+ Adicionar** e selecione **Adicionar atribuição de função**.

    ![Em portal do Azure, adicione atribuição de função ao recurso de criação.](./media/luis-how-to-collaborate/authoring-resource-access-control-add-role.png)

1. Na janela **Adicionar atribuição de função** , selecione a **função** de colaborador. Na opção **atribuir acesso a** , selecione **usuário, grupo ou entidade de serviço do Azure ad**. Na opção **selecionar** , insira o endereço de email do usuário. Se o usuário for conhecido por mais de um endereço de email para o mesmo domínio, certifique-se de inserir a conta de email _principal_ .

    ![Adicionar email do usuário à função colaborador do Azure AD](./media/luis-how-to-collaborate/add-role-assignment-for-contributor.png)

    Quando o email do usuário for encontrado, selecione a conta e selecione **salvar**.

    Se você tiver problemas com essa atribuição de função, examine [atribuir funções do Azure](../../role-based-access-control/role-assignments-portal.md) e [solução de problemas de controle de acesso do Azure](../../role-based-access-control/troubleshooting.md#problems-with-azure-role-assignments).

## <a name="view-the-app-as-a-contributor"></a>Exibir o aplicativo como um colaborador

Depois que você tiver sido adicionado como um colaborador, [entre no portal do Luis](sign-in-luis-portal.md).

[!INCLUDE [switch azure directories](includes/switch-azure-directories.md)]

### <a name="users-with-multiple-emails"></a>Usuários com vários emails

Se você adicionar colaboradores a um aplicativo LUIS, você estará especificando o endereço de email exato. Embora Azure Active Directory (Azure AD) permita que um único usuário tenha mais de uma conta de email usada de forma intercambiável, o LUIS exige que o usuário entre com o endereço de email especificado ao adicionar o colaborador.

<a name="owner-and-collaborators"></a>

### <a name="azure-active-directory-resources"></a>Recursos do Azure Active Directory

Se você usar o [Azure Active Directory](../../active-directory/index.yml) (Azure AD) em sua organização, o Reconhecimento Vocal (LUIS) precisará de permissão para acessar as informações sobre o acesso de seus usuários quando eles quiserem usar o LUIS. Os recursos exigidos pelo LUIS são mínimos.

Você vê a descrição detalhada ao tentar se inscrever com uma conta que tem consentimento do administrador ou não precisa dele, como o consentimento do administrador:

* Permite que você entre no aplicativo com sua conta organizacional e permite que o aplicativo leia seu perfil. Ele também permite que o aplicativo leia informações básicas da empresa. Isso permite ao LUIS ler dados de perfil básicos, como ID de usuário, email e nome
* Permite que o aplicativo veja e atualize seus dados, mesmo quando não estiver usando o aplicativo no momento. A permissão é necessária para atualizar o token de acesso do usuário.


### <a name="azure-active-directory-tenant-user"></a>Usuário de locatário do Azure Active Directory

LUIS usa o fluxo de autorização padrão do Azure Active Directory (Azure AD).

O administrador do locatário deverá trabalhar diretamente com o usuário que precisa de ter o acesso concedido para usar LUIS no Azure AD.

* Primeiro, o usuário faz logon no LUIS e verá a caixa de diálogo pop-up que necessita de aprovação do administrador. O usuário entra em contato com o administrador do locatário antes de continuar.
* Segundo, o administrador do locatário faz logon no LUIS e verá uma caixa de diálogo de pop-up de fluxo de autorização. Esta é a caixa de diálogo que o administrador deve conceder permissão para o usuário. Depois que o administrador aceita a permissão, o usuário pode continuar com o LUIS. Se o administrador do locatário não fizer logon no LUIS, o administrador pode acessar [consentimento](https://account.activedirectory.windowsazure.com/r#/applications) para LUIS. Nessa página, você pode filtrar a lista para itens que incluem o nome `LUIS` .

Se o administrador de locatários desejar que somente determinados usuários usem o LUIS, haverá duas soluções possíveis:
* Fornecer o "consentimento do administrador" (consentimento a todos os usuários do Azure AD), mas, em seguida, definir como "Sim" a opção "Atribuição de usuário obrigatória" em Propriedades do Aplicativo Empresarial e, por fim, atribuir/adicionar somente os usuários desejados ao Aplicativo. Com esse método, o Administrador ainda fornece o "consentimento do administrador" ao Aplicativo, mas é possível controlar os usuários que podem acessá-lo.
* Uma segunda solução é usar a API de [Gerenciamento de acesso e identidade do Azure AD no Microsoft Graph](/graph/azuread-identity-access-management-concept-overview) para fornecer consentimento para cada usuário específico.

Saiba mais sobre o consentimento e os usuários do Azure Active Directory:
* [Restringir seu aplicativo](../../active-directory/develop/howto-restrict-your-app-to-a-set-of-users.md) a um conjunto de usuários

## <a name="next-steps"></a>Próximas etapas

* Saiba [como usar versões](luis-how-to-manage-versions.md) para controlar o ciclo de vida do aplicativo.
* Entenda os conceitos, incluindo o [recurso de criação](luis-how-to-azure-subscription.md#authoring-key) e os [colaboradores](luis-how-to-azure-subscription.md#contributions-from-other-authors) nesse recurso.
* Saiba [como criar](luis-how-to-azure-subscription.md) recursos de criação e tempo de execução
* Migrar para o novo [recurso de criação](luis-migration-authoring.md)