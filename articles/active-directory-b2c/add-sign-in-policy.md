---
title: Configurar um fluxo de entrada
titleSuffix: Azure Active Directory B2C
description: Saiba como configurar um fluxo de entrada no Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/04/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: c84966f7b4dc1740235fe6414da2ba832a1334fd
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102119902"
---
# <a name="set-up-a-sign-in-flow-in-azure-active-directory-b2c"></a>Configurar um fluxo de entrada no Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

## <a name="sign-in-flow-overview"></a>Visão geral do fluxo de entrada

A política de entrada permite que os usuários: 

* Os usuários podem entrar com uma conta local Azure AD B2C
* Inscrever-se ou entrar com uma conta social
* Redefinição de senha
* Os usuários não podem se inscrever em uma conta local Azure AD B2C. Para criar uma conta, um administrador pode usar [portal do Azure](manage-users-portal.md#create-a-consumer-user)ou [MS API do Graph](microsoft-graph-operations.md).

![Fluxo de edição de perfil](./media/add-sign-in-policy/sign-in-user-flow.png)

## <a name="prerequisites"></a>Pré-requisitos

Se você ainda não fez isso, [registre um aplicativo Web em Azure Active Directory B2C](tutorial-register-applications.md).

::: zone pivot="b2c-user-flow"

## <a name="create-a-sign-in-user-flow"></a>Criar um fluxo de usuário de entrada

Para adicionar a política de entrada:

1. Entre no [portal do Azure](https://portal.azure.com).
1. Selecione o ícone **Diretório + Assinatura** na barra de ferramentas do portal e selecione o diretório que contém o locatário do Azure AD B2C.
1. No portal do Azure, pesquise e selecione **Azure AD B2C**.
1. Em **Políticas**, selecione **Fluxos dos usuários** e, em seguida, selecione **Novo fluxo de usuário**.
1. Na página **criar um fluxo de usuário** , selecione o fluxo de usuário de **entrada** .
1. Em **Selecionar uma versão**, selecione **Recomendado** e, em seguida, selecione **Criar**. ([Saiba mais](user-flow-versions.md) sobre as versões de fluxo de usuário.)
1. Insira um **Nome** para o fluxo de usuário. Por exemplo, *signupsignin1*.
1. Para **provedores de identidade**, selecione **entrada de email**.
1. Para **declarações de aplicativo**, escolha as declarações e os atributos que você deseja enviar para seu aplicativo. Por exemplo, selecione **Mostrar mais** e, em seguida, escolha atributos e declarações para **nome de exibição**, **nome**,  **sobrenome** e **ID de objeto do usuário**. Clique em **OK**.
1. Clique em **Criar** para adicionar o fluxo de usuário. Um prefixo *B2C_1* é anexado automaticamente ao nome.

### <a name="test-the-user-flow"></a>Testar o fluxo de usuário

1. Selecione o fluxo de usuário que você criou para abrir a página de visão geral e, em seguida, selecione **Executar fluxo de usuário**.
1. Para **Aplicativo**, selecione o aplicativo Web denominado *webapp1* que você registrou anteriormente. A **URL de resposta** deve mostrar `https://jwt.ms`.
1. Clique em **Executar fluxo de usuário**.
1. Você deve ser capaz de entrar com a conta que você criou (usando o MS API do Graph), sem o link de inscrição. O token retornado inclui as declarações que você selecionou.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="remove-the-sign-up-link"></a>Remover o link de inscrição

O perfil técnico **SelfAsserted-LocalAccountSignin-email** é uma [declaração automática](self-asserted-technical-profile.md), que é invocada durante o fluxo de inscrição ou entrada. Para remover o link de inscrição, defina os `setting.showSignupLink` metadados como `false` . Substitua os perfis técnicos SelfAsserted-LocalAccountSignin-email no arquivo de extensão. 

1. Abra o arquivo de extensões da sua política. Por exemplo, _`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**_.
1. Localize o elemento `ClaimsProviders`. Se o elemento não existir, adicione-o.
1. Adicione o seguinte provedor de declarações ao `ClaimsProviders` elemento:

    ```xml
    <!--
    <ClaimsProviders> -->
      <ClaimsProvider>
        <DisplayName>Local Account</DisplayName>
        <TechnicalProfiles>
          <TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
            <Metadata>
              <Item Key="setting.showSignupLink">false</Item>
            </Metadata>
          </TechnicalProfile>
        </TechnicalProfiles>
      </ClaimsProvider>
    <!--
    </ClaimsProviders> -->
    ```

1. No `<BuildingBlocks>` elemento, adicione o seguinte [ContentDefinition](contentdefinitions.md) para fazer referência à versão 1.2.0 ou ao URI de dados mais recente:

    ```XML
    <!-- 
    <BuildingBlocks> 
      <ContentDefinitions>-->
        <ContentDefinition Id="api.localaccountsignup">
          <DataUri>urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.2.0</DataUri>
        </ContentDefinition>
      <!--
      </ContentDefinitions>
    </BuildingBlocks> -->
    ```

## <a name="update-and-test-your-policy"></a>Atualizar e testar sua política

1. Entre no [portal do Azure](https://portal.azure.com).
1. Verifique se você está usando o diretório que contém o locatário do Azure Active Directory escolhendo o filtro **Diretório + assinatura** no menu superior e escolhendo o diretório que contém o locatário do Azure Active Directory.
1. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure e pesquise e selecione **Registros de aplicativo**.
1. Selecione **Estrutura de Experiência de Identidade**.
1. Selecione **carregar política personalizada** e, em seguida, carregue o arquivo de política que você alterou, *TrustFrameworkExtensions.xml*.
1. Selecione a política de entrada que você carregou e clique no botão **executar agora** .
1. Você deve ser capaz de entrar com a conta que você criou (usando o MS API do Graph), sem o link de inscrição.

::: zone-end

## <a name="next-steps"></a>Próximas etapas

* Adicione uma [entrada com o provedor de identidade social](add-identity-provider.md).
* Configure um [fluxo de redefinição de senha](add-password-reset-policy.md).
