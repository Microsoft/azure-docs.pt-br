---
title: Configurar o cluster do Linux do Azure Service Fabric no Windows | Microsoft Docs
description: Este artigo aborda como configurar clusters do Service Fabric Linux executando em máquinas de desenvolvimento do Windows. Isto é particularmente útil para o desenvolvimento de plataformas cruzadas.
services: service-fabric
documentationcenter: .net
author: suhuruli
manager: mfussell
editor: ''
ms.assetid: bf84458f-4b87-4de1-9844-19909e368deb
ms.service: service-fabric
ms.devlang: java
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/20/2017
ms.author: suhuruli
ms.openlocfilehash: e700250a6ebcdb82f99c1b460a510811d7ceb96c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60719933"
---
# <a name="set-up-a-linux-service-fabric-cluster-on-your-windows-developer-machine"></a>Configurar um cluster do Linux do Service Fabric no seu computador de desenvolvedor do Windows

Este documento aborda como configurar um Service Fabric do Linux local em computadores de desenvolvedor do Windows. Configurar um cluster do Linux local é útil para testar rapidamente aplicativos segmentados para clusters do Linux, mas são desenvolvidos em um computador do Windows.

## <a name="prerequisites"></a>Pré-requisitos
Os clusters do Service Fabric baseados no Linux não são executados nativamente no Windows. Para executar um cluster do Service Fabric local, é fornecida uma imagem de contêiner Docker pré-configurada. Antes de começar, você precisa do:

* Pelo menos 4 GB de RAM
* Versão mais recente do [Docker](https://store.docker.com/editions/community/docker-ce-desktop-windows)
* Docker deve estar em execução no modo do Linux

>[!TIP]
> * Você pode seguir as etapas mencionadas na [documentação](https://store.docker.com/editions/community/docker-ce-desktop-windows/plans/docker-ce-desktop-windows-tier?tab=instructions) de Docker oficial para instalar o Docker no Windows. 
> * Após concluir a instalação, valide se ele foi instalado corretamente seguindo as etapas mencionadas [aqui](https://docs.docker.com/docker-for-windows/#check-versions-of-docker-engine-compose-and-machine)


## <a name="create-a-local-container-and-setup-service-fabric"></a>Criar um contêiner local e configuração do Service Fabric
Para configurar um contêiner local do Docker e ter um cluster do Service Fabric em execução, execute as seguintes etapas no PowerShell:


1. Atualize a configuração do daemon de Docker em seu host com o seguinte e reinicie o daemon de Docker: 

    ```json
    {
      "ipv6": true,
      "fixed-cidr-v6": "2001:db8:1::/64"
    }
    ```
    A maneira recomendada de atualização é - vá para o ícone do Docker > Configurações> Daemon> Avançado e atualize-o lá. Em seguida, reinicie o daemon de Docker para que as alterações entrem em vigor. 

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

    ```powershell 
    docker build -t mysfcluster .
    ```
    
    >[!NOTE]
    >Essa operação levará algum tempo, mas só precisa ser feita uma vez.

4. Agora é possível iniciar rapidamente uma cópia local do Service Fabric sempre que for necessário, executando:

    ```powershell 
    docker run --name sftestcluster -d -v //var/run/docker.sock:/var/run/docker.sock -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 mysfcluster
    ```

    >[!TIP]
    >Forneça um nome para sua instância de contêiner para que ela possa ser tratada de forma mais legível. 
    >
    >Se o seu aplicativo estiver escutando em determinadas portas, as portas deverão ser especificadas usando marcas `-p` adicionais. Por exemplo, se seu aplicativo estiver escutando na porta 8080, adicione a seguinte marca `-p`:
    >
    >`docker run -itd -p 19080:19080 -p 8080:8080 --name sfonebox microsoft/service-fabric-onebox`
    >

5. O cluster levará pouco tempo para iniciar e é possível exibir logs usando o comando a seguir ou ir até o painel para exibir a integridade dos clusters [http://localhost:19080](http://localhost:19080):

    ```powershell 
    docker logs sftestcluster
    ```

6. Após a conclusão com êxito da etapa 5, você poderá ir para ``http://localhost:19080`` do seu Windows e poderá visualizar o Service Fabric Explorer. Neste ponto, você poderá se conectar ao cluster usando qualquer ferramenta do seu computador de desenvolvedor do Windows e implantar o aplicativo direcionado para clusters do Service Fabric do Linux. 

    > [!NOTE]
    > Atualmente, não há suporte para o plugin Eclipse no Windows. 

7. Ao terminar, pare e limpe o contêiner com este comando:

    ```powershell 
    docker rm -f sftestcluster
    ```

### <a name="known-limitations"></a>Limitações conhecidas 
 
 A seguir estão algumas limitações conhecidas do cluster local em execução em um contêiner para Mac’s: 
 
 * O serviço DNS não é executado e não há suporte para [Problema 132](https://github.com/Microsoft/service-fabric/issues/132)

## <a name="next-steps"></a>Próximas etapas
* Introdução ao [Eclipse](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-eclipse)
* Confira outras [amostras de Java](https://github.com/Azure-Samples/service-fabric-java-getting-started)


<!-- Image references -->

[publishdialog]: ./media/service-fabric-manage-multiple-environment-app-configuration/publish-dialog-choose-app-config.png
[app-parameters-solution-explorer]:./media/service-fabric-manage-multiple-environment-app-configuration/app-parameters-in-solution-explorer.png
