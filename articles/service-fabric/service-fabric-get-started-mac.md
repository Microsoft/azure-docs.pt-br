---
title: Configurar seu ambiente de desenvolvimento no macOS
description: Instale o runtime, o SDK e as ferramentas e crie um cluster de desenvolvimento local. Depois de concluir essa configuração, você estará pronto para criar aplicativos no macOS.
ms.topic: conceptual
ms.date: 10/16/2020
ms.custom: devx-track-js
ms.openlocfilehash: d08046c8f29901dd9650a1edc886efa2ff226e00
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93086770"
---
# <a name="set-up-your-development-environment-on-mac-os-x"></a>Configurar seu ambiente de desenvolvimento no Mac OS X
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md)
> * [Linux](service-fabric-get-started-linux.md)
> * [Mac OS X](service-fabric-get-started-mac.md)

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
    Você pode atualizar essas configurações diretamente no arquivo daemon.json em seu caminho de instalação do Docker. Você pode modificar diretamente as definições de configuração do daemon no Docker. Selecione o **ícone do Docker** e então selecione **Preferências** > **Daemon** > **Avançado** .
    
    >[!NOTE]
    >
    >É recomendável modificar o daemon diretamente no Docker porque o local do daemon.jsno arquivo pode variar de uma máquina para uma máquina. Por exemplo, ~/Library/Containers/com.docker.docker/Data/database/com.docker.driver.amd64-linux/etc/docker/daemon.json.
    >

    >[!TIP]
    >Ao testar aplicativos grandes, recomendamos aumentar os recursos alocados para o Docker. Isso pode ser feito selecionando o **ícone do Docker** e depois selecionando **Avançado** para ajustar a quantidade de núcleos e memória.

2. Inicie o cluster.<br/>
    <b>Ubuntu 18, 4 LTS:</b>
    ```bash
    docker run --name sftestcluster -d -v /var/run/docker.sock:/var/run/docker.sock -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 mcr.microsoft.com/service-fabric/onebox:u18
    ```

    <b>Ubuntu 16, 4 LTS:</b>
    ```bash
    docker run --name sftestcluster -d -v /var/run/docker.sock:/var/run/docker.sock -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 mcr.microsoft.com/service-fabric/onebox:u16
    ```

    >[!TIP]
    > Por padrão, isso busca a imagem com a versão mais recente do Service Fabric. Para obter revisões específicas, visite a página [Service Fabric Onebox](https://hub.docker.com/_/microsoft-service-fabric-onebox) no Hub do Docker.



3. Opcional: Crie sua imagem de Service Fabric estendida.

    Em um novo diretório, crie um arquivo chamado `Dockerfile` para criar sua imagem personalizada:

    >[!NOTE]
    >Você pode adaptar a imagem acima com um Dockerfile para adicionar outros programas ou dependências em seu contêiner.
    >Por exemplo, adicionar `RUN apt-get install nodejs -y` permitirá o suporte para aplicativos `nodejs` como executáveis convidados.
    ```Dockerfile
    FROM mcr.microsoft.com/service-fabric/onebox:u18
    RUN apt-get install nodejs -y
    EXPOSE 19080 19000 80 443
    WORKDIR /home/ClusterDeployer
    CMD ["./ClusterDeployer.sh"]
    ```
    
    >[!TIP]
    > Por padrão, isso busca a imagem com a versão mais recente do Service Fabric. Para obter revisões específicas, visite a página do [Hub do Docker](https://hub.docker.com/r/microsoft/service-fabric-onebox/) .

    Para criar sua imagem reutilizável a partir do `Dockerfile` , abra um terminal e `cd` para o que está mantendo diretamente seu e `Dockerfile` , em seguida, execute:

    ```bash 
    docker build -t mysfcluster .
    ```
    
    >[!NOTE]
    >Essa operação levará algum tempo, mas só precisa ser feita uma vez.

    Agora você pode iniciar rapidamente uma cópia local de Service Fabric sempre que precisar dela executando:

    ```bash 
    docker run --name sftestcluster -d -v /var/run/docker.sock:/var/run/docker.sock -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 mysfcluster
    ```

    >[!TIP]
    >Forneça um nome para sua instância de contêiner para que ela possa ser tratada de forma mais legível. 
    >
    >Se o seu aplicativo estiver escutando em determinadas portas, as portas deverão ser especificadas usando marcas `-p` adicionais. Por exemplo, se seu aplicativo estiver escutando na porta 8080, adicione a seguinte marca `-p`:
    >
    >`docker run -itd -p 19000:19000 -p 19080:19080 -p 8080:8080 --name sfonebox mcr.microsoft.com/service-fabric/onebox:u18`
    >

4. O cluster levará algum tempo para iniciar. Quando estiver em execução, você poderá exibir os logs usando o comando a seguir ou ir até o painel para exibir a integridade dos clusters: `http://localhost:19080`

    ```bash 
    docker logs sftestcluster
    ```


5. Para parar e limpar o contêiner, use o comando a seguir. No entanto, vamos usar esse contêiner na próxima etapa.

    ```bash 
    docker rm -f sftestcluster
    ```

### <a name="known-limitations"></a>Limitações conhecidas 
 
 A seguir estão algumas limitações conhecidas do cluster local em execução em um contêiner para Mac’s: 
 
 * O serviço DNS não é executado e atualmente não tem suporte no contêiner. [Problema #132](https://github.com/Microsoft/service-fabric/issues/132)
 * A execução de aplicativos baseados em contêiner requer a execução do it em um host Linux. Atualmente, não há suporte para aplicativos de contêiner aninhados.

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

    > [!IMPORTANT]
    > As versões atuais do `brew cask install java` podem instalar uma versão mais recente do JDK.
    > Certifique-se de instalar o JDK 8.

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

## <a name="set-up-net-core-31-development"></a>Configurar o desenvolvimento do .NET Core 3,1

Instale o [SDK do .NET Core 3,1 para Mac](https://www.microsoft.com/net/core#macos) para começar a [criar aplicativos Service Fabric em C#](service-fabric-create-your-first-linux-application-with-csharp.md). Os pacotes para aplicativos do .NET Core Service Fabric são hospedados em NuGet.org.

## <a name="install-the-service-fabric-plug-in-for-eclipse-on-your-mac"></a>Instalar o plug-in do Service Fabric para Eclipse no Mac

O Azure Service Fabric fornece um plug-in do Eclipse Neon (ou posterior) para o IDE do Java. O plug-in simplifica o processo de criação, compilação e implantação de serviços Java. Para instalar ou atualizar o plug-in do Service Fabric para Eclipse para a versão mais recente, execute [estas etapas](service-fabric-get-started-eclipse.md#install-or-update-the-service-fabric-plug-in-in-eclipse). As outras etapas na [documentação do Service Fabric para Eclipse](service-fabric-get-started-eclipse.md) também são aplicáveis: criar um aplicativo, adicionar um serviço a um aplicativo, desinstalar um aplicativo e assim por diante.

A última etapa é instanciar o contêiner com um caminho que é compartilhado com o seu host. O plug-in requer que esse tipo de instanciação trabalhe com o contêiner do Docker em seu Mac. Por exemplo: 

```bash
docker run -itd -p 19080:19080 -v /Users/sayantan/work/workspaces/mySFWorkspace:/tmp/mySFWorkspace --name sfonebox mcr.microsoft.com/service-fabric/onebox:latest
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
