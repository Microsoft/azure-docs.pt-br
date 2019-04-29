---
title: Configurar aplicativos do Node. js - serviço de aplicativo do Azure | Microsoft Docs
description: Saiba como configurar aplicativos do Node. js funcione no serviço de aplicativo do Azure
services: app-service
documentationcenter: ''
author: cephalin
manager: jpconnock
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/28/2019
ms.author: cephalin
ms.openlocfilehash: 43dc76e6d1e1ec2a6167f1d3e3cc7b8780f843db
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60850147"
---
# <a name="configure-a-linux-nodejs-app-for-azure-app-service"></a>Configurar um aplicativo do Node. js do Linux para o serviço de aplicativo do Azure

Aplicativos Node. js devem ser implantados com todas as dependências NPM necessárias. O mecanismo de implantação do serviço de aplicativo (Kudu) é executado automaticamente `npm install --production` para você quando você implanta uma [repositório Git](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json), ou uma [pacote Zip](../deploy-zip.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) com processos de compilação ativados. Se você implantar os arquivos usando o [FTP/S](../deploy-ftp.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json), no entanto, você precisa carregar os pacotes necessários manualmente.

Este guia fornece instruções para desenvolvedores de Node. js que usam um contêiner interno do Linux no serviço de aplicativo e principais conceitos. Se você nunca usou o serviço de aplicativo do Azure, siga as [início rápido do Node. js](quickstart-nodejs.md) e [Node. js com o tutorial de MongoDB](tutorial-nodejs-mongodb-app.md) primeiro.

## <a name="show-nodejs-version"></a>Mostrar a versão do Node. js

