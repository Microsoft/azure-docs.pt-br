---
title: Configurar um fluxo de edição de perfil
titleSuffix: Azure AD B2C
description: Saiba como configurar um fluxo de edição de perfil no Azure Active Directory B2C.
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
ms.openlocfilehash: 1cf66918d8b5211dcc03be944dbcb92cdf6e0773
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/17/2020
ms.locfileid: "97618684"
---
# <a name="set-up-a-profile-editing-flow-in-azure-active-directory-b2c"></a>Configurar um fluxo de edição de perfil no Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

## <a name="profile-editing-flow"></a>Fluxo de edição de perfil

A política de edição de perfil permite que os usuários gerenciem seus atributos de perfil, como nome de exibição, sobrenome, nome, cidade e outros. O fluxo de edição de perfil envolve as seguintes etapas: 

1. Inscrever-se ou entrar, com conta local ou social. Se a sessão ainda estiver ativa, o Azure AD B2C autoriza o usuário e pula para a próxima etapa.
1. Azure AD B2C lê o perfil do usuário do diretório e permite que o usuário edite os atributos.

![Fluxo de edição de perfil](./media/add-profile-editing-policy/profile-editing-flow.png)


## <a name="prerequisites"></a>Pré-requisitos

Se você ainda não fez isso, [registre um aplicativo Web em Azure Active Directory B2C](tutorial-register-applications.md).

::: zone pivot="b2c-user-flow"

## <a name="create-a-profile-editing-user-flow"></a>Criar um fluxo de usuário de edição de perfil

Se quiser permitir que os usuários editem seu perfil no aplicativo, use um fluxo do usuário de edição de perfil.

1. No menu da página de visão geral do locatário do Azure AD B2C, selecione **Fluxos dos usuários** e, em seguida, selecione **Novo fluxo de usuário**.
1. Na página **Criar um fluxo de usuário**, selecione o fluxo do usuário **Edição de perfil**. 
1. Em **Selecionar uma versão**, selecione **Recomendado** e, em seguida, selecione **Criar**.
1. Insira um **Nome** para o fluxo de usuário. Por exemplo, *profileediting1*.
1. Para **provedores de identidade**, selecione **entrada de email**.
1. Para **Atributos do usuário**, escolha os atributos que deseja que o cliente seja capaz de editar no perfil dele. Por exemplo, selecione **Mostrar mais** e, em seguida, escolha os atributos e as declarações para **Nome de exibição** e **Cargo**. Clique em **OK**.
1. Clique em **Criar** para adicionar o fluxo de usuário. Um prefixo *B2C_1* é acrescentado automaticamente ao nome.

### <a name="test-the-user-flow"></a>Testar o fluxo de usuário

1. Selecione o fluxo de usuário que você criou para abrir a página de visão geral e, em seguida, selecione **Executar fluxo de usuário**.
1. Para **Aplicativo**, selecione o aplicativo Web denominado *webapp1* que você registrou anteriormente. A **URL de resposta** deve mostrar `https://jwt.ms`.
1. Clique em **Executar o fluxo do usuário** e, em seguida, entre com a conta que criou anteriormente.
1. Agora, você tem a oportunidade de alterar o nome de exibição e o cargo do usuário. Clique em **Continuar**. O token é retornado para `https://jwt.ms` e deve ser exibido para você.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-profile-editing-policy"></a>Criar uma política de edição de perfil

As políticas personalizadas são um conjunto de arquivos XML que você carrega em seu locatário Azure AD B2C para definir percursos do usuário. Fornecemos pacotes de início com várias políticas predefinidas, incluindo: inscrição e entrada, redefinição de senha e política de edição de perfil. Para obter mais informações, consulte Introdução [às políticas personalizadas no Azure ad B2C](custom-policy-get-started.md).

::: zone-end

## <a name="next-steps"></a>Próximas etapas

* Adicione uma [entrada com o provedor de identidade social](add-identity-provider.md).