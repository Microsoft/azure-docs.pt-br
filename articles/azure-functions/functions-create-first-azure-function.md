---
title: "Criar sua primeira função no Portal do Azure | Microsoft Docs"
description: "Aprenda a criar sua primeira Função do Azure para a execução sem servidor usando o Portal do Azure."
services: functions
documentationcenter: na
author: ggailey777
manager: erikre
editor: 
tags: 
ms.assetid: 96cf87b9-8db6-41a8-863a-abb828e3d06d
ms.service: functions
ms.devlang: multiple
ms.topic: hero-article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 06/08/2017
ms.author: glenga
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 245ce9261332a3d36a36968f7c9dbc4611a019b2
ms.openlocfilehash: f00ca3b8a35c0c49277457bd42fe8a314520d5a5
ms.contentlocale: pt-br
ms.lasthandoff: 06/09/2017


---
# <a name="create-your-first-function-in-the-azure-portal"></a>Criar sua primeira função no portal do Azure

O Azure Functions lhe permite executar seu código em um ambiente serverless sem que seja preciso primeiro criar uma VM ou publicar um aplicativo Web. Neste tópico, aprenda a usar o Functions para criar uma função "Olá, Mundo" no Portal do Azure.

![Criar um aplicativo de funções no portal do Azure](./media/functions-create-first-azure-function/function-app-in-portal-editor.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="log-in-to-azure"></a>Fazer logon no Azure

Faça logon no [Portal do Azure](https://portal.azure.com/).

## <a name="create-a-function-app"></a>Criar um aplicativo de funções

Você deve ter um aplicativo de funções para hospedar a execução de suas funções. Um aplicativo de funções permite a você agrupar funções como uma unidade lógica para facilitar o gerenciamento, implantação e compartilhamento de recursos. 

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

![Aplicativo de funções criado com êxito.](./media/functions-create-first-azure-function/function-app-create-success.png)

[!INCLUDE [functions-portal-favorite-function-apps](../../includes/functions-portal-favorite-function-apps.md)]

Em seguida, crie uma nova função no novo aplicativo de funções.

## <a name="create-function"></a>Criar uma função disparada por HTTP

1. Expanda seu novo aplicativo de funções, depois clique no botão **+** ao lado de **Functions**.

2.  Na página **Início rápido**, selecione **WebHook + API**, escolha uma linguagem de programação para a função e clique em **Criar esta função**. 
   
    ![Início rápido de funções no Portal do Azure.](./media/functions-create-first-azure-function/function-app-quickstart-node-webhook.png)

Uma função é criada na linguagem escolhida por você usando o modelo de uma função disparada por HTTP. Você pode executar a nova função enviando uma solicitação HTTP.

## <a name="test-the-function"></a>Testar a função

1. Em sua nova função, clique em **</> Obter URL da função**, selecione **padrão (Tecla de função)** e clique em **Copiar**. 

    ![Copiar a URL da função do Portal do Azure](./media/functions-create-first-azure-function/function-app-develop-tab-testing.png)

2. Cole a URL para a solicitação HTTP na barra de endereços do navegador. Acrescente o valor de cadeia de consulta `&name=<yourname>` a essa URL e execute a solicitação. O exemplo a seguir mostra a resposta no navegador à solicitação GET retornada pela função:

    ![Resposta da função no navegador.](./media/functions-create-first-azure-function/function-app-browser-testing.png)

    A URL da solicitação inclui uma chave que é necessária, por padrão, para acessar sua função via HTTP.   

## <a name="view-the-function-logs"></a>Exibir os logs da função 

Quando a função é executada, informações de rastreamento são gravadas nos logs. Para ver a saída do rastreamento da execução anterior, volte para sua função no portal e clique na seta para cima na parte inferior da tela para expandir **Logs**. 

![Visualizador de log de função no Portal do Azure.](./media/functions-create-first-azure-function/function-view-logs.png)

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [Clean up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Próximas etapas

Você criou um aplicativo de funções com uma função simples disparada por HTTP.  

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

Para obter mais informações, consulte [Associações HTTP e de webhook do Azure Functions](functions-bindings-http-webhook.md).




