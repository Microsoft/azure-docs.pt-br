---
title: Crie um ambiente de desenvolvimento Node.js no Kubernetes na nuvem com VS Code
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 09/26/2018
ms.topic: tutorial
description: Desenvolvimento rápido de Kubernetes com contêineres e microsserviços no Azure
keywords: Docker, Kubernetes, Azure, AKS, Serviço de Kubernetes do Azure, contêineres, Helm, malha de serviço, roteamento de malha de serviço, kubectl, k8s
ms.openlocfilehash: 30f912e9c1573b32247bb3c2a3f7d4026436748b
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67503045"
---
# <a name="get-started-on-azure-dev-spaces-with-nodejs"></a>Introdução ao Azure Dev Spaces com Node.js

Neste guia, você aprenderá a:

- Crie um ambiente baseado em Kubernetes no Azure que seja otimizado para desenvolvimento – um _espaço de desenvolvimento_.
- Desenvolver código em containers iterativamente usando o VS Code e a linha de comando.
- Desenvolva e teste o código produtivamente em um ambiente de equipe.

> [!Note]
> **Caso tenha problemas** a qualquer momento, veja a seção [Solução de problemas](troubleshooting.md).

## <a name="install-the-azure-cli"></a>Instalar a CLI do Azure
O Azure Dev Spaces requer somente uma configuração mínima do computador local. A maior parte da configuração do seu espaço de desenvolvimento é armazenada na nuvem e pode ser compartilhada com outros usuários. Comece baixando e executando a [CLI do Azure](/cli/azure/install-azure-cli?view=azure-cli-latest).

### <a name="sign-in-to-azure-cli"></a>Entrar na CLI do Azure
Entre no Azure. Digite o seguinte comando em uma janela de terminal:

```cmd
az login
```

