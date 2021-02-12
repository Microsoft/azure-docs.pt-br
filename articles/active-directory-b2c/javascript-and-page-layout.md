---
title: JavaScript e versões de layout de página
titleSuffix: Azure AD B2C
description: Saiba como habilitar o JavaScript e usar versões de layout de página no Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/10/2020
ms.custom: project-no-code, devx-track-js
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 6bb478038d398226db38dc20e49ed7a14e5d5d0a
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99592799"
---
# <a name="javascript-and-page-layout-versions-in-azure-active-directory-b2c"></a>JavaScript e versões de layout de página no Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

Azure AD B2C fornece um conjunto de conteúdo empacotado que contém HTML, CSS e JavaScript para os elementos da interface do usuário em seus fluxos de usuário e políticas personalizadas. Para habilitar o JavaScript para seus aplicativos:

::: zone pivot="b2c-user-flow"

* Selecionar um [layout de página](page-layout.md)
* Habilite-o no fluxo do usuário usando o portal do Azure
* Usar [b2clogin.com](b2clogin.md) em suas solicitações

::: zone-end

::: zone pivot="b2c-custom-policy"

* Selecionar um [layout de página](page-layout.md)
* Adicionar um elemento à [política personalizada](custom-policy-overview.md)
* Usar [b2clogin.com](b2clogin.md) em suas solicitações

::: zone-end

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]


## <a name="select-a-page-layout-version"></a>Selecionar uma versão de layout de página

Se você pretende habilitar o código JavaScript do lado do cliente, os elementos dos quais você baseia seu JavaScript devem ser imutáveis. Se não forem imutáveis, as alterações poderão causar um comportamento inesperado nas páginas do usuário. Para evitar esses problemas, aplique o uso de um layout de página e especifique uma versão de layout de página para garantir que as definições de conteúdo com base em seu JavaScript sejam imutáveis. Mesmo que você não pretenda habilitar o JavaScript, você pode especificar uma versão de layout de página para suas páginas.

::: zone pivot="b2c-user-flow"

Para especificar uma versão de layout de página para suas páginas de fluxo de usuário: 

1. No locatário do Azure AD B2C, selecione **Fluxos dos usuários**.
1. Selecione sua política (por exemplo, "B2C_1_SignupSignin") para abri-la.
1. Selecione **layouts de página**. Escolha um **nome de layout** e, em seguida, escolha a **versão de layout de página (visualização)**.

Para obter informações sobre as diferentes versões de layout de página, consulte o [log de página versão de layout de alteração](page-layout.md).

![Configurações de layout de página no portal mostrando o menu suspenso versão de layout de página](media/javascript-and-page-layout/page-layout-version.png)

::: zone-end

::: zone pivot="b2c-custom-policy"

Selecione um [layout de página](contentdefinitions.md#select-a-page-layout) para os elementos da interface do usuário do seu aplicativo.

Defina uma [versão de layout de página](contentdefinitions.md#migrating-to-page-layout) com `contract` a versão de página para *todas* as definições de conteúdo em sua política personalizada. O formato do valor deve conter a palavra `contract` : _urn: com: Microsoft: AAD: B2C: elements:page-Name: Version_. Saiba como [migrar para o layout da página](contentdefinitions.md#migrating-to-page-layout) com a versão da página.

O exemplo a seguir mostra os identificadores de definição de conteúdo e o **DataUri** correspondente com o contrato de página: 

```xml
<ContentDefinitions>
  <ContentDefinition Id="api.error">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:globalexception:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.idpselections.signup">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.signuporsignin">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.selfasserted.profileupdate">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountsignup">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountpasswordreset">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:1.2.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.phonefactor">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:multifactor:1.2.0</DataUri>
  </ContentDefinition>
</ContentDefinitions>
```
::: zone-end

## <a name="enable-javascript"></a>Habilitar o JavaScript

::: zone pivot="b2c-user-flow"

Nas **Propriedades** de fluxo do usuário, você pode habilitar o JavaScript. Habilitar o JavaScript também impõe o uso de um layout de página. Em seguida, você pode definir a versão de layout da página para o fluxo do usuário, conforme descrito na próxima seção.

![Página Propriedades do fluxo de usuário com a configuração Habilitar JavaScript realçada](media/javascript-and-page-layout/javascript-settings.png)

::: zone-end

::: zone pivot="b2c-custom-policy"

Você habilita a execução do script, adicionando o elemento **ScriptExecution** ao elemento [RelyingParty](relyingparty.md).

1. Abra o arquivo de política personalizada. Por exemplo, *SignUpOrSignin.xml*.
1. Adicione o elemento **ScriptExecution** ao elemento **RelyingParty** :

    ```xml
    <RelyingParty>
      <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
      <UserJourneyBehaviors>
        <ScriptExecution>Allow</ScriptExecution>
      </UserJourneyBehaviors>
      ...
    </RelyingParty>
    ```
1. Salve e carregue o arquivo.

::: zone-end

## <a name="guidelines-for-using-javascript"></a>Diretrizes para usar JavaScript

Siga estas diretrizes ao personalizar a interface do seu aplicativo usando JavaScript:

- Não associe um evento de clique nos elementos HTML `<a>`.
- Não use uma dependência no código do Azure AD B2C ou comentários.
- Não altere a ordem ou a hierarquia dos elementos HTML do Azure AD B2C. Use uma política do Azure AD B2C para controlar a ordem dos elementos da UI (interface do usuário).
- Você pode chamar qualquer serviço RESTful com estas considerações:
    - Talvez seja necessário definir o CORS do serviço RESTful para permitir chamadas HTTP do lado do cliente.
    - Verifique se o serviço RESTful é seguro e usa apenas o protocolo HTTPS.
    - Não use o JavaScript diretamente para chamar pontos de extremidade do Azure AD B2C.
- É possível incorporar o JavaScript ou vincular a arquivos JavaScript externos. Ao usar um arquivo JavaScript externo, certifique-se de usar a URL absoluta e não uma URL relativa.
- Estruturas do JavaScript:
    - O Azure AD B2C usa uma versão específica do jQuery. Não inclua outra versão do jQuery. Usar mais de uma versão na mesma página causa problemas.
    - Não há suporte para uso de RequireJS.
    - O Azure AD B2C não dá suporte para a maioria das estruturas do JavaScript.
- As configurações do Azure AD B2C podem ser lidas chamando objetos `window.SETTINGS`, `window.CONTENT`, como o idioma da interface do usuário atual. Não altere o valor desses objetos.
- Para personalizar a mensagem de erro do Azure AD B2C, use a localização em uma política.
- Se algo puder ser obtido usando uma política, geralmente é a maneira recomendada.

## <a name="javascript-samples"></a>Exemplos de JavaScript

### <a name="show-or-hide-a-password"></a>Mostrar ou ocultar uma senha

É uma maneira comum para ajudar seus clientes a obterem êxito nas inscrições deles e permitir que eles vejam o que inseriram como a senha. Essa opção ajuda os usuários a inscrever-se, permitindo que eles vejam e façam correções em suas senhas, caso necessário. Qualquer campo de digitação de senha tem uma caixa de seleção com um rótulo **Mostrar senha**.  Isso permite que o usuário veja a senha em texto sem formatação. Inclua este snippet de código em seu modelo de inscrição ou entrada para uma página autodeclarada:

```Javascript
function makePwdToggler(pwd){
  // Create show-password checkbox
  var checkbox = document.createElement('input');
  checkbox.setAttribute('type', 'checkbox');
  var id = pwd.id + 'toggler';
  checkbox.setAttribute('id', id);

  var label = document.createElement('label');
  label.setAttribute('for', id);
  label.appendChild(document.createTextNode('show password'));

  var div = document.createElement('div');
  div.appendChild(checkbox);
  div.appendChild(label);

  // Add show-password checkbox under password input
  pwd.insertAdjacentElement('afterend', div);

  // Add toggle password callback
  function toggle(){
    if(pwd.type === 'password'){
      pwd.type = 'text';
    } else {
      pwd.type = 'password';
    }
  }
  checkbox.onclick = toggle;
  // For non-mouse usage
  checkbox.onkeydown = toggle;
}

function setupPwdTogglers(){
  var pwdInputs = document.querySelectorAll('input[type=password]');
  for (var i = 0; i < pwdInputs.length; i++) {
    makePwdToggler(pwdInputs[i]);
  }
}

setupPwdTogglers();
```

### <a name="add-terms-of-use"></a>Adicionar termos de uso

Inclua o seguinte código na sua página em que você quer incluir uma caixa de seleção de **termos de uso**. Essa caixa de seleção é normalmente necessária nas páginas de criação de conta local e de criação de conta social.

```Javascript
function addTermsOfUseLink() {
    // find the terms of use label element
    var termsOfUseLabel = document.querySelector('#api label[for="termsOfUse"]');
    if (!termsOfUseLabel) {
        return;
    }

    // get the label text
    var termsLabelText = termsOfUseLabel.innerHTML;

    // create a new <a> element with the same inner text
    var termsOfUseUrl = 'https://docs.microsoft.com/legal/termsofuse';
    var termsOfUseLink = document.createElement('a');
    termsOfUseLink.setAttribute('href', termsOfUseUrl);
    termsOfUseLink.setAttribute('target', '_blank');
    termsOfUseLink.appendChild(document.createTextNode(termsLabelText));

    // replace the label text with the new element
    termsOfUseLabel.replaceChild(termsOfUseLink, termsOfUseLabel.firstChild);
}
```

No código, substitua `termsOfUseUrl` pelo link para o contrato de termos de uso. Para seu diretório, crie um novo atributo de usuário chamado **termsOfUse** e, em seguida, inclua **termsOfUse** como um atributo de usuário.

## <a name="next-steps"></a>Próximas etapas

Encontre mais informações sobre como você pode personalizar a interface do usuário de seus aplicativos em [Personalizar a interface do usuário do seu aplicativo no Azure Active Directory B2C](customize-ui-with-html.md).
