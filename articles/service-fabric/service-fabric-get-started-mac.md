---
title: Configurar o ambiente de desenvolvimento no Mac OS X para funcionar com o Azure Service Fabric | Microsoft Docs
description: Instale o tempo de execução, o SDK e as ferramentas e crie um cluster de desenvolvimento local. Depois de concluir a instalação, você estará pronto para criar aplicativos no Mac OS X.
services: service-fabric
documentationcenter: linux
author: suhuruli
manager: chackdan
editor: ''
ms.assetid: bf84458f-4b87-4de1-9844-19909e368deb
ms.service: service-fabric
ms.devlang: linux
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/17/2017
ms.author: suhuruli
ms.openlocfilehash: 84d1f52b5fb8f18d3578bad28930f74534b1409f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60947563"
---
# <a name="set-up-your-development-environment-on-mac-os-x"></a>Configurar seu ambiente de desenvolvimento no Mac OS X
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md)
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
>
>  

Você pode criar aplicativos do Azure Service Fabric para serem executados nos clusters do Linux usando o Mac OS X. Este documento aborda como configurar seu Mac para o desenvolvimento.

## <a name="prerequisites"></a>Pré-requisitos
O Azure Service Fabric não é executado nativamente no Mac OS X. Para executar um cluster do Service Fabric local, é fornecida uma imagem de contêiner pré-configurada do Docker. Antes de começar, você precisa do:

