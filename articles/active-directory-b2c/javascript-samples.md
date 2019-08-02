---
title: Exemplos de JavaScript – Azure Active Directory B2C | Microsoft Docs
description: Saiba mais sobre como usar o JavaScript no Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 5dfc1ef732c8456356de82f7fe026476fdfc075b
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2019
ms.locfileid: "68227125"
---
# <a name="javascript-samples-for-use-in-azure-active-directory-b2c"></a>Exemplos de JavaScript para uso no Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

É possível adicionar seu próprio código do JavaScript do lado do cliente aos aplicativos do Azure AD (Azure Active Directory) B2C. Para habilitar o JavaScript para seus aplicativos, você deve adicionar um elemento à sua [política personalizada](active-directory-b2c-overview-custom.md), selecionar um [layout de página](page-layout.md)e usar [b2clogin.com](b2clogin.md) em suas solicitações. Este artigo descreve como você pode alterar sua política personalizada para habilitar a execução de script.

> [!NOTE]
> Se você quiser habilitar o JavaScript para fluxos de usuário, consulte [JavaScript e versões de layout de página em Azure Active Directory B2C](user-flow-javascript-overview.md).

## <a name="prerequisites"></a>Pré-requisitos

Selecione um layout de página para os elementos da interface do usuário do seu aplicativo. Se você pretende usar o JavaScript, precisará definir uma versão de layout de página para todas as definições de conteúdo em sua política personalizada.

## <a name="add-the-scriptexecution-element"></a>Adicionar o elemento ScriptExecution

Você habilita a execução do script, adicionando o elemento **ScriptExecution** ao elemento [RelyingParty](relyingparty.md).

1. Abra o arquivo de política personalizada. Por exemplo, *SignUpOrSignin.xml*.
2. Adicione o elemento **ScriptExecution** ao elemento **UserJourneyBehaviors** do **RelyingParty**:

    ```XML
    <RelyingParty>
      <DefaultUserJourney ReferenceId="B2CSignUpOrSignInWithPassword" />
      <UserJourneyBehaviors>
        <ScriptExecution>Allow</ScriptExecution>
      </UserJourneyBehaviors>
      ...
    </RelyingParty>
    ```
3. Salve e carregue o arquivo.

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

É uma maneira comum para ajudar seus clientes a obterem êxito nas inscrições deles e permitir que eles vejam o que inseriram como a senha. Essa opção ajuda os usuários a inscrever-se, permitindo que eles vejam e façam correções em suas senhas, caso necessário. Qualquer campo de digitação de senha tem uma caixa de seleção com um rótulo **Mostrar senha**.  Isso permite que o usuário veja a senha em texto sem formatação. Inclua este trecho de código em seu modelo de inscrição ou entrada para uma página autodeclarada:

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

Encontre mais informações sobre como personalizar a interface do usuário dos seus aplicativos em [Personalizar a interface do usuário do aplicativo usando uma política personalizada no Azure Active Directory B2C](active-directory-b2c-ui-customization-custom.md).
