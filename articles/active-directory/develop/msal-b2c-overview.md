---
title: Saiba como os aplicativos podem interoperar com o Azure AD B2C usando a Biblioteca de Autenticação da Microsoft
description: A MSAL (Biblioteca de Autenticação da Microsoft) permite que aplicativos interoperem com o Azure AD B2C e adquiram tokens para chamar APIs Web seguras. Essas APIs Web podem ser o Microsoft Graph, outras APIs da Microsoft, APIs Web de terceiros ou sua própria API Web.
services: active-directory
documentationcenter: dev-center-name
author: negoe
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/04/2019
ms.author: negoe
ms.reviewer: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: db05f59faf945e425761fe7a20bad3e263246a39
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2019
ms.locfileid: "67849352"
---
# <a name="use-microsoft-authentication-library-to-interoperate-with-azure-active-directory-b2c"></a>Use a Biblioteca de Autenticação da Microsoft para interoperar com o Azure Active Directory B2C

A MSAL (Biblioteca de Autenticação da Microsoft) permite que os desenvolvedores de aplicativos autentiquem os usuários com identidades sociais e locais usando o [Azure Active Directory B2C (Azure AD B2C)](https://docs.microsoft.com/azure/active-directory-b2c/). O Azure AD B2C é um serviço de gerenciamento de identidades. Usando-o, você pode personalizar e controlar como os clientes se inscrevem, entram e gerenciam seus perfis quando usam seus aplicativos.

O Azure AD B2C também permite definir a marca e personalizar a interface do usuário de seus aplicativos para fornecer uma experiência unificada para o cliente.

Este tutorial demonstra como usar a MSAL para interoperar com o Azure AD B2C.

## <a name="prerequisites"></a>Pré-requisitos

Se você ainda não criou seu próprio [locatário do Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant), crie um agora. Também é possível usar um locatário existente do Azure AD B2C.

## <a name="javascript"></a>JavaScript

As etapas a seguir demonstram como um aplicativo de página única pode usar o Azure AD B2C para inscrição e conexão, bem como para chamar uma API Web protegida.

### <a name="step-1-register-your-application"></a>Etapa 1: Registre seu aplicativo

Para implementar a autenticação, primeiro você precisará registrar seu aplicativo. Confira [Registrar seu aplicativo](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp#step-4-register-your-own-web-application-with-azure-ad-b2c) para ver as etapas detalhadas.

### <a name="step-2-download-the-sample-application"></a>Etapa 2: Baixar o aplicativo de exemplo

Baixe o exemplo como um arquivo zip ou clone-o do GitHub:

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

### <a name="step-3-configure-authentication"></a>Etapa 3: Configurar autenticação

1. Abra o arquivo **index.html** no exemplo.

1. Configure a amostra com a ID do aplicativo e a chave que você anotou anteriormente ao registrar seu aplicativo. Altere as linhas de código a seguir substituindo os valores pelos nomes do diretório e de APIs:

   ```javascript
   // The current application coordinates were pre-registered in a B2C directory.

   const msalConfig = {
       auth:{
           clientId: "Enter_the_Application_Id_here",
           authority: "https://login.microsoftonline.com/tfp/<your-tenant-name>.onmicrosoft.com/<your-sign-in-sign-up-policy>",
           b2cScopes: ["https://<your-tenant-name>.onmicrosoft.com/hello/demo.read"],
           webApi: 'http://localhost:5000/hello',
     };

   // create UserAgentApplication instance
   const myMSALObj = new UserAgentApplication(msalConfig);
   ```

O nome do [fluxo de usuário](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies) neste tutorial é **B2C_1_signupsignin1**. Se estiver usando um nome de fluxo de usuário diferente, configure o valor da **autoridade** como o nome em questão.

### <a name="step-4-configure-your-application-to-use-b2clogincom"></a>Etapa 4: Configurar seu aplicativo para usar `b2clogin.com`

Você pode usar `b2clogin.com` em vez de `login.microsoftonline.com` como URL de redirecionamento. Isso é feito em seu aplicativo do Azure AD B2C quando você configura um provedor de identidade para inscrição e entrada.

Usar `b2clogin.com` no contexto de `https://your-tenant-name.b2clogin.com/your-tenant-guid` tem os seguintes efeitos:

- Os serviços da Microsoft consomem menos espaço no cabeçalho do cookie.
- As URLs não incluem mais uma referência à Microsoft. Por exemplo, seu aplicativo do Azure AD B2C provavelmente refere-se a `login.microsoftonline.com`.

 Para usar `b2clogin.com`, você precisa atualizar a configuração de seu aplicativo.  

- Defina a propriedade **validateAuthority** como `false` para que redirecionamentos que usam `b2clogin.com` possam ocorrer.

O exemplo a seguir mostra como definir a propriedade:

```javascript
// The current application coordinates were pre-registered in a B2C directory.

const msalConfig = {
    auth:{
        clientId: "Enter_the_Application_Id_here",
        authority: "https://contoso.b2clogin.com/tfp/contoso.onmicrosoft.com/B2C_1_signupsignin1",
        b2cScopes: ["https://contoso.onmicrosoft.com/demoapi/demo.read"],
        webApi: 'https://contosohello.azurewebsites.net/hello',
        validateAuthority: false;

};
// create UserAgentApplication instance
const myMSALObj = new UserAgentApplication(msalConfig);
```

> [!NOTE]
> O aplicativo Azure AD B2C provavelmente faz referência `login.microsoftonline.com` em vários locais, como as referências de fluxo de usuário e ponto de extremidade de token. Certifique-se de que o ponto de extremidade de autorização, ponto de extremidade de token e emissor foram atualizados para usar `your-tenant-name.b2clogin.com`.

Siga [este exemplo de JavaScript da MSAL](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp#single-page-application-built-on-msaljs-with-azure-ad-b2c) de como usar a Versão Prévia da MSAL para JavaScript (MSAL.js). O exemplo obtém um token de acesso e chama uma API protegida pelo Azure AD B2C.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre:

- [Políticas personalizadas](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview-custom)
- [Personalização da interface do usuário](https://docs.microsoft.com/azure/active-directory-b2c/customize-ui-overview)