---
title: 'Início Rápido: Criar um aplicativo Web Node.js'
description: Implante seu primeiro Olá, Mundo em Node.js no Serviço de Aplicativo do Azure em minutos.
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.topic: quickstart
ms.date: 08/01/2020
ms.custom: mvc, devcenter, seodec18
zone_pivot_groups: app-service-platform-windows-linux
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 7cd9add699d9bd4a3eff9cdcf1e65af0d754b70a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101747260"
---
# <a name="create-a-nodejs-web-app-in-azure"></a>Criar um aplicativo Web do Node.js no Azure

Introdução ao <abbr title="Um serviço baseado em HTTP para hospedagem de aplicativos Web, APIs REST e aplicativos de back-end móveis.">Serviço de aplicativo do Azure</abbr> criando um aplicativo Node.js/Express localmente usando o Visual Studio Code e implantando o aplicativo na nuvem do Azure. Como usa uma <abbr title="No Serviço de Aplicativo do Azure, uma camada base na qual seu aplicativo é executado nas mesmas VMs que outros aplicativos, incluindo os aplicativos de outros clientes. Essa camada é voltada para desenvolvimento e teste.">camada gratuita,</abbr>você não incorre em custos para concluir este início rápido.

## <a name="1-prepare-your-environment"></a>1. Prepare o seu ambiente

- Uma conta do Azure com uma assinatura ativa <abbr title="Uma assinatura do Azure é um contêiner lógico usado para provisionar recursos no Azure. Ela contém os detalhes de todos os seus recursos, como VMs (máquinas virtuais), bancos de dados e muito mais.">subscription</abbr>. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-app-service-extension&mktingSource=vscode-tutorial-app-service-extension).
- <a href="https://git-scm.com/" target="_blank">Instalar o Git</a>
- [Node.js e npm](https://nodejs.org). Execute o comando `node --version` para verificar se o Node.js está instalado.
- [Visual Studio Code](https://code.visualstudio.com/).
- A [extensão do Serviço de Aplicativo do Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice) para Visual Studio Code.

[Relatar um problema](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&prepare-your-environment)




<br>
<hr/>

## <a name="2-clone-and-run-a-local-nodejs-application"></a>2. Clonar e executar um aplicativo Node.js local

1. No computador local, abra um terminal e clone o repositório de exemplo:

    ```bash
    git clone https://github.com/Azure-Samples/nodejs-docs-hello-world
    ```

1. Navegue até a nova pasta do aplicativo:

    ```bash
    cd nodejs-docs-hello-world
    ```

1. Instalar as dependências:

    ```bash
    npm install
    ```

1. Inicie o aplicativo para testá-lo localmente:

    ```bash
    npm start
    ```
    
1. Abra o navegador e navegue até `http://localhost:1337`. O navegador deverá exibir "Olá, Mundo!".

1. Selecione <kbd>CTRL</kbd> + <kbd>C</kbd> no terminal para interromper o servidor.

[Relatar um problema](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&prepare-your-environment)


<br>
<hr/>




<!-- VS Code extension works differently for Windows/Linus - Step 3 -->

::: zone pivot="platform-windows"  

[!INCLUDE [Windows](./includes/quickstart-nodejs-uiex-windows.md)]


::: zone-end

::: zone pivot="platform-linux"  

[!INCLUDE [Windows](./includes/quickstart-nodejs-uiex-linux.md)]

::: zone-end


## <a name="4-viewing-logs-from-visual-studio-code"></a>4. Exibindo logs do Visual Studio Code

Veja os  <abbr title="Chamadas a `console.log` no aplicativo são exibidas na Janela de Saída no Visual Studio Code.">log</abbr> do aplicativo do Serviço de Aplicativo em execução.

1. Localize o aplicativo no explorador do **SERVIÇO DE APLICATIVO DO AZURE**, clique com o botão direito do mouse no nome do aplicativo e escolha **Iniciar Streaming de Logs**.

1. A janela de saída do Visual Studio Code é aberta.

    ![Exibir logs de streaming](./media/quickstart-nodejs/view-logs.png)

    :::image type="content" source="./media/quickstart-nodejs/enable-restart.png" alt-text="Captura de tela do prompt do VS Code para habilitar o registro em log do arquivo e reiniciar o aplicativo Web, com o botão Sim selecionado.":::

1. Após alguns segundos, vocês verá uma mensagem indicando que você está conectado ao serviço de streaming de log. 
1. Atualize a página algumas vezes para ver mais atividades.

    <pre class="is-monospace is-size-small has-padding-medium has-background-tertiary has-text-tertiary-invert">
    2020-09-20 20:37:39.574 INFO  - Initiating warmup request to container msdocs-vscode-node_2_00ac292a for site msdocs-vscode-node
    2020-09-20 20:37:55.011 INFO  - Waiting for response to warmup request for container msdocs-vscode-node_2_00ac292a. Elapsed time = 15.4373071 sec
    2020-09-20 20:38:08.233 INFO  - Container msdocs-vscode-node_2_00ac292a for site msdocs-vscode-node initialized successfully and is ready to serve requests.
    2020-09-20T20:38:21  Startup Request, url: /Default.cshtml, method: GET, type: request, pid: 61,1,7, SCM_SKIP_SSL_VALIDATION: 0, SCM_BIN_PATH: /opt/Kudu/bin, ScmType: None
    </pre>

<br>

[Relatar um problema](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&prepare-your-environment)

<br>
<hr/>

## <a name="5-clean-up-resources"></a>5. Limpar os recursos

Localize o aplicativo no explorador do **SERVIÇO DE APLICATIVO DO AZURE**, clique com o botão direito do mouse no nome do aplicativo e escolha **Excluir**. 

:::image type="content" source="./media/quickstart-nodejs/delete-resource-ieux.png" alt-text="Localize o aplicativo no explorador do `**`SERVIÇO DE APLICATIVO DO AZURE`**`, clique com o botão direito do mouse no nome do aplicativo e escolha `Excluir`":::

## <a name="next-steps"></a>Próximas etapas

Parabéns, você concluiu com êxito este início rápido! Você pode implantar alterações nesse aplicativo usando o mesmo processo e escolhendo o aplicativo existente em vez de criar um.

Em seguida, confira as outras extensões do Azure.

* [Cosmos DB](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
* [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
* [Ferramentas do Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
* [Ferramentas da CLI do Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
* [Ferramentas do Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

Ou obtenha todas elas instalando o pacote de extensão [Pacote de Nós para Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack).