---
author: v-demjoh
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 05/15/2020
ms.author: v-demjoh
ms.openlocfilehash: ec34ba7b2d3bc026376aeb1cb91847d8eac1cac5
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/07/2021
ms.locfileid: "107073581"
---
## <a name="download-and-install"></a>Fazer o download e instalar

#### <a name="windows-install"></a>[Instalação do Windows](#tab/windowsinstall)

Siga estas etapas para instalar a CLI de Fala no Windows:

1. No Windows, é necessário ter os [Pacotes Redistribuíveis do Microsoft Visual C++ para Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) para a sua plataforma. Ao instalá-los pela primeira vez, pode ser necessária uma reinicialização.
1. Instalar o [SDK do .NET Core 3.1](/dotnet/core/install/windows).
2. Instale a CLI de Fala usando o NuGet por meio deste comando:

   ```console
   dotnet tool install --global Microsoft.CognitiveServices.Speech.CLI
   ```
Digite `spx` para ver a ajuda da CLI de Fala.

> [!NOTE]
> Como alternativa ao NuGet, você pode baixar e extrair o [arquivo .zip](https://aka.ms/speech/spx-zips.zip) da CLI de Fala, localizar e extrair sua plataforma do diretório `spx-zips` e adicionar o caminho `spx` à variável **PATH** do seu sistema.


### <a name="font-limitations"></a>Limitações de fontes

No Windows, a CLI de Fala só pode mostrar as fontes disponíveis no prompt de comando do computador local.
O [Terminal do Windows](https://www.microsoft.com/en-us/p/windows-terminal/9n0dx20hk701) dá suporte a todas as fontes produzidas de maneira interativa pela CLI de Fala.

Se você produzir a saída em um arquivo, um editor de texto como o Bloco de notas ou um navegador da Web como o Microsoft Edge também poderá mostrar todas as fontes.

#### <a name="linux-install"></a>[Instalação do Linux](#tab/linuxinstall)

As seguintes distribuições do Linux têm suporte para arquiteturas x64 usando a CLI de Fala:

* CentOS 7/8
* Debian 9/10 
* Red Hat Enterprise Linux (RHEL) 7/8
* Ubuntu 16.04/18.04/20.04

> [!NOTE]
> Há suporte para arquiteturas adicionais pelo SDK de Fala (não pela CLI de Fala). Para obter mais informações, confira [Sobre o SDK de Fala](../speech-sdk.md).

Siga estas etapas para instalar a CLI de Fala no Linux em uma CPU x64:

1. Instalar o [SDK do .NET Core 3.1](/dotnet/core/install/linux).
2. Instale a CLI de Fala usando o NuGet por meio deste comando:

    `dotnet tool install --global Microsoft.CognitiveServices.Speech.CLI`

Digite `spx` para ver a ajuda da CLI de Fala.

> [!NOTE]
> Como alternativa ao NuGet, você pode baixar os binários no [arquivo .zip](https://aka.ms/speech/spx-zips.zip), extrair `spx-netcore-30-linux-x64.zip` para um novo diretório `~/spx`, digitar `sudo chmod +r+x spx` no binário e adicionar o caminho `~/spx` à variável de sistema PATH.


#### <a name="docker-install-windows-linux-macos"></a>[Instalação do Docker (Windows, Linux, macOS)](#tab/dockerinstall)

Siga estas etapas para instalar a CLI de Fala em um contêiner do Docker:

1. <a href="https://www.docker.com/get-started" target="_blank">Instale o Docker Desktop</a> para sua plataforma, se ainda não estiver instalado.
2. Em um novo prompt de comando ou terminal, digite este comando: 
   ```console   
   docker pull msftspeech/spx
   ```
3. Digite este comando. Você verá informações da Ajuda para a CLI de Fala: 
   ```console 
   docker run -it --rm msftspeech/spx help
   ```

### <a name="mount-a-directory-in-the-container"></a>Montar um diretório no contêiner

A ferramenta CLI de Fala salva as definições de configuração como arquivos e carrega esses arquivos durante a execução de qualquer comando (exceto os comandos da Ajuda).
Ao usar a CLI de Fala em um contêiner do Docker, você precisará montar um diretório local por meio do contêiner, de modo que a ferramenta possa armazenar ou localizar as definições de configuração, e também para que a ferramenta possa ler ou gravar todos os arquivos necessários para o comando, como arquivos de áudio de fala.

No Windows, digite este comando para criar um diretório local que possa ser usado pela CLI de Fala no contêiner:

`mkdir c:\spx-data`

Ou então, no Linux ou no macOS, digite este comando em um terminal para criar um diretório e ver o caminho absoluto dele:

```bash
mkdir ~/spx-data
cd ~/spx-data
pwd
```

Você usará o caminho absoluto ao chamar a CLI de Fala.

### <a name="run-speech-cli-in-the-container"></a>Executar a CLI de Fala no contêiner

Esta documentação mostra o comando `spx` da CLI de Fala usado nas instalações que não são do Docker.
Ao chamar o comando `spx` em um contêiner do Docker, você precisará montar um diretório no contêiner para o sistema de arquivos em que a CLI de Fala possa armazenar e localizar valores de configuração, bem como ler e gravar arquivos.

No Windows, os comandos serão iniciados da seguinte maneira:

```console
docker run -it -v c:\spx-data:/data --rm msftspeech/spx
```

No Linux ou macOS, os comandos se parecerão com o exemplo abaixo. Substitua `ABSOLUTE_PATH` pelo caminho absoluto do diretório montado. Esse caminho foi retornado pelo comando `pwd` na seção anterior. 

Se você executar esse comando antes de definir sua chave e região, receberá uma mensagem de erro informando que você deve definir sua chave e região:
```console   
sudo docker run -it -v ABSOLUTE_PATH:/data --rm msftspeech/spx
```

Para usar o comando `spx` instalado em um contêiner, sempre insira o comando completo mostrado acima, seguido dos parâmetros da solicitação.
Por exemplo, no Windows, este comando define a chave:

```console
docker run -it -v c:\spx-data:/data --rm msftspeech/spx config @key --set SUBSCRIPTION-KEY
```

Para obter uma interação mais estendida com a ferramenta de linha de comando, inicie um contêiner com um shell do Bash interativo adicionando um parâmetro de ponto de entrada.
No Windows, insira este comando para iniciar um contêiner que expõe uma interface de linha de comando interativa, na qual você pode inserir vários comandos `spx`:
```console
docker run -it --entrypoint=/bin/bash -v c:\spx-data:/data --rm msftspeech/spx
```

> [!WARNING]
> Não é possível usar o microfone do computador durante a execução da CLI da Fala em um contêiner do Docker. No entanto, você pode ler e salvar arquivos de áudio em seu diretório montado local. 

<!-- Need to troubleshoot issues with docker pull image

### Optional: Create a command line shortcut

If you're running the the Speech CLI from a Docker container on Linux or macOS you can create a shortcut. 

Follow these instructions to create a shortcut:
1. Open `.bash_profile` with your favorite text editor. For example:
   ```shell
   nano ~/.bash_profile
   ```
2. Next, add this function to your `.bash_profile`. Make sure you update this function with the correct path to your mounted directory:
   ```shell   
   spx(){
       sudo docker run -it -v ABSOLUTE_PATH:/data --rm msftspeech/spx
   }
   ```
3. Source your profile:
   ```shell
   source ~/.bash_profile
   ```
4. Now instead of running `sudo docker run -it -v ABSOLUTE_PATH:/data --rm msftspeech/spx`, you can just type `spx` followed by arguments. For example: 
   ```shell
   // Get some help
   spx help recognize

   // Recognize speech from an audio file 
   spx recognize --file /mounted/directory/file.wav
   ```

> [!WARNING]
> If you change the mounted directory that Docker is referencing, you need to update the function in `.bash_profile`.
--->
***

## <a name="create-subscription-config"></a>Criar configuração da assinatura

Para começar a usar a CLI de Fala, você precisa inserir a chave de assinatura de Fala e o identificador de região. Obtenha essas credenciais seguindo as etapas em [Experimente o serviço de Fala gratuitamente](../overview.md#try-the-speech-service-for-free).
Depois de obter a chave de assinatura e o identificador da região (por exemplo, `eastus`, `westus`), execute os comandos a seguir.

```console
spx config @key --set SUBSCRIPTION-KEY
spx config @region --set REGION
```

Sua autenticação de assinatura agora está armazenada para futuras solicitações de SPX. Se você precisar remover qualquer um desses valores armazenados, execute `spx config @region --clear` ou `spx config @key --clear`.
