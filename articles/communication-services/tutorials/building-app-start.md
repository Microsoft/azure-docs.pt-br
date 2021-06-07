---
title: Tutorial – Preparar um aplicativo Web para os Serviços de Comunicação do Azure (Node.js)
titleSuffix: An Azure Communication Services tutorial
description: Saiba como criar um aplicativo Web de linha de base que dá suporte aos Serviços de Comunicação do Azure
author: nmurav
services: azure-communication-services
ms.author: nmurav
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: a5a23d6a06c8cdff4deabac5251597b7ffe0c833
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105728038"
---
# <a name="tutorial-prepare-a-web-app-for-azure-communication-services-nodejs"></a>Tutorial: Preparar um aplicativo Web para os Serviços de Comunicação do Azure (Node.js)

Você pode usar os Serviços de Comunicação do Azure para adicionar a comunicação em tempo real aos aplicativos. Neste tutorial, você aprenderá a configurar um aplicativo Web que dá suporte aos Serviços de Comunicação do Azure. Este é um tutorial introdutório destinado a novos desenvolvedores que desejam começar a usar a comunicação em tempo real.

Ao final deste tutorial, você terá um aplicativo Web de linha de base configurado com os SDKs dos Serviços de Comunicação do Azure. Então, você poderá usar esse aplicativo para começar a criar sua solução de comunicação em tempo real.