* Pelo menos 4 GB de RAM.
* A versão mais recente do [Docker](https://www.docker.com/).

>[!TIP]
>
>Para instalar o Docker no seu Mac, siga as etapas da [documentação do Docker](https://docs.docker.com/docker-for-mac/install/#what-to-know-before-you-install). Depois de instalar, [verifique sua instalação](https://docs.docker.com/docker-for-mac/#check-versions-of-docker-engine-compose-and-machine).
>

## <a name="create-a-local-container-and-set-up-service-fabric"></a>Criar um contêiner local e configuração do Service Fabric
Para configurar um contêiner de Docker local e ter um cluster do Service Fabric em execução, execute as seguintes etapas:

1. Atualize a configuração do daemon de Docker em seu host com as configurações a seguir e reinicie o daemon de Docker: 

    ```json
    {
        "ipv6": true,
        "fixed-cidr-v6": "fd00::/64"
    }
    ```
    Você pode atualizar essas configurações diretamente no arquivo daemon.json em seu caminho de instalação do Docker. Você pode modificar diretamente as definições de configuração do daemon no Docker. Selecione o **ícone do Docker** e então selecione **Preferências** > **Daemon** > **Avançado**.
    
    >[!NOTE]
    >
    >É recomendado modificar o daemon diretamente no Docker porque o local do arquivo daemon.json pode variar de máquina para máquina. Por exemplo, ~/Library/Containers/com.docker.docker/Data/database/com.docker.driver.amd64-linux/etc/docker/daemon.json.
    >

    >[!TIP]
    >Ao testar aplicativos grandes, recomendamos aumentar os recursos alocados para o Docker. Isso pode ser feito selecionando o **ícone do Docker** e depois selecionando **Avançado** para ajustar a quantidade de núcleos e memória.

2. Em um novo diretório, crie um arquivo chamado `Dockerfile` para criar sua imagem do Service Fabric:

    ```Dockerfile
    FROM microsoft/service-fabric-onebox
    WORKDIR /home/ClusterDeployer
    RUN ./setup.sh
    #Generate the local
    RUN locale-gen en_US.UTF-8
    #Set environment variables
    ENV LANG=en_US.UTF-8
    ENV LANGUAGE=en_US:en
    ENV LC_ALL=en_US.UTF-8
    EXPOSE 19080 19000 80 443
    #Start SSH before running the cluster
    CMD /etc/init.d/ssh start && ./run.sh
    ```

    >[!NOTE]
    >É possível adaptar esse arquivo para adicionar outros programas ou dependências em seu contêiner.
    >Por exemplo, adicionar `RUN apt-get install nodejs -y` permitirá o suporte para aplicativos `nodejs` como executáveis convidados.
    
    >[!TIP]
    > Por padrão, isso busca a imagem com a versão mais recente do Service Fabric. Para análises específicas, visite a página [Hub do Docker](https://hub.docker.com/r/microsoft/service-fabric-onebox/)

3. Para criar sua imagem reutilizável a partir de `Dockerfile`, abra um terminal e `cd` para o que está diretamente mantendo seu `Dockerfile`, depois execute:

    ```bash 
    docker build -t mysfcluster .
    ```
    
    >[!NOTE]
    >Essa operação levará algum tempo, mas só precisa ser feita uma vez.

4. Agora é possível iniciar rapidamente uma cópia local do Service Fabric sempre que for necessário, executando:

    ```bash 
    docker run --name sftestcluster -d -v /var/run/docker.sock:/var/run/docker.sock -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 mysfcluster
    ```

    >[!TIP]
    >Forneça um nome para sua instância de contêiner para que ela possa ser tratada de forma mais legível. 
    >
    >Se o seu aplicativo estiver escutando em determinadas portas, as portas deverão ser especificadas usando marcas `-p` adicionais. Por exemplo, se seu aplicativo estiver escutando na porta 8080, adicione a seguinte marca `-p`:
    >
    >`docker run -itd -p 19080:19080 -p 8080:8080 --name sfonebox microsoft/service-fabric-onebox`
    >

5. O cluster levará algum tempo para iniciar. Quando estiver sendo executado, será possível exibir logs usando o comando a seguir ou ir até o painel para exibir a integridade dos clusters [http://localhost:19080](http://localhost:19080):

    ```bash 
    docker logs sftestcluster
    ```



6. Para interromper e limpar o contêiner, use o comando a seguir. No entanto, vamos usar esse contêiner na próxima etapa.

    ```bash 
    docker rm -f sftestcluster
    ```

### <a name="known-limitations"></a>Limitações conhecidas 
 
 A seguir estão algumas limitações conhecidas do cluster local em execução em um contêiner para Mac’s: 
 
 * O serviço DNS não é executado e não há suporte para [Problema 132](https://github.com/Microsoft/service-fabric/issues/132)

## <a name="set-up-the-service-fabric-cli-sfctl-on-your-mac"></a>Configurar a CLI do Service Fabric (sfctl) no seu Mac

Siga as instruções em [CLI do Service Fabric](service-fabric-cli.md#cli-mac) para instalar a CLI do Service Fabric (`sfctl`) no seu Mac.
Os comandos da CLI dão suporte à interação com entidades do Service Fabric, incluindo clusters, aplicativos e serviços.

1. Para conectar-se ao cluster antes de implantar aplicativos, execute o comando a seguir. 

```bash
sfctl cluster select --endpoint http://localhost:19080
```

## <a name="create-your-application-on-your-mac-by-using-yeoman"></a>Criar seu aplicativo no Mac usando Yeoman

O Service Fabric fornece ferramentas de scaffolding que ajudarão a criar um aplicativo do Service Fabric no terminal usando gerador de modelos Yeoman. Use as etapas a seguir para garantir que o gerador de modelos Yeoman do Service Fabric esteja funcionando em seu computador:

1. O Node.js e o Gerenciador de Pacotes do Nó (NPM) devem estar instalados no seu Mac. O software pode ser instalado usando [HomeBrew](https://brew.sh/), da seguinte maneira:

    ```bash
    brew install node
    node -v
    npm -v
    ```
2. Instale o gerador de modelos [Yeoman](https://yeoman.io/) em seu computador com base no NPM:

    ```bash
    npm install -g yo
    ```
3. Instale o gerador do Yeoman de sua preferência ao executar as etapas na [documentação](service-fabric-get-started-linux.md#set-up-yeoman-generators-for-containers-and-guest-executables) do guia de introdução. Para criar apps do Service Fabric usando o Yeoman, execute as etapas:

    ```bash
    npm install -g generator-azuresfjava       # for Service Fabric Java Applications
    npm install -g generator-azuresfguest      # for Service Fabric Guest executables
    npm install -g generator-azuresfcontainer  # for Service Fabric Container Applications
    ```
4. Após a instalação dos geradores, crie os serviços do contêiner ou do executável do convidado executando `yo azuresfguest` ou `yo azuresfcontainer` respectivamente.

5. Para criar um aplicativo Java do Service Fabric no seu Mac, o JDK versão 1.8 e o Gradle devem ser instalados no computador host. O software pode ser instalado usando [HomeBrew](https://brew.sh/), da seguinte maneira: 

    ```bash
    brew update
    brew cask install java
    brew install gradle
    ```

    >[!TIP]
    > Lembre-se de verificar se você tem a versão correta do JDK instalada. 

## <a name="deploy-your-application-on-your-mac-from-the-terminal"></a>Implantar seu aplicativo no Mac desde o terminal

Depois de criar e compilar o aplicativo do Service Fabric, você pode implantar seu aplicativo usando a [CLI do Service Fabric](service-fabric-cli.md#cli-mac):

1. Conecte-se ao cluster do Service Fabric em execução na instância de contêiner no seu Mac:

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```

2. Do diretório do projeto, execute o script de instalação:

    ```bash
    cd MyProject
    bash install.sh
    ```

## <a name="set-up-net-core-20-development"></a>Configurar o desenvolvimento do .NET Core 2.0

Instale o [SDK do .NET Core 2.0 para Mac](https://www.microsoft.com/net/core#macos) para iniciar a [criação de aplicativos do Service Fabric em C#](service-fabric-create-your-first-linux-application-with-csharp.md). Os pacotes de aplicativos do .NET Core 2.0 Service Fabric estão hospedados em NuGet.org, atualmente em versão prévia.

## <a name="install-the-service-fabric-plug-in-for-eclipse-on-your-mac"></a>Instalar o plug-in do Service Fabric para Eclipse no Mac

O Azure Service Fabric fornece um plug-in do Eclipse Neon (ou posterior) para o IDE do Java. O plug-in simplifica o processo de criação, compilação e implantação de serviços Java. Para instalar ou atualizar o plug-in do Service Fabric para Eclipse para a versão mais recente, execute [estas etapas](service-fabric-get-started-eclipse.md#install-or-update-the-service-fabric-plug-in-in-eclipse). As outras etapas na [documentação do Service Fabric para Eclipse](service-fabric-get-started-eclipse.md) também são aplicáveis: criar um aplicativo, adicionar um serviço a um aplicativo, desinstalar um aplicativo e assim por diante.

A última etapa é instanciar o contêiner com um caminho que é compartilhado com o seu host. O plug-in requer que esse tipo de instanciação trabalhe com o contêiner do Docker em seu Mac. Por exemplo:

```bash
docker run -itd -p 19080:19080 -v /Users/sayantan/work/workspaces/mySFWorkspace:/tmp/mySFWorkspace --name sfonebox microsoft/service-fabric-onebox
```

Os atributos são definidos da seguinte maneira:
* `/Users/sayantan/work/workspaces/mySFWorkspace` é o caminho totalmente qualificado do workspace no seu Mac.
* `/tmp/mySFWorkspace` é o caminho no interior do contêiner para onde o workspace deve ser mapeado.

>[!NOTE]
> 
>Se você tiver um caminho/nome diferente para seu workspace, atualize esses valores no comando `docker run`.
> 
>Se você iniciar o contêiner com um nome diferente de `sfonebox`, atualize-o no arquivo testclient.sh em seu aplicativo Java do ator do Service Fabric.
>

## <a name="next-steps"></a>Próximas etapas
<!-- Links -->
* [Criar e implantar seu primeiro aplicativo Java de do Service Fabric no Linux usando Yeoman](service-fabric-create-your-first-linux-application-with-java.md)
* [Criar e implantar seu primeiro aplicativo Java do Service Fabric no Linux usando o plug-in do Service Fabric para o Eclipse](service-fabric-get-started-eclipse.md)
* [Criar um cluster do Service Fabric no portal do Azure](service-fabric-cluster-creation-via-portal.md)
* [Criar um cluster do Service Fabric usando o Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
* [Entender o modelo de aplicativo do Service Fabric](service-fabric-application-model.md)
* [Usar a CLI do Service Fabric para gerenciar seus aplicativos](service-fabric-application-lifecycle-sfctl.md)
* [Preparar um ambiente de desenvolvimento do Linux no Windows](service-fabric-local-linux-cluster-windows.md)

<!-- Images -->
[cluster-setup-script]: ./media/service-fabric-get-started-mac/cluster-setup-mac.png
[sfx-mac]: ./media/service-fabric-get-started-mac/sfx-mac.png
[sf-eclipse-plugin-install]: ./media/service-fabric-get-started-mac/sf-eclipse-plugin-install.png
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship
