---
title: Arquivo de inclusão
description: Arquivo de inclusão
author: anthonychu
ms.service: signalr
ms.topic: include
ms.date: 03/04/2019
ms.author: antchu
ms.custom: include file
ms.openlocfilehash: 73d4d4e1a5f148dce6099b2d747ee1c290bcf7c1
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172293"
---
## <a name="run-the-web-application"></a>Executar o aplicativo Web

1. Há um aplicativo Web de página única de exemplo hospedado no GitHub para sua conveniência. Abra seu navegador em [https://azure-samples.github.io/signalr-service-quickstart-serverless-chat/demo/chat-v2/](https://azure-samples.github.io/signalr-service-quickstart-serverless-chat/demo/chat-v2/).

    > [!NOTE]
    > A origem do arquivo HTML está localizada em [/docs/demo/chat-v2/index.html](https://github.com/Azure-Samples/signalr-service-quickstart-serverless-chat/blob/master/docs/demo/chat-v2/index.html).

1. Quando for solicitada a URL base do aplicativo de funções, digite `http://localhost:7071`.

1. Insira um nome de usuário quando solicitado.

1. O aplicativo Web chama a função *GetSignalRInfo* o aplicativo de funções para recuperar as informações de conexão para se conectar ao Serviço Azure SignalR. Quando a conexão for concluída, a caixa de entrada de mensagem de chat será exibida.

1. Digite uma mensagem e pressione Enter. O aplicativo envia a mensagem para a função *SendMessage* no aplicativo Função do Azure, que usa a associação da saída do SignalR para difundir a mensagem para todos os clientes conectados. Se tudo estiver funcionando corretamente, a mensagem deverá aparecer no aplicativo.

    ![Executar o aplicativo](../media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-run-application.png)

1. Abra outra instância do aplicativo Web em outra janela do navegador. Você verá que todas as mensagens enviadas serão exibidas em todas as instâncias do aplicativo.

> [!IMPORTANT]
> Como a página HTML é atendida usando HTTPS, mas o tempo de execução local do Azure Functions está usando HTTP por padrão, seu navegador (como o Firefox) pode impor uma política de conteúdo misto que bloqueia as solicitações da página da web para suas funções. Para resolver isso, use um navegador que não tenha essa restrição ou inicie um servidor HTTP local, como [servidor http](https://www.npmjs.com/package/http-server) no diretório */docs/demo/chat-v2*. Verifique se a origem foi adicionada para a `CORS` configuração em *local.settings.json*.