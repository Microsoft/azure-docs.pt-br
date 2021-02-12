---
title: Como habilitar o SSO entre aplicativos no Android usando o MSAL | Azure
titleSuffix: Microsoft identity platform
description: Como usar a MSAL (biblioteca de autenticação da Microsoft) para Android para habilitar o logon único em seus aplicativos.
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
ms.openlocfilehash: c4c98ad377100c35b0c364607bfd3803d07a95a7
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2021
ms.locfileid: "98015922"
---
# <a name="how-to-enable-cross-app-sso-on-android-using-msal"></a>Como habilitar o SSO entre aplicativos no Android usando o MSAL

O SSO (logon único) permite que os usuários insiram suas credenciais apenas uma vez e que essas credenciais funcionem automaticamente entre os aplicativos.

A [plataforma de identidade](./index.yml) da Microsoft e a MSAL (biblioteca de autenticação da Microsoft) ajudam você a habilitar o SSO em seu próprio pacote de aplicativos. Com os aplicativos de capacidade do agente e autenticador, você pode estender o SSO em todo o dispositivo.

Neste "como", você aprenderá a configurar os SDKs usados pelo seu aplicativo para fornecer SSO aos seus clientes.

## <a name="prerequisites"></a>Pré-requisitos

Este "como" pressupõe que você saiba como:

