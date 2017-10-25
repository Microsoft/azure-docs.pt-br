---
title: "Configurar o PHP em Aplicativos Web do Serviço de Aplicativo do Azure | Microsoft Docs"
description: "Saiba como configurar a instalação padrão do PHP ou adicione uma instalação de PHP personalizada para aplicativos Web no Serviço de Aplicativo do Azure."
services: app-service
documentationcenter: php
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 95c4072b-8570-496b-9c48-ee21a223fb60
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 04/25/2017
ms.author: robmcm
ms.openlocfilehash: 624dd416f37aacdb3d2f6e59afdc2efe646e610b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="configure-php-in-azure-app-service-web-apps"></a>Configurar o PHP em aplicativos Web do Serviço de Aplicativo do Azure
## <a name="introduction"></a>Introdução
Este guia mostrará como configurar o tempo de execução do PHP interno para aplicativos Web no [Serviço de Aplicativo do Azure](http://go.microsoft.com/fwlink/?LinkId=529714), fornecer um tempo de execução personalizado do PHP e habilitar extensões. Para usar o Serviço de Aplicativo, inscreva-se para a [avaliação gratuita]. Para aproveitar ao máximo este guia, você deve primeiro criar um aplicativo Web do PHP no Serviço de Aplicativo.

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="how-to-change-the-built-in-php-version"></a>Como: alterar a versão interna do PHP
Por padrão, quando você cria um aplicativo Web do Serviço de Aplicativo, o PHP 5.5 é instalado e fica imediatamente disponível para uso. A melhor forma de visualizar a revisão da versão, sua configuração padrão e as extensões habilitadas é implantar um script que chame a função [phpinfo()] .

As versões 5.6 e 7.0 do PHP também estão disponíveis, mas não são habilitadas por padrão. Para atualizar a versão do PHP, execute um destes métodos:

### <a name="azure-portal"></a>Portal do Azure
1. Navegue até seu aplicativo Web no [Portal do Azure](https://portal.azure.com) e clique no botão **Configurações** .
   
    ![Configurações do aplicativo Web][settings-button]
2. Na folha **Configurações**, selecione **Configurações do aplicativo** e escolha a nova versão do PHP.
   
    ![Configurações do aplicativo][application-settings]
3. Clique no botão **Salvar** na parte superior da folha **Configurações do aplicativo Web**.
   
    ![Salvar definições de configuração][save-button]

### <a name="azure-powershell-windows"></a>PowerShell do Azure (Windows)
1. Abra o Azure PowerShell e faça logon em sua conta:
   
        PS C:\> Login-AzureRmAccount
2. Defina a versão PHP do aplicativo Web.
   
        PS C:\> Set-AzureWebsite -PhpVersion {5.5 | 5.6 | 7.0} -Name {app-name}
3. A versão do PHP agora está definida. Você pode confirmar essas configurações:
   
        PS C:\> Get-AzureWebsite -Name {app-name} | findstr PhpVersion

### <a name="azure-command-line-interface-linux-mac-windows"></a>Interface de linha de comando do Azure (Linux, Mac, Windows)
Para usar a Interface de Linha de Comando do Azure, é necessário ter **Node.js** instalado no computador.

1. Abra o Terminal e faça logon em sua conta.
   
        azure login
2. Defina a versão PHP do aplicativo Web.
   
        azure site set --php-version {5.5 | 5.6 | 7.0} {app-name}

3. A versão do PHP agora está definida. Você pode confirmar essas configurações:
   
        azure site show {app-name}

> [!NOTE] 
> Os comandos da [CLI 2.0 do Azure](https://github.com/Azure/azure-cli) equivalentes aos mencionados acima são:
>
>

    az login
    az appservice web config update --php-version {5.5 | 5.6 | 7.0} -g {resource-group-name} -n {app-name}
    az appservice web config show -g {resource-group-name} -n {app-name}

## <a name="how-to-change-the-built-in-php-configurations"></a>Como: alterar as configurações internas do PHP
Para qualquer tempo de execução interno do PHP, é possível alterar qualquer uma das opções de configuração seguindo as etapas abaixo. (Para obter informações sobre diretrizes de php. ini, consulte [Lista de diretrizes de php. ini].)

### <a name="changing-phpiniuser-phpiniperdir-phpiniall-configuration-settings"></a>Alterando as configurações de PHP\_INI\_USER, PHP\_INI\_PERDIR e PHP\_INI\_ALL
1. Adicione um arquivo [.user.ini] no seu diretório raiz.
2. Adicione as definições de configuração ao arquivo `.user.ini` usando a mesma sintaxe que você usaria em um arquivo `php.ini`. Por exemplo, se você quisesse ativar a configuração `display_errors` e definir a configuração `upload_max_filesize` como 10 M, o arquivo `.user.ini` conteria este texto:
   
        ; Example Settings
        display_errors=On
        upload_max_filesize=10M
   
        ; OPTIONAL: Turn this on to write errors to d:\home\LogFiles\php_errors.log
        ; log_errors=On
3. Implante seu aplicativo Web.
4. Reinicie o aplicativo Web. (É necessário reiniciar, pois a frequência com a qual o PHP lê arquivos `.user.ini` é regida pela configuração`user_ini.cache_ttl`, que é uma configuração de nível de sistema e é, por padrão, 300 segundos (5 minutos). Reiniciar o aplicativo Web força o PHP a ler as novas configurações no arquivo `.user.ini` ).

Uma alternativa ao uso de um arquivo `.user.ini` é usar a função [ini_set()] em scripts para definir opções de configuração que não sejam diretrizes de nível de sistema.

### <a name="changing-phpinisystem-configuration-settings"></a>Alterando as configurações de PHP\_INI\_SYSTEM
1. Adicionar uma Configuração de Aplicativo a seu aplicativo Web com a chave `PHP_INI_SCAN_DIR` e o valor `d:\home\site\ini`
2. Crie um arquivo `settings.ini` usando o console Kudu (http://&lt;nome-do-site&gt;.scm.azurewebsite.net) no diretório `d:\home\site\ini`.
3. Adicione as definições de configuração ao arquivo `settings.ini` usando a mesma sintaxe que você usaria em um arquivo php.ini. Por exemplo, se você quisesse apontar a configuração `curl.cainfo` para um arquivo `*.crt` e definir a configuração 'wincache.maxfilesize' como 512 K, o arquivo `settings.ini` conteria este texto:
   
        ; Example Settings
        curl.cainfo="%ProgramFiles(x86)%\Git\bin\curl-ca-bundle.crt"
        wincache.maxfilesize=512
4. Reinicie seu aplicativo Web para carregar as alterações.

## <a name="how-to-enable-extensions-in-the-default-php-runtime"></a>Como: habilitar extensões no tempo de execução padrão do PHP
Conforme indicado na seção anterior, a melhor forma de visualizar a versão padrão do PHP, sua configuração padrão e as extensões habilitadas é implantar um script que chame [phpinfo()]. Para habilitar extensões adicionais, siga as etapas abaixo:

### <a name="configure-via-ini-settings"></a>Configurar por meio de configurações ini
1. Adicione um diretório `ext` ao diretório `d:\home\site`.
2. Coloque arquivos de extensão `.dll` no diretório `ext` (por exemplo, `php_xdebug.dll`). Certifique-se de que as extensões sejam compatíveis com a versão padrão do PHP e também com nts (non-thread-safe) e VC9.
3. Adicionar uma Configuração de Aplicativo a seu aplicativo Web com a chave `PHP_INI_SCAN_DIR` e o valor `d:\home\site\ini`
4. Crie um arquivo `ini` em `d:\home\site\ini` chamado `extensions.ini`.
5. Adicione as definições de configuração ao arquivo `extensions.ini` usando a mesma sintaxe que você usaria em um arquivo php.ini. Por exemplo, se você desejasse habilitar as extensões MongoDB e XDebug, o arquivo `extensions.ini` conteria este texto:
   
        ; Enable Extensions
        extension=d:\home\site\ext\php_mongo.dll
        zend_extension=d:\home\site\ext\php_xdebug.dll
6. Reinicie seu aplicativo Web para carregar as alterações.

### <a name="configure-via-app-setting"></a>Configurar por meio de Configuração de Aplicativo
1. Adicione um diretório `bin` ao diretório raiz.
2. Coloque arquivos de extensão `.dll` no diretório `bin` (por exemplo, `php_xdebug.dll`). Certifique-se de que as extensões sejam compatíveis com a versão padrão do PHP e também com nts (non-thread-safe) e VC9.
3. Implante seu aplicativo Web.
4. Navegue até o aplicativo Web no Portal do Azure e clique no botão **Configurações** .
   
    ![Configurações do aplicativo Web][settings-button]
5. Na folha **Configurações**, selecione **Configurações do Aplicativo** e role até a seção **Configurações do Aplicativo**.
6. Na seção **Configurações do aplicativo**, crie uma chave **PHP_EXTENSIONS**. O valor para essa chave seria um caminho relativo à raiz do site: **bin\seu-arquivo-de-ext**.
   
    ![Habilitar extensões em configurações do aplicativo][php-extensions]
7. Clique no botão **Salvar** na parte superior da folha **Configurações do aplicativo Web**.
   
    ![Salvar definições de configuração][save-button]

Também há suporte para extensões Zend usando uma chave **PHP_ZENDEXTENSIONS**. Para habilitar várias extensões, inclua uma lista de arquivos `.dll` separados por vírgulas para o valor de configuração do aplicativo.

## <a name="how-to-use-a-custom-php-runtime"></a>Como: usar um tempo de execução personalizado do PHP
Em vez do tempo de execução padrão do PHP, os aplicativos Web do Serviço de Aplicativo podem usar um tempo de execução de PHP fornecido por você para executar scripts PHP. O tempo de execução que você fornece pode ser configurado por um arquivo `php.ini` também fornecido por você. Para usar um tempo de execução personalizado do PHP com aplicativos Web, siga as etapas abaixo.

1. Obtenha uma versão do PHP ou VC11 para Windows que seja não thread safe e compatível com a versão VC9. Versões recentes do PHP para Windows podem ser encontradas aqui: [http://windows.php.net/download/]. Versões mais antigas podem ser encontradas neste arquivo morto: [http://windows.php.net/downloads/releases/archives/].
2. Modifique o arquivo `php.ini` para o seu tempo de execução. Observe que quaisquer definições de configuração que forem diretrizes exclusivamente de nível de sistema serão ignoradas por Aplicativos Web. (Para informações sobre diretrizes de nível de sistema apenas, consulte [Lista de diretrizes de php. ini].)
3. Opcionalmente, adicione extensões para o seu tempo de execução do PHP e habilite-as no arquivo `php.ini` .
4. Adicione o diretório `bin` ao seu diretório raiz e coloque lá o diretório que contém o seu tempo de execução do PHP (por exemplo, `bin\php`).
5. Implante seu aplicativo Web.
6. Navegue até o aplicativo Web no Portal do Azure e clique no botão **Configurações** .
   
    ![Configurações do aplicativo Web][settings-button]
7. Na folha **Configurações**, selecione **Configurações do Aplicativo** e role até a seção **Mapeamentos de manipulador**. Adicione `*.php` ao campo Extensão e adicionar o caminho para o executável `php-cgi.exe`. Se você colocar seu tempo de execução do PHP no diretório `bin` na raiz do aplicativo, o caminho será `D:\home\site\wwwroot\bin\php\php-cgi.exe`.
   
    ![Especificar o manipulador em mapeamentos de manipulador][handler-mappings]
8. Clique no botão **Salvar** na parte superior da folha **Configurações do aplicativo Web**.
   
    ![Salvar definições de configuração][save-button]

<a name="composer" />

## <a name="how-to-enable-composer-automation-in-azure"></a>Como habilitar a automação do Criador no Azure
Por padrão, o Serviço de Aplicativo não fará nada com o composer.json se você tiver um em seu projeto PHP. Se você usar a [implantação Git](app-service-deploy-local-git.md), você poderá habilitar o composer.json durante o `git push` habilitando a extensão do Criador.

> [!NOTE]
> Você pode [votar para obter o suporte de primeira classe do Criador no Serviço de Aplicativo aqui](https://feedback.azure.com/forums/169385-web-apps-formerly-websites/suggestions/6477437-first-class-support-for-composer-and-pip).
> 
> 

1. Na folha do aplicativo Web PHP no [portal do Azure](https://portal.azure.com), clique em **Ferramentas** > **Extensões**.
   
    ![Folha de configurações do Portal do Azure para habilitar a automação do Criador no Azure](./media/web-sites-php-configure/composer-extension-settings.png)
2. Clique em **Adicionar** e em **Criador**.
   
    ![Adicionar extensão do Criador para habilitar sua respectiva automação no Azure](./media/web-sites-php-configure/composer-extension-add.png)
3. Clique em **OK** para aceitar os termos legais. Clique em **OK** novamente para adicionar a extensão.
   
    Agora, a folha **Extensões instaladas** mostrará a Extensão do criador.  
    ![Aceite os termos legais para habilitar a automação do Criador no Azure](./media/web-sites-php-configure/composer-extension-view.png)
4. Agora, execute `git add`, `git commit` e `git push` como na seção anterior. Agora, você verá que o Compositor está instalando dependências definidas no composer.json.
   
    ![Implantação do Git com a automação do Criador no Azure](./media/web-sites-php-configure/composer-extension-success.png)

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações, veja o [Centro de Desenvolvimento PHP](/develop/php/).

> [!NOTE]
> Se você deseja começar a usar o Serviço de Aplicativo do Azure antes de se inscrever em uma conta do Azure, vá até [Experimentar o Serviço de Aplicativo](https://azure.microsoft.com/try/app-service/), em que você pode criar imediatamente um aplicativo Web inicial de curta duração no Serviço de Aplicativo. Nenhum cartão de crédito é exigido, sem compromissos.
> 
> 

[avaliação gratuita]: https://www.windowsazure.com/pricing/free-trial/
[phpinfo()]: http://php.net/manual/en/function.phpinfo.php
[select-php-version]: ./media/web-sites-php-configure/select-php-version.png
[Lista de diretrizes de php. ini]: http://www.php.net/manual/en/ini.list.php
[.user.ini]: http://www.php.net/manual/en/configuration.file.per-user.php
[ini_set()]: http://www.php.net/manual/en/function.ini-set.php
[application-settings]: ./media/web-sites-php-configure/application-settings.png
[settings-button]: ./media/web-sites-php-configure/settings-button.png
[save-button]: ./media/web-sites-php-configure/save-button.png
[php-extensions]: ./media/web-sites-php-configure/php-extensions.png
[handler-mappings]: ./media/web-sites-php-configure/handler-mappings.png
[http://windows.php.net/download/]: http://windows.php.net/download/
[http://windows.php.net/downloads/releases/archives/]: http://windows.php.net/downloads/releases/archives/
[SETPHPVERCLI]: ./media/web-sites-php-configure/ChangePHPVersion-XPlatCLI.png
[GETPHPVERCLI]: ./media/web-sites-php-configure/ShowPHPVersion-XplatCLI.png
[SETPHPVERPS]: ./media/web-sites-php-configure/ChangePHPVersion-PS.png
[GETPHPVERPS]: ./media/web-sites-php-configure/ShowPHPVersion-PS.png

