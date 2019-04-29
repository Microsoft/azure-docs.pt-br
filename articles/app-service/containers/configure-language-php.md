---
title: Configurar aplicativos PHP - serviço de aplicativo do Azure | Microsoft Docs
description: Saiba como configurar aplicativos PHP funcione no serviço de aplicativo do Azure
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
ms.openlocfilehash: 11d0648ee5090f02cb96c2d42a8d90cc3ea0ed28
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60853296"
---
# <a name="configure-a-linux-php-app-for-azure-app-service"></a>Configurar um aplicativo PHP de Linux para o serviço de aplicativo do Azure

Este guia mostra como configurar o tempo de execução do PHP interno para aplicativos web, back-ends móveis e aplicativos de API no serviço de aplicativo do Azure.

Este guia fornece instruções para desenvolvedores PHP que usam um contêiner interno do Linux no serviço de aplicativo e principais conceitos. Se você nunca usou o serviço de aplicativo do Azure, siga as [início rápido PHP](quickstart-php.md) e [PHP com MySQL tutorial](tutorial-php-mysql-app.md) primeiro.

## <a name="show-php-version"></a>Mostrar a versão do PHP

Para mostrar a versão atual do PHP, execute o seguinte comando [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Para mostrar todas as versões do PHP, execute o seguinte comando [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep PHP
```

## <a name="set-php-version"></a>Definir a versão do PHP

Execute o seguinte comando [Cloud Shell](https://shell.azure.com) para definir a versão do PHP para a versão 7.2:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --linux-fx-version "PHP|7.2"
```

## <a name="run-composer"></a>Execute o Composer

Por padrão, o Kudu não executados [Composer](https://getcomposer.org/). Para habilitar a automação do Composer durante a implantação do Kudu, você precisará fornecer um [script de implantação personalizado](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script).

Em uma janela do terminal local, altere o diretório para a raiz do seu repositório. Siga as [etapas de instalação de linha de comando](https://getcomposer.org/download/) para baixar *Phar*.

Execute os seguintes comandos:

```bash
npm install kuduscript -g
kuduscript --php --scriptType bash --suppressPrompt
```

Raiz do repositório agora tem dois novos arquivos além *Phar*: *. Deployment* e *deploy.sh*. Esses arquivos funcionam tanto para tipos de Windows e Linux do serviço de aplicativo.

Abra *deploy.sh* e localize o `Deployment` seção. Substitua a seção inteira com o código a seguir:

```bash
##################################################################################################################################
# Deployment
# ----------

echo PHP deployment

# 1. KuduSync
if [[ "$IN_PLACE_DEPLOYMENT" -ne "1" ]]; then
  "$KUDU_SYNC_CMD" -v 50 -f "$DEPLOYMENT_SOURCE" -t "$DEPLOYMENT_TARGET" -n "$NEXT_MANIFEST_PATH" -p "$PREVIOUS_MANIFEST_PATH" -i ".git;.hg;.deployment;deploy.sh"
  exitWithMessageOnError "Kudu Sync failed"
fi

# 3. Initialize Composer Config
initializeDeploymentConfig

# 4. Use composer
echo "$DEPLOYMENT_TARGET"
if [ -e "$DEPLOYMENT_TARGET/composer.json" ]; then
  echo "Found composer.json"
  pushd "$DEPLOYMENT_TARGET"
  php composer.phar install $COMPOSER_ARGS
  exitWithMessageOnError "Composer install failed"
  popd
fi
##################################################################################################################################
```

Confirme todas as suas alterações e implantar seu código novamente. Composer agora deve estar em execução como parte da automação da implantação.

## <a name="customize-start-up"></a>Personalizar a inicialização

Por padrão, o contêiner interno do PHP, execute o servidor Apache. Na inicialização, ele é executado `apache2ctl -D FOREGROUND"`. Se desejar, você pode executar um comando diferente na inicialização, executando o seguinte comando na [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<custom-command>"
```

## <a name="access-environment-variables"></a>Acessar variáveis de ambiente

No Serviço de Aplicativo, você pode [definir configurações de aplicativo](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#app-settings) fora do código do aplicativo. Em seguida, você pode acessá-los usando o padrão [GETENV ()](https://secure.php.net/manual/function.getenv.php) padrão. Por exemplo, para acessar uma configuração de aplicativo chamada `DB_HOST`, use o seguinte código:

```php
getenv("DB_HOST")
```

## <a name="change-site-root"></a>Alterar raiz do site

A estrutura da web de sua escolha pode usar um subdiretório como a raiz do site. Por exemplo, [Laravel](https://laravel.com/), usa o `public/` subdiretório como a raiz do site.

A imagem do PHP padrão para o serviço de aplicativo usa Apache, e ele não permite que você personalize a raiz do site para seu aplicativo. Para contornar essa limitação, adicione uma *. htaccess* arquivo à raiz do seu repositório com o seguinte conteúdo:

```
<IfModule mod_rewrite.c>
    RewriteEngine on

    RewriteRule ^.*$ /public/$1 [NC,L,QSA]
</IfModule>
```

Se você preferir não usar a regeneração de *.htaccess*, implante seu aplicativo Laravel com uma [imagem personalizada do Docker](quickstart-docker-go.md).

## <a name="detect-https-session"></a>Detectar sessão HTTPS

No Serviço de Aplicativo, a [Terminação SSL](https://wikipedia.org/wiki/TLS_termination_proxy) ocorre nos balanceadores de carga de rede de modo que todas as solicitações HTTPS cheguem ao seu aplicativo como solicitações HTTP não criptografadas. Se a lógica de aplicativo precisar verificar se as solicitações do usuário estão criptografadas ou não, inspecione o cabeçalho `X-Forwarded-Proto`.

```php
if (isset($_SERVER['X-Forwarded-Proto']) && $_SERVER['X-Forwarded-Proto'] === 'https') {
  // Do something when HTTPS is used
}
```

Estrutura Web populares permitem que você acesse informações do `X-Forwarded-*` no seu padrão de aplicativo básico. Em [CodeIgniter](https://codeigniter.com/), o [is_https()](https://github.com/bcit-ci/CodeIgniter/blob/master/system/core/Common.php#L338-L365) verifica o valor de `X_FORWARDED_PROTO` por padrão.

## <a name="customize-phpini-settings"></a>Personalizar as configurações de PHP. ini

Se você precisar fazer alterações em sua instalação do PHP, você pode alterar qualquer um de [ini](http://www.php.net/manual/ini.list.php) seguindo estas etapas.

> [!NOTE]
> A melhor maneira de ver a versão do PHP e o atual *PHP. ini* configuração é chamar [phpinfo ()](https://php.net/manual/function.phpinfo.php) em seu aplicativo.
>

### <a name="customize-non-phpinisystem-directives"></a>Personalizar as diretivas de não-PHP_INI_SYSTEM

Para personalizar as diretivas PHP_INI_USER, PHP_INI_PERDIR e PHP_INI_ALL (consulte [ini](http://www.php.net/manual/ini.list.php)), adicione um *. htaccess* arquivo para o diretório raiz do seu aplicativo.

No *. htaccess* do arquivo, adicione as diretivas usando o `php_value <directive-name> <value>` sintaxe. Por exemplo: 

```
php_value upload_max_filesize 1000M
php_value post_max_size 2000M
php_value memory_limit 3000M
php_value max_execution_time 180
php_value max_input_time 180
php_value display_errors On
php_value upload_max_filesize 10M
```

Reimplante seu aplicativo com as alterações e reiniciá-lo. Se você implantá-lo com o Kudu (por exemplo, usando [Git](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)), ele será reiniciado automaticamente após a implantação.

Como uma alternativa ao uso *. htaccess*, você pode usar [ini_set ()](http://www.php.net/manual/function.ini-set.php) em seu aplicativo para personalizar essas diretivas não PHP_INI_SYSTEM.

### <a name="customize-phpinisystem-directives"></a>Personalizar as diretivas PHP_INI_SYSTEM

Para personalizar as diretivas PHP_INI_SYSTEM (consulte [ini](http://www.php.net/manual/ini.list.php)), você não pode usar o *. htaccess* abordagem. Serviço de aplicativo fornece um mecanismo separado usando o `PHP_INI_SCAN_DIR` configuração de aplicativo.

Primeiro, execute o seguinte comando [Cloud Shell](https://shell.azure.com) para adicionar um configuração de aplicativo chamada `PHP_INI_SCAN_DIR`:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PHP_INI_SCAN_DIR="/usr/local/etc/php/conf.d:/home/site/ini"
```

`/usr/local/etc/php/conf.d` é o diretório padrão em que *PHP. ini* existe. `/home/site/ini` é o diretório personalizado no qual você adicionará um personalizado *. ini* arquivo. Separe os valores com um `:`.

Navegue até a sessão SSH da web com seu contêiner do Linux (`https://cephalin-container.scm.azurewebsites.net/webssh/host`).

Crie um diretório no `/home/site` chamado `ini`, em seguida, crie um *. ini* de arquivo no `/home/site/ini` diretório (por exemplo, *ini)* com as diretivas que você deseja personalizar. Use a mesma sintaxe que você usaria em um *PHP. ini* arquivo. 

> [!TIP]
> Nos contêineres internos do Linux no serviço de aplicativo */home* é usado como armazenamento compartilhado persistente. 
>

Por exemplo, para alterar o valor de [expose_php](http://php.net/manual/ini.core.php#ini.expose-php) execute os seguintes comandos:

```bash
cd /home/site
mkdir ini
echo "expose_php = Off" >> ini/setting.ini
```

Para que as alterações entrem em vigor, reinicie o aplicativo.

## <a name="enable-php-extensions"></a>Habilitar extensões PHP

As instalações do PHP internas contém as extensões mais comumente usadas. Você pode habilitar extensões adicionais da mesma maneira que você [personalizar as diretivas do PHP. ini](#customize-php_ini_system-directives).

> [!NOTE]
> A melhor maneira de ver a versão do PHP e o atual *PHP. ini* configuração é chamar [phpinfo ()](https://php.net/manual/function.phpinfo.php) em seu aplicativo.
>

Para habilitar extensões adicionais, siga estas etapas:

Adicionar um `bin` diretório para o diretório raiz do seu aplicativo e colocar o `.so` arquivos de extensão nela (por exemplo, *mongodb.so*). Certifique-se de que as extensões são compatíveis com a versão do PHP no Azure e e VC9 compatível com o non-thread-safe (nts).

Implante as alterações.

Siga as etapas em [diretivas de personalizar PHP_INI_SYSTEM](#customize-php_ini_system-directives), adicione as extensões em personalizado *. ini* arquivo com o [extensão](https://www.php.net/manual/ini.core.php#ini.extension) ou [zend_extension ](https://www.php.net/manual/ini.core.php#ini.zend-extension) diretivas.

```ini
extension=/home/site/wwwroot/bin/mongodb.so
zend_extension=/home/site/wwwroot/bin/xdebug.so
```

Para que as alterações entrem em vigor, reinicie o aplicativo.

## <a name="access-diagnostic-logs"></a>Acessar logs de diagnóstico

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>Abra a sessão SSH aberta no navegador

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="troubleshooting"></a>solução de problemas

Quando um aplicativo PHP em funcionamento se comporta de forma diferente no serviço de aplicativo ou tem erros, tente o seguinte:

- [Acessar o fluxo de log](#access-diagnostic-logs).
- Teste o aplicativo localmente no modo de produção. Serviço de aplicativo executa seus aplicativos Node. js no modo de produção, portanto, você precisará certificar-se de que seu projeto funcione conforme o esperado no modo de produção localmente. Por exemplo: 
    - Dependendo da sua *Composer. JSON*, pacotes diferentes podem ser instalados para o modo de produção (`require` versus `require-dev`).
    - Determinadas estruturas da web podem implantar arquivos estáticos diferentemente em modo de produção.
    - Determinadas estruturas da web podem usar scripts de inicialização personalizada ao ser executado no modo de produção.
- Execute seu aplicativo no serviço de aplicativo no modo de depuração. Por exemplo, na [Laravel](http://meanjs.org/), você pode configurar seu aplicativo para gerar mensagens de depuração em produção por [configuração de `APP_DEBUG` configuração do aplicativo para `true` ](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json).

### <a name="robots933456"></a>robots933456

Você pode ver a seguinte mensagem nos logs de contêiner:

```
2019-04-08T14:07:56.641002476Z "-" - - [08/Apr/2019:14:07:56 +0000] "GET /robots933456.txt HTTP/1.1" 404 415 "-" "-"
```

Você pode ignorar com segurança esta mensagem. `/robots933456.txt` é um caminho de URL fictício que usa o serviço de aplicativo para verificar se o contêiner é capaz de atender às solicitações. Uma resposta 404 simplesmente indica que o caminho não existir, mas ele permite que o serviço de aplicativo que o contêiner está íntegra e pronta para responder às solicitações.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Tutorial: Aplicativo PHP com o MySQL](tutorial-php-mysql-app.md)

> [!div class="nextstepaction"]
> [Perguntas frequentes sobre o Serviço de Aplicativo no Linux](app-service-linux-faq.md)