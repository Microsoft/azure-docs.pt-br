---
title: Criar um fluxo de usuário – Azure Active Directory B2C
description: Saiba como criar fluxos dos usuários no portal do Azure para habilitar a inscrição, a entrada e a edição de perfil do usuário para seus aplicativos no Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 07/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 418446e0d465b606b8d580297cebd73c466d4841
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/11/2020
ms.locfileid: "97109005"
---
# <a name="create-a-user-flow-in-azure-active-directory-b2c"></a>Criar um fluxo de usuário no Azure Active Directory B2C

É possível criar [fluxos dos usuários](user-flow-overview.md) de diferentes tipos no locatário do Azure AD B2C (Azure Active Directory B2C) e usá-los nos aplicativos, conforme necessário. Fluxos dos usuários podem ser reutilizados nos aplicativos.

> [!IMPORTANT]
> Alteramos a maneira como fazemos referência às versões de fluxo do usuário. Anteriormente, oferecíamos versões V1 (prontas para produção) e versões V1.1 e V2 (versão prévia). Agora, consolidamos os fluxos dos usuários nas versões **Recomendado** (versão prévia da próxima geração) e **Standard** (em disponibilidade geral). Todos os fluxos dos usuários da versão prévia herdada da V1.1 e da V2 serão preteridos até **1º de agosto de 2021**. Para obter detalhes, consulte as [Versões de fluxo de usuário no Azure AD B2C](user-flow-versions.md).

## <a name="before-you-begin"></a>Antes de começar

