---
title: Como habilitar o SSO entre aplicativos no iOS usando o ADAL | Microsoft Docs
description: Como usar os recursos do SDK do ADAL para habilitar o Logon Único em seus aplicativos.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: how-to
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: brandwe
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 08b018082c753b9524cb12a72d637fe5458d9114
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "85383692"
---
# <a name="how-to-enable-cross-app-sso-on-ios-using-adal"></a>Como habilitar o SSO entre aplicativos no iOS usando a ADAL

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

SSO (logon único) permite que os usuários só digitem suas credenciais uma vez, e essas credenciais funcionarão automaticamente entre aplicativos e entre plataformas que outros aplicativos possam usar (como Contas Microsoft ou uma conta corporativa do Microsoft 365), não importa o editor.

A plataforma de identidade da Microsoft, juntamente com os SDKs, torna fácil habilitar SSO em seu próprio pacote de aplicativos, ou com a funcionalidade de agente e aplicativos Autenticadores, em todo o dispositivo.

Neste passo a passo, você aprenderá a configurar o SDK em seu aplicativo para fornecer SSO aos seus clientes.

Estas instruções se aplicam a:

* Azure Active Directory (Azure Active Directory)
* Azure Active Directory B2C
* Azure Active Directory B2B
* Acesso condicional ao Azure Active Directory

## <a name="prerequisites"></a>Pré-requisitos

Estas instruções pressupõem que você sabe como:

