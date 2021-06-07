---
title: Como habilitar o SSO entre aplicativos no Android usando MSAL | Azure
titleSuffix: Microsoft identity platform
description: Como usar a MSAL (Biblioteca de Autenticação da Microsoft) para Android para habilitar o logon único em aplicativos diferentes.
services: active-directory
author: hamiltonha
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: android
ms.devlang: java
ms.topic: how-to
ms.date: 10/15/2020
ms.author: hahamil
ms.reviewer: marsma
ms.openlocfilehash: f3d4ec8db89e9bebfdcd594e842a6c19d3d66d54
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100104086"
---
# <a name="how-to-enable-cross-app-sso-on-android-using-msal"></a>Como habilitar o SSO entre aplicativos no Android usando a MSAL

Com o SSO (Logon Único), os usuários inserem suas credenciais apenas uma vez, e elas funcionam automaticamente em diferentes aplicativos.

A [plataforma de identidade da Microsoft](./index.yml) e a MSAL (Biblioteca de Autenticação da Microsoft) ajudam a habilitar o SSO em um pacote de aplicativos. Com o recurso de agente e os aplicativos autenticadores, você pode estender a SSO em todo o dispositivo.

Neste passo a passo, você aprenderá a configurar os SDKs usados por seu aplicativo para oferecer o SSO aos clientes.

## <a name="prerequisites"></a>Pré-requisitos

Para usar estas instruções, você deve saber como fazer o seguinte:

