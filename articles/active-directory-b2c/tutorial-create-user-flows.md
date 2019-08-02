---
title: Tutorial – criar fluxos de usuário-Azure Active Directory B2C
description: Saiba como criar fluxos de usuário no portal do Azure para habilitar a inscrição, a entrada e a edição de perfil de usuário para seus aplicativos no Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 06/07/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 130186af13b9207326cd52b46b880c1f13a16100
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2019
ms.locfileid: "67848086"
---
# <a name="tutorial-create-user-flows-in-azure-active-directory-b2c"></a>Tutorial: Criar fluxos dos usuários no Azure Active Directory B2C

Em seus aplicativos, você pode ter [fluxos de usuário](active-directory-b2c-reference-policies.md) que permitem que os usuários se inscrevam, entrem ou gerenciem seu perfil. É possível criar vários fluxos dos usuários de diferentes tipos no locatário do Azure AD (Azure Active Directory) B2C e usá-los nos aplicativos, conforme necessário. Fluxos dos usuários podem ser reutilizados nos aplicativos.

Neste artigo, você aprenderá a:

> [!div class="checklist"]
> * Criar um fluxo de usuário de inscrição e de entrada
> * Criar um fluxo de usuário de edição de perfil
> * Criar um fluxo de usuário de redefinição de senha

Este tutorial mostra como criar alguns fluxos dos usuários recomendados usando o portal do Azure. Se você estiver procurando informações sobre como configurar um fluxo de credenciais de senha de proprietário de recurso (ROPC) em seu aplicativo, consulte [Configurar o fluxo de credenciais de senha do proprietário do recurso em Azure ad B2C](configure-ropc.md).

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

[Registre seus aplicativos](tutorial-register-applications.md) que fazem parte dos fluxos dos usuários que deseja criar.

## <a name="create-a-sign-up-and-sign-in-user-flow"></a>Criar um fluxo de usuário de inscrição e de entrada

O fluxo do usuário de inscrição e credenciais controla as experiências de inscrição e credenciais do consumidor com uma única configuração. Os usuários do aplicativo são conduzidos pelo caminho certo, de acordo com o contexto.

