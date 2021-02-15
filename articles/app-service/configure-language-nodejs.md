---
title: Configurar aplicativos Node.js
description: Saiba como configurar um aplicativo Node.js nas instâncias nativas do Windows ou em um contêiner do Linux predefinido, no serviço Azure App. Este artigo mostra as tarefas de configuração mais comuns.
ms.custom: devx-track-js, devx-track-azurecli
ms.devlang: nodejs
ms.topic: article
ms.date: 06/02/2020
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: 8bdf637ab773e90a5eac42bcaa443cf6741db636
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94696006"
---
# <a name="configure-a-nodejs-app-for-azure-app-service"></a>Configurar um aplicativo de Node.js para o serviço Azure App

Node.js aplicativos devem ser implantados com todas as dependências de NPM necessárias. O mecanismo de implantação do serviço de aplicativo é executado automaticamente `npm install --production` quando você implanta um [repositório git](deploy-local-git.md)ou um [pacote zip](deploy-zip.md) com a automação de compilação habilitada. No entanto, se você implantar seus arquivos usando [FTP/S](deploy-ftp.md), será necessário carregar os pacotes necessários manualmente.

Este guia fornece os principais conceitos e instruções para os desenvolvedores de Node.js que se implantam no serviço de aplicativo. Se você nunca usou Azure App serviço, siga o guia de [ início rápidoNode.js](quickstart-nodejs.md) e [Node.js com o MongoDB](tutorial-nodejs-mongodb-app.md) primeiro.

## <a name="show-nodejs-version"></a>Mostrar versão de Node.js

::: zone pivot="platform-windows"  

