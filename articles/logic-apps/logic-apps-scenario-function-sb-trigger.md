---
title: "Cenário: Disparar aplicativos lógicos com o Azure Functions e Barramento de Serviço do Azure | Microsoft Docs"
description: "Crie uma função para disparar um aplicativo lógico usando o Azure Functions e o Barramento de Serviço do Azure"
services: logic-apps,functions
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: anneta
editor: 
ms.assetid: 19cbd921-7071-4221-ab86-b44d0fc0ecef
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 05/23/2016
ms.author: LADocs; jehollan
ms.openlocfilehash: 088f10bc32dd492f82f0a10a7e5829e76f588758
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="scenario-trigger-a-logic-app-with-azure-functions-and-azure-service-bus"></a>Cenário: Disparar um aplicativo lógico usando o Azure Functions e o Barramento de Serviço do Azure

Você pode usar as Azure Functions para criar um gatilho para um aplicativo lógico quando você precisa implantar um ouvinte ou uma tarefa de execução longa. Por exemplo, você pode criar uma função que escutaria em uma fila e acionaria imediatamente um aplicativo lógico como um gatilho de envio.

## <a name="build-the-logic-app"></a>Compilar o aplicativo lógico
Neste exemplo, você terá uma função em execução para cada aplicativo lógico que precisa ser disparado. Primeiro, crie um aplicativo lógico com um gatilho de solicitação HTTP. A função chama esse ponto de extremidade sempre que uma mensagem da fila é recebida.  

1. Crie um aplicativo lógico.
2. Selecione o gatilho **Manual – Quando uma solicitação HTTP é recebida**.
   Opcionalmente, você pode especificar um esquema JSON para usar com a mensagem da fila usando uma ferramenta como [jsonschema.net](http://jsonschema.net). Cole o esquema no gatilho. Os esquemas ajudam o designer a entender a forma dos dados e movimentar as propriedades com mais facilidade pelo fluxo de trabalho.
2. Adicione as etapas adicionais que você deseja que ocorram após uma mensagem da fila ser recebida. Por exemplo, envie um email por meio do Office 365.  
3. Salve o aplicativo lógico para gerar a URL de retorno de chamada do gatilho para esse aplicativo lógico. A URL aparecerá no cartão do gatilho.

![A URL de retorno de chamada aparece no cartão do gatilho][1]

## <a name="build-the-function"></a>Compilar a função
Em seguida, crie uma função que atuará como o gatilho e ouvirá a fila.

1. Abra o [Portal do Azure Functions](https://functions.azure.com/signin), escolha **Nova Função** e selecione o modelo **ServiceBusQueueTrigger - C#**.
   
    ![portal das Azure Functions][2]
2. Configure a conexão com a fila do Barramento de Serviço, que usará o ouvinte de `OnMessageReceive()` do SDK do Barramento de Serviço.
3. Grave uma função básica para chamar o ponto de extremidade do aplicativo lógico (criado anteriormente) usando a mensagem da fila como gatilho. Aqui está um exemplo completo de uma função. O exemplo usa um tipo de conteúdo de mensagem `application/json`, mas você pode alterar isso conforme o necessário.
   
   ```
   using System;
   using System.Threading.Tasks;
   using System.Net.Http;
   using System.Text;
   
   private static string logicAppUri = @"https://prod-05.westus.logic.azure.com:443/.........";
   
   public static void Run(string myQueueItem, TraceWriter log)
   {
   
       log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
       using (var client = new HttpClient())
       {
           var response = client.PostAsync(logicAppUri, new StringContent(myQueueItem, Encoding.UTF8, "application/json")).Result;
       }
   }
   ```

Para testar, adicionar uma mensagem na fila por meio de uma ferramenta como o [Explorer do Barramento de Serviço](https://github.com/paolosalvatori/ServiceBusExplorer). Veja o aplicativo lógico disparar imediatamente após a função receber a mensagem.

<!-- Image References -->
[1]: ./media/logic-apps-scenario-function-sb-trigger/manualtrigger.png
[2]: ./media/logic-apps-scenario-function-sb-trigger/newqueuetriggerfunction.png
