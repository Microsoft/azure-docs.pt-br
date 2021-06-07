---
title: incluir arquivo
description: incluir arquivo
services: azure-communication-services
author: mikben
manager: mikben
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 03/10/2021
ms.topic: include
ms.custom: include file
ms.author: mikben
ms.openlocfilehash: 0504467e387c0875a8e00bd1cf638ec437232092
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106554249"
---
O **Exemplo de Destaque de Chamadas em Grupo** dos Serviços de Comunicação do Azure demonstra como o SDK da Web de Chamadas dos Serviços de Comunicação pode ser usado para criar uma experiência de chamada em grupo.

Neste guia de início rápido de Exemplo, aprenderemos como o exemplo funciona antes de o executarmos no computador local. Em seguida, implantaremos o exemplo no Azure usando seus próprios recursos de Serviços de Comunicação do Azure.

## <a name="download-code"></a>Código de download

Localize o projeto para este exemplo no [GitHub](https://github.com/Azure-Samples/communication-services-web-calling-hero). Uma versão do exemplo com a [Interoperabilidade do Teams](../../concepts/teams-interop.md) pode ser encontrada em um [Branch](https://github.com/Azure-Samples/communication-services-web-calling-hero/tree/teams-interop) separado.

## <a name="overview"></a>Visão geral

O exemplo tem um aplicativo do lado do cliente e um aplicativo do lado do servidor. O **aplicativo do lado do cliente** é um aplicativo Web React/Redux que usa a estrutura de interface do usuário Fluent da Microsoft. Esse aplicativo envia solicitações a um **aplicativo do lado do servidor** ASP.NET Core que ajuda o aplicativo do lado do cliente a se conectar ao Azure.

Aqui está um exemplo:

:::image type="content" source="../media/calling/landing-page.png" alt-text="Captura de tela mostrando a página de aterrissagem do aplicativo de exemplo.":::

Quando você pressiona o botão "Iniciar uma chamada", o aplicativo Web busca um token de acesso do usuário do aplicativo do lado do servidor. Esse token é usado para conectar o aplicativo cliente aos Serviços de Comunicação do Azure. Depois que o token é recuperado, você é solicitado a especificar a câmera e o microfone que deseja usar. Você pode desabilitar/habilitar seus dispositivos com controles de alternância:

:::image type="content" source="../media/calling/pre-call.png" alt-text="Captura de tela mostrando a tela pré-chamada do aplicativo de exemplo.":::

Depois de configurar o nome de exibição e os dispositivos, você pode ingressar na sessão de chamada. Depois, você verá a tela principal de chamada na qual está a experiência de chamada principal.

:::image type="content" source="../media/calling/main-app.png" alt-text="Captura de tela mostrando a tela principal do aplicativo de exemplo.":::

Componentes da tela principal de chamada:

- **Galeria de Mídia**: a fase principal em que os participantes são mostrados. Se um participante tiver a câmera habilitada, o feed de vídeo dele será mostrado aqui. Cada participante tem um bloco individual que mostra seu nome de exibição e fluxo de vídeo (quando há um)
- **Cabeçalho**: É aí que os controles de chamada primários estão localizados para alternar as configurações e a barra lateral do participante, ativar/desativar vídeo e combinação, compartilhar a tela e sair da chamada.
- **Barra lateral**: é nela que as informações de participantes e configurações são mostradas quando alternadas usando os controles no cabeçalho. O componente pode ser descartado usando o "X"' no canto superior direito. A barra lateral dos participantes mostrará uma lista de participantes e um link para convidar mais usuários para o chat. A barra lateral de configurações permite que você defina as configurações de microfone e câmera.

> [!NOTE]
> Com base nas limitações do SDK de chamada da Web, apenas um fluxo de vídeo remoto é renderizado. Para obter mais informações, confira [Suporte ao Fluxo do SDK de chamada](https://docs.microsoft.com/azure/communication-services/concepts/voice-video-calling/calling-sdk-features#calling-sdk-streaming-support).

Abaixo, você encontrará mais informações sobre os pré-requisitos e as etapas para configurar o exemplo.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura ativa. Para obter detalhes, confira [Criar uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Node.js (12.18.4 e superiores)](https://nodejs.org/en/download/)
- [Visual Studio (2019 e superiores)](https://visualstudio.microsoft.com/vs/)
- [.NET Core 3.1](https://dotnet.microsoft.com/download/dotnet-core/3.1) (Instale a versão que corresponde à sua instância do Visual Studio: 32 vs 64 bits)
- Um recurso dos Serviços de Comunicação do Azure. Para obter detalhes, confira [Criar um recurso de comunicação do Azure](../../quickstarts/create-communication-resource.md). Você precisará registrar a **cadeia de conexão** do recurso para este guia de início rápido.

## <a name="locally-deploy-the-service--client-applications"></a>Implantar localmente o serviço e o aplicativos cliente

O exemplo de chamada em grupo consiste, essencialmente, em dois aplicativos: ClientApp e Service.NET.

Quando queremos implantar localmente, precisamos iniciar ambos os aplicativos. Quando o aplicativo para servidores é acessado pelo navegador, ele usa o ClientApp implantado localmente para a experiência do usuário.

Você pode testar o exemplo localmente abrindo várias sessões do navegador com a URL da chamada para simular uma chamada de vários usuários.

### <a name="before-running-the-sample-for-the-first-time"></a>Antes de executar o exemplo pela primeira vez

1. Abra uma instância do PowerShell, terminal do Windows, prompt de comando ou equivalente e navegue até o diretório para o qual você deseja clonar o exemplo.
2. `git clone https://github.com/Azure-Samples/communication-services-web-calling-hero.git`
3. Obtenha a `Connection String` do portal do Azure. Para obter mais informações sobre cadeias de conexão, confira [Criar Recursos de Comunicação do Azure](../../quickstarts/create-communication-resource.md).
4. Depois de obter o `Connection String`, adicione a cadeia de conexão ao arquivo **Calling/appsetting.json** encontrado na pasta Serviços do .NET. Insira a cadeia de conexão na variável: `ResourceConnectionString`.

### <a name="local-run"></a>Execução local

1. Acesse a pasta Chamada e abra a solução `Calling.csproj` no Visual Studio.
2. Execute o projeto `Calling`. O navegador será aberto em `localhost:5001`.

## <a name="publish-the-sample-to-azure"></a>Publicar o exemplo no Azure

1. Clique com o botão direito do mouse no projeto `Calling` e selecione Publicar.
2. Crie um perfil de publicação e selecione sua assinatura do Azure.
3. Antes de publicar, adicione sua cadeia de conexão com `Edit App Service Settings`, preencha `ResourceConnectionString` como a chave e forneça sua cadeia de conexão (copiada de appsettings.json) como o valor.

## <a name="clean-up-resources"></a>Limpar os recursos

Se quiser limpar e remover uma assinatura dos Serviços de Comunicação, exclua o recurso ou o grupo de recursos. Excluir o grupo de recursos também exclui todos os recursos associados a ele. Saiba mais sobre [como limpar recursos](../../quickstarts/create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Próximas etapas

>[!div class="nextstepaction"]
>[Baixar o exemplo do GitHub](https://github.com/Azure-Samples/communication-services-web-calling-hero)

Para obter mais informações, consulte os seguintes artigos:

- Familiarize-se com o [uso do SDK de Chamada](../../quickstarts/voice-video-calling/calling-client-samples.md)
- Saiba mais sobre [como a chamada funciona](../../concepts/voice-video-calling/about-call-types.md)

### <a name="additional-reading"></a>Leituras adicionais

- [Amostras](./../overview.md) – encontre mais amostras e exemplos em nossa página de visão geral de amostras.
- [Redux](https://redux.js.org/) – gerenciamento de estado do lado do cliente
- [FluentUI](https://aka.ms/fluent-ui) – biblioteca de interface do usuário da Microsoft
- [React](https://reactjs.org/) – biblioteca para criar interfaces do usuário
- [ASP.NET Core](/aspnet/core/introduction-to-aspnet-core?preserve-view=true&view=aspnetcore-3.1) – Estrutura para criar aplicativos Web
