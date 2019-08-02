---
title: Criar um aplicativo de contêiner Linux no Service Fabric no Azure | Microsoft Docs
description: Nesse início rápido, você construirá uma imagem do Docker com o seu aplicativo, enviará por push a imagem para um registro de contêiner e implantará um contêiner a um cluster do Service Fabric.
services: service-fabric
documentationcenter: linux
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: python
ms.topic: quickstart
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/22/2019
ms.author: suhuruli
ms.custom: mvc
ms.openlocfilehash: 0f6e24d2790f78ab0ec7292640fda3ab30e03a03
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/29/2019
ms.locfileid: "68599410"
---
# <a name="quickstart-deploy-linux-containers-to-service-fabric"></a>Início Rápido: Implantar contêineres do Linux no Service Fabric

O Azure Service Fabric é uma plataforma de sistemas distribuídos para implantação e gerenciamento de contêineres e microsserviços escalonáveis e confiáveis.

Este guia de início rápido mostra como implantar contêineres do Linux em um cluster do Service Fabric no Azure. Uma vez concluído, você terá um aplicativo de votação que consiste em um front-end de Web em Python e um back-end em Redis em execução em um cluster do Service Fabric. Você também aprenderá a fazer o failover de um aplicativo e a dimensionar um aplicativo em seu cluster.

![Página da Web do aplicativo de votação][quickstartpic]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de início rápido:

1. Crie uma [conta gratuita do Azure](https://azure.microsoft.com/free/) antes de começar se você não tiver uma assinatura.

2. Instale a [CLI do Azure](/cli/azure/install-azure-cli-apt?view=azure-cli-latest)

3. Instalar o [SDK e a CLI do Service Fabric](service-fabric-get-started-linux.md#installation-methods)

4. Instalar o [Git](https://git-scm.com/)


## <a name="get-the-application-package"></a>Obter o pacote de aplicativos

Para implantar os contêineres no Service Fabric, você precisa de um conjunto de arquivos de manifesto (a definição do aplicativo) que descreve os contêineres individuais e o aplicativo.

Em um console, use o Git para clonar uma cópia da definição de aplicativo; altere os diretórios para o diretório `Voting` em seu clone.

```bash
git clone https://github.com/Azure-Samples/service-fabric-containers.git

cd service-fabric-containers/Linux/container-tutorial/Voting
```

## <a name="create-a-service-fabric-cluster"></a>Criar um cluster do Service Fabric

Para implantar o aplicativo no Azure, é necessário que um cluster do Service Fabric execute o aplicativo. Os comandos a seguir criam um cluster de cinco nós no Azure.  Os comandos também criam um certificado autoassinado, adicionam-no a um cofre de chaves e baixam o certificado localmente. O novo certificado é usado para proteger o cluster quando ele é implantado e é usado para autenticar clientes.

```azurecli
#!/bin/bash

# Variables
ResourceGroupName="containertestcluster" 
ClusterName="containertestcluster" 
Location="eastus" 
Password="q6D7nN%6ck@6" 
Subject="containertestcluster.eastus.cloudapp.azure.com" 
VaultName="containertestvault" 
VmPassword="Mypa$$word!321"
VmUserName="sfadminuser"

# Login to Azure and set the subscription
az login

az account set --subscription <mySubscriptionID>

# Create resource group
az group create --name $ResourceGroupName --location $Location 

# Create secure five node Linux cluster. Creates a key vault in a resource group
# and creates a certficate in the key vault. The certificate's subject name must match 
# the domain that you use to access the Service Fabric cluster.  The certificate is downloaded locally.
az sf cluster create --resource-group $ResourceGroupName --location $Location --certificate-output-folder . --certificate-password $Password --certificate-subject-name $Subject --cluster-name $ClusterName --cluster-size 5 --os UbuntuServer1604 --vault-name $VaultName --vault-resource-group $ResourceGroupName --vm-password $VmPassword --vm-user-name $VmUserName
```

> [!Note]
> O serviço de front-end da Web está configurado para escutar o tráfego de entrada na porta 80. Por padrão, a porta 80 está aberta no seu cluster de VMs e no Azure Load Balancer.
>

## <a name="configure-your-environment"></a>Configurar seu ambiente

O Service Fabric fornece várias ferramentas que você pode usar para gerenciar um cluster e seus aplicativos:

- Service Fabric Explorer, uma ferramenta baseada no navegador.
- CLI (Interface de Linha de Comando) do Service Fabric, que é executada sobre a CLI do Azure. 
- Comandos do PowerShell.

Neste início rápido você usa a CLI do Service Fabric e o Service Fabric Explorer (uma ferramenta baseada na Web). Para usar o Service Fabric Explorer, você precisa importar o arquivo PFX de certificado para o navegador. Por padrão, o arquivo PFX não tem nenhuma senha.

O Mozilla Firefox é o navegador padrão no Ubuntu 16.04. Para importar o certificado para o Firefox, clique no botão de menu no canto superior direito do seu navegador e clique em **Opções**. Na página **Preferências**, use a caixa de pesquisa para procurar por "certificados". Clique em **Exibir Certificados**, selecione a guia **Seus Certificados**, clique em **Importar** e siga os prompts para importar o certificado.

   ![Instalar certificado no Firefox](./media/service-fabric-quickstart-containers-linux/install-cert-firefox.png)

## <a name="deploy-the-service-fabric-application"></a>Implantar o aplicativo do Service Fabric

1. Conecte-se ao cluster do Service Fabric no Azure usando a CLI. O ponto de extremidade é o ponto de extremidade de gerenciamento do cluster. Você criou o arquivo PEM na seção anterior. 

    ```bash
    sfctl cluster select --endpoint https://containertestcluster.eastus.cloudapp.azure.com:19080 --pem containertestcluster22019013100.pem --no-verify
    ```

2. Use o script de instalação para copiar a definição do aplicativo de votação para o cluster, registrar o tipo de aplicativo e criar uma instância do aplicativo.  O arquivo PEM do certificado deve estar localizado no mesmo diretório que o arquivo *install.sh*.

    ```bash
    ./install.sh
    ```

3. Abra um navegador da Web e navegue até o ponto de extremidade do Service Fabric Explorer para seu cluster. O ponto de extremidade tem o seguinte formato: **https://\<my-azure-service-fabric-cluster-url>:19080/Explorer**; por exemplo, `https://containertestcluster.eastus.cloudapp.azure.com:19080/Explorer`. </br>

4. Expanda o nó **Aplicativos** para ver que agora há uma entrada para o tipo de aplicativo de votação e a instância que você criou.

    ![Service Fabric Explorer][sfx]

5. Para conectar-se ao contêiner em execução, abra um navegador da Web e navegue até a URL do seu cluster. Por exemplo, `http://containertestcluster.eastus.cloudapp.azure.com:80`. Você deve ver o aplicativo de votação no navegador.

    ![Página da Web do aplicativo de votação][quickstartpic]

> [!NOTE]
> Você também pode implantar aplicativos do Service Fabric com o Docker Compose. Por exemplo, o comando a seguir pode ser usado para implantar e instalar o aplicativo no cluster usando o Docker Compose.
>  ```bash
> sfctl compose create --deployment-name TestApp --file-path ../docker-compose.yml
> ```

## <a name="fail-over-a-container-in-a-cluster"></a>Fazer failover de um contêiner em um cluster

O Service Fabric faz com que suas instâncias de contêiner sejam movidas automaticamente para outros nós no cluster em caso de falha. Também é possível drenar um nó para contêineres manualmente e movê-los normalmente para outros nós no cluster. O Service Fabric fornece várias maneiras de dimensionar seus serviços. Nas etapas a seguir, use o Service Fabric Explorer.

Para fazer failover do contêiner de front-end, execute as seguintes etapas:

1. Abra o Service Fabric Explorer no cluster, por exemplo, `https://containertestcluster.eastus.cloudapp.azure.com:19080/Explorer`.
2. Clique no nó **fabric:/Voting/azurevotefront** do modo de exibição de árvore e expanda o nó de partição (representado por um GUID). Observe o nome do nó no modo de exibição de árvore, que mostra os nós onde o contêiner está em execução no momento, por exemplo, `_nodetype_1`.
3. Expanda o nó **Nós** no modo de exibição de árvore. Clique nas reticências (...) ao lado do nó que está executando o contêiner.
4. Escolha **Reiniciar** para reiniciar o nó e confirmar a ação de reinicialização. A reinicialização faz o contêiner fazer failover para outro nó no cluster.

    ![Modo de exibição de nó no Service Fabric Explorer][sfxquickstartshownodetype]

## <a name="scale-applications-and-services-in-a-cluster"></a>Dimensionar aplicativos e serviços em um cluster

Os serviços do Service Fabric podem ser facilmente dimensionados em um cluster para acomodar a carga dos serviços. Dimensione um serviço alterando o número de instâncias em execução no cluster.

Para dimensionar o serviço de front-end da Web, realize as seguintes etapas:

1. Abra o Service Fabric Explorer no cluster, por exemplo, `https://containertestcluster.eastus.cloudapp.azure.com:19080`.
2. Clique nas reticências (três pontos) ao lado do nó **fabric:/Voting/azurevotefront** no modo de exibição de árvore e escolha **Dimensionar Serviço**.

    ![Início do serviço de escala do Service Fabric Explorer][containersquickstartscale]

    Agora, você pode optar por dimensionar o número de instâncias do serviço de front-end da Web.

3. Altere o número para **2** e clique em **Dimensionar Serviço**.
4. Clique no nó **fabric:/Voting/azurevotefront** no modo de exibição de árvore e expanda o nó de partição (representado por um GUID).

    ![Serviço de escala do Service Fabric Explorer concluído][containersquickstartscaledone]

    Agora é possível ver que o serviço tem duas instâncias. No modo de exibição de árvore, você pode ver em quais nós as instâncias são executadas.

Com essa tarefa de gerenciamento simples, você dobrou o número de recursos disponíveis para o serviço de front-end processar a carga do usuário. É importante entender que você não precisa de várias instâncias de um serviço para que ele seja executado de forma confiável. Se um serviço falhar, o Service Fabric fará com que uma nova instância de serviço seja executada no cluster.

## <a name="clean-up-resources"></a>Limpar recursos

Use o script de desinstalação (uninstall.sh) fornecido com o modelo para excluir a instância do aplicativo no cluster e cancelar o registro do tipo de aplicativo. Esse script leva algum tempo para limpar a instância e, portanto, você não deve executar o script de instalação imediatamente após esse script. Você pode usar o Service Fabric Explorer para determinar quando a instância foi removida e o tipo de aplicativo teve o registro cancelado.

```bash
./uninstall.sh
```

A maneira mais simples de excluir o cluster e todos os recursos que ele consume é excluir o grupo de recursos.

Entre no Azure e selecione a ID de assinatura com a qual você deseja remover o cluster. Você pode encontrar sua ID de assinatura fazendo logon no portal do Azure. Exclua o grupo de recursos e todos os recursos do cluster usando o comando [az group delete](/cli/azure/group?view=azure-cli-latest).

```azurecli
az login
az account set --subscription <guid>
ResourceGroupName="containertestcluster"
az group delete --name $ResourceGroupName
```

Se você concluiu o trabalho com o cluster, remova o certificado do repositório de certificados. Por exemplo:
- No Windows: Use o [snap-in MMC de certificados](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/how-to-view-certificates-with-the-mmc-snap-in). Selecione **Minha conta de usuário** ao adicionar o snap-in. Navegue até `Certificates - Current User\Personal\Certificates` e remova o certificado.
- No Mac: Use o aplicativo de Conjunto de Chaves.
- No Ubuntu: siga as etapas usadas para exibir certificados e remova o certificado.

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você implantou um aplicativo de contêiner do Linux em um cluster do Service Fabric no Azure, realizou failover no aplicativo e dimensionou o aplicativo no cluster. Para saber mais sobre como trabalhar com contêineres do Linux no Service Fabric, prossiga para o tutorial sobre aplicativos de contêiner do Linux.

> [!div class="nextstepaction"]
> [Criar um aplicativo de contêiner do Linux](./service-fabric-tutorial-create-container-images.md)

[sfx]: ./media/service-fabric-quickstart-containers-linux/containersquickstartappinstance.png
[quickstartpic]: ./media/service-fabric-quickstart-containers-linux/votingapp.png
[sfxquickstartshownodetype]:  ./media/service-fabric-quickstart-containers-linux/containersquickstartrestart.png
[containersquickstartscale]: ./media/service-fabric-quickstart-containers-linux/containersquickstartscale.png
[containersquickstartscaledone]: ./media/service-fabric-quickstart-containers-linux/containersquickstartscaledone.png
