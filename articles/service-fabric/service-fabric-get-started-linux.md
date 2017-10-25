---
title: Configurar o ambiente de desenvolvimento no Linux | Microsoft Docs
description: "Instale o tempo de execução, o SDK e crie um cluster de desenvolvimento local no Linux. Depois de concluir a instalação, você estará pronto para criar aplicativos."
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: d552c8cd-67d1-45e8-91dc-871853f44fc6
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 9/19/2017
ms.author: subramar
ms.openlocfilehash: da9aff17c16e179be200677bfbfd1287fff269e3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="prepare-your-development-environment-on-linux"></a>Preparar seu ambiente de desenvolvimento no Linux
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md)
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
>
>  

Para implantar e executar os [aplicativos do Azure Service Fabric](service-fabric-application-model.md) em seu computador de desenvolvimento Linux, instale o tempo de execução e o SDK comum. Você também pode instalar os SDKs opcionais para desenvolvimento em Java e .NET Core.

## <a name="prerequisites"></a>Pré-requisitos

Há suporte de desenvolvimento para as seguintes versões de sistema operacional:

* Ubuntu 16.04 (`Xenial Xerus`)

## <a name="installation-methods"></a>Métodos de Instalação

### <a name="1-script-installation"></a>1. Instalação de script

Um script é fornecido por questões de conveniência para instalar o tempo de execução do Service Fabric e o SDK do Service Fabric comum juntamente com a CLI **sfctl**. Execute as etapas de instalação manual na próxima seção para determinar o que está sendo instalado e as licenças que estão sendo concordadas. A execução do script supõe que você concorda com as licenças para todos os softwares que estão sendo instalados. 

Depois que o script é executado com êxito, você poderá pular diretamente para [Configurar um cluster local](#set-up-a-local-cluster).

```bash
sudo curl -s https://raw.githubusercontent.com/Azure/service-fabric-scripts-and-templates/master/scripts/SetupServiceFabric/SetupServiceFabric.sh | sudo bash
```

### <a name="2-manual-installation"></a>2. Instalação manual
Para a instalação manual de tempo de execução do Service Fabric e SDK comum, siga o restante deste guia.

## <a name="update-your-apt-sources"></a>Atualizar suas fontes APT
Para instalar o SDK e o pacote de execução associado via ferramenta da linha de comando apt-get, primeiro atualize as fontes APT (Advanced Packaging Tool).

1. Abra um terminal.
2. Adicione o repositório do Service Fabric à sua lista de fontes.

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] http://apt-mo.trafficmanager.net/repos/servicefabric/ xenial main" > /etc/apt/sources.list.d/servicefabric.list'
    ```

3. Adicionar o repositório `dotnet` à sua lista de fontes.

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/dotnet-release/ xenial main" > /etc/apt/sources.list.d/dotnetdev.list'
    ```

4. Adicione a nova chave Gnu Privacy Guard (GnuPG ou GPG) ao arquivo de chaves APT.

    ```bash
    sudo apt-key adv --keyserver apt-mo.trafficmanager.net --recv-keys 417A0893
    sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 417A0893
    ```

5. Adicione a chave GPG oficial do Docker ao arquivo de chaves APT.

    ```bash
    sudo apt-get install curl
    sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
    ```

6. Configure o repositório do Docker.

    ```bash
    sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
    ```

7. Atualize suas listas de pacote com base nos repositórios recém-adicionados.

    ```bash
    sudo apt-get update
    ```

## <a name="install-and-set-up-the-service-fabric-sdk-for-local-cluster-setup"></a>Instalar e configurar o SDK do Service Fabric para a configuração do cluster local

Após a atualização das fontes, você poderá instalar o SDK. Instale o pacote do SDK do Service Fabric, confirme a instalação e aceite o contrato de licença.

```bash
sudo apt-get install servicefabricsdkcommon
```

