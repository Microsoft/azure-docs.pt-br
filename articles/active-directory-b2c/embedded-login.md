---
title: Inserir Azure Active Directory B2C interface do usuário em seu aplicativo com uma política personalizada
titleSuffix: Azure AD B2C
description: Saiba como inserir Azure Active Directory B2C interface do usuário em seu aplicativo com uma política personalizada
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/21/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: ccad323c1834894367cca0ef0d3f98eb1b1b1ec3
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2021
ms.locfileid: "105639913"
---
# <a name="embedded-sign-in-experience"></a>Experiência de entrada inserida

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

Para uma experiência de entrada mais simples, você pode evitar o redirecionamento de usuários para uma página de entrada separada ou a geração de uma janela pop-up. Usando o elemento de quadro embutido `<iframe>` , você pode inserir a interface do usuário de entrada do Azure ad B2C diretamente em seu aplicativo Web.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="web-application-embedded-sign-in"></a>Entrada inserida do aplicativo Web

O elemento de quadro embutido `<iframe>` é usado para inserir um documento em uma página da Web do HTML5. Você pode usar o elemento iframe para inserir a interface do usuário de entrada do Azure AD B2C diretamente em seu aplicativo Web, como mostrado no exemplo a seguir:

![Logon com a experiência de DIV em foco](media/embedded-login/login-hovering.png)

Ao usar o iframe, considere o seguinte:

- A entrada inserida só dá suporte a contas locais. A maioria dos provedores de identidade social (por exemplo, Google e Facebook) bloqueiam suas páginas de entrada de serem renderizadas em quadros embutidos.
- Como Azure AD B2C cookies de sessão em um iframe são considerados cookies de terceiros, determinados navegadores (por exemplo, Safari ou Chrome no modo Incognito) bloqueiam ou desmarcam esses cookies, resultando em uma experiência de usuário indesejável. Para evitar esse problema, verifique se o nome de domínio do aplicativo e seu domínio Azure AD B2C têm a *mesma origem*. Para usar a mesma origem, [habilite domínios personalizados](custom-domain.md) para Azure ad B2C locatário e, em seguida, configure seu aplicativo Web com a mesma origem. Por exemplo, um aplicativo hospedado em ' https://app.contoso.com ' tem a mesma origem que Azure ad B2C em execução em ' https://login.contoso.com '.

## <a name="prerequisites"></a>Pré-requisitos

* Conclua as etapas em [Introdução às políticas personalizadas no Active Directory B2C](custom-policy-get-started.md).
* [Habilite domínios personalizados](custom-domain.md) para suas políticas.

## <a name="configure-your-policy"></a>Configurar sua política

Para permitir que a interface do usuário do Azure AD B2C seja inserida em um iframe, uma política de segurança de conteúdo `Content-Security-Policy` e opções de quadro `X-Frame-Options` devem ser incluídas nos cabeçalhos de resposta http Azure ad B2C. Esses cabeçalhos permitem que a interface do usuário do Azure AD B2C seja executada sob o nome de domínio do aplicativo.

Adicione um elemento **JourneyFraming** dentro do elemento [RelyingParty](relyingparty.md) .  O elemento **UserJourneyBehaviors** deve seguir o **DefaultUserJourney**. O elemento **UserJourneyBehaviors** deve ser semelhante a este exemplo:

```xml
<!--
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" /> -->
  <UserJourneyBehaviors> 
    <JourneyFraming Enabled="true" Sources="https://somesite.com https://anothersite.com" /> 
  </UserJourneyBehaviors>
<!--
</RelyingParty> -->
```

O atributo **sources** contém o URI do seu aplicativo Web. Adicione um espaço entre URIs. Cada URI deve atender aos seguintes requisitos:

- O URI deve ser confiável e pertencente ao seu aplicativo.
- O URI deve usar o esquema HTTPS.  
- O URI completo do aplicativo Web deve ser especificado. Não há suporte para caracteres curinga.

Além disso, é recomendável que você também impeça que seu próprio nome de domínio seja inserido em um iframe, definindo os cabeçalhos content-Security-Policy e X-Frame-Options, respectivamente nas páginas do aplicativo. Isso reduzirá as preocupações de segurança em relação aos navegadores mais antigos relacionados à incorporação aninhada de iframes.

## <a name="adjust-policy-user-interface"></a>Ajustar a interface do usuário da política

Com a [personalização da interface do usuário](customize-ui.md)Azure ad B2C, você tem controle quase total sobre o conteúdo HTML e CSS apresentado aos usuários. Siga as etapas para personalizar uma página HTML usando definições de conteúdo. Para ajustar a interface do usuário do Azure AD B2C no tamanho do iframe, forneça uma página HTML limpa sem espaços em fundo e extras.  