1. Entre no [Portal do Azure](https://portal.azure.com).
1. Verifique se você está usando o diretório que contém o locatário do Azure AD B2C clicando no **filtro Diretório e assinatura** no menu superior e escolhendo o diretório que contém seu locatário.

    ![Filtro de diretório e assinatura com locatário B2C selecionado](./media/tutorial-create-user-flows/switch-directories.PNG)

1. Escolha **Todos os serviços** no canto superior esquerdo do Portal do Azure, pesquise **Azure AD B2C** e selecione-o.
1. No menu à esquerda em **políticas**, selecione **fluxos de usuário (políticas)** e, em seguida, selecione **novo fluxo de usuário**.

    ![Página fluxos do usuário no portal com o botão novo fluxo de usuário realçado](./media/tutorial-create-user-flows/signup-signin-user-flow.png)

1. Na guia **recomendado** , selecione o fluxo de usuário **inscrever-se e entrar** .

    ![Selecione uma página de fluxo de usuário com a inscrição e o fluxo de entrada realçados](./media/tutorial-create-user-flows/signup-signin-type.png)

1. Insira um **Nome** para o fluxo de usuário. Por exemplo, *signupsignin1*.
1. Para **Provedores de identidade**, selecione **Inscrição por email**.

    ![Criar página de fluxo de usuário no portal do Azure com propriedades realçadas](./media/tutorial-create-user-flows/signup-signin-properties.png)

1. Para **Atributos e declarações do usuário**, escolha as declarações e atributos que deseja coletar e enviar do usuário durante a inscrição. Por exemplo, selecione **Mostrar mais**e, em seguida, escolha atributos e declarações para **país/região**, **nome de exibição**e **CEP**. Clique em **OK**.

    ![Página de seleção de atributos e declarações com três declarações selecionadas](./media/tutorial-create-user-flows/signup-signin-attributes.png)

1. Clique em **Criar** para adicionar o fluxo de usuário. Um prefixo *B2C_1* é acrescentado automaticamente ao nome.

### <a name="test-the-user-flow"></a>Testar o fluxo de usuário

1. Selecione o fluxo de usuário que você criou para abrir sua página de visão geral e, em seguida, selecione **executar fluxo de usuário**.
1. Para **Aplicativo**, selecione o aplicativo Web denominado *webapp1* que você registrou anteriormente. A **URL de resposta** deve mostrar `https://jwt.ms`.
1. Clique em **Executar o fluxo do usuário**e, em seguida, selecione **Inscreva-se agora**.

    ![Página executar fluxo de usuário no portal com o botão Executar fluxo de usuário realçado](./media/tutorial-create-user-flows/signup-signin-run-now.PNG)

1. Insira um endereço de email válido, clique em **enviar código de verificação**, insira o código de verificação que você recebe e, em seguida, selecione **verificar código**.
1. Insira uma nova senha e confirme-a.
1. Selecione seu país e região, insira o nome que você deseja exibir, insira um CEP e, em seguida, clique em **criar**. O token é retornado para `https://jwt.ms` e deve ser exibido para você.
1. Agora, você pode executar o fluxo do usuário novamente e poderá se conectar com a conta que você criou. O token retornado inclui as declarações que você selecionou país/região, nome e CEP.

## <a name="create-a-profile-editing-user-flow"></a>Criar um fluxo de usuário de edição de perfil

Se quiser permitir que os usuários editem seu perfil no aplicativo, use um fluxo do usuário de edição de perfil.

1. No menu à esquerda da página Visão geral do locatário Azure AD B2C, selecione **fluxos de usuário (políticas)** e, em seguida, selecione **novo fluxo de usuário**.
1. Selecione o fluxo do usuário **Edição de perfil** na guia Recomendado.
1. Insira um **Nome** para o fluxo de usuário. Por exemplo, *profileediting1*.
1. Para **Provedores de identidade**, selecione **Conexão na Conta Local**.
1. Para **Atributos do usuário**, escolha os atributos que deseja que o cliente seja capaz de editar no perfil dele. Por exemplo, selecione **Mostrar mais**e, em seguida, escolha os atributos e as declarações para **nome de exibição** e **cargo**. Clique em **OK**.
1. Clique em **Criar** para adicionar o fluxo de usuário. Um prefixo *B2C_1* é acrescentado automaticamente ao nome.

### <a name="test-the-user-flow"></a>Testar o fluxo de usuário

1. Selecione o fluxo de usuário que você criou para abrir sua página de visão geral e, em seguida, selecione **executar fluxo de usuário**.
1. Para **Aplicativo**, selecione o aplicativo Web denominado *webapp1* que você registrou anteriormente. A **URL de resposta** deve mostrar `https://jwt.ms`.
1. Clique em **Executar o fluxo do usuário** e, em seguida, entre com a conta que criou anteriormente.
1. Agora, você tem a oportunidade de alterar o nome de exibição e o cargo do usuário. Clique em **Continue**. O token é retornado para `https://jwt.ms` e deve ser exibido para você.

## <a name="create-a-password-reset-user-flow"></a>Criar um fluxo de usuário de redefinição de senha

Para permitir que os usuários do seu aplicativo redefinam sua senha, use um fluxo de usuário de redefinição de senha.

1. No menu à esquerda, selecione **fluxos de usuário (políticas)** e, em seguida, selecione **novo fluxo de usuário**.
1. Selecione o fluxo do usuário **Redefinição de senha** na guia Recomendado.
1. Insira um **Nome** para o fluxo de usuário. Por exemplo, *passwordreset1*.
1. Em **Provedores de identidade**, habilite **Redefinir senha usando endereço de email**.
1. Em Declarações do aplicativo, clique em **Mostrar mais** e escolha as declarações que você quer retornar nos tokens de autorização enviados de volta ao seu aplicativo. Por exemplo, selecione **ID de Objeto do Usuário**.
1. Clique em **OK**.
1. Clique em **Criar** para adicionar o fluxo de usuário. Um prefixo *B2C_1* é acrescentado automaticamente ao nome.

### <a name="test-the-user-flow"></a>Testar o fluxo de usuário

1. Selecione o fluxo de usuário que você criou para abrir sua página de visão geral e, em seguida, selecione **executar fluxo de usuário**.
1. Para **Aplicativo**, selecione o aplicativo Web denominado *webapp1* que você registrou anteriormente. A **URL de resposta** deve mostrar `https://jwt.ms`.
1. Clique em **executar fluxo de usuário**, verifique o endereço de email da conta que você criou anteriormente e selecione **continuar**.
1. Agora, você pode alterar a senha para o usuário. Altere a senha e selecione **continuar**. O token é retornado para `https://jwt.ms` e deve ser exibido para você.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a:

> [!div class="checklist"]
> * Criar um fluxo de usuário de inscrição e de entrada
> * Criar um fluxo de usuário de edição de perfil
> * Criar um fluxo de usuário de redefinição de senha

Em seguida, saiba como adicionar provedores de identidade aos seus aplicativos para habilitar a entrada do usuário com provedores como o Azure AD, Amazon, Facebook, GitHub, LinkedIn, Microsoft ou Twitter.

> [!div class="nextstepaction"]
> [Adicionar provedores de identidade aos seus aplicativos >](tutorial-add-identity-providers.md)