Para mostrar a versão atual do Node. js, execute o seguinte comando [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Para mostrar todas as versões do Node. js compatíveis, execute o seguinte comando [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep NODE
```

## <a name="set-nodejs-version"></a>Definir versão do Node. js

Para definir seu aplicativo para um [suporte para a versão do Node. js](#show-nodejs-version), execute o seguinte comando [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "NODE|10.14"
```

Essa configuração especifica a versão do Node. js a ser usado, tanto em tempo de execução e durante a restauração de pacote automatizado do Kudu.

> [!NOTE]
> Você deve definir a versão do Node. js em seu projeto `package.json`. O mecanismo de implantação é executado em um contêiner separado que contém todas as versões com suporte do Node. js.

## <a name="configure-nodejs-server"></a>Configurar o servidor Node. js

Os contêineres do Node. js acompanham [PM2](http://pm2.keymetrics.io/), um Gerenciador de processos de produção. Você pode configurar seu aplicativo para começar com PM2, ou com o NPM ou com um comando personalizado.

- [Execute o comando personalizado](#run-custom-command)
- [Executar início do npm](#run-npm-start)
- [Executar com PM2](#run-with-pm2)

### <a name="run-custom-command"></a>Execute o comando personalizado

O serviço de aplicativo pode iniciar seu aplicativo usando um comando personalizado, como um executável, como *run.sh*. Por exemplo, para executar `npm run start:prod`, execute o seguinte comando [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "npm run start:prod"
```

### <a name="run-npm-start"></a>Executar início do npm

Para iniciar seu aplicativo usando `npm start`, apenas certifique-se de uma `start` script está no *Package. JSON* arquivo. Por exemplo: 

```json
{
  ...
  "scripts": {
    "start": "gulp",
    ...
  },
  ...
}
```

Para usar um personalizado *Package. JSON* em seu projeto, execute o seguinte comando [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<filename>.json"
```

### <a name="run-with-pm2"></a>Executar com PM2

O contêiner automaticamente inicia o aplicativo com PM2 quando um dos arquivos comuns do Node. js é encontrado no seu projeto:

- *bin/www*
- *server.js*
- *app.js*
- *index.js*
- *hostingstart.js*
- Um dos seguintes [arquivos PM2](http://pm2.keymetrics.io/docs/usage/application-declaration/#process-file): *Process* e *ecosystem.config.js*

Você também pode configurar um arquivo de inicialização personalizada com as seguintes extensões:

- Um *. js* arquivo
- Um [arquivo PM2](http://pm2.keymetrics.io/docs/usage/application-declaration/#process-file) com a extensão *. JSON*, *. js*, *YAML*, ou *. yml*

Para adicionar um arquivo de inicialização personalizadas, execute o seguinte comando [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<filname-with-extension>"
```

## <a name="debug-remotely"></a>Depurar remotamente

> [!NOTE]
> Depuração remota está atualmente em visualização.

Você pode depurar seu aplicativo Node. js remotamente no [Visual Studio Code](https://code.visualstudio.com/) se você configurá-lo para [executar com PM2](#run-with-pm2), exceto quando você executá-lo usando um *. js, *.yml, ou *YAML*.

Na maioria dos casos, nenhuma configuração adicional é necessária para seu aplicativo. Se seu aplicativo for executado com um *Process* arquivo (padrão ou personalizado), ele deve ter um `script` propriedade na raiz do JSON. Por exemplo: 

```json
{
  "name"        : "worker",
  "script"      : "./index.js",
  ...
}
```

Para configurar o Visual Studio Code para depuração remota, instale o [extensão de serviço de aplicativo](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice). Siga as instruções na página de extensão e entrar no Azure no Visual Studio Code.

No Azure explorer, localize o aplicativo que você deseja depurar, clique duas vezes e selecione **iniciar a depuração remota**. Clique em **Sim** para habilitá-lo para seu aplicativo. Serviço de aplicativo é iniciado um proxy de túnel para você e anexa o depurador. Você pode fazer solicitações para o aplicativo e ver o depurador pausa em pontos de interrupção.

Quando terminar com a depuração, pare o depurador selecionando **desconectar**. Quando solicitado, clique **Sim** para desabilitar a depuração remota. Para desabilitá-lo mais tarde, seu aplicativo novamente no Gerenciador do Azure com o botão direito e selecione **desabilitar a depuração remota**.

## <a name="access-environment-variables"></a>Acessar variáveis de ambiente

No Serviço de Aplicativo, você pode [definir configurações de aplicativo](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#app-settings) fora do código do aplicativo. Em seguida, você pode acessá-los usando o padrão do Node. js. Por exemplo, para acessar uma configuração de aplicativo chamada `NODE_ENV`, use o seguinte código:

```javascript
process.env.NODE_ENV
```

## <a name="run-gruntbowergulp"></a>Execute o Bower/Grunt/Gulp

Por padrão, o Kudu executa `npm install --production` quando ele reconhece que um aplicativo Node. js é implantado. Se seu aplicativo requer que as ferramentas de automação populares, como Grunt, Bower ou Gulp, você precisará fornecer um [script de implantação personalizado](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script) para executá-lo.

Para habilitar seu repositório para executar essas ferramentas, você precisará adicioná-los às dependências em *Package. JSON.* Por exemplo: 

```json
"dependencies": {
  "bower": "^1.7.9",
  "grunt": "^1.0.1",
  "gulp": "^3.9.1",
  ...
}
```

Em uma janela do terminal local, altere o diretório para a raiz do repositório e execute os seguintes comandos:

```bash
npm install kuduscript -g
kuduscript --node --scriptType bash --suppressPrompt
```

Raiz do repositório agora tem dois arquivos adicionais: *. Deployment* e *deploy.sh*.

Abra *deploy.sh* e localize o `Deployment` seção, que se parece com isso:

```bash
##################################################################################################################################
# Deployment
# ----------
```

Esta seção termina com execução `npm install --production`. Adicione a seção de código que você precisa para executar a ferramenta necessária *no final* da `Deployment` seção:

- [Bower](#bower)
- [Gulp](#gulp)
- [Grunt](#grunt)

Veja uma [exemplo de exemplo Mean. js](https://github.com/Azure-Samples/meanjs/blob/master/deploy.sh#L112-L135), em que o script de implantação também executa um personalizado `npm install` comando.

### <a name="bower"></a>Bower

Este trecho de código é executado `bower install`.

```bash
if [ -e "$DEPLOYMENT_TARGET/bower.json" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/bower install
  exitWithMessageOnError "bower failed"
  cd - > /dev/null
fi
```

### <a name="gulp"></a>Gulp

Este trecho de código é executado `gulp imagemin`.

```bash
if [ -e "$DEPLOYMENT_TARGET/gulpfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/gulp imagemin
  exitWithMessageOnError "gulp failed"
  cd - > /dev/null
fi
```

### <a name="grunt"></a>Grunt

Este trecho de código é executado `grunt`.

```bash
if [ -e "$DEPLOYMENT_TARGET/Gruntfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/grunt
  exitWithMessageOnError "Grunt failed"
  cd - > /dev/null
fi
```

## <a name="detect-https-session"></a>Detectar sessão HTTPS

No Serviço de Aplicativo, a [Terminação SSL](https://wikipedia.org/wiki/TLS_termination_proxy) ocorre nos balanceadores de carga de rede de modo que todas as solicitações HTTPS cheguem ao seu aplicativo como solicitações HTTP não criptografadas. Se a lógica de aplicativo precisar verificar se as solicitações do usuário estão criptografadas ou não, inspecione o cabeçalho `X-Forwarded-Proto`.

Estrutura Web populares permitem que você acesse informações do `X-Forwarded-*` no seu padrão de aplicativo básico. Na [Express](https://expressjs.com/), você pode usar [confiar proxies](http://expressjs.com/guide/behind-proxies.html). Por exemplo: 

```javascript
app.set('trust proxy', 1)
...
if (req.secure) {
  // Do something when HTTPS is used
}
```

## <a name="access-diagnostic-logs"></a>Acessar logs de diagnóstico

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>Abra a sessão SSH aberta no navegador

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="troubleshooting"></a>solução de problemas

Quando um aplicativo Node. js em funcionamento se comporta de forma diferente no serviço de aplicativo ou tem erros, tente o seguinte:

- [Acessar o fluxo de log](#access-diagnostic-logs).
- Teste o aplicativo localmente no modo de produção. Serviço de aplicativo executa seus aplicativos Node. js no modo de produção, portanto, você precisará certificar-se de que seu projeto funcione conforme o esperado no modo de produção localmente. Por exemplo: 
    - Dependendo da sua *Package. JSON*, pacotes diferentes podem ser instalados para o modo de produção (`dependencies` versus `devDependencies`).
    - Determinadas estruturas da web podem implantar arquivos estáticos diferentemente em modo de produção.
    - Determinadas estruturas da web podem usar scripts de inicialização personalizada ao ser executado no modo de produção.
- Execute seu aplicativo no serviço de aplicativo no modo de desenvolvimento. Por exemplo, na [Mean. js](http://meanjs.org/), você pode configurar seu aplicativo para o modo de desenvolvimento no tempo de execução pelo [configuração de `NODE_ENV` configuração do aplicativo](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json).

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Tutorial: Aplicativo Node.js com o MongoDB](tutorial-nodejs-mongodb-app.md)

> [!div class="nextstepaction"]
> [Perguntas frequentes sobre o Serviço de Aplicativo no Linux](app-service-linux-faq.md)