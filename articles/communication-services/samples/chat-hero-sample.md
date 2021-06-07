---
title: Exemplo de destaque de chat de grupo
titleSuffix: An Azure Communication Services sample overview
description: Visão geral do exemplo de destaque do chat usando os Serviços de Comunicação do Azure para permitir que os desenvolvedores aprendam mais sobre o funcionamento interno do exemplo e saibam como modificá-lo.
author: ddematheu2
manager: nimag
services: azure-communication-services
ms.author: dademath
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 5547e49cd7ea7f6c0cc97b5559564b64e0deda7c
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106168834"
---
# <a name="get-started-with-the-group-chat-hero-sample"></a>Introdução ao exemplo de destaque de chat de grupo

[!INCLUDE [Public Preview Notice](../includes/public-preview-include-chat.md)]

> [!IMPORTANT]
> [Este exemplo está disponível **no GitHub**.](https://github.com/Azure-Samples/communication-services-web-chat-hero)


O **Exemplo de Destaque de Chat em Grupo** dos Serviços de Comunicação do Azure demonstra como o SDK da Web de chat dos Serviços de Comunicação pode ser usada para criar uma experiência de chamada em grupo.

Neste guia de início rápido de Exemplo, aprenderemos como o exemplo funciona antes de o executarmos no computador local. Em seguida, implantaremos o exemplo no Azure usando seus próprios recursos de Serviços de Comunicação do Azure.


## <a name="overview"></a>Visão geral

O exemplo tem um aplicativo do lado do cliente e um aplicativo do lado do servidor. O **aplicativo do lado do cliente** é um aplicativo Web React/Redux que usa a estrutura de interface do usuário Fluent da Microsoft. Esse aplicativo envia solicitações a um **aplicativo do lado do servidor** ASP.NET Core que ajuda o aplicativo do lado do cliente a se conectar ao Azure.

Aqui está um exemplo:

:::image type="content" source="./media/chat/landing-page.png" alt-text="Captura de tela mostrando a página de aterrissagem do aplicativo de exemplo.":::

Quando você pressiona o botão "Iniciar um Chat", o aplicativo Web busca um token de acesso do usuário do aplicativo do lado do servidor. Esse token é usado para conectar o aplicativo cliente aos Serviços de Comunicação do Azure. Depois que o token é recuperado, você é solicitado a especificar seu nome e emoji que vão representá-lo no chat.

:::image type="content" source="./media/chat/pre-chat.png" alt-text="Captura de tela mostrando o chat do aplicativo.":::

Depois de configurar seu nome de exibição e emoji, você pode ingressar na sessão de chat. Agora, você verá a tela de chat principal na qual reside a experiência de chat central.

:::image type="content" source="./media/chat/main-app.png" alt-text="Captura de tela mostrando o exemplo principal do aplicativo de exemplo.":::

Componentes da tela principal de chat:

- **Área de Chat Principal**: essa é a principal experiência de chat em que os usuários podem enviar e receber mensagens. Para enviar mensagens, você pode usar a área de entrada e pressionar Enter (ou usar o botão Enviar). As mensagens de chat recebidas são categorizadas pelo remetente com o nome e o emoji corretos. Você verá dois tipos de notificações na área de chat: 1) notificações de digitando quando um usuário está digitando e 2) notificações de enviado e lido para mensagens.
- **Cabeçalho**: Ali, o usuário verá o título da conversa de chat e os controles para alternar o participante e as barras laterais de configurações, além de um botão Sair para sair da sessão de chat.
- **Barra lateral**: é aí que os participantes e as informações de configuração são mostrados quando alternados usando os controles no cabeçalho. A barra lateral dos participantes contém uma lista de participantes no chat e um link para convidar os participantes para a sessão de chat. A barra lateral de configurações permite que você configure o título da conversa de chat.

Abaixo, você encontrará mais informações sobre os pré-requisitos e as etapas para configurar o exemplo.

## <a name="prerequisites"></a>Pré-requisitos

