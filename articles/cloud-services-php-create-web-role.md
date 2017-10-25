---
title: "Criar funções Web e de trabalho do Azure para PHP | Microsoft Docs"
description: "Um guia para a criação de funções da Web do PHP e de trabalho em um serviço de nuvem do Azure e para a configuração do tempo de execução do PHP."
services: 
documentationcenter: php
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 9f7ccda0-bd96-4f7b-a7af-fb279a9e975b
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 04/25/2017
ms.author: robmcm
ms.openlocfilehash: 214fdcfe20f3fa4ebcbe41308404f8b7e7d15310
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-create-php-web-and-worker-roles"></a>Como criar funções Web e de trabalho do PHP
## <a name="overview"></a>Visão geral
Este guia mostrará como criar funções Web ou de trabalho do PHP em um ambiente de desenvolvimento Windows, como escolher uma versão específica do PHP nas versões "internas" disponíveis, como alterar a configuração do PHP, habilitar extensões e, finalmente, como implantar no Azure. Também descreve como configurar uma função Web ou de trabalho para usar um tempo de execução do PHP (com configuração e extensões personalizadas) que você fornece.

## <a name="what-are-php-web-and-worker-roles"></a>O que são funções Web e de Trabalho do PHP?
O Azure fornece três modelos de computação para a execução de aplicativos: Serviço de Aplicativo do Azure, Máquinas Virtuais do Azure e Serviços de Nuvem do Azure. Todos os três modelos oferecem suporte ao PHP. Os Serviços de Nuvem, que incluem as funções Web e de trabalho, fornecem a *PaaS (plataforma como serviço)*. Dentro de um serviço de nuvem, uma função web fornece um servidor Web de IIS (Serviços de Informações da Internet) dedicado, usado para hospedar aplicativos Web de front-end. Uma função de trabalho pode executar tarefas assíncronas, de execução longa ou perpétuas, independentemente da interação do usuário ou da entrada.

Para obter mais informações sobre essas opções, consulte [Opções de hospedagem de computação fornecidas pelo Azure](cloud-services/cloud-services-choose-me.md).

## <a name="download-the-azure-sdk-for-php"></a>Baixar o SDK do Azure para PHP
O [SDK do Azure para PHP] consiste em vários componentes. Este artigo usará dois deles: o Azure PowerShell e os emuladores do Azure. Esses dois componentes podem ser instalados pelo Microsoft Web Platform Installer. Para obter mais informações, confira [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview).

## <a name="create-a-cloud-services-project"></a>Criar um projeto de Serviço de Nuvem
A primeira etapa na criação de uma função Web ou de trabalho do PHP é criar um projeto de Serviço do Azure. um projeto de Serviço do Azure serve como um contêiner lógico para as funções da Web e de trabalho, e contém os arquivos de [definição do serviço (.csdef) ] e [configuração do serviço (.cscfg)] do projeto.

Para criar um novo projeto de Serviço do Azure, execute o Azure PowerShell como administrador e execute o seguinte comando:

    PS C:\>New-AzureServiceProject myProject

Esse comando criará um novo diretório (`myProject`) ao qual você pode adicionar funções Web e de trabalho.

## <a name="add-php-web-or-worker-roles"></a>Adicionar funções de trabalho ou Web PHP
Para adicionar uma função Web do PHP a um projeto, execute o seguinte comando no diretório raiz do projeto:

    PS C:\myProject> Add-AzurePHPWebRole roleName

Para uma função de trabalho, use este comando:

    PS C:\myProject> Add-AzurePHPWorkerRole roleName

> [!NOTE]
> O `roleName` é opcional. Se for omitido, o nome da função será gerado automaticamente. A primeira função web criada será `WebRole1`, a segunda será `WebRole2` e assim por diante. A primeira função de trabalho criada será `WorkerRole1`, a segunda será `WorkerRole2` e assim por diante.
>
>

## <a name="specify-the-built-in-php-version"></a>Especificar a versão do PHP interno
Quando você adiciona uma função Web ou de trabalho do PHP a um projeto, os arquivos de configuração do projeto são modificados para que o PHP seja instalado em cada instância da web ou de trabalho do seu aplicativo quando ele for implantado. Para ver a versão do PHP que será instalada por padrão, execute o seguinte comando:

    PS C:\myProject> Get-AzureServiceProjectRoleRuntime

A saída do comando acima será semelhante à mostrada a seguir. Neste exemplo, o sinalizador `IsDefault` está definido como `true` para PHP 5.3.17 indicando que ele será a versão do PHP padrão instalada.

```
Runtime Version     PackageUri                      IsDefault
------- -------     ----------                      ---------
Node 0.6.17         http://nodertncu.blob.core...   False
Node 0.6.20         http://nodertncu.blob.core...   True
Node 0.8.4          http://nodertncu.blob.core...   False
IISNode 0.1.21      http://nodertncu.blob.core...   True
Cache 1.8.0         http://nodertncu.blob.core...   True
PHP 5.3.17          http://nodertncu.blob.core...   True
PHP 5.4.0           http://nodertncu.blob.core...   False
```

Você pode definir a versão de tempo de execução do PHP para qualquer uma das versões do PHP listadas. Por exemplo, para definir a versão do PHP (para uma função com o nome `roleName`) como 5.4.0, use o comando a seguir:

    PS C:\myProject> Set-AzureServiceProjectRole roleName php 5.4.0

> [!NOTE]
> As versões de PHP disponíveis podem mudar no futuro.
>
>

## <a name="customize-the-built-in-php-runtime"></a>Personalizar o tempo de execução do PHP interno
Você tem controle total sobre a configuração do tempo de execução do PHP que é instalado quando você segue as etapas acima, incluindo a modificação das configurações de `php.ini` e a habilitação de extensões.

Para personalizar o tempo de execução do PHP interno, siga estas etapas:

1. Adicione uma nova pasta, chamada `php`, ao diretório `bin` da sua função web. Para uma função de trabalho, adicione-o ao diretório raiz da função.
2. Na pasta `php`, crie outra pasta chamada `ext`. Coloque todos os arquivos de extensão `.dll` (por exemplo, `php_mongo.dll`) que você deseja habilitar nessa pasta.
3. Adicionar um arquivo `php.ini` à pasta `php`. Habilite todas as extensões personalizadas e defina todas as diretivas do PHP nesse arquivo. Por exemplo, se você quiser ativar o `display_errors` e habilitar a extensão `php_mongo.dll`, os conteúdos do seu arquivo `php.ini` serão da seguinte maneira:

        display_errors=On
        extension=php_mongo.dll

> [!NOTE]
> Todas as configurações que você não definir explicitamente no arquivo `php.ini` que você fornecer serão definidas automaticamente para seus valores padrão. No entanto, lembre-se de que você pode adicionar um arquivo `php.ini` completo.
>
>

## <a name="use-your-own-php-runtime"></a>Usar seu próprio tempo de execução PHP
Em alguns casos, em vez de selecionar um tempo de execução do PHP interno e configurá-lo conforme descrito acima, você pode desejar fornecer seu próprio tempo de execução do PHP. Por exemplo, você pode usar o mesmo tempo de execução do PHP em uma função Web ou de trabalho que você usa no seu ambiente de desenvolvimento. Isso torna mais fácil garantir que o aplicativo não mudará o comportamento no ambiente de produção.

### <a name="configure-a-web-role-to-use-your-own-php-runtime"></a>Configurar uma função web para usar o seu próprio tempo de execução do PHP
Para configurar uma função web para usar um tempo de execução do PHP fornecido por você, siga as etapas a seguir:

1. Crie um projeto de Serviço do Azure e adicione uma função Web do PHP conforme descrito anteriormente neste tópico.
2. Crie uma pasta `php` na pasta `bin` que está no diretório raiz de sua função web e adicione o tempo de execução do PHP (todos os binários, arquivos de configuração, subpastas etc.) à pasta `php`.
3. (OPCIONAL) Se o tempo de execução do PHP usar os [Drivers da Microsoft para PHP para SQL Server][sqlsrv drivers], você precisará configurar a função Web para instalar o [SQL Server Native Client 2012][sql native client] quando ela for provisionada. Para fazer isso, adicione o [instalador sqlncli.msi x64] à pasta `bin` no diretório-raiz de sua função Web. O script de inicialização descrito na próxima etapa executará o instalador silenciosamente quando a função for provisionada. Se o tempo de execução do PHP não usar os Drivers Microsoft para PHP para SQL Server, você poderá remover a seguinte linha do script mostrado na próxima etapa:

        msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES
4. Defina uma tarefa de inicialização que configura os [Serviços de Informações da Internet (IIS)][iis.net] para usar o tempo de execução do PHP para manipular solicitações de páginas `.php`. Para fazer isso, abra o arquivo `setup_web.cmd` (no arquivo `bin` do diretório raiz da função web) em um editor de texto e substitua seu conteúdo pelo script a seguir:

    ```cmd
    @ECHO ON
    cd "%~dp0"

    if "%EMULATED%"=="true" exit /b 0

    msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES

    SET PHP_FULL_PATH=%~dp0php\php-cgi.exe
    SET NEW_PATH=%PATH%;%RoleRoot%\base\x86

    %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='%PHP_FULL_PATH%',maxInstances='12',idleTimeout='60000',activityTimeout='3600',requestTimeout='60000',instanceMaxRequests='10000',protocol='NamedPipe',flushNamedPipe='False']" /commit:apphost
    %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='%PHP_FULL_PATH%'].environmentVariables.[name='PATH',value='%NEW_PATH%']" /commit:apphost
    %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='%PHP_FULL_PATH%'].environmentVariables.[name='PHP_FCGI_MAX_REQUESTS',value='10000']" /commit:apphost
    %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/handlers /+"[name='PHP',path='*.php',verb='GET,HEAD,POST',modules='FastCgiModule',scriptProcessor='%PHP_FULL_PATH%',resourceType='Either',requireAccess='Script']" /commit:apphost
    %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /"[fullPath='%PHP_FULL_PATH%'].queueLength:50000"
    ```
5. Adicione os arquivos do aplicativo ao diretório raiz da função Web. Esse será o diretório raiz do servidor web.
6. Publique seu aplicativo como descrito na seção abaixo, [Publicar seu aplicativo](#publish-your-application).

> [!NOTE]
> O script `download.ps1` (na pasta `bin` do diretório raiz da função web) pode ser excluído após a realização das etapas descritas acima para usar seu próprio tempo de execução do PHP.
>
>

### <a name="configure-a-worker-role-to-use-your-own-php-runtime"></a>Configurar uma função de trabalho para usar o seu próprio tempo de execução do PHP
Para configurar uma função de trabalho para usar um tempo de execução do PHP fornecido por você, siga as etapas a seguir:

1. Crie um projeto de Serviço do Azure e adicione uma função de trabalho do PHP conforme descrito anteriormente neste tópico.
2. Crie uma pasta `php` no diretório raiz de sua função de trabalho e adicione o tempo de execução do PHP (todos os binários, arquivos de configuração, subpastas etc.) para a pasta `php`.
3. (OPCIONAL) Se o tempo de execução do PHP usar [Drivers da Microsoft para PHP para SQL Server][sqlsrv drivers], você precisará configurar a função de trabalho para instalar o [SQL Server Native Client 2012][sql native client] quando ela for provisionada. Para fazer isso, adicione o [instalador sqlncli.msi x64] ao diretório raiz da função de trabalho. O script de inicialização descrito na próxima etapa executará o instalador silenciosamente quando a função for provisionada. Se o tempo de execução do PHP não usar os Drivers Microsoft para PHP para SQL Server, você poderá remover a seguinte linha do script mostrado na próxima etapa:

        msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES
4. Defina uma tarefa de inicialização que adiciona o executável `php.exe` à variável de ambiente PATH da função de trabalho quando a função é configurada. Para fazer isso, abra o arquivo `setup_worker.cmd` (no diretório raiz da função de trabalho) em um editor de texto e substitua seu conteúdo pelo script a seguir:

    ```cmd
    @echo on

    cd "%~dp0"

    echo Granting permissions for Network Service to the web root directory...
    icacls ..\ /grant "Network Service":(OI)(CI)W
    if %ERRORLEVEL% neq 0 goto error
    echo OK

    if "%EMULATED%"=="true" exit /b 0

    msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES

    setx Path "%PATH%;%~dp0php" /M

    if %ERRORLEVEL% neq 0 goto error

    echo SUCCESS
    exit /b 0

    :error

    echo FAILED
    exit /b -1
    ```
5. Adicione os arquivos do aplicativo ao diretório raiz da função de trabalho.
6. Publique seu aplicativo como descrito na seção abaixo, [Publicar seu aplicativo](#publish-your-application).

## <a name="run-your-application-in-the-compute-and-storage-emulators"></a>Executar seu aplicativo nos emuladores de computação e de armazenamento
Os emuladores do Azure fornecem um ambiente local no qual você pode testar seu aplicativo do Azure antes de implantá-lo na nuvem. Existem algumas diferenças entre os emuladores e o ambiente do Azure. Para entender isso melhor, consulte [Usar o emulador de armazenamento do Azure para desenvolvimento e teste](storage/common/storage-use-emulator.md).

Observe que você deve ter instalado o PHP localmente para usar o emulador de computação. O emulador de computação usará sua instalação local do PHP para executar o aplicativo.

Para executar seu projeto nos emuladores, execute o seguinte comando no diretório raiz do projeto:

    PS C:\MyProject> Start-AzureEmulator

Você verá uma saída semelhante a essa:

    Creating local package...
    Starting Emulator...
    Role is running at http://127.0.0.1:81
    Started

Você pode ver o aplicativo em execução no emulador abrindo um navegador da Web e navegando até o endereço local mostrado na saída (`http://127.0.0.1:81` na saída de exemplo acima).

Para parar os emuladores, execute este comando:

    PS C:\MyProject> Stop-AzureEmulator

## <a name="publish-your-application"></a>Publicar seu aplicativo
Para publicar seu aplicativo, você precisa primeiro importar suas configurações de publicação usando o cmdlet [Import-AzurePublishSettingsFile](https://msdn.microsoft.com/library/azure/dn790370.aspx) . Em seguida, você pode publicar o aplicativo usando o cmdlet [Publish-AzureServiceProject](https://msdn.microsoft.com/library/azure/dn495166.aspx) . Para obter informações sobre como entrar, confira [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview).

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações, consulte o [Centro de Desenvolvimento PHP](/develop/php/).

[SDK do Azure para PHP]: /develop/php/common-tasks/download-php-sdk/
[install ps and emulators]: http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[definição do serviço (.csdef) ]: http://msdn.microsoft.com/library/windowsazure/ee758711.aspx
[configuração do serviço (.cscfg)]: http://msdn.microsoft.com/library/windowsazure/ee758710.aspx
[iis.net]: http://www.iis.net/
[sql native client]: http://msdn.microsoft.com/sqlserver/aa937733.aspx
[sqlsrv drivers]: http://php.net/sqlsrv
[instalador sqlncli.msi x64]: http://go.microsoft.com/fwlink/?LinkID=239648
