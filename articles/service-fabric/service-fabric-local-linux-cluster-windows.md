---
title: Configurar um cluster do Linux do Azure Service Fabric no Windows
description: Este artigo aborda como configurar clusters do Service Fabric Linux executando em máquinas de desenvolvimento do Windows. Essa abordagem é útil para o desenvolvimento de plataforma cruzada.
ms.topic: conceptual
ms.date: 10/16/2020
ms.openlocfilehash: 7b25a84e76773baea9f17430df1b7ba13aa661aa
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93087070"
---
# <a name="set-up-a-linux-service-fabric-cluster-on-your-windows-developer-machine"></a>Configurar um cluster do Linux do Service Fabric no seu computador de desenvolvedor do Windows

Este documento aborda como configurar um cluster local Service Fabric do Linux em um computador de desenvolvimento do Windows. Configurar um cluster do Linux local é útil para testar rapidamente aplicativos segmentados para clusters do Linux, mas são desenvolvidos em um computador do Windows.

## <a name="prerequisites"></a>Pré-requisitos
Os clusters Service Fabric baseados em Linux não são executados no Windows, mas para habilitar o protótipo de plataforma cruzada, fornecemos um contêiner do Docker de cluster do Linux Service Fabric um box, que pode ser implantado via Docker for Windows.

Antes de começar, você precisa do:

* Pelo menos 4 GB de RAM
* Versão mais recente do [Docker for Windows](https://store.docker.com/editions/community/docker-ce-desktop-windows)
* O Docker deve estar em execução no modo de contêineres do Linux

>[!TIP]
> Para instalar o Docker em seu computador Windows, siga as etapas na [documentação do Docker](https://store.docker.com/editions/community/docker-ce-desktop-windows/plans/docker-ce-desktop-windows-tier?tab=instructions). Depois de instalar, [verifique sua instalação](https://docs.docker.com/docker-for-windows/#check-versions-of-docker-engine-compose-and-machine).
>

## <a name="create-a-local-container-and-setup-service-fabric"></a>Criar um contêiner local e configuração do Service Fabric
Para configurar um contêiner do Docker local e ter um Cluster Service Fabric em execução, execute as seguintes etapas:


1. Atualize a configuração do daemon de Docker em seu host com o seguinte e reinicie o daemon de Docker: 

    ```json
    {
      "ipv6": true,
      "fixed-cidr-v6": "2001:db8:1::/64"
    }
    ```
    A maneira recomendável de atualizar o é ir para: 

    * Ícone do Docker > configurações > mecanismo do Docker
    * Adicione os novos campos listados acima
    * Aplicar & reiniciar – reinicie o daemon do Docker para que as alterações entrem em vigor.

2. Inicie o cluster por meio do PowerShell.<br/>
    <b>Ubuntu 18, 4 LTS:</b>
    ```powershell
    docker run --name sftestcluster -d -v /var/run/docker.sock:/var/run/docker.sock -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 mcr.microsoft.com/service-fabric/onebox:u18
    ```

    <b>Ubuntu 16, 4 LTS:</b>
    ```powershell
    docker run --name sftestcluster -d -v /var/run/docker.sock:/var/run/docker.sock -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 mcr.microsoft.com/service-fabric/onebox:u16
    ```

    >[!TIP]
    > Por padrão, isso busca a imagem com a versão mais recente do Service Fabric. Para obter revisões específicas, consulte a página [Service Fabric Onebox](https://hub.docker.com/_/microsoft-service-fabric-onebox) no Hub do Docker.



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

    ```powershell 
    docker build -t mysfcluster .
    ```
    
    >[!NOTE]
    >Essa operação levará algum tempo, mas só precisa ser feita uma vez.

    Agora você pode iniciar rapidamente uma cópia local de Service Fabric sempre que precisar dela executando:

    ```powershell 
    docker run --name sftestcluster -d -v /var/run/docker.sock:/var/run/docker.sock -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 mysfcluster
    ```

    >[!TIP]
    >Forneça um nome para sua instância de contêiner para que ela possa ser tratada de forma mais legível. 
    >
    >Se o seu aplicativo estiver escutando em determinadas portas, as portas deverão ser especificadas usando marcas `-p` adicionais. Por exemplo, se seu aplicativo estiver escutando na porta 8080, adicione a seguinte marca `-p`:
    >
    >`docker run -itd -p 19000:19000 -p 19080:19080 -p 8080:8080 --name sfonebox mcr.microsoft.com/service-fabric/onebox:u18`
    >


4. O cluster levará pouco tempo para ser iniciado, e é possível exibir logs usando o comando a seguir ou ir até o painel para exibir a integridade dos clusters `http://localhost:19080`:

    ```powershell 
    docker logs sftestcluster
    ```

5. Depois que o cluster for implantado com êxito, conforme observado na etapa 4, você poderá ir ``http://localhost:19080`` de seu computador Windows para localizar o painel do Service Fabric Explorer. Neste ponto, você pode se conectar a esse cluster usando as ferramentas do seu computador de desenvolvedor do Windows e implantar aplicativos destinados a clusters do Linux Service Fabric. 

    > [!NOTE]
    > Atualmente, não há suporte para o plugin Eclipse no Windows. 

6. Quando terminar, pare e limpe o contêiner com este comando:

    ```powershell 
    docker rm -f sftestcluster
    ```

### <a name="known-limitations"></a>Limitações conhecidas 
 
 A seguir estão algumas limitações conhecidas do cluster local em execução em um contêiner para Mac’s: 
 
 * O serviço DNS não é executado e atualmente não tem suporte no contêiner. [Problema #132](https://github.com/Microsoft/service-fabric/issues/132)
 * A execução de aplicativos baseados em contêiner requer a execução do it em um host Linux. Atualmente, não há suporte para aplicativos de contêiner aninhados.

## <a name="next-steps"></a>Próximas etapas
* [Criar e implantar seu primeiro aplicativo Java de do Service Fabric no Linux usando Yeoman](service-fabric-create-your-first-linux-application-with-java.md)
* Introdução ao [Eclipse](./service-fabric-get-started-eclipse.md)
* Confira outras [amostras de Java](https://github.com/Azure-Samples/service-fabric-java-getting-started)
* Saiba mais sobre [as opções de suporte do Service Fabric](service-fabric-support.md)


<!-- Image references -->

[publishdialog]: ./media/service-fabric-manage-multiple-environment-app-configuration/publish-dialog-choose-app-config.png
[app-parameters-solution-explorer]:./media/service-fabric-manage-multiple-environment-app-configuration/app-parameters-in-solution-explorer.png
