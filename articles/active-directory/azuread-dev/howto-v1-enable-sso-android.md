---
title: Como habilitar o SSO entre aplicativos no Android usando a ADAL | Microsoft Docs
description: Como usar os recursos do SDK do ADAL para habilitar o logon único em seus aplicativos.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.tgt_pltfrm: android
ms.devlang: java
ms.topic: how-to
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: brandwe, jmprieur
ms.custom: aaddev, devx-track-java
ROBOTS: NOINDEX
ms.openlocfilehash: 9d1faf7aed5cf4c4975925c7a56d169dc99a7bd5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "87313584"
---
# <a name="how-to-enable-cross-app-sso-on-android-using-adal"></a>Como habilitar o SSO entre aplicativos no Android usando a ADAL

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

SSO (logon único) permite que os usuários só digitem suas credenciais uma vez, e essas credenciais funcionarão automaticamente entre aplicativos e entre plataformas que outros aplicativos possam usar (como Contas Microsoft ou uma conta corporativa do Microsoft 365), não importa o editor.

A plataforma de identidade da Microsoft, juntamente com os SDKs, torna fácil habilitar SSO em seu próprio pacote de aplicativos, ou com a funcionalidade de agente e aplicativos Autenticadores, em todo o dispositivo.

Neste passo a passo, você aprenderá a configurar o SDK em seu aplicativo para fornecer SSO aos seus clientes.

## <a name="prerequisites"></a>Pré-requisitos

Estas instruções pressupõem que você sabe como:

- Provisionar seu aplicativo usando o portal herdado para o Azure AD (Azure Active Directory). Para obter mais informações, consulte [registrar um aplicativo](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)
- Integrar seu aplicativo com o [SDK do Android para Azure AD](https://github.com/AzureAD/azure-activedirectory-library-for-android).

## <a name="single-sign-on-concepts"></a>Conceitos de logon único

### <a name="identity-brokers"></a>Agentes de identidade

A Microsoft fornece aplicativos para todas as plataformas móveis que permitem o uso das mesmas credenciais em aplicativos de diferentes fornecedores e para recursos aprimorados que exigem um único local seguro do qual validar as credenciais. Eles são chamados de **agentes**.

No iOS e no Android, agentes são fornecidos por meio de aplicativos disponíveis para download que os clientes instalam de modo independente ou são enviados efetuando push par o dispositivo por uma empresa que gerencia alguns ou todos os dispositivos para os funcionários. Agentes oferecem suporte ao gerenciamento de segurança apenas para alguns aplicativos ou todo o dispositivo com base na configuração do administrador de TI. No Windows, essa funcionalidade é fornecida por um seletor de conta integrado ao sistema operacional, conhecido tecnicamente como o Agente de Autenticação da Web.

#### <a name="broker-assisted-login"></a>Logon assistido por agente

Os logons assistido por agente são experiências de logon que ocorrem dentro do aplicativo do agente e usam o armazenamento e a segurança do agente para compartilhar as credenciais em todos os aplicativos no dispositivo, e aplicam a plataforma de identidade. A implicação de que os aplicativos dependerão do agente para fazer logon dos usuários. No iOS e no Android, esses agentes são fornecidos por meio de aplicativos baixáveis que os clientes instalam de forma independente ou que podem ser enviados por push ao dispositivo por uma empresa que gerencia o dispositivo para o usuário. Um exemplo desse tipo de aplicativo é o Microsoft Authenticator no iOS. No Windows, essa funcionalidade é fornecida por um seletor de conta integrado ao sistema operacional, conhecido tecnicamente como o Agente de Autenticação da Web.
A experiência varia de acordo com a plataforma e, às vezes, pode ser perturbador para os usuários se não for gerenciada corretamente. Provavelmente você estará mais familiarizado com esse padrão se tiver instalado o aplicativo do Facebook e usado o Facebook Connect em outro aplicativo. A plataforma de identidade usa o mesmo padrão.

Em Android, o seletor de conta é exibido na parte superior de seu aplicativo, o que é menos perturbador para o usuário.

#### <a name="how-the-broker-gets-invoked"></a>Como o agente é invocado

Se um agente compatível for instalado no dispositivo, como o aplicativo Microsoft Authenticator, os SDKs de identidade farão automaticamente o trabalho de invocar o agente para você quando um usuário indicar que deseja fazer logon usando qualquer conta da plataforma de identidade.

#### <a name="how-microsoft-ensures-the-application-is-valid"></a>Como a Microsoft garante que o aplicativo é válido?

A necessidade de garantir que a identidade de um aplicativo que chama o agente seja crucial para a segurança fornecida nos logons assistidos do agente. Nem o iOS nem Android impõem identificadores exclusivos que sejam válidos somente para um determinado aplicativo, portanto, aplicativos mal-intencionados podem "falsificar" o identificador de um aplicativo legítimo e receber os tokens destinados ao aplicativo legítimo. Para garantir que a Microsoft esteja sempre se comunicando com o aplicativo certo no runtime, é pedido ao desenvolvedor que forneça um redirectURI personalizado ao registrar seu aplicativo com a Microsoft. **O modo como os desenvolvedores devem criar esse URI de redirecionamento é abordado com detalhes logo abaixo.** Este redirectURI personalizado contém a impressão digital do certificado do aplicativo e tem a garantia da Google Play Store de ser exclusivo para o aplicativo. Quando um aplicativo chama o agente, o agente solicita que o sistema operacional Android o forneça com a impressão digital do certificado que chamou o agente. O agente fornece a impressão digital do certificado para a Microsoft na chamada para o sistema de identidade. Se a impressão digital do certificado do aplicativo não corresponder à impressão digital do certificado fornecida para nós pelo desenvolvedor durante o registro, o acesso será negado aos tokens do recurso que o aplicativo está solicitando. Essa verificação garante que apenas o aplicativo registrado pelo desenvolvedor receba tokens.

Os logons SSO de agente têm os seguintes benefícios:

* O usuário desfruta do SSO em todos os aplicativos, independentemente do fornecedor.
* Seu aplicativo pode usar recursos corporativos mais avançados, como o Acesso Condicional, e oferecer suporte a cenários do Intune.
* Seu aplicativo pode oferecer suporte à autenticação baseada em certificado para usuários corporativos.
* Uma experiência de entrada mais segura, pois a identidade do aplicativo e do usuário são verificadas pelo aplicativo agente com criptografia e algoritmos de segurança adicionais.

Veja uma representação de como os SDKs funcionam com os aplicativos agentes a fim de habilitar o SSO:

```
+------------+ +------------+   +-------------+
|            | |            |   |             |
|   App 1    | |   App 2    |   |   Someone   |
|            | |            |   |    else's   |
|            | |            |   |     App     |
+------------+ +------------+   +-------------+
|  ADAL SDK  | |  ADAL SDK  |   |  ADAL SDK   |
+-----+------+-+-----+------+-  +-------+-----+
      |              |                  |
      |       +------v------+           |
      |       |             |           |
      |       | Microsoft   |           |
      +-------> Broker      |^----------+
              | Application
              |             |
              +-------------+
              |             |
              |   Broker    |
              |   Storage   |
              |             |
              +-------------+

```

### <a name="turning-on-sso-for-broker-assisted-sso"></a>Ativar o SSO assistido por agente

A capacidade de um aplicativo usar qualquer agente instalado no dispositivo está desativada por padrão. Para usar seu aplicativo com o agente, você deve fazer algumas configurações adicionais e adicionar algum código ao seu aplicativo.

Execute estas etapas:

1. Habilitar o modo de agente na chamada do código do aplicativo para o SDK do MS
2. Estabelecer um novo URI de redirecionamento e fornecê-lo ao aplicativo e no registro do aplicativo
3. Configurar as permissões corretas no manifesto do Android

#### <a name="step-1-enable-broker-mode-in-your-application"></a>Etapa 1: habilitar o modo de agente em seu aplicativo

A capacidade de seu aplicativo de usar o agente é ativada quando você cria as “configurações” ou configuração inicial da sua instância de autenticação. Para fazer isso em seu aplicativo:

```
AuthenticationSettings.Instance.setUseBroker(true);
```

#### <a name="step-2-establish-a-new-redirect-uri-with-your-url-scheme"></a>Etapa 2: Estabelecer um novo URI de redirecionamento com seu esquema de URL

Para garantir que o aplicativo correto receba os tokens de credencial retornados, é necessário certificar-se de que o retorno de chamada para seu aplicativo de forma que o sistema operacional Android possa verificar. O sistema operacional Android usa o hash do certificado no Google Play Store. Esse hash do certificado não pode ser falsificado por um aplicativo invasor. Juntamente com o URI do nosso aplicativo de agente, a Microsoft garante que os tokens sejam retornados para o aplicativo correto. É necessário que um URI de redirecionamento exclusivo seja registrado no aplicativo.

O URI de redirecionamento deve ser no formato correto de:

`msauth://packagename/Base64UrlencodedSignature`

ex: *msauth://com.example.userapp/IcB5PxIyvbLkbFVtBI%2FitkW%2Fejk%3D*

É possível registrar esse URI de redirecionamento no registro de seu aplicativo usando o [Portal do Azure](https://portal.azure.com/). Para saber mais sobre o registro de aplicativo do Azure AD, confira [Integração com o Azure Active Directory](../develop/active-directory-how-to-integrate.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

#### <a name="step-3-set-up-the-correct-permissions-in-your-application"></a>Etapa 3: Configurar as permissões corretas em seu aplicativo

O aplicativo de agente no Android usa o recurso de Gerenciador de Contas do sistema operacional Android para gerenciar credenciais nos aplicativos. Para usar o agente no Android, seu manifesto de aplicativo deve ter permissões para usar contas do Gerenciador de Contas. Essas permissões são discutidas detalhadamente na [documentação do Google para o Gerente de Contas](https://developer.android.com/reference/android/accounts/AccountManager.html)

Em particular, essas permissões são:

```
GET_ACCOUNTS
USE_CREDENTIALS
MANAGE_ACCOUNTS
```

### <a name="youve-configured-sso"></a>Você configurou o SSO!

Agora, o SDK de identidade compartilhará automaticamente as credenciais em seus aplicativos e invocará o agente se ele estiver presente em seu dispositivo.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre o [protocolo de logon único SAML](../develop/single-sign-on-saml-protocol.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)
