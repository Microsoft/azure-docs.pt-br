---
title: "Aplicativo híbrido local/na nuvem de Retransmissão do WCF do Azure (.NET) | Microsoft Docs"
description: "Saiba como criar um aplicativo híbrido .NET local/na nuvem usando a Retransmissão do WCF do Azure."
services: service-bus-relay
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 9ed02f7c-ebfb-4f39-9c97-b7dc15bcb4c1
ms.service: service-bus-relay
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 06/14/2017
ms.author: sethm
ms.openlocfilehash: d15c30dad9fb4bbe9082d6a3c72cd20ed42bbc3e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="net-on-premisescloud-hybrid-application-using-azure-wcf-relay"></a>Aplicativo híbrido .NET local/na nuvem usando a Retransmissão do WCF do Azure
## <a name="introduction"></a>Introdução

Este artigo mostra como criar um aplicativo de nuvem híbrida com o Microsoft Azure e o Visual Studio. Este tutorial pressupõe que você não tem uma experiência anterior com o Azure. Em menos de 30 minutos, você terá um aplicativo que usa vários recursos do Azure em funcionamento na nuvem.

Você aprenderá:

* Criar ou adaptar um serviço Web existente para consumo por uma solução de Web.
* Usar o serviço de Retransmissão do WCF do Azure para compartilhar dados entre um aplicativo do Azure e um serviço Web hospedado em outro lugar.

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="how-azure-relay-helps-with-hybrid-solutions"></a>Como a Retransmissão do Azure ajuda com soluções híbridas

As soluções de negócios geralmente são compostas por uma combinação de código personalizado escrito para lidar com os requisitos de negócios novos e exclusivos e a funcionalidade existente fornecida pelas soluções e sistemas que já estão estabelecidos.

Os arquitetos de solução estão começando a utilizar a nuvem para obter um manuseio mais fácil de requisitos de escala e custos operacionais mais baixos. Ao fazer isso, eles descobrem serviços ativos existentes que gostariam de aproveitar, como blocos de construção para suas soluções estão dentro do firewall corporativo e fora de alcance fácil para acesso pela solução de nuvem. Muitos serviços internos não são construídos ou hospedados de forma que possam ser facilmente expostos na borda da rede corporativa.

