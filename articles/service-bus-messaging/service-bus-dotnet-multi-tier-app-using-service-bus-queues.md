---
title: "Aplicativo multicamadas .NET usando o Barramento de Serviço do Azure | Microsoft Docs"
description: "Um tutorial .NET que ajuda você a desenvolver um aplicativo de várias camadas no Azure que usa filas do barramento de serviço para se comunicar entre camadas."
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 1b8608ca-aa5a-4700-b400-54d65b02615c
ms.service: service-bus-messaging
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: get-started-article
ms.date: 04/11/2017
ms.author: sethm
ms.openlocfilehash: 8b502f5ac5d89801d390a872e7a8b06e094ecbba
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="net-multi-tier-application-using-azure-service-bus-queues"></a>Aplicativo multicamadas .NET usando filas do Barramento de Serviço do Azure
## <a name="introduction"></a>Introdução
O desenvolvimento para o Microsoft Azure é fácil usando o Visual Studio e o SDK do Azure gratuito para o .NET. Este tutorial orienta você nas etapas para criar um aplicativo que usa vários recursos do Azure em execução no seu ambiente local.

Você aprenderá o seguinte:

* Habilitar o computador para o desenvolvimento do Azure com um único download e instalar.
* Usar o Visual Studio para desenvolver para o Azure.
* Criar um aplicativo multicamadas no Azure usando funções web e de trabalho.
* Como comunicar-se entre camadas usando filas do Barramento de Serviço.

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

Neste tutorial você compilará e executará o aplicativo de multicamadas em um serviço de nuvem do Microsoft Azure. O front-end é uma função Web MVC do ASP.NET e o back-end é uma função de trabalho que usa uma fila do Barramento de Serviço. Você pode criar o mesmo aplicativo multicamadas com o front-end que o de um projeto Web, que é implantado em um site do Azure em vez de em um serviço de nuvem. Você também pode experimentar o tutorial [Aplicativo .NET híbrido local/na nuvem](../service-bus-relay/service-bus-dotnet-hybrid-app-using-service-bus-relay.md).

A captura de tela a seguir mostra o aplicativo concluído.

![][0]

## <a name="scenario-overview-inter-role-communication"></a>Visão geral de cenário: comunicação interfunções
Para enviar um pedido para processamento, o componente de UI de front-end, executando a função web, é necessário interagir com a lógica de camada intermediária em execução na função de trabalho. Este exemplo usa a mensagem do Barramento de Serviço para a comunicação entre as camadas.

Usar a mensagem do Barramento de Serviço entre a Web e as camadas intermediárias separa os dois componentes. Ao contrário das mensagens diretas (isto é, TCP ou HTTP), a camada da Web não se conecta com a camada intermediária diretamente; em vez disso, envia unidades de trabalho, como mensagens, para o Barramento de Serviço, que mantém confiável até a camada intermediária estar pronta para os consumir e processar.

O Barramento de Serviço fornece duas entidades para dar suporte ao sistema de mensagens agenciado: filas e tópicos. Com filas, cada mensagem enviada para a fila é consumida por um único destinatário. Os tópicos dão suporte ao padrão de publicação/assinatura em que cada mensagem publicada é disponibilizada para uma assinatura registrada com o tópico. Cada assinatura mantém logicamente sua própria fila de mensagens. As assinaturas também podem ser configuradas com as regras de filtro que restringem o conjunto de mensagens passado para a fila de assinatura para aquelas que correspondem ao filtro. O exemplo a seguir usa filas do barramento de serviço.

![][1]

Esse mecanismo de comunicação oferece diversas vantagens sobre mensagens diretas:

* **Desacoplamento temporal.** Com o padrão de mensagens assíncrono, os produtores e consumidores não precisam estar online ao mesmo tempo. O ServiceBus armazena de forma confiável as mensagens até que a parte de consumo esteja prontapara recebê-las. Isso permite que os componentes do aplicativo distribuído sejam desconectados voluntariamente, por exemplo, para manutenção ou devido a uma falha de componente, sem afetar o sistema como um todo. Além disso, o aplicativo de consumo só precisa ser colocado online durante determinadas horas do dia.
* **Nivelamento de carga.** Em muitos aplicativos, a carga do sistema varia ao longo do tempo enquanto o tempo de processamento necessário para cada unidade de trabalho for normalmente constante. Intermediar produtores de mensagem e consumidorescom uma fila significa que o aplicativo de consumo (o função de trabalho) sóprecisa ser configurado para acomodar a carga média em vez de pico decarga. A profundidade da fila aumentará e diminuirá conforme a carga de entrada variar. Isso economiza dinheiro diretamente em termos da quantidade deinfraestrutura necessária para atender à carga do aplicativo.
* **Balanceamento de carga.** Conforme a carga aumenta, mais processos de função de trabalho podem seradicionados à leitura da fila. Cada mensagem é processada por apenas umdos processos de trabalho. Além disso, esse balanceamento de carga com base em recepção permite uma utilização ideal das máquinas de trabalho mesmo se as máquinas de trabalho diferem em termos de capacidade de processamento, pois elas irão receber mensagens em sua própria taxa máxima. Esse padrão geralmente é chamado de padrão de *consumidor concorrente*.
  
  ![][2]

As seções a seguir discutem o código que implementa essa arquitetura.

## <a name="set-up-the-development-environment"></a>Configurar o ambiente de desenvolvimento
Antes começar a desenvolver os aplicativos do Azure, obtenha as ferramentas e configure seu ambiente de desenvolvimento.

1. Instale o Azure SDK para .NET da [página de downloads](https://azure.microsoft.com/downloads/) do SDK.
2. Na coluna **.NET**, clique na versão do [Visual Studio](http://www.visualstudio.com) que você está usando. As etapas neste tutorial usam o Visual Studio 2015, mas também funcionam com o Visual Studio 2017.
3. Quando for solicitado a executar ou salvar o instalador, clique em **Executar**.
4. No **Web Platform Installer**, clique em **Instalar** e prossiga com a instalação.
5. Quando a instalação estiver concluída, você terá tudo o que é necessário para iniciar o desenvolvimento do aplicativo. O SDK inclui ferramentas que permitem que você desenvolva facilmente aplicativos do Azure no Visual Studio.

## <a name="create-a-namespace"></a>Criar um namespace
A próxima etapa é para criar um namespace de serviço e obter uma chave de Assinatura de Acesso Compartilhado (SAS). Um namespace fornece um limite de aplicativo para cada aplicativo exposto por meio do Barramento de Serviço. A chave SAS é gerada pelo sistema quando um namespace de serviço é criado. A combinação do namespace e a chave SAS fornece as credenciais para o Barramento de Serviço autenticar o acesso a um aplicativo.

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="create-a-web-role"></a>Criar uma função Web
Nesta seção, você compila o front-end de seu aplicativo. Primeiro, você cria as páginas que seu aplicativo exibe.
Depois, adiciona o código que envia os itens para uma fila do Barramento de Serviço e exibe as informações de status sobre a fila.

### <a name="create-the-project"></a>Criar o projeto
1. Usando os privilégios de administrador, inicie o Visual Studio: clique com o botão direito no ícone do programa do **Visual Studio**, em seguida, clique em **Executar como administrador**. O emulador de computação do Azure, discutido mais adiante neste artigo, exige iniciar o Visual Studio com privilégios de administrador.
   
   No Visual Studio, no menu **Arquivo**, clique em **Novo** e clique em **Projeto**.
2. Em **Modelos Instalados**, em **Visual C#**, clique em **Nuvem** e em **Serviço de Nuvem do Azure**. Nomeie o projeto como **AppVáriasCamadas**. Em seguida, clique em **OK**.
   
   ![][9]
3. Em funções do **.NET Framework 4.5**, clique duas vezes em **Função Web do ASP.NET**.
   
   ![][10]
4. Focalize **WebRole1** em solução do **Serviço de Nuvem do Azure**, clique no ícone de lápis e renomeie a função web para **FrontendWebRole**. Em seguida, clique em **OK**. (Certifique-se de inserir "Frontend" com "e" minúsculo, não "FrontEnd".)
   
   ![][11]
5. Na caixa de diálogo **Novo Projeto ASP.NET** na lista **Selecionar um modelo**, clique em **MVC**.
   
   ![][12]
6. Ainda na caixa de diálogo **Novo projeto ASP.NET**, clique no botão **Alterar Autenticação**. Na caixa de diálogo **Alterar Autenticação**, clique em **Sem Autenticação** e clique em **OK**. Para este tutorial, você está implantando um aplicativo que não precisa de um logon de usuário.
   
    ![][16]
7. De novo na caixa de diálogo **Novo Projeto ASP .NET**, clique em **OK** para criar o projeto.
8. No **Gerenciador de Soluções**, no projeto **FrontendWebRole**, clique com botão direito do mouse em **Referências** e clique em **Gerenciar Pacotes NuGet**.
9. Clique na guia **Procurar** e procure `Microsoft Azure Service Bus`. Selecione o pacote **WindowsAzure.ServiceBus**, clique em **Instalar** e aceite os termos de uso.
   
   ![][13]
   
   Os assemblies de cliente obrigatórios agora são referenciados e alguns arquivos de código novos foram adicionados.
10. Em **Gerenciador de Soluções**, clique com o botão direito do mouse em **Modelos**, **Adicionar** e **Classe**. Na caixa **Nome**, digite o nome **OnlineOrder.cs**. Clique em **Adicionar**.

### <a name="write-the-code-for-your-web-role"></a>Escreva o código para a função Web
Nesta seção, você cria várias páginas que exibem seu aplicativo.

1. No arquivo OnlineOrder.cs do Visual Studio, substitua a definição do namespace existente pelo seguinte código:
   
   ```csharp
   namespace FrontendWebRole.Models
   {
       public class OnlineOrder
       {
           public string Customer { get; set; }
           public string Product { get; set; }
       }
   }
   ```
2. No **Gerenciador de Soluções**, clique duas vezes em **Controllers\HomeController.cs**. Adicione as seguintes instruções **using** na parte superior do arquivo para incluir os namespaces do modelo que você acabou de criar, bem como o Barramento de Serviço.
   
   ```csharp
   using FrontendWebRole.Models;
   using Microsoft.ServiceBus.Messaging;
   using Microsoft.ServiceBus;
   ```
3. Também no arquivo HomeController.cs do Visual Studio, substitua a definição do namespace existente pelo código a seguir. Esse código contém métodos para processar o envio de itens para a fila.
   
   ```csharp
   namespace FrontendWebRole.Controllers
   {
       public class HomeController : Controller
       {
           public ActionResult Index()
           {
               // Simply redirect to Submit, since Submit will serve as the
               // front page of this application.
               return RedirectToAction("Submit");
           }
   
           public ActionResult About()
           {
               return View();
           }
   
           // GET: /Home/Submit.
           // Controller method for a view you will create for the submission
           // form.
           public ActionResult Submit()
           {
               // Will put code for displaying queue message count here.
   
               return View();
           }
   
           // POST: /Home/Submit.
           // Controller method for handling submissions from the submission
           // form.
           [HttpPost]
           // Attribute to help prevent cross-site scripting attacks and
           // cross-site request forgery.  
           [ValidateAntiForgeryToken]
           public ActionResult Submit(OnlineOrder order)
           {
               if (ModelState.IsValid)
               {
                   // Will put code for submitting to queue here.
   
                   return RedirectToAction("Submit");
               }
               else
               {
                   return View(order);
               }
           }
       }
   }
   ```
4. No menu **Compilar**, clique em **Compilar Solução** para testar a precisão de seu trabalho até o momento.
5. Agora, crie a exibição do método `Submit()` criado anteriormente. Clique com o botão direito no método `Submit()` (a sobrecarga de `Submit()` que não usa nenhum parâmetro), em seguida, escolha **Adicionar Exibição**.
   
   ![][14]
6. É exibida uma caixa de diálogo para criar a exibição. Na lista **Modelo**, escolha **Criar**. Na lista **Classe do modelo**, clique na classe **OnlineOrder**.
   
   ![][15]
7. Clique em **Adicionar**.
8. Agora, você alterará o nome exibido de seu aplicativo. No **Gerenciador de Soluções**, clique duas vezes no arquivo **Views\Shared\\_Layout.cshtml** para abrir no editor do Visual Studio.
9. Substitua todas as ocorrências de **Meu Aplicativo ASP.NET** para **Produtos da LITWARE**.
10. Remova os links **Página Inicial**, **Sobre** e **Contato**. Exclua o código destacado:
    
    ![][28]
11. Por fim, modifique a página de envio para incluir algumas informações sobre a fila. No **Gerenciador de Soluções**, clique duas vezes no arquivo **Views\Home\Submit.cshtml** para abri-lo no editor do Visual Studio. Adicione a seguinte linha após `<h2>Submit</h2>`. Por enquanto, `ViewBag.MessageCount` está vazio. Você irá preenchê-lo mais tarde.
    
    ```html
    <p>Current number of orders in queue waiting to be processed: @ViewBag.MessageCount</p>
    ```
12. Você agora implementou a interface do usuário. Você pode pressionar **F5** para executar o aplicativo e confirmar que parece conforme o esperado.
    
    ![][17]

### <a name="write-the-code-for-submitting-items-to-a-service-bus-queue"></a>Escreva o código para enviar itens para uma fila do Brramento de Serviço
Agora, adicione o código para enviar itens para uma fila. Primeiro, você cria uma classe que contém as informações de conexão de fila do Barramento de Serviço. Em seguida, você inicializa a conexão do Global.aspx.cs. Por fim, você atualiza o código de envio criado anteriormente em HomeController.cs para efetivamente enviar itens para uma fila do Barramento de Serviço.

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto **FrontendWebRole** (clique com o botão direito no projeto, e não na função). Clique em **Adicionar** e depois em **Classe**.
2. Nomeie a classe **QueueConnector.cs**. Clique em **Adicionar** para criar a classe.
3. Agora, você adiciona o código que encapsula as informações da conexão e inicializa a conexão em uma fila do Barramento de Serviço. Substitua todo o conteúdo de QueueConnector.cs pelo código a seguir e insira valores para `your Service Bus namespace` (nome do namespace) e `yourKey`, que é a **chave primária** obtida anteriormente no portal do Azure.
   
   ```csharp
   using System;
   using System.Collections.Generic;
   using System.Linq;
   using System.Web;
   using Microsoft.ServiceBus.Messaging;
   using Microsoft.ServiceBus;
   
   namespace FrontendWebRole
   {
       public static class QueueConnector
       {
           // Thread-safe. Recommended that you cache rather than recreating it
           // on every request.
           public static QueueClient OrdersQueueClient;
   
           // Obtain these values from the portal.
           public const string Namespace = "your Service Bus namespace";
   
           // The name of your queue.
           public const string QueueName = "OrdersQueue";
   
           public static NamespaceManager CreateNamespaceManager()
           {
               // Create the namespace manager which gives you access to
               // management operations.
               var uri = ServiceBusEnvironment.CreateServiceUri(
                   "sb", Namespace, String.Empty);
               var tP = TokenProvider.CreateSharedAccessSignatureTokenProvider(
                   "RootManageSharedAccessKey", "yourKey");
               return new NamespaceManager(uri, tP);
           }
   
           public static void Initialize()
           {
               // Using Http to be friendly with outbound firewalls.
               ServiceBusEnvironment.SystemConnectivity.Mode =
                   ConnectivityMode.Http;
   
               // Create the namespace manager which gives you access to
               // management operations.
               var namespaceManager = CreateNamespaceManager();
   
               // Create the queue if it does not exist already.
               if (!namespaceManager.QueueExists(QueueName))
               {
                   namespaceManager.CreateQueue(QueueName);
               }
   
               // Get a client to the queue.
               var messagingFactory = MessagingFactory.Create(
                   namespaceManager.Address,
                   namespaceManager.Settings.TokenProvider);
               OrdersQueueClient = messagingFactory.CreateQueueClient(
                   "OrdersQueue");
           }
       }
   }
   ```
4. Agora, garanta que o método **Initialize** seja chamado. No **Gerenciador de Soluções**, clique duas vezes em **Global.asax\Global.asax.cs**.
5. Adicione a linha de código a seguir ao fim do método **Application_Start**.
   
   ```csharp
   FrontendWebRole.QueueConnector.Initialize();
   ```
6. Por fim, atualize o código da Web criado anteriormente, para enviar itens para a fila. No **Gerenciador de Soluções**, clique duas vezes em **Controllers\HomeController.cs**.
7. Atualize o método `Submit()` (a sobrecarga sem parâmetros) da seguinte maneira para obter a contagem de mensagens da fila.
   
   ```csharp
   public ActionResult Submit()
   {
       // Get a NamespaceManager which allows you to perform management and
       // diagnostic operations on your Service Bus queues.
       var namespaceManager = QueueConnector.CreateNamespaceManager();
   
       // Get the queue, and obtain the message count.
       var queue = namespaceManager.GetQueue(QueueConnector.QueueName);
       ViewBag.MessageCount = queue.MessageCount;
   
       return View();
   }
   ```
8. Atualize o método `Submit(OnlineOrder order)` (a sobrecarga com um parâmetro) da seguinte maneira para enviar as informações do pedido para a fila.
   
   ```csharp
   public ActionResult Submit(OnlineOrder order)
   {
       if (ModelState.IsValid)
       {
           // Create a message from the order.
           var message = new BrokeredMessage(order);
   
           // Submit the order.
           QueueConnector.OrdersQueueClient.Send(message);
           return RedirectToAction("Submit");
       }
       else
       {
           return View(order);
       }
   }
   ```
9. Agora você pode executar o aplicativo novamente. Cada vez que você enviar umpedido, o número de mensagens aumenta.
   
   ![][18]

## <a name="create-the-worker-role"></a>Criar a função de trabalho
Agora você criará a função de trabalho que processa o envio de pedidos. Este exemplo usa o modelo de projeto do Visual Studio **Função de Trabalho com Fila do Barramento de Serviço**. Você já obteve as credenciais necessárias no portal.

1. Certifique-se de ter conectado o Visual Studio à sua conta do Azure.
2. No Visual Studio, no **Gerenciador de Soluções**, clique com o botão direito na pasta **Funções** no projeto **AppVáriasCamadas**.
3. Clique em **Adicionar** e clique em **Novo Projeto da Função de Trabalho**. A caixa de diálogo **Adicionar Novo Projeto da Função** é exibida.
   
   ![][26]
4. Na caixa de diálogo **Adicionar Novo Projeto** de Função, clique em **Função de Trabalho com Fila do Barramento de Serviço**.
   
   ![][23]
5. Na caixa **Nome**, nomeie o projeto **OrderProcessingRole**. Clique em **Adicionar**.
6. Copie a cadeia de conexão que você obteve na etapa 9 da seção "Criar um namespace do Barramento de Serviço" para a área de transferência.
7. No **Gerenciador de Soluções**, clique com botão direito do mouse em **OrderProcessingRole** criado na etapa 5 (não se esqueça de clicar com o botão direito do mouse em **OrderProcessingRole** em **Funções**, e não na classe). Clique em **Propriedades**.
8. Na guia **Configurações** da caixa de diálogo **Propriedades**, clique dentro da caixa **Valor** para **Microsoft.ServiceBus.ConnectionString** e cole o valor do ponto de extremidade que você copiou na etapa 6.
   
   ![][25]
9. Crie uma classe **OnlineOrder** para representar os pedidos conforme você os processa na fila. Você pode reutilizar uma classe já criada. No **Gerenciador de Soluções**, clique com o botão direito na classe **OrderProcessingRole** (clique com o botão direito no ícone da classe, não na função). Clique em **Adicionar** e clique em **Item Existente**.
10. Navegue até a subpasta para **FrontendWebRole\Models** e clique duas vezes em **OnlineOrder.cs** para adicioná-la a esse projeto.
11. Em **WorkerRole.cs**, altere o valor da variável **QueueName** de `"ProcessingQueue"` para `"OrdersQueue"`, como mostrado no código a seguir.
    
    ```csharp
    // The name of your queue.
    const string QueueName = "OrdersQueue";
    ```
12. Adicione a seguinte instrução using na parte superior do arquivo WorkerRole.cs.
    
    ```csharp
    using FrontendWebRole.Models;
    ```
13. Na função `Run()`, dentro da chamada `OnMessage()`, substitua o conteúdo da cláusula `try` pelo código a seguir.
    
    ```csharp
    Trace.WriteLine("Processing", receivedMessage.SequenceNumber.ToString());
    // View the message as an OnlineOrder.
    OnlineOrder order = receivedMessage.GetBody<OnlineOrder>();
    Trace.WriteLine(order.Customer + ": " + order.Product, "ProcessingMessage");
    receivedMessage.Complete();
    ```
14. Você concluiu o aplicativo. Você pode testar o aplicativo completo clicando com o botão direito do mouse no projeto AppVáriasCamadas no Gerenciador de Soluções, selecionando **Definir como Projeto de Inicialização** e pressionando F5. Observe que o número de mensagens não é incrementado porque a função de trabalho processa itens da fila e os marca como concluído. Você pode ver a saída do rastreamento da função de trabalho, exibindo a interface do usuário do emulador de computação do Azure. É possível fazer isso clicando com o botão direito do mouse no ícone do emulador na área de notificação da sua barra de tarefas e selecionando **Mostrar Interface do Usuário do Emulador de Computação**.
    
    ![][19]
    
    ![][20]

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre o Barramento de Serviço, consulte os seguintes recursos:  

* [Documentação do Barramento de Serviço do Azure][sbdocs]  
* [Página de serviço do Barramento de Serviço][sbacom]  
* [Como usar filas do Barramento de Serviço][sbacomqhowto]  

Para saber mais sobre os cenários com várias camadas, consulte:  

* [Aplicativo de várias camadas .NET usando tabelas de armazenamento, filas e blobs][mutitierstorage]  

[0]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-app.png
[1]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-100.png
[2]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-101.png
[9]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-10.png
[10]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-11.png
[11]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-02.png
[12]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-12.png
[13]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-13.png
[14]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-33.png
[15]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-34.png
[16]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-14.png
[17]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-app.png
[18]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-app2.png

[19]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-38.png
[20]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-39.png
[23]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/SBWorkerRole1.png
[25]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/SBWorkerRoleProperties.png
[26]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/SBNewWorkerRole.png
[28]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-40.png

[sbdocs]: /azure/service-bus-messaging/  
[sbacom]: https://azure.microsoft.com/services/service-bus/  
[sbacomqhowto]: service-bus-dotnet-get-started-with-queues.md  
[mutitierstorage]: https://code.msdn.microsoft.com/Windows-Azure-Multi-Tier-eadceb36
