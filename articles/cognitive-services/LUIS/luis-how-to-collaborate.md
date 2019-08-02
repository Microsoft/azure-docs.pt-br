---
title: Colaborar com outras pessoas – LUIS
titleSuffix: Azure Cognitive Services
description: Um proprietário de aplicativo pode adicionar colaboradores ao aplicativo. Esses colaboradores podem modificar o modelo, treinar e publicar o aplicativo.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: dbc27176e7a300c0799e326acb10b99ac663fa89
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638128"
---
# <a name="how-to-manage-authors-and-collaborators"></a>Como gerenciar autores e colaboradores 

Um proprietário de aplicativo pode adicionar colaboradores ao aplicativo. Esses colaboradores podem modificar o modelo, treinar e publicar o aplicativo. 

<a name="owner-and-collaborators"></a>

## <a name="add-collaborator"></a>Adicionar colaborador

Um aplicativo tem um único autor, mas pode ter vários colaboradores. Para permitir que os colaboradores editem o aplicativo LUIS, você deve adicionar o email que eles usam para acessar o portal do LUIS à lista de colaboradores. Depois que eles são adicionados, o aplicativo aparece no portal do LUIS deles.

1. Selecione **Gerenciar** no menu superior direito e selecione **Colaboradores** no menu esquerdo.

2. Selecione **Adicionar Colaborador** na barra de ferramentas.

    [![Adicionar colaborador](./media/luis-how-to-collaborate/add-collaborator.png "Adicionar colaborador")](./media/luis-how-to-collaborate/add-collaborator.png#lightbox)

3. Insira o endereço de email que o colaborador usa para entrar no portal do LUIS.

    ![Adicionar o endereço de email do colaborado](./media/luis-how-to-collaborate/add-collaborator-pop-up.png)

## <a name="transfer-of-ownership"></a>Transferência de propriedade

Embora atualmente o LUIS não dê suporte à transferência de propriedade, você pode exportar seu aplicativo e outro usuário do LUIS pode importá-lo. Pode haver pequenas diferenças nas pontuações do LUIS entre os dois aplicativos. 

## <a name="azure-active-directory-resources"></a>Recursos do Azure Active Directory

Se você usar o [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/) (Azure AD) em sua organização, o Reconhecimento Vocal (LUIS) precisará de permissão para acessar as informações sobre o acesso de seus usuários quando eles quiserem usar o LUIS. Os recursos exigidos pelo LUIS são mínimos. 

Você vê a descrição detalhada ao tentar se inscrever com uma conta que tem consentimento do administrador ou não precisa dele, como o consentimento do administrador:

* Permite que você entre no aplicativo com sua conta organizacional e permite que o aplicativo leia seu perfil. Ele também permite que o aplicativo leia informações básicas da empresa. Isso permite ao LUIS ler dados de perfil básicos, como ID de usuário, email e nome
* Permite que o aplicativo veja e atualize seus dados, mesmo quando não estiver usando o aplicativo no momento. A permissão é necessária para atualizar o token de acesso do usuário.


## <a name="azure-active-directory-tenant-user"></a>Usuário de locatário do Azure Active Directory

LUIS usa o fluxo de autorização padrão do Azure Active Directory (Azure AD). 

O administrador do locatário deverá trabalhar diretamente com o usuário que precisa de ter o acesso concedido para usar LUIS no Azure AD. 

* Primeiro, o usuário faz logon no LUIS e verá a caixa de diálogo pop-up que necessita de aprovação do administrador. O usuário entra em contato com o administrador do locatário antes de continuar. 
* Segundo, o administrador do locatário faz logon no LUIS e verá uma caixa de diálogo de pop-up de fluxo de autorização. Esta é a caixa de diálogo que o administrador deve conceder permissão para o usuário. Depois que o administrador aceita a permissão, o usuário pode continuar com o LUIS. Se o administrador do locatário não fizer logon no LUIS, o administrador pode acessar [consentimento](https://account.activedirectory.windowsazure.com/r#/applications) para LUIS, mostrado na captura de tela a seguir. Observe que a lista é filtrada para itens que incluem o nome `LUIS`.

![Permissão do Azure Active Directory pelo website do aplicativo](./media/luis-how-to-collaborate/tenant-permissions.png)

Se o administrador de locatários desejar que somente determinados usuários usem o LUIS, haverá duas soluções possíveis:
* Fornecer o "consentimento do administrador" (consentimento a todos os usuários do Azure AD), mas, em seguida, definir como "Sim" a opção "Atribuição de usuário obrigatória" em Propriedades do Aplicativo Empresarial e, por fim, atribuir/adicionar somente os usuários desejados ao Aplicativo. Com esse método, o Administrador ainda fornece o "consentimento do administrador" ao Aplicativo, mas é possível controlar os usuários que podem acessá-lo.
* Uma segunda solução é obtida por meio da [API do Graph do Azure AD](https://docs.microsoft.com/graph/azuread-identity-access-management-concept-overview) para fornecer consentimento a cada usuário específico. 

Saiba mais sobre o consentimento e os usuários do Azure Active Directory: 
* [Restringir o aplicativo](../../active-directory/develop/howto-restrict-your-app-to-a-set-of-users.md) a um conjunto de usuários

### <a name="user-accounts-with-multiple-emails-for-collaborators"></a>Contas de usuários com vários emails para colaboradores

Se você adicionar colaboradores a um aplicativo LUIS, você está especificando o endereço de email exato que um colaborador necessita para usar LUIS como colaborador. Enquanto o Azure Active Directory (AD do Azure) permite que um único usuário tenha mais de uma conta de email usada alternadamente, LUIS exige que o usuário faça logon com o endereço de email especificado na lista do colaborador.

