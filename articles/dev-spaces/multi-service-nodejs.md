---
title: Executar vários serviços dependentes usando Node.js e VS Code
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 11/21/2018
ms.topic: tutorial
description: Desenvolvimento rápido de Kubernetes com contêineres e microsserviços no Azure
keywords: Docker, Kubernetes, Azure, AKS, Serviço de Kubernetes do Azure, contêineres, Helm, malha de serviço, roteamento de malha de serviço, kubectl, k8s
ms.openlocfilehash: 136d48f1c420ac71896eaafa0daab476c7fba6fa
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67503063"
---
# <a name="multi-service-development-with-azure-dev-spaces"></a>Desenvolvimento de vários serviços com Azure Dev Spaces

Neste tutorial, você aprenderá a desenvolver aplicativos de vários serviços usando Azure Dev Spaces, bem como alguns dos benefícios adicionais que o Azure Dev Spaces oferece.

## <a name="call-a-service-running-in-a-separate-container"></a>Chamar um serviço em execução em um contêiner separado

Nesta seção, você criará um segundo serviço, `mywebapi`, e fará com que ele seja chamado por `webfrontend`. Cada serviço será executado em contêineres separados. Em seguida, você fará a depuração em ambos os contêineres.

![](media/common/multi-container.png)

### <a name="open-sample-code-for-mywebapi"></a>Abrir exemplo de código para *mywebapi*
Você já deve ter o exemplo de código para `mywebapi` para esse guia em uma pasta chamada `samples` (se não, vá para https://github.com/Azure/dev-spaces e selecione **Clonar ou baixar** para baixar o repositório do GitHub). O código para essa seção está em `samples/nodejs/getting-started/mywebapi`.

### <a name="run-mywebapi"></a>Executar *mywebapi*
1. Abra a pasta `mywebapi` em uma *janela separada do VS Code*.
1. Abra a **Paleta de Comandos** (usando o menu **Exibir | Paleta de Comandos**) e use o preenchimento automático para digitar e selecionar este comando: `Azure Dev Spaces: Prepare configuration files for Azure Dev Spaces`. Esse comando não é deve ser confundido com o `azds prep`, que configura o projeto para a implantação.
1. Pressione F5 e aguarde a criação e implantação do serviço. Você saberá que está pronto, quando a mensagem *Escuta na porta 80* for exibida no console de depuração.
1. Anote a URL do ponto de extremidade, ele será parecida com `http://localhost:<portnumber>`. **Dica: A barra de status do VS Code fica laranja e exibe uma URL clicável.** Pode parecer que o contêiner está sendo executado localmente, mas, na verdade, ele está em execução em seu ambiente de desenvolvimento no Azure. O motivo para o endereço do localhost é porque `mywebapi` não definiu nenhum ponto de extremidade público e somente pode ser acessado de dentro da instância de Kubernetes. Para sua conveniência e para facilitar a interação com o serviço privado em sua máquina local, o Azure Dev Spaces cria um túnel SSH temporário para o contêiner em execução no Azure.
1. Quando `mywebapi` estiver pronto, abra seu navegador para o endereço do localhost. Você deve ver uma resposta do serviço `mywebapi` (“Olá do mywebapi”).


### <a name="make-a-request-from-webfrontend-to-mywebapi"></a>Faça uma solicitação de *webfrontend* para *mywebapi*
Vamos escrever código agora em `webfrontend`, que faz uma solicitação para `mywebapi`.
1. Alterne para a janela do VS Code para `webfrontend`.
1. Adicione estas linhas de código na parte superior do `server.js`:
    ```javascript
    var request = require('request');
    ```

3. *Substitua* o código pelo manipulador GET `/api`. Ao lidar com uma solicitação, ele faz uma chamada para `mywebapi` e, em seguida, retorna os resultados de ambos os serviços.

    ```javascript
    app.get('/api', function (req, res) {
       request({
          uri: 'http://mywebapi',
          headers: {
             /* propagate the dev space routing header */
             'azds-route-as': req.headers['azds-route-as']
          }
       }, function (error, response, body) {
           res.send('Hello from webfrontend and ' + body);
       });
    });
    ```
   1. *Remova* a linha `server.close()` do final do `server.js`

O exemplo de código anterior encaminha o cabeçalho `azds-route-as` da solicitação de entrada para a solicitação de saída. Você verá posteriormente como isso ajuda as equipes de desenvolvimento colaborativas.

### <a name="debug-across-multiple-services"></a>Depurar em vários serviços
1. Neste ponto, `mywebapi` ainda deve estar em execução com o depurador anexado. Se não estiver, pressione F5 no projeto `mywebapi`.
1. Defina um ponto de interrupção no manipulador GET `/` padrão [na linha 8 de `server.js`](https://github.com/Azure/dev-spaces/blob/master/samples/nodejs/getting-started/mywebapi/server.js#L8).
1. No projeto `webfrontend`, defina um ponto de interrupção antes que ele envie uma solicitação GET para `http://mywebapi`.
1. Pressione F5 no projeto `webfrontend`.
1. Abra o aplicativo Web e percorra o código em ambos os serviços. O aplicativo Web deverá exibir uma mensagem concatenada pelos dois serviços: "Olá de webfrontend e Olá de mywebapi".

### <a name="well-done"></a>Muito bem!
Agora você tem um aplicativo de vários contêineres em que cada contêiner pode ser desenvolvido e implantado separadamente.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Saiba mais sobre o desenvolvimento de equipe no Azure Dev Spaces](team-development-nodejs.md)
