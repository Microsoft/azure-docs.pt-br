---
title: "Introdução a Cordova no AD do Azure | Microsoft Docs"
description: Como compilar um aplicativo do Cordova que se integra ao Azure AD para entrada e que chama APIs protegidas do Azure AD usando OAuth.
services: active-directory
documentationcenter: 
author: vibronet
manager: mbaldwin
editor: 
ms.assetid: b1a8d7bd-7ad6-44d5-8ccb-5255bb623345
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 01/07/2017
ms.author: vittorib
ms.custom: aaddev
ms.openlocfilehash: d9f53148787729d29a0a89cce1b8b2b83ba228f8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="integrate-azure-ad-with-an-apache-cordova-app"></a>Integrar o AD do Azure com um aplicativo Apache Cordova
[!INCLUDE [active-directory-devquickstarts-switcher](../../../includes/active-directory-devquickstarts-switcher.md)]

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Você pode usar o Apache Cordova para desenvolver aplicativos HTML5/JavaScript que podem ser executados em dispositivos móveis, como aplicativos nativos completos. Com o Azure Active Directory (Azure AD), você pode adicionar recursos de autenticação de nível empresarial a seus aplicativos Cordova.

Um plug-in do Cordova encapsula os SDKs nativos do Azure AD em iOS, Android, Windows Store e Windows Phone. Usando esse plug-in, você pode aprimorar seu aplicativo para dar suporte à entrada com contas do Active Directory do Windows Server de seus usuários, obter acesso ao Office 365 e APIs do Azure e até mesmo ajudar a proteger chamadas para sua própria API da web personalizada.

Neste tutorial, usaremos o plug-in do Apache Cordova para a Biblioteca de Autenticação do Active Directory (ADAL) para aprimorar um aplicativo simples adicionando os seguintes recursos:

* Com apenas algumas linhas de código, autenticar um usuário e obter um token.
* Usar esse token para invocar a API do Graph para consultar o diretório e exibir os resultados.  
* Use o cache de token da ADAL para minimizar os prompts de autenticação para o usuário.

Para fazer essas melhorias, você precisa:

1. Registrar um aplicativo com o Azure AD.
2. Adicione código ao seu aplicativo para solicitar tokens.
3. Adicionar código para usar o token para consultar a Graph API e exibir os resultados.
4. Criar o projeto de implantação Cordova com todas as plataformas de destino, adicionar o plug-in Cordova ADAL e testar a solução em emuladores.

## <a name="prerequisites"></a>Pré-requisitos
Para concluir este tutorial, você precisará:

* Um locatário do Azure AD onde você pode ter uma conta com direitos de desenvolvimento de aplicativo.
* Um ambiente de desenvolvimento configurado para usar o Apache Cordova.  

Se você já tiver ambos configurados, vá diretamente para a etapa 1.

Se você não tiver um locatário do Azure AD, use as [instruções sobre como obter um](active-directory-howto-tenant.md).

Se você não tiver o Apache Cordova configurado no seu computador, instale o seguinte:

* [Git](http://git-scm.com/book/en/v2/Getting-Started-Installing-Git)
* [Node.js](https://nodejs.org/download/)
* [Cordova CLI](https://cordova.apache.org/) (pode ser facilmente instalado por meio do gerenciador de pacotes NPM: `npm install -g cordova`)

As instalações anteriores devem funcionar no PC e no Mac.

Cada plataforma de destino tem pré-requisitos diferentes:

* Para compilar e executar um aplicativo para Tablet/PC Windows ou Windows Phone:
  * Instale o [Visual Studio 2013 para Windows com Atualização 2 ou superior](http://www.visualstudio.com/downloads/download-visual-studio-vs#d-express-windows-8) (Express ou outra versão) ou [Visual Studio 2015](https://www.visualstudio.com/downloads/download-visual-studio-vs#d-community).

* Para compilar e executar um aplicativo para iOS:

  * Instale o Xcode 6.x ou posterior. Baixe-o no [site de desenvolvedores da Apple](http://developer.apple.com/downloads) ou na [Mac App Store](http://itunes.apple.com/us/app/xcode/id497799835?mt=12).
  * Instale o [ios-sim](https://www.npmjs.org/package/ios-sim). Você pode usá-lo para iniciar aplicativos iOS no simulador do iOS na linha de comando. (Você pode instalá-lo facilmente por meio do terminal: `npm install -g ios-sim`.)
* Para compilar e executar um aplicativo para Android:

  * Instale [Java Development Kit (JDK) 7](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) ou superior. Certifique-se de que `JAVA_HOME` (variável de ambiente) está configurado corretamente de acordo com o caminho de instalação do JDK (por exemplo C:\Program Files\Java\jdk1.7.0_75).
  * Instale o [SDK do Android](http://developer.android.com/sdk/installing/index.html?pkg=tools) e adicione o local `<android-sdk-location>\tools` (por exemplo, C:\tools\Android\android-sdk\tools) em sua `PATH` variável de ambiente.
  * Abra o Gerenciador de SDK do Android (por exemplo, por meio do terminal: `android`) e instale:
    * *Android 5.0.1 (API 21)* SDK de plataforma
    * *Android SDK Build-tools* versão 19.1.0 ou superior
    * *Repositório de suporte do Android* (Extras)

  O SDK do Android não fornece qualquer instância do emulador padrão. Crie uma nova executando `android avd` no terminal e, em seguida, selecionando **Criar**, se você quiser executar o aplicativo Android em um emulador. Recomendamos um nível de API de 19 ou superior. Para obter mais informações sobre as opções de criação e o emulador Android, consulte [Gerenciador AVD](http://developer.android.com/tools/help/avd-manager.html) no site do Android.

## <a name="step-1-register-an-application-with-azure-ad"></a>Etapa 1: registrar um aplicativo com o Azure AD
Esta etapa é opcional. Este tutorial fornece valores previamente provisionados que você pode usar para ver o exemplo em ação sem fazer nenhum provisionamento em seu próprio locatário. No entanto, é recomendável que você execute essa etapa e se familiarize com o processo, pois ele será necessário quando você criar seus próprios aplicativos.

O Azure AD emite tokens somente para aplicativos conhecidos. Antes de poder usar o AD do Azure do seu aplicativo, você precisa criar uma entrada para ele no seu locatário. Para registrar um novo aplicativo no seu locatário:

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Na barra superior, clique em sua conta. Na lista **Diretório**, escolha o locatário do Azure AD no qual você quer registrar seu aplicativo.
3. Clique em **Mais Serviços** no painel esquerdo e selecione **Azure Active Directory**.
4. Clique em **Registros do aplicativo**e, em seguida, selecione **Adicionar**.
5. Siga os prompts e crie um **Aplicativo cliente nativo**. (Embora os aplicativos Cordova sejam baseados em HTML, estamos criando um aplicativo cliente nativo aqui. A opção **Aplicativo cliente nativo** deve ser selecionada, ou o aplicativo não funcionará).
  * **Nome** descreve seu aplicativo para os usuários.
  * **URI de Redirecionamento** é o URI usado para retornar os tokens para o seu aplicativo. Digite **http://MyDirectorySearcherApp**.

Depois de concluir o registro, o Azure AD atribui uma identificação exclusiva do aplicativo ao seu aplicativo. Você precisará desse valor nas próximas seções. Você o encontrará na guia do aplicativo do aplicativo recém-criado.

Para executar `DirSearchClient Sample`, dê permissão ao aplicativo recém-criado para consultar a API do Graph do Azure AD:

1. Na página **Configurações**, selecione **Permissões necessárias** e escolha **Adicionar**.  
2. Para o aplicativo Azure Active Directory, selecione **Microsoft Graph** como a API e adicione a permissão **Acessar o diretório como o usuário conectado** em **Permissões delegadas**.  Isso permite que seu aplicativo consulte a API do Graph para usuários.

## <a name="step-2-clone-the-sample-app-repository"></a>Etapa 2: clonar o repositório do aplicativo de exemplo
No shell ou na linha de comando, digite o seguinte comando:

    git clone -b skeleton https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-Cordova.git

## <a name="step-3-create-the-cordova-app"></a>Etapa 3: criar o aplicativo Cordova
Há várias maneiras de criar aplicativos Cordova. Neste tutorial, usaremos a interface de linha de comando do Cordova (CLI).

1. No shell ou na linha de comando, digite o seguinte comando:

        cordova create DirSearchClient

   O comando cria a estrutura de pastas e scaffolding para o projeto Cordova.

2. Siga para a nova pasta DirSearchClient:

        cd .\DirSearchClient

3. Copie o conteúdo do projeto inicial na subpasta www usando um gerenciador de arquivos ou o seguinte comando no seu shell:

  * Windows: `xcopy ..\NativeClient-MultiTarget-Cordova\DirSearchClient www /E /Y`
  * Mac: `cp -r  ../NativeClient-MultiTarget-Cordova/DirSearchClient/* www`

4. Adicione a lista branca de plug-in. Isso é necessário para invocar a API do Graph.

        cordova plugin add cordova-plugin-whitelist

5. Adicione todas as plataformas às quais deseja oferecer suporte. Para obter um exemplo funcional, você precisará executar pelo menos um dos comandos a seguir. Observe que você não poderá emular o iOS no Windows ou emular o Windows em um Mac.

        cordova platform add android
        cordova platform add ios
        cordova platform add windows

6. Adicione a ADAL para o plug-in Cordova no seu projeto:

        cordova plugin add cordova-plugin-ms-adal

## <a name="step-4-add-code-to-authenticate-users-and-obtain-tokens-from-azure-ad"></a>Etapa 4: adicionar código para autenticar usuários e obter tokens do Azure AD
O aplicativo que você está desenvolvendo neste tutorial fornecerá um recurso de pesquisa de diretório simples. O usuário poderá, então, digitar o alias de qualquer usuário no diretório e visualizar alguns atributos básicos. O projeto inicial contém a definição da interface do usuário básico do aplicativo (em www/index.html) e o scaffolding que conecta os ciclos de eventos do aplicativo básico, ligações de interface do usuário e a lógica de exibição dos resultados (em www/js/index.js). A única tarefa que falta para você fazer é adicionar a lógica que implementa as tarefas de identidade.

A primeira coisa que você precisa fazer no seu código é apresentar os valores de protocolo que o Azure AD usa para identificar o seu aplicativo e os recursos de destino. Esses valores serão usados para construir as solicitações de token posteriormente. Insira o trecho a seguir na parte superior do arquivo index.js:

```javascript
var authority = "https://login.microsoftonline.com/common",
    redirectUri = "http://MyDirectorySearcherApp",
    resourceUri = "https://graph.windows.net",
    clientId = "a5d92493-ae5a-4a9f-bcbf-9f1d354067d3",
    graphApiVersion = "2013-11-08";
```

Os valores `redirectUri` e `clientId` devem corresponder aos valores que descrevem o seu aplicativo no Azure AD. Você pode encontrá-los na guia **Configurar** no portal do Azure, conforme descrito na etapa 1, mostrada anteriormente neste tutorial.

> [!NOTE]
> Se tiver optado por não registrar um novo aplicativo no seu próprio locatário, você pode simplesmente colar os valores pré-configurados como tal. Você poderá ver o exemplo em execução, embora sempre deva criar sua própria entrada para os aplicativos destinados a produção.

Em seguida, adicione o código de solicitação de token. Insira o trecho a seguir entre as definições `search` e `renderData`:

```javascript
    // Shows the user authentication dialog box if required
    authenticate: function (authCompletedCallback) {

        app.context = new Microsoft.ADAL.AuthenticationContext(authority);
        app.context.tokenCache.readItems().then(function (items) {
            if (items.length > 0) {
                authority = items[0].authority;
                app.context = new Microsoft.ADAL.AuthenticationContext(authority);
            }
            // Attempt to authorize the user silently
            app.context.acquireTokenSilentAsync(resourceUri, clientId)
            .then(authCompletedCallback, function () {
                // We require user credentials, so this triggers the authentication dialog box
                app.context.acquireTokenAsync(resourceUri, clientId, redirectUri)
                .then(authCompletedCallback, function (err) {
                    app.error("Failed to authenticate: " + err);
                });
            });
        });

    },
```
Vamos examinar essa função, separando-a em suas duas partes principais.
Esse exemplo foi criado para funcionar com qualquer locatário, em vez de estar vinculado a um locatário determinado. Ele usa o ponto de extremidade "/common", que permite que o usuário insira qualquer conta no momento da autenticação e direciona a solicitação para o locatário a que ela pertence.

Esta primeira parte do método inspeciona o cache da ADAL para ver se um token já está armazenado. Nesse caso, o método usa os locatários de onde o token veio para reinicializar a ADAL. Isso é necessário para evitar prompts extras, uma vez que o uso de "/common" sempre resulta em solicitar que o usuário insira uma nova conta.

```javascript
        app.context = new Microsoft.ADAL.AuthenticationContext(authority);
        app.context.tokenCache.readItems().then(function (items) {
            if (items.length > 0) {
                authority = items[0].authority;
                app.context = new Microsoft.ADAL.AuthenticationContext(authority);
            }
```
A segunda parte do método executa a solicitação de token adequada. O método `acquireTokenSilentAsync` solicita que a ADAL retorne um token para o recurso especificado sem mostrar qualquer experiência do usuário. Isso pode acontecer se o cache já tiver um token de acesso adequado armazenado, ou se houver um token de atualização que pode ser usado para obter um novo token de acesso sem mostrar nenhum prompt. Se essa tentativa falhar, voltamos para `acquireTokenAsync`, que solicitará visivelmente que o usuário seja autenticado.

```javascript
            // Attempt to authorize the user silently
            app.context.acquireTokenSilentAsync(resourceUri, clientId)
            .then(authCompletedCallback, function () {
                // We require user credentials, so this triggers the authentication dialog box
                app.context.acquireTokenAsync(resourceUri, clientId, redirectUri)
                .then(authCompletedCallback, function (err) {
                    app.error("Failed to authenticate: " + err);
                });
            });
```
Agora que temos o token, podemos finalmente invocar a API do Graph e executar a consulta de pesquisa desejada. Insira o trecho a seguir logo abaixo da definição `authenticate`:

```javascript
// Makes an API call to receive the user list
    requestData: function (authResult, searchText) {
        var req = new XMLHttpRequest();
        var url = resourceUri + "/" + authResult.tenantId + "/users?api-version=" + graphApiVersion;
        url = searchText ? url + "&$filter=mailNickname eq '" + searchText + "'" : url + "&$top=10";

        req.open("GET", url, true);
        req.setRequestHeader('Authorization', 'Bearer ' + authResult.accessToken);

        req.onload = function(e) {
            if (e.target.status >= 200 && e.target.status < 300) {
                app.renderData(JSON.parse(e.target.response));
                return;
            }
            app.error('Data request failed: ' + e.target.response);
        };
        req.onerror = function(e) {
            app.error('Data request failed: ' + e.error);
        }

        req.send();
    },

```
Os arquivos de ponto de partida forneceram uma experiência do usuário simples para digitar o alias de um usuário em uma caixa de texto. Esse método usa esse valor para construir uma consulta, combiná-la ao token de acesso, enviá-la ao Microsoft Graph e analisar os resultados. O método `renderData`, já presente no arquivo de ponto de partida, se encarrega de visualizar os resultados.

## <a name="step-5-run-the-app"></a>Etapa 5: executar o aplicativo
Seu aplicativo está finalmente pronto para execução. Operá-lo é simples: quando o aplicativo for iniciado, digite na caixa de texto o alias do usuário que você deseja pesquisar e clique no botão. Será solicitada a sua autenticação. Após a autenticação e pesquisa bem-sucedidas, os atributos do usuário pesquisado serão exibidos.

Execuções subsequentes executarão a pesquisa sem mostrar qualquer prompt, devido à presença do token adquirido anteriormente em cache.

As etapas concretas para execução do aplicativo variam de acordo com a plataforma.

### <a name="windows-10"></a>Windows 10
   Tablet/computador: `cordova run windows --archs=x64 -- --appx=uap`

   Dispositivo móvel (requer um dispositivo móvel do Windows10 conectado a um computador): `cordova run windows --archs=arm -- --appx=uap --phone`

   > [!NOTE]
   > Durante a primeira execução, pode ser solicitado que você se inscreva para uma licença de desenvolvedor. Para mais informações, consulte [Licença de desenvolvedor](https://msdn.microsoft.com/library/windows/apps/hh974578.aspx).

### <a name="windows-81-tabletpc"></a>Tablet/computador Windows 8.1
   `cordova run windows`

   > [!NOTE]
   > Durante a primeira execução, pode ser solicitado que você se inscreva para uma licença de desenvolvedor. Para mais informações, consulte [Licença de desenvolvedor](https://msdn.microsoft.com/library/windows/apps/hh974578.aspx).

### <a name="windows-phone-81"></a>Windows Phone 8,1
   Para executar em um dispositivo conectado: `cordova run windows --device -- --phone`

   Para executar no emulador padrão: `cordova emulate windows -- --phone`

   Use `cordova run windows --list -- --phone` para ver todos os destinos disponíveis e `cordova run windows --target=<target_name> -- --phone` para executar o aplicativo em um emulador ou dispositivo específico (por exemplo, `cordova run windows --target="Emulator 8.1 720P 4.7 inch" -- --phone`).

### <a name="android"></a>Android
   Para executar em um dispositivo conectado: `cordova run android --device`

   Para executar no emulador padrão: `cordova emulate android`

   Verifique se que você criou uma instância do emulador usando o Gerenciador de AVD, conforme descrito anteriormente na seção "Pré-requisitos".

   Use `cordova run android --list` para ver todos os destinos disponíveis e `cordova run android --target=<target_name>` para executar o aplicativo em um emulador ou dispositivo específico (por exemplo, `cordova run android --target="Nexus4_emulator"`).

### <a name="ios"></a>iOS
   Para executar em um dispositivo conectado: `cordova run ios --device`

   Para executar no emulador padrão: `cordova emulate ios`

   > [!NOTE]
   > Verifique se você tem o pacote `ios-sim` instalado para ser executado no emulador. Para mais informações, consulte a seção “Pré-requisitos”.

    Use `cordova run ios --list` to see all available targets and `cordova run ios --target=<target_name>` to run the application on specific device or emulator (for example, `cordova run android --target="iPhone-6"`).

    Use `cordova run --help` to see additional build and run options.

## <a name="next-steps"></a>Próximas etapas
Para referência, o exemplo concluído (sem seus valores de configuração) está disponível no [GitHub](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-Cordova/tree/complete/DirSearchClient).

Agora você pode passar para cenários mais avançados (e mais interessantes). Você talvez queira: [proteger uma API da Web Node. js com o Azure AD](active-directory-devquickstarts-webapi-nodejs.md).

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]
