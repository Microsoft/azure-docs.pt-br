---
title: incluir arquivo
description: incluir arquivo
services: azure-communication-services
author: ddematheu2
manager: chpalm
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 9/1/2020
ms.topic: include
ms.custom: include file
ms.author: dademath
ms.openlocfilehash: c6e8be5462e0caffec7a1c88dae54f3f818ec323
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106498831"
---
[!INCLUDE [Public Preview Notice](../../includes/public-preview-include-android-ios.md)]


O **Exemplo de Destaque de Chamadas em Grupo para Android** dos Serviços de Comunicação do Azure demonstra como o SDK de Chamada do Android dos Serviços de Comunicação pode ser usado para criar uma experiência de chamada em grupo que inclua voz e vídeo. Neste exemplo de guia de início rápido, você aprenderá a configurar e executar o exemplo. Uma visão geral do exemplo é fornecida para o contexto.

## <a name="download-code"></a>Código de download

Localize o projeto para este exemplo no [GitHub](https://github.com/Azure-Samples/communication-services-android-calling-hero). Uma versão do exemplo com a [Interoperabilidade do Teams](../../concepts/teams-interop.md) pode ser encontrada em um [Branch](https://github.com/Azure-Samples/communication-services-android-calling-hero/tree/feature/teams_interop) separado.

## <a name="overview"></a>Visão geral

O exemplo é um aplicativo Android nativo que usa os SDKs para do Android dos Serviços de Comunicação do Azure para criar uma experiência de chamada com voz e vídeo. O aplicativo usa um componente do lado do servidor para provisionar tokens de acesso que são usados para inicializar o SDK dos Serviços de Comunicação do Azure. Para configurar esse componente do lado do servidor, sinta-se à vontade para seguir o tutorial [Serviço Confiável com o Azure Functions](../../tutorials/trusted-service-tutorial.md).

Aqui está um exemplo:

:::image type="content" source="../media/calling/landing-page-android.png" alt-text="Captura de tela mostrando a página de aterrissagem do aplicativo de exemplo.":::

Quando você clica no botão "Iniciar nova chamada", o aplicativo Android cria uma chamada e ingressa nela. O aplicativo também permite que você ingresse em uma chamada existente dos Serviços de Comunicação do Azure especificando a ID da chamada existente.

Depois de ingressar em uma chamada, você precisará fornecer permissão ao aplicativo para acessar a câmera e o microfone. Você também precisará fornecer um nome de exibição.

:::image type="content" source="../media/calling/pre-call-android.png" alt-text="Captura de tela mostrando a tela pré-chamada do aplicativo de exemplo.":::

Depois de configurar o nome de exibição e os dispositivos, você poderá ingressar na chamada. Você verá a tela principal de chamada na qual está a experiência de chamada principal.

:::image type="content" source="../media/calling/main-app-android.png" alt-text="Captura de tela mostrando a tela principal do aplicativo de exemplo.":::

Componentes da tela principal de chamada:

- **Galeria de Mídia**: a fase principal em que os participantes são mostrados. Se um participante tiver a câmera habilitada, o feed de vídeo dele será mostrado aqui. Cada participante tem um bloco individual que mostra o nome de exibição e o fluxo de vídeo (quando houver). A galeria dá suporte a vários participantes e é atualizada quando os participantes são adicionados à chamada ou removidos dela.
- **Barra de Ação**: é nela que os controles de chamada primários estão localizados. Esses controles permitem ativar/desativar o vídeo e o microfone, compartilhar a tela e sair da chamada.

Abaixo, você encontrará mais informações sobre os pré-requisitos e as etapas para configurar o exemplo.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura ativa. Para obter detalhes, confira [Criar uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Android Studio](https://developer.android.com/studio) em execução no computador
- Um recurso dos Serviços de Comunicação do Azure. Para obter detalhes, confira [Criar um recurso de comunicação do Azure](../../quickstarts/create-communication-resource.md).
- Uma função do Azure que executa o [ponto de extremidade de autenticação](../../tutorials/trusted-service-tutorial.md) para buscar tokens de acesso.

## <a name="running-sample-locally"></a>Como executar o exemplo localmente

O exemplo de chamada em grupo pode ser executado localmente com o Android Studio. Os desenvolvedores podem usar o dispositivo físico ou um emulador para testar o aplicativo.

### <a name="before-running-the-sample-for-the-first-time"></a>Antes de executar o exemplo pela primeira vez

1. Abra o Android Studio e selecione `Open an Existing Project`
2. Abra a pasta `AzureCalling` dentro da versão baixada do exemplo.
3. Expanda app/assets para atualizar `appSettings.properties`. Defina o valor da chave `communicationTokenFetchUrl` como a URL para o ponto de extremidade de autenticação com um pré-requisito.

### <a name="run-sample"></a>Executar o exemplo

Compile e execute o exemplo no Android Studio.

## <a name="optional-securing-an-authentication-endpoint"></a>(Opcional) Como proteger um ponto de extremidade de autenticação

Para fins de demonstração, esse exemplo usa um ponto de extremidade publicamente acessível por padrão para buscar um token dos Serviços de Comunicação do Azure. Para cenários de produção, recomendamos usar um ponto de extremidade próprio protegido para provisionar seus tokens.

Com a configuração adicional, este exemplo dá suporte à conexão com um ponto de extremidade protegido do **Azure AD** (Azure Active Directory), de modo que o logon do usuário seja necessário para que o aplicativo busque um token dos Serviços de Comunicação do Azure. Confira as etapas abaixo:

1. Habilitar a autenticação do Azure Active Directory no seu aplicativo.  
   - [Registrar seu aplicativo no Azure Active Directory (usando as configurações da plataforma Android)](../../../active-directory/develop/tutorial-v2-android.md) 
    - [Configurar seu aplicativo do Serviço de Aplicativo ou do Azure Functions aplicativo para usar o logon do Azure AD](../../../app-service/configure-authentication-provider-aad.md)

2. Acesse a página de visão geral do aplicativo registrado em Registros do aplicativo do Azure Active Directory. Anote a `Package name`, a `Signature hash` e o `MSAL Configutaion`

:::image type="content" source="../media/calling/aad-overview-android.png" alt-text="Configuração do Azure Active Directory no portal do Azure.":::

3. Edite `AzureCalling/app/src/main/res/raw/auth_config_single_account.json` e defina `isAADAuthEnabled` para habilitar o Azure Active Directory
4. Edite `AndroidManifest.xml` e defina `android:path` como o hash de assinatura do repositório de chaves. (Opcional). O valor atual usa um hash do debug.keystore empacotado. Se for usado um repositório de chaves diferente, ele precisará ser atualizado.
   ```
   <activity android:name="com.microsoft.identity.client.BrowserTabActivity">
            <intent-filter>
                <action android:name="android.intent.action.VIEW" />
                <category android:name="android.intent.category.DEFAULT" />
                <category android:name="android.intent.category.BROWSABLE" />
                <data
                    android:host="com.azure.samples.communication.calling"
                    android:path="/Signature hash" <!-- do not remove /. The current hash in AndroidManifest.xml is for debug.keystore. -->
                    android:scheme="msauth" />
            </intent-filter>
        </activity>
   ```
5. Copie a configuração da MSAL Android do portal do Azure e cole-a em `AzureCalling/app/src/main/res/raw/auth_config_single_account.json`. Inclua "account_mode": "SINGLE"
   ```
      {
         "client_id": "",
         "authorization_user_agent": "DEFAULT",
         "redirect_uri": "",
         "account_mode" : "SINGLE",
         "authorities": [
            {
               "type": "AAD",
               "audience": {
               "type": "AzureADMyOrg",
               "tenant_id": ""
               }
            }
         ]
      }
   ```

6. Edite `AzureCalling/app/src/main/res/raw/auth_config_single_account.json` e defina o valor da chave `communicationTokenFetchUrl` como a URL para o ponto de extremidade de autenticação seguro.
7. Edite `AzureCalling/app/src/main/res/raw/auth_config_single_account.json` e defina o valor da chave `aadScopes` dos escopos `Azure Active Directory` `Expose an API`

## <a name="clean-up-resources"></a>Limpar os recursos

Se quiser limpar e remover uma assinatura dos Serviços de Comunicação, exclua o recurso ou o grupo de recursos. Excluir o grupo de recursos também exclui todos os recursos associados a ele. Saiba mais sobre [como limpar recursos](../../quickstarts/create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Próximas etapas

>[!div class="nextstepaction"]
>[Baixar o exemplo do GitHub](https://github.com/Azure-Samples/communication-services-android-calling-hero)

Para obter mais informações, consulte os seguintes artigos:

- Familiarize-se com o [uso do SDK de Chamada](../../quickstarts/voice-video-calling/calling-client-samples.md)
- Saiba mais sobre [como a chamada funciona](../../concepts/voice-video-calling/about-call-types.md)

### <a name="additional-reading"></a>Leituras adicionais

- [GitHub de Comunicação do Azure](https://github.com/Azure/communication) – Encontre mais exemplos e informações na página oficial do GitHub
- [Amostras](./../overview.md) – encontre mais amostras e exemplos em nossa página de visão geral de amostras.
- [Recursos de Chamada de Comunicação do Azure](https://docs.microsoft.com/azure/communication-services/concepts/voice-video-calling/calling-sdk-features): para saber mais sobre o SDK de Chamada para Android, confira [SDK de Chamada para Android de Comunicação do Azure](https://search.maven.org/artifact/com.azure.android/azure-communication-calling)