- Provisionar o aplicativo usando o portal do Azure. Para obter mais informações sobre esse tópico, confira as instruções para criação de aplicativos no [tutorial do Android](./tutorial-v2-android.md#create-a-project)
- Integrar o aplicativo à [Biblioteca de Autenticação da Microsoft para Android](https://github.com/AzureAD/microsoft-authentication-library-for-android).

## <a name="methods-for-single-sign-on"></a>Métodos de logon único

Os aplicativos que usam a MSAL para Android podem realizar o SSO de duas maneiras:

* Por meio de um [aplicativo agente](#sso-through-brokered-authentication)
* Por meio do [navegador do sistema](#sso-through-system-browser)


   É recomendável usar um aplicativo de agente para benefícios como SSO em todo o dispositivo, gerenciamento de conta e acesso condicional. No entanto, ele exige que os usuários baixem aplicativos adicionais.

## <a name="sso-through-brokered-authentication"></a>SSO por meio de autenticação agenciada

Recomendamos que você use um dos agentes de autenticação da Microsoft para participar do SSO em todo o dispositivo e cumprir as políticas de acesso condicional organizacional. A integração com um agente oferece os seguintes benefícios:

- Logon único no dispositivo
- Acesso condicional para:
  - Proteção de Aplicativo do Intune
  - Registro de dispositivo (Workplace Join)
  - Gerenciamento do Dispositivo Móvel
- Gerenciamento de conta em todo o dispositivo
  -  via Android AccountManager e configurações da conta
  - "Conta de trabalho"– tipo de conta personalizada

No Android, o Microsoft Authentication Broker é um componente incluído nos aplicativos [Microsoft Authenticator](https://play.google.com/store/apps/details?id=com.azure.authenticator) e [Portal da Empresa do Intune](https://play.google.com/store/apps/details?id=com.microsoft.windowsintune.companyportal).

O diagrama a seguir mostra a relação entre o aplicativo, a MSAL (Biblioteca de Autenticação da Microsoft) e os agentes de autenticação da Microsoft.

![Diagrama mostrando como um aplicativo se relaciona à MSAL, aos aplicativos de agente e ao gerenciador de contas do Android.](./media/brokered-auth/brokered-deployment-diagram.png)

### <a name="installing-apps-that-host-a-broker"></a>Instalação de aplicativos que hospedam um agente

O proprietário do dispositivo pode instalar os aplicativos de hospedagem de agente usando a App Store (geralmente a Google Play Store) a qualquer momento. No entanto, algumas APIs (recursos) são protegidas por políticas de acesso condicional que exigem que os dispositivos sejam:

- Registrados (ingressados no local de trabalho) e/ou
- Registrados no gerenciamento de dispositivos ou
- Registrados na Proteção de Aplicativo do Intune

Se o dispositivo ainda não tiver um aplicativo de agente instalado, a MSAL instruirá o usuário a instalar um assim que o aplicativo tentar obter um token interativamente. O aplicativo precisará conduzir o usuário pelas etapas para tornar o dispositivo compatível com a política necessária.

### <a name="effects-of-installing-and-uninstalling-a-broker"></a>Efeitos da instalação e desinstalação de um agente

#### <a name="when-a-broker-is-installed"></a>Quando um agente é instalado

Quando um agente é instalado em um dispositivo, todas as solicitações de token interativas subsequentes (chamadas para `acquireToken()`) são tratadas pelo agente em vez de localmente pela MSAL. Os estados de SSO disponíveis anteriormente para a MSAL não estão disponíveis para o agente. Por isso, o usuário precisará autenticar novamente ou selecionar uma conta na lista de contas conhecidas pelo dispositivo.

A instalação de um agente não exige que o usuário entre novamente. Somente quando o usuário precisar resolver um `MsalUiRequiredException` é que a próxima solicitação vai ao agente. `MsalUiRequiredException` pode surgir por vários motivos e precisa ser resolvido interativamente. Por exemplo:

- O usuário alterou a senha associada à sua conta.
- A conta do usuário não atende mais a uma política de acesso condicional.
- O usuário revogou seu consentimento para que o aplicativo seja associado à sua conta.

**Vários agentes** – se houver mais de um agente instalado no dispositivo, o agente que foi instalado primeiro será sempre o agente ativo. Apenas um agente pode estar ativo em um dispositivo.

#### <a name="when-a-broker-is-uninstalled"></a>Quando um agente é desinstalado

Se houver apenas um aplicativo de hospedagem de agente instalado e ele for removido, o usuário precisará entrar novamente. A desinstalação do agente ativo remove do dispositivo a conta e os tokens associados.

Se o Portal da Empresa do Intune estiver instalado e operando como o agente ativo e o Microsoft Authenticator também estiver instalado, se o Portal da Empresa do Intune (agente ativo) for desinstalado, o usuário precisará entrar novamente. Quando o usuário entrar novamente, o aplicativo Microsoft Authenticator se tornará o agente ativo.

### <a name="integrating-with-a-broker"></a>Integração com um agente

#### <a name="generate-a-redirect-uri-for-a-broker"></a>Gerar um URI de redirecionamento para um agente

Você deve registrar um URI de redirecionamento que seja compatível com o agente. O URI de redirecionamento do agente deve incluir o nome do pacote do aplicativo e a representação da assinatura do aplicativo codificada em base64.

O formato do URI de redirecionamento é: `msauth://<yourpackagename>/<base64urlencodedsignature>`

Você pode usar o [keytool](https://manpages.debian.org/buster/openjdk-11-jre-headless/keytool.1.en.html) para gerar um hash de assinatura codificado em base64 com as chaves de assinatura do aplicativo e, em seguida, usar o portal do Azure para gerar o URI de redirecionamento com o hash.

Linux e macOS:

```bash
keytool -exportcert -alias androiddebugkey -keystore ~/.android/debug.keystore | openssl sha1 -binary | openssl base64
```

Windows:

```powershell
keytool -exportcert -alias androiddebugkey -keystore %HOMEPATH%\.android\debug.keystore | openssl sha1 -binary | openssl base64
```

Depois de gerar um hash de assinatura com o *keytool*, use o portal do Azure para gerar o URI de redirecionamento:

1. Entre no <a href="https://portal.azure.com/" target="_blank">portal do Azure</a> e selecione o aplicativo Android nos **registros de aplicativo**.
1. Selecione **Autenticação** > **Adicionar uma plataforma** > **Android**.
1. No painel **Configure seu aplicativo Android** que é aberto, insira o **hash de assinatura** que você gerou anteriormente e o **nome de pacote**.
1. Clique no botão **Configurar**.

O portal do Azure gera o URI de redirecionamento e o exibe no campo **URI de redirecionamento** do painel **Configuração do Android**.

Para obter mais informações sobre como assinar seu aplicativo, consulte [Assinar seu aplicativo](https://developer.android.com/studio/publish/app-signing) no guia do usuário do Android Studio.

> [!IMPORTANT]
> Use a chave de assinatura de produção para a versão de produção do aplicativo.

#### <a name="configure-msal-to-use-a-broker"></a>Configurar a MSAL para usar um agente

Para usar um agente no aplicativo, você deve atestar que configurou o redirecionamento do agente. Por exemplo, para incluir o URI de redirecionamento habilitado para agente e indicar o registro dele, inclua as seguintes configurações no arquivo de configuração da MSAL:

```json
"redirect_uri" : "<yourbrokerredirecturi>",
"broker_redirect_uri_registered": true
```

#### <a name="broker-related-exceptions"></a>Exceções relacionadas ao agente

A MSAL se comunica com o agente de duas maneiras:

- Serviço associado ao agente
- Android AccountManager

A MSAL primeiro usa o serviço associado ao agente, pois a chamada ao serviço não requer nenhuma permissão do Android. Se a associação ao serviço associado falhar, a MSAL usará a API do Android AccountManager. A MSAL só fará isso se o aplicativo já tiver recebido a permissão `"READ_CONTACTS"`.

Caso ocorra um `MsalClientException` com o código de erro `"BROKER_BIND_FAILURE"`, há duas opções:

- Peça ao usuário para desabilitar a otimização de energia do aplicativo Microsoft Authenticator e do Portal da Empresa do Intune.
- Peça ao usuário para conceder a permissão `"READ_CONTACTS"`

### <a name="verify-broker-integration"></a>Verificar a integração do agente

Talvez não fique claro imediatamente que a integração do agente está funcionando, mas você pode usar as seguintes etapas para verificar:

1. No dispositivo Android, realize uma solicitação usando o agente.
1. Nas configurações do dispositivo Android, procure uma conta recém-criada correspondente à conta com a qual você se autenticou. A conta deve ser do tipo *conta corporativa*.

Você poderá remover a conta das configurações se quiser repetir o teste.

## <a name="sso-through-system-browser"></a>SSO por meio do navegador do sistema

Os aplicativos Android têm a opção de usar o WebView, o navegador do sistema ou as guias personalizadas do Chrome para a autenticação do usuário. Quando o aplicativo não usa a autenticação agenciada, é necessário usar o navegador do sistema em vez do WebView nativo para obter o SSO.

### <a name="authorization-agents"></a>Agentes de autorização

A escolha de uma estratégia para agentes de autorização é opcional e representa uma funcionalidade adicional que os aplicativos podem personalizar. A maioria dos aplicativos usa os padrões da MSAL (veja os vários padrões em [Noções básicas sobre o arquivo de configuração da MSAL do Android](msal-configuration.md)).

A MSAL dá suporte para a autorização usando um `WebView` ou o navegador do sistema.  A imagem abaixo mostra o uso do `WebView` ou do navegador do sistema com CustomTabs ou sem CustomTabs:

![Exemplos de logon da MSAL](./media/authorization-agents/sign-in-ui.jpg)

### <a name="single-sign-on-implications"></a>Implicações do logon único

Por padrão, os aplicativos integrados à MSAL usam as guias personalizadas do navegador do sistema para autorização. Ao contrário dos WebViews, as guias personalizadas compartilham um pote de cookies com o navegador do sistema padrão, permitindo menos entradas com a Web ou outros aplicativos nativos integrados às guias personalizadas.

Se o aplicativo usar uma estratégia de `WebView` sem integrar o suporte para o Microsoft Authenticator ou o Portal da Empresa, os usuários não terão uma experiência de logon único em todo o dispositivo ou entre aplicativos nativos e aplicativos Web.

Se o aplicativo usar a MSAL com um agente como o Microsoft Authenticator ou o Portal da Empresa do Intune, os usuários poderão ter uma experiência de SSO entre aplicativos se eles tiverem uma entrada ativa com um dos aplicativos.

### <a name="webview"></a>WebView

Para usar o WebView no aplicativo, coloque a seguinte linha no JSON de configuração do aplicativo que é passado à MSAL:

```json
"authorization_user_agent" : "WEBVIEW"
```

Ao usar o `WebView` no aplicativo, o usuário entra diretamente no aplicativo. Os tokens são mantidos dentro da área restrita do aplicativo e não estão disponíveis fora do pote de cookies do aplicativo. Por isso, o usuário não pode ter uma experiência de SSO entre aplicativos, a menos que os aplicativos estejam integrados ao Authenticator ou ao Portal da Empresa.

No entanto, o `WebView` permite personalizar a aparência da interface do usuário de entrada. Confira [WebViews do Android](https://developer.android.com/reference/android/webkit/WebView) para obter mais informações sobre como fazer essa personalização.

### <a name="default-browser-plus-custom-tabs"></a>Navegador padrão mais guias personalizadas

Por padrão, a MSAL usa o navegador e uma estratégia de [guias personalizadas](https://developer.chrome.com/multidevice/android/customtabs). Para indicar explicitamente essa estratégia e evitar alterações nas versões futuras do `DEFAULT`, use a seguinte configuração de JSON no arquivo de configuração personalizada:

```json
"authorization_user_agent" : "BROWSER"
```

Use essa abordagem para proporcionar uma experiência de SSO por meio do navegador do dispositivo. A MSAL usa um pote de cookies compartilhado, que permite que outros aplicativos nativos ou aplicativos Web obtenham o SSO no dispositivo usando o cookie de sessão persistente definido pela MSAL.

### <a name="browser-selection-heuristic"></a>Heurística de seleção do navegador

Como é impossível para a MSAL especificar o pacote exato do navegador a ser usado em cada um dos vários celulares Android, ela implementa uma heurística de seleção de navegador que tenta oferecer o melhor SSO entre dispositivos.

A MSAL principalmente identifica o navegador padrão no gerenciador de pacotes e verifica se ele está em uma lista testada de navegadores seguros. Caso contrário, a MSAL usa o WebView em vez de iniciar outro navegador não padrão na lista de confiança. O navegador padrão será escolhido independentemente do suporte para guias personalizadas. Se o navegador der suporte para guias personalizadas, a MSAL iniciará a guia personalizada. As guias personalizadas têm aparência mais próxima de um `WebView` no aplicativo e permitem a personalização básica da interface do usuário. Confira [Guias personalizadas no Android](https://developer.chrome.com/multidevice/android/customtabs) para saber mais.

Se não houver pacotes de navegador no dispositivo, a MSAL usará o `WebView` no aplicativo. Se a configuração padrão do dispositivo não for alterada, o mesmo navegador deverá ser iniciado para cada conexão para garantir uma experiência de SSO.

#### <a name="tested-browsers"></a>Navegadores testados

Os seguintes navegadores foram testados para verificar se eles redirecionam corretamente para o `"redirect_uri"` especificado no arquivo de configuração:

| Dispositivo | Navegador interno | Chrome | Opera  | Microsoft Edge | Navegador de UC | Firefox |
| -- |:-------------:| -----:|-----:|-----:|-----:|-----:|
| Nexus 4 (API 17) | passagem | passagem |não aplicável |não aplicável |não aplicável |não aplicável |
| Samsung S7 (API 25) | aprovado<sup>1</sup> | passagem | passagem | passagem | falha |passagem |
| Huawei (API 26) |aprovado<sup>2</sup> | passagem | falha | passagem | passagem |passagem |
| Vivo (API 26) |passagem|passagem|passagem|passagem|passagem|falha|
| Pixel 2 (API 26) |passagem | passagem | passagem | passagem | falha |passagem |
| Oppo | passagem | não aplicável<sup>3</sup>|não aplicável  |não aplicável |não aplicável | não aplicável|
| OnePlus (API 25) |passagem | passagem | passagem | passagem | falha |passagem |
| Nexus (API 28) |passagem | passagem | passagem | passagem | falha |passagem |
|MI | passagem | passagem | passagem | passagem | falha |passagem |

<sup>1</sup>O navegador interno da Samsung é o Samsung Internet.<br/>
<sup>2</sup>O navegador interno da Huawei é o Huawei Browser.<br/>
<sup>3</sup>Não é possível alterar o navegador padrão nas configurações de dispositivo da Oppo.

## <a name="next-steps"></a>Próximas etapas

O [modo de dispositivo compartilhado para dispositivos Android](msal-android-shared-devices.md) permite que você configure um dispositivo Android para que ele possa ser facilmente compartilhado por vários funcionários.