* Provisionar seu aplicativo usando o portal herdado para o Azure AD. Para saber mais, consulte [Registrar um aplicativo](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)
* Integrar seu aplicativo com o [SDK do Azure AD para iOS](https://github.com/AzureAD/azure-activedirectory-library-for-objc).

## <a name="single-sign-on-concepts"></a>Conceitos de logon único

### <a name="identity-brokers"></a>Agentes de identidade

A Microsoft fornece aplicativos para todas as plataformas móveis que permitem o uso das mesmas credenciais em aplicativos de diferentes fornecedores e para recursos aprimorados que exigem um único local seguro do qual validar as credenciais. Eles são chamados de **agentes**.

No iOS e no Android, agentes são fornecidos por meio de aplicativos disponíveis para download que os clientes instalam de modo independente ou são enviados efetuando push par o dispositivo por uma empresa que gerencia alguns ou todos os dispositivos para os funcionários. Agentes oferecem suporte ao gerenciamento de segurança apenas para alguns aplicativos ou todo o dispositivo com base na configuração do administrador de TI. No Windows, essa funcionalidade é fornecida por um seletor de conta integrado ao sistema operacional, conhecido tecnicamente como o Agente de Autenticação da Web.

### <a name="patterns-for-logging-in-on-mobile-devices"></a>Padrões de logon em dispositivos móveis

Acesso às credenciais em dispositivos segue dois padrões básicos:

* Logons assistidos por não agentes
* Logons assistidos por agentes

#### <a name="non-broker-assisted-logins"></a>Logons assistidos por não agentes

Os logons assistidos por não agentes são experiências de logon que ocorrem em linha com o aplicativo e usam o armazenamento local no dispositivo para esse aplicativo. Esse armazenamento pode ser compartilhado entre aplicativos, mas as credenciais ficam associadas estritamente ao aplicativo ou pacote de aplicativos que usam essa credencial. Provavelmente você já experimentou isso em muitos aplicativos móveis ao inserir um nome de usuário e senha dentro do próprio aplicativo.

Esses logons têm os seguintes benefícios:

* A experiência de usuário existe totalmente dentro do aplicativo.
* As credenciais podem ser compartilhadas entre aplicativos assinados com o mesmo certificado, fornecendo uma experiência de logon único para o pacote de aplicativos.
* O controle sobre a experiência de logon é fornecido para o aplicativo antes e depois da entrada.

Esses logons têm as seguintes desvantagens:

* Os usuários não conseguem realizar logon único em todos os aplicativos que usam uma identidade da Microsoft, somente nas identidades da Microsoft que seu aplicativo configurou.
* Seu aplicativo não pode ser usado com recursos de negócios mais avançados, como o Acesso Condicional ou no pacote de produtos do Intune.
* Seu aplicativo não dá suporte à autenticação baseada em certificado para usuários corporativos.

Veja uma representação de como os SDKs funcionam com o armazenamento compartilhado de seus aplicativos a fim de habilitar o SSO:

```
+------------+ +------------+  +-------------+
|            | |            |  |             |
|   App 1    | |   App 2    |  |   App 3     |
|            | |            |  |             |
|            | |            |  |             |
+------------+ +------------+  +-------------+
| ADAL SDK  |  |  ADAL SDK  |  |  ADAL SDK   |
+------------+-+------------+--+-------------+
|                                            |
|            App Shared Storage              |
+--------------------------------------------+
```

#### <a name="broker-assisted-logins"></a>Logons assistidos por agentes

Os logons assistido por agente são experiências de logon que ocorrem dentro do aplicativo do agente e usam o armazenamento e a segurança do agente para compartilhar as credenciais em todos os aplicativos no dispositivo, e aplicam a plataforma de identidade. Isso significa que os aplicativos dependem do agente para realizar a entrada dos usuários. No iOS e no Android, esses agentes são fornecidos por meio de aplicativos baixáveis que os clientes instalam de forma independente ou enviam por push ao dispositivo por uma empresa que gerencia o dispositivo para o usuário. Um exemplo desse tipo de aplicativo é o Microsoft Authenticator no iOS. No Windows, essa funcionalidade é fornecida por um seletor de conta integrado ao sistema operacional, conhecido tecnicamente como o Agente de Autenticação da Web.

A experiência varia de acordo com a plataforma e, às vezes, pode ser perturbador para os usuários se não for gerenciada corretamente. Provavelmente você estará mais familiarizado com esse padrão se tiver instalado o aplicativo do Facebook e usado o Facebook Connect em outro aplicativo. A plataforma de identidade usa o mesmo padrão.

Para iOS, isso leva a uma animação de "transição", em que seu aplicativo é enviado ao segundo plano enquanto os aplicativos do Microsoft Authenticator ficam em primeiro plano para o usuário selecionar em qual conta quer entrar.

Para Android e Windows, o seletor de conta é exibido na parte superior de seu aplicativo, o que é menos perturbador para o usuário.

#### <a name="how-the-broker-gets-invoked"></a>Como o agente é invocado

Se um agente compatível for instalado no dispositivo, como o aplicativo Microsoft Authenticator, os SDKs farão automaticamente o trabalho de invocar o agente para você quando um usuário indicar que deseja fazer logon usando qualquer conta da plataforma de identidade. Essa conta pode ser uma Conta da Microsoft pessoal, uma conta corporativa ou de estudante ou uma conta que você fornece e hospeda no Azure usando nossos produtos B2C e B2B.

#### <a name="how-we-ensure-the-application-is-valid"></a>Como podemos garantir que o aplicativo é válido

A necessidade de garantir a identidade de um aplicativo para ligar para o agente é fundamental para a segurança fornecida em logons assistidos por agente. O iOS e o Android não impõem identificadores exclusivos que são válidos somente para um determinado aplicativo, portanto, aplicativos mal-intencionados podem "falsificar" o identificador de um aplicativo legítimo e receber os tokens destinados ao aplicativo legítimo. Para garantir que estejamos sempre nos comunicando com o aplicativo certo no runtime, pedimos ao desenvolvedor que forneça um redirectURI personalizado ao registrar seu aplicativo com a Microsoft. O modo como os desenvolvedores devem criar esse URI de redirecionamento é abordado com detalhes logo abaixo. Este redirectURI personalizado contém a ID de Pacote do aplicativo e tem a garantia da Apple App Store de ser exclusivo para o aplicativo. Quando um aplicativo chama o agente, o agente solicita que o sistema operacional iOS o forneça com a ID de Pacote que chamou o agente. O agente fornece a ID de Pacote para a Microsoft na chamada para nosso sistema de identidade. Se a ID de Pacote do aplicativo não corresponder com a ID de Pacote fornecida para nós pelo desenvolvedor durante o registro, negaremos o acesso aos tokens do recurso que o aplicativo está solicitando. Essa verificação garante que apenas o aplicativo registrado pelo desenvolvedor receba tokens.

**O desenvolvedor tem a opção de definir se o SDK chama o agente ou usa o fluxo não assistido por agente.** No entanto, se o desenvolvedor optar por não usar o fluxo assistido por agente, perderá o benefício de usar as credenciais de SSO que o usuário já pode ter adicionado ao dispositivo e isso impedirá que o aplicativo seja usado com recursos empresariais fornecidos pela Microsoft aos seus clientes, como o Acesso Condicional, funcionalidades de gerenciamento do Intune e autenticação baseada em certificado.

Esses logons têm os seguintes benefícios:

* O usuário desfruta do SSO em todos os aplicativos, independentemente do fornecedor.
* Seu aplicativo pode usar recursos corporativos mais avançados, como o Acesso Condicional ou usar o pacote de produtos do Intune.
* Seu aplicativo pode oferecer suporte à autenticação baseada em certificado para usuários corporativos.
* Uma experiência de entrada muito mais segura, pois a identidade do aplicativo e do usuário são verificadas pelo aplicativo agente com criptografia e algoritmos de segurança adicionais.

Esses logons têm as seguintes desvantagens:

* No iOS, o usuário sai da experiência de seu aplicativo enquanto as credenciais são escolhidas.
* Perda da capacidade de gerenciar a experiência de logon de seus clientes dentro de seu aplicativo.

Veja uma representação de como os SDKs funcionam com os aplicativos agentes a fim de habilitar o SSO:

```
+------------+ +------------+   +-------------+
|            | |            |   |             |
|   App 1    | |   App 2    |   |   Someone   |
|            | |            |   |    Else's   |
|            | |            |   |     App     |
+------------+ +------------+   +-------------+
| Azure SDK  | | Azure SDK  |   | Azure SDK   |
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

## <a name="enabling-cross-app-sso-using-adal"></a>Habilitar SSO entre aplicativos usando a ADAL

Aqui, usaremos o SDK do iOS para ADAL a fim de:

* Ativar o SSO assistido por não agente para seu pacote de aplicativos
* Ativar o suporte para SSO assistido por agente

### <a name="turning-on-sso-for-non-broker-assisted-sso"></a>Ativar o SSO assistido por não agente

Para SSO não assistido por agente entre aplicativos, os SDKs gerenciam grande parte da complexidade do SSO para você. Isso inclui localizar o usuário correto no cache e manter uma lista de usuários conectados para consulta.

Para habilitar o SSO entre aplicativos que você possui, é necessário fazer o seguinte:

1. Verifique se todos os seus aplicativos usam a mesma ID de Cliente ou de Aplicativo.
2. Certifique-se de que todos os aplicativos compartilhem o mesmo certificado de assinatura da Apple para que você possa compartilhar os conjuntos de chaves.
3. Solicite o mesmo direito de conjunto de chaves para cada um dos seus aplicativos.
4. Conte aos SDKs sobre o conjunto de chaves compartilhado que você deseja usar.

#### <a name="using-the-same-client-id--application-id-for-all-the-applications-in-your-suite-of-apps"></a>Usando a mesma ID de Cliente/ID de Aplicativo para todos os aplicativos em seu pacote de aplicativos

Para que a plataforma de identidade saiba que tem permissão para compartilhar tokens entre seus aplicativos, cada um dos aplicativos precisará compartilhar a mesma ID de Cliente ou de Aplicativo. Esse é o identificador exclusivo fornecido para você quando você registrou seu primeiro aplicativo no portal.

URIs de redirecionamento permitem identificar aplicativos diferentes para o serviço de identidade da Microsoft se ele usa a mesma ID de Aplicativo. Cada aplicativo pode ter vários URIs de Redirecionamento registrados no portal de integração. Cada aplicativo em seu pacote terá um URI de redirecionamento diferente. Veja abaixo um exemplo de como isso acontece.

URI de Redirecionamento do App1: `x-msauth-mytestiosapp://com.myapp.mytestapp`

URI de Redirecionamento do App2: `x-msauth-mytestiosapp://com.myapp.mytestapp2`

URI de Redirecionamento do App3: `x-msauth-mytestiosapp://com.myapp.mytestapp3`

....

Eles estão aninhados sob a mesma ID de cliente/ID de aplicativo e são pesquisados com base no URI de redirecionamento que você retorna para nós na configuração de seu SDK.

```
+-------------------+
|                   |
|  Client ID        |
+---------+---------+
          |
          |           +-----------------------------------+
          |           |  App 1 Redirect URI               |
          +----------^+                                   |
          |           +-----------------------------------+
          |
          |           +-----------------------------------+
          +----------^+  App 2 Redirect URI               |
          |           |                                   |
          |           +-----------------------------------+
          |
          +----------^+-----------------------------------+
                      |  App 3 Redirect URI               |
                      |                                   |
                      +-----------------------------------+

```

O formato desses URIs de redirecionamento é explicado abaixo. Você pode usar qualquer URI de Redirecionamento, a menos que você queira oferecer suporte ao agente e, nesse caso, ele deverá ser parecido com o mostrado acima*

#### <a name="create-keychain-sharing-between-applications"></a>Criar o compartilhamento do conjunto de chaves entre aplicativos

A habilitação do compartilhamento de conjunto de chaves está além do escopo deste documento e é abordada pela Apple em seu documento [Adicionando recursos](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html). O importante é que você decida qual o nome de seu conjunto de chaves e adicionar esse recurso em todos os seus aplicativos.

Quando os direitos estiverem configurados corretamente, você deverá ver um arquivo em diretório de projeto chamado `entitlements.plist` , contendo algo parecido com o seguinte:

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "https://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>keychain-access-groups</key>
    <array>
        <string>$(AppIdentifierPrefix)com.myapp.mytestapp</string>
        <string>$(AppIdentifierPrefix)com.myapp.mycache</string>
    </array>
</dict>
</plist>
```

Quando o direito do conjunto de chaves estiver habilitado em cada um de seus aplicativos e você estiver pronto para usar o SSO, informe ao SDK de identidade sobre o conjunto de chaves usando a seguinte configuração em seu `ADAuthenticationSettings` com a seguinte configuração:

```
defaultKeychainSharingGroup=@"com.myapp.mycache";
```

> [!WARNING]
> Quando você compartilha um conjunto de chaves em seus aplicativos, qualquer aplicativo pode excluir usuários, ou pior, excluir todos os tokens em seu aplicativo. Isso é particularmente desastroso se você tiver aplicativos que dependem dos tokens para o trabalho em segundo plano. O compartilhamento do conjunto de chaves significa que você deve ter muito cuidado com toda e qualquer operação de remoção por meio dos SDKs de identidade.

É isso! O SDK agora compartilhará as credenciais om todos os seus aplicativos. A lista de usuários também será compartilhada entre instâncias do aplicativo.

### <a name="turning-on-sso-for-broker-assisted-sso"></a>Ativar o SSO assistido por agente

A capacidade de um aplicativo usar qualquer agente instalado no dispositivo está **desativada por padrão**. Para usar seu aplicativo com o agente, você deve fazer algumas configurações adicionais e adicionar algum código ao seu aplicativo.

Execute estas etapas:

1. Habilitar o modo de agente na chamada do código do aplicativo para o SDK do MS.
2. Estabelecer um novo URI de redirecionamento e fornecê-lo ao aplicativo e no registro do aplicativo.
3. Registrando um esquema de URL.
4. Adicione uma permissão ao arquivo info.plist.

#### <a name="step-1-enable-broker-mode-in-your-application"></a>Etapa 1: habilitar o modo de agente em seu aplicativo

A capacidade de seu aplicativo de usar o agente é ativada quando você cria o “contexto” ou configuração inicial de seu objeto de autenticação. Faça isso configurando o tipo de credenciais em seu código:

```
/*! See the ADCredentialsType enumeration definition for details */
@propertyADCredentialsType credentialsType;
```
A configuração `AD_CREDENTIALS_AUTO` permitirá que o SDK tente chamar o agente, `AD_CREDENTIALS_EMBEDDED` impedirá que o SDK chame o agente.

#### <a name="step-2-registering-a-url-scheme"></a>Etapa 2: Registrando um esquema de URL

A plataforma de identidade usa URLs para invocar o agente e, em seguida, devolver o controle ao seu aplicativo. Para concluir esse percurso de ida e volta você precisa de um esquema de URL registrado para o aplicativo que seja de conhecimento da plataforma de identidade. Isso pode acontecer junto com outros esquemas de aplicativo que você pode ter registrado anteriormente com seu aplicativo.

> [!WARNING]
> Recomendamos que o esquema de URL seja exclusivo o bastante para minimizar a possibilidade de outro aplicativo usar o mesmo esquema de URL. A Apple não impõe a exclusividade dos esquemas de URL registrados na app store.

Veja abaixo um exemplo de como isso aparece na configuração de seu projeto. Você pode também fazer isso no XCode, da seguinte maneira:

```
<key>CFBundleURLTypes</key>
<array>
    <dict>
        <key>CFBundleTypeRole</key>
        <string>Editor</string>
        <key>CFBundleURLName</key>
        <string>com.myapp.mytestapp</string>
        <key>CFBundleURLSchemes</key>
        <array>
            <string>x-msauth-mytestiosapp</string>
        </array>
    </dict>
</array>
```

#### <a name="step-3-establish-a-new-redirect-uri-with-your-url-scheme"></a>Etapa 3: estabelecer um novo URI de redirecionamento com seu esquema de URL

Para garantir que sempre retornarmos os tokens de credencial para o aplicativo correto, precisamos ter certeza de que chamamos seu aplicativo de forma que o sistema operacional iOS possa verificar. O sistema operacional iOS relata aos aplicativos de agente da Microsoft a ID do Pacote do aplicativo por meio de uma chamada. Isso não pode ser falsificado por um aplicativo mal-intencionado. Portanto, podemos aproveitar isso juntamente com o URI do nosso aplicativo de agente para garantir que os tokens sejam retornados para o aplicativo correto. Precisamos que você estabeleça esse URI de redirecionamento exclusivo em seu aplicativo e defina como um URI de Redirecionamento em nosso portal do desenvolvedor.

O URI de redirecionamento deve ser no formato correto de:

`<app-scheme>://<your.bundle.id>`

Por exemplo: *x-msauth-mytestiosapp://com.myapp.mytestapp*

Esse URI de redirecionamento deve ser especificado no registro de seu aplicativo usando o [portal do Azure](https://portal.azure.com/). Para saber mais sobre o registro de aplicativo do Azure AD, confira [Integração com o Azure Active Directory](../develop/active-directory-how-to-integrate.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

##### <a name="step-3a-add-a-redirect-uri-in-your-app-and-dev-portal-to-support-certificate-based-authentication"></a>Etapa 3a: adicionar um URI de redirecionamento ao portal do aplicativo e de desenvolvimento a fim de oferecer suporte à autenticação baseada em certificado

Para dar suporte à autenticação baseada em certificado, é necessário registrar um segundo "msauth" em seu aplicativo e no [portal do Microsoft Azure](https://portal.azure.com/) a fim de lidar com a autenticação de certificados caso você queira adicionar esse suporte ao seu aplicativo.

`msauth://code/<broker-redirect-uri-in-url-encoded-form>`

ex: *msauth://code/x-msauth-mytestiosapp%3A%2F%2Fcom.myapp.mytestapp*

#### <a name="step-4-add-a-configuration-parameter-to-your-app"></a>Etapa 4: adicione um parâmetro de configuração ao aplicativo

O ADAL usa – canOpenURL: para verificar se o agente está instalado no dispositivo. No iOS 9, a Apple bloqueou os esquemas que um aplicativo pode consultar. Você precisará adicionar "msauth" à seção LSApplicationQueriesSchemes de seu `info.plist file`.

```
    <key>LSApplicationQueriesSchemes</key>
    <array>
        <string>msauth</string>
    </array>

```

### <a name="youve-configured-sso"></a>Você configurou o SSO!

Agora, o SDK de identidade compartilhará automaticamente as credenciais em seus aplicativos e invocará o agente se ele estiver presente em seu dispositivo.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre o [protocolo de logon único SAML](../develop/single-sign-on-saml-protocol.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)
