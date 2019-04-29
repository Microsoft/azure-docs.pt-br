---
title: 'Cenário: Disparar aplicativos lógicos com o Azure Functions e Barramento de Serviço do Azure | Microsoft Docs'
description: Crie funções que disparam aplicativos lógicos usando o Azure Functions e o Barramento de Serviço do Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: jehollan, klam, LADocs
ms.topic: article
ms.assetid: 19cbd921-7071-4221-ab86-b44d0fc0ecef
ms.date: 08/25/2018
ms.openlocfilehash: 1d3c4039ae823d3797e768af5892333d4d925268
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60995828"
---
# <a name="scenario-trigger-logic-apps-with-azure-functions-and-azure-service-bus"></a>Cenário: Disparar aplicativos lógicos com Azure Functions e barramento de serviço do Azure

Você pode usar as Azure Functions para criar um gatilho para um aplicativo lógico quando você precisa implantar um ouvinte ou uma tarefa de execução longa. Por exemplo, você pode criar uma função que escutaria em uma fila e acionaria imediatamente um aplicativo lógico como um gatilho de envio.

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscreva-se em uma conta gratuita do Azure</a>. 

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md) 

* Antes de criar uma função do Azure, [crie um aplicativo de funções](../azure-functions/functions-create-function-app-portal.md).

## <a name="create-logic-app"></a>Criar aplicativo lógico

Neste exemplo, você terá uma função em execução para cada aplicativo lógico que precisa ser disparado. Primeiro, crie um aplicativo lógico com um gatilho de solicitação HTTP. A função chama esse ponto de extremidade sempre que uma mensagem da fila é recebida.  

1. Entre no [portal do Azure](https://portal.azure.com) e crie um aplicativo lógico em branco. 

   Se você estiver familiarizado com aplicativos lógicos, examine [guia de início rápido: criar seu primeiro aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md).

1. Na caixa de pesquisa, digite “solicitação http”. Na lista de gatilhos, selecione este gatilho: **Quando uma solicitação HTTP é recebida**

   ![Selecionar gatilho](./media/logic-apps-scenario-function-sb-trigger/when-http-request-received-trigger.png)

1. Para o gatilho **Solicitação**, você pode, opcionalmente, inserir um esquema JSON para uso com a mensagem da fila. Os esquemas JSON ajudam o Designer de Aplicativo Lógico a entender a estrutura dos dados de entrada e facilitar a seleção das saídas em todo o fluxo de trabalho. 

   Para especificar um esquema, insira o esquema na caixa **Esquema JSON do Corpo da Solicitação**, por exemplo: 

   ![Especificar o esquema JSON](./media/logic-apps-scenario-function-sb-trigger/when-http-request-received-trigger-schema.png)

   Se você não tem um esquema, mas tem um conteúdo de exemplo no formato JSON, você pode gerar um esquema com base nesse conteúdo.

   1. No gatilho de Solicitação, escolha **Usar o conteúdo de exemplo para gerar o esquema**.

   1. Em **Inserir ou colar um conteúdo JSON de exemplo**, forneça o conteúdo de exemplo e, em seguida, escolha **Concluído**.
      
      ![Inserir o conteúdo de exemplo](./media/logic-apps-scenario-function-sb-trigger/enter-sample-payload.png)

   Este conteúdo de exemplo gera o seguinte esquema que aparece no gatilho:

   ```json
   {
      "type": "object",
      "properties": {
         "address": {
            "type": "object",
            "properties": {
               "number": {
                  "type": "integer"
               },
               "street": {
                  "type": "string"
               },
               "city": {
                  "type": "string"
               },
               "postalCode": {
                  "type": "integer"
               },
               "country": {
                  "type": "string"
               }
            }
         }
      }
   }
   ```

1. Adicione outras ações que deseja que aconteçam depois de receber a mensagem da fila. 

   Por exemplo, você pode enviar um email com o conector do Office 365 Outlook.

1. Salve seu aplicativo lógico, o qual gera a URL de retorno de chamada para o gatilho neste aplicativo lógico. Essa URL é exibida na propriedade **HTTP POST URL**.

   ![URL de retorno de chamada gerada para o gatilho](./media/logic-apps-scenario-function-sb-trigger/callback-URL-for-trigger.png)

## <a name="create-azure-function"></a>Criar função do Azure

Em seguida, crie a função que atuará como o gatilho e escutará a fila. 

1. No portal do Azure, abra e expanda seu aplicativo de funções, se ainda não estiver aberto. 

1. No nome do aplicativo de funções, expanda **Funções**. No painel **Funções**, escolha **Nova função**. Selecione esse modelo: **Gatilho de fila do barramento de serviço-C#**
   
   ![Portal de seleção do Azure Functions](./media/logic-apps-scenario-function-sb-trigger/newqueuetriggerfunction.png)

1. Forneça um nome para o gatilho e, em seguida, configure a conexão com a fila do Barramento de Serviço, que usa o ouvinte `OnMessageReceive()` do SDK do Barramento de Serviço do Azure.

1. Escreva uma função básica para chamar o ponto de extremidade do aplicativo lógico criado anteriormente usando a mensagem da fila como gatilho, por exemplo: 
   
   ```CSharp
   using System;
   using System.Threading.Tasks;
   using System.Net.Http;
   using System.Text;
   
   private static string logicAppUri = @"https://prod-05.westus.logic.azure.com:443/.........";
   
   // Re-use instance of http clients if possible - https://docs.microsoft.com/en-us/azure/azure-functions/manage-connections
   private static HttpClient httpClient = new HttpClient();
   
   public static void Run(string myQueueItem, TraceWriter log)
   {
       log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");

       var response = httpClient.PostAsync(logicAppUri, new StringContent(myQueueItem, Encoding.UTF8, "application/json")).Result;
   }
   ```

   Este exemplo usa o tipo de conteúdo de mensagem `application/json`, mas você pode alterar isso conforme o necessário.

1. Para testar a função, adicione uma mensagem da fila usando uma ferramenta como o [Gerenciador do Barramento de Serviço](https://github.com/paolosalvatori/ServiceBusExplorer). 

   O aplicativo lógico dispara imediatamente após a função receber a mensagem.

## <a name="get-support"></a>Obter suporte

* Em caso de dúvidas, visite o [Fórum dos Aplicativos Lógicos do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para enviar ou votar em ideias de recurso, visite o [site de comentários do usuário de Aplicativos Lógicos](https://aka.ms/logicapps-wish).