Para mostrar a versão atual do Node.js, execute o seguinte comando no [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config appsettings list --name <app-name> --resource-group <resource-group-name> --query "[?name=='WEBSITE_NODE_DEFAULT_VERSION'].value"
```

Para mostrar todas as versões de Node.js com suporte, execute o seguinte comando no [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes | grep node
```

::: zone-end

::: zone pivot="platform-linux"

Para mostrar a versão atual do Node.js, execute o seguinte comando no [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Para mostrar todas as versões de Node.js com suporte, execute o seguinte comando no [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep NODE
```

::: zone-end

## <a name="set-nodejs-version"></a>Definir Node.js versão

::: zone pivot="platform-windows"  

Para definir seu aplicativo para uma [versão Node.js com suporte](#show-nodejs-version), execute o seguinte comando na [Cloud Shell](https://shell.azure.com) para definir `WEBSITE_NODE_DEFAULT_VERSION` como uma versão com suporte:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_NODE_DEFAULT_VERSION="10.15"
```

Essa configuração especifica a versão de Node.js a ser usada, em tempo de execução e durante a restauração automatizada de pacote durante a automação de compilação do serviço de aplicativo.

> [!NOTE]
> Você deve definir a versão Node.js no seu projeto `package.json` . O mecanismo de implantação é executado em um processo separado que contém todas as versões de Node.js com suporte.

::: zone-end

::: zone pivot="platform-linux"

Para definir seu aplicativo para uma [versão Node.js com suporte](#show-nodejs-version), execute o seguinte comando na [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "NODE|10.14"
```

Essa configuração especifica a versão de Node.js a ser usada, em tempo de execução e durante a restauração de pacote automatizado em kudu.

> [!NOTE]
> Você deve definir a versão Node.js no seu projeto `package.json` . O mecanismo de implantação é executado em um contêiner separado que contém todas as versões de Node.js com suporte.

::: zone-end

## <a name="get-port-number"></a>Obter número da porta

Você Node.js aplicativo precisa escutar a porta certa para receber solicitações de entrada.

::: zone pivot="platform-windows"  

No serviço de aplicativo no Windows, os aplicativos Node.js são hospedados com [IISNode](https://github.com/Azure/iisnode)e seu aplicativo Node.js deve escutar a porta especificada na `process.env.PORT` variável. O exemplo a seguir mostra como fazer isso em um aplicativo Express simples:

::: zone-end

::: zone pivot="platform-linux"  

O serviço de aplicativo define a variável de ambiente `PORT` no contêiner de Node.js e encaminha as solicitações de entrada para o contêiner nesse número de porta. Para receber as solicitações, seu aplicativo deve escutar essa porta usando `process.env.PORT` . O exemplo a seguir mostra como fazer isso em um aplicativo Express simples:

::: zone-end

```javascript
const express = require('express')
const app = express()
const port = process.env.PORT || 3000

app.get('/', (req, res) => {
  res.send('Hello World!')
})

app.listen(port, () => {
  console.log(`Example app listening at http://localhost:${port}`)
})
```

::: zone pivot="platform-linux"

## <a name="customize-build-automation"></a>Personalizar a automação de build

Se você implantar seu aplicativo usando pacotes Git ou zip com a automação de build ativada, a automação de build do Serviço de Aplicativo percorrerá a seguinte sequência:

1. Executar script personalizado se especificado por `PRE_BUILD_SCRIPT_PATH`.
1. Execute `npm install` sem nenhum sinalizador, o que inclui NPM `preinstall` e `postinstall` scripts e também instalações `devDependencies` .
1. Executar `npm run build` se um script de compilação for especificado no seu *package.jsem*.
1. Executar `npm run build:azure` se um Build: script do Azure for especificado no seu *package.jsem*.
1. Executar script personalizado se especificado por `POST_BUILD_SCRIPT_PATH`.

> [!NOTE]
> Conforme descrito em [NPM docs](https://docs.npmjs.com/misc/scripts), scripts chamados `prebuild` e `postbuild` executados antes e depois `build` , respectivamente, se especificados. `preinstall` e `postinstall` executar antes e depois `install` , respectivamente.

`PRE_BUILD_COMMAND` e `POST_BUILD_COMMAND` são variáveis de ambiente vazias por padrão. Para executar comandos pré-build, defina `PRE_BUILD_COMMAND`. Para executar comandos pós-build, defina `POST_BUILD_COMMAND`.

O exemplo a seguir especifica as duas variáveis para uma série de comandos, separados por vírgulas.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PRE_BUILD_COMMAND="echo foo, scripts/prebuild.sh"
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings POST_BUILD_COMMAND="echo foo, scripts/postbuild.sh"
```

Para obter variáveis de ambiente adicionais para personalizar a automação de build, confira [Configuração do Oryx](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md).

Para obter mais informações sobre como o serviço de aplicativo é executado e compila Node.js aplicativos no Linux, consulte [a documentação do Oryx: como Node.js aplicativos são detectados e compilados](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/nodejs.md).

## <a name="configure-nodejs-server"></a>Configurar o servidor de Node.js

Os contêineres de Node.js vêm com [PM2](https://pm2.keymetrics.io/), gerente de processo de produção. Você pode configurar seu aplicativo para iniciar com PM2, ou com NPM, ou com um comando personalizado.

- [Executar comando personalizado](#run-custom-command)
- [Executar NPM iniciar](#run-npm-start)
- [Executar com PM2](#run-with-pm2)

### <a name="run-custom-command"></a>Executar comando personalizado

O serviço de aplicativo pode iniciar seu aplicativo usando um comando personalizado, como um executável como *Run.sh*. Por exemplo, para executar `npm run start:prod` , execute o seguinte comando no [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "npm run start:prod"
```

### <a name="run-npm-start"></a>Executar NPM iniciar

Para iniciar seu aplicativo usando `npm start` , apenas verifique se um `start` script está no *package.jsno* arquivo. Por exemplo:

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

Para usar um *package.jspersonalizado em* seu projeto, execute o seguinte comando no [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<filename>.json"
```

### <a name="run-with-pm2"></a>Executar com PM2

O contêiner inicia automaticamente seu aplicativo com PM2 quando um dos arquivos comuns do Node.js é encontrado em seu projeto:

- *bin/www*
- *server.js*
- *app.js*
- *index.js*
- *hostingstart.js*
- Um dos seguintes [arquivos PM2](https://pm2.keymetrics.io/docs/usage/application-declaration/#process-file): *process.jsem* e *ecosystem.config.js*

Você também pode configurar um arquivo de inicialização personalizado com as seguintes extensões:

- Um arquivo *. js*
- Um [arquivo PM2](https://pm2.keymetrics.io/docs/usage/application-declaration/#process-file) com a extensão *. JSON*, *.config.js*, *. YAML* ou *. yml*

Para adicionar um arquivo de inicialização personalizado, execute o seguinte comando no [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<filname-with-extension>"
```

## <a name="debug-remotely"></a>Depurar remotamente

> [!NOTE]
> A depuração remota está atualmente em visualização.

Você pode depurar seu aplicativo Node.js remotamente no [Visual Studio Code](https://code.visualstudio.com/) se configurá-lo para ser [executado com PM2](#run-with-pm2), exceto quando você executá-lo usando um * .config.js, *. yml ou *. YAML*.

Na maioria dos casos, nenhuma configuração adicional é necessária para seu aplicativo. Se seu aplicativo for executado com um *process.jsno* arquivo (padrão ou personalizado), ele deverá ter uma `script` Propriedade na raiz JSON. Por exemplo:

```json
{
  "name"        : "worker",
  "script"      : "./index.js",
  ...
}
```

Para configurar Visual Studio Code para depuração remota, instale a [extensão do serviço de aplicativo](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice). Siga as instruções na página extensão e entre no Azure no Visual Studio Code.

No Azure Explorer, localize o aplicativo que você deseja depurar, clique nele com o botão direito do mouse e selecione **Iniciar Depuração remota**. Clique em **Sim** para habilitá-lo para seu aplicativo. O serviço de aplicativo inicia um proxy de túnel para você e anexa o depurador. Em seguida, você pode fazer solicitações para o aplicativo e ver o depurador pausando em pontos de interrupção.

Depois de concluir a depuração, pare o depurador selecionando **Desconectar**. Quando solicitado, você deve clicar em **Sim** para desabilitar a depuração remota. Para desabilitá-lo mais tarde, clique com o botão direito do mouse no seu aplicativo novamente no Azure Explorer e selecione **Desabilitar depuração remota**.

::: zone-end

## <a name="access-environment-variables"></a>Acessar variáveis de ambiente

No Serviço de Aplicativo, você pode [definir configurações de aplicativo](configure-common.md) fora do código do aplicativo. Em seguida, você pode acessá-los usando o padrão de Node.js padrão. Por exemplo, para acessar uma configuração de aplicativo chamada `NODE_ENV`, use o seguinte código:

```javascript
process.env.NODE_ENV
```

## <a name="run-gruntbowergulp"></a>Executar Grunt/Bower/Gulp

Por padrão, a automação de compilação do serviço de aplicativo é executada `npm install --production` quando reconhece um aplicativo Node.js é implantada por meio da implantação do git ou zip com a automação de compilação habilitada. Se seu aplicativo exigir qualquer uma das ferramentas de automação populares, como Grunt, Bower ou Gulp, você precisará fornecer um [script de implantação personalizado](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script) para executá-lo.

Para habilitar seu repositório para executar essas ferramentas, você precisa adicioná-las às dependências no *package.jsno.* Por exemplo:

```json
"dependencies": {
  "bower": "^1.7.9",
  "grunt": "^1.0.1",
  "gulp": "^3.9.1",
  ...
}
```

Em uma janela de terminal local, altere o diretório para a raiz do repositório e execute os seguintes comandos:

```bash
npm install kuduscript -g
kuduscript --node --scriptType bash --suppressPrompt
```

A raiz do repositório agora tem dois arquivos adicionais: *. Deployment* e *Deploy.sh*.

Abra *Deploy.sh* e localize a `Deployment` seção, que é semelhante a esta:

```bash
##################################################################################################################################
# Deployment
# ----------
```

Esta seção termina com em execução `npm install --production` . Adicione a seção de código de que você precisa para executar a ferramenta necessária *no final* da `Deployment` seção:

- [Bower](#bower)
- [Gulp](#gulp)
- [Grunt](#grunt)

Veja um [exemplo no exemplo de MEAN.js](https://github.com/Azure-Samples/meanjs/blob/master/deploy.sh#L112-L135), em que o script de implantação também executa um `npm install` comando personalizado.

### <a name="bower"></a>Bower

Este trecho de código é executado `bower install` .

```bash
if [ -e "$DEPLOYMENT_TARGET/bower.json" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/bower install
  exitWithMessageOnError "bower failed"
  cd - > /dev/null
fi
```

### <a name="gulp"></a>Gulp

Este trecho de código é executado `gulp imagemin` .

```bash
if [ -e "$DEPLOYMENT_TARGET/gulpfile.js" ]; then
  cd "$DEPLOYMENT_TARGET"
  eval ./node_modules/.bin/gulp imagemin
  exitWithMessageOnError "gulp failed"
  cd - > /dev/null
fi
```

### <a name="grunt"></a>Grunt

Este trecho de código é executado `grunt` .

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

Estrutura Web populares permitem que você acesse informações do `X-Forwarded-*` no seu padrão de aplicativo básico. No [Express](https://expressjs.com/), você pode usar [proxies de confiança](https://expressjs.com/guide/behind-proxies.html). Por exemplo:

```javascript
app.set('trust proxy', 1)
...
if (req.secure) {
  // Do something when HTTPS is used
}
```

## <a name="access-diagnostic-logs"></a>Acessar logs de diagnóstico

::: zone pivot="platform-windows"  

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

::: zone-end

::: zone pivot="platform-linux"

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-linux-no-h.md)]

::: zone-end

## <a name="troubleshooting"></a>Solução de problemas

Quando um aplicativo Node.js funcionando se comporta de forma diferente no serviço de aplicativo ou tem erros, tente o seguinte:

- [Acessar o fluxo de log](#access-diagnostic-logs).
- Teste o aplicativo localmente no modo de produção. O Serviço de Aplicativo executa seus aplicativos do Node.js no modo de produção, portanto, verifique se seu projeto funciona como esperado localmente no modo de produção. Por exemplo:
    - Dependendo do seu *package.jsno*, pacotes diferentes podem ser instalados para o modo de produção ( `dependencies` vs. `devDependencies` ).
    - Determinadas estruturas da Web podem implantar arquivos estáticos de maneira diferente no modo de produção.
    - Determinadas estruturas da Web podem usar scripts de inicialização personalizados ao serem executados no modo de produção.
- Execute seu aplicativo no serviço de aplicativo no modo de desenvolvimento. Por exemplo, em [MEAN.js](https://meanjs.org/), você pode definir seu aplicativo para o modo de desenvolvimento em tempo de execução [definindo a `NODE_ENV` configuração do aplicativo](configure-common.md).

::: zone pivot="platform-linux"

[!INCLUDE [robots933456](../../includes/app-service-web-configure-robots933456.md)]

::: zone-end

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Tutorial: Aplicativo Node.js com o MongoDB](tutorial-nodejs-mongodb-app.md)

::: zone pivot="platform-linux"

> [!div class="nextstepaction"]
> [Perguntas frequentes sobre o Serviço de Aplicativo no Linux](faq-app-service-linux.md)

::: zone-end