- **Registre o aplicativo** que você deseja usar para testar o novo fluxo de usuário. Para ver um exemplo, confira o [Tutorial: Registrar um aplicativo Web no Azure AD B2C](tutorial-register-applications.md).
- **Adicione provedores de identidade externos** se desejar habilitar a entrada de usuário com provedores como Azure AD, Amazon, Facebook, GitHub, LinkedIn, Microsoft ou Twitter. Para um exemplo, veja [Tutorial: Adicionar provedores de identidade aos seus aplicativos no Azure AD B2C](tutorial-add-identity-providers.md).
- **Configure o provedor de identidade da conta local** para especificar os tipos de identidade (email, nome de usuário, número de telefone) para os quais você deseja dar suporte para contas locais em seu locatário. Em seguida, você pode escolher entre esses tipos de identidade com suporte ao criar fluxos dos usuários individuais. Quando um usuário conclui o fluxo do usuário, uma conta local é criada no diretório do Azure AD B2C, e seu provedor de identidade de **conta local** autentica as informações do usuário. Configure o provedor de identidade de conta local do seu locatário com estas etapas:

   1. Entre no [portal do Azure](https://portal.azure.com/). 
   2. Selecione o filtro **Diretório + assinatura** no menu superior e escolha o diretório que contém o locatário do Azure AD B2C.
   3. Na barra de pesquisa na parte superior do portal do Azure, pesquise e selecione **Azure AD B2C**.
   4. Em **Gerenciar**, selecione **Provedores de identidade**.
   5. Na lista de provedores de identidade, selecione **Conta local**.
   6. Na página **Configurar IdP local**, selecione todos os tipos de identidade aos quais você deseja dar suporte. A seleção de opções aqui simplesmente as disponibiliza para os fluxos dos usuários criados posteriormente:
      - **Telefone** (versão prévia): permite que um usuário insira um número de telefone, que é verificado na inscrição e se torna a ID de usuário dele.
      - **Email** (padrão): permite que um usuário insira um endereço de email, que é verificado na inscrição e se torna a ID de usuário dele.
      - **Nome de usuário**: permite que um usuário crie a própria ID de usuário único. Um endereço de email é coletado do usuário e verificado.
    7. Clique em **Salvar**.

## <a name="create-a-user-flow"></a>Criar um fluxo de usuário

1. Entre no [portal do Azure](https://portal.azure.com).
2. Selecione o ícone **Diretório + Assinatura** na barra de ferramentas do portal e selecione o diretório que contém o locatário do Azure AD B2C.

    ![Locatário do B2C, painel Diretório e Assinatura, portal do Azure](./media/create-user-flow/directory-subscription-pane.png)

3. No portal do Azure, pesquise e selecione **Azure AD B2C**.
4. Em **Políticas**, selecione **Fluxos dos usuários** e, em seguida, selecione **Novo fluxo de usuário**.

    ![Página fluxos dos usuários no portal com o botão Novo fluxo de usuário realçado](./media/create-user-flow/signup-signin-user-flow.png)

5. Na página **Criar um fluxo de usuário**, selecione o tipo de fluxo de usuário que você deseja criar (confira [Fluxos dos usuários no Azure AD B2C](user-flow-overview.md) para obter uma visão geral).

    ![Página Selecione um fluxo de usuário com o fluxo Inscrever-se e entrar realçado](./media/create-user-flow/select-user-flow-type.png)

6. Em **Selecionar uma versão**, selecione **Recomendado** e, em seguida, selecione **Criar**. ([Saiba mais](user-flow-versions.md) sobre as versões de fluxo de usuário.)

    ![Página Criar fluxo de usuário no portal do Azure com as propriedades realçadas](./media/create-user-flow/select-version.png)

7. Insira um **Nome** para o fluxo de usuário (por exemplo, *signupsignin1*, *profileediting1*, *passwordreset1*).
8. Em **Provedores de identidade**, escolha as opções, dependendo do tipo de fluxo de usuário que você está criando:

   - **Conta local**. Se você quiser permitir que os usuários criem contas locais em seu locatário do Azure AD B2C, selecione o tipo de identificador que você deseja que eles usem (por exemplo, email, ID de usuário ou telefone). Somente os tipos de identidade configurados nas configurações do [provedor de identidade de conta local](#before-you-begin) são listados.

   - **Provedores de identidade social**. Se desejar permitir a entrada do usuário com [provedores de identidade social adicionados](tutorial-add-identity-providers.md), como Azure AD, Amazon, Facebook, GitHub, LinkedIn, Microsoft ou Twitter, selecione os provedores na lista.

9. Para **Atributos e declarações do usuário**, escolha as declarações e atributos que deseja coletar e enviar do usuário durante a inscrição. Selecione **Mostrar mais**. Selecione os atributos e as declarações e selecione **OK**.

    ![Página Seleção de atributos e declarações com três declarações selecionadas](./media/create-user-flow/signup-signin-attributes.png)

10. Selecione **Criar** para adicionar o fluxo de usuário. Um prefixo *B2C_1* é anexado automaticamente ao nome.

### <a name="test-the-user-flow"></a>Testar o fluxo de usuário

1. Selecione **Políticas** > **Fluxos de usuário** e selecione o fluxo de usuário criado. Na página de visão geral do fluxo de usuário, selecione **Executar o fluxo de usuário**.
1. Para **Aplicativo**, selecione o aplicativo Web registrado na etapa 1. A **URL de resposta** deve mostrar `https://jwt.ms`.
1. Selecione **Executar fluxo de usuário**.
2. Dependendo do tipo de fluxo de usuário que você está testando, inscreva-se usando um endereço de email válido e siga o fluxo de inscrição ou entre usando uma conta que você criou anteriormente.

    ![Página Executar fluxo de usuários no portal com o botão Executar fluxo de usuário realçado](./media/create-user-flow/sign-up-sign-in-run-now.png)

1. Siga os prompts de fluxo de usuário. Quando você concluir o fluxo de usuário, o token será retornado para `https://jwt.ms` e deverá ser exibido para você.

> [!NOTE]
> No momento, a experiência "Executar fluxo de usuário" não é compatível com o tipo de URL de resposta SPA que usa o fluxo de código de autorização. Para usar a experiência "Executar fluxo de usuário" com esses tipos de aplicativos, registre uma URL de resposta do tipo "Web" e habilite o fluxo implícito conforme descrito [aqui](tutorial-register-spa.md).

## <a name="next-steps"></a>Próximas etapas

- [Adicionar o Acesso Condicional a fluxos dos usuários do Azure AD B2C](conditional-access-user-flow.md)
- [Personalizar a interface do usuário em um fluxo de usuário do Azure AD B2C](customize-ui-with-html.md)
