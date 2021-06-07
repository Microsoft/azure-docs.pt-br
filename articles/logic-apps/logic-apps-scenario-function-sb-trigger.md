---
title: Chamar aplicativos lógicos com o Azure Functions
description: Chamar ou disparar aplicativos lógicos usando o Azure Functions e o barramento de serviço do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: jehollan, klam, logicappspm
ms.topic: article
ms.date: 11/08/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: a7df9ba1318f40de8af392cfaedbe51d7a5df755
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98784925"
---
# <a name="call-or-trigger-logic-apps-by-using-azure-functions-and-azure-service-bus"></a>Chamar ou disparar aplicativos lógicos usando o Azure Functions e o barramento de serviço do Azure

Você pode usar [Azure Functions](../azure-functions/functions-overview.md) para disparar um aplicativo lógico quando precisar implantar um ouvinte ou uma tarefa de execução longa. Por exemplo, você pode criar uma função que escuta em uma fila [do barramento de serviço do Azure](../service-bus-messaging/service-bus-messaging-overview.md) e imediatamente aciona um aplicativo lógico como um gatilho de envio por push.

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* Um namespace do barramento de serviço do Azure. Se você não tiver um namespace, [primeiro crie seu namespace](../service-bus-messaging/service-bus-create-namespace-portal.md).

* Um aplicativo de funções, que é um contêiner para suas funções. Se você não tiver um aplicativo de funções, [crie seu aplicativo de funções primeiro](../azure-functions/functions-get-started.md)e certifique-se de selecionar .net como a pilha de tempo de execução.

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md)

## <a name="create-logic-app"></a>Criar aplicativo lógico

Para esse cenário, você tem uma função executando cada aplicativo lógico que deseja disparar. Primeiro, crie um aplicativo lógico que comece com um gatilho de solicitação HTTP. A função chama esse ponto de extremidade sempre que uma mensagem da fila é recebida.