>   [!TIP]
>   Os comandos a seguir automatizam a aceitação da licença para pacotes do Service Fabric:
>   ```bash
>   echo "servicefabric servicefabric/accepted-eula-ga select true" | sudo debconf-set-selections
>   echo "servicefabricsdkcommon servicefabricsdkcommon/accepted-eula-ga select true" | sudo debconf-set-selections
>   ```

## <a name="set-up-a-local-cluster"></a>Configurar um cluster local
  Quando a instalação for bem-sucedida, você poderá iniciar um cluster local.

  1. Execute o script de configuração do cluster.

      ```bash
      sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
      ```

  2. Abra um navegador da web e vá para o [Service Fabric Explorer](http://localhost:19080/Explorer). Se o cluster tiver sido iniciado, você deverá ver o painel do Service Fabric Explorer.

      ![Service Fabric Explorer no Linux][sfx-linux]

  Neste ponto, você é capaz de implantar os pacotes do aplicativo Service Fabric predefinidos ou novos com base nos contêineres ou executáveis do convidado. Para compilar novos serviços usando os SDKs do Java ou .NET Core, siga as etapas de configuração opcionais fornecidas nas seções posteriores.


  > [!NOTE]
  > Não há suporte para os clusters autônomos no Linux.
  >

## <a name="set-up-the-service-fabric-cli"></a>Configurar a CLI do Service Fabric

A [CLI do Service Fabric](service-fabric-cli.md) tem comandos para interagir com as entidades do Service Fabric, inclusive clusters e aplicativos.
Siga as instruções em [CLI do Service Fabric](service-fabric-cli.md) para instalar a CLI.


## <a name="set-up-yeoman-generators-for-containers-and-guest-executables"></a>Configurar os geradores do Yeoman para os contêineres e executáveis do convidado
O Service Fabric fornece ferramentas de scaffolding que ajudarão a criar um aplicativo do Service Fabric em um terminal usando geradores de modelos Yeoman. Siga estas etapas para configurar os geradores de modelo do Yeoman do Service Fabric:

1. Instalar o nodejs e o NPM em seu computador

  ```bash
  sudo apt-get install npm
  sudo apt install nodejs-legacy
  ```
2. Instalar o gerador de modelos [Yeoman](http://yeoman.io/) em seu computador a partir do NPM

  ```bash
  sudo npm install -g yo
  ```
3. Instalar o gerador de contêineres Yeo do Service Fabric e o gerador de executáveis do convidado a partir do NPM

  ```bash
  sudo npm install -g generator-azuresfcontainer  # for Service Fabric container application
  sudo npm install -g generator-azuresfguest      # for Service Fabric guest executable application
  ```

Depois de instalar os geradores, você deverá conseguir criar os serviços do contêiner ou do executável do convidado executando `yo azuresfguest` ou `yo azuresfcontainer` respectivamente.

## <a name="set-up-net-core-20-development"></a>Configurar o desenvolvimento do .NET Core 2.0

Instale o [SDK do .NET Core 2.0 para Ubuntu](https://www.microsoft.com/net/core#linuxubuntu) para iniciar a [criação de aplicativos do Service Fabric em C#](service-fabric-create-your-first-linux-application-with-csharp.md). Os pacotes de aplicativos do .NET Core 2.0 Service Fabric estão hospedados em NuGet.org, atualmente em versão prévia.

## <a name="set-up-java-development"></a>Configurar o desenvolvimento de Java

Para criar serviços do Service Fabric usando o Java, instale o JDK 1.8 e Gradle para executar tarefas de compilação. O trecho a seguir instala o Open JDK 1.8 junto com o Gradle. As bibliotecas Java do Service Fabric são extraídas do Maven.

  ```bash
  sudo apt-get install openjdk-8-jdk-headless
  sudo apt-get install gradle
  ```

## <a name="install-the-eclipse-neon-plug-in-optional"></a>Instalar o plug-in do Eclipse Neon (opcional)

Você pode instalar o plug-in Eclipse do Service Fabric de dentro do IDE do Eclipse para Desenvolvedores Java. Você pode usar o Eclipse para criar aplicativos executáveis de convidado do Service Fabric e aplicativos de contêiner além dos aplicativos Java do Service Fabric.

1. No Eclipse, verifique se você tem o Eclipse Neon mais recente e a versão mais recente do Buildship (1.0.17 ou posterior) instalados. Você pode verificar as versões dos componentes instalados selecionando **Ajuda** > **Detalhes da Instalação**. Você pode atualizar Buildship usando as instruções em [Buildship do Eclipse: plug-ins do Eclipse para Gradle][buildship-update].

2. Para instalar o plug-in Service Fabric, selecione**Ajuda** > **Instalar Novo Software**.

3. Na caixa **Trabalhar com**, digite **http://dl.microsoft.com/eclipse**.

4. Clique em **Adicionar**.

    ![A página Software Disponível][sf-eclipse-plugin]

5. Selecione o plug-in **ServiceFabric** e clique em **Avançar**.

6. Conclua as etapas de instalação e aceite o contrato de licença do usuário final.

Se você já tiver o plug-in Service Fabric instalado, verifique se tem a versão mais recente. Você pode verificar selecionando **Ajuda** > **Detalhes da Instalação** e pesquisando Service Fabric na lista de plug-ins instalados. Se uma versão mais recente estiver disponível, selecione **Atualizar**.

Para obter mais informações, consulte [Plug-in Service Fabric para o desenvolvimento de aplicativos Java do Eclipse](service-fabric-get-started-eclipse.md).

## <a name="update-the-sdk-and-runtime"></a>Atualizar SDK e execução

Para atualizar para a versão mais recente do SDK e tempo de execução, execute os seguintes comandos :

```bash
sudo apt-get update
sudo apt-get install servicefabric servicefabricsdkcommon
```
Para atualizar os binários SDK do Java a partir do Maven, você precisa atualizar os detalhes da versão do binário correspondente no arquivo ``build.gradle`` para apontar para a versão mais recente. Para saber exatamente onde você precisa atualizar a versão, poderá referenciar qualquer arquivo ``build.gradle`` nos exemplos de introdução do Service Fabric [aqui](https://github.com/Azure-Samples/service-fabric-java-getting-started).

> [!NOTE]
> Atualizar os pacotes pode causar a interrupção do seu cluster de desenvolvimento local. Reinicie o cluster local após uma atualização seguindo as instruções nesta página.

## <a name="remove-the-sdk"></a>Remover o SDK
Para remover os SDKs do Service Fabric, execute o seguinte:

```bash
sudo apt-get remove servicefabric servicefabicsdkcommon
sudo npm uninstall generator-azuresfcontainer
sudo npm uninstall generator-azuresfguest
sudo apt-get install -f
```

## <a name="next-steps"></a>Próximas etapas

* [Criar e implantar seu primeiro aplicativo Java do Service Fabric no Linux usando o Yeoman](service-fabric-create-your-first-linux-application-with-java.md)
* [Criar e implantar seu primeiro aplicativo Java do Service Fabric no Linux usando o plug-in Service Fabric para o Eclipse](service-fabric-get-started-eclipse.md)
* [Criar seu primeiro aplicativo do CSharp no Linux](service-fabric-create-your-first-linux-application-with-csharp.md)
* [Preparar seu ambiente de desenvolvimento no OSX](service-fabric-get-started-mac.md)
* [Usar a CLI do Service Fabric para gerenciar seus aplicativos](service-fabric-application-lifecycle-sfctl.md)
* [Diferenças Windows/Linux do Service Fabric](service-fabric-linux-windows-differences.md)
* [Introdução à CLI do Service Fabric](service-fabric-cli.md)

<!-- Links -->

[azure-xplat-cli-github]: https://github.com/Azure/azure-xplat-cli
[install-node]: https://nodejs.org/en/download/package-manager/#installing-node-js-via-package-manager
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship

<!--Images -->

[sf-eclipse-plugin]: ./media/service-fabric-get-started-linux/service-fabric-eclipse-plugin.png
[sfx-linux]: ./media/service-fabric-get-started-linux/sfx-linux.png
