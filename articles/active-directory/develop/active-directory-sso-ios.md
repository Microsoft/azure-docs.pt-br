---
title: Como habilitar o SSO entre aplicativos no iOS usando o ADAL | Microsoft Docs
description: "Como usar os recursos do SDK do ADAL para habilitar o Logon Único em seus aplicativos. "
services: active-directory
documentationcenter: 
author: brandwe
manager: mbaldwin
editor: 
ms.assetid: d042d6da-7503-4e20-bb55-06917de01fcd
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 04/07/2017
ms.author: brandwe
ms.custom: aaddev
ms.openlocfilehash: 73b8ed7e6a153a0790f7eae9bd51bb2e554ae72e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-enable-cross-app-sso-on-ios-using-adal"></a>Como habilitar o SSO entre aplicativos no iOS usando a ADAL
Atualmente, os clientes esperam o recurso de Logon Único (SSO), para que os usuários precisem inserir suas credenciais apenas uma vez, e essas credenciais sejam aplicadas entre os aplicativos. A dificuldade em digitar o nome de usuário e senha em uma tela pequena, muitas vezes combinada com um fator adicional (2FA) como uma chamada telefônica ou um código enviado via texto, se transforma rapidamente em insatisfação se um usuário tiver que fazer isso mais de uma vez para o seu produto.

Além disso, se você aplicar uma plataforma de identidade usada por outros aplicativos, como Contas da Microsoft ou uma conta corporativa do Office365, os clientes esperam que essas credenciais estejam disponíveis em todos os aplicativos, independentemente do fornecedor.

A plataforma Microsoft Identity, juntamente com nossos SDKs do Microsoft Identity, faz todo esse trabalho difícil para você e permite que você agrade aos seus clientes com o SSO dentro de seu próprio pacote de aplicativos ou, como ocorre com o nossa funcionalidade de agente e aplicativos autenticadores, em todo o dispositivo.

Este passo a passo informa como configurar nosso SDK dentro de seu aplicativo a fim de fornecer esse benefício aos clientes.

Este passo a passo se aplica a:

* Azure Active Directory
* Active Directory B2C do Azure
* Azure Active Directory B2B
* Acesso condicional ao Azure Active Directory

O documento anterior pressupõe que você sabe como [provisionar aplicativos no portal herdado do Azure Active Directory](active-directory-how-to-integrate.md) e que integrou seu aplicativo ao [SDK do Microsoft Identity para iOS](https://github.com/AzureAD/azure-activedirectory-library-for-objc).

## <a name="sso-concepts-in-the-microsoft-identity-platform"></a>Conceitos de SSO na Plataforma do Microsoft Identity
### <a name="microsoft-identity-brokers"></a>Agentes do Microsoft Identity
A Microsoft fornece aplicativos para todas as plataformas móveis que permitem o uso das mesmas credenciais em aplicativos de diferentes fornecedores e recursos aprimorados especiais que exigem um único local seguro do qual validar as credenciais. Chamamos esses recursos de **agentes**. No iOS e no Android esses agentes são fornecidos por meio de aplicativos baixáveis que os clientes instalam de forma independente ou que podem ser enviados por push ao dispositivo por uma empresa que gerencia uma parte ou todos os dispositivos para seus funcionários. Esses agentes oferecem suporte ao gerenciamento de segurança apenas para alguns aplicativos, ou para todo o dispositivo, com base no desejo dos administradores de TI. No Windows, essa funcionalidade é fornecida por um seletor de conta integrado ao sistema operacional, conhecido tecnicamente como o Agente de Autenticação da Web.

Para saber mais como usamos esses agentes e como os clientes podem vê-los em seu fluxo de logon para a plataforma do Microsoft Identity, continue lendo.

### <a name="patterns-for-logging-in-on-mobile-devices"></a>Padrões de logon em dispositivos móveis
O acesso às credenciais em dispositivos segue dois padrões básicos para a plataforma do Microsoft Identity:

* Logons assistidos por não agentes
* Logons assistidos por agentes

#### <a name="non-broker-assisted-logins"></a>Logons assistidos por não agentes
Os logons assistidos por não agentes são experiências de logon que ocorrem em linha com o aplicativo e usam o armazenamento local no dispositivo para esse aplicativo. Esse armazenamento pode ser compartilhado entre aplicativos, mas as credenciais ficam associadas estritamente ao aplicativo ou pacote de aplicativos que usam essa credencial. Provavelmente você já experimentou isso em muitos aplicativos móveis ao inserir um nome de usuário e senha dentro do próprio aplicativo.

Esses logons têm os seguintes benefícios:

* A experiência de usuário existe totalmente dentro do aplicativo.
* As credenciais podem ser compartilhadas entre aplicativos assinados com o mesmo certificado, fornecendo uma experiência de logon único para o pacote de aplicativos.
* O controle sobre a experiência de logon é fornecido para o aplicativo antes e depois da entrada.

Esses logons têm as seguintes desvantagens:

* O usuário não consegue experimentar o logon único em todos os aplicativos que usam um Microsoft Identity, somente entre os Microsoft Identities configurados por seu aplicativo.
* Seu aplicativo não pode ser usado com recursos de negócios mais avançados, como o Acesso Condicional ou no pacote de produtos do InTune.
* Seu aplicativo não dá suporte à autenticação baseada em certificado para usuários corporativos.

Veja uma representação de como os SDKs do Microsoft Identity funcionam com o armazenamento compartilhado de seus aplicativos a fim de habilitar o SSO:

```
+------------+ +------------+  +-------------+
|            | |            |  |             |
|   App 1    | |   App 2    |  |   App 3     |
|            | |            |  |             |
|            | |            |  |             |
+------------+ +------------+  +-------------+
| ADAL SDK  |  |  ADAL SDK  |  |  ADAK SDK   |
+------------+-+------------+--+-------------+
|                                            |
|            App Shared Storage              |
+--------------------------------------------+
```

#### <a name="broker-assisted-logins"></a>Logons assistidos por agentes
Os logons assistido por agente são experiências de logon que ocorrem dentro do aplicativo do agente e usam o armazenamento e a segurança do agente para compartilhar as credenciais em todos os aplicativos no dispositivo, e aplicam a plataforma Microsoft Identity. Isso significa que os aplicativos dependem do agente para realizar a entrada dos usuários. No iOS e no Android esses agentes são fornecidos por meio de aplicativos baixáveis que os clientes instalam de forma independente ou que podem ser enviados por push ao dispositivo por uma empresa que gerencia o dispositivo para o usuário. Um exemplo desse tipo de aplicativo é o Microsoft Authenticator no iOS. No Windows, essa funcionalidade é fornecida por um seletor de conta integrado ao sistema operacional, conhecido tecnicamente como o Agente de Autenticação da Web.
A experiência varia de acordo com a plataforma e, às vezes, pode ser perturbador para os usuários se não for gerenciada corretamente. Provavelmente você estará mais familiarizado com esse padrão se tiver instalado o aplicativo do Facebook e usado o Facebook Connect em outro aplicativo. A plataforma Microsoft Identity usa o mesmo padrão.

Para iOS, isso leva a uma animação de "transição", em que seu aplicativo é enviado ao segundo plano enquanto os aplicativos do Microsoft Authenticator ficam em primeiro plano para o usuário selecionar em qual conta quer entrar.  

Para Android e Windows, o seletor de conta é exibido na parte superior de seu aplicativo, o que é menos perturbador para o usuário.

#### <a name="how-the-broker-gets-invoked"></a>Como o agente é invocado
Se um agente compatível for instalado no dispositivo, como o aplicativo Microsoft Authenticator, os SDKs do Microsoft Identity farão automaticamente o trabalho de invocar o agente para você quando um usuário indicar que deseja fazer logon usando qualquer conta da plataforma Microsoft Identity. Essa conta pode ser uma Conta da Microsoft pessoal, uma conta corporativa ou de estudante ou uma conta que você fornece e hospeda no Azure usando nossos produtos B2C e B2B. 

 #### <a name="how-we-ensure-the-application-is-valid"></a>Como podemos garantir que o aplicativo é válido
 
 A necessidade de garantir a identidade de uma chamada de aplicativo para o agente é fundamental para a segurança fornecida em logons assistidos por agente. O iOS e o Android não impõem identificadores exclusivos que são válidos somente para um determinado aplicativo, portanto, aplicativos mal-intencionados podem "falsificar" o identificador de um aplicativo legítimo e receber os tokens destinados ao aplicativo legítimo. Para garantir que estejamos sempre nos comunicando com o aplicativo certo no tempo de execução, pedimos ao desenvolvedor que forneça um redirectURI personalizado ao registrar seu aplicativo com a Microsoft. **O modo como os desenvolvedores devem criar esse URI de redirecionamento será abordado com detalhes logo abaixo.** Este redirectURI personalizado contém a ID de Pacote do aplicativo e tem a garantia da Apple App Store de ser exclusivo para o aplicativo. Quando um aplicativo chama o agente, o agente solicita que o sistema operacional iOS o forneça com a ID de Pacote que chamou o agente. O agente fornece a ID de Pacote para a Microsoft na chamada para nosso sistema de identidade. Se a ID de Pacote do aplicativo não corresponder com a ID de Pacote fornecida para nós pelo desenvolvedor durante o registro, negaremos o acesso aos tokens do recurso que o aplicativo está solicitando. Essa verificação garante que apenas o aplicativo registrado pelo desenvolvedor receba tokens.

**O desenvolvedor pode optar se o SDK do Microsoft Identity chama o agente ou usa o fluxo assistido por não agente.** No entanto, se o desenvolvedor optar por não usar o fluxo assistido por agente perderá a vantagem de usar as credenciais de SSO que o usuário já pode ter adicionado ao dispositivo e impede que o aplicativo seja usado com recursos corporativos fornecidos pela Microsoft aos seus clientes, como o Acesso Condicional, recursos de gerenciamento do Intune e autenticação baseada em certificado.

Esses logons têm os seguintes benefícios:

* O usuário desfruta do SSO em todos os aplicativos, independentemente do fornecedor.
* Seu aplicativo pode usar recursos corporativos mais avançados, como o Acesso Condicional ou usar o pacote de produtos do InTune.
* Seu aplicativo pode oferecer suporte à autenticação baseada em certificado para usuários corporativos.
* Uma experiência de entrada muito mais segura, pois a identidade do aplicativo e do usuário são verificadas pelo aplicativo agente com criptografia e algoritmos de segurança adicionais.

Esses logons têm as seguintes desvantagens:

* No iOS, o usuário sai da experiência de seu aplicativo enquanto as credenciais são escolhidas.
* Perda da capacidade de gerenciar a experiência de logon de seus clientes dentro de seu aplicativo.

Veja uma representação de como os SDKs do Microsoft Identity funcionam com os aplicativos agentes a fim de habilitar o SSO:

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

Com essas informações em mãos, você deve ser capaz de entender melhor e implementar o SSO em seu aplicativo usando a plataforma do Microsoft Identity e os SDKs.

## <a name="enabling-cross-app-sso-using-adal"></a>Habilitar SSO entre aplicativos usando a ADAL
Aqui, usaremos o SDK do iOS para ADAL a fim de:

* Ativar o SSO assistido por não agente para seu pacote de aplicativos
* Ativar o suporte para SSO assistido por agente

### <a name="turning-on-sso-for-non-broker-assisted-sso"></a>Ativar o SSO assistido por não agente
Para SSO assistido por não agente entre aplicativos, os SDKs do Microsoft Identity gerenciam grande parte da complexidade do SSO para você. Isso inclui localizar o usuário correto no cache e manter uma lista de usuários conectados para consulta.

Para habilitar o SSO entre aplicativos que você possui, é necessário fazer o seguinte:

1. Certifique-se de que todos os seus aplicativos usem a mesma ID de Cliente ou de Aplicativo.
2. Certifique-se de que todos os aplicativos compartilhem o mesmo certificado de assinatura da Apple para que você possa compartilhar os conjuntos de chaves.
3. Solicite o mesmo direito de conjunto de chaves para cada um dos seus aplicativos.
4. Informe aos SDKs do Microsoft Identity sobre o conjunto de chaves compartilhado que você deseja usar.

#### <a name="using-the-same-client-id--application-id-for-all-the-applications-in-your-suite-of-apps"></a>Usando a mesma ID de Cliente/ID de Aplicativo para todos os aplicativos em seu pacote de aplicativos
Para que a plataforma do Microsoft Identity saiba que tem permissão para compartilhar tokens entre seus aplicativos, cada um dos aplicativos precisará compartilhar a mesma ID de Cliente ou de Aplicativo. Esse é o identificador exclusivo fornecido para você quando você registrou seu primeiro aplicativo no portal.

Você deve estar imaginando como poderá identificar aplicativos diferentes para o serviço do Microsoft Identity se ele usa a mesma ID de Aplicativo. A resposta está nos **URIs de Redirecionamento**. Cada aplicativo pode ter vários URIs de Redirecionamento registrados no portal de integração. Cada aplicativo em seu pacote terá um URI de redirecionamento diferente. Veja abaixo um exemplo de como isso acontece.

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


*Observe que o formato desses URIs de Redirecionamento é explicados abaixo. Você pode usar qualquer URI de Redirecionamento, a menos que você queira oferecer suporte ao agente e, nesse caso, ele deverá ser parecido com o mostrado acima*

#### <a name="create-keychain-sharing-between-applications"></a>Criar o compartilhamento do conjunto de chaves entre aplicativos
A habilitação do compartilhamento de conjunto de chaves está além do escopo deste documento e é abordada pela Apple em seu documento [Adicionando recursos](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html). O importante é que você decida qual o nome de seu conjunto de chaves e adicionar esse recurso em todos os seus aplicativos.

Quando os direitos estiverem configurados corretamente, você deverá ver um arquivo em diretório de projeto chamado `entitlements.plist` , contendo algo parecido com o seguinte:

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
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

Quando o direito do conjunto de chaves estiver habilitado em cada um de seus aplicativos e você estiver pronto para usar o SSO, informe ao SDK do Microsoft Identity sobre o conjunto de chaves usando a seguinte configuração em seu `ADAuthenticationSettings` com a seguinte configuração:

```
defaultKeychainSharingGroup=@"com.myapp.mycache";
```

> [!WARNING]
> Quando você compartilha um conjunto de chaves em seus aplicativos, qualquer aplicativo pode excluir usuários, ou pior, excluir todos os tokens em seu aplicativo. Isso é particularmente desastroso se você tiver aplicativos que dependem dos tokens para o trabalho em segundo plano. O compartilhamento do conjunto de chaves significa que você deve ter muito cuidado com toda e qualquer operação de remoção por meio dos SDKs do Microsoft Identity.
> 
> 

É isso! Agora, o SDK do Microsoft Identity compartilhará as credenciais em todos os seus aplicativos. A lista de usuários também será compartilhada entre instâncias do aplicativo.

### <a name="turning-on-sso-for-broker-assisted-sso"></a>Ativar o SSO assistido por agente
A capacidade de um aplicativo usar qualquer agente instalado no dispositivo está **desativada por padrão**. Para usar seu aplicativo com o agente, você deve fazer algumas configurações adicionais e adicionar algum código ao seu aplicativo.

Execute estas etapas:

1. Habilitar o modo de agente na chamada do código do aplicativo para o SDK do MS.
2. Estabelecer um novo URI de redirecionamento e fornecê-lo ao aplicativo e no registro do aplicativo.
3. Registrando um esquema de URL.
4. Suporte a iOS9: adicione uma permissão para o arquivo info plist.

#### <a name="step-1-enable-broker-mode-in-your-application"></a>Etapa 1: habilitar o modo de agente em seu aplicativo
A capacidade de seu aplicativo de usar o agente é ativada quando você cria o “contexto” ou configuração inicial de seu objeto de autenticação. Faça isso configurando o tipo de credenciais em seu código:

```
/*! See the ADCredentialsType enumeration definition for details */
@propertyADCredentialsType credentialsType;
```
A configuração `AD_CREDENTIALS_AUTO` permitirá que o SDK do Microsoft Identity tente chamar o agente, `AD_CREDENTIALS_EMBEDDED` impedirá que o SDK do Microsoft Identity chame o agente.

#### <a name="step-2-registering-a-url-scheme"></a>Etapa 2: Registrando um esquema de URL
A plataforma Microsoft Identity usa URLs para invocar o agente e, em seguida, devolver o controle ao seu aplicativo. Para concluir esse percurso de ida e volta você precisa de um esquema de URL registrado para o aplicativo que seja de conhecimento da plataforma do Microsoft Identity. Isso pode acontecer junto com outros esquemas de aplicativo que você pode ter registrado anteriormente com seu aplicativo.

> [!WARNING]
> Recomendamos que o esquema de URL seja exclusivo o bastante para minimizar a possibilidade de outro aplicativo usar o mesmo esquema de URL. A Apple não impõe a exclusividade dos esquemas de URL registrados na app store.
> 
> 

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

Esse URI de Redirecionamento deve ser especificado no registro de seu aplicativo usando o [Portal do Azure](https://portal.azure.com/). Para saber mais sobre o registro de aplicativo do Azure AD, confira [Integração com o Azure Active Directory](active-directory-how-to-integrate.md).

##### <a name="step-3a-add-a-redirect-uri-in-your-app-and-dev-portal-to-support-certificate-based-authentication"></a>Etapa 3a: adicionar um URI de redirecionamento ao portal do aplicativo e de desenvolvimento a fim de oferecer suporte à autenticação baseada em certificado
Para dar suporte à autenticação baseada em certificado, é necessário registrar um segundo "msauth" em seu aplicativo e no [Portal do Azure](https://portal.azure.com/) a fim de lidar com a autenticação de certificados caso você queira adicionar esse suporte ao seu aplicativo.

`msauth://code/<broker-redirect-uri-in-url-encoded-form>`

ex: *msauth://code/x-msauth-mytestiosapp%3A%2F%2Fcom.myapp.mytestapp*

#### <a name="step-4-ios9-add-a-configuration-parameter-to-your-app"></a>Etapa 4: iOS9: adicionar um parâmetro de configuração ao seu aplicativo
O ADAL usa – canOpenURL: para verificar se o agente está instalado no dispositivo. No iOS 9, a Apple bloqueou quais esquemas um aplicativo pode consultar. Você precisará adicionar "msauth" à seção LSApplicationQueriesSchemes de seu `info.plist file`.

<key>LSApplicationQueriesSchemes</key>

<array> <string>msauth</string>
</array>

### <a name="youve-configured-sso"></a>Você configurou o SSO!
Agora, o SDK do Microsoft Identity compartilhará automaticamente as credenciais em seus aplicativos e invocará o agente se ele estiver presente em seu dispositivo.