1. Entre no [portal do Azure](https://portal.azure.com) e crie um aplicativo lógico em branco.

   Se ainda não estiver familiarizado com aplicativos lógicos, veja o [Guia de Início Rápido: criar seu primeiro aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md).

1. Na caixa de pesquisa, insira `http request`. Na lista de gatilhos, selecione o gatilho **quando uma solicitação HTTP é recebida** .

   ![Selecionar gatilho](./media/logic-apps-scenario-function-sb-trigger/when-http-request-received-trigger.png)

   Com o gatilho de solicitação, você pode opcionalmente inserir um esquema JSON a ser usado com a mensagem da fila. Os esquemas JSON ajudam o designer de aplicativo lógico a entender a estrutura dos dados de entrada e tornar as saídas mais fáceis de usar em seu fluxo de trabalho.

1. Para especificar um esquema, insira o esquema na caixa **Esquema JSON do Corpo da Solicitação**, por exemplo:

   ![Especificar o esquema JSON](./media/logic-apps-scenario-function-sb-trigger/when-http-request-received-trigger-schema.png)

   Se você não tem um esquema, mas tem um conteúdo de exemplo no formato JSON, você pode gerar um esquema com base nesse conteúdo.

   1. No gatilho de Solicitação, selecione **Usar o conteúdo de amostra para gerar o esquema**.

   1. Em **Inserir ou colar um exemplo de carga JSON**, insira seu conteúdo de exemplo e, em seguida, selecione **concluído**.

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

1. Adicione outras ações que você deseja executar depois de receber a mensagem da fila.

   Por exemplo, você pode enviar um email com o conector do Office 365 Outlook.

1. Salve seu aplicativo lógico, o qual gera a URL de retorno de chamada para o gatilho neste aplicativo lógico. Posteriormente, você usará essa URL de retorno de chamada no código para o gatilho de fila do barramento de serviço do Azure.

   A URL de retorno de chamada aparece na propriedade **URL http post** .

   ![URL de retorno de chamada gerada para o gatilho](./media/logic-apps-scenario-function-sb-trigger/callback-URL-for-trigger.png)

## <a name="create-a-function"></a>Criar uma função

Em seguida, crie a função que atuará como o gatilho e escutará a fila.

1. No portal do Azure, abra e expanda seu aplicativo de funções, se ainda não estiver aberto. 

1. No nome do aplicativo de funções, expanda **Funções**. No painel **funções** , selecione **nova função**.

   ![Expanda "funções" e selecione "nova função"](./media/logic-apps-scenario-function-sb-trigger/add-new-function-to-function-app.png)

1. Selecione este modelo com base em se você criou um novo aplicativo de funções no qual selecionou o .NET como a pilha de tempo de execução ou está usando um aplicativo de funções existente.

   * Para novos aplicativos de funções, selecione este modelo: **gatilho de fila do barramento de serviço**

     ![Selecionar modelo para o novo aplicativo de funções](./media/logic-apps-scenario-function-sb-trigger/current-add-queue-trigger-template.png)

   * Para um aplicativo de funções existente, selecione este modelo: **gatilho de fila do barramento de serviço-C#**

     ![Selecionar modelo para o aplicativo de funções existente](./media/logic-apps-scenario-function-sb-trigger/legacy-add-queue-trigger-template.png)

1. No painel **gatilho de fila do barramento de serviço do Azure** , forneça um nome para o gatilho e configure a **conexão do barramento de serviço** para a fila, que usa o ouvinte do SDK do barramento de serviço do Azure `OnMessageReceive()` e selecione **criar**.

1. Escreva uma função básica para chamar o ponto de extremidade do aplicativo lógico criado anteriormente usando a mensagem da fila como um gatilho. Antes de escrever sua função, examine estas considerações:

   * Este exemplo usa o tipo de conteúdo de mensagem `application/json`, mas você pode alterar isso conforme o necessário.
   
   * Devido a possíveis funções em execução simultânea, grandes volumes ou cargas pesadas, evite instanciar a [classe HttpClient](/dotnet/api/system.net.http.httpclient) com a `using` instrução e criar diretamente instâncias de HttpClient por solicitação. Para obter mais informações, consulte [usar o HttpClientFactory para implementar solicitações HTTP resilientes](/dotnet/architecture/microservices/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests#issues-with-the-original-httpclient-class-available-in-net-core).
   
   * Se possível, reutilize a instância de clientes HTTP. Para obter mais informações, consulte [Manage Connections in Azure Functions](../azure-functions/manage-connections.md).

   Este exemplo usa o [ `Task.Run` método](/dotnet/api/system.threading.tasks.task.run) no modo [assíncrono](/dotnet/csharp/language-reference/keywords/async) . Para obter mais informações, consulte [programação assíncrona com Async e Await](/dotnet/csharp/programming-guide/concepts/async/).

   ```csharp
   using System;
   using System.Threading.Tasks;
   using System.Net.Http;
   using System.Text;

   // Can also fetch from App Settings or environment variable
   private static string logicAppUri = @"https://prod-05.westus.logic.azure.com:443/workflows/<remaining-callback-URL>";

   // Reuse the instance of HTTP clients if possible: https://docs.microsoft.com/azure/azure-functions/manage-connections
   private static HttpClient httpClient = new HttpClient();

   public static async Task Run(string myQueueItem, TraceWriter log) 
   {
      log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
      var response = await httpClient.PostAsync(logicAppUri, new StringContent(myQueueItem, Encoding.UTF8, "application/json")); 
   }
   ```

1. Para testar a função, adicione uma mensagem da fila usando uma ferramenta como o [Gerenciador do Barramento de Serviço](https://github.com/paolosalvatori/ServiceBusExplorer).

   O aplicativo lógico dispara imediatamente após a função receber a mensagem.

## <a name="next-steps"></a>Próximas etapas

* [Chamar, disparar ou aninhar fluxos de trabalho usando pontos de extremidade HTTP](../logic-apps/logic-apps-http-endpoint.md)