O código CSS a seguir oculta o Azure AD B2C elementos HTML e ajusta o tamanho do painel para preencher o iframe.

```css
div.social, div.divider {
    display: none;
}

div.api_container{
    padding-top:0;
}

.panel {
    width: 100%!important
}
```

Em alguns casos, talvez você queira notificar para o aplicativo do qual Azure AD B2C página está sendo apresentada no momento. Por exemplo, quando um usuário seleciona a opção de inscrição, talvez você queira que o aplicativo responda ocultando os links para entrar com uma conta social ou ajustando o tamanho do iframe.

Para notificar seu aplicativo da página de Azure AD B2C atual, [habilite sua política para JavaScript](./javascript-and-page-layout.md)e, em seguida, use as mensagens de postagem do HTML5. O código JavaScript a seguir envia uma mensagem post para o aplicativo com `signUp` :

```javascript
window.parent.postMessage("signUp", '*');
```

## <a name="configure-a-web-application"></a>Configurar um aplicativo Web

Quando um usuário seleciona o botão de entrada, o [aplicativo Web](code-samples.md#web-apps-and-apis) gera uma solicitação de autorização que leva o usuário para Azure ad B2C experiência de entrada. Após a conclusão da entrada, Azure AD B2C retorna um token de ID ou código de autorização para o URI de redirecionamento configurado em seu aplicativo.

Para dar suporte ao logon incorporado, a propriedade **src** do iframe aponta para o controlador de entrada, como `/account/SignUpSignIn` , que gera a solicitação de autorização e redireciona o usuário para Azure ad B2C política.

```html
<iframe id="loginframe" frameborder="0" src="/account/SignUpSignIn"></iframe>
``` 

Depois que o token de ID é recebido e validado pelo aplicativo, o fluxo de autorização é concluído e o aplicativo reconhece e confia no usuário. Como o fluxo de autorização ocorre dentro do iframe, você precisa recarregar a página principal. Depois que a página é recarregada, o botão de entrada muda para "sair" e o nome de usuário é apresentado na interface do usuário.  

Veja a seguir um exemplo que mostra como o URI de redirecionamento de entrada pode atualizar a página principal:

```javascript
window.top.location.reload();
```

### <a name="add-sign-in-with-social-accounts-to-a-web-app"></a>Adicionar entrada com contas sociais a um aplicativo Web

Provedores de identidade social bloqueiam suas páginas de entrada da renderização em quadros embutidos. Você pode usar uma política separada para contas sociais ou pode usar uma única política para entrada e inscrição com contas locais e sociais. Em seguida, você pode usar o `domain_hint` parâmetro de cadeia de caracteres de consulta. O parâmetro de dica de domínio leva o usuário diretamente para a página de entrada do provedor de identidade social.

Em seu aplicativo, adicione os botões de entrada com a conta social. Quando um usuário clica em um dos botões de conta social, o controle precisa alterar o nome da política ou definir o parâmetro de dica de domínio.

<!-- TBD: add a diagram -->

O URI de redirecionamento pode ser o mesmo URI de redirecionamento usado pelo iframe. Você pode ignorar a recarga da página.

## <a name="configure-a-single-page-application"></a>Configurar um aplicativo de página única

Para um aplicativo de página única, você também precisará de uma segunda página HTML "entrar" que é carregada no iframe. Essa página de entrada hospeda o código da biblioteca de autenticação que gera o código de autorização e retorna o token.

Quando o aplicativo de página única precisar do token de acesso, use o código JavaScript para obter o token de acesso do iframe e do objeto que o contém.

> [!NOTE]
> No momento, não há suporte para a execução do MSAL 2,0 em um iframe.

O código a seguir é um exemplo que é executado na página principal e chama o código JavaScript de um iframe:

```javascript
function getToken()
{
  var token = document.getElementById("loginframe").contentWindow.getToken("adB2CSignInSignUp");

  if (token === "LoginIsRequired")
    document.getElementById("tokenTextarea").value = "Please login!!!"
  else
    document.getElementById("tokenTextarea").value = token.access_token;
}

function logOut()
{
  document.getElementById("loginframe").contentWindow.policyLogout("adB2CSignInSignUp", "B2C_1A_SignUpOrSignIn");
}
```

## <a name="next-steps"></a>Próximas etapas

Consulte os seguintes artigos relacionados:

- [Personalização da interface do usuário](customize-ui.md)
- Referência de elemento [RelyingParty](relyingparty.md)
- [Habilitar sua política para JavaScript](./javascript-and-page-layout.md)
- [Exemplos de código](code-samples.md)

::: zone-end
