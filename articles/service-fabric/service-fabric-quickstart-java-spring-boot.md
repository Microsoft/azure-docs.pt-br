---
title: 'Guia de início rápido: criar um aplicativo do Spring Boot no Azure Service Fabric'
description: Neste guia de início rápido, você implanta um aplicativo Spring Boot para o Azure Service Fabric usando um aplicativo Spring Boot de exemplo.
ms.date: 01/29/2019
ms.topic: quickstart
ms.custom:
- mvc
- devcenter
- seo-java-august2019
- seo-java-september2019
- devx-track-java
- mode-api
ms.openlocfilehash: ad01697349eab20aa55fc6f6b59bc0d317c5a7e2
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530852"
---
# <a name="quickstart-deploy-a-java-spring-boot-app-on-azure-service-fabric"></a>Guia de início rápido: implantar um aplicativo Java do Spring Boot no Azure Service Fabric

Neste início rápido, você implantará um aplicativo Java Spring Boot no Azure Service Fabric usando ferramentas de linha de comando conhecidas no Linux ou no macOS. O Azure Service Fabric é uma plataforma de sistemas distribuídos para implantação e gerenciamento de contêineres e microsserviços. 

## <a name="prerequisites"></a>Pré-requisitos

#### <a name="linux"></a>[Linux](#tab/linux)

