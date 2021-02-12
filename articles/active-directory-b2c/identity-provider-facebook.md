---
title: Configurar a inscrição e a entrada com uma conta do Facebook
titleSuffix: Azure AD B2C
description: Forneça a inscrição e entrada aos consumidores com contas do Facebook em seus aplicativos usando o Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/19/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: e0e71bc0e3a81b5ab2f455224ed2ed4281532d55
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98952667"
---
# <a name="set-up-sign-up-and-sign-in-with-a-facebook-account-using-azure-active-directory-b2c"></a>Configurar a inscrição e entrada com a conta do Facebook usando o Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-a-facebook-application"></a>Criar um aplicativo do Facebook

Para habilitar a entrada para usuários com uma conta do Facebook no Azure Active Directory B2C (Azure AD B2C), você precisa criar um aplicativo no [painel do aplicativo do Facebook](https://developers.facebook.com/). Para obter mais informações, consulte [desenvolvimento de aplicativos](https://developers.facebook.com/docs/development). Se ainda não tiver uma conta do Facebook, abra uma em [https://www.facebook.com/](https://www.facebook.com/).

1. Entre no site [Desenvolvedores do Facebook](https://developers.facebook.com/) com suas credenciais de conta do Facebook.
1. Se ainda não tiver feito isso, você precisará registrar-se como desenvolvedor do Facebook. Para isso, escolha **Começar**, no canto superior direito da página, aceite as políticas do Facebook e conclua as etapas de registro.
1. Escolha **Meus Aplicativos** e **Criar Aplicativo**.
1. Selecione **criar experiências conectadas**.
1. Insira um **Nome de Exibição** e um **Email de Contato** válido.
1. Selecione **Criar ID do aplicativo**. Isso pode exigir a aceitação das políticas de plataforma do Facebook e a conclusão de uma verificação de segurança online.
1. Escolha **Configurações** > **Básicas**.
    1. Escolha uma **Categoria**, por exemplo, `Business and Pages`. Esse valor é exigido pelo Facebook, mas não é usado para o Azure AD B2C.
    1. Insira uma URL para a **URL dos termos de serviço**, por exemplo `http://www.contoso.com/tos` . A URL da política é uma página que você mantém para fornecer os termos e condições do seu aplicativo.
    1. Insira uma URL para a **URL da Política de Privacidade**, por exemplo `http://www.contoso.com/privacy`. A URL da política é uma página que você mantém para fornecer informações de privacidade para o seu aplicativo.
1. Na parte inferior da página, escolha **Adicionar Plataforma** e, em seguida, escolha **Site**.
1. Em **URL do site**, insira o endereço do seu site, por exemplo `https://contoso.com` . 
1. Selecione **Salvar alterações**.
1. Na parte superior da página, copie o valor de **ID do Aplicativo**.
1. Escolha **Mostrar** e copie o valor de **Segredo do Aplicativo**. Você usará ambos para configurar o Facebook como um provedor de identidade em seu locatário. **Segredo do Aplicativo** é uma credencial de segurança importante.
1. No menu, selecione o sinal de **adição** ao lado de **produtos**. Em **Adicionar produtos ao seu aplicativo**, selecione **Configurar** em logon do **Facebook**.
1. No menu, selecione **logon do Facebook**, selecione **configurações**.
1. Em **URIs de Redirecionamento do OAuth Válidos**, insira `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`. Substitua `your-tenant-name` pelo nome do seu locatário. Escolha **Salvar Alterações** na parte inferior da página.
1. Para disponibilizar seu aplicativo do Facebook para Azure AD B2C, selecione o seletor de status na parte superior direita da página e **ative-o** para tornar o aplicativo público e selecione **modo de comutação**.  Neste ponto, o Status deverá mudar de **Desenvolvimento** para **Ativo**.

::: zone pivot="b2c-user-flow"

## <a name="configure-facebook-as-an-identity-provider"></a>Configurar o Facebook como um provedor de identidade

1. Entre no [portal do Azure](https://portal.azure.com/) como administrador global do locatário Azure AD B2C.
1. Verifique se você está usando o diretório que contém o locatário do Azure AD B2C selecionando o filtro **Diretório + assinatura** no menu superior e escolhendo o diretório que contém o locatário.
1. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**.
1. Selecione **Provedores de identidade** e escolha **Facebook**.
1. Insira um **Nome**. Por exemplo, *Facebook*.
1. Para **ID do cliente**, insira a ID do aplicativo do Facebook que criou anteriormente.
1. Para **Segredo do cliente**, insira o Segredo do Aplicativo que você registrou.
1. Clique em **Salvar**.

## <a name="add-facebook-identity-provider-to-a-user-flow"></a>Adicionar o provedor de identidade do Facebook a um fluxo de usuário 

1. No locatário do Azure AD B2C, selecione **Fluxos dos usuários**.
1. Clique no fluxo de usuário para o qual você deseja adicionar o provedor de identidade do Facebook.
1. Em **provedores de identidade social**, selecione **Facebook**.
1. Selecione **Salvar**.
1. Para testar sua política, selecione **executar fluxo de usuário**.
1. Para **aplicativo**, selecione o aplicativo Web chamado *testapp1* que você registrou anteriormente. A **URL de resposta** deve mostrar `https://jwt.ms`.
1. Clique em **executar fluxo de usuário**

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-policy-key"></a>Criar uma chave de política

Você precisa armazenar o segredo do aplicativo que você registrou anteriormente em seu locatário Azure AD B2C.

1. Entre no [portal do Azure](https://portal.azure.com/).
2. Verifique se você está usando o diretório que contém seu locatário do Azure AD B2C. Escolha o filtro **Diretório + assinatura** no menu superior e escolha o diretório que contém o locatário.
3. Escolha **Todos os serviços** no canto superior esquerdo do Portal do Azure, pesquise **Azure AD B2C** e selecione-o.
4. Na página de Visão Geral, selecione **Estrutura de Experiência de Identidade**.
5. Selecione **Chaves de Política** e, em seguida, escolha **Adicionar**.
6. Para **Opções**, escolha `Manual`.
7. Insira um **Nome** para a chave de política. Por exemplo, `FacebookSecret`. O prefixo `B2C_1A_` será adicionado automaticamente ao nome da chave.
8. Em **segredo**, insira o segredo do aplicativo que você registrou anteriormente.
9. Para **Uso de chave**, selecione `Signature`.
10. Clique em **Criar**.

## <a name="configure-a-facebook-account-as-an-identity-provider"></a>Configurar o Facebook como um provedor de identidade

1. No arquivo `SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`** , substitua o valor de `client_id` pela ID do aplicativo do Facebook:

   ```xml
   <TechnicalProfile Id="Facebook-OAUTH">
     <Metadata>
     <!--Replace the value of client_id in this technical profile with the Facebook app ID"-->
       <Item Key="client_id">00000000000000</Item>
   ```

## <a name="upload-and-test-the-policy"></a>Carregar e testar a política

Atualize o arquivo de RP (terceira parte confiável) que iniciará o percurso do usuário que você criou.

1. Faça upload do arquivo *TrustFrameworkExtensions.xml* no seu locatário.
1. Na página **Políticas personalizadas**, selecione **B2C_1A_signup_signin**.
1. Para **Selecionar aplicativo**, selecione o aplicativo Web chamado *testapp1* que você registrou anteriormente. A **URL de resposta** deve mostrar `https://jwt.ms`.
1. Selecione **Executar agora** e o Facebook para entrar no Facebook e testar a política personalizada.

::: zone-end

## <a name="next-steps"></a>Próximas etapas

Saiba como [passar o token do Facebook para seu aplicativo](idp-pass-through-user-flow.md).
