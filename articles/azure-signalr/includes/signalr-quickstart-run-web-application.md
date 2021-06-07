---
title: incluir arquivo
description: incluir arquivo
author: anthonychu
ms.service: signalr
ms.topic: include
ms.date: 03/04/2019
ms.author: antchu
ms.custom: include file
ms.openlocfilehash: 2af9c1dfd13d3aeafa7cf1ac76537117ecc15aff
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "84317663"
---
## <a name="run-the-web-application"></a>Executar o aplicativo Web

1. Para simplificar o teste do cliente, abra o navegador para o nosso aplicativo Web de página única de amostra [https://azure-samples.github.io/signalr-service-quickstart-serverless-chat/demo/chat-v2/](https://azure-samples.github.io/signalr-service-quickstart-serverless-chat/demo/chat-v2/). 

    > [!NOTE]
    > A origem do arquivo HTML está localizada em [/docs/demo/chat-v2/index.html](https://github.com/Azure-Samples/signalr-service-quickstart-serverless-chat/blob/master/docs/demo/chat-v2/index.html). E, se você desejar hospedar o HTML por conta própria, inicie um servidor HTTP local, como [http-server](https://www.npmjs.com/package/http-server) no diretório */docs/demo/chat-v2*. Verifique se a origem foi adicionada para a configuração `CORS` em *local.settings.json* semelhante à amostra.
    > 
    > ```javascript
    > "Host": {
    >  "LocalHttpPort": 7071,
    >  "CORS": "http://localhost:8080,https://azure-samples.github.io",
    >  "CORSCredentials": true
    > }
    >
    > ```

1. Quando for solicitada a URL base do aplicativo de funções, digite `http://localhost:7071`.

1. Insira um nome de usuário quando solicitado.

1. O aplicativo Web chama a função *GetSignalRInfo* o aplicativo de funções para recuperar as informações de conexão para se conectar ao Serviço Azure SignalR. Quando a conexão for concluída, a caixa de entrada de mensagem de chat será exibida.

1. Digite uma mensagem e pressione Enter. O aplicativo envia a mensagem para a função *SendMessage* no aplicativo Função do Azure, que usa a associação da saída do SignalR para difundir a mensagem para todos os clientes conectados. Se tudo estiver funcionando corretamente, a mensagem deverá aparecer no aplicativo.

    ![Executar o aplicativo](../media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-run-application.png)

1. Abra outra instância do aplicativo Web em outra janela do navegador. Você verá que todas as mensagens enviadas serão exibidas em todas as instâncias do aplicativo.
