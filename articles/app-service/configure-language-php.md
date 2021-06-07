---
title: Configurar aplicativos de PHP
description: Saiba como configurar um aplicativo PHP nas instâncias nativas do Windows ou em um contêiner PHP predefinido, no serviço Azure App. Este artigo mostra as tarefas de configuração mais comuns.
ms.devlang: php
ms.topic: article
ms.date: 06/02/2020
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: afac8273b5729bcf5470be471145214426dc7dab
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "90055292"
---
# <a name="configure-a-php-app-for-azure-app-service"></a>Configurar um aplicativo PHP para Azure App serviço

Este guia mostra como configurar seus aplicativos Web PHP, back-ends móveis e aplicativos de API no serviço Azure App.

Este guia fornece os principais conceitos e instruções para desenvolvedores de PHP que implantam aplicativos no serviço de aplicativo. Se você nunca usou o Serviço de Aplicativo do Azure, siga o [Início rápido do PHP](quickstart-php.md) e o [tutorial de PHP com MySQL](tutorial-php-mysql-app.md) primeiro.

## <a name="show-php-version"></a>Mostrar a versão do PHP

::: zone pivot="platform-windows"  

Para mostrar a versão atual do PHP, execute o seguinte comando no [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query phpVersion
```

Para mostrar todas as versões do PHP compatíveis, execute o seguinte comando no [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes | grep php
```

::: zone-end

::: zone pivot="platform-linux"

Para mostrar a versão atual do PHP, execute o seguinte comando no [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Para mostrar todas as versões do PHP compatíveis, execute o seguinte comando no [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep PHP
```

::: zone-end

## <a name="set-php-version"></a>Definir a versão do PHP

::: zone pivot="platform-windows"  

Execute o seguinte comando no [Cloud Shell](https://shell.azure.com) para definir a versão do PHP como 7,4:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --php-version 7.4
```

::: zone-end

::: zone pivot="platform-linux"

Execute o seguinte comando no [Cloud Shell](https://shell.azure.com) para definir a versão do PHP como 7.2:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --linux-fx-version "PHP|7.2"
```

::: zone-end

::: zone pivot="platform-windows"  

## <a name="run-composer"></a>Executar o Composer

Se você quiser que o serviço de aplicativo execute o [Composer](https://getcomposer.org/) no momento da implantação, a maneira mais fácil é incluir o compositor em seu repositório.

Em uma janela de terminal local, altere o diretório para a raiz do repositório e siga as instruções em [baixar o Composer](https://getcomposer.org/download/) para baixar o *Composer. Phar* na raiz do diretório.

Execute os seguintes comandos (você precisa do [NPM](https://www.npmjs.com/get-npm) instalado):

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

Adicione a seção de código de que você precisa para executar a ferramenta necessária *no final* da `Deployment` seção:

```bash
# 4. Use composer
echo "$DEPLOYMENT_TARGET"
if [ -e "$DEPLOYMENT_TARGET/composer.json" ]; then
  echo "Found composer.json"
  pushd "$DEPLOYMENT_TARGET"
  php composer.phar install $COMPOSER_ARGS
  exitWithMessageOnError "Composer install failed"
  popd
fi
```

Confirme todas as suas alterações e implante seu código usando git ou a implantação de zip com a automação de compilação habilitada. O Composer agora deve estar em execução como parte da automação da implantação.

## <a name="run-gruntbowergulp"></a>Executar Grunt/Bower/Gulp

Se você quiser que o serviço de aplicativo execute ferramentas de automação populares no momento da implantação, como Grunt, Bower ou Gulp, você precisará fornecer um [script de implantação personalizado](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script). O serviço de aplicativo executa esse script quando você implanta com o Git ou com a [implantação de zip](deploy-zip.md) com a automação de compilação habilitada. 

Para habilitar seu repositório para executar essas ferramentas, você precisa adicioná-las às dependências no *package.jsno.* Por exemplo:

```json
"dependencies": {
  "bower": "^1.7.9",
  "grunt": "^1.0.1",
  "gulp": "^3.9.1",
  ...
}
```

Em uma janela de terminal local, altere o diretório para a raiz do repositório e execute os seguintes comandos (você precisa do [NPM](https://www.npmjs.com/get-npm) instalado):

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

::: zone-end

::: zone pivot="platform-linux"

## <a name="customize-build-automation"></a>Personalizar a automação de build

Se você implantar seu aplicativo usando pacotes Git ou zip com a automação de build ativada, a automação de build do Serviço de Aplicativo percorrerá a seguinte sequência:

1. Executar script personalizado se especificado por `PRE_BUILD_SCRIPT_PATH`.
1. Execute `php composer.phar install`.
1. Executar script personalizado se especificado por `POST_BUILD_SCRIPT_PATH`.

`PRE_BUILD_COMMAND` e `POST_BUILD_COMMAND` são variáveis de ambiente vazias por padrão. Para executar comandos pré-build, defina `PRE_BUILD_COMMAND`. Para executar comandos pós-build, defina `POST_BUILD_COMMAND`.

O exemplo a seguir especifica as duas variáveis para uma série de comandos, separados por vírgulas.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PRE_BUILD_COMMAND="echo foo, scripts/prebuild.sh"
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings POST_BUILD_COMMAND="echo foo, scripts/postbuild.sh"
```

Para obter variáveis de ambiente adicionais para personalizar a automação de build, confira [Configuração do Oryx](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md).

Para obter mais informações sobre como o Serviço de Aplicativo executa e compila aplicativos de PHP no Linux, confira a [Documentação do Oryx: Como aplicativos PHP são detectados e compilados](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/php.md).

## <a name="customize-start-up"></a>Personalizar a inicialização

Por padrão, o contêiner de PHP interno executa o servidor Apache. Na inicialização, ele executa `apache2ctl -D FOREGROUND"`. Se desejar, você poderá executar um comando diferente na inicialização. Para isso, execute o seguinte comando no [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<custom-command>"
```

::: zone-end

## <a name="access-environment-variables"></a>Acessar variáveis de ambiente

No Serviço de Aplicativo, você pode [definir configurações de aplicativo](configure-common.md#configure-app-settings) fora do código do aplicativo. Em seguida, pode acessá-las usando o padrão [getenv()](https://secure.php.net/manual/function.getenv.php). Por exemplo, para acessar uma configuração de aplicativo chamada `DB_HOST`, use o seguinte código:

```php
getenv("DB_HOST")
```

## <a name="change-site-root"></a>Alterar raiz do site

::: zone pivot="platform-windows"  

A estrutura da Web de sua escolha pode usar um subdiretório como a raiz do site. Por exemplo, [Laravel](https://laravel.com/), usa o *público/* subdiretório como a raiz do site.

Para personalizar a raiz do site, defina o caminho do aplicativo virtual para o aplicativo usando o [`az resource update`](/cli/azure/resource#az-resource-update) comando. O exemplo a seguir define a raiz do site para o *público/* subdiretório em seu repositório. 

```azurecli-interactive
az resource update --name web --resource-group <group-name> --namespace Microsoft.Web --resource-type config --parent sites/<app-name> --set properties.virtualApplications[0].physicalPath="site\wwwroot\public" --api-version 2015-06-01
```

Por padrão, o Serviço de Aplicativo do Azure aponta o caminho do aplicativo virtual raiz ( _/_ ) para o diretório raiz dos arquivos de aplicativo implantados (_sites\wwwroot_).

::: zone-end

::: zone pivot="platform-linux"

A estrutura da Web de sua escolha pode usar um subdiretório como a raiz do site. Por exemplo, a [Laravel](https://laravel.com/), usa o subdiretório `public/` como raiz do site.

A imagem do PHP padrão para o Serviço de Aplicativo usa o Apache e não permite a personalização da raiz do site para seu aplicativo. Para contornar essa limitação, adicione um arquivo *.htaccess* à raiz do repositório com o seguinte conteúdo:

```
<IfModule mod_rewrite.c>
    RewriteEngine on
    RewriteCond %{REQUEST_URI} ^(.*)
    RewriteRule ^(.*)$ /public/$1 [NC,L,QSA]
</IfModule>
```

Se você preferir não usar a regeneração de *.htaccess*, implante seu aplicativo Laravel com uma [imagem personalizada do Docker](quickstart-custom-container.md).

::: zone-end

## <a name="detect-https-session"></a>Detectar sessão HTTPS

No Serviço de Aplicativo, a [Terminação SSL](https://wikipedia.org/wiki/TLS_termination_proxy) ocorre nos balanceadores de carga de rede de modo que todas as solicitações HTTPS cheguem ao seu aplicativo como solicitações HTTP não criptografadas. Se a lógica de aplicativo precisar verificar se as solicitações do usuário estão criptografadas ou não, inspecione o cabeçalho `X-Forwarded-Proto`.

```php
if (isset($_SERVER['HTTP_X_FORWARDED_PROTO']) && $_SERVER['HTTP_X_FORWARDED_PROTO'] === 'https') {
// Do something when HTTPS is used
}
```

Estrutura Web populares permitem que você acesse informações do `X-Forwarded-*` no seu padrão de aplicativo básico. Em [CodeIgniter](https://codeigniter.com/), o [is_https()](https://github.com/bcit-ci/CodeIgniter/blob/master/system/core/Common.php#L338-L365) verifica o valor de `X_FORWARDED_PROTO` por padrão.

## <a name="customize-phpini-settings"></a>Personalizar configurações do php.ini

Se você precisar fazer alterações na instalação do PHP, altere qualquer uma das [diretivas php.ini](https://www.php.net/manual/ini.list.php) seguindo estas etapas.

> [!NOTE]
> A melhor maneira de ver a versão do PHP e a configuração *php.ini* atual é chamar [phpinfo()](https://php.net/manual/function.phpinfo.php) em seu aplicativo.
>

### <a name="customize-non-php_ini_system-directives"></a><a name="Customize-non-PHP_INI_SYSTEM directives"></a>Personalizar diretivas diferentes de PHP_INI_SYSTEM

::: zone pivot="platform-windows"  

Para personalizar as diretivas PHP_INI_USER, PHP_INI_PERDIR e PHP_INI_ALL (consulte as [ diretivasphp.ini](https://www.php.net/manual/ini.list.php)), adicione um `.user.ini` arquivo ao diretório raiz do seu aplicativo.

Adicione as definições de configuração ao arquivo `.user.ini` usando a mesma sintaxe que você usaria em um arquivo `php.ini`. Por exemplo, se você quisesse ativar a configuração `display_errors` e definir a configuração `upload_max_filesize` para 10M, o arquivo `.user.ini` conteria este texto:

```
 ; Example Settings
 display_errors=On
 upload_max_filesize=10M

 ; Write errors to d:\home\LogFiles\php_errors.log
 ; log_errors=On
```

Reimplante seu aplicativo com as alterações e reinicie-o.

Como alternativa ao uso de um `.user.ini` arquivo, você pode usar [ini_set ()](https://www.php.net/manual/function.ini-set.php) em seu aplicativo para personalizar essas diretivas não PHP_INI_SYSTEM.

::: zone-end

::: zone pivot="platform-linux"

Para personalizar as diretivas PHP_INI_USER, PHP_INI_PERDIR e PHP_INI_ALL (consulte [diretivas php.ini](https://www.php.net/manual/ini.list.php)), adicione um arquivo *.htaccess* ao diretório raiz do seu aplicativo.

No arquivo *.htaccess*, adicione as diretivas usando a sintaxe `php_value <directive-name> <value>`. Por exemplo:

```
php_value upload_max_filesize 1000M
php_value post_max_size 2000M
php_value memory_limit 3000M
php_value max_execution_time 180
php_value max_input_time 180
php_value display_errors On
php_value upload_max_filesize 10M
```

Reimplante seu aplicativo com as alterações e reinicie-o. Se você implantá-lo com Kudu (por exemplo, usando [Git](deploy-local-git.md)), ele será automaticamente reiniciado após a implantação.

Como alternativa ao uso do *.htaccess*, você pode usar [ini_set()](https://www.php.net/manual/function.ini-set.php) em seu aplicativo para personalizar essas diretivas que não são PHP_INI_SYSTEM.

::: zone-end

### <a name="customize-php_ini_system-directives"></a><a name="customize-php_ini_system-directives"></a>Personalizar diretivas PHP_INI_SYSTEM

::: zone pivot="platform-windows"  

Para personalizar diretivas PHP_INI_SYSTEM (consulte [diretivas php.ini](https://www.php.net/manual/ini.list.php)), você não pode usar a abordagem do *.htaccess*. O Serviço de Aplicativo oferece um mecanismo separado usando a configuração `PHP_INI_SCAN_DIR` do aplicativo.

Primeiro, execute o seguinte comando no [Cloud Shell](https://shell.azure.com) para adicionar uma configuração de aplicativo chamada `PHP_INI_SCAN_DIR`:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PHP_INI_SCAN_DIR="d:\home\site\ini"
```

Navegue até o console do kudu ( `https://<app-name>.scm.azurewebsites.net/DebugConsole` ) e navegue até `d:\home\site` .

Crie um diretório em `d:\home\site` chamado `ini` e, em seguida, crie um arquivo *.ini* no diretório `d:\home\site\ini` (por exemplo, *settings.ini)* com as diretivas que você deseja personalizar. Use a mesma sintaxe que você usaria em um arquivo *php.ini*. 

Por exemplo, para alterar o valor de [expose_php](https://php.net/manual/ini.core.php#ini.expose-php) execute os seguintes comandos:

```bash
cd /home/site
mkdir ini
echo "expose_php = Off" >> ini/setting.ini
```

Para que as alterações entrem em vigor, reinicie o aplicativo.

::: zone-end

::: zone pivot="platform-linux"

Para personalizar diretivas PHP_INI_SYSTEM (consulte [diretivas php.ini](https://www.php.net/manual/ini.list.php)), você não pode usar a abordagem do *.htaccess*. O Serviço de Aplicativo oferece um mecanismo separado usando a configuração `PHP_INI_SCAN_DIR` do aplicativo.

Primeiro, execute o seguinte comando no [Cloud Shell](https://shell.azure.com) para adicionar uma configuração de aplicativo chamada `PHP_INI_SCAN_DIR`:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PHP_INI_SCAN_DIR="/usr/local/etc/php/conf.d:/home/site/ini"
```

`/usr/local/etc/php/conf.d` é o diretório padrão em que o *php.ini* existe. `/home/site/ini` é o diretório personalizado no qual você adicionará um arquivo *.ini* personalizado. Separe os valores com um `:`.

Navegue até a sessão SSH da Web com seu contêiner do Linux (`https://<app-name>.scm.azurewebsites.net/webssh/host`).

Crie um diretório em `/home/site` chamado `ini` e, em seguida, crie um arquivo *.ini* no diretório `/home/site/ini` (por exemplo, *settings.ini)* com as diretivas que você deseja personalizar. Use a mesma sintaxe que você usaria em um arquivo *php.ini*. 

> [!TIP]
> Nos contêineres internos do Linux no Serviço de Aplicativo, */home* é usado como armazenamento compartilhado persistente. 
>

Por exemplo, para alterar o valor de [expose_php](https://php.net/manual/ini.core.php#ini.expose-php) execute os seguintes comandos:

```bash
cd /home/site
mkdir ini
echo "expose_php = Off" >> ini/setting.ini
```

Para que as alterações entrem em vigor, reinicie o aplicativo.

::: zone-end

## <a name="enable-php-extensions"></a>Habilitar extensões PHP

::: zone pivot="platform-windows"  

As instalações internas do PHP contêm as extensões usadas com mais frequência. Você pode habilitar outras extensões da mesma forma que [personaliza as diretivas do php.ini](#customize-php_ini_system-directives).

> [!NOTE]
> A melhor maneira de ver a versão do PHP e a configuração *php.ini* atual é chamar [phpinfo()](https://php.net/manual/function.phpinfo.php) em seu aplicativo.
>

Para habilitar extensões adicionais, siga estas etapas:

Adicione um `bin` diretório ao diretório raiz do seu aplicativo e coloque os `.dll` arquivos de extensão nele (por exemplo, *mongodb.dll*). Verifique se as extensões são compatíveis com a versão do PHP no Azure e também com nts (non-thread-safe) e VC9.

Implante suas alterações.

Siga as etapas em [Personalizar diretivas PHP_INI_SYSTEM](#customize-php_ini_system-directives) e adicione as extensões ao arquivo personalizado *.ini* com as diretivas [extension](https://www.php.net/manual/ini.core.php#ini.extension) ou [zend_extension](https://www.php.net/manual/ini.core.php#ini.zend-extension).

```
extension=d:\home\site\wwwroot\bin\mongodb.dll
zend_extension=d:\home\site\wwwroot\bin\xdebug.dll
```

Para que as alterações entrem em vigor, reinicie o aplicativo.

::: zone-end

::: zone pivot="platform-linux"

As instalações internas do PHP contêm as extensões usadas com mais frequência. Você pode habilitar outras extensões da mesma forma que [personaliza as diretivas do php.ini](#customize-php_ini_system-directives).

> [!NOTE]
> A melhor maneira de ver a versão do PHP e a configuração *php.ini* atual é chamar [phpinfo()](https://php.net/manual/function.phpinfo.php) em seu aplicativo.
>

Para habilitar extensões adicionais, siga estas etapas:

Adicione um diretório `bin` ao diretório raiz do seu aplicativo e coloque os arquivos de extensão `.so` nele (por exemplo, *mongodb.so*). Verifique se as extensões são compatíveis com a versão do PHP no Azure e também com nts (non-thread-safe) e VC9.

Implante suas alterações.

Siga as etapas em [Personalizar diretivas PHP_INI_SYSTEM](#customize-php_ini_system-directives) e adicione as extensões ao arquivo personalizado *.ini* com as diretivas [extension](https://www.php.net/manual/ini.core.php#ini.extension) ou [zend_extension](https://www.php.net/manual/ini.core.php#ini.zend-extension).

```ini
extension=/home/site/wwwroot/bin/mongodb.so
zend_extension=/home/site/wwwroot/bin/xdebug.so
```

Para que as alterações entrem em vigor, reinicie o aplicativo.

::: zone-end

## <a name="access-diagnostic-logs"></a>Acessar logs de diagnóstico

::: zone pivot="platform-windows"  

Use o utilitário [error_log ()](https://php.net/manual/function.error-log.php) padrão para fazer seus logs de diagnóstico aparecerem no serviço Azure app.

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

::: zone-end

::: zone pivot="platform-linux"

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-linux-no-h.md)]

::: zone-end

## <a name="troubleshooting"></a>Solução de problemas

Quando um aplicativo PHP que esteja funcionando se comporta de maneira diferente no Serviço de Aplicativo ou tem erros, tente o seguinte:

- [Acessar o fluxo de log](#access-diagnostic-logs).
- Teste o aplicativo localmente no modo de produção. O serviço de aplicativo executa seu aplicativo no modo de produção, portanto, você precisa certificar-se de que seu projeto funciona como esperado no modo de produção localmente. Por exemplo:
    - Dependendo do *composer.json*, diferentes pacotes podem ser instalados para o modo de produção (`require` versus `require-dev`).
    - Determinadas estruturas da Web podem implantar arquivos estáticos de maneira diferente no modo de produção.
    - Determinadas estruturas da Web podem usar scripts de inicialização personalizados ao serem executados no modo de produção.
- Execute seu aplicativo no Serviço de Aplicativo no modo de depuração. Por exemplo, no [Laravel](https://meanjs.org/), você pode configurar seu aplicativo para gerar mensagens de depuração em produção [definindo a configuração do aplicativo `APP_DEBUG` como `true`](configure-common.md#configure-app-settings).

::: zone pivot="platform-linux"

[!INCLUDE [robots933456](../../includes/app-service-web-configure-robots933456.md)]

::: zone-end

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Tutorial: Aplicativo PHP com o MySQL](tutorial-php-mysql-app.md)

::: zone pivot="platform-linux"

> [!div class="nextstepaction"]
> [Perguntas frequentes sobre o Serviço de Aplicativo no Linux](faq-app-service-linux.md)

::: zone-end

