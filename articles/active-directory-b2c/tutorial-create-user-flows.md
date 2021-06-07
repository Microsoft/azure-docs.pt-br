---
title: Tutorial – Criar fluxos dos usuários – Azure Active Directory B2C
description: Siga este tutorial para aprender a criar fluxos dos usuários no portal do Azure para habilitar a inscrição, a entrada e a edição de perfil do usuário para seus aplicativos no Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 03/22/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c42c6465af8e895d833332be847c134b97ee8ddc
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104781289"
---
# <a name="tutorial-create-user-flows-in-azure-active-directory-b2c"></a>Tutorial: Criar fluxos dos usuários no Azure Active Directory B2C

Nos seus aplicativos, pode haver [fluxos dos usuários](user-flow-overview.md) que permitem que os usuários inscrevam-se, conectem-se ou gerenciem seus perfis. É possível criar vários fluxos dos usuários de diferentes tipos no locatário do Azure AD B2C (Azure Active Directory B2C) e usá-los nos aplicativos, conforme necessário. Fluxos dos usuários podem ser reutilizados nos aplicativos.

Neste artigo, você aprenderá como:

> [!div class="checklist"]
> * Criar um fluxo de usuário de inscrição e de entrada
> * Habilitar a redefinição de senha por autoatendimento
> * Criar um fluxo de usuário de edição de perfil


Este tutorial mostra como criar alguns fluxos dos usuários recomendados usando o portal do Azure. Se você estiver procurando informações sobre como configurar um fluxo de ROPC (credenciais de senha de proprietário do recurso) em seu aplicativo, confira [Configurar o fluxo de credenciais de senha de proprietário do recurso no Azure AD B2C](add-ropc-policy.md).

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

> [!IMPORTANT]
> Alteramos a maneira como fazemos referência às versões de fluxo do usuário. Anteriormente, oferecíamos versões V1 (prontas para produção) e versões V1.1 e V2 (versão prévia). Agora, consolidamos os fluxos dos usuários nas versões **Recomendado** (versão prévia da próxima geração) e **Standard** (em disponibilidade geral). Todos os fluxos dos usuários da versão prévia herdada da V1.1 e da V2 serão preteridos até **1º de agosto de 2021**. Para obter detalhes, consulte as [Versões de fluxo de usuário no Azure AD B2C](user-flow-versions.md).

## <a name="prerequisites"></a>Pré-requisitos

[Registre seus aplicativos](tutorial-register-applications.md) que fazem parte dos fluxos dos usuários que deseja criar.

## <a name="create-a-sign-up-and-sign-in-user-flow"></a>Criar um fluxo de usuário de inscrição e de entrada

O fluxo do usuário de inscrição e credenciais controla as experiências de inscrição e credenciais do consumidor com uma única configuração. Os usuários do aplicativo são conduzidos pelo caminho certo, de acordo com o contexto.

