---
title: Aplicativo de daemon chamando APIs Web (configuração de aplicativo)-plataforma de identidade da Microsoft
description: Saiba como criar um aplicativo daemon que chama APIs da Web (configuração de aplicativo)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/16/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 705545fd5167087be1a001c45f58907d6ff225e8
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68277837"
---
# <a name="daemon-app-that-calls-web-apis---code-configuration"></a>Aplicativo daemon que chama a configuração de código de APIs da Web

Saiba como configurar o código para seu aplicativo daemon que chama APIs da Web.

## <a name="msal-libraries-supporting-daemon-apps"></a>Bibliotecas de MSAL com suporte a aplicativos de daemon

As bibliotecas da Microsoft que oferecem suporte a aplicativos daemon são:

  Biblioteca MSAL | DESCRIÇÃO
  ------------ | ----------
  ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | As plataformas com suporte para criar um aplicativo daemon são .NET Framework e plataformas .NET Core (não UWP, Xamarin. iOS e Xamarin. Android, pois essas plataformas são usadas para criar aplicativos cliente públicos)
  ![Python](media/sample-v2-code/logo_python.png) <br/> MSAL.Python | Desenvolvimento em andamento-em visualização pública
  ![Java](media/sample-v2-code/logo_java.png) <br/> MSAL.Java | Desenvolvimento em andamento-em visualização pública

## <a name="configuration-of-the-authority"></a>Configuração da autoridade

Considerando que os aplicativos de daemon não usam permissões delegadas, mas permissões de aplicativo, seu *tipo de conta com suporte* não pode ser *contas em qualquer diretório organizacional e contas pessoais da Microsoft (por exemplo, Skype, Xbox, Outlook.com)* . Na verdade, não há nenhum administrador de locatários para conceder consentimento ao aplicativo de daemon para contas pessoais da Microsoft. Você precisará escolher *contas em minha organização* ou *contas em qualquer organização*.

Portanto, a autoridade especificada na configuração do aplicativo deve ser o locatário-Ed (especificando uma ID de locatário ou um nome de domínio associado à sua organização).

Se você for um ISV e quiser fornecer uma ferramenta multilocatário, poderá usar `organizations`o. Mas tenha em mente que você também precisará explicar aos seus clientes como conceder o consentimento do administrador. Consulte [solicitando consentimento para um locatário inteiro](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant) para obter detalhes. Além disso, há uma limitação no MSAL que `organizations` só é permitida quando as credenciais do cliente são um segredo do aplicativo (não um certificado). Consulte [MSAL.net bug #891](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/891)

## <a name="application-configuration-and-instantiation"></a>Configuração e instanciação do aplicativo

Em bibliotecas MSAL, as credenciais do cliente (segredo ou certificado) são passadas como um parâmetro da construção do aplicativo cliente confidencial.

> [!IMPORTANT]
> Mesmo que seu aplicativo seja um aplicativo de console em execução como um serviço, se for um aplicativo de daemon, ele precisará ser um aplicativo cliente confidencial.

### <a name="msalnet"></a>MSAL.NET

Adicione o pacote NuGet [Microsoft. IdentityClient](https://www.nuget.org/packages/Microsoft.Identity.Client) ao seu aplicativo.

Usar o namespace MSAL.NET

```CSharp
using Microsoft.Identity.Client;
```

O aplicativo daemon será apresentado por um`IConfidentialClientApplication`

```CSharp
IConfidentialClientApplication app;
```

Este é o código para criar um aplicativo com um segredo do aplicativo:

```CSharp
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
           .WithClientSecret(config.ClientSecret)
           .WithAuthority(new Uri(config.Authority))
           .Build();
```

Este é o código para criar um aplicativo com um certificado:

```CSharp
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
    .WithCertificate(certificate)
    .WithAuthority(new Uri(config.Authority))
    .Build();
```

Por fim, em vez de um segredo do cliente ou um certificado, o aplicativo cliente confidencial também pode provar sua identidade usando as declarações do cliente. Este cenário avançado é detalhado em [asserções do cliente](msal-net-client-assertions.md)


### <a name="msalpython"></a>MSAL.Python

```Python
# Create a preferably long-lived app instance which maintains a token cache.

app = msal.ConfidentialClientApplication(
    config["client_id"], authority=config["authority"],
    client_credential=config["secret"],
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache

    )
```

### <a name="msaljava"></a>MSAL.Java

```Java
PrivateKey key = getPrivateKey();
X509Certificate publicCertificate = getPublicCertificate();

// create clientCredential with public and private key
IClientCredential credential = ClientCredentialFactory.create(key, publicCertificate);

ConfidentialClientApplication cca = ConfidentialClientApplication
  .builder(CLIENT_ID, credential)
  .authority(AUTHORITY_MICROSOFT)
  .build();
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Aplicativo de daemon-adquirindo tokens para o aplicativo](./scenario-daemon-acquire-token.md)
