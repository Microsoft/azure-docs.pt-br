---
title: Desabilitar a verificação de email durante a inscrição do cliente
titleSuffix: Azure AD B2C
description: Saiba como desabilitar a verificação de email durante a inscrição do cliente no Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/10/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 0f70c8d501a7d56f4bc29e0f2b065760cad625e5
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/16/2020
ms.locfileid: "97585012"
---
# <a name="disable-email-verification-during-customer-sign-up-in-azure-active-directory-b2c"></a>Desabilitar a verificação de email durante a inscrição do cliente no Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Por padrão, Azure Active Directory B2C (Azure AD B2C) verifica o endereço de email do cliente para contas locais (contas para usuários que se inscrevem com endereço de email ou nome de usuário). O Azure AD B2C garante endereços de email válidos exigindo que os clientes os verifiquem durante o processo de inscrição. Ele também impede que os atores mal-intencionados usem processos automatizados para gerar contas fraudulentas em seus aplicativos.

Alguns desenvolvedores de aplicativos preferem ignorar a verificação de email durante o processo de inscrição e, em vez disso, fazer com que os clientes verifiquem seu endereço de email posteriormente. Para dar suporte a isso, o Azure AD B2C pode ser configurado para desabilitar a verificação de email. Isso cria um processo de inscrição mais suave e dá aos desenvolvedores a flexibilidade para diferenciar os clientes que verificaram seu endereço de email dos clientes que não têm.

> [!WARNING]
> Desabilitar a verificação de email no processo de inscrição pode gerar spam. Se você desabilitar a verificação de email padrão fornecida pelo Azure AD B2C, recomendamos que implemente um sistema de verificação de substituição.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]
## <a name="disable-email-verification"></a>Desabilitar a verificação por email

::: zone pivot="b2c-user-flow"

Siga estas etapas para desabilitar a verificação de email:

1. Entre no [Portal do Azure](https://portal.azure.com)
1. Use o filtro **diretório + assinatura** no menu superior para selecionar o diretório que contém seu locatário Azure ad B2C.
1. No menu à esquerda, selecione **Azure AD B2C**. Ou selecione **Todos os serviços** e pesquise e selecione **Azure AD B2C**.
1. Escolha **Fluxos de usuário**.
1. Selecione o fluxo de usuário para o qual você deseja desabilitar a verificação de email. Por exemplo, *B2C_1_signinsignup*.
1. Selecione **layouts de página**.
1. Selecione **a página de inscrição da conta local**.
1. Em **atributos de usuário**, selecione **endereço de email**.
1. Na lista suspensa **requer verificação** , selecione **não**.
1. Selecione **Salvar**. A verificação de email agora está desabilitada para este fluxo de usuário.

::: zone-end

::: zone pivot="b2c-custom-policy"
O perfil técnico do **LocalAccountSignUpWithLogonEmail** é uma [declaração automática](self-asserted-technical-profile.md), que é invocada durante o fluxo de inscrição. Para desabilitar a verificação de email, defina os `EnforceEmailVerification` metadados como false. Substitua os perfis técnicos do LocalAccountSignUpWithLogonEmail no arquivo de extensão. 

1. Abra o arquivo de extensões da sua política. Por exemplo, <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em>.
1. Localize o elemento `ClaimsProviders`. Se o elemento não existir, adicione-o.
1. Adicione o seguinte provedor de declarações ao `ClaimsProviders` elemento:

```xml
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
      <Metadata>
        <Item Key="EnforceEmailVerification">false</Item>
      </Metadata>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```
::: zone-end

::: zone pivot="b2c-user-flow"

## <a name="test-your-policy"></a>Testar sua política 

1. Entre no [Portal do Azure](https://portal.azure.com)
1. Use o filtro **diretório + assinatura** no menu superior para selecionar o diretório que contém seu locatário Azure ad B2C.
1. No menu à esquerda, selecione **Azure AD B2C**. Ou selecione **Todos os serviços** e pesquise e selecione **Azure AD B2C**.
1. Escolha **Fluxos de usuário**.
1. Selecione o fluxo de usuário para o qual você deseja desabilitar a verificação de email. Por exemplo, *B2C_1_signinsignup*.
1. Para testar sua política, selecione **executar fluxo de usuário**.
1. Para **aplicativo**, selecione o aplicativo Web chamado *testapp1* que você registrou anteriormente. A **URL de resposta** deve mostrar `https://jwt.ms`.
1. Clique em **executar fluxo de usuário**
1. Você deve ser capaz de se inscrever usando um endereço de email sem a validação.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="update-and-test-the-relying-party-file"></a>Atualizar e testar o arquivo de terceira parte confiável

1. Entre no [portal do Azure](https://portal.azure.com).
1. Verifique se você está usando o diretório que contém o locatário do Azure Active Directory escolhendo o filtro **Diretório + assinatura** no menu superior e escolhendo o diretório que contém o locatário do Azure Active Directory.
1. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure e pesquise e selecione **Registros de aplicativo**.
1. Selecione **Estrutura de Experiência de Identidade**.
1. Selecione **Carregar política personalizada** e carregue os dois arquivos de política alterados.
1. Selecione a política de inscrição ou de entrada carregada e clique no botão **Executar agora**.
1. Você deve ser capaz de se inscrever usando um endereço de email sem a validação.

::: zone-end


## <a name="next-steps"></a>Próximas etapas

- Saiba como [Personalizar a interface do usuário no Azure Active Directory B2C](customize-ui-with-html.md)