1. Entre no [portal do Azure](https://portal.azure.com).
1. Selecione o ícone **Diretório + Assinatura** na barra de ferramentas do portal e selecione o diretório que contém o locatário do Azure AD B2C.

    ![Locatário do B2C, painel Diretório e Assinatura, portal do Azure](./media/tutorial-create-user-flows/directory-subscription-pane.png)

1. No portal do Azure, pesquise e selecione **Azure AD B2C**.
1. Em **Políticas**, selecione **Fluxos dos usuários** e, em seguida, selecione **Novo fluxo de usuário**.

    ![Página fluxos dos usuários no portal com o botão Novo fluxo de usuário realçado](./media/tutorial-create-user-flows/signup-signin-user-flow.png)

1. Na página **Criar um fluxo de usuário**, selecione o fluxo do usuário **Inscrever-se e entrar**.

    ![Página Selecione um fluxo de usuário com o fluxo Inscrever-se e entrar realçado](./media/tutorial-create-user-flows/select-user-flow-type.png)

1. Em **Selecionar uma versão**, selecione **Recomendado** e, em seguida, selecione **Criar**. ([Saiba mais](user-flow-versions.md) sobre as versões de fluxo de usuário.)

    ![Página Criar fluxo de usuário no portal do Azure com as propriedades realçadas](./media/tutorial-create-user-flows/select-version.png)

1. Insira um **Nome** para o fluxo de usuário. Por exemplo, *signupsignin1*.
1. Para **Provedores de identidade**, selecione **Inscrição por email**.
1. Para **Atributos e declarações do usuário**, escolha as declarações e atributos que deseja coletar e enviar do usuário durante a inscrição. Por exemplo, selecione **Mostrar mais** e, depois, escolha atributos e declarações de **País/região**, **Nome de exibição** e **Código postal**. Clique em **OK**.

    ![Página Seleção de atributos e declarações com três declarações selecionadas](./media/tutorial-create-user-flows/signup-signin-attributes.png)

1. Clique em **Criar** para adicionar o fluxo de usuário. Um prefixo *B2C_1* é anexado automaticamente ao nome.

### <a name="test-the-user-flow"></a>Testar o fluxo de usuário

1. Selecione o fluxo de usuário que você criou para abrir a página de visão geral e, em seguida, selecione **Executar fluxo de usuário**.
1. Para **Aplicativo**, selecione o aplicativo Web denominado *webapp1* que você registrou anteriormente. A **URL de resposta** deve mostrar `https://jwt.ms`.
1. Clique em **Executar o fluxo do usuário** e, em seguida, selecione **Inscreva-se agora**.

    ![Página Executar fluxo de usuários no portal com o botão Executar fluxo de usuário realçado](./media/tutorial-create-user-flows/signup-signin-run-now.PNG)

1. Insira um endereço de email válido, clique em **Enviar código de verificação**, insira o código de verificação que você receber e, em seguida selecione **Verificar código**.
1. Insira uma nova senha e confirme-a.
1. Selecione seu país e região, insira o nome que você deseja que seja exibido, insira um código postal e, em seguida, clique em **Criar**. O token é retornado para `https://jwt.ms` e deve ser exibido para você.
1. Agora, você pode executar o fluxo do usuário novamente e poderá se conectar com a conta que você criou. O token retornado inclui as declarações que você selecionou de país/região, nome e código postal.

> [!NOTE]
> No momento, a experiência "Executar fluxo de usuário" não é compatível com o tipo de URL de resposta SPA que usa o fluxo de código de autorização. Para usar a experiência "Executar fluxo de usuário" com esses tipos de aplicativos, registre uma URL de resposta do tipo "Web" e habilite o fluxo implícito conforme descrito [aqui](tutorial-register-spa.md).

## <a name="enable-self-service-password-reset"></a>Habilitar a redefinição de senha por autoatendimento

Para habilitar a [redefinição de senha por autoatendimento](add-password-reset-policy.md) para o fluxo de usuários de inscrição ou de entrada:

1. Selecione o fluxo de usuários de inscrição ou de entrada criado.
1. Em **Configurações** no menu esquerdo, selecione **Propriedades**.
1. Em **Complexidade da senha**, selecione **Redefinição de senha por autoatendimento**.
1. Clique em **Salvar**.

### <a name="test-the-user-flow"></a>Testar o fluxo de usuário

1. Selecione o fluxo de usuário que você criou para abrir a página de visão geral e, em seguida, selecione **Executar fluxo de usuário**.
1. Para **Aplicativo**, selecione o aplicativo Web denominado *webapp1* que você registrou anteriormente. A **URL de resposta** deve mostrar `https://jwt.ms`.
1. Selecione **Executar fluxo de usuário**.
1. Na página de inscrição ou de entrada, escolha **Esqueceu sua senha?** .
1. Verifique o endereço de email da conta que você criou anteriormente e selecione **Continuar**.
1. Agora, você pode alterar a senha para o usuário. Altere a senha e selecione **Continuar**. O token é retornado para `https://jwt.ms` e deve ser exibido para você.

## <a name="create-a-profile-editing-user-flow"></a>Criar um fluxo de usuário de edição de perfil

Se quiser permitir que os usuários editem seu perfil no aplicativo, use um fluxo do usuário de edição de perfil.

1. No menu da página de visão geral do locatário do Azure AD B2C, selecione **Fluxos dos usuários** e, em seguida, selecione **Novo fluxo de usuário**.
1. Na página **Criar um fluxo de usuário**, selecione o fluxo do usuário **Edição de perfil**. 
1. Em **Selecionar uma versão**, selecione **Recomendado** e, em seguida, selecione **Criar**.
1. Insira um **Nome** para o fluxo de usuário. Por exemplo, *profileediting1*.
1. Para **Provedores de identidade**, selecione **Conexão na Conta Local**.
2. Para **Atributos do usuário**, escolha os atributos que deseja que o cliente seja capaz de editar no perfil dele. Por exemplo, selecione **Mostrar mais** e, em seguida, escolha os atributos e as declarações para **Nome de exibição** e **Cargo**. Clique em **OK**.
3. Clique em **Criar** para adicionar o fluxo de usuário. Um prefixo *B2C_1* é acrescentado automaticamente ao nome.

### <a name="test-the-user-flow"></a>Testar o fluxo de usuário

1. Selecione o fluxo de usuário que você criou para abrir a página de visão geral e, em seguida, selecione **Executar fluxo de usuário**.
1. Para **Aplicativo**, selecione o aplicativo Web denominado *webapp1* que você registrou anteriormente. A **URL de resposta** deve mostrar `https://jwt.ms`.
1. Clique em **Executar o fluxo do usuário** e, em seguida, entre com a conta que criou anteriormente.
1. Agora, você tem a oportunidade de alterar o nome de exibição e o cargo do usuário. Clique em **Continuar**. O token é retornado para `https://jwt.ms` e deve ser exibido para você.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a:

> [!div class="checklist"]
> * Criar um fluxo de usuário de inscrição e de entrada
> * Criar um fluxo de usuário de edição de perfil
> * Criar um fluxo de usuário de redefinição de senha

Em seguida, saiba como usar o Azure AD B2C para conectar e inscrever usuários em um aplicativo. Siga o aplicativo Web ASP.NET vinculado abaixo ou navegue para outro aplicativo no sumário em **Autenticar usuários**.

> [!div class="nextstepaction"]
> [Tutorial: Habilitar a autenticação em um aplicativo Web usando o Azure AD B2C >](tutorial-web-app-dotnet.md)
