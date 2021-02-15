---
title: Configurar um fluxo de redefinição de senha
titleSuffix: Azure AD B2C
description: Saiba como configurar um fluxo de redefinição de senha no Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/16/2020
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 6e6e4aa4ece781f415308b638323f8d4d4b34038
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/17/2020
ms.locfileid: "97618686"
---
# <a name="set-up-a-password-reset-flow-in-azure-active-directory-b2c"></a>Configurar um fluxo de redefinição de senha no Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

## <a name="password-rest-flow"></a>Fluxo REST de senha

A política de redefinição de senha permite que os usuários redefinam sua própria senha esquecida. O fluxo de redefinição de senha envolve as seguintes etapas: 
1. Na página de inscrição e entrada, o usuário clica em "esqueceu sua senha?" . Azure AD B2C retorna o código de erro AADB2C90118 para seu aplicativo. O aplicativo manipula esse código de erro invocando a política de redefinição de senha. 
1. Os usuários fornecem e verificam seus emails com uma senha de tempo única.
1. Insira uma nova senha.

![Fluxo de redefinição de senha](./media/add-password-reset-policy/password-reset-flow.png)

## <a name="prerequisites"></a>Pré-requisitos

Se você ainda não fez isso, [registre um aplicativo Web em Azure Active Directory B2C](tutorial-register-applications.md).

::: zone pivot="b2c-user-flow"

## <a name="create-a-password-reset-user-flow"></a>Criar um fluxo de usuário de redefinição de senha

Para permitir que os usuários do seu aplicativo redefinam a senha, use um fluxo de usuário de redefinição de senha.

1. No menu de visão geral do locatário do Azure AD B2C, selecione **Fluxos dos usuários** e, em seguida, selecione **Novo fluxo de usuário**.
1. Na página **Criar um fluxo de usuário**, selecione o fluxo do usuário **Redefinição de senha**. 
1. Em **Selecionar uma versão**, selecione **Recomendado** e, em seguida, selecione **Criar**.
1. Insira um **Nome** para o fluxo de usuário. Por exemplo, *passwordreset1*.
1. Em **Provedores de identidade**, habilite **Redefinir senha usando endereço de email**.
2. Em Declarações do aplicativo, clique em **Mostrar mais** e escolha as declarações que você quer retornar nos tokens de autorização enviados de volta ao seu aplicativo. Por exemplo, selecione **ID de Objeto do Usuário**.
3. Clique em **OK**.
4. Clique em **Criar** para adicionar o fluxo de usuário. Um prefixo *B2C_1* é acrescentado automaticamente ao nome.

### <a name="test-the-user-flow"></a>Testar o fluxo de usuário

1. Selecione o fluxo de usuário que você criou para abrir a página de visão geral e, em seguida, selecione **Executar fluxo de usuário**.
1. Para **Aplicativo**, selecione o aplicativo Web denominado *webapp1* que você registrou anteriormente. A **URL de resposta** deve mostrar `https://jwt.ms`.
1. Clique em **Executar fluxo de usuário**, verifique o endereço de email da conta que você criou anteriormente e selecione **Continuar**.
1. Agora, você pode alterar a senha para o usuário. Altere a senha e selecione **Continuar**. O token é retornado para `https://jwt.ms` e deve ser exibido para você.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-password-reset-policy"></a>Criar uma política de redefinição de senha

As políticas personalizadas são um conjunto de arquivos XML que você carrega em seu locatário Azure AD B2C para definir percursos do usuário. Fornecemos pacotes de início com várias políticas predefinidas, incluindo: inscrição e entrada, redefinição de senha e política de edição de perfil. Para obter mais informações, consulte Introdução [às políticas personalizadas no Azure ad B2C](custom-policy-get-started.md).

::: zone-end

## <a name="next-steps"></a>Próximas etapas

Configure um [fluxo de edição de perfil](add-profile-editing-policy.md).