Sinta-se à vontade para visitar a [página dos Serviços de Comunicação do Azure no GitHub](https://github.com/Azure/communication) para fornecer comentários.

Neste tutorial, você aprenderá como:
> [!div class="checklist"]
> * Configurar o ambiente de desenvolvimento.
> * Configurar um servidor Web local.
> * Adicionar os pacotes de Serviços de Comunicação do Azure ao seu site.
> * Publicar o seu site nos sites estáticos do Azure.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura ativa. Para obter detalhes, confira [Criar uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). A conta gratuita oferece US$ 200 em créditos Azure para você experimentar qualquer combinação de serviços.
- [Visual Studio Code](https://code.visualstudio.com/), para editar códigos no ambiente de desenvolvimento local.
- [webpack](https://webpack.js.org/) para agrupar e hospedar localmente o seu código.
- [Node.js](https://nodejs.org/en/) para instalar e gerenciar dependências como os SDKs e o webpack dos Serviços de Comunicação do Azure.
- [NVM e npm](/windows/nodejs/setup-on-windows) para processar o controle de versão.
- A [extensão do Armazenamento do Azure para](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage) Visual Studio Code. Você precisa dessa extensão para publicar seu aplicativo no Armazenamento do Azure. [Leia mais sobre como hospedar sites estáticos no Armazenamento do Azure](../../storage/blobs/storage-blob-static-website.md).
- A [extensão do Serviço de Aplicativo do Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice). A extensão permite implantar sites com a opção de configurar a CI/CD (integração contínua e entrega contínua) totalmente gerenciada.
- A [extensão do Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) para criar aplicativos sem servidor próprios. Por exemplo, você pode hospedar o aplicativo de autenticação no Azure Functions.
- Um recurso e uma cadeia de conexão ativos dos Serviços de Comunicação. [Saiba como criar um recurso dos Serviços de Comunicação](../quickstarts/create-communication-resource.md).
- Um token de acesso do usuário. Para obter instruções, confira o [início rápido para criar e gerenciar tokens de acesso](../quickstarts/access-tokens.md?pivots=programming-language-javascript) ou o [tutorial para criar um serviço de autenticação confiável](./trusted-service-tutorial.md).


## <a name="configure-your-development-environment"></a>Configurar seu ambiente de desenvolvimento

Seu ambiente de desenvolvimento local será configurado da seguinte maneira:

:::image type="content" source="./media/step-one-pic-one.png" alt-text="Diagrama que ilustra a arquitetura do ambiente de desenvolvimento.":::


### <a name="install-nodejs-nvm-and-npm"></a>Instalar o Node.js, o NVM e o NPM

Usaremos o Node.js para baixar e instalar várias dependências necessárias para o aplicativo do lado do cliente. Usaremos isso para gerar arquivos estáticos que, depois, hospedaremos no Azure. Portanto, você não precisa se preocupar em configurá-lo no seu servidor.

Os desenvolvedores do Windows podem seguir [este tutorial do Node.js](/windows/nodejs/setup-on-windows) para configurar o Node, o NVM e o NPM. 

Este tutorial se baseia na versão LTS 12.20.0. Depois de instalar o NVM, use o seguinte comando do PowerShell para implantar a versão que deseja usar:

```PowerShell
nvm list available
nvm install 12.20.0
nvm use 12.20.0
```

:::image type="content" source="./media/step-one-pic-two.png" alt-text="Captura de tela que mostra os comandos para implantar uma versão do Node.":::

### <a name="configure-visual-studio-code"></a>Configurar o Visual Studio Code

Baixe o [Visual Studio Code](https://code.visualstudio.com/) em uma das [plataformas compatíveis](https://code.visualstudio.com/docs/supporting/requirements#_platforms).

### <a name="create-a-workspace-for-your-azure-communication-services-projects"></a>Criar um workspace para seus projetos dos Serviços de Comunicação do Azure

Crie uma pasta para armazenar os arquivos de projeto, da seguinte maneira: `C:\Users\Documents\ACS\CallingApp`. No Visual Studio Code, selecione **Arquivo** > **Adicionar Pasta ao Workspace** e adicione a pasta ao workspace em questão.

:::image type="content" source="./media/step-one-pic-three.png" alt-text="Captura de tela que mostra as seleções para adicionar um arquivo a um workspace.":::

Acesse **EXPLORER** no painel esquerdo e você verá a pasta `CallingApp` no workspace **Sem título**.

:::image type="content" source="./media/step-one-pic-four.png" alt-text="Captura de tela que mostra o Explorer e o workspace sem título.":::

Fique à vontade para atualizar o nome do workspace. Valide a versão do Node.js clicando com o botão direito do mouse na pasta `CallingApp` e selecionando **Abrir no Terminal Integrado**.

:::image type="content" source="./media/step-one-pic-five.png" alt-text="Captura de tela que mostra a seleção para abrir uma pasta em um terminal integrado.":::

No terminal, digite o seguinte comando para validar a versão do Node.js instalada na etapa anterior:

```JavaScript
node --version
```

:::image type="content" source="./media/step-one-pic-six.png" alt-text="Captura de tela que mostra a validação da versão do Node.":::

### <a name="install-azure-extensions-for-visual-studio-code"></a>Instalar as extensões do Azure para Visual Studio Code

Instale a [extensão do Armazenamento do Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage) por meio do marketplace do Visual Studio ou com o Visual Studio Code (**Exibir** > **Extensões** > **Armazenamento do Azure**).

:::image type="content" source="./media/step-one-pic-seven.png" alt-text="Captura de tela que mostra o botão para instalar a extensão Armazenamento do Azure.":::

Siga as mesmas etapas para as extensões do [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) e do [Serviço de Aplicativo do Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice).


## <a name="set-up-a-local-web-server"></a>Configurar um servidor Web local

### <a name="create-an-npm-package"></a>Criar um pacote NPM

No terminal, com base no caminho da pasta do workspace, insira:

``` console
npm init -y
```

Esse comando inicializa um novo pacote npm e adiciona `package.json` à pasta raiz do projeto.

:::image type="content" source="./media/step-one-pic-eight.png" alt-text="Captura de tela que mostra o pacote J S O N.":::

Para obter mais documentação sobre o `npm init`, confira a [página de documentos do NPM para esse comando](https://docs.npmjs.com/cli/v6/commands/npm-init).

### <a name="install-webpack"></a>Instalar o webpack

Você pode usar o [webpack](https://webpack.js.org/) para agrupar o código em arquivos estáticos que podem ser implantados no Azure. Ele também tem um servidor de desenvolvimento, que será configurado para uso com o exemplo de chamada.

No terminal, insira o seguinte comando para instalar o webpack:

``` Console
npm install webpack@4.42.0 webpack-cli@3.3.11 webpack-dev-server@3.10.3 --save-dev
```

Este tutorial foi testado com as versões especificadas no comando anterior. A especificação de `-dev` informa ao gerenciador de pacotes que essa dependência se destina ao desenvolvimento e não deve ser incluída no código implantado no Azure.

Você verá dois novos pacotes adicionados ao arquivo `package.json` como `devDependencies`. Os pacotes serão instalados no diretório `./CallingApp/node_modules/`.

:::image type="content" source="./media/step-one-pic-ten.png" alt-text="Captura de tela que mostra a configuração do webpack.":::

### <a name="configure-the-development-server"></a>Configurar o servidor de desenvolvimento

A execução de um aplicativo estático (como o arquivo `index.html`) por meio do navegador usa o protocolo `file://`. Para que os módulos do NPM funcionem corretamente, será necessário dispor do protocolo HTTP usando o webpack como um servidor de desenvolvimento local.

Você deverá criar duas configurações: uma para desenvolvimento e outra para produção. Os arquivos preparados para produção serão minificados, o que significa que removeremos deles espaços em branco e caracteres não usados. Essa configuração é apropriada para cenários de produção em que a latência deve ser minimizada ou quando o código deve ser ocultado.

Você deverá usar a ferramenta `webpack-merge` para trabalhar com [diferentes arquivos de configuração para o webpack](https://webpack.js.org/guides/production/).

Vamos começar com o ambiente de desenvolvimento. Primeiramente, você precisa instalar `webpack merge`. No terminal, execute o seguinte comando:

```Console
npm install --save-dev webpack-merge
```

No arquivo `package.json`, você verá mais uma dependência adicionada a `devDependencies`.

Em seguida, crie um arquivo chamado `webpack.common.js` e adicione o seguinte código:

```JavaScript
const path = require('path');
module.exports ={
    entry: './app.js',
    output: {
        filename:'app.js',
        path: path.resolve(__dirname, 'dist'),
    }
}
```

Então, adicione mais dois arquivos, um para cada configuração:

* `webpack.dev.js`
* `webpack.prod.js`

Agora, modifique o arquivo `webpack.dev.js` adicionando o seguinte código a ele:

```JavaScript
const { merge } = require('webpack-merge');
const common = require('./webpack.common.js');

module.exports = merge(common, {
    mode: 'development',
    devtool: 'inline-source-map',
});
```
Nessa configuração, você importará parâmetros comuns de `webpack.common.js`, mesclará os dois arquivos, definirá o modo como `development` e vai configurar o source map como `inline-source-map`.

O modo de desenvolvimento instrui o webpack a não minificar os arquivos e não gerar arquivos de produção otimizados. Você poderá encontrar uma documentação detalhada sobre os modos do webpack na [página Modo do webpack](https://webpack.js.org/configuration/mode/).

As opções do source map estão listadas na [página Devtool do webpack](https://webpack.js.org/configuration/devtool/#root). A definição do source map facilita a depuração por meio do navegador.

:::image type="content" source="./media/step-one-pic-11.png" alt-text="Captura de tela que mostra o código para configurar o webpack.":::

Para executar o servidor de desenvolvimento, acesse `package.json` e adicione o seguinte código em `scripts`:

```JavaScript
    "build:dev": "webpack-dev-server --config webpack.dev.js"
```

Agora, o arquivo será parecido com este:

```JavaScript
{
  "name": "CallingApp",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "build:dev": "webpack-dev-server --config webpack.dev.js"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "devDependencies": {
    "webpack": "^4.42.0",
    "webpack-cli": "^3.3.11",
    "webpack-dev-server": "^3.10.3"
  }
}
```

Você adicionou o comando que pode ser usado no npm.

:::image type="content" source="./media/step-one-pic-12.png" alt-text="Captura de tela que mostra a modificação de package.json.":::

### <a name="test-the-development-server"></a>Testar o servidor de desenvolvimento

 No Visual Studio Code, crie três arquivos no projeto:

* `index.html`
* `app.js`
* `app.css` (opcional, para estilizar o aplicativo)

Cole este código em `index.html`:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>My first ACS application</title>
    <link rel="stylesheet" href="./app.css"/>
    <script src="./app.js" defer></script>
</head>
<body>
    <h1>Hello from ACS!</h1>
</body>
</html>
```
:::image type="content" source="./media/step-one-pic-13.png" alt-text="Captura de tela que mostra o arquivo HTML.":::

Adicione o seguinte código a `app.js`:

```JavaScript
alert('Hello world alert!');
console.log('Hello world console!');
```
Adicione o seguinte código a `app.css`:

```CSS
html {
    font-family: sans-serif;
  }
```
Não se esqueça de salvá-lo. O arquivo não salvo é indicado por pontos brancos ao lado dos nomes dos arquivos no Explorer.

:::image type="content" source="./media/step-one-pic-14.png" alt-text="Captura de tela que mostra o arquivo de App.js com código JavaScript.":::

Ao abrir essa página, você verá a mensagem exibida com um alerta no console do navegador.

:::image type="content" source="./media/step-one-pic-15.png" alt-text="Captura de tela que mostra o arquivo App.css.":::

Use o seguinte comando do terminal para testar sua configuração de desenvolvimento:

```Console
npm run build:dev
```

O console mostrará o local em que o servidor está sendo executado. Por padrão, ele é `http://localhost:8080`. O comando `build:dev` é aquele que você adicionou ao `package.json` anteriormente.

:::image type="content" source="./media/step-one-pic-16.png" alt-text="Captura de tela que mostra o início de um servidor de desenvolvimento.":::
 
Acesse o endereço no navegador e você verá a página e o alerta configurados nas etapas anteriores.
 
:::image type="content" source="./media/step-one-pic-17.png" alt-text="Captura de tela da página HTML.":::
  
 
Enquanto o servidor estiver em execução, você poderá alterar o código e o servidor. A página HTML será recarregada automaticamente. 

Em seguida, acesse o arquivo `app.js` no Visual Studio Code e exclua `alert('Hello world alert!');`. Salve o arquivo e verifique se o alerta desaparece do navegador.

Para interromper o servidor, execute `Ctrl+C` no terminal. Para iniciar o servidor, insira `npm run build:dev` a qualquer momento.

## <a name="add-the-azure-communication-services-packages"></a>Adicionar os pacotes dos Serviços de Comunicação do Azure

Use o comando `npm install` para instalar o SDK de Chamada dos Serviços de Comunicação do Azure para JavaScript.

```Console
npm install @azure/communication-common --save
npm install @azure/communication-calling --save
```

Essa ação adiciona os pacotes comuns e de chamada dos Serviços de Comunicação do Azure como dependências do pacote. Você verá dois novos pacotes adicionados ao arquivo `package.json`. Você pode encontrar mais informações sobre `npm install` na [página de documentos do NPM para esse comando](https://docs.npmjs.com/cli/v6/commands/npm-install).

:::image type="content" source="./media/step-one-pic-nine.png" alt-text="Captura de tela que mostra o código para instalação dos pacotes de Serviços de Comunicação do Azure.":::

Esses pacotes são fornecidos pela equipe dos Serviços de Comunicação do Azure e incluem as bibliotecas de autenticação e de chamada. O comando `--save` sinaliza que o aplicativo depende desses pacotes para uso em produção e será incluído em `devDependencies`, no arquivo `package.json`. Quando você cria o aplicativo para produção, os pacotes são incluídos no código de produção.


## <a name="publish-your-website-to-azure-static-websites"></a>Publicar o site nos sites estáticos do Azure

### <a name="create-a-configuration-for-production-deployment"></a>Criar uma configuração para implantação de produção

Adicione o seguinte código a `webpack.prod.js`:

```JavaScript
const { merge } = require('webpack-merge');
const common = require('./webpack.common.js');

module.exports = merge(common, {
  mode: 'production',
});
```

Essa configuração será mesclada com `webpack.common.js` (em que você especificou o arquivo de entrada e no qual armazenará os resultados). A configuração também definirá o modo como `production`.
 
No `package.json`, adicione o seguinte código:

```JavaScript
"build:prod": "webpack --config webpack.prod.js"
```

O arquivo deve ter esta aparência:

```JavaScript
{
  "name": "CallingApp",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "build:dev": "webpack-dev-server --config webpack.dev.js",
    "build:prod": "webpack --config webpack.prod.js"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "dependencies": {
    "@azure/communication-calling": "^1.0.0-beta.6",
    "@azure/communication-common": "^1.0.0"
  },
  "devDependencies": {
    "webpack": "^4.42.0",
    "webpack-cli": "^3.3.11",
    "webpack-dev-server": "^3.10.3",
    "webpack-merge": "^5.7.3"
  }
}
```

:::image type="content" source="./media/step-one-pic-20.png" alt-text="Captura de tela que mostra os arquivos configurados.":::


No terminal, execute:

```Console
npm run build:prod
```

O comando criará uma pasta `dist` e um arquivo estático `app.js` pronto para produção nela. 

:::image type="content" source="./media/step-one-pic-21.png" alt-text="Captura de tela que mostra o build de produção.":::
 
 
### <a name="deploy-your-app-to-azure-storage"></a>Implantar seu aplicativo no Armazenamento do Azure

Copie `index.html` e `app.css` para a pasta `dist`.

Na pasta `dist`, crie um arquivo e dê a ele o nome de `404.html`. Copie a seguinte marcação para esse arquivo:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <link rel="stylesheet" href="./app.css"/>
    <title>Document</title>
</head>
<body>
    <h1>The page does not exists.</h1>
</body>
</html>
```

Salve o arquivo (Ctrl+S).

Clique com o botão direito do mouse em `dist` e selecione **Implantar no Site Estático por meio do Armazenamento do Azure**.

:::image type="content" source="./media/step-one-pic-22.png" alt-text="Captura de tela das seleções para iniciar a implantação no Azure.":::
 
Em **Selecionar assinatura**, escolha **Entrar no Azure** (ou **Criar uma Conta Gratuita do Azure**, caso não tenha criado uma assinatura anteriormente).
 
:::image type="content" source="./media/step-one-pic-23.png" alt-text="Captura de tela que mostra as seleções para entrar no Azure.":::
 
Selecione **Criar Conta de Armazenamento** > **Avançado**.

:::image type="content" source="./media/step-one-pic-24.png" alt-text="Captura de tela que mostra seleções para criar o grupo de contas de armazenamento.":::
 
Forneça o nome do grupo de armazenamento.
 
:::image type="content" source="./media/step-one-pic-25.png" alt-text="Captura de tela que mostra como adicionar um nome para a conta.":::
 
Crie um grupo de recursos, se necessário.
 
:::image type="content" source="./media/step-one-pic-26.png" alt-text="Captura de tela que mostra a seleção para criar um grupo de recursos.":::
  
Para a pergunta **Deseja habilitar a hospedagem de site estático?** , selecione **Sim**.

:::image type="content" source="./media/step-one-pic-27.png" alt-text="Captura de tela que mostra a seleção da opção para habilitar a hospedagem de site estático.":::
  
Em **Inserir o nome do documento de índice**, aceite o nome de arquivo padrão. Você já criou o arquivo `index.html`.

Em **Inserir o caminho do documento do erro 404**, insira **404.html**.  
  
Selecione a localização do aplicativo. A localização selecionada definirá qual processador de mídia será usado no futuro aplicativo de chamadas nas chamadas em grupo. 

Os Serviços de Comunicação do Azure selecionam o processador de mídia com base na localização do aplicativo.

:::image type="content" source="./media/step-one-pic-28.png" alt-text="Captura de tela que mostra uma lista de locais.":::
  
Aguarde até que o recurso e o site sejam criados. 
 
Selecione **Navegar para o site**.

:::image type="content" source="./media/step-one-pic-29.png" alt-text="Captura de tela que mostra uma mensagem informando que a implantação foi concluída, com o botão para navegar até um site.":::
 
Nas ferramentas de desenvolvimento do navegador, você poderá inspecionar o código-fonte e ver o arquivo que está preparado para produção.
 
:::image type="content" source="./media/step-one-pic-30.png" alt-text="Captura de tela da origem do site com o arquivo.":::

Acesse o [portal do Azure](https://portal.azure.com/#home), selecione o grupo de recursos e depois o aplicativo que você criou. Em seguida, selecione **Configurações** > **Site estático**. Você poderá ver que os sites estáticos estarão habilitados. Observe o ponto de extremidade primário, o nome do documento de índice e o caminho do documento de erro.

:::image type="content" source="./media/step-one-pic-31.png" alt-text="Captura de tela que mostra a seleção do site estático.":::

No **Serviço Blob**, selecione **Contêineres**. A lista mostra dois contêineres criados, um para logs (`$logs`) e outro para o conteúdo do site (`$web`).

:::image type="content" source="./media/step-one-pic-32.png" alt-text="Captura de tela que mostra a configuração do contêiner.":::

Se você abrir o contêiner `$web`, verá os arquivos que criou no Visual Studio e implantou no Azure. 

:::image type="content" source="./media/step-one-pic-33.png" alt-text="Captura de tela que mostra os arquivos implantados no Azure.":::

Reimplante o aplicativo por meio do Visual Studio Code a qualquer momento.

Agora você está pronto para criar seu primeiro aplicativo Web dos Serviços de Comunicação do Azure.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Adicionar chamada de voz ao aplicativo](../quickstarts/voice-video-calling/getting-started-with-calling.md)

Talvez seja necessário também:

- [Adicionar o chat ao seu aplicativo](../quickstarts/chat/get-started.md)
- [Criar tokens de acesso para o usuário](../quickstarts/access-tokens.md)
- [Saber mais sobre a arquitetura cliente e servidor](../concepts/client-and-server-architecture.md)
- [Aprender sobre autenticação](../concepts/authentication.md)
