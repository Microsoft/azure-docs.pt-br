---
title: Serviços de Comunicação do Azure – Exemplo de chamada da Web
titleSuffix: An Azure Communication Services sample
description: Saiba mais sobre o exemplo de chamada da Web dos Serviços de Comunicação do Azure
author: chriswhilar
manager: mariusu-msft
services: azure-communication-services
ms.author: mariusu
ms.date: 10/15/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: a00f319c55665695879aac3c8ba711ce13fd5d4e
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/25/2021
ms.locfileid: "98763042"
---
# <a name="get-started-with-the-web-calling-sample"></a>Introdução ao exemplo de chamada da Web

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

> [!IMPORTANT]
> [Este exemplo está disponível em Github](https://github.com/Azure-Samples/communication-services-web-calling-tutorial/).

O **exemplo de chamada da Web** dos Serviços de Comunicação do Azure demonstram como a biblioteca de clientes de Chamadas do Serviços de Comunicação pode ser usada para criar uma experiência de chamada com JavaScript.

Neste guia de início rápido de Exemplo, aprenderemos como o exemplo funciona antes de o executarmos no computador local. Em seguida, implantaremos o exemplo no Azure usando seus próprios recursos de Serviços de Comunicação do Azure.

## <a name="overview"></a>Visão geral

O exemplo de chamada da Web é um aplicativo da Web que serve como uma explicação passo a passo dos vários recursos fornecidos pela biblioteca cliente de chamada da Web dos Serviços de Comunicação. 

Este exemplo foi criado para desenvolvedores e facilita o início do uso dos Serviços de Comunicação. Sua interface do usuário é dividida em várias seções, cada uma apresentando um botão "mostrar código" que permite copiar o código diretamente do navegador para seu próprio aplicativo de Serviços de Comunicação.

Quando o [exemplo de chamada da Web](https://github.com/Azure-Samples/communication-services-web-calling-tutorial) estiver em execução no computador, você verá a seguinte página de aterrissagem:

:::image type="content" source="./media/web-calling-tutorial-page-1.png" alt-text="Tutorial de chamada da Web 1" lightbox="./media/web-calling-tutorial-page-1.png":::

:::image type="content" source="./media/web-calling-tutorial-page-2.png" alt-text="Tutorial de chamada da Web 2" lightbox="./media/web-calling-tutorial-page-2.png":::


## <a name="user-provisioning-and-sdk-initialization"></a>Provisionamento do usuário e inicialização de SDK 

Para começar a usar a demonstração, insira a cadeia de conexão do seu recurso [Serviços de Comunicação](../quickstarts/create-communication-resource.md) no `config.json`. Isso será usado para provisionar um [token de acesso de usuário](../concepts/authentication.md) para que seu SDK de chamada possa ser inicializado.

Insira seu próprio identificador pessoal na entrada de identidade do usuário. Se nada for fornecido aqui, uma identidade de usuário aleatória será gerada. 

Clique em "Provisionamento de usuário e inicializar SDK" para inicializar o SDK usando um token provisionado pelo serviço de provisionamento de token de back-end. Este serviço de back-end está em `/project/webpack.config.js`.

Clique no botão "Mostrar código" para ver o código de exemplo que você pode usar em sua própria solução.

Você deverá ver o seguinte quando o SDK for inicializado:

:::image type="content" source="./media/user-provisioning.png" alt-text="Provisionamento do usuário" lightbox="./media/user-provisioning.png":::

Agora você está pronto para começar a colocar chamadas usando seu recurso de Serviços de Comunicação!

## <a name="placing-and-receiving-calls"></a>Envio e recebimento de chamadas

O SDK de chamada Web dos Serviços de Comunicação permite chamada **1:1**, **1: N** e **grupo**.

Para chamadas de saída 1:1 ou 1:N, você pode especificar várias identidades de usuário de Serviços de Comunicação para chamar usando valores separados por vírgulas. Você também pode especificar números de telefone tradicionais (PSTN) para chamar usando valores separados por vírgulas. 

Ao chamar números de telefone PSTN, especifique a ID de chamador alternativa. Clique no botão "Fazer chamada" para fazer uma chamada de saída:

:::image type="content" source="./media/place-a-call.png" alt-text="Fazer uma chamada" lightbox="./media/place-a-call.png":::

Para ingressar em uma chamada de grupo, insira o GUID que identifica a chamada e clique no botão "grupo de junção":

:::image type="content" source="./media/join-a-group-call.png" alt-text="Ingressar em um grupo" lightbox="./media/join-a-group-call.png":::

Clique no botão "Mostrar código" para ver o código de exemplo para fazer chamadas, receber chamadas e ingressar em chamadas em grupo.

Uma chamada ativa parece com:

:::image type="content" source="./media/group-call.png" alt-text="Chamada em grupo" lightbox="./media/group-call.png":::

Este exemplo também fornece snippets de código para os seguintes recursos:

  - Clicando no ícone de vídeo para ativar/desativar sua câmera de vídeo
  - Clicando no ícone de microfone para ligar/desligar o microfone
  - Clicando no ícone de reprodução para manter/desestabilizar a chamada
  - Clicando no ícone de tela para iniciar/parar o compartilhamento de sua tela
  - Clicando no ícone de pessoa para adicionar um participante à chamada
  - Clicando em "Remover participante" na lista de participantes para remover um participante específico da chamada


## <a name="next-steps"></a>Próximas etapas

>[!div class="nextstepaction"] 
>[Baixar o exemplo do GitHub](https://github.com/Azure-Samples/communication-services-web-calling-tutorial/)

Para obter mais informações, consulte os seguintes artigos:

- Familiarize-se com [usar a biblioteca de clientes de chamada](../quickstarts/voice-video-calling/calling-client-samples.md)
- Saiba mais sobre [como a chamada funciona](../concepts/voice-video-calling/about-call-types.md)
- Examine os [Documentos de referência de API](/javascript/api/azure-communication-services/@azure/communication-calling/?view=azure-communication-services-js)
- Examinar o exemplo [Contoso Med App](https://github.com/Azure-Samples/communication-services-contoso-med-app)

## <a name="additional-reading"></a>Leituras adicionais

- [GitHub de Comunicação do Azure](https://github.com/Azure/communication) – Encontre mais exemplos e informações na página oficial do GitHub
- [Redux](https://redux.js.org/) – gerenciamento de estado do lado do cliente
- [FluentUI](https://aka.ms/fluent-ui) – biblioteca de interface do usuário da Microsoft
- [React](https://reactjs.org/) – biblioteca para criar interfaces do usuário
- [ASP.NET Core](/aspnet/core/introduction-to-aspnet-core?preserve-view=true&view=aspnetcore-3.1) – Estrutura para criar aplicativos Web