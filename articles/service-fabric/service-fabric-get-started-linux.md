---
title: Configurar seu ambiente de desenvolvimento no Linux
description: Instale o runtime, o SDK e crie um cluster de desenvolvimento local no Linux. Depois de concluir a instalação, você estará pronto para criar aplicativos.
ms.topic: conceptual
ms.date: 10/16/2020
ms.custom: devx-track-js
ms.openlocfilehash: 14b8a278605a908b4182c724831b2e42de54a753
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93086883"
---
# <a name="prepare-your-development-environment-on-linux"></a>Preparar seu ambiente de desenvolvimento no Linux
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md)
> * [Linux](service-fabric-get-started-linux.md)
> * [Mac OS X](service-fabric-get-started-mac.md)

Para implantar e executar os [aplicativos do Azure Service Fabric](service-fabric-application-model.md) em seu computador de desenvolvimento Linux, instale o runtime e o SDK comum. Você também pode instalar os SDKs opcionais para desenvolvimento em Java e .NET Core. 

As etapas neste artigo pressupõem que você instale nativamente no Linux ou use a [imagem de contêiner Service Fabric Onebox](https://hub.docker.com/_/microsoft-service-fabric-onebox), ou seja, `mcr.microsoft.com/service-fabric/onebox:u18` .

Você pode gerenciar Service Fabric entidades hospedadas na nuvem ou localmente com a CLI (interface de linha de comando) do Azure Service Fabric. Para obter informações sobre como instalar a CLI, consulte [Configurar a CLI do Service Fabric](./service-fabric-cli.md).


## <a name="prerequisites"></a>Pré-requisitos

Há suporte de desenvolvimento para estas versões de sistema operacional.

* Ubuntu 16, 4 ( `Xenial Xerus` ), 18, 4 ( `Bionic Beaver` )

    Verifique se o pacote `apt-transport-https` está instalado.
         
    ```bash
    sudo apt-get install apt-transport-https
    ```
* Red Hat Enterprise Linux 7.4 (suporte à versão prévia do Service Fabric)


## <a name="installation-methods"></a>Métodos de instalação

<!-- markdownlint-disable MD025 -->
<!-- markdownlint-disable MD024 -->

# <a name="ubuntu"></a>[Ubuntu](#tab/sdksetupubuntu)

## <a name="update-your-apt-sources"></a>Atualizar suas fontes APT
Para instalar o SDK e o pacote de execução associado via ferramenta da linha de comando apt-get, primeiro atualize as fontes APT (Advanced Packaging Tool).

## <a name="script-installation"></a>Instalação de script

Para sua conveniência, um script é fornecido para instalar o Service Fabric Runtime e o Service Fabric SDK comum, juntamente com a CLI do [ **sfctl**](service-fabric-cli.md). A execução do script supõe que você concorda com as licenças para todos os softwares que estão sendo instalados. Como alternativa, você pode executar as etapas de [instalação manual](#manual-installation) na próxima seção, que apresentará licenças associadas, bem como os componentes que estão sendo instalados.

Depois que o script for executado com êxito, é possível pular para [Configurar um cluster local](#set-up-a-local-cluster).

```bash
sudo curl -s https://raw.githubusercontent.com/Azure/service-fabric-scripts-and-templates/master/scripts/SetupServiceFabric/SetupServiceFabric.sh | sudo bash
```

## <a name="manual-installation"></a>Instalação manual
Para a instalação manual do runtime do Service Fabric e SDK comum, siga o restante deste guia.

1. Abra um terminal.

2. Adicione o `dotnet` repositório à sua lista de fontes correspondente à sua distribuição.

    ```bash
    wget -q https://packages.microsoft.com/config/ubuntu/$(lsb_release -rs)/packages-microsoft-prod.deb
    sudo dpkg -i packages-microsoft-prod.deb
    ```

3. Adicione a nova chave MS Open Tech GNU Privacy Guard (GnuPG ou GPG) ao seu keyring de APT.

    ```bash
    sudo curl -fsSL https://packages.microsoft.com/keys/msopentech.asc | sudo apt-key add -
    ```

4. Adicione a chave GPG oficial do Docker ao arquivo de chaves APT.

    ```bash
    sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
    ```

5. Configure o repositório do Docker.

    ```bash
    sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
    ```

6. Adicionar chave de JDK Azul ao seu token de autenticação APT e configurar seu repositório.

    ```bash
    sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 0xB1998361219BD9C9
    sudo apt-add-repository "deb http://repos.azul.com/azure-only/zulu/apt stable main"
    ```

7. Atualize suas listas de pacote com base nos repositórios recém-adicionados.

    ```bash
    sudo apt-get update
    ```

## <a name="install-and-set-up-the-service-fabric-sdk-for-a-local-cluster"></a>Instalar e configurar o SDK do Service Fabric para um cluster local

Após a atualização das fontes, você poderá instalar o SDK. Instale o pacote do SDK do Service Fabric, confirme a instalação e aceite o contrato de licença.

### <a name="ubuntu"></a>Ubuntu

```bash
sudo apt-get install servicefabricsdkcommon
```

> [!TIP]
>   Os comandos a seguir automatizam a aceitação da licença para pacotes do Service Fabric:
>   ```bash
>   echo "servicefabric servicefabric/accepted-eula-ga select true" | sudo debconf-set-selections
>   echo "servicefabricsdkcommon servicefabricsdkcommon/accepted-eula-ga select true" | sudo debconf-set-selections
>   ```

# <a name="red-hat-enterprise-linux-74"></a>[Red Hat Enterprise Linux 7.4](#tab/sdksetuprhel74)

## <a name="update-your-yum-repositories"></a>Atualizar seus repositórios do yum
Para instalar o SDK e o pacote de tempo de execução associado por meio da ferramenta de linha de comando Yum, você deve primeiro atualizar as origens do pacote.

## <a name="manual-installation-rhel"></a>Instalação manual (RHEL)
Para a instalação manual do runtime do Service Fabric e SDK comum, siga o restante deste guia.

1. Abra um terminal.
2. Faça o download e instale EPEL (Pacotes adicionais para Enterprise Linux).

    ```bash
    wget https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
    sudo yum install epel-release-latest-7.noarch.rpm
    ```
3. Adicione o repositório de pacotes de EfficiOS RHEL7 ao seu sistema.

    ```bash
    sudo wget -P /etc/yum.repos.d/ https://packages.efficios.com/repo.files/EfficiOS-RHEL7-x86-64.repo
    ```

4. Importe a chave de assinatura do pacote EfficiOS para o chaveiro GPG local.

    ```bash
    sudo rpmkeys --import https://packages.efficios.com/rhel/repo.key
    ```

5. Adicione o repositório RHEL da Microsoft ao seu sistema.

    ```bash
    curl https://packages.microsoft.com/config/rhel/7.4/prod.repo > ./microsoft-prod.repo
    sudo cp ./microsoft-prod.repo /etc/yum.repos.d/
    ```

## <a name="install-and-set-up-the-service-fabric-sdk-for-a-local-cluster-rhel"></a>Instalar e configurar o SDK do Service Fabric para um cluster local (RHEL)

Após a atualização das fontes, você poderá instalar o SDK. Instale o pacote do SDK do Service Fabric, confirme a instalação e aceite o contrato de licença.

```bash
sudo yum install servicefabricsdkcommon
```

---

## <a name="included-packages"></a>Pacotes incluídos
O runtime do Service Fabric que vem com a instalação anterior do SDK inclui os pacotes da tabela a seguir. 

 | | DotNetCore | Java | Python | NodeJS | 
--- | --- | --- | --- |---
**Ubuntu** | 2.0.7 | AzulJDK 1.8 | Implícito do npm | mais recente |
**RHEL** | - | OpenJDK 1.8 | Implícito do npm | mais recente |

## <a name="set-up-a-local-cluster"></a>Configurar um cluster local
1. Inicie um cluster de Service Fabric local para desenvolvimento.

# <a name="container-based-local-cluster"></a>[Cluster local baseado em contêiner](#tab/localclusteroneboxcontainer)

Inicie um cluster [Service Fabric Onebox](https://hub.docker.com/_/microsoft-service-fabric-onebox) baseado em contêiner.

1. Instale o Moby para poder implantar contêineres do Docker.
    ```bash
    sudo apt-get install moby-engine moby-cli -y
    ```
2. Atualize a configuração do daemon do Docker em seu host com as configurações a seguir e reinicie o daemon do Docker. Detalhes: [habilitar o suporte a IPv6](https://docs.docker.com/config/daemon/ipv6/)

    ```json
    {
        "ipv6": true,
        "fixed-cidr-v6": "fd00::/64"
    }
    ```

3. Inicie o cluster.<br/>
    <b>Ubuntu 18, 4 LTS:</b>
    ```bash
    docker run --name sftestcluster -d -v /var/run/docker.sock:/var/run/docker.sock -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 mcr.microsoft.com/service-fabric/onebox:u18
    ```

    <b>Ubuntu 16, 4 LTS:</b>
    ```bash
    docker run --name sftestcluster -d -v /var/run/docker.sock:/var/run/docker.sock -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 mcr.microsoft.com/service-fabric/onebox:u16
    ```

    >[!TIP]
    > Por padrão, isso busca a imagem com a versão mais recente do Service Fabric. Para obter revisões específicas, visite a página do [Hub do Docker](https://hub.docker.com/r/microsoft/service-fabric-onebox/) .

# <a name="local-cluster"></a>[Cluster local](#tab/localcluster)

Depois de instalar o SDK usando as etapas acima, inicie um cluster local.

1. Execute o script de configuração do cluster.

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```

---

2. Abra um navegador da Web e vá para **Service Fabric Explorer** ( `http://localhost:19080/Explorer` ). Ao iniciar o cluster, você vê o painel do Service Fabric Explorer. A configuração completa do cluster pode demorar alguns minutos. Se o seu navegador não conseguir abrir a URL, ou se o Service Fabric Explorer não mostrar que o sistema está pronto, aguarde alguns minutos e tente novamente.

    ![Service Fabric Explorer no Linux][sfx-linux]

    Agora você pode implantar os pacotes do aplicativo Service Fabric predefinidos ou novos com base nos contêineres ou executáveis do convidado. Para compilar novos serviços usando os SDKs do Java ou .NET Core, siga as etapas de configuração opcionais fornecidas nas próximas seções.


> [!NOTE]
> Não há suporte para os clusters autônomos no Linux.


> [!TIP]
> Se você tiver um disco SSD disponível, é recomendável passar um caminho de pasta SSD usando `--clusterdataroot` com devclustersetup.sh para obter um desempenho melhor.

## <a name="set-up-the-service-fabric-cli"></a>Configurar a CLI do Service Fabric

A [CLI do Service Fabric](service-fabric-cli.md) tem comandos para interagir com as entidades do Service Fabric, inclusive clusters e aplicativos. Para instalar a CLI, siga as instruções em [CLI do Service Fabric](service-fabric-cli.md).


## <a name="set-up-yeoman-generators-for-containers-and-guest-executables"></a>Configurar os geradores do Yeoman para os contêineres e executáveis do convidado
O Service Fabric fornece ferramentas de scaffolding que ajudam a criar aplicativos do Service Fabric em um terminal usando geradores de modelos Yeoman. Siga estas etapas para configurar os geradores de modelo do Yeoman do Service Fabric:

1. Instale o Node.js e o npm em seu computador.

    ```bash
    sudo add-apt-repository "deb https://deb.nodesource.com/node_8.x $(lsb_release -s -c) main"
    sudo apt-get update
    sudo apt-get install nodejs
    ```
2. Instale o gerador de modelos [Yeoman](https://yeoman.io/) do npm em seu computador.

    ```bash
    sudo npm install -g yo
    ```
3. Instalar o gerador de contêineres Yeo do Service Fabric e o gerador de executáveis do convidado a partir do npm.

    ```bash
    sudo npm install -g generator-azuresfcontainer  # for Service Fabric container application
    sudo npm install -g generator-azuresfguest      # for Service Fabric guest executable application
    ```

Após a instalação dos geradores, crie os serviços do contêiner ou do executável do convidado executando `yo azuresfguest` ou `yo azuresfcontainer` respectivamente.

## <a name="set-up-net-core-31-development"></a>Configurar o desenvolvimento do .NET Core 3,1

Instale o [SDK do .NET Core 3,1 para Ubuntu](https://www.microsoft.com/net/core#linuxubuntu) para começar a [criar aplicativos Service Fabric em C#](service-fabric-create-your-first-linux-application-with-csharp.md). Os pacotes para aplicativos do .NET Core Service Fabric são hospedados em NuGet.org.

## <a name="set-up-java-development"></a>Configurar o desenvolvimento de Java

Para criar serviços do Service Fabric usando o Java, instale o Gradle para executar tarefas de compilação. Execute o comando abaixo para instalar o Gradle. As bibliotecas Java do Service Fabric são extraídas do Maven.


* Ubuntu

    ```bash
    curl -s https://get.sdkman.io | bash
    sdk install gradle 5.1
    ```

* Red Hat Enterprise Linux 7.4 (suporte à versão prévia do Service Fabric)

  ```bash
  sudo yum install java-1.8.0-openjdk-devel
  curl -s https://get.sdkman.io | bash
  sdk install gradle
  ```

Você também precisa instalar o gerador de Yeo do Service Fabric para executáveis do Java. Verifique se você tem o [Yeoman instalado](#set-up-yeoman-generators-for-containers-and-guest-executables), depois execute o seguinte comando:

  ```bash
  npm install -g generator-azuresfjava
  ```
 
## <a name="install-the-eclipse-plug-in-optional"></a>Instalar o plug-in do Eclipse (opcional)

Você pode instalar o plug-in Eclipse do Service Fabric de dentro do IDE do Eclipse para Desenvolvedores Java ou Java EE para Desenvolvedores. Você pode usar o Eclipse para criar aplicativos executáveis de convidado do Service Fabric e aplicativos de contêiner além dos aplicativos Java do Service Fabric.

> [!IMPORTANT]
> O plug-in do Service Fabric requer o Eclipse Neon ou uma versão posterior. Consulte as instruções após essa observação para saber como verificar qual versão do Eclipse você possui. Se você tiver uma versão anterior do Eclipse instalada, pode baixar as versões mais recentes no [site do Eclipse](https://www.eclipse.org). Não é recomendável instalar por cima de (substituir) uma instalação existente do Eclipse. Remova-a antes de executar o instalador ou instale a versão mais recente em um diretório diferente.
> 
> No Ubuntu, é recomendável instalar diretamente a partir do site do Eclipse em vez de usar um instalador de pacote (`apt` ou `apt-get`). Isso garante que você obtenha a versão mais atual do Eclipse. Você pode instalar o IDE do Eclipse para Desenvolvedores Java ou para Java EE para Desenvolvedores.

1. No Eclipse, verifique se você instalou o Eclipse Neon ou posterior e o Buildship versão 2.2.1 ou posterior. Verifique as versões dos componentes instalados selecionando **ajuda**  >  **sobre** os  >  **detalhes da instalação** do eclipse. Você pode atualizar Buildship usando as instruções em [Buildship do Eclipse: plug-ins do Eclipse para Gradle][buildship-update].

2. Para instalar o plug-in Service Fabric, selecione **ajuda**  >  **instalar novo software** .

3. Na caixa **trabalhar com** , digite **https: \/ /DL.Microsoft.com/Eclipse** .

4. Selecione **Adicionar** .

    ![Página do software disponível][sf-eclipse-plugin]

5. Selecione o plug-in **ServiceFabric** e selecione **Avançar** .

6. Execute as etapas de instalação. Depois aceite os termos de licença do usuário final.

Se você já tiver o plug-in Service Fabric instalado, verifique se tem a versão mais recente. Selecione **ajuda**  >  **sobre** os  >  **detalhes de instalação** do eclipse. Em seguida, pesquise Service Fabric na lista de plug-ins instalados. Selecione **Atualizar** se uma versão mais recente estiver disponível.

Para obter mais informações, consulte [Plug-in Service Fabric para o desenvolvimento de aplicativos Java do Eclipse](service-fabric-get-started-eclipse.md).

## <a name="update-the-sdk-and-runtime"></a>Atualizar SDK e execução

Para atualizar para a versão mais recente do SDK e runtime, execute os seguintes comandos.

```bash
sudo apt-get update
sudo apt-get install servicefabric servicefabricsdkcommon
```
Para atualizar os binários SDK do Java a partir do Maven, você precisa atualizar os detalhes da versão do binário correspondente no arquivo ``build.gradle`` para apontar para a versão mais recente. Para saber exatamente onde você precisa atualizar a versão, consulte qualquer ``build.gradle`` arquivo  nos [exemplos de introdução do Service Fabric](https://github.com/Azure-Samples/service-fabric-java-getting-started).

> [!NOTE]
> Atualizar os pacotes pode causar a interrupção do seu cluster de desenvolvimento local. Reinicie o cluster local após uma atualização seguindo as instruções deste artigo.

## <a name="remove-the-sdk"></a>Remover o SDK
Para remover os SDKs do Service Fabric, execute os comandos a seguir.

* Ubuntu

    ```bash
    sudo apt-get remove servicefabric servicefabicsdkcommon
    npm uninstall -g generator-azuresfcontainer
    npm uninstall -g generator-azuresfguest
    sudo apt-get install -f
    ```


* Red Hat Enterprise Linux 7.4 (suporte à versão prévia do Service Fabric)

    ```bash
    sudo yum remove servicefabric servicefabicsdkcommon
    npm uninstall -g generator-azuresfcontainer
    npm uninstall -g generator-azuresfguest
    ```

## <a name="next-steps"></a>Próximas etapas

* [Criar e implantar seu primeiro aplicativo Java do Service Fabric no Linux usando o Yeoman](service-fabric-create-your-first-linux-application-with-java.md)
* [Criar e implantar seu primeiro aplicativo Java do Service Fabric no Linux usando o plug-in Service Fabric para o Eclipse](service-fabric-get-started-eclipse.md)
* [Criar seu primeiro aplicativo do CSharp no Linux](service-fabric-create-your-first-linux-application-with-csharp.md)
* [Preparar seu ambiente de desenvolvimento no OSX](service-fabric-get-started-mac.md)
* [Preparar um ambiente de desenvolvimento do Linux no Windows](service-fabric-local-linux-cluster-windows.md)
* [Gerenciar seus aplicativos usando a CLI do Service Fabric](service-fabric-application-lifecycle-sfctl.md)
* [Diferenças entre Windows e Linux do Service Fabric](service-fabric-linux-windows-differences.md)
* [Introdução à CLI do Service Fabric](service-fabric-cli.md)

<!-- Links -->

[azure-xplat-cli-github]: https://github.com/Azure/azure-xplat-cli
[install-node]: https://nodejs.org/en/download/package-manager/#installing-node-js-via-package-manager
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship

<!--Images -->

[sf-eclipse-plugin]: ./media/service-fabric-get-started-linux/service-fabric-eclipse-plugin.png
[sfx-linux]: ./media/service-fabric-get-started-linux/sfx-linux.png
