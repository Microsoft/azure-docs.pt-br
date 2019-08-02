---
title: Configuração de aplicativo cliente (biblioteca de autenticação da Microsoft) | Azure
description: Saiba mais sobre as opções de configuração para clientes públicos e aplicativos cliente confidenciais na MSAL (biblioteca de autenticação da Microsoft).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/16/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 767f7362a6c46d864ba17f23f6506bf6cdb71414
ms.sourcegitcommit: 770b060438122f090ab90d81e3ff2f023455213b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/17/2019
ms.locfileid: "68304725"
---
# <a name="application-configuration-options"></a>Opções de configuração de aplicativo

No seu código, você inicializa um novo aplicativo cliente público ou confidencial (ou agente do usuário para MSAL. js) para autenticar e adquirir tokens. Você pode definir várias opções de configuração ao inicializar o aplicativo cliente na biblioteca de autenticação da Microsoft (MSAL). Essas opções se enquadram em dois grupos:

- Opções de registro, incluindo:
    - [Autoridade](#authority) (composto pela [instância](#cloud-instance) do provedor de identidade e pelo [público](#application-audience) de entrada para o aplicativo, e possivelmente pela ID do locatário).
    - [ID do cliente](#client-id).
    - [URI](#redirect-uri)de redirecionamento.
    - [Segredo do cliente](#client-secret) (para aplicativos cliente confidenciais).
- [Opções de log](#logging), incluindo nível de log, controle de dados pessoais e o nome do componente usando a biblioteca.

## <a name="authority"></a>Authority
A autoridade é uma URL que indica um diretório do qual MSAL pode solicitar tokens. As autoridades comuns são:

- https\://login.microsoftonline.com/\<locatário\>/, em &lt;que&gt; Tenant é a ID do locatário do locatário do Azure Active Directory (Azure AD) ou um domínio associado a este locatário do Azure AD. Usado somente para conectar usuários de uma organização específica.
- https\://login.microsoftonline.com/Common/. Usado para conectar usuários com contas corporativas e de estudante ou contas pessoais da Microsoft.
- https\://login.microsoftonline.com/Organizations/. Usado para conectar usuários com contas corporativas e de estudante.
- https\://login.microsoftonline.com/consumers/. Usado para conectar usuários somente com contas pessoais da Microsoft (anteriormente conhecidas como contas do Windows Live ID).

A configuração de autoridade precisa ser consistente com o que é declarado no portal de registro de aplicativo.

A URL da autoridade é composta pela instância e pelo público.

A autoridade pode ser:
- Uma autoridade de nuvem do Azure AD.
- Uma autoridade de Azure AD B2C. Consulte as [especificações do B2C](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics).
- Uma autoridade de Serviços de Federação do Active Directory (AD FS) (AD FS). Consulte [suporte a AD FS](https://aka.ms/msal-net-adfs-support).

As autoridades de nuvem do Azure AD têm duas partes:
- A *instância* do provedor de identidade
- O *público-alvo* para o aplicativo

A instância e o público podem ser concatenados e fornecidos como a URL da autoridade. Em versões do MSAL.NET anteriores à MSAL 3. *x*, você tinha que compor a autoridade por conta própria, com base na nuvem que deseja direcionar e o público-alvo da entrada.  Este diagrama mostra como a URL da autoridade é composta:

![Como a URL da autoridade é composta](media/msal-client-application-configuration/authority.png)

## <a name="cloud-instance"></a>Instância de nuvem
A *instância* é usada para especificar se seu aplicativo está assinando usuários da nuvem pública do Azure ou de nuvens nacionais. Usando o MSAL em seu código, você pode definir a instância de nuvem do Azure usando uma enumeração ou passando a URL para a [instância de nuvem nacional](authentication-national-cloud.md#azure-ad-authentication-endpoints) como o `Instance` membro (se você souber).

MSAL.NET gerará uma exceção explícita se `Instance` and `AzureCloudInstance` forem especificadas.

Se você não especificar uma instância, seu aplicativo será direcionado para a instância de nuvem pública do Azure ( `https://login.onmicrosoftonline.com`a instância de URL).

## <a name="application-audience"></a>Público-alvo do aplicativo

O público-alvo depende das necessidades comerciais para seu aplicativo:
- Se você for um desenvolvedor de LOB (linha de negócios), provavelmente produzirá um aplicativo de locatário único que será usado somente em sua organização. Nesse caso, você precisa especificar a organização, seja por sua ID de locatário (a ID da sua instância do Azure AD) ou por um nome de domínio associado à instância do Azure AD.
- Se você for um ISV, talvez queira conectar usuários com suas contas corporativas e de estudante em qualquer organização ou em algumas organizações (aplicativo multilocatário). Mas talvez você também queira fazer com que os usuários entrem com suas contas pessoais da Microsoft.

### <a name="how-to-specify-the-audience-in-your-codeconfiguration"></a>Como especificar o público em seu código/configuração
Usando o MSAL em seu código, você especifica o público usando um dos seguintes valores:
- A enumeração de audiência da autoridade do Azure AD
- A ID do locatário, que pode ser:
  - Um GUID (a ID da sua instância do Azure AD) para aplicativos de locatário único
  - Um nome de domínio associado à sua instância do Azure AD (também para aplicativos de locatário único)
- Um desses espaços reservados como uma ID de locatário no lugar da enumeração de audiência da autoridade do Azure AD:
    - `organizations`para um aplicativo multilocatário
    - `consumers`para conectar usuários somente com suas contas pessoais
    - `common`para conectar usuários com suas contas corporativas e de estudante ou suas contas pessoais da Microsoft

O MSAL gerará uma exceção significativa se você especificar o público da autoridade do Azure AD e a ID do locatário.

Se você não especificar um público, seu aplicativo será direcionado ao Azure AD e às contas pessoais da Microsoft como um público. (Ou seja, ele se comportará `common` como se tivesse sido especificado.)

### <a name="effective-audience"></a>Público-alvo efetivo
O público efetivo para seu aplicativo será o mínimo (se houver uma interseção) do público que você definiu em seu aplicativo e o público especificado no registro do aplicativo. Na verdade, a experiência de [registros de aplicativo](https://aka.ms/appregistrations) permite especificar o público (os tipos de conta com suporte) para o aplicativo. Para obter mais informações, confira [Início Rápido: Registre um aplicativo com a plataforma](quickstart-register-app.md)de identidade da Microsoft.

Atualmente, a única maneira de obter um aplicativo para conectar usuários com apenas contas pessoais da Microsoft é definir essas duas configurações:
- Defina o público de registro do `Work and school accounts and personal accounts`aplicativo como.
- Defina o público em seu código/configuração como `AadAuthorityAudience.PersonalMicrosoftAccount` (ou `TenantID` = "consumidores").

## <a name="client-id"></a>ID do cliente
A ID do cliente é a ID exclusiva do aplicativo (cliente) atribuída ao seu aplicativo pelo Azure AD quando o aplicativo foi registrado.

## <a name="redirect-uri"></a>URI de redirecionamento
O URI de redirecionamento é o URI para o qual o provedor de identidade enviará os tokens de segurança.

### <a name="redirect-uri-for-public-client-apps"></a>URI de redirecionamento para aplicativos cliente públicos
Se você for um desenvolvedor de aplicativo cliente público que está usando o MSAL:
- Você desejaria usar `.WithDefaultRedirectUri()` em aplicativos da área de trabalho ou UWP (MSAL.NET 4.1 +). Esse método definirá a propriedade URI de redirecionamento do aplicativo cliente público para o URI de redirecionamento recomendado padrão para aplicativos cliente públicos. 

  Plataforma  | URI de redirecionamento  
  ---------  | --------------
  Aplicativo de desktop (FW .NET) | `https://login.microsoftonline.com/common/oauth2/nativeclient` 
  UWP | valor de `WebAuthenticationBroker.GetCurrentApplicationCallbackUri()`. Isso habilita o SSO com o navegador definindo o valor como o resultado de WebAuthenticationBroker. GetCurrentApplicationCallbackUri () que você precisa registrar
  .NET Core | `https://localhost`. Isso permite que o usuário use o navegador do sistema para autenticação interativa, já que o .NET Core não tem uma interface do usuário para a exibição da Web inserida no momento.

- Você não precisará adicionar um URI de redirecionamento se estiver criando um aplicativo Xamarin Android e Ios que não dá suporte ao agente (o URI de `msal{ClientId}://auth` redirecionamento é definido automaticamente como para o Xamarin Android e Ios

- Você precisa configurar o URI de redirecionamento no [registros de aplicativo](https://aka.ms/appregistrations):

   ![URI de redirecionamento no Registros de aplicativo](media/msal-client-application-configuration/redirect-uri.png)

Você pode substituir o URI de redirecionamento `RedirectUri` usando a propriedade (por exemplo, se você usar agentes). Aqui estão alguns exemplos de URIs de redirecionamento para esse cenário:

- `RedirectUriOnAndroid` = "msauth-5a434691-ccb2-4fd1-b97b-b64bcfbc03fc://com.microsoft.identity.client.sample";
- `RedirectUriOnIos`= $ "msauth. {Bundle. ID}://auth ";

Para obter detalhes, consulte a [documentação para Android e Ios](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Leveraging-the-broker-on-iOS).

### <a name="redirect-uri-for-confidential-client-apps"></a>URI de redirecionamento para aplicativos cliente confidenciais
Para aplicativos Web, o URI de redirecionamento (ou URI de resposta) é o URI que o AD do Azure usará para enviar o token de volta para o aplicativo. Esse URI pode ser a URL do aplicativo Web/API Web se o aplicativo confidencial for um desses. O URI de redirecionamento precisa ser registrado no registro do aplicativo. Esse registro é especialmente importante quando você implanta um aplicativo que você testou inicialmente localmente. Em seguida, você precisa adicionar a URL de resposta do aplicativo implantado no portal de registro de aplicativo.

Para aplicativos de daemon, você não precisa especificar um URI de redirecionamento.

## <a name="client-secret"></a>Segredo do cliente
Esta opção especifica o segredo do cliente para o aplicativo cliente confidencial. Esse segredo (senha de aplicativo) é fornecido pelo portal de registro de aplicativo ou fornecido ao Azure AD durante o registro do aplicativo com o PowerShell AzureAD, PowerShell AzureRM ou CLI do Azure.

## <a name="logging"></a>Registrando em log
As outras opções de configuração habilitam o registro em log e a solução de problemas. Consulte o artigo sobre [registro em log](msal-logging.md) para obter detalhes sobre como usá-los.

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre como [criar uma instância de aplicativos cliente usando o MSAL.net](msal-net-initializing-client-applications.md).

Saiba mais sobre como [criar uma instância de aplicativos cliente usando o MSAL. js](msal-js-initializing-client-applications.md).