- [Ambiente Java](./service-fabric-get-started-linux.md#set-up-java-development) e [Yeoman](./service-fabric-get-started-linux.md#set-up-yeoman-generators-for-containers-and-guest-executables)
- [SDK e CLI (interface de linha de comando) do Service Fabric](./service-fabric-get-started-linux.md#installation-methods)
- [Git](https://git-scm.com/downloads)

#### <a name="macos"></a>[MacOS](#tab/macos)

- [Ambiente Java e Yeoman](./service-fabric-get-started-mac.md#create-your-application-on-your-mac-by-using-yeoman)
- [SDK e CLI (interface de linha de comando) do Service Fabric](./service-fabric-cli.md#cli-mac)
- [Git](https://git-scm.com/downloads)

--- 

## <a name="download-the-sample"></a>Baixar o exemplo

Em uma janela de terminal, execute o comando a seguir para clonar o aplicativo Spring Boot de exemplo [Introdução](https://github.com/spring-guides/gs-spring-boot) no computador local.

```bash
git clone https://github.com/spring-guides/gs-spring-boot.git
```

## <a name="build-the-spring-boot-application"></a>Criar o aplicativo Spring Boot 
No diretório *gs-spring-boot/complete*, execute o comando abaixo para criar o aplicativo 

```bash
./gradlew build
``` 

## <a name="package-the-spring-boot-application"></a>Empacotar o aplicativo Spring Boot 
1. No diretório *gs-spring-boot* em seu clone, execute o comando `yo azuresfguest`. 

1. Insira os seguintes detalhes para cada aviso.

    ![Entradas do Spring Boot no Yeoman](./media/service-fabric-quickstart-java-spring-boot/yeoman-entries-spring-boot.png)

1. Na pasta *SpringServiceFabric/SpringServiceFabric/SpringGettingStartedPkg/code*, crie um arquivo chamado *entryPoint.sh*. Adicione o código a seguir ao arquivo *entryPoint.sh*. 

    ```bash
    #!/bin/bash
    BASEDIR=$(dirname $0)
    cd $BASEDIR
    java -jar *spring-boot*.jar
    ```

1. Adicionar o recurso de **Pontos de extremidade** ao arquivo *gs-spring-boot/SpringServiceFabric/SpringServiceFabric/SpringGettingStartedPkg/ServiceManifest.xml*

    ```xml 
        <Resources>
          <Endpoints>
            <Endpoint Name="WebEndpoint" Protocol="http" Port="8080" />
          </Endpoints>
       </Resources>
    ```

    O *ServiceManifest.xml* agora tem esta aparência: 

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <ServiceManifest Name="SpringGettingStartedPkg" Version="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" >

       <ServiceTypes>
          <StatelessServiceType ServiceTypeName="SpringGettingStartedType" UseImplicitHost="true">
       </StatelessServiceType>
       </ServiceTypes>

       <CodePackage Name="code" Version="1.0.0">
          <EntryPoint>
             <ExeHost>
                <Program>entryPoint.sh</Program>
                <Arguments></Arguments>
                <WorkingFolder>CodePackage</WorkingFolder>
             </ExeHost>
          </EntryPoint>
       </CodePackage>
        <Resources>
          <Endpoints>
            <Endpoint Name="WebEndpoint" Protocol="http" Port="8080" />
          </Endpoints>
       </Resources>
     </ServiceManifest>
    ```

Neste estágio, você criou um aplicativo Service Fabric para o Guia de Introdução do Spring Boot de exemplo que pode ser implantado no Service Fabric.

## <a name="run-the-application-locally"></a>Executar o aplicativo localmente

1. Inicie o cluster local nos computadores Ubuntu executando o seguinte comando:

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```

    Se estiver usando um Mac, inicie o cluster local na imagem do Docker (assumindo que você seguiu os [pré-requisitos](./service-fabric-get-started-mac.md#create-a-local-container-and-set-up-service-fabric) a fim de configurar o cluster local para Mac). 

    ```bash
    docker run --name sftestcluster -d -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 -p 8080:8080 mysfcluster
    ```

    A inicialização do cluster local leva algum tempo. Para confirmar se o cluster está totalmente funcional, acesse o Service Fabric Explorer em `http://localhost:19080`. Os cinco nós íntegros indicam que o cluster local está em execução. 
    
    ![O Service Fabric Explorer mostra nós íntegros](./media/service-fabric-quickstart-java-spring-boot/service-fabric-explorer-healthy-nodes.png)

1. Abra a pasta *gs-spring-boot/SpringServiceFabric*.
1. Execute o seguinte comando para se conectar ao cluster local.

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```
1. Execute o script *install.sh*.

    ```bash
    ./install.sh
    ```

1. Abra seu navegador da Web favorito e acesse o aplicativo em `http://localhost:8080`.

    ![Amostra do Spring Boot no Service Fabric](./media/service-fabric-quickstart-java-spring-boot/spring-boot-service-fabric-sample.png)

Agora é possível acessar o aplicativo Spring Boot que foi implantado em um cluster do Service Fabric.

Para obter mais informações, confira a amostra de [Introdução](https://spring.io/guides/gs/spring-boot/) do Spring Boot no site do Spring.

## <a name="scale-applications-and-services-in-a-cluster"></a>Dimensionar aplicativos e serviços em um cluster

Os serviços do Service Fabric podem ser colocados em escala em um cluster para acomodar uma alteração na carga dos serviços. Dimensione um serviço alterando o número de instâncias em execução no cluster. Existem várias maneiras de colocar seus serviços em escala, por exemplo, usando scripts ou comandos da CLI do Service Fabric (sfctl). As etapas a seguir usam o Service Fabric Explorer.

O Service Fabric Explorer é executado em todos os clusters do Service Fabric e pode ser acessado em um navegador, navegando para a porta de gerenciamento HTTP do cluster (19080), por exemplo, `http://localhost:19080`.

Para dimensionar o serviço de front-end da Web, faça o seguinte:

1. Abra o Service Fabric Explorer no cluster – por exemplo, `http://localhost:19080`.
1. Selecione as reticências (**...**) ao lado do nó **fabric:/SpringServiceFabric/SpringGettingStarted** no modo de exibição de árvore e selecione **Dimensionar Serviço**.

    ![Amostra do serviço de escala do Service Fabric Explorer](./media/service-fabric-quickstart-java-spring-boot/service-fabric-explorer-scale-sample.png)

    Agora é possível optar por dimensionar o número de instâncias do serviço.

1. Altere o número para **3** e selecione **Dimensionar Serviço**.

    Uma maneira alternativa de dimensionar o serviço usando a linha de comando é a seguinte.

    ```bash
    # Connect to your local cluster
    sfctl cluster select --endpoint https://<ConnectionIPOrURL>:19080 --pem <path_to_certificate> --no-verify

    # Run Bash command to scale instance count for your service
    sfctl service update --service-id 'SpringServiceFabric~SpringGettingStarted' --instance-count 3 --stateless 
    ``` 

1. Selecione o nó **fabric:/SpringServiceFabric/SpringGettingStarted** no modo de exibição de árvore e expanda o nó de partição (representado por um GUID).

    ![Serviço de escala do Service Fabric Explorer concluído](./media/service-fabric-quickstart-java-spring-boot/service-fabric-explorer-partition-node.png)

    O serviço tem três instâncias e o modo de exibição de árvore mostra em quais nós as instâncias são executadas.

Com essa tarefa de gerenciamento simples, você dobrou o número de recursos disponíveis para o serviço de front-end processar a carga do usuário. É importante entender que você não precisa de várias instâncias de um serviço para que ele seja executado de forma confiável. Se um serviço falhar, o Service Fabric fará com que uma nova instância de serviço seja executada no cluster.

## <a name="fail-over-services-in-a-cluster"></a>Fazer failover de serviços em um cluster

Para demonstrar o failover do serviço, é possível simular uma reinicialização do nó usando o Service Fabric Explorer. Verifique se apenas uma instância do seu serviço está em execução.

1. Abra o Service Fabric Explorer no cluster – por exemplo, `http://localhost:19080`.
1. Clique nas reticências (**...**) ao lado do nó que está executando a instância do serviço e reinicie o nó.

    ![Reiniciar o nó no Service Fabric Explorer](./media/service-fabric-quickstart-java-spring-boot/service=fabric-explorer-restart=node.png)
1. Agora a instância do serviço é movida para um nó diferente, e seu aplicativo não passa por nenhum tempo de inatividade.

    ![Reinicialização bem-sucedida do nó no Service Fabric Explorer](./media/service-fabric-quickstart-java-spring-boot/service-fabric-explorer-service-moved.png)

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você aprendeu a:

* Implantar um aplicativo Spring Boot no Service Fabric
* Implantar o aplicativo no cluster local
* Expandir o aplicativo para vários nós
* Executar failover de seu serviço sem nenhuma ocorrência de disponibilidade

Para saber mais sobre como trabalhar com aplicativos Java no Service Fabric, continue o tutorial para os aplicativos Java.

> [!div class="nextstepaction"]
> [Implantar um aplicativo Java](./service-fabric-tutorial-create-java-app.md)
