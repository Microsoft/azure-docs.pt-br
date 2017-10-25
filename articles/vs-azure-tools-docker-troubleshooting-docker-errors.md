---
title: "Solução de erros do cliente Docker no Windows usando o Visual Studio | Microsoft Docs"
description: "Solucione os problemas encontrados na utilização do Visual Studio para criar e implantar aplicativos Web no Docker ou no Windows usando o Visual Studio."
services: azure-container-service
documentationcenter: na
author: mlearned
manager: douge
editor: 
ms.assetid: 346f70b9-7b52-4688-a8e8-8f53869618d3
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 06/08/2016
ms.author: mlearned
ms.openlocfilehash: 89fa04a1107b6abb49aefd68066443717ac9b731
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-visual-studio-docker-development"></a>Solucionar problemas de desenvolvimento do Docker no Visual Studio

Quando estiver trabalhando com o Preview das Ferramentas do Visual Studio para Docker, você pode encontrar alguns problemas devido à natureza da visualização.
Veja a seguir alguns problemas comuns e suas resoluções.  

## <a name="visual-studio-2017-rc"></a>Visual Studio 2017 RC

### <a name="linux-containers"></a>**Contêineres do Linux**

####  <a name="build-errors-occur-when-debugging-a-net-core-web-or-console-application"></a>Os erros de compilação ocorrem durante a depuração de um aplicativo Web ou de um console do .NET Core  

Isso pode estar relacionado ao não compartilhamento da unidade na qual reside o projeto com o Docker para Windows.  Você poderá receber um erro como o seguinte:

```
The "PrepareForLaunch" task failed unexpectedly.
Microsoft.DotNet.Docker.CommandLineClientException: Creating network "webapplication13628050196_default" with the default driver
Building webapplication1
Creating webapplication13628050196_webapplication1_1
ERROR: for webapplication1  Cannot create container for service webapplication1: C: drive is not shared. Please share it in Docker for Windows Settings
```
Para resolver esse problema:

1. Clique com o botão direito do mouse em **Docker para Windows** na área de notificação e selecione **Configurações**.  
2. Escolha **Unidades Compartilhadas** e compartilhe a unidade em que está o projeto.

### <a name="windows-containers"></a>**Contêineres do Windows**

Os problemas a seguir são específicos à depuração de aplicativos de console e Web do .NET Framework em contêineres do Windows.

#### <a name="prerequisites"></a>Pré-requisitos

1. O Visual Studio 2017 RC (ou posterior) com o .NET Core e a carga de trabalho do Docker (Preview) devem estar instalados.
2. Atualização de Aniversário do Windows 10 com os últimos patches do Windows Update. Especificamente, o [KB3194798](https://support.microsoft.com/en-us/help/3194798/cumulative-update-for-windows-10-version-1607-and-windows-server-2016-october-11,-2016) deve ser instalado. 
3. O [Docker para Windows](https://docs.docker.com/docker-for-windows/) (compilação 1.13.0 ou posterior) deve ser instalado.
4. **Alternar para contêineres do Windows** deve ser selecionada. Na área de notificação, clique em **Docker para Windows** e selecione **Alternar para contêineres do Windows**. Após a reinicialização do computador, garanta que essa configuração seja mantida.

#### <a name="console-output-does-not-appear-in-visual-studios-output-window-while-debugging-a-console-application"></a>A saída do console não é exibida na janela de saída do Visual Studio durante a depuração de um aplicativo de console

Esse é um problema conhecido no Depurador do Visual Studio (msvsmon.exe) que, no momento, não é projetado para esse cenário. Suporte para esse cenário pode ser incluído em uma versão futura. Para ver a saída por meio do aplicativo de console no Visual Studio, use **Docker: Iniciar Projeto**, que é equivalente a **Iniciar sem Depuração**.

#### <a name="debugging-web-applications-with-the-release-configuration-fails-with-403-forbidden-error"></a>A depuração de aplicativos Web com a configuração de versão falha com o erro (403) Proibido

Para resolver o problema, abra o arquivo web.release.config na solução e comente ou exclua as seguintes linhas:

```
<compilation xdt:Transform="RemoveAttributes(debug)" />
```

## <a name="visual-studio-2015"></a>Visual Studio 2015

### <a name="linux-containers"></a>**Contêineres do Linux**

#### <a name="unable-to-validate-volume-mapping"></a>Não é possível validar o mapeamento de volume
O mapeamento de volume é necessário para compartilhar o código-fonte e os binários do seu aplicativo com a pasta de aplicativo no contêiner.  Os mapeamentos de volume específico estão contidos em docker-compose.dev.debug.yml e docker-compose.dev.release.yml. Como os arquivos são alterados em seu computador host, os contêineres refletem essas alterações em uma estrutura de pastas semelhante.

Para habilitar o mapeamento do volume:

1. Clique em **Moby** na área de notificação e selecione **configurações**.
2. Selecione **Unidades Compartilhadas**.
3. Selecione a unidade que hospeda o projeto e a unidade na qual %USERPROFILE% reside.
4. Clique em **Aplicar**.

Para testar se o mapeamento do volume está funcionando, recrie e selecione F5 no Visual Studio depois que uma ou mais unidades tiverem sido compartilhadas ou execute o código a seguir em um prompt de comando.

> [!NOTE]
> Este exemplo pressupõe que a pasta Usuários esteja localizada na unidade C e que ela foi compartilhada.
> Examine, conforme necessário, se você compartilhou outra unidade.

```
docker run -it -v /c/Users/Public:/wormhole busybox
```

Execute o código a seguir no contêiner do Linux.

```
/ # ls
```

Você deverá ver uma listagem de diretórios da pasta Usuários/Público. Se nenhum arquivo for exibido e sua pasta /c/Usuários/Público não estiver vazia, o mapeamento de volume não estará configurado corretamente.

```
bin       etc       proc      sys       usr       wormhole
dev       home      root      tmp       var
```

Mude para o diretório wormhole para ver o conteúdo do diretório `/c/Users/Public`:

```
/ # cd wormhole/
/wormhole # ls
AccountPictures  Downloads        Music            Videos
Desktop          Host             NuGet.Config     desktop.ini
Documents        Libraries        Pictures
/wormhole #
```

> [!NOTE]
> Quando estiver trabalhando com VMs Linux, o sistema de arquivos do contêiner diferenciará maiúsculas de minúsculas.

## <a name="build-prepareforbuild-task-failed-unexpectedly"></a>Compilação: a tarefa "PrepareForBuild" falhou inesperadamente

Microsoft.DotNet.Docker.CommandLine.ClientException: erro ao tentar se conectar.

Verifique se o host do Docker padrão está em execução. Abra um prompt de comando e execute:

```
docker info
```

Se isso retornar um erro, tente iniciar o aplicativo da área de trabalho **Docker para Windows**. Se o aplicativo da área de trabalho estiver em execução, o **Moby** deverá estar visível na área de notificação. Clique com botão direito em **Moby** e abra **configurações**. Clique em **Redefinir** e reinicie o Docker.

## <a name="an-error-dialog-occurs-when-attempting-to-add-docker-support-or-debug-f5-an-aspnet-core-application-in-a-container"></a>Uma caixa de diálogo de erro ocorre na tentativa de adicionar Suporte ao Docker ou depurar (F5) um aplicativo do ASP.NET Core em um contêiner

Depois de desinstalar e instalar extensões, o cache MEF (Managed Extensibility Framework) no Visual Studio pode se corromper. Quando isso ocorre, ele pode causar várias mensagens de erro quando você estiver adicionando Suporte ao Docker e/ou tentando executar ou depurar (F5) seu aplicativo do ASP.NET Core. Como solução alternativa, use as etapas a seguir para excluir e gerar novamente o cache MEF.

1. Feche todas as instâncias do Visual Studio.
1. Abra %USERPROFILE%\AppData\Local\Microsoft\VisualStudio\14.0\.
1. Exclua as seguintes pastas:
     ```
       ComponentModelCache
       Extensions
       MEFCacheBackup
    ```
1. Abra o Visual Studio.
1. Tente o cenário novamente.
