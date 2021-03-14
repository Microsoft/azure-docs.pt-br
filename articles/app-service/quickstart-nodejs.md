---
title: 'Início Rápido: Criar um aplicativo Web Node.js'
description: Implante seu primeiro Olá, Mundo em Node.js no Serviço de Aplicativo do Azure em minutos.
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.topic: quickstart
ms.date: 08/01/2020
ms.custom: mvc, devcenter, seodec18
zone_pivot_groups: app-service-platform-windows-linux
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./quickstart-nodejs-uiex
ms.openlocfilehash: 6c6f0543dcfbecd16ba4176272f928ffd0eb54de
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101735107"
---
# <a name="create-a-nodejs-web-app-in-azure"></a>Criar um aplicativo Web do Node.js no Azure

::: zone pivot="platform-windows"  

Introdução ao Serviço de Aplicativo do Azure com a criação de um aplicativo Node.js/Express localmente usando o Visual Studio Code e, em seguida, a implantação do aplicativo na nuvem. Como você usa uma Camada gratuita do Serviço de Aplicativo, não é gerado nenhum custo para a conclusão deste início rápido.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-app-service-extension&mktingSource=vscode-tutorial-app-service-extension).
- <a href="https://git-scm.com/" target="_blank">Instalar o Git</a>
- [Node.js e npm](https://nodejs.org). Execute o comando `node --version` para verificar se o Node.js está instalado.
- [Visual Studio Code](https://code.visualstudio.com/).
- A [extensão do Serviço de Aplicativo do Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice) para Visual Studio Code.

## <a name="clone-and-run-a-local-nodejs-application"></a>Clonar e executar um aplicativo Node.js local

1. No computador local, abra um terminal e clone o repositório de exemplo:

    ```bash
    git clone https://github.com/Azure-Samples/nodejs-docs-hello-world
    ```

1. Navegue até a nova pasta do aplicativo:

    ```bash
    cd nodejs-docs-hello-world
    ```

1. Inicie o aplicativo para testá-lo localmente:

    ```bash
    npm start
    ```
    
1. Abra o navegador e navegue até `http://localhost:1337`. O navegador deverá exibir "Olá, Mundo!".

1. Selecione **CTRL**+**C** no terminal para interromper o servidor.

> [!div class="nextstepaction"]
> [Encontrei um problema](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=create-app)

## <a name="deploy-the-app-to-azure"></a>Implantar o aplicativo no Azure

Nesta seção, você implantará o aplicativo Node.js no Azure usando o VS Code e a extensão do Serviço de Aplicativo do Azure.

1. No terminal, verifique se você está na pasta *nodejs-docs-hello-world* e, em seguida, inicie o Visual Studio Code com o seguinte comando:

    ```bash
    code .
    ```

1. Na barra de atividade do VS Code, selecione o logotipo do Azure para mostrar o gerenciador do **SERVIÇO DE APLICATIVO DO AZURE**. Selecione **Entrar no Azure...** e siga as instruções. (Confira [Solução de problemas de entrada no Azure](#troubleshooting-azure-sign-in) abaixo em caso de erros.) Após a conexão, o gerenciador deverá mostrar o nome da sua assinatura do Azure.

    ![Entrar no Azure](media/quickstart-nodejs/sign-in.png)

1. No gerenciador do **SERVIÇO DE APLICATIVO DO AZURE** do VS Code, selecione o ícone de seta azul para cima para implantar o aplicativo no Azure. (Invoque também o mesmo comando na **paleta de comandos** (**CTRL**+**Shift**+**P**) digitando 'implantar no aplicativo Web' e escolhendo **Serviço de Aplicativo do Azure: Implantar no Aplicativo Web**).

    :::image type="content" source="media/quickstart-nodejs/deploy.png" alt-text="Captura de tela do serviço de Aplicativo do Azure no VS Code que mostra o ícone de seta azul selecionado.":::
        
1. Escolha a pasta *nodejs-docs-hello-world*.

1. Escolha uma opção de criação com base no sistema operacional no qual você deseja implantar:

    - Linux: Escolha **Criar Aplicativo Web**
    - Windows: Escolha **Criar Aplicativo Web... Avançado**

1. Digite um nome global exclusivo para o aplicativo Web e selecione **Enter**. O nome precisa ser exclusivo em todo o Azure e usar somente caracteres alfanuméricos ('A-Z', 'a-z' e '0-9') e hifens ('-').

1. Se estiver direcionando para o Linux, selecione uma versão do Node.js quando solicitado. Uma versão **LTS** é recomendada.

1. Se o destino for o Windows, siga os avisos adicionais:
    1. Selecione **Criar um grupo de recursos** e, em seguida, insira um nome para o grupo de recursos, como `AppServiceQS-rg`.
    1. Para o sistema operacional, selecione **Windows**.
    1. Selecione **Criar Plano do Serviço de Aplicativo**, insira um nome para o plano (como `AppServiceQS-plan`) e, em seguida, selecione **F1 Gratuito** como o tipo de preço.
    1. Escolha **Ignorar por enquanto** o Application Insights for solicitado.
    1. Escolha uma região perto de você ou perto dos recursos que deseja acessar.

1. Depois de responder a todos os avisos, o VS Code mostrará os recursos do Azure que estão sendo criados para o aplicativo no pop-up de notificação.

    Na implantação no Linux, selecione **Sim** quando precisar atualizar a configuração para executar `npm install` no servidor Linux de destino.

    ![Aviso para atualizar a configuração no servidor Linux de destino](media/quickstart-nodejs/server-build.png)

1. Selecione **Sim** quando for exibido o aviso **Sempre implantar o workspace "nodejs-docs-hello-world" em (nome do aplicativo)"** . A seleção da opção **Sim** instrui o VS Code a ter automaticamente como destino o mesmo aplicativo Web do Serviço de Aplicativo com as implantações seguintes.

1. Na implantação no Linux, selecione **Procurar no Site** no aviso para ver o aplicativo Web recém-implantado após a conclusão da implantação. O navegador deverá exibir "Olá, Mundo!"

1. Se estiver fazendo a implantação no Windows, primeiro, você precisará definir o número de versão do Node.js para o aplicativo Web:

    1. No VS Code, expanda o nó do novo serviço de aplicativo, clique com o botão direito do mouse em **Configurações de Aplicativo** e selecione **Adicionar Nova Configuração...** :

        ![Comando Adicionar configuração de aplicativo](media/quickstart-nodejs/add-setting.png)

    1. Insira `WEBSITE_NODE_DEFAULT_VERSION` para a chave de configuração.
    1. Insira `10.15.2` para o valor da configuração.
    1. Clique com o botão direito do mouse no nó do serviço de aplicativo e selecione **Reiniciar**

        ![Comando Reiniciar serviço de aplicativo](media/quickstart-nodejs/restart.png)

    1. Clique com o botão direito do mouse no nó do serviço de aplicativo mais uma vez e selecione **Procurar no Site**.

> [!div class="nextstepaction"]
> [Encontrei um problema](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=deploy-app)

### <a name="troubleshooting-azure-sign-in"></a>Solução de problemas de entrada no Azure

Se for exibido o erro **"Não é possível encontrar a assinatura com o nome [ID da assinatura]"** quando você entrar no Azure, isso poderá ocorrer porque talvez você esteja atrás de um proxy e não consiga acessar a API do Azure. Configure as variáveis de ambiente `HTTP_PROXY` e `HTTPS_PROXY` com suas informações de proxy no seu terminal usando o `export`.

```bash
export HTTPS_PROXY=https://username:password@proxy:8080
export HTTP_PROXY=http://username:password@proxy:8080
```

Se a configuração das variáveis de ambiente não corrigir o problema, entre em contato conosco selecionando o botão **Encontrei um problema** acima.

### <a name="update-the-app"></a>Atualizar o aplicativo

Você pode implantar alterações nesse aplicativo fazendo edições no VS Code, salvando os arquivos e, em seguida, usando o mesmo processo que antes, escolhendo apenas o aplicativo existente, em vez de criar outro.

## <a name="viewing-logs"></a>Exibir logs

Veja a saída de log (chamadas a `console.log`) no aplicativo diretamente na Janela de Saída do VS Code.

1. No gerenciador do **SERVIÇO DE APLICATIVO DO AZURE**, clique com o botão direito do mouse no nó do aplicativo e escolha **Iniciar Streaming de Logs**.

    ![Iniciar Streaming de Logs](media/quickstart-nodejs/view-logs.png)

1. Quando solicitado, escolha habilitar o registro em log e reinicie o aplicativo. Depois que o aplicativo for reiniciado, a janela de saída do VS Code será aberta com uma conexão com o fluxo de log. 

    :::image type="content" source="media/quickstart-nodejs/enable-restart.png" alt-text="Captura de tela do prompt do Visual Studio Code para habilitar o registro em log e reiniciar o aplicativo com o botão Sim selecionado.":::

1. Após alguns segundos, a Janela de Saída mostrará uma mensagem indicando que você está conectado ao serviço de streaming de log. Gere mais atividades de saída atualizando a página no navegador.

    <pre>
    Connecting to log stream...
    2020-03-04T19:29:44  Welcome, you are now connected to log-streaming service. The default timeout is 2 hours.
    Change the timeout with the App Setting SCM_LOGSTREAM_TIMEOUT (in seconds).
    </pre>

> [!div class="nextstepaction"]
> [Encontrei um problema](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=tailing-logs)

## <a name="next-steps"></a>Próximas etapas

Parabéns, você concluiu com êxito este início rápido!

> [!div class="nextstepaction"]
> [Tutorial: Aplicativo Node.js com o MongoDB](tutorial-nodejs-mongodb-app.md)

> [!div class="nextstepaction"]
> [Configurar o aplicativo Node.js](configure-language-nodejs.md)

Confira as outras extensões do Azure.

* [Cosmos DB](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
* [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
* [Ferramentas do Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
* [Ferramentas da CLI do Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
* [Ferramentas do Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

Ou obtenha todas elas instalando o pacote de extensão [Pacote de Nós para Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack).
::: zone-end

::: zone pivot="platform-linux"  
## <a name="prerequisites"></a>Pré-requisitos

Se você não tiver uma conta do Azure, [inscreva-se hoje mesmo](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-app-service-extension&mktingSource=vscode-tutorial-app-service-extension) para obter uma conta gratuita com US$ 200 em créditos do Azure para experimentar uma combinação de serviços.

É preciso que o [Visual Studio Code](https://code.visualstudio.com/) esteja instalado junto com [Node.js e npm](https://nodejs.org/en/download), o gerenciador de pacotes do Node.js.

Será necessário instalar a [extensão do Serviço de Aplicativo do Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice), que você pode usar para criar, gerenciar e implantar Aplicativos Web Linux na PaaS (Plataforma como Serviço) do Azure.

### <a name="sign-in"></a>Entrar

Depois que a extensão for instalada, faça logon em sua conta do Azure. Na Barra de Atividades, clique no logotipo do Azure para mostrar o explorador **SERVIÇO DE APLICATIVO DO AZURE**. Selecione **Entrar no Azure...** e siga as instruções.

![entrar no Azure](./media/quickstart-nodejs/sign-in.png)

### <a name="troubleshooting"></a>Solução de problemas

Se você vir o erro **"Não é possível encontrar a assinatura com o nome [ID da assinatura]"** , talvez seja porque você está atrás de um proxy e não consegue acessar a API do Azure. Configure as variáveis de ambiente `HTTP_PROXY` e `HTTPS_PROXY` com suas informações de proxy no seu terminal usando o `export`.

```sh
export HTTPS_PROXY=https://username:password@proxy:8080
export HTTP_PROXY=http://username:password@proxy:8080
```

Se a configuração das variáveis de ambiente não corrigir o problema, contate-nos selecionando o botão **Encontrei um problema** abaixo.

### <a name="prerequisite-check"></a>Verificação de pré-requisitos

Antes de continuar, verifique se você tem todos os pré-requisitos instalados e configurados.

No VS Code, você deverá ver o endereço de email do Azure na Barra de Status e sua assinatura no explorador **SERVIÇO DE APLICATIVO DO AZURE**.

> [!div class="nextstepaction"]
> [Encontrei um problema](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=getting-started)

## <a name="create-your-nodejs-application"></a>Crie seu aplicativo Node.js

Em seguida, crie um aplicativo Node.js que possa ser implantado na nuvem. Este início rápido usa um gerador de aplicativos para realizar scaffold rapidamente do aplicativo de um terminal.

> [!TIP]
> Se você já tiver concluído o [tutorial do Node.js](https://code.visualstudio.com/docs/nodejs/nodejs-tutorial), poderá pular para [Implantar no Azure](#deploy-to-azure).

### <a name="scaffold-a-new-application-with-the-express-generator"></a>Crie uma plataforma para um novo aplicativo com o Gerador Express

[Express](https://www.expressjs.com) é uma estrutura popular para criar e executar aplicativos Node.js. Você pode realizar scaffold (criar) de um novo aplicativo Express usando a ferramenta [Gerador Express](https://expressjs.com/en/starter/generator.html). O Gerador Express é enviado como um módulo npm e pode ser executado diretamente (sem instalação) usando a ferramenta de linha de comando npm `npx`.

```bash
npx express-generator myExpressApp --view pug --git
```

Os parâmetros `--view pug --git` instruem o gerador a usar o mecanismo de modelo [pug](https://pugjs.org/api/getting-started.html) (anteriormente conhecido como `jade`) e a criar um arquivo `.gitignore`.

Para instalar todas as dependências do aplicativo, vá para a nova pasta e execute `npm install`.

```bash
cd myExpressApp
npm install
```

### <a name="run-the-application"></a>Executar o aplicativo

Em seguida, verifique se o aplicativo é executado. No terminal, inicie o aplicativo usando o comando `npm start` para iniciar o servidor.

```bash
npm start
```

Agora, abra o navegador e navegue até `http://localhost:3000`, em que você verá algo assim:

![Executar aplicativo Express](./media/quickstart-nodejs/express.png)

> [!div class="nextstepaction"]
> [Encontrei um problema](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=create-app)

## <a name="deploy-to-azure"></a>Implantar no Azure

Nesta seção, você implantará seu aplicativo do Node.js usando o VS Code e a extensão do Serviço de Aplicativo do Azure. Este guia de início rápido usa o modelo de implantação mais básico em que seu aplicativo é compactado e implantado em um aplicativo Web do Azure no Linux.

### <a name="deploy-using-azure-app-service"></a>Implantar usando o Serviço de Aplicativo do Azure

Primeiro, abra a pasta do aplicativo no VS Code.

```bash
code .
```

No explorador **SERVIÇO DE APLICATIVO DO AZURE**, selecione o ícone de seta azul para cima para implantar seu aplicativo no Azure.

:::image type="content" source="./media/quickstart-nodejs/deploy.png" alt-text="Captura de tela do menu do Serviço de Aplicativo do Azure no Visual Studio Code com a seta de implantação azul selecionada.":::

> [!TIP]
> Você também pode implantar na **Paleta de Comandos** (CTRL + SHIFT + P) digitando “implantar no aplicativo Web” e executando o comando **Serviço de Aplicativo do Azure: Implantar no comando** do aplicativo Web.

1. Escolha o diretório que está aberto no momento, `myExpressApp`.

1. Escolha **Criar aplicativo Web**, que é implantado no Serviço de Aplicativo no Linux por padrão.

1. Digite um nome globalmente exclusivo para seu Aplicativo Web e pressione ENTER. Os caracteres válidos para um nome de aplicativo são “a-z”, “0-9” e “-”.

1. Escolha a **versão do Node.js**; LTS é recomendado.

    O canal de notificação mostra os recursos do Azure que estão sendo criados para seu aplicativo.

1. Selecione **Sim** quando solicitado a atualizar sua configuração para ser executar `npm install` no servidor de destino. Seu aplicativo será implantado.

    :::image type="content" source="./media/quickstart-nodejs/server-build.png" alt-text="Captura de tela do prompt para atualizar a sua configuração no servidor de destino com o botão Sim selecionado.":::

1. Quando a implantação é iniciada, você deve atualizar o workspace para que futuras implantações direcionem automaticamente o mesmo Aplicativo Web do Serviço de Aplicativo. Escolha **Sim** para que suas alterações sejam implantadas no aplicativo correto.

    :::image type="content" source="./media/quickstart-nodejs/save-configuration.png" alt-text="Captura de tela do prompt para atualizar o seu workspace com o botão Sim selecionado.":::

> [!TIP]
> Verifique se seu aplicativo está escutando na porta fornecida pela variável de ambiente PORT: `process.env.PORT`.

### <a name="browse-the-app-in-azure"></a>Procurar o aplicativo no Azure

Quando a implantação for concluída, selecione **Procurar no Site** no prompt para exibir seu aplicativo Web implantado recentemente.

### <a name="troubleshooting"></a>Solução de problemas

Se você vir o erro **"Você não tem permissão para exibir este diretório ou página."** , o aplicativo provavelmente terá falhado em iniciar corretamente. Passe para a próxima seção e exiba a saída do log para localizar e corrigir o erro. Se você não conseguir corrigi-lo, contate-nos selecionando o botão **Encontrei um problema** abaixo. Temos o prazer de ajudar!

> [!div class="nextstepaction"]
> [Encontrei um problema](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=deploy-app)

### <a name="update-the-app"></a>Atualizar o aplicativo

Você pode implantar alterações nesse aplicativo usando o mesmo processo e escolhendo o aplicativo existente em vez de criar um.

## <a name="viewing-logs"></a>Exibir logs

Nesta seção, você aprenderá a exibir (ou “seguir”) os logs do aplicativo do Serviço de Aplicativo em execução. Chamadas a `console.log` no aplicativo são exibidas na Janela de Saída no Visual Studio Code.

Localize o aplicativo no explorador **SERVIÇO DE APLICATIVO DO AZURE**, clique com o botão direito do mouse no aplicativo e escolha **Exibir logs de streaming**.

A Janela de Saída do VS Code será aberta com uma conexão com o fluxo de log.

![Exibir logs de streaming](./media/quickstart-nodejs/view-logs.png)

:::image type="content" source="./media/quickstart-nodejs/enable-restart.png" alt-text="Captura de tela do prompt do VS Code para habilitar o registro em log do arquivo e reiniciar o aplicativo Web, com o botão Sim selecionado.":::

Após alguns segundos, vocês verá uma mensagem indicando que você está conectado ao serviço de streaming de log. Atualize a página algumas vezes para ver mais atividades.

<pre>
2019-09-20 20:37:39.574 INFO  - Initiating warmup request to container msdocs-vscode-node_2_00ac292a for site msdocs-vscode-node
2019-09-20 20:37:55.011 INFO  - Waiting for response to warmup request for container msdocs-vscode-node_2_00ac292a. Elapsed time = 15.4373071 sec
2019-09-20 20:38:08.233 INFO  - Container msdocs-vscode-node_2_00ac292a for site msdocs-vscode-node initialized successfully and is ready to serve requests.
2019-09-20T20:38:21  Startup Request, url: /Default.cshtml, method: GET, type: request, pid: 61,1,7, SCM_SKIP_SSL_VALIDATION: 0, SCM_BIN_PATH: /opt/Kudu/bin, ScmType: None
</pre>

> [!div class="nextstepaction"]
> [Encontrei um problema](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=tailing-logs)

## <a name="next-steps"></a>Próximas etapas

Parabéns, você concluiu com êxito este início rápido!

Em seguida, confira as outras extensões do Azure.

* [Cosmos DB](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
* [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
* [Ferramentas do Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
* [Ferramentas da CLI do Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
* [Ferramentas do Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

Ou obtenha todas elas instalando o pacote de extensão [Pacote de Nós para Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack).


::: zone-end
