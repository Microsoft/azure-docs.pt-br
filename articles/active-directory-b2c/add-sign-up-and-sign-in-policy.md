---
title: Configurar um fluxo de inscrição e entrada
titleSuffix: Azure AD B2C
description: Saiba como configurar um fluxo de inscrição e entrada no Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/16/2020
ms.author: mimart
ms.subservice: B2C
ms.custom: b2c-support
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: fbcdef0002a227d5319fc01e625146480e4c99fc
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105043743"
---
# <a name="set-up-a-sign-up-and-sign-in-flow-in-azure-active-directory-b2c"></a>Configurar um fluxo de inscrição e entrada no Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

## <a name="sign-up-and-sign-in-flow"></a>Fluxo de inscrição e entrada

A política de inscrição e entrada permite que os usuários: 

* Inscrever-se com a conta local
* Entrar com a conta local
* Inscrever-se ou entrar com uma conta social
* Redefinição de senha

![Fluxo de edição de perfil](./media/add-sign-up-and-sign-in-policy/add-sign-up-and-sign-in-flow.png)

## <a name="prerequisites"></a>Pré-requisitos

Se você ainda não fez isso, [registre um aplicativo Web em Azure Active Directory B2C](tutorial-register-applications.md).

::: zone pivot="b2c-user-flow"

## <a name="create-a-sign-up-and-sign-in-user-flow"></a>Criar um fluxo de usuário de inscrição e de entrada

O fluxo do usuário de inscrição e credenciais controla as experiências de inscrição e credenciais do consumidor com uma única configuração. Os usuários do aplicativo são conduzidos pelo caminho certo, de acordo com o contexto.

1. Entre no [portal do Azure](https://portal.azure.com).
1. Selecione o ícone **Diretório + Assinatura** na barra de ferramentas do portal e selecione o diretório que contém o locatário do Azure AD B2C.
1. No portal do Azure, pesquise e selecione **Azure AD B2C**.
1. Em **Políticas**, selecione **Fluxos dos usuários** e, em seguida, selecione **Novo fluxo de usuário**.

    ![Página fluxos dos usuários no portal com o botão Novo fluxo de usuário realçado](./media/add-sign-up-and-sign-in-policy/signup-signin-user-flow.png)

1. Na página **Criar um fluxo de usuário**, selecione o fluxo do usuário **Inscrever-se e entrar**.

    ![Página Selecione um fluxo de usuário com o fluxo Inscrever-se e entrar realçado](./media/add-sign-up-and-sign-in-policy/select-user-flow-type.png)

1. Em **Selecionar uma versão**, selecione **Recomendado** e, em seguida, selecione **Criar**. ([Saiba mais](user-flow-versions.md) sobre as versões de fluxo de usuário.)

    ![Página Criar fluxo de usuário no portal do Azure com as propriedades realçadas](./media/add-sign-up-and-sign-in-policy/select-version.png)

1. Insira um **Nome** para o fluxo de usuário. Por exemplo, *signupsignin1*.
1. Para **provedores de identidade**, selecione **inscrição de email**.
1. Para **Atributos e declarações do usuário**, escolha as declarações e atributos que deseja coletar e enviar do usuário durante a inscrição. Por exemplo, selecione **Mostrar mais** e, depois, escolha atributos e declarações de **País/região**, **Nome de exibição** e **Código postal**. Clique em **OK**.

    ![Página Seleção de atributos e declarações com três declarações selecionadas](./media/add-sign-up-and-sign-in-policy/signup-signin-attributes.png)

1. Clique em **Criar** para adicionar o fluxo de usuário. Um prefixo *B2C_1* é anexado automaticamente ao nome.
2. Siga as etapas para [lidar com o fluxo para "esqueceu sua senha?"](add-password-reset-policy.md?pivots=b2c-user-flow.md#self-service-password-reset-recommended) na política de inscrição ou entrada.

### <a name="test-the-user-flow"></a>Testar o fluxo de usuário

1. Selecione o fluxo de usuário que você criou para abrir a página de visão geral e, em seguida, selecione **Executar fluxo de usuário**.
1. Para **Aplicativo**, selecione o aplicativo Web denominado *webapp1* que você registrou anteriormente. A **URL de resposta** deve mostrar `https://jwt.ms`.
1. Clique em **Executar o fluxo do usuário** e, em seguida, selecione **Inscreva-se agora**.

    ![Página Executar fluxo de usuários no portal com o botão Executar fluxo de usuário realçado](./media/add-sign-up-and-sign-in-policy/signup-signin-run-now.png)

1. Insira um endereço de email válido, clique em **Enviar código de verificação**, insira o código de verificação que você receber e, em seguida selecione **Verificar código**.
1. Insira uma nova senha e confirme-a.
1. Selecione seu país e região, insira o nome que você deseja que seja exibido, insira um código postal e, em seguida, clique em **Criar**. O token é retornado para `https://jwt.ms` e deve ser exibido para você.
1. Agora, você pode executar o fluxo do usuário novamente e poderá se conectar com a conta que você criou. O token retornado inclui as declarações que você selecionou de país/região, nome e código postal.

> [!NOTE]
> No momento, a experiência "Executar fluxo de usuário" não é compatível com o tipo de URL de resposta SPA que usa o fluxo de código de autorização. Para usar a experiência "Executar fluxo de usuário" com esses tipos de aplicativos, registre uma URL de resposta do tipo "Web" e habilite o fluxo implícito conforme descrito [aqui](tutorial-register-spa.md).

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-sign-up-and-sign-in-policy"></a>Criar uma política de inscrição e entrada

As políticas personalizadas são um conjunto de arquivos XML que você carrega em seu locatário Azure AD B2C para definir percursos do usuário. Fornecemos pacotes de início com várias políticas predefinidas, incluindo: inscrição e entrada, redefinição de senha e política de edição de perfil. Para obter mais informações, consulte Introdução [às políticas personalizadas no Azure ad B2C](custom-policy-get-started.md).

::: zone-end

## <a name="next-steps"></a>Próximas etapas

* Adicione uma [entrada com o provedor de identidade social](add-identity-provider.md).
* Configure um [fluxo de redefinição de senha](add-password-reset-policy.md).
