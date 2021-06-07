---
title: Desenvolver com o ASP.NET Core – Serviço do Azure SignalR
description: Um início rápido sobre como usar o Serviço do Azure SignalR para criar uma sala de chat com a estrutura ASP.NET.
author: sffamily
ms.service: signalr
ms.devlang: dotnet
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.date: 09/28/2020
ms.author: zhshang
ms.openlocfilehash: c39ef505b0cea0ad0c03b81683db8441077cd0d2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94874535"
---
# <a name="quickstart-create-a-chat-room-with-aspnet-and-signalr-service"></a>Início Rápido: Criar uma sala de chat com o ASP.NET e o Serviço do SignalR

O Serviço do Azure SignalR se baseia no [SignalR para ASP.NET Core 2.1](/aspnet/core/signalr/introduction?preserve-view=true&view=aspnetcore-2.1), que **não** é 100% compatível com o ASP.NET SignalR. O Serviço do Azure SignalR reimplementou o protocolo de dados do ASP.NET SignalR com base nas tecnologias mais recentes do ASP.NET Core. Ao usar o Serviço do Azure SignalR para o ASP.NET SignalR, não há mais suporte para alguns recursos do ASP.NET SignalR, por exemplo, o Azure SignalR não reproduz mensagens quando o cliente se reconecta. Além disso, não há suporte para o transporte do Forever Frame e para o JSONP. Algumas alterações de código e a versão correta das bibliotecas dependentes são necessárias para fazer com que o aplicativo ASP.NET SignalR funcione com o Serviço do SignalR.

Veja o [documento sobre diferenças de versão](/aspnet/core/signalr/version-differences?preserve-view=true&view=aspnetcore-3.1) para obter uma lista completa de comparação de recursos entre o ASP.NET SignalR e o ASP.NET Core SignalR.

Neste início rápido, você aprenderá a usar o ASP.NET e o Serviço do Azure SignalR para um [aplicativo de sala de chat](./signalr-quickstart-dotnet-core.md) semelhante.


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note-dotnet.md)]

## <a name="prerequisites"></a>Pré-requisitos

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)
* [.NET 4.6.1](https://www.microsoft.com/net/download/windows)
* [ASP.NET SignalR 2.4.1](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/)

Está com problemas? Experimente o [guia de solução de problemas](signalr-howto-troubleshoot-guide.md) ou [fale conosco](https://aka.ms/asrs/qsnet).

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no [portal do Azure](https://portal.azure.com/) com sua conta do Azure.

Está com problemas? Experimente o [guia de solução de problemas](signalr-howto-troubleshoot-guide.md) ou [fale conosco](https://aka.ms/asrs/qsnet).

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

Não há suporte para o modo *sem servidor* em aplicativos ASP.NET SignalR. Sempre use a opção *Padrão* ou *Clássico* para a instância de Serviço do Azure SignalR.

Crie também recursos do Azure usados neste início rápido com [Criar um script do Serviço do SignalR](scripts/signalr-cli-create-service.md).

Está com problemas? Experimente o [guia de solução de problemas](signalr-howto-troubleshoot-guide.md) ou [fale conosco](https://aka.ms/asrs/qsnet).

## <a name="clone-the-sample-application"></a>Clonar o aplicativo de exemplo

Durante a implantação do serviço, vamos trabalhar com o código. Clone o [aplicativo de exemplo do GitHub](https://github.com/aspnet/AzureSignalR-samples/tree/master/aspnet-samples/ChatRoom), defina a cadeia de conexão do SignalR Service e execute o aplicativo localmente.

1. Abra uma janela de terminal git. Mude para uma pasta em que deseja clonar o projeto de exemplo.

1. Execute o comando a seguir para clonar o repositório de exemplo. Este comando cria uma cópia do aplicativo de exemplo no seu computador.

    ```bash
    git clone https://github.com/aspnet/AzureSignalR-samples.git
    ```

Está com problemas? Experimente o [guia de solução de problemas](signalr-howto-troubleshoot-guide.md) ou [fale conosco](https://aka.ms/asrs/qsnet).

## <a name="configure-and-run-chat-room-web-app"></a>Configurar e executar o aplicativo Web de sala de chat

1. Inicie o Visual Studio e abra a solução na pasta *aspnet-samples/ChatRoom/* do repositório clonado.

1. No navegador em que o portal do Azure é aberto, encontre e selecione a instância criada.

1. Selecione **Chaves** para exibir as cadeias de conexão para a instância do SignalR Service.

1. Selecione e copie a cadeia de conexão primária.

1. Agora, defina a cadeia de conexão no arquivo *web.config*.

    ```xml
    <configuration>
    <connectionStrings>
        <add name="Azure:SignalR:ConnectionString" connectionString="<Replace By Your Connection String>"/>
    </connectionStrings>
    ...
    </configuration>
    ```

1. Em *Startup.cs*, em vez de chamar `MapSignalR()`, você precisará chamar `MapAzureSignalR({YourApplicationName})` e passar a cadeia de conexão para fazer com que o aplicativo se conecte ao serviço, em vez de realizar a hospedagem do SignalR por si só. Substitua `{YourApplicationName}` pelo nome de seu aplicativo. Esse nome é um nome exclusivo para diferenciar esse aplicativo dos outros aplicativos. Você pode usar `this.GetType().FullName` como o valor.

    ```cs
    public void Configuration(IAppBuilder app)
    {
        // Any connection or hub wire up and configuration should go here
        app.MapAzureSignalR(this.GetType().FullName);
    }
    ```

    Você também precisará referenciar o SDK do serviço antes de usar essas APIs. Abra as **Ferramentas | Gerenciador de Pacotes NuGet | Console do Gerenciador de Pacotes** e execute o comando:

    ```powershell
    Install-Package Microsoft.Azure.SignalR.AspNet
    ```

    Além dessas alterações, todo o resto permanecerá igual, e você ainda poderá usar a interface de hub com a qual já está familiarizado para escrever a lógica de negócios.

    > [!NOTE]
    > Na implementação, um ponto de extremidade `/signalr/negotiate` é exposto para negociação pelo SDK do Serviço do Azure SignalR. Ele retornará uma resposta de negociação especial quando os clientes tentarem se conectar e redirecionará os clientes ao ponto de extremidade de serviço definido na cadeia de conexão.

1. Pressione <kbd>F5</kbd> para executar o projeto no modo de depuração. Você poderá ver que o aplicativo é executado localmente. Em vez de hospedar um runtime do SignalR pelo próprio aplicativo, ele agora se conecta ao Serviço do Azure SignalR.

Está com problemas? Experimente o [guia de solução de problemas](signalr-howto-troubleshoot-guide.md) ou [fale conosco](https://aka.ms/asrs/qsnet).

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

> [!IMPORTANT]
> A exclusão de um grupo de recursos é irreversível, e o grupo de recursos e todos os recursos contidos nele são excluídos permanentemente. Não exclua acidentalmente o grupo de recursos ou os recursos incorretos. Se tiver criado os recursos para hospedar este exemplo dentro de um grupo de recursos existente que contém recursos que você quer manter, exclua cada recurso individualmente de suas respectivas folhas, em vez de excluir o grupo de recursos.

Entre no [portal do Azure](https://portal.azure.com) e clique em **Grupos de recursos**.

Na caixa de texto **Filtrar por nome...** , digite o nome do seu grupo de recursos. As instruções deste início rápido usaram um grupo de recursos chamado *SignalRTestResources*. Em seu grupo de recursos, na lista de resultados, clique em **...** , depois em **Excluir grupo de recursos**.

![Excluir](./media/signalr-quickstart-dotnet-core/signalr-delete-resource-group.png)

Após alguns instantes, o grupo de recursos, e todos os recursos contidos nele, serão excluídos.

Está com problemas? Experimente o [guia de solução de problemas](signalr-howto-troubleshoot-guide.md) ou [fale conosco](https://aka.ms/asrs/qsnet).

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você criou um recurso do Serviço do Azure SignalR e o usou com um aplicativo Web ASP.NET. Em seguida, saiba como desenvolver aplicativos em tempo real usando o Serviço do Azure SignalR com o ASP.NET Core.

> [!div class="nextstepaction"]
> [Serviço do Azure SignalR com o ASP.NET Core](./signalr-quickstart-dotnet-core.md)
