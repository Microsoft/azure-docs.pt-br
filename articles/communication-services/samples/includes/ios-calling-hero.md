---
title: incluir arquivo
description: incluir arquivo
services: azure-communication-services
author: ddematheu2
manager: chpalm
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 03/10/2021
ms.topic: include
ms.custom: include file
ms.author: dademath
ms.openlocfilehash: 287520f2964fba7c3c3804853e9356a8c77b2d06
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106498792"
---
[!INCLUDE [Public Preview Notice](../../includes/public-preview-include-android-ios.md)]

O **Exemplo de Destaque de Chamadas em Grupo para iOS** dos Serviços de Comunicação do Azure demonstra como o SDK de Chamada para iOS dos Serviços de Comunicação pode ser usado para criar uma experiência de chamada em grupo que inclua voz e vídeo. Neste exemplo de guia de início rápido, você aprenderá a configurar e executar o exemplo. Uma visão geral do exemplo é fornecida para o contexto.

## <a name="download-code"></a>Código de download

Localize o projeto para este exemplo no [GitHub](https://github.com/Azure-Samples/communication-services-ios-calling-hero). Uma versão do exemplo com a [Interoperabilidade do Teams](../../concepts/teams-interop.md) pode ser encontrada em um [Branch](https://github.com/Azure-Samples/communication-services-ios-calling-hero/tree/feature/teams_interop) separado.

## <a name="overview"></a>Visão geral

O exemplo é um aplicativo iOS nativo que usa os SDKs para iOS dos Serviços de Comunicação do Azure para criar uma experiência de chamada com voz e vídeo. O aplicativo usa um componente do lado do servidor para provisionar tokens de acesso que são usados para inicializar o SDK dos Serviços de Comunicação do Azure. Para configurar esse componente do lado do servidor, sinta-se à vontade para seguir o tutorial [Serviço Confiável com o Azure Functions](../../tutorials/trusted-service-tutorial.md).

Aqui está um exemplo:

:::image type="content" source="../media/calling/landing-page-ios.png" alt-text="Captura de tela mostrando a página de aterrissagem do aplicativo de exemplo.":::

Quando você clica no botão "Iniciar nova chamada", o aplicativo iOS cria uma chamada e ingressa nela. O aplicativo permite que você ingresse em uma chamada existente dos Serviços de Comunicação do Azure pela especificação da ID da chamada existente.

Depois de ingressar em uma chamada, você precisará fornecer permissão ao aplicativo para acessar a câmera e o microfone. Você também precisará fornecer um nome de exibição.

:::image type="content" source="../media/calling/pre-call-ios.png" alt-text="Captura de tela mostrando a tela pré-chamada do aplicativo de exemplo.":::

Depois de configurar o nome de exibição e os dispositivos, você poderá ingressar na chamada. Você verá a tela principal de chamada na qual está a experiência de chamada principal.

:::image type="content" source="../media/calling/main-app-ios.png" alt-text="Captura de tela mostrando a tela principal do aplicativo de exemplo.":::

Componentes da tela principal de chamada:

- **Galeria de Mídia**: a fase principal em que os participantes são mostrados. Se um participante tiver a câmera habilitada, o feed de vídeo dele será mostrado aqui. Cada participante tem um bloco individual que mostra o nome de exibição e o fluxo de vídeo (quando houver). A galeria dá suporte a vários participantes e é atualizada quando os participantes são adicionados à chamada ou removidos dela.
- **Barra de Ação**: é nela que os controles de chamada primários estão localizados. Esses controles permitem ativar/desativar o vídeo e o microfone, compartilhar a tela e sair da chamada.

Abaixo, você encontrará mais informações sobre os pré-requisitos e as etapas para configurar o exemplo.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura ativa. Para obter detalhes, confira [Criar uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Um Mac executando [Xcode](https://go.microsoft.com/fwLink/p/?LinkID=266532), juntamente com um certificado de desenvolvedor válido instalado em seu conjunto de chaves.
- Um recurso dos Serviços de Comunicação do Azure. Para obter detalhes, confira [Criar um recurso de comunicação do Azure](../../quickstarts/create-communication-resource.md).
- Uma função do Azure que executa o [ponto de extremidade de autenticação](../../tutorials/trusted-service-tutorial.md) para buscar tokens de acesso.

## <a name="running-sample-locally"></a>Como executar o exemplo localmente

O exemplo de chamada em grupo pode ser executado localmente com o Xcode. Os desenvolvedores podem usar o dispositivo físico ou um emulador para testar o aplicativo.

### <a name="before-running-the-sample-for-the-first-time"></a>Antes de executar o exemplo pela primeira vez

1. Instale as dependências executando `pod install`.
2. Abra `AzureCalling.xcworkspace` no Xcode.
3. Atualizar `AppSettings.plist`. Defina o valor da chave `communicationTokenFetchUrl` como a URL para o ponto de extremidade de autenticação.

### <a name="run-sample"></a>Executar o exemplo

Compile e execute o exemplo no Xcode.

## <a name="optional-securing-an-authentication-endpoint"></a>(Opcional) Como proteger um ponto de extremidade de autenticação

Para fins de demonstração, esse exemplo usa um ponto de extremidade publicamente acessível por padrão para buscar um token de acesso dos Serviços de Comunicação do Azure. Para cenários de produção, recomendamos usar um ponto de extremidade próprio protegido para provisionar seus tokens.

Com a configuração adicional, esse exemplo dá suporte à conexão com um ponto de extremidade protegido do **Azure AD** (Azure Active Directory), de modo que o logon do usuário seja necessário para que o aplicativo busque um token de acesso dos Serviços de Comunicação do Azure. Confira as etapas abaixo:

1. Habilitar a autenticação do Azure Active Directory no seu aplicativo.  
   - [Registrar seu aplicativo no Azure Active Directory (usando as configurações da plataforma iOS/macOS)](../../../active-directory/develop/tutorial-v2-ios.md) 
    - [Configurar seu aplicativo do Serviço de Aplicativo ou do Azure Functions aplicativo para usar o logon do Azure AD](../../../app-service/configure-authentication-provider-aad.md)
2. Acesse a página de visão geral do aplicativo registrado em Registros do aplicativo do Azure Active Directory. Anote a `Application (client) ID`, a `Directory (tenant) ID` e o `Application ID URI`

:::image type="content" source="../media/calling/aad-overview.png" alt-text="Configuração do Azure Active Directory no portal do Azure.":::

3. Abra `AppSettings.plist` no Xcode e adicione os seguintes valores de chave:
   - `communicationTokenFetchUrl`: a URL usada para solicitar o token dos Serviços de Comunicação do Azure 
   - `isAADAuthEnabled`: um valor booliano para indicar se a autenticação de token dos Serviços de Comunicação do Azure é necessária
   - `aadClientId`: a ID de cliente do aplicativo
   - `aadTenantId`: a ID do Diretório (locatário)
   - `aadRedirectURI`: o URI de redirecionamento deve estar neste formato: `msauth.<app_bundle_id>://auth`
   - `aadScopes`: uma matriz de escopos de permissão solicitada dos usuários para autorização. Adicione `<Application ID URI>/user_impersonation` à matriz para permitir acesso ao ponto de extremidade de autenticação

## <a name="clean-up-resources"></a>Limpar os recursos

Se quiser limpar e remover uma assinatura dos Serviços de Comunicação, exclua o recurso ou o grupo de recursos. Excluir o grupo de recursos também exclui todos os recursos associados a ele. Saiba mais sobre [como limpar recursos](../../quickstarts/create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Próximas etapas

>[!div class="nextstepaction"]
>[Baixar o exemplo do GitHub](https://github.com/Azure-Samples/communication-services-ios-calling-hero)

Para obter mais informações, consulte os seguintes artigos:

- Familiarize-se com o [uso do SDK de Chamada](../../quickstarts/voice-video-calling/calling-client-samples.md)
- Saiba mais sobre [como a chamada funciona](../../concepts/voice-video-calling/about-call-types.md)

### <a name="additional-reading"></a>Leituras adicionais

- [GitHub de Comunicação do Azure](https://github.com/Azure/communication) – Encontre mais exemplos e informações na página oficial do GitHub
- [Amostras](./../overview.md) – encontre mais amostras e exemplos em nossa página de visão geral de amostras.
- [Recursos de Chamada de Comunicação do Azure](https://docs.microsoft.com/azure/communication-services/concepts/voice-video-calling/calling-sdk-features): para saber mais sobre o SDK de Chamada para iOS, confira [SDK de Chamada para iOS de Comunicação do Azure](https://github.com/Azure/Communication/releases/)