A [Retransmissão do Azure](https://azure.microsoft.com/services/service-bus/) foi desenvolvida para o caso de utilizar os serviços Web do WCF (Windows Communication Foundation) existentes e torná-los acessíveis com segurança para soluções que residem fora do perímetro corporativo, sem exigir alterações intrusivas na infraestrutura da rede corporativa. Esses serviços de retransmissão ainda estão hospedados dentro de seu ambiente existente, mas delegam a escuta de sessões de entrada e solicitações para o serviço de retransmissão hospedado na nuvem. A Retransmissão do Azure também protege esses serviços contra acesso não autorizado usando a autenticação [SAS (Assinatura de Acesso Compartilhado)](../service-bus-messaging/service-bus-sas.md).

## <a name="solution-scenario"></a>Cenário da solução
Neste tutorial, você criará um site ASP.NET que permitirá ver uma lista de produtos na página do inventário de produtos.

![][0]

O tutorial supõe que você tem informações sobre produtos em um sistema local existente e utiliza a Retransmissão do Azure para acessar esse sistema. Isso é simulado por um serviço Web executado em um aplicativo de console simples e com o suporte de um conjunto de produtos na memória. Você poderá executar esse aplicativo de console em seu próprio computador e implantar a função web no Azure. Fazendo isso, você verá como a função de web em execução no datacenter do Azure realmente será chamada em seu computador, embora seu computador certamente resida atrás de pelo menos um firewall e de uma camada de NAT (conversão de endereços de rede).

## <a name="set-up-the-development-environment"></a>Configurar o ambiente de desenvolvimento

Antes de começar a desenvolver aplicativos do Azure, baixe as ferramentas e configure seu ambiente de desenvolvimento:

1. Instale o Azure SDK para .NET da [página de downloads](https://azure.microsoft.com/downloads/) do SDK.
2. Na coluna **.NET**, clique na versão do [Visual Studio](http://www.visualstudio.com) que você está usando. As etapas neste tutorial usam o Visual Studio 2015, mas também funcionam com o Visual Studio 2017.
3. Quando for solicitado a executar ou salvar o instalador, clique em **Executar**.
4. No **Web Platform Installer**, clique em **Instalar** e prossiga com a instalação.
5. Quando a instalação estiver concluída, você terá tudo o que é necessário para iniciar o desenvolvimento do aplicativo. O SDK inclui ferramentas que permitem que você desenvolva facilmente aplicativos do Azure no Visual Studio.

## <a name="create-a-namespace"></a>Criar um namespace

Para começar a usar os recursos de retransmissão no Azure, você deve primeiro criar um namespace de serviço. Um namespace fornece um contêiner de escopo para endereçar recursos do Azure dentro de seu aplicativo. Siga as [instruções aqui](relay-create-namespace-portal.md) para criar um namespace de Retransmissão.

## <a name="create-an-on-premises-server"></a>Criar um servidor local

Primeiro você irá criar um sistema de catálogo de produtos (fictício) local. Será muito simples, você pode ver isso como uma representação de um sistema de catálogo de produtos real local com uma superfície de serviço completa que estamos tentando integrar.

Este projeto é um aplicativo de console do Visual Studio e usa o [pacote NuGet do Barramento de Serviço do Azure](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) para incluir as bibliotecas do Barramento de Serviço e as definições da configuração.

### <a name="create-the-project"></a>Criar o projeto

1. Utilizando privilégios do administrador, inicie o Microsoft Visual Studio. Para fazer isso, clique com o botão direito do mouse no ícone do programa do Visual Studio e, em seguida, clique em **Executar como administrador**.
2. No Visual Studio, no menu **Arquivo**, clique em **Novo** e clique em **Projeto**.
3. Em **Modelos Instalados**, em **Visual C#**, clique em **Aplicativo de Console (.NET Framework)**. Na caixa **Nome**, digite o nome **ServidorDeProdutos**:

   ![][11]
4. Clique em **OK** para criar o projeto **ServidorDeProdutos**.
5. Se você já tiver instalado o Gerenciador de Pacotes NuGet para Visual Studio, vá para a próxima etapa. Caso contrário, visite [NuGet][NuGet] e clique em [Instalar NuGet](http://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c). Siga os prompts para instalar o Gerenciador de Pacotes NuGet e, em seguida, reinicie o Visual Studio.
6. No Gerenciador de Soluções, clique com o botão direito no projeto **ServidorDeProdutos** e clique em **Gerenciar Pacotes NuGet**.
7. Clique na guia **Procurar** e procure `Microsoft Azure Service Bus`. Selecione o pacote **WindowsAzure.ServiceBus**.
8. Clique em **Instalar**e aceite os termos de uso.

   ![][13]

   Observe que os assemblies do cliente necessários agora são referenciados.
8. Adicione uma nova classe para seu contrato de produto. No Gerenciador de Soluções, clique com o botão direito do mouse no projeto **ServidorDeProdutos**, clique em **Adicionar** e em **Classe**.
9. Na caixa **Nome**, digite o nome **ProductsContract.cs**. Clique em **Adicionar**.
10. Em **ProductsContract.cs**, substitua a definição do namespace pelo código a seguir, que define o contrato do serviço.

    ```csharp
    namespace ProductsServer
    {
        using System.Collections.Generic;
        using System.Runtime.Serialization;
        using System.ServiceModel;

        // Define the data contract for the service
        [DataContract]
        // Declare the serializable properties.
        public class ProductData
        {
            [DataMember]
            public string Id { get; set; }
            [DataMember]
            public string Name { get; set; }
            [DataMember]
            public string Quantity { get; set; }
        }

        // Define the service contract.
        [ServiceContract]
        interface IProducts
        {
            [OperationContract]
            IList<ProductData> GetProducts();

        }

        interface IProductsChannel : IProducts, IClientChannel
        {
        }
    }
    ```
11. Em Program.cs, substitua a definição do namespace pelo código a seguir, que adiciona o serviço de perfil e o host para ele.

    ```csharp
    namespace ProductsServer
    {
        using System;
        using System.Linq;
        using System.Collections.Generic;
        using System.ServiceModel;

        // Implement the IProducts interface.
        class ProductsService : IProducts
        {

            // Populate array of products for display on website
            ProductData[] products =
                new []
                    {
                        new ProductData{ Id = "1", Name = "Rock",
                                         Quantity = "1"},
                        new ProductData{ Id = "2", Name = "Paper",
                                         Quantity = "3"},
                        new ProductData{ Id = "3", Name = "Scissors",
                                         Quantity = "5"},
                        new ProductData{ Id = "4", Name = "Well",
                                         Quantity = "2500"},
                    };

            // Display a message in the service console application
            // when the list of products is retrieved.
            public IList<ProductData> GetProducts()
            {
                Console.WriteLine("GetProducts called.");
                return products;
            }

        }

        class Program
        {
            // Define the Main() function in the service application.
            static void Main(string[] args)
            {
                var sh = new ServiceHost(typeof(ProductsService));
                sh.Open();

                Console.WriteLine("Press ENTER to close");
                Console.ReadLine();

                sh.Close();
            }
        }
    }
    ```
12. No Gerenciador de Soluções, clique duas vezes no arquivo **App.config** para abri-lo no editor do Visual Studio. Na parte inferior do elemento `<system.ServiceModel>` (mas ainda no `<system.ServiceModel>`), adicione o seguinte código XML. Substitua *seuNamespaceDeServiço* pelo nome do seu namespace e *suaChave* pela chave SAS recuperada anteriormente no portal:

    ```xml
    <system.serviceModel>
    ...
      <services>
         <service name="ProductsServer.ProductsService">
           <endpoint address="sb://yourServiceNamespace.servicebus.windows.net/products" binding="netTcpRelayBinding" contract="ProductsServer.IProducts" behaviorConfiguration="products"/>
         </service>
      </services>
      <behaviors>
         <endpointBehaviors>
           <behavior name="products">
             <transportClientEndpointBehavior>
                <tokenProvider>
                   <sharedAccessSignature keyName="RootManageSharedAccessKey" key="yourKey" />
                </tokenProvider>
             </transportClientEndpointBehavior>
           </behavior>
         </endpointBehaviors>
      </behaviors>
    </system.serviceModel>
    ```
13. Ainda em App.config, no elemento `<appSettings>`, substitua o valor da cadeia de conexão pela cadeia de conexão obtida anteriormente no portal.

    ```xml
    <appSettings>
       <!-- Service Bus specific app settings for messaging connections -->
       <add key="Microsoft.ServiceBus.ConnectionString"
           value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey"/>
    </appSettings>
    ```
14. Pressione **Ctrl+Shift+B** ou no menu **Compilar**, clique em **Compilar Solução** para compilar o aplicativo e verificar a precisão de seu trabalho até o momento.

## <a name="create-an-aspnet-application"></a>Criar um aplicativo ASP.NET

Nesta seção você criará um aplicativo ASP.NET simples que exibe os dados recuperados do seu serviço de produto.

### <a name="create-the-project"></a>Criar o projeto

1. Certifique-se de que o Visual Studio está sendo executado com os privilégios de administrador.
2. No Visual Studio, no menu **Arquivo**, clique em **Novo** e clique em **Projeto**.
3. Em **Modelos Instalados**, em **Visual C#**, clique em **Aplicativo Web ASP.NET (.NET Framework)**. Nomeie o projeto como **PortalDeProdutos**. Em seguida, clique em **OK**.

   ![][15]

4. Na lista **Modelos ASP.NET** do diálogo **Novo Aplicativo Web ASP.NET**, clique em **MVC**.

   ![][16]

6. Clique no botão **Alterar Autenticação**. Na caixa de diálogo **Alterar Autenticação**, garanta que **Sem Autenticação** esteja selecionado e clique em **OK**. Para este tutorial, você está implantando um aplicativo que não precisa de um logon de usuário.

    ![][18]

7. De volta ao diálogo **Novo aplicativo Web ASP.NET**, clique em **OK** para criar o aplicativo MVC.
8. Agora você deve configurar os recursos do Azure para um novo aplicativo Web. Siga as etapas da [seção Publicar no Azure deste artigo](../app-service/app-service-web-get-started-dotnet.md). Em seguida, retorne a este tutorial e prossiga para a próxima etapa.
10. No Gerenciador de Soluções, clique com o botão direito do mouse em **Modelos**, clique em **Adicionar** e em **Classe**. Na caixa **Nome**, digite o nome **Product.cs**. Clique em **Adicionar**.

    ![][17]

### <a name="modify-the-web-application"></a>Modificar o aplicativo web

1. No arquivo Product.cs, no Visual Studio, substitua a definição de namespace existente pelo código a seguir.

   ```csharp
    // Declare properties for the products inventory.
    namespace ProductsWeb.Models
    {
       public class Product
       {
           public string Id { get; set; }
           public string Name { get; set; }
           public string Quantity { get; set; }
       }
    }
    ```
2. No Gerenciador de Soluções, expanda a pasta **Controladores**, clique duas vezes no arquivo **HomeController.cs** para abri-lo no Visual Studio.
3. Em **HomeController.cs**, substitua a definição do namespace existente pelo código a seguir.

    ```csharp
    namespace ProductsWeb.Controllers
    {
        using System.Collections.Generic;
        using System.Web.Mvc;
        using Models;

        public class HomeController : Controller
        {
            // Return a view of the products inventory.
            public ActionResult Index(string Identifier, string ProductName)
            {
                var products = new List<Product>
                    {new Product {Id = Identifier, Name = ProductName}};
                return View(products);
            }
         }
    }
    ```
4. No Gerenciador de Soluções, expanda a pasta Views\Shared e clique duas vezes em **_Layout.cshtml** para abri-lo no editor do Visual Studio.
5. Altere todas as ocorrências de **Meu Aplicativo ASP.NET** para **Produtos da LITWARE**.
6. Remova os links **Página Inicial**, **Sobre** e **Contato**. No exemplo a seguir, exclua o código destacado.

    ![][41]

7. No Gerenciador de Soluções, expanda a pasta Views\Home e clique duas vezes em **Index.cshtml** para abri-lo no editor do Visual Studio. Substitua todo o conteúdo do arquivo pelo código a seguir.

   ```html
   @model IEnumerable<ProductsWeb.Models.Product>

   @{
            ViewBag.Title = "Index";
   }

   <h2>Prod Inventory</h2>

   <table>
             <tr>
                 <th>
                     @Html.DisplayNameFor(model => model.Name)
                 </th>
                 <th></th>
                 <th>
                     @Html.DisplayNameFor(model => model.Quantity)
                 </th>
             </tr>

   @foreach (var item in Model) {
             <tr>
                 <td>
                     @Html.DisplayFor(modelItem => item.Name)
                 </td>
                 <td>
                     @Html.DisplayFor(modelItem => item.Quantity)
                 </td>
             </tr>
   }

   </table>
   ```
8. Para verificar a precisão de seu trabalho até o momento, você pode pressionar **Ctrl+Shift+B** para compilar o projeto.

### <a name="run-the-app-locally"></a>Executar o aplicativo localmente

Execute o aplicativo para verificar se ele funciona.

1. Verifique se o **PortalDeProdutos** é o projeto ativo. No Gerenciador de Soluções, clique com o botão direito do mouse no nome do projeto e selecione **Definir como Projeto de Inicialização**.
2. No Visual Studio, pressione **F5**.
3. Seu aplicativo deve aparecer em execução em um navegador.

   ![][21]

## <a name="put-the-pieces-together"></a>Juntar as peças

A próxima etapa é vincular o servidor de produtos local com o aplicativo ASP.NET.

1. Se ele ainda não estiver aberto, no Visual Studio, reabra o projeto **PortalDeProdutos** criado na seção [Criar um Aplicativo ASP.NET](#create-an-aspnet-application).
2. Assim como na etapa da seção "Criar um servidor local", adicione o pacote NuGet às referências do projeto. No Gerenciador de Soluções, clique com o botão direito no projeto **PortalDeProdutos** e clique em **Gerenciar Pacotes NuGet**.
3. Procure "Barramento de Serviço" e selecione o item **WindowsAzure.ServiceBus** . Então conclua a instalação e feche esta caixa de diálogo.
4. No Gerenciador de Soluções, clique com o botão direito do mouse no projeto **PortalDeProdutos**, clique em **Adicionar** e em **Item Existente**.
5. Navegue até o arquivo **ProductsContract.cs** do projeto de console **ServidorDeProdutos**. Clique para realçar ProductsContract.cs. Clique na seta para baixo ao lado de **Adiciona**r e clique em **Adicionar como Link**.

   ![][24]

6. Agora abra o arquivo **HomeController.cs** no editor do Visual Studio e substitua a definição de namespace pelo código a seguir. Substitua *seuNamespaceDeServiço* pelo nome do seu namespace de serviço e *suaChave* pela sua chave SAS. Isso permitirá que o cliente chame o serviço local, retornando o resultado da chamada.

   ```csharp
   namespace ProductsWeb.Controllers
   {
       using System.Linq;
       using System.ServiceModel;
       using System.Web.Mvc;
       using Microsoft.ServiceBus;
       using Models;
       using ProductsServer;

       public class HomeController : Controller
       {
           // Declare the channel factory.
           static ChannelFactory<IProductsChannel> channelFactory;

           static HomeController()
           {
               // Create shared access signature token credentials for authentication.
               channelFactory = new ChannelFactory<IProductsChannel>(new NetTcpRelayBinding(),
                   "sb://yourServiceNamespace.servicebus.windows.net/products");
               channelFactory.Endpoint.Behaviors.Add(new TransportClientEndpointBehavior {
                   TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(
                       "RootManageSharedAccessKey", "yourKey") });
           }

           public ActionResult Index()
           {
               using (IProductsChannel channel = channelFactory.CreateChannel())
               {
                   // Return a view of the products inventory.
                   return this.View(from prod in channel.GetProducts()
                                    select
                                        new Product { Id = prod.Id, Name = prod.Name,
                                            Quantity = prod.Quantity });
               }
           }
       }
   }
   ```
7. No Solution Explorer, clique com botão direito na solução **PortalDeProdutos** (clique com o botão direito na solução, não no projeto). Clique em **Adicionar** e depois clique em **Projeto Existente**.
8. Navegue até o projeto **ServidorDeProdutos** e clique duas vezes no arquivo de solução **ServidorDeProdutos.csproj** para adicioná-lo.
9. O **ServidorDeProdutos** deve estar em execução para exibir os dados em **PortalDeProdutos**. No Gerenciador de Soluções, clique com o botão direito do mouse na solução **PortalDeProdutos** e clique em **Propriedades**. A caixa de diálogo **Páginas da Propriedade** é exibida.
10. No lado esquerdo, clique em **Projeto de Inicialização**. No lado direito, clique em **Vários projeto de Inicialização**. Verifique se **ServidorDeProdutos** e **PortalDeProdutos** aparecem, nessa ordem, com **Iniciar** definido como a ação para ambos.

      ![][25]

11. Ainda na caixa de diálogo **Propriedades**, clique em **Dependências do Projeto** no lado esquerdo.
12. Na lista **Projetos**, clique em **ServidorDeProdutos**. Confirme se **ProductsPortal** não está selecionado.
13. Na lista **Projetos**, clique em **PortalDeProdutos**. Verifique se **ServidorDeProdutos** está selecionado.

    ![][26]

14. Clique em **OK** na caixa de diálogo **Páginas da Propriedade**.

## <a name="run-the-project-locally"></a>Executar o projeto localmente

Para testar o aplicativo localmente, no Visual Studio, pressione **F5**. O servidor local (**ServidorDeProdutos**) deve iniciar primeiro e, então, o aplicativo **PortalDeProdutos** deve iniciar em uma janela do navegador. Desta vez, você verá que o inventário de produtos lista dados recuperados do sistema local de serviço de produto.

![][10]

Pressione **Atualizar** na página **PortalDeProdutos**. Sempre que você atualizar a página, verá que o aplicativo do servidor exibirá uma mensagem quando `GetProducts()` do **ServidorDeProdutos** for chamado.

Feche os aplicativos antes de prosseguir para a próxima etapa.

## <a name="deploy-the-productsportal-project-to-an-azure-web-app"></a>Implantar o projeto ProductsPortal em um aplicativo Web do Azure

A próxima etapa é publicar novamente o front-end **ProductsPortal** do aplicativo Web do Azure. Faça o seguinte:

1. No Gerenciador de Soluções, clique com o botão direito do mouse no projeto **ProductsPortal** e clique em **Publicar**. Em seguida, clique em **Publicar** na página **Publicar**.

  > [!NOTE]
  > Você verá uma mensagem de erro na janela do navegador quando o projeto Web **PortalDeProdutos** for iniciado automaticamente após a implantação. Isso é esperado e ocorre porque o aplicativo **ServidorDeProdutos** não está sendo executado ainda.
>
>

2. Copie a URL do aplicativo Web implantado, pois você precisará dela na próxima etapa. Você também pode obter essa URL na janela Atividade do Serviço de Aplicativo do Azure no Visual Studio:

  ![][9]

3. Feche a janela do navegador para interromper o aplicativo em execução.

### <a name="set-productsportal-as-web-app"></a>Defina ProductsPortal como o aplicativo Web

Antes de executar o aplicativo na nuvem, você deve garantir que **PortalDeProdutos** seja inicializado de dentro do Visual Studio como um aplicativo Web.

1. No Visual Studio, clique com o botão direito no projeto **ProductsPortal** e em **Propriedades**.
2. Na coluna à esquerda, clique em **Web**.
3. Na seção **Iniciar Ação**, clique no botão **Iniciar URL** e na caixa de texto, insira a URL de seu aplicativo Web implantado anteriormente; por exemplo, `http://productsportal1234567890.azurewebsites.net/`.

    ![][27]

4. No menu **Arquivo** no Visual Studio, clique em **Salvar Tudo**.
5. No menu Compilar no Visual Studio, clique em **Recompilar Solução**.

## <a name="run-the-application"></a>Executar o aplicativo

1. Pressione F5 para compilar e executar o aplicativo. O servidor local (o aplicativo de console **ServidorDeProdutos**) deve iniciar primeiro, em seguida, o aplicativo **PortalDeProdutos** deve iniciar em uma janela do navegador, como mostrado na captura de tela a seguir. Observe novamente que o inventário de produtos lista os dados recuperados no sistema local do serviço de produto e exibe os dados em um aplicativo Web. Verifique a URL para saber se **PortalDeProdutos** está em execução na nuvem, como um aplicativo Web do Azure.

   ![][1]

   > [!IMPORTANT]
   > O aplicativo de console **ServidorDeProdutos** deve estar em execução e ser capaz de fornecer dados para o aplicativo **PortalDeProdutos**. Se o navegador exibir um erro, aguarde mais alguns segundos para **ServidorDeProdutos** carregar e exibir a mensagem a seguir. Em seguida, pressione **Atualizar** no navegador.
   >
   >

   ![][37]
2. No navegador, pressione **Atualizar** na página **PortalDeProdutos**. Sempre que você atualizar a página, verá que o aplicativo do servidor exibirá uma mensagem quando `GetProducts()` do **ServidorDeProdutos** for chamado.

    ![][38]

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a Retransmissão do Azure, consulte os seguintes recursos:  

* [O que é Retransmissão do Azure?](relay-what-is-it.md)  
* [Como usar a Retransmissão](service-bus-dotnet-how-to-use-relay.md)  

[0]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hybrid.png
[1]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App2.png
[NuGet]: http://nuget.org

[11]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-con-1.png
[13]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-13.png
[15]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-2.png
[16]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-4.png
[17]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-7.png
[18]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-5.png
[9]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-9.png
[10]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App3.png

[21]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App1.png
[24]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-12.png
[25]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-13.png
[26]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-14.png
[27]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-8.png

[36]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App2.png
[37]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-service1.png
[38]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-service2.png
[41]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-40.png
[43]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-43.png
