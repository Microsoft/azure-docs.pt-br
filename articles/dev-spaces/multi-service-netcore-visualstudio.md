---
title: Executar vários serviços dependentes usando .NET Core e Visual Studio
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.custom: vs-azure
ms.workload: azure-vs
author: zr-msft
ms.author: zarhoads
ms.date: 07/09/2018
ms.topic: tutorial
description: Desenvolvimento rápido de Kubernetes com contêineres e microsserviços no Azure
keywords: Docker, Kubernetes, Azure, AKS, Serviço de Kubernetes do Azure, contêineres, Helm, malha de serviço, roteamento de malha de serviço, kubectl, k8s
ms.openlocfilehash: dd90dee2f973bb26a43706eb77f15778cb9116a0
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67502968"
---
# <a name="multi-service-development-with-azure-dev-spaces"></a>Desenvolvimento de vários serviços com Azure Dev Spaces

Neste tutorial, você aprenderá a desenvolver aplicativos de vários serviços usando Azure Dev Spaces, bem como alguns dos benefícios adicionais que o Azure Dev Spaces oferece.

## <a name="call-another-container"></a>Chamar outro contêiner
Nesta seção, você criará um segundo serviço, `mywebapi`, e fará com que ele seja chamado por `webfrontend`. Cada serviço será executado em contêineres separados. Em seguida, você fará a depuração em ambos os contêineres.

![](media/common/multi-container.png)

### <a name="download-sample-code-for-mywebapi"></a>Baixar código de exemplo para *mywebapi*
Por economizar tempo, vamos baixar o código de exemplo de um repositório do GitHub. Vá para https://github.com/Azure/dev-spaces e selecione **Clonar ou baixar** para baixar o repositório do GitHub. O código para essa seção está em `samples/dotnetcore/getting-started/mywebapi`.

### <a name="run-mywebapi"></a>Executar *mywebapi*
1. Abra o projeto `mywebapi` em uma *janela separada do Visual Studio*.
1. Selecione **Azure Dev Spaces** na lista suspensa de configurações de inicialização, conforme feito anteriormente no projeto `webfrontend`. Em vez de criar um novo cluster AKS desta vez, selecione o mesmo que você já criou. Assim como antes, deixe o espaço com o padrão `default` e clique em **OK**. Na janela de Saída, pode ser que você note que o Visual Studio inicia para “aquecer” esse novo serviço em seu espaço de desenvolvimento para acelerar as coisas ao iniciar a depuração.
1. Pressione F5 e aguarde a criação e implantação do serviço. Você saberá que está pronto quando a barra de status do Visual Studio ficar laranja
1. Anote o ponto de extremidade de URL exibido no painel **Azure Dev Spaces for AKS** na janela de **Saída**. Será semelhante a `http://localhost:<portnumber>`. Pode parecer que o contêiner está sendo executado localmente, mas, na verdade, ele está em execução no espaço de desenvolvimento no Azure.
2. Quando `mywebapi` estiver pronto, abra seu navegador no endereço do localhost e acrescente `/api/values` à URL para invocar a API GET padrão para `ValuesController`. 
3. Se todas as etapas foram bem-sucedidas, você deverá ver uma resposta do serviço `mywebapi` que se parece com isto.

    ![](media/get-started-netcore-visualstudio/WebAPIResponse.png)

### <a name="make-a-request-from-webfrontend-to-mywebapi"></a>Faça uma solicitação de *webfrontend* para *mywebapi*
Vamos escrever código agora em `webfrontend`, que faz uma solicitação para `mywebapi`. Alterne para a janela do Visual Studio que tem o projeto `webfrontend`. No arquivo `HomeController.cs`, *substitua* o código pelo método Sobre com o seguinte código:

   ```csharp
   public async Task<IActionResult> About()
   {
      ViewData["Message"] = "Hello from webfrontend";

      using (var client = new System.Net.Http.HttpClient())
            {
                // Call *mywebapi*, and display its response in the page
                var request = new System.Net.Http.HttpRequestMessage();
                request.RequestUri = new Uri("http://mywebapi/api/values/1");
                if (this.Request.Headers.ContainsKey("azds-route-as"))
                {
                    // Propagate the dev space routing header
                    request.Headers.Add("azds-route-as", this.Request.Headers["azds-route-as"] as IEnumerable<string>);
                }
                var response = await client.SendAsync(request);
                ViewData["Message"] += " and " + await response.Content.ReadAsStringAsync();
            }

      return View();
   }
   ```

O exemplo de código anterior encaminha o cabeçalho `azds-route-as` da solicitação de entrada para a solicitação de saída. Você verá mais tarde como isso facilita uma experiência de desenvolvimento mais produtiva em [cenários de equipe](team-development-netcore-visualstudio.md).

### <a name="debug-across-multiple-services"></a>Depurar em vários serviços
1. Neste ponto, `mywebapi` ainda deve estar em execução com o depurador anexado. Se não estiver, pressione F5 no projeto `mywebapi`.
1. Defina um ponto de interrupção no método `Get(int id)`, no arquivo `Controllers/ValuesController.cs`, que manipula solicitações GET `api/values/{id}`.
1. No projeto `webfrontend`, no qual você colou o código acima, defina um ponto de interrupção antes que ele envie uma solicitação GET para `mywebapi/api/values`.
1. Pressione F5 no projeto `webfrontend`. Novamente, o Visual Studio abrirá um navegador para a porta localhost apropriada, e o aplicativo Web será exibido.
1. Clique no link “**Sobre**” na parte superior da página para disparar o ponto de interrupção no projeto `webfrontend`. 
1. Pressione F10 para continuar. Agora o ponto de interrupção no projeto `mywebapi` será disparado.
1. Pressione F5 para continuar e você estará novamente no código no projeto `webfrontend`.
1. Pressionar F5 mais uma vez concluirá a solicitação e retornará uma página no navegador. No aplicativo Web, a página Sobre exibirá uma mensagem concatenada pelos dois serviços: "Olá de webfrontend e Olá de mywebapi".

### <a name="well-done"></a>Muito bem!
Agora você tem um aplicativo de vários contêineres em que cada contêiner pode ser desenvolvido e implantado separadamente.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Saiba mais sobre o desenvolvimento de equipe no Azure Dev Spaces](team-development-netcore-visualstudio.md)