- Provisione seu aplicativo usando o portal do Azure. Para obter mais informações sobre este tópico, consulte as instruções para criar um aplicativo no [tutorial do Android](./tutorial-v2-android.md#create-a-project)
- Integre seu aplicativo com a [biblioteca de autenticação da Microsoft para Android](https://github.com/AzureAD/microsoft-authentication-library-for-android).

## <a name="methods-for-single-sign-on"></a>Métodos para logon único

Há duas maneiras de aplicativos que usam o MSAL para Android para obter o SSO:

* Por meio de um [aplicativo agente](#sso-through-brokered-authentication)
* Por meio do [navegador do sistema](#sso-through-system-browser)


   É recomendável usar um aplicativo de agente para benefícios como SSO de todo o dispositivo, gerenciamento de conta e acesso condicional. No entanto, ele exige que os usuários baixem aplicativos adicionais.

## <a name="sso-through-brokered-authentication"></a>SSO por meio de autenticação orientada

Recomendamos que você use um dos agentes de autenticação da Microsoft para participar do SSO (logon único) em todo o dispositivo e para atender às políticas de acesso condicional organizacional. A integração com um agente oferece os seguintes benefícios:

- Logon único do dispositivo
- Acesso condicional para:
  - Proteção de Aplicativo do Intune
  - Registro de dispositivo (Workplace Join)
  - Gerenciamento do Dispositivo Móvel
- Gerenciamento de conta em todo o dispositivo
  -  por meio do Android Contamanager & configurações de conta
  - "Conta de trabalho"-tipo de conta personalizada

No Android, o Microsoft Authentication Broker é um componente que está incluído no [Microsoft Authenticator](https://play.google.com/store/apps/details?id=com.azure.authenticator) e [portal da empresa do Intune](https://play.google.com/store/apps/details?id=com.microsoft.windowsintune.companyportal) aplicativos.

O diagrama a seguir ilustra a relação entre seu aplicativo, a MSAL (biblioteca de autenticação da Microsoft) e os agentes de autenticação da Microsoft.

![Diagrama mostrando como um aplicativo se relaciona ao MSAL, aos aplicativos do agente e ao Gerenciador de contas do Android.](./media/brokered-auth/brokered-deployment-diagram.png)

### <a name="installing-apps-that-host-a-broker"></a>Instalando aplicativos que hospedam um agente

Os aplicativos de Hospedagem de agente podem ser instalados pelo proprietário do dispositivo de sua loja de aplicativos (normalmente Google Play Store) a qualquer momento. No entanto, algumas APIs (recursos) são protegidas por políticas de acesso condicional que exigem que os dispositivos sejam:

- Registrado (ingressado no local de trabalho) e/ou
- Registrado no gerenciamento de dispositivos ou
- Registrado no Proteção de Aplicativo do Intune

Se um dispositivo ainda não tiver um aplicativo de agente instalado, o MSAL instruirá o usuário a instalar um assim que o aplicativo tentar obter um token interativamente. O aplicativo precisará conduzir o usuário pelas etapas para tornar o dispositivo compatível com a política necessária.

### <a name="effects-of-installing-and-uninstalling-a-broker"></a>Efeitos da instalação e desinstalação de um agente

#### <a name="when-a-broker-is-installed"></a>Quando um agente é instalado

Quando um agente é instalado em um dispositivo, todas as solicitações de token interativo subsequentes (chamadas para `acquireToken()` ) são tratadas pelo agente em vez de localmente por MSAL. Qualquer estado de SSO disponível anteriormente para MSAL não está disponível para o agente. Como resultado, o usuário precisará autenticar novamente ou selecionar uma conta da lista de contas existente conhecida pelo dispositivo.

A instalação de um agente não exige que o usuário entre novamente. Somente quando o usuário precisar resolver um `MsalUiRequiredException` , a próxima solicitação vai para o agente. `MsalUiRequiredException` pode ser lançada por vários motivos e precisa ser resolvida interativamente. Por exemplo:

- O usuário alterou a senha associada à sua conta.
- A conta do usuário não atende mais a uma política de acesso condicional.
- O usuário revogou seu consentimento para que o aplicativo seja associado à sua conta.

**Vários agentes** – se vários agentes estiverem instalados em um dispositivo, o agente que foi instalado primeiro será sempre o agente ativo. Apenas um único agente pode estar ativo em um dispositivo.

#### <a name="when-a-broker-is-uninstalled"></a>Quando um agente é desinstalado

Se houver apenas um aplicativo de Hospedagem de agente instalado e ele for removido, o usuário precisará entrar novamente. A desinstalação do agente ativo remove a conta e os tokens associados do dispositivo.

Se o Portal da Empresa do Intune estiver instalado e estiver operando como o agente ativo, e Microsoft Authenticator também estiver instalado, se o Portal da Empresa do Intune (Active Broker) for desinstalado, o usuário precisará entrar novamente. Depois que eles entrarem novamente, o aplicativo Microsoft Authenticator se tornará o agente ativo.

### <a name="integrating-with-a-broker"></a>Integração com um agente

#### <a name="generate-a-redirect-uri-for-a-broker"></a>Gerar um URI de redirecionamento para um agente

Você deve registrar um URI de redirecionamento que seja compatível com o agente. O URI de redirecionamento para o agente deve incluir o nome do pacote do aplicativo e a representação codificada em base64 da assinatura do seu aplicativo.

O formato do URI de redirecionamento é: `msauth://<yourpackagename>/<base64urlencodedsignature>`

Você pode usar o [keytool](https://manpages.debian.org/buster/openjdk-11-jre-headless/keytool.1.en.html) para gerar um hash de assinatura codificado em base64 usando as chaves de assinatura do aplicativo e, em seguida, usar o portal do Azure para gerar o URI de redirecionamento usando esse hash.

Linux e macOS:

```bash
keytool -exportcert -alias androiddebugkey -keystore ~/.android/debug.keystore | openssl sha1 -binary | openssl base64
```

Windows:

```powershell
keytool -exportcert -alias androiddebugkey -keystore %HOMEPATH%\.android\debug.keystore | openssl sha1 -binary | openssl base64
```

Depois de gerar um hash de assinatura com *keytool*, use o portal do Azure para gerar o URI de redirecionamento:

1. Entre no <a href="https://portal.azure.com/" target="_blank">portal do Azure <span class="docon docon-navigate-external x-hidden-focus"></span></a> e selecione seu aplicativo Android no **registros de aplicativo**.
1. Selecione **autenticação**  >  **Adicionar uma plataforma**  >  **Android**.
1. No painel **configurar seu aplicativo Android** que é aberto, insira o **hash de assinatura** que você gerou anteriormente e um **nome de pacote**.
1. Selecione o botão **Configurar** .

O portal do Azure gera o URI de redirecionamento para você e o exibe no campo **URI de redirecionamento** do painel de **configuração do Android** .

Para obter mais informações sobre como assinar seu aplicativo, consulte [assinar seu aplicativo](https://developer.android.com/studio/publish/app-signing) no guia do usuário do Android Studio.

> [!IMPORTANT]
> Use sua chave de assinatura de produção para a versão de produção do seu aplicativo.

#### <a name="configure-msal-to-use-a-broker"></a>Configurar o MSAL para usar um agente

Para usar um agente em seu aplicativo, você deve atestar que configurou o redirecionamento do agente. Por exemplo, inclua o URI de redirecionamento habilitado para o agente – e indique que você o registrou, incluindo as seguintes configurações no arquivo de configuração do MSAL:

```json
"redirect_uri" : "<yourbrokerredirecturi>",
"broker_redirect_uri_registered": true
```

#### <a name="broker-related-exceptions"></a>Exceções relacionadas ao agente

O MSAL se comunica com o agente de duas maneiras:

- Serviço ligado do agente
- Conta do androidmanager

O MSAL primeiro usa o serviço associado ao agente, pois chamar esse serviço não requer nenhuma permissão do Android. Se a associação ao serviço ligado falhar, o MSAL usará a API de conta do Androidmanager. MSAL só faz isso se seu aplicativo já tiver recebido a `"READ_CONTACTS"` permissão.

Se você receber um `MsalClientException` código de erro com `"BROKER_BIND_FAILURE"` , haverá duas opções:

- Peça ao usuário para desabilitar a otimização de energia para o aplicativo Microsoft Authenticator e o Portal da Empresa do Intune.
- Peça ao usuário para conceder a `"READ_CONTACTS"` permissão

### <a name="verify-broker-integration"></a>Verificar integração do Broker

Talvez não esteja imediatamente claro que a integração do agente está funcionando, mas você pode usar as seguintes etapas para verificar:

1. Em seu dispositivo Android, conclua uma solicitação usando o agente.
1. Nas configurações em seu dispositivo Android, procure uma conta recém-criada correspondente à conta com a qual você se autenticou. A conta deve ser do tipo *conta de trabalho*.

Você poderá remover a conta das configurações se quiser repetir o teste.

## <a name="sso-through-system-browser"></a>SSO por meio do navegador do sistema

Os aplicativos Android têm a opção de usar as guias do WebView, do navegador do sistema ou do Chrome para a experiência do usuário de autenticação. Se o aplicativo não estiver usando a autenticação orientada, será necessário usar o navegador do sistema em vez do WebView nativo para obter o SSO.

### <a name="authorization-agents"></a>Agentes de autorização

A escolha de uma estratégia específica para agentes de autorização é opcional e representa a funcionalidade adicional que os aplicativos podem personalizar. A maioria dos aplicativos usará os padrões MSAL (consulte [entender o arquivo de configuração do MSAL do Android](msal-configuration.md) para ver os vários padrões).

O MSAL dá suporte à autorização usando um `WebView` ou o navegador do sistema.  A imagem abaixo mostra como ela está usando o `WebView` ou o navegador do sistema com CustomTabs ou sem CustomTabs:

![Exemplos de logon do MSAL](./media/authorization-agents/sign-in-ui.jpg)

### <a name="single-sign-on-implications"></a>Implicações de logon único

Por padrão, os aplicativos integrados ao MSAL usam as guias personalizadas do navegador do sistema para autorizar. Ao contrário das exibições, as guias personalizadas compartilham um jar de cookie com o navegador do sistema padrão, permitindo menos entradas com a Web ou outros aplicativos nativos que se integraram com guias personalizadas.

Se o aplicativo usar uma `WebView` estratégia sem integrar Microsoft Authenticator ou portal da empresa suporte em seu aplicativo, os usuários não terão uma experiência de logon único em todo o dispositivo ou entre aplicativos nativos e aplicativos Web.

Se o aplicativo usar o MSAL com um agente como Microsoft Authenticator ou Portal da Empresa do Intune, os usuários poderão ter uma experiência de SSO entre aplicativos se eles tiverem uma entrada ativa com um dos aplicativos.

### <a name="webview"></a>WebView

Para usar o WebView no aplicativo, coloque a seguinte linha no JSON de configuração do aplicativo que é passado para MSAL:

```json
"authorization_user_agent" : "WEBVIEW"
```

Ao usar o no aplicativo `WebView` , o usuário entra diretamente no aplicativo. Os tokens são mantidos dentro da área restrita do aplicativo e não estão disponíveis fora do jar do cookie do aplicativo. Como resultado, o usuário não pode ter uma experiência de SSO entre aplicativos, a menos que os aplicativos se integrem com o autenticador ou Portal da Empresa.

No entanto, o `WebView` fornece a capacidade de personalizar a aparência da interface do usuário de entrada. Consulte [Webviews do Android](https://developer.android.com/reference/android/webkit/WebView) para obter mais informações sobre como fazer essa personalização.

### <a name="default-browser-plus-custom-tabs"></a>Navegador padrão mais guias personalizadas

Por padrão, o MSAL usa o navegador e uma estratégia de [guias personalizadas](https://developer.chrome.com/multidevice/android/customtabs) . Você pode indicar explicitamente essa estratégia para evitar alterações em versões futuras no `DEFAULT` usando a seguinte configuração de JSON no arquivo de configuração personalizada:

```json
"authorization_user_agent" : "BROWSER"
```

Use essa abordagem para fornecer uma experiência de SSO por meio do navegador do dispositivo. O MSAL usa um jar de cookie compartilhado, que permite que outros aplicativos nativos ou aplicativos Web obtenham o SSO no dispositivo usando o cookie de sessão persistente definido por MSAL.

### <a name="browser-selection-heuristic"></a>Heurística de seleção do navegador

Como é impossível para MSAL especificar o pacote exato do navegador a ser usado em cada uma das grandes matrizes de telefones Android, o MSAL implementa uma heurística de seleção de navegador que tenta fornecer o melhor SSO entre dispositivos.

O MSAL recupera principalmente o navegador padrão do Gerenciador de pacotes e verifica se ele está em uma lista testada de navegadores seguros. Caso contrário, o MSAL voltará a usar o WebView em vez de iniciar outro navegador não padrão na lista de confiança. O navegador padrão será escolhido independentemente de ele oferecer suporte a guias personalizadas. Se o navegador oferecer suporte a guias personalizadas, o MSAL iniciará a guia personalizada. as guias personalizadas terão uma aparência mais próxima de um aplicativo `WebView` e permitirão a personalização básica da interface do usuário. Consulte [guias personalizadas no Android](https://developer.chrome.com/multidevice/android/customtabs) para saber mais.

Se não houver nenhum pacote de navegador no dispositivo, o MSAL usará o no aplicativo `WebView` . Se a configuração padrão do dispositivo não for alterada, o mesmo navegador deverá ser iniciado para cada conexão para garantir uma experiência de SSO.

#### <a name="tested-browsers"></a>Navegadores testados

Os seguintes navegadores foram testados para ver se redirecionados corretamente para o `"redirect_uri"` especificado no arquivo de configuração:

| Dispositivo | Navegador interno | Chrome | Opera  | Microsoft Edge | Navegador de UC | Firefox |
| -- |:-------------:| -----:|-----:|-----:|-----:|-----:|
| Nexus 4 (API 17) | passagem | passagem |não aplicável |não aplicável |não aplicável |não aplicável |
| Samsung S7 (API 25) | Pass<sup>1</sup> | passagem | passagem | passagem | falha |passagem |
| Huawei (API 26) |Pass<sup>2</sup> | passagem | falha | passagem | passagem |passagem |
| Vivo (API 26) |passagem|passagem|passagem|passagem|passagem|falha|
| Pixel 2 (API 26) |passagem | passagem | passagem | passagem | falha |passagem |
| Oppo | passagem | Não aplicável<sup>3</sup>|não aplicável  |não aplicável |não aplicável | não aplicável|
| OnePlus (API 25) |passagem | passagem | passagem | passagem | falha |passagem |
| Nexus (API 28) |passagem | passagem | passagem | passagem | falha |passagem |
|MI | passagem | passagem | passagem | passagem | falha |passagem |

<sup>1</sup> O navegador interno da Samsung é a Samsung Internet.<br/>
<sup>2</sup> O navegador interno do Huawei é o navegador Huawei.<br/>
<sup>3</sup> O navegador padrão não pode ser alterado dentro da configuração do dispositivo Oppo.

## <a name="next-steps"></a>Próximas etapas

O [modo de dispositivo compartilhado para dispositivos Android](msal-android-shared-devices.md) permite que você configure um dispositivo Android para que ele possa ser facilmente compartilhado por vários funcionários.