> [!Note]
> Caso não tenha uma assinatura do Azure, é possível criar uma [conta gratuita](https://azure.microsoft.com/free).

#### <a name="if-you-have-multiple-azure-subscriptions"></a>Caso tenha várias assinaturas do Azure...
Você pode exibir suas assinaturas, executando: 

```cmd
az account list
```
Localize a assinatura que tem `isDefault: true`na saída do JSON.
Se não for a assinatura que você deseja usar, é possível alterar a assinatura padrão:

```cmd
az account set --subscription <subscription ID>
```

## <a name="create-a-kubernetes-cluster-enabled-for-azure-dev-spaces"></a>Criar um cluster Kubernetes habilitado para Azure Dev Spaces

No prompt de comando, crie o grupo de recursos em uma [região que dá suporte ao Azure Dev Spaces][supported-regions].

```cmd
az group create --name MyResourceGroup --location <region>
```

Crie um cluster Kubernetes com o seguinte comando:

```cmd
az aks create -g MyResourceGroup -n MyAKS --location <region> --disable-rbac --generate-ssh-keys
```

São necessários alguns minutos para criar o cluster.

### <a name="configure-your-aks-cluster-to-use-azure-dev-spaces"></a>Configurar o cluster AKS para usar Azure Dev Spaces

Insira o seguinte comando da CLI do Azure, usando o grupo de recursos que contém o cluster do AKS e o nome do cluster do AKS. O comando configura o cluster com suporte para o Azure Dev Spaces.

   ```cmd
   az aks use-dev-spaces -g MyResourceGroup -n MyAKS
   ```

> [!IMPORTANT]
> O processo de configuração do Azure Dev Spaces remove o namespace `azds` no cluster, se existir.

## <a name="get-kubernetes-debugging-for-vs-code"></a>Obter depuração do Kubernetes para VS Code
Recursos avançados como depuração do Kubernetes estão disponíveis para desenvolvedores .NET Core e Node.js usando VS Code.

1. Caso não o tenha, instale o [VS Code](https://code.visualstudio.com/Download).
1. Baixe e instale a [extensão Azure Dev Spaces do VS](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds). Clique em Instalar uma vez na página do Marketplace da extensão e novamente no VS Code. 

## <a name="create-a-nodejs-container-in-kubernetes"></a>Criar um contêiner de Node. js no Kubernetes

Nesta seção, você criará um aplicativo Web Node. js e o executará em um contêiner no Kubernetes.

### <a name="create-a-nodejs-web-app"></a>Criar um aplicativo Web Node.js
Baixe código do GitHub navegando até https://github.com/Azure/dev-spaces e selecionando **Clonar ou Baixar** para baixar o repositório do GitHub em seu ambiente local. O código para este guia está em `samples/nodejs/getting-started/webfrontend`.

## <a name="prepare-code-for-docker-and-kubernetes-development"></a>Preparar código para desenvolvimento de Docker e Kubernetes
Até o momento, você tem um aplicativo Web básico que pode ser executado localmente. Agora, você colocará o aplicativo em contêiner criando ativos que definem o contêiner do aplicativo e como ele será implantado no Kubernetes. Isso é fácil de fazer usando o Azure Dev Spaces: 

1. Inicialize o VS Code e abra a pasta `webfrontend`. (Você pode ignorar todos os prompts padrão para adicionar ativos de depuração ou restaurar o projeto.)
1. Abra o Terminal Integrado no VS Code (usando o menu **Exibir > Terminal Integrado**).
1. Execute esse comando (certifique-se de que **webfrontend** seja a pasta atual):

    ```cmd
    azds prep --public
    ```

O comando `azds prep` da CLI do Azure gera ativos de Docker e Kubernetes com configurações padrão:
* `./Dockerfile` descreve a imagem de contêiner do aplicativo e como o código-fonte é compilado e executado no contêiner.
* Um [Gráfico Helm](https://docs.helm.sh) em `./charts/webfrontend` descreve como implantar o contêiner no Kubernetes.

Por enquanto, não é necessário entender o conteúdo completo desses arquivos. Vale ressaltar, no entanto, que **os mesmos ativos de configuração como código do Kubernetes e do Docker podem ser usados desde o desenvolvimento até a produção, fornecendo assim melhor consistência em diferentes ambientes.**
 
Um arquivo nomeado `./azds.yaml` também é gerado pelo comando `prep` e é o arquivo de configuração dos Azure Dev Spaces. Ele complementa os artefatos do Docker e do Kubernetes com configuração adicional que permite uma experiência de desenvolvimento iterativo no Azure.

## <a name="build-and-run-code-in-kubernetes"></a>Compilar e executar um código no Kubernetes
Vamos executar nosso código! Na janela do terminal, execute este comando na **pasta de código raiz**, webfrontend:

```cmd
azds up
```

Fique atento à saída do comando; você verá várias coisas durante sua execução:
- O código-fonte é sincronizado com o espaço de desenvolvimento no Azure.
- Uma imagem de contêiner é criada no Azure, conforme especificado pelos ativos do Docker na pasta de código.
- Os objetos do Kubernetes que utilizam a imagem de contêiner são criados, conforme especificado pelo gráfico do Helm na pasta de código.
- As informações sobre os pontos de extremidade do contêiner são exibidas. Em nosso caso, estamos esperando uma URL HTTP pública.
- Supondo que os estágios acima foram concluídos com êxito, você deverá começar a ver a saída de `stdout` (e `stderr`) conforme o contêiner é iniciado.

> [!Note]
> Essas etapas levarão mais tempo na primeira vez em que o comando `up` for executado, mas as execuções seguintes deverão ser mais rápidas.

### <a name="test-the-web-app"></a>Testar o aplicativo Web
Examine a saída do console para obter informações sobre a URL pública criada com o comando `up`. Ela estará neste formato: 

```
(pending registration) Service 'webfrontend' port 'http' will be available at <url>
Service 'webfrontend' port 'http' is available at http://webfrontend.1234567890abcdef1234.eus.azds.io/
Service 'webfrontend' port 80 (TCP) is available at 'http://localhost:<port>'
```

Identifique a URL pública do serviço na saída do comando `up`. Termina em `.azds.io`. No exemplo acima, a URL pública é `http://webfrontend.1234567890abcdef1234.eus.azds.io/`.

Para ver seu aplicativo Web, abra a URL pública em um navegador. Além disso, observe a saída de `stdout` e `stderr` é transmitida para a janela do terminal *azds trace* enquanto você interage com o aplicativo Web. Também é possível exibir informações de controle de solicitações HTTP à medida que passam pelo sistema. Isso facilita o acompanhamento de chamadas de vários serviços complexas durante o desenvolvimento. A instrumentação adicionada pelo Dev Spaces fornece essa solicitação de acompanhamento.

> [!Note]
> Além da URL pública, você pode usar a URL `http://localhost:<portnumber>` alternativa exibida na saída do console. Se você usar a URL de host local, poderá parecer que o contêiner está sendo executado localmente, mas, na verdade, ele está sendo executado no Azure. O Azure Dev Spaces usa Kubernetes com a funcionalidade de *encaminhamento de porta* para mapear a porta do localhost para o contêiner em execução no AKS. Isso facilita a interação com o serviço em seu computador local.

### <a name="update-a-content-file"></a>Atualizar um arquivo de conteúdo
O Azure Dev Spaces não serve apenas para executar o código em Kubernetes; ele também serve para permitir que você veja as alterações de código entrarem em vigor de forma rápida e iterativa em um ambiente Kubernetes na nuvem.

1. Localize o arquivo `./public/index.html` e edite o HTML. Por exemplo, altere a cor de fundo da página para um tom de azul [na linha 15](https://github.com/Azure/dev-spaces/blob/master/samples/nodejs/getting-started/webfrontend/public/index.html#L15):

    ```html
    <body style="background-color: #95B9C7; margin-left:10px; margin-right:10px;">
    ```

1. Salve o arquivo. Em seguida, na janela do Terminal, você verá uma mensagem informando que um arquivo no contêiner em execução foi atualizado.
1. Volte para o navegador e atualize a página. Você verá a cor atualizada.

O que aconteceu? Edições em arquivos de conteúdo, como HTML e CSS, não exigem a reinicialização do processo do Node.js em portanto, um comando `azds up` ativo sincronizam automaticamente os arquivos de conteúdo modificados diretamente no contêiner em execução no Azure, permitindo uma maneira rápida de ver suas edições de conteúdo.

### <a name="test-from-a-mobile-device"></a>Testar de um dispositivo móvel
Abra o aplicativo Web em um dispositivo móvel usando a URL pública para webfrontend. Você deseja copiar e enviar a URL do desktop para seu dispositivo para evitar a inserção do endereço longo. Quando o aplicativo Web é carregado em um dispositivo móvel, você verá que a interface do usuário não é exibida corretamente em um dispositivo pequeno.

Para corrigir isso, você adicionará uma marca META `viewport`:
1. Abra o arquivo `./public/index.html`
1. Adicione uma `viewport` meta tag ao elemento `head` existente que inicia [na linha 6](https://github.com/Azure/dev-spaces/blob/master/samples/nodejs/getting-started/webfrontend/public/index.html#L6):

    ```html
    <head>
        <!-- Add this line -->
        <meta name="viewport" content="width=device-width, initial-scale=1">
    </head>
    ```

1. Salve o arquivo.
1. Atualize o navegador do dispositivo. Agora você deve ver o aplicativo Web renderizado corretamente. 

Este exemplo mostra que alguns problemas não são encontrados até que você teste os dispositivos em que se pretende que o aplicativo seja usado. Com o Azure Dev Spaces, você pode iterar em seu código rapidamente e validar as alterações nos dispositivos de destino.

### <a name="update-a-code-file"></a>Atualizar um arquivo de código
A atualização dos arquivos de código do lado do servidor exige um pouco mais de trabalho, já que um aplicativo Node.js precisa ser reiniciado.

1. Na janela do terminal, pressione `Ctrl+C` (para parar `azds up`).
1. Abra o arquivo de código chamado `server.js` e edite a mensagem de saudação do serviço: 

    ```javascript
    res.send('Hello from webfrontend running in Azure!');
    ```

3. Salve o arquivo.
1. Execute `azds up` na janela do terminal. 

Esse comando recompila a imagem de contêiner e reimplanta o gráfico de Helm. Recarregue a página do navegador para ver as alterações de código em vigor.

Mas existe um *método ainda mais rápido* para desenvolver código, que você irá explorar na próxima seção. 

## <a name="debug-a-container-in-kubernetes"></a>Depurar um contêiner no Kubernetes

Nesta seção, você usará o VS Code para depurar diretamente depurar nosso contêiner em execução no Azure. Você também aprenderá a obter um loop de edição, execução e teste mais rápido.

![](media/common/edit-refresh-see.png)

> [!Note]
> **Caso tenha problemas** a qualquer momento, consulte a seção [Solução de problemas](troubleshooting.md) ou poste um comentário nesta página.

### <a name="initialize-debug-assets-with-the-vs-code-extension"></a>Inicialize os recursos de depuração com a extensão do VS Code
Primeiro, é necessário configurar o projeto de código para que o VS Code comunique-se com o espaço de desenvolvimento no Azure. A extensão do VS Code para o Azure Dev Spaces fornece um comando auxiliar para configurar a configuração de depuração. 

Abra a **Paleta de Comandos** (usando o menu **Exibir | Paleta de Comandos**) e use o preenchimento automático para digitar e selecionar este comando: `Azure Dev Spaces: Prepare configuration files for Azure Dev Spaces`. 

Isso adiciona a configuração de depuração para o Azure Dev Spaces na pasta `.vscode`. Esse comando não é deve ser confundido com o `azds prep`, que configura o projeto para a implantação.

![](media/common/command-palette.png)

### <a name="select-the-azds-debug-configuration"></a>Selecionar a configuração de depuração AZDS
1. Para abrir o modo de exibição Depuração, clique no ícone Depuração na **Barra de Atividades** no lado do VS Code.
1. Selecione **Iniciar Programa (AZDS)** como a configuração de depuração ativa.

![](media/get-started-node/debug-configuration-nodejs2.png)

> [!Note]
> Se você não vir comandos do Azure Dev Spaces na Paleta de Comandos, verifique se [instalou a extensão do VS Code para o Azure Dev Spaces](get-started-nodejs.md#get-kubernetes-debugging-for-vs-code).

### <a name="debug-the-container-in-kubernetes"></a>Depurar o contêiner no Kubernetes
Pressione **F5** para depurar seu código no Kubernetes!

Semelhante ao comando `up`, o código é sincronizado com o ambiente de desenvolvimento quando você inicia a depuração, e um contêiner é criado e implantado no Kubernetes. Desta vez, o depurador está anexado ao contêiner remoto.

> [!Tip]
> A barra de status do VS Code ficará laranja, indicando que o depurador foi anexado. Isso também exibirá uma URL clicável, que você pode usar para abrir rapidamente seu site.

![](media/common/vscode-status-bar-url.png)

Defina um ponto de interrupção em um arquivo de código do lado do servidor, por exemplo, em `app.get('/api'...` na [linha 13 de `server.js`](https://github.com/Azure/dev-spaces/blob/master/samples/nodejs/getting-started/webfrontend/server.js#L13). 

    ```javascript
    app.get('/api', function (req, res) {
        res.send('Hello from webfrontend');
    });
    ```

Atualize a página do navegador ou pressione o botão *Diga Novamente* para poder alcançar o ponto de interrupção e percorrer o código.

Você tem acesso completo às informações de depuração exatamente como teria se o código fosse executado localmente, por exemplo, a pilha de chamadas, as variáveis locais, as informações de exceção etc.

### <a name="edit-code-and-refresh-the-debug-session"></a>Editar código e atualizar a sessão de depuração
Com o depurador ativo, edite o código. Por exemplo, modifique a mensagem de saudação na [linha 13 `server.js`](https://github.com/Azure/dev-spaces/blob/master/samples/nodejs/getting-started/webfrontend/server.js#L13) novamente:

```javascript
app.get('/api', function (req, res) {
    res.send('**** Hello from webfrontend running in Azure! ****');
});
```

Salve o arquivo e, no **painel Ações de depuração**, clique no botão **Reiniciar**. 

![](media/common/debug-action-refresh.png)

Em vez de recompilar e reimplantar uma nova imagem de contêiner sempre que houver edições de código, o que geralmente levará um tempo considerável, o Azure Dev Spaces reiniciará o processo do Node.js entre as sessões de depuração para fornecer um loop de edição/depuração mais rápido.

Atualize o aplicativo Web no navegador ou pressione o botão *Diga Novamente*. Sua mensagem personalizada deverá aparecer na interface de usuário.

### <a name="use-nodemon-to-develop-even-faster"></a>Use NodeMon para desenvolver ainda mais rapidamente
*Nodemon* é uma ferramenta popular que os desenvolvedores de Node.js usam para desenvolvimento rápido. Em vez de reiniciar manualmente o processo do Node sempre que for feita uma edição de código do lado do servidor, os desenvolvedores geralmente configurarão o projeto do Node para fazer com que o *nodemon* monitore alterações de arquivo e reinicie automaticamente o processo do servidor. Nesse estilo de trabalho, o desenvolvedor apenas atualiza o navegador depois de fazer uma edição de código.

Com o Azure Dev Spaces, você pode usar vários dos mesmos fluxos de trabalho de desenvolvimento usados no desenvolvimento local. Para ilustrar essa questão, o projeto de exemplo `webfrontend` foi configurado para usar *nodemon* (ele é configurado como uma dependência de desenvolvimento em `package.json`).

Experimente as etapas a seguir:
1. Pare o depurador do VS Code.
1. Clique no ícone Depuração na **Barra de Atividades** no lado do VS Code. 
1. Selecione **Anexar (AZDS)** como a configuração de depuração ativa.
1. Pressione F5.

Nessa configuração, o contêiner está configurado para iniciar o *nodemon*. Quando forem feitas edições de código do servidor, *nodemon* reinicia automaticamente o processo do Node, assim como faz quando desenvolve localmente. 
1. Edite a mensagem de saudação novamente em `server.js` e salve o arquivo.
1. Atualize o navegador ou clique no botão *Diga novamente* para ver as alterações entrarem em vigor!

**Agora você tem um método para iterar em código rapidamente e depurar diretamente no Kubernetes!** Em seguida, você verá como você pode criar e chamar um segundo contêiner.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Saiba mais sobre o desenvolvimento de vários serviços](multi-service-nodejs.md)


[supported-regions]: about.md#supported-regions-and-configurations