- Criar uma conta do Azure com uma assinatura ativa. Para obter detalhes, confira [Criar uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Node.js (8.11.2 e superiores)](https://nodejs.org/en/download/)
- [Visual Studio (2017 e superiores)](https://visualstudio.microsoft.com/vs/)
- [.NET Core 3.1](https://dotnet.microsoft.com/download/dotnet-core/3.1) (Instale a versão que corresponde à sua instância do Visual Studio: 32 vs 64 bits)
- Criar um recurso dos Serviços de Comunicação do Azure. Para obter detalhes, confira [Criar um recurso de comunicação do Azure](../quickstarts/create-communication-resource.md). Você precisará registrar a **cadeia de conexão** do recurso para este guia de início rápido.

## <a name="locally-deploying-the-service--client-app"></a>Como implantar localmente o aplicativo de cliente e serviço

O exemplo de chat de conversa único consiste, essencialmente, em dois "aplicativos": um aplicativo cliente e um aplicativo para servidores.

Abra o Visual Studio no chat.csproj e execute no modo de depuração. Isso iniciará o serviço de front-end do chat. Quando o aplicativo de servidor for visitado pelo navegador, ele redirecionará o tráfego para o serviço de front-end de chat implantado localmente.

Você pode testar o exemplo localmente abrindo várias sessões do navegador com a URL do chat para simular um chat de vários usuários.

## <a name="before-running-the-sample-for-the-first-time"></a>Antes de executar o exemplo pela primeira vez

1. Abra uma instância do PowerShell, terminal do Windows, prompt de comando ou equivalente e navegue até o diretório para o qual você deseja clonar o exemplo.
2. `git clone https://github.com/Azure-Samples/communication-services-web-chat-hero.git`
3. Obtenha a `Connection String` do portal do Azure. Para obter mais informações sobre cadeias de conexão, confira [Criar Recursos de Comunicação do Azure](../quickstarts/create-communication-resource.md)
4. Depois de obter a `Connection String`, adicione a cadeia de conexão ao arquivo **Chat/appsettings.json** encontrado na pasta Chat. Insira a cadeia de conexão na variável: `ResourceConnectionString`.

### <a name="local-run"></a>Execução local

1. Acesse a pasta Chat e abra a solução `Chat.csproj` no Visual Studio
2. Execute o projeto. O navegador será aberto em localhost:5000.

#### <a name="troubleshooting"></a>Solução de problemas

- A solução não é criada; ela gera erros durante a instalação/criada do NPM

   Limpar/recompilar a solução C#

## <a name="publish-the-sample-to-azure"></a>Publicar o exemplo no Azure

1. Clique com o botão direito do mouse no projeto `Chat` e selecione Publicar.
2. Crie um perfil de publicação e selecione sua assinatura do Azure.
3. Antes de publicar, adicione sua cadeia de conexão com `Edit App Service Settings`, preencha `ResourceConnectionString` como a chave e forneça sua cadeia de conexão (copiada de appsettings.json) como o valor.

## <a name="clean-up-resources"></a>Limpar os recursos

Se quiser limpar e remover uma assinatura dos Serviços de Comunicação, exclua o recurso ou o grupo de recursos. Excluir o grupo de recursos também exclui todos os recursos associados a ele. Saiba mais sobre [como limpar recursos](../quickstarts/create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Próximas etapas

>[!div class="nextstepaction"]
>[Baixar o exemplo do GitHub](https://github.com/Azure-Samples/communication-services-web-chat-hero)

Para obter mais informações, consulte os seguintes artigos:

- Saiba mais sobre os [conceitos de chat](../concepts/chat/concepts.md)
- Familiarize-se com nosso [SDK de Chat](../concepts/chat/sdk-features.md)
- Examinar o exemplo [Contoso Med App](https://github.com/Azure-Samples/communication-services-contoso-med-app)

## <a name="additional-reading"></a>Leituras adicionais

- [Amostras](./overview.md) – encontre mais amostras e exemplos em nossa página de visão geral de amostras.
- [Redux](https://redux.js.org/) – gerenciamento de estado do lado do cliente
- [FluentUI](https://aka.ms/fluent-ui) – biblioteca de interface do usuário da Microsoft
- [React](https://reactjs.org/) – biblioteca para criar interfaces do usuário
- [ASP.NET Core](/aspnet/core/introduction-to-aspnet-core?preserve-view=true&view=aspnetcore-3.1) – Estrutura para criar aplicativos Web
