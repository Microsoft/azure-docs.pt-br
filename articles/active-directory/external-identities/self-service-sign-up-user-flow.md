---
title: Adicionar um fluxo dos usuários de inscrição por autoatendimento — Azure AD
description: Crie fluxos de usuário para aplicativos que são criados por sua organização. Em seguida, os usuários que visitam esse aplicativo podem obter uma conta de convidado usando as opções configuradas no fluxo do usuário.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 03/02/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a59e573c9b24d9a8b5577b55d143fcaca67952f0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102120752"
---
# <a name="add-a-self-service-sign-up-user-flow-to-an-app"></a>Adicionar um fluxo do usuário de inscrição por autoatendimento a um aplicativo

> [!NOTE]
> Alguns dos recursos mencionados neste artigo são recursos de visualização pública do Azure Active Directory. Para saber mais sobre versões prévias, consulte os [Termos de Uso Complementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Para aplicativos que você cria, você pode criar fluxos de usuário que permitem que um usuário se inscreva em um aplicativo e crie uma nova conta de convidado. Um fluxo de usuário de inscrição de autoatendimento define a série de etapas que o usuário seguirá durante a inscrição, os provedores de identidade que você permitirá que eles usem e os atributos de usuário que você deseja coletar. Você pode associar um ou mais aplicativos a um único fluxo de usuário.

> [!NOTE]
> Você pode associar os fluxos de usuários com aplicativos criados por sua organização. Os fluxos de usuários não podem ser usados para aplicativos da Microsoft, como SharePoint ou Teams.

## <a name="before-you-begin"></a>Antes de começar

### <a name="add-identity-providers-optional"></a>Adicionar provedores de identidade (opcional)

O Azure AD é o provedor de identidade padrão para inscrição de autoatendimento. Isso significa que os usuários podem se inscrever por padrão com uma conta do Azure AD. Em seus fluxos de usuário de inscrição de autoatendimento, você também pode incluir provedores de identidade social, como Google e Facebook, conta da Microsoft (versão prévia) e senha de uso único de email (versão prévia).

- [Provedor de identidade da conta da Microsoft (versão prévia)](microsoft-account.md)
- [Autenticação de senha de uso único de email](one-time-passcode.md)
- [Adicionar o Facebook à sua lista de provedores de identidade social](facebook-federation.md)
- [Adicionar o Google à sua lista de provedores de identidade social](google-federation.md)

### <a name="define-custom-attributes-optional"></a>Definir atributos personalizados (opcional)

Os atributos de usuário são valores coletados do usuário durante a inscrição de autoatendimento. O Azure AD vem com um conjunto interno de atributos, mas você pode criar atributos personalizados para uso em seu fluxo de usuário. Você também pode ler e gravar esses atributos usando a API do Microsoft Graph. Consulte [ atributos personalizados para fluxos de usuário](user-flow-add-custom-attributes.md).

## <a name="enable-self-service-sign-up-for-your-tenant"></a>Habilitar inscrição de autoatendimento para seu locatário

Para poder adicionar um fluxo de usuário de inscrição de autoatendimento aos seus aplicativos você precisa habilitar o recurso para seu locatário. Depois de habilitado, os controles ficam disponíveis no fluxo do usuário que permite associar o fluxo do usuário a um aplicativo.

1. Entre no [Portal do Azure](https://portal.azure.com) como administrador do Microsoft Azure AD.
2. Em **Serviços do Azure**, selecione **Azure Active Directory**.
3. Selecione **Configurações de usuário** e, em **Usuários externos**, selecione **Gerenciar configurações de colaboração externa**.
4. Defina a opção **habilitar inscrição de autoatendimento de convidado via fluxos de usuário** para **Sim**.

   ![Habilitar inscrição de autoatendimento de convidado](media/self-service-sign-up-user-flow/enable-self-service-sign-up.png)
5. Clique em **Salvar**.
## <a name="create-the-user-flow-for-self-service-sign-up"></a>Criar o fluxo de usuário para inscrição de autoatendimento

Em seguida, você criará o fluxo de usuário para inscrição de autoatendimento e o adicionará a um aplicativo.

1. Entre no [Portal do Azure](https://portal.azure.com) como administrador do Microsoft Azure AD.
2. Em **Serviços do Azure**, selecione **Azure Active Directory**.
3. No menu à esquerda, selecione **Identidades Externas**.
4. Selecione **fluxos de usuário** e, em seguida, selecione **novo fluxo de usuário**.

   ![Botão Adicionar um novo fluxo de usuário](media/self-service-sign-up-user-flow/new-user-flow.png)

5. Na página **Criar**, insira um **Nome** para o fluxo de usuário. Observe que o nome é automaticamente prefixado com **B2X_1_** .
6. Na lista **Provedores de identidade**, selecione um ou mais provedores de identidade que os usuários externos podem usar para fazer logon em seu aplicativo. A **inscrição no Azure Active Directory** é selecionada por padrão. (Consulte [Antes de começar](#before-you-begin) no início deste artigo para aprender a adicionar provedores de identidade.)
7. Em **Atributos de usuário**, escolha os atributos que você deseja coletar do usuário. Para obter atributos adicionais, selecione **Mostrar mais**. Por exemplo, selecione **Mostrar mais** e, depois, escolha atributos e declarações de **País/região**, **Nome de exibição** e **Código postal**. Selecione **OK**.

   ![Criar uma nova página de fluxo de usuário](media/self-service-sign-up-user-flow/create-user-flow.png)

> [!NOTE]
> Você só pode coletar atributos quando um usuário se inscreve pela primeira vez. Depois que um usuário se inscreve, ele não será mais solicitado a coletar informações de atributo, mesmo se você alterar o fluxo do usuário.

8. Selecione **Criar**.
9. O novo fluxo de usuário aparece na lista **fluxos de usuário** . Se necessário, atualize a página.

## <a name="select-the-layout-of-the-attribute-collection-form"></a>Selecionar o layout do formulário de coleção de atributos

Você pode escolher a ordem na qual os atributos são exibidos na página de inscrição. 

1. No [portal do Azure](https://portal.azure.com), selecione **Azure Active Directory**.
2. Selecione **identidades externas**, selecione **fluxos de usuário**.
3. Selecione o fluxo de usuário de inscrição por autoatendimento na lista.
4. Em **Personalizar**, selecione **Layouts da página**.
5. Os atributos que você escolheu coletar estarão listados. Para alterar a ordem de exibição, selecione um atributo e, em seguida, selecione **Mover para cima**, **Mover para baixo**, **Mover para a parte superior** ou **Mover para a parte inferior**.
6. Clique em **Salvar**.

## <a name="add-applications-to-the-self-service-sign-up-user-flow"></a>Adicionar aplicativos ao fluxo de usuário de inscrição de autoatendimento

Agora você pode associar aplicativos ao fluxo do usuário.

1. Entre no [Portal do Azure](https://portal.azure.com) como administrador do Microsoft Azure AD.
2. Em **Serviços do Azure**, selecione **Azure Active Directory**.
3. No menu à esquerda, selecione **Identidades Externas**.
4. Em **inscrição de autoatendimento**, selecione **fluxos de usuário**.
5. Selecione o fluxo de usuário de inscrição por autoatendimento na lista.
6. No menu à esquerda, em **Usar**, selecione **Aplicativos**.
7. Escolha **Adicionar aplicativo**.

   ![Como atribuir um aplicativo ao fluxo de usuário](media/self-service-sign-up-user-flow/assign-app-to-user-flow.png)

8. Selecione o aplicativo na lista. Ou use a caixa de pesquisa para localizar o aplicativo e, em seguida, selecione-o.
9. Clique em **Selecionar**.

## <a name="next-steps"></a>Próximas etapas

- [Adicionar o Google à sua lista de provedores de identidade social](google-federation.md)
- [Adicionar o Facebook à sua lista de provedores de identidade social](facebook-federation.md)
- [Use conectores de API para personalizar e estender seus fluxos de usuário por meio de APIs da Web](api-connectors-overview.md)
- [Adicionar fluxo de trabalho de aprovação personalizado ao seu fluxo de usuário](self-service-sign-up-add-approvals.md)
