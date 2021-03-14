---
title: Tutorial – Preparar um aplicativo Web para os Serviços de Comunicação do Azure (Node.js)
titleSuffix: An Azure Communication Services tutorial
description: Saiba como criar um aplicativo Web de linha de base que dá suporte aos Serviços de Comunicação do Azure
author: nmurav
services: azure-communication-services
ms.author: nmurav
ms.date: 01/03/2012
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: cd535227b421f4fb56dac3afb37033e3d77f75f7
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101691528"
---
# <a name="tutorial-prepare-a-web-app-for-azure-communication-services-nodejs"></a>Tutorial: Preparar um aplicativo Web para os Serviços de Comunicação do Azure (Node.js)

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

Os Serviços de Comunicação do Azure permitem que você adicione a comunicação em tempo real aos seus aplicativos. Neste tutorial, você aprenderá a configurar um aplicativo Web que dá suporte aos Serviços de Comunicação do Azure. Este é um tutorial introdutório destinado a novos desenvolvedores que desejam começar a usar a comunicação em tempo real.

Ao final deste tutorial, você terá um aplicativo Web de linha de base configurado com bibliotecas de clientes dos Serviços de Comunicação do Azure que você poderá usar para começar a criar sua solução de comunicação em tempo real.

Sinta-se à vontade para acessar a página [dos Serviços de Comunicação do Azure no GitHub](https://github.com/Azure/communication) para fornecer comentários.

Neste tutorial, você aprenderá como:
> [!div class="checklist"]
> * Configurar seu ambiente de desenvolvimento
> * Configurar um servidor Web local
> * Adicionar pacotes dos Serviços de Comunicação do Azure ao seu site
> * Publicar seu site em Sites Estáticos do Azure

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura ativa. Para obter detalhes, confira [Criar uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). Observe que a conta gratuita oferece US$ 200 em créditos Azure para experimentar qualquer combinação de serviços.
- [Visual Studio Code](https://code.visualstudio.com/): Usaremos isso para editar o código no ambiente de desenvolvimento local.
- [webpack](https://webpack.js.org/): isso será usado para agrupar e hospedar localmente o código.
- [Node.js](https://nodejs.org/en/): isso será usado para instalar e gerenciar dependências como o webpack e as bibliotecas de clientes dos Serviços de Comunicação do Azure.
- [NVM e npm](/windows/nodejs/setup-on-windows) para processar o controle de versão.
- A [extensão do Armazenamento do Azure para](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage) Visual Studio Code. Essa extensão é necessária para publicar seu aplicativo no Armazenamento do Azure. [Leia mais sobre como hospedar sites estáticos no Armazenamento do Azure](../../storage/blobs/storage-blob-static-website.md)
- A [extensão do Serviço de Aplicativo do Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice). A extensão permite implantar sites (semelhantes ao anterior), mas com a opção de configurar a CI/CD (integração contínua e entrega contínua) totalmente gerenciada.
- A [extensão da Função do Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) para criar aplicativos sem servidor próprios. Por exemplo, você pode hospedar seu aplicativo de autenticação em funções do Azure.
- Um recurso e uma cadeia de conexão ativos dos Serviços de Comunicação. [Crie um recurso dos Serviços de Comunicação](../quickstarts/create-communication-resource.md).
- Um token de acesso do usuário. Confira o [guia de início rápido de tokens de acesso](../quickstarts/access-tokens.md?pivots=programming-language-javascript) ou o [tutorial de serviço confiável](./trusted-service-tutorial.md) para obter instruções.


## <a name="configure-your-development-environment"></a>Configurar seu ambiente de desenvolvimento

Seu ambiente de desenvolvimento local será configurado da seguinte maneira:

:::image type="content" source="./media/step-one-pic-one.png" alt-text="Arquitetura do ambiente de desenvolvedor":::


### <a name="install-nodejs-nvm-and-npm"></a>Instalar o Node.js, o NVM e o npm

Usaremos o Node.js para baixar e instalar várias dependências necessárias para nosso aplicativo do lado do cliente. Usaremos isso para gerar arquivos estáticos que, depois, hospedaremos no Azure. Portanto, você não precisa se preocupar em configurá-lo no seu servidor.

Os desenvolvedores do Windows podem seguir [este tutorial do NodeJS](/windows/nodejs/setup-on-windows) para configurar o Node, o NVM e o npm. 

Testamos esse tutorial usando a versão LTS 12.20.0. Depois de instalar o NVM, use o seguinte comando do PowerShell para implantar a versão que deseja usar:

```PowerShell
nvm list available
nvm install 12.20.0
nvm use 12.20.0
```

:::image type="content" source="./media/step-one-pic-two.png" alt-text="Como trabalhar com o NVM para implantar o Node.js":::

### <a name="configure-visual-studio-code"></a>Configurar o Visual Studio Code

Baixe o [Visual Studio Code](https://code.visualstudio.com/) em uma das [plataformas compatíveis](https://code.visualstudio.com/docs/supporting/requirements#_platforms).

### <a name="create-a-workspace-for-your-azure-communication-services-projects"></a>Criar um workspace para seus projetos dos Serviços de Comunicação do Azure

Crie uma pasta para armazenar seus arquivos de projeto, desta forma: `C:\Users\Documents\ACS\CallingApp`. No Visual Studio Code, clique em "Arquivo", "Adicionar Pasta ao Workspace" e adicione a pasta ao seu workspace.

:::image type="content" source="./media/step-one-pic-three.png" alt-text="Como criar um local de trabalho":::

Acesse "Explorer" no Visual Studio Code no painel esquerdo e você verá sua pasta "CallingApp" no workspace "Sem título".

:::image type="content" source="./media/step-one-pic-four.png" alt-text="Explorer":::

Fique à vontade para atualizar o nome do workspace. Valide a versão do Node.js clicando com o botão direito do mouse na pasta "CallingApp" e selecionando "Abrir no Terminal Integrado".

:::image type="content" source="./media/step-one-pic-five.png" alt-text="Como abrir um terminal":::

No terminal, digite o seguinte comando para validar a versão do Node.js instalada na etapa anterior:

```JavaScript
node --version
```

:::image type="content" source="./media/step-one-pic-six.png" alt-text="Como validar a versão do Node.js":::

### <a name="install-azure-extensions-for-visual-studio-code"></a>Instalar extensões do Azure para Visual Studio Code

Instale a [extensão do Armazenamento do Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage) por meio do marketplace do Visual Studio ou com o Visual Studio Code (Exibir > Extensões > Armazenamento do Azure).

:::image type="content" source="./media/step-one-pic-seven.png" alt-text="Como instalar a extensão do Armazenamento do Azure 1":::

Siga as mesmas etapas para as extensões do [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) e do [Serviço de Aplicativo do Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice).


## <a name="set-up-a-local-webserver"></a>Configurar um servidor Web local

### <a name="create-a-new-npm-package"></a>Criar um pacote npm

No terminal, no caminho da pasta do workspace, digite:

``` console
npm init -y
```

Esse comando inicializa um novo pacote npm e adiciona `package.json` à pasta raiz do projeto.

:::image type="content" source="./media/step-one-pic-eight.png" alt-text="JSON do pacote":::

Encontre a documentação adicional sobre o comando npm init [aqui](https://docs.npmjs.com/cli/v6/commands/npm-init)

### <a name="install-webpack"></a>Instalar o webpack

O [webpack](https://webpack.js.org/) permite que você agrupe o código em arquivos estáticos que podem ser implantados no Azure. Ele também tem um servidor de desenvolvimento, que configuraremos para usar com o exemplo de chamada.

Em seu terminal, digite o seguinte para instalar o webpack:

``` Console
npm install webpack@4.42.0 webpack-cli@3.3.11 webpack-dev-server@3.10.3 --save-dev
```

Este tutorial foi testado com as versões especificadas acima. A especificação de `-dev` informa ao gerenciador de pacotes de que essa dependência destina-se a fins de desenvolvimento e não deve ser incluída no código que implantamos no Azure.

Você verá dois novos pacotes adicionados ao arquivo `package.json` como "devDependencies". Os pacotes serão instalados no diretório `./CallingApp/node_modules/`.

:::image type="content" source="./media/step-one-pic-ten.png" alt-text="configuração do webpack":::

### <a name="configure-the-development-server"></a>Configurar o servidor de desenvolvimento

A execução de um aplicativo estático (como o arquivo `index.html`) por meio do navegador usa o protocolo `file://`. Para que os módulos do npm funcionem corretamente, precisaremos do protocolo HTTP usando o webpack como um servidor de desenvolvimento local.

Criaremos duas configurações: uma para desenvolvimento e outra para produção. Os arquivos preparados para produção serão minificados, o que significa que removeremos espaços em branco e caracteres não utilizados. Isso é apropriado para cenários de produção em que a latência deve ser minimizada ou quando o código deve ser ocultado.

Usaremos a ferramenta `webpack-merge` para trabalhar com [diferentes arquivos de configuração para o webpack](https://webpack.js.org/guides/production/)

Vamos começar com o ambiente de desenvolvimento. Primeiro, precisamos instalar `webpack merge`. No terminal, execute o seguinte:

```Console
npm install --save-dev webpack-merge
```

No arquivo `package.json`, você pode ver mais uma dependência adicionada a "devDependencies".

Na próxima etapa, precisaremos criar um arquivo `webpack.common.js` e adicionar o seguinte código:

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

Em seguida, adicionaremos mais dois arquivos, um para cada configuração:

* webpack.dev.js
* webpack.prod.js

Na próxima etapa, precisaremos modificar o arquivo `webpack.dev.js`. Adicione o seguinte código ao arquivo:

```JavaScript
const { merge } = require('webpack-merge');
const common = require('./webpack.common.js');

module.exports = merge(common, {
    mode: 'development',
    devtool: 'inline-source-map',
});
```
Nessa configuração, importamos parâmetros comuns de `webpack.common.js`, mesclamos os dois arquivos, definimos o modo como "desenvolvimento" e configuramos SourceMap como "inline-source-map".

O modo de desenvolvimento instrui o webpack a não minificar os arquivos e não gerar arquivos de produção otimizados. Encontre a documentação detalhada sobre os modos do webpack [aqui](https://webpack.js.org/configuration/mode/).

As opções de source map são listadas [aqui](https://webpack.js.org/configuration/devtool/#root). A definição do source map facilita a depuração por meio do navegador.

:::image type="content" source="./media/step-one-pic-11.png" alt-text="Como configurar o webpack":::

Para executar o servidor de desenvolvimento, acesse `package.json` e adicione o seguinte código em scripts:

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

:::image type="content" source="./media/step-one-pic-12.png" alt-text="Modificar package.json":::

### <a name="testing-the-development-server"></a>Como testar o servidor de desenvolvimento

 No Visual Studio Code, crie três arquivos no projeto:

* `index.html`
* `app.js`
* `app.css` (opcional; permite definir o estilo do aplicativo)

Cole isso em `index.html`:

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
:::image type="content" source="./media/step-one-pic-13.png" alt-text="Arquivo HTML":::

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
Não se esqueça de salvá-lo. O arquivo não salvo é indicado por pontos brancos ao lado de nomes de arquivo no Explorador de Arquivos.

 :::image type="content" source="./media/step-one-pic-14.png" alt-text="Arquivo App.js com o código JS":::

Ao abrir essa página, você verá a mensagem exibida com um alerta e dentro do console do navegador.

:::image type="content" source="./media/step-one-pic-15.png" alt-text="Arquivo App.css":::

Use o seguinte comando do terminal para testar sua configuração de desenvolvimento:

```Console
npm run build:dev
```

O console mostrará o local em que o servidor está sendo executado. Por padrão, ele é `http://localhost:8080`. O comando build:dev é o comando que adicionamos ao `package.json` anteriormente.

 :::image type="content" source="./media/step-one-pic-16.png" alt-text="Como iniciar um servidor de desenvolvimento":::
 
 Navegue até o endereço no navegador e você verá a página e o alerta, configurado nas etapas anteriores.
 
  :::image type="content" source="./media/step-one-pic-17.png" alt-text="Página HTML":::
  
 
Enquanto o servidor estiver em execução, você poderá alterar o código, e o servidor e a página HTML serão recarregados automaticamente. 

Em seguida, acesse o arquivo `app.js` no Visual Studio Code e exclua `alert('Hello world alert!');`. Salve o arquivo e verifique se o alerta desaparece do navegador.

Para interromper o servidor, execute `Ctrl+C` no terminal. Para iniciar o servidor, digite `npm run build:dev` a qualquer momento.

## <a name="add-the-azure-communication-services-packages"></a>Adicionar os pacotes dos Serviços de Comunicação do Azure

Use o comando `npm install` para instalar a biblioteca de clientes de Chamada dos Serviços de Comunicação do Azure para JavaScript.

```Console
npm install @azure/communication-common --save
npm install @azure/communication-calling --save
```

Essa ação adicionará os pacotes comuns e de chamada dos Serviços de Comunicação do Azure como dependências do pacote. Você verá dois novos pacotes adicionados ao arquivo `package.json`. Encontre mais informações sobre o comando `npm install` [aqui](https://docs.npmjs.com/cli/v6/commands/npm-install).

:::image type="content" source="./media/step-one-pic-nine.png" alt-text="Como instalar os pacotes dos Serviços de Comunicação do Azure":::

Esses pacotes são fornecidos pela equipe dos Serviços de Comunicação do Azure e incluem as bibliotecas de autenticação e de chamada. O comando "--save" sinaliza que o aplicativo depende desses pacotes para uso em produção e será incluído nas `dependencies` do arquivo `package.json`. Quando criarmos o aplicativo para produção, os pacotes serão incluídos no código de produção.


## <a name="publish-your-website-to-azure-static-websites"></a>Publicar seu site em Sites Estáticos do Azure

### <a name="create-a-configuration-for-production-deployment"></a>Criar uma configuração para implantação de produção

Adicione o seguinte código ao `webpack.prod.js`:

```JavaScript
const { merge } = require('webpack-merge');
 const common = require('./webpack.common.js');

 module.exports = merge(common, {
   mode: 'production',
 });
 ```

Observe que essa configuração será mesclada com o webpack.common.js (no qual especificamos o arquivo de entrada e no qual os resultados serão armazenados) e o modo será definido como "produção".
 
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
    "@azure/communication-calling": "^1.0.0-beta.3",
    "@azure/communication-common": "^1.0.0-beta.3"
  },
  "devDependencies": {
    "webpack": "^4.42.0",
    "webpack-cli": "^3.3.11",
    "webpack-dev-server": "^3.10.3",
    "webpack-merge": "^5.7.3"
  }
}
```

 :::image type="content" source="./media/step-one-pic-20.png" alt-text="Arquivos configurados":::


No terminal, execute:

```Console
npm run build:prod
```

O comando criará uma pasta `dist` e o arquivo estático `app.js` pronto para produção nela. 

 :::image type="content" source="./media/step-one-pic-21.png" alt-text="Build de produção":::
 
 
### <a name="deploy-your-app-to-azure-storage"></a>Implantar seu aplicativo no Armazenamento do Azure
 
Copie `index.html` e `app.css` para a pasta `dist`.

Na pasta `dist`, crie um arquivo e nomeie-o `404.html`. Copie a seguinte marcação para esse arquivo:

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

Salve o arquivo (CTRL + S).

Clique com o botão direito do mouse e selecione Implantar no Site Estático por meio do Armazenamento do Azure.

:::image type="content" source="./media/step-one-pic-22.png" alt-text="Início da implantação no Azure":::
 
No campo `Select subscription`, selecione "Entrar no Azure (ou "Criar uma conta gratuita do Azure" caso não tenha criado uma assinatura antes)
 
:::image type="content" source="./media/step-one-pic-23.png" alt-text="Entrar no Azure":::
 
Selecione `Create new Storage Account` > `Advanced`:

 :::image type="content" source="./media/step-one-pic-24.png" alt-text="Criação do grupo da conta de armazenamento":::
 
 Forneça o nome do grupo de armazenamento:
 
 :::image type="content" source="./media/step-one-pic-25.png" alt-text="Adição de um nome para a conta":::
 
Crie um grupo de recursos, se necessário:
 
  :::image type="content" source="./media/step-one-pic-26.png" alt-text="Criação de um grupo":::
  
  Responda "Sim" para a pergunta "Deseja habilitar a hospedagem de site estático?"
  
  :::image type="content" source="./media/step-one-pic-27.png" alt-text="Seleção da opção para habilitar a hospedagem de site estático":::
  
Aceite o nome de arquivo padrão em "Insira o nome do documento de índice", conforme criamos o arquivo `index.html`.

Digite `404.html` em "Insira o caminho do documento de erro 404".  
  
Selecione a localização do aplicativo. A localização selecionada definirá qual processador de mídia será usado no seu futuro aplicativo de chamadas nas chamadas em grupo. 

Os Serviços de Comunicação do Azure selecionam o processador de mídia com base na localização do aplicativo.

:::image type="content" source="./media/step-one-pic-28.png" alt-text="Selecionar local":::
  
Aguarde até que o recurso e o site sejam criados. 
 
Clique em "Navegar para o site":

:::image type="content" source="./media/step-one-pic-29.png" alt-text="Implantação concluída":::
 
Nas ferramentas de desenvolvimento do navegador, inspecione o código-fonte e veja o arquivo, preparado para produção.
 
:::image type="content" source="./media/step-one-pic-30.png" alt-text="Site":::

Acesse o [portal do Azure](https://portal.azure.com/#home), selecione o grupo de recursos e o aplicativo que você criou e procure `Settings` > `Static website`. Você poderá ver que os sites estáticos estão habilitados e anotar o ponto de extremidade primário, o documento de índice e os arquivos do documento de caminho de erro.

:::image type="content" source="./media/step-one-pic-31.png" alt-text="Seleção de site estático":::

Em "Serviço blob", selecione "Contêineres" e você verá dois contêineres criados: um para os logs ($logs) e o outro para o conteúdo do site ($web)

:::image type="content" source="./media/step-one-pic-32.png" alt-text="Configuração do contêiner":::

Se você acessar `$web`, verá os arquivos que criou no Visual Studio e implantou no Azure. 

:::image type="content" source="./media/step-one-pic-33.png" alt-text="Implantação":::

Reimplante o aplicativo por meio do Visual Studio Code a qualquer momento.

Agora você está pronto para criar seu primeiro aplicativo Web dos Serviços de Comunicação do Azure.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Adicionar chamada de voz ao aplicativo](../quickstarts/voice-video-calling/getting-started-with-calling.md)

Você também pode querer:

- [Adicionar o chat ao seu aplicativo](../quickstarts/chat/get-started.md)
- [Criar tokens de acesso do usuário](../quickstarts/access-tokens.md)
- [Saber mais sobre a arquitetura cliente e servidor](../concepts/client-and-server-architecture.md)
- [Aprender sobre autenticação](../concepts/authentication.md)