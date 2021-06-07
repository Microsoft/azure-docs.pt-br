---
title: Configurar o Jenkins para um aplicativo Java no Service Fabric no Azure
description: Neste tutorial, saiba como configurar a integração contínua usando o Jenkins para implantar um aplicativo Service Fabric Java.
ms.topic: tutorial
ms.date: 08/27/2018
ms.custom: mvc, devx-track-java
ms.openlocfilehash: 4e29104f0586a8e18afb7a640903c57ce988b692
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91531965"
---
# <a name="tutorial-configure-a-jenkins-environment-to-enable-cicd-for-a-java-application-on-service-fabric"></a>Tutorial: Configurar um ambiente Jenkins para habilitar a CI/CD para um aplicativo Java no Service Fabric

Este tutorial é a parte cinco de uma série. Ele mostra como usar o Jenkins para implantar atualizações em seu aplicativo. Neste tutorial, o plug-in Jenkins do Service Fabric é usado com um repositório do GitHub que hospeda o aplicativo Voting para implantar o aplicativo em um cluster.

Na parte cinco da série, você aprende a:
> [!div class="checklist"]
> * Implantar o contêiner Jenkins do Service Fabric no seu computador
> * Configurar o ambiente Jenkins para implantação no Service Fabric
> * Atualizar seu aplicativo

Nesta série de tutoriais, você aprenderá a:
> [!div class="checklist"]
> * [Compilar um aplicativo Java do Reliable Services no Service Fabric](service-fabric-tutorial-create-java-app.md)
> * [Implantar e depurar o aplicativo em um cluster local](service-fabric-tutorial-debug-log-local-cluster.md)
> * [Implantar o aplicativo em um cluster do Azure](service-fabric-tutorial-java-deploy-azure.md)
> * [Configurar monitoramento e diagnóstico para o aplicativo](service-fabric-tutorial-java-elk.md)
> * Configurar CI/CD

## <a name="prerequisites"></a>Pré-requisitos

* Instalar o Git no computador local da [página de downloads do Git](https://git-scm.com/downloads). Para obter mais informações sobre o Git, leia a [documentação do Git](https://git-scm.com/docs).
* Ter um conhecimento prático do [Jenkins](https://jenkins.io/).
* Criar uma conta do [GitHub](https://github.com/) e saber como usar o GitHub.
* Instalar o [Docker](https://www.docker.com/community-edition) no seu computador.

## <a name="pull-and-deploy-service-fabric-jenkins-container-image"></a>Efetuar pull e implantar a imagem de contêiner Jenkins do Service Fabric

Você pode configurar o Jenkins dentro ou fora de um cluster do Service Fabric. As instruções a seguir mostram como configurá-lo fora de um cluster usando uma imagem fornecida do Docker. No entanto, um ambiente de compilação Jenkins pré-configurado também pode ser usado. A imagem de contêiner a seguir vem instalada com o plug-in do Service Fabric e está pronta para ser usada com o Service Fabric imediatamente.

1. Baixar a imagem de contêiner Jenkins do Service Fabric: ``docker pull rapatchi/jenkins:v10``. Esta imagem é fornecida com o plug-in do Service Fabric Jenkins pré-instalado.

1. Execute a imagem de contêiner com o local onde seus certificados do Azure são armazenados em seu computador local montado.

    ```bash
    docker run -itd -p 8080:8080 -v /service-fabric-java-quickstart/AzureCluster rapatchi/jenkins:v10
    ```

1. Obter a ID de instância de imagem de contêiner. Você pode listar todos os contêineres do Docker com o comando ``docker ps –a``

1. Recuperar a senha de sua instância do Jenkins executando o seguinte comando:

    ```sh
    docker exec [first-four-digits-of-container-ID] cat /var/jenkins_home/secrets/initialAdminPassword
    ```

    Se a ID do contêiner for 2d24a73b5964, use 2d 24.
    * Esta senha é necessária para entrar no painel do Jenkins do portal, que é ``http://<HOST-IP>:8080``
    * Depois de entrar pela primeira vez, é possível criar sua própria conta de usuário ou usar a conta de administrador.

1. Configurar o GitHub para trabalhar com o Jenkins usando as etapas mencionadas em [Gerando uma nova chave SSH e adicionando-a ao agente SSH](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/). Como os comandos são executados do contêiner Docker, siga as instruções do ambiente do Linux.
   * Use as instruções fornecidas pelo GitHub para gerar a chave SSH. Em seguida, adicione a chave SSH para a conta do GitHub que está hospedando o repositório.
   * Execute os comandos mencionados no link anterior no shell Jenkins Docker (e não no host).
   * Para fazer logon no shell Jenkins do host, use os seguintes comandos:

     ```sh
     docker exec -t -i [first-four-digits-of-container-ID] /bin/bash
     ```

     Verifique se o cluster ou a máquina em que a imagem de contêiner Jenkins está hospedada tem um IP público. Ter um IP voltado para o público permite que a instância do Jenkins receba notificações do GitHub.

## <a name="create-and-configure-a-jenkins-job"></a>Criar e configurar um trabalho do Jenkins

1. Primeiro, se você não tiver um repositório que possa usar para hospedar o projeto do Voting no GitHub, crie um. O repositório será chamado de **dev_test** no restante deste tutorial.

1. Crie um **novo item** no painel do Jenkins. em ``http://<HOST-IP>:8080``.

1. Insira um nome de item (por exemplo, **MyJob**). Selecione **projeto em estilo livre** e clique em **OK**.

1. Acesse a página do trabalho e clique em **Configurar**.

   a. Na seção geral, marque a caixa de seleção **Proejto GitHub** e especifique a URL do project GitHub. Essa URL hospeda o aplicativo Java do Service Fabric que você deseja integrar à integração contínua do Jenkins, no fluxo de implantação contínua (CI/CD) (por exemplo, ``https://github.com/testaccount/dev_test``).

   b. Na seção **Gerenciamento de Código-Fonte**, selecione **Git**. Especifique a URL do repositório que está hospedando o aplicativo Java do Service Fabric que você deseja integrar ao fluxo CI/CD do Jenkins (por exemplo, *`https://github.com/testaccount/dev_test.git`* ). Você também pode especificar aqui quais ramificações devem ser criadas (por exemplo, **/mestre**).

1. Configure seu *GitHub* (que está hospedando o repositório) para que ele seja capaz de se comunicar com o Jenkins. Use as seguintes etapas:

   a. Vá para sua página do repositório GitHub. Vá para **Configurações** > **Integrações e Serviços**.

   b. Selecione **Adicionar Serviço**, digite **Jenkins** e selecione o **plug-in Jenkins-Github**.

   c. Insira a URL do webhook Jenkins (por padrão, ele deve ser ``http://<PublicIPorFQDN>:8081/github-webhook/``). Clique em **adicionar/atualizar serviço**.

   d. Um evento de teste é enviado para a instância do Jenkins. Você verá uma marca de seleção verde ao lado do webhook no GitHub, e o projeto é criado.

   ![Configuração do Jenkins no Service Fabric](./media/service-fabric-tutorial-java-jenkins/jenkinsconfiguration.png)

1. Na seção **Criar Gatilhos**, selecione a opção de compilação desejada. Para este exemplo, você deseja disparar uma compilação sempre que ocorrer alguma envio para o repositório. Portanto, você seleciona **Gatilho de gancho do GitHub para sondagem GITScm**.

1. Na **seção Criar**, na lista suspensa **Adicionar etapa de compilação**, selecione a opção **Invocar Gradle Script**. No widget que aparecer, abra o menu avançado, especifique o caminho para **Script do build raiz** para o aplicativo. Ele obtém o build.gradle no caminho especificado e funciona de maneira correspondente.

    ![Ação Compilar do Jenkins no Service Fabric](./media/service-fabric-tutorial-java-jenkins/jenkinsbuildscreenshot.png)

1. No menu suspenso **Ações Pós-Compilação**, selecione **Implantar Projeto do Service Fabric**. Aqui, você precisa fornecer detalhes do cluster em que o aplicativo do Service Fabric compilado para o Jenkins deve ser implantado. O caminho para o certificado é onde o volume foi montado (/tmp/myCerts).

    Você também pode fornecer detalhes adicionais usados para implantar o aplicativo. Confira a seguinte captura de tela para obter um exemplo dos detalhes do aplicativo:

    ![Caixa de diálogo Ações pós-compilação com opções adicionais de implantação de projeto do Service Fabric](./media/service-fabric-tutorial-java-jenkins/sfjenkins.png)

    > [!NOTE]
    > Aqui, o cluster pode ser o mesmo que hospeda o aplicativo de contêiner Jenkins caso você esteja usando o Service Fabric para implantar a imagem de contêiner Jenkins.
    >

1. Clique em **Save** (Salvar).

## <a name="update-your-existing-application"></a>Atualizar seu aplicativo existente

1. Atualize o título do HTML no arquivo *VotingApplication/VotingWebPkg/Code/wwwroot/index.html* com **Service Fabric Voting Sample V2**.

    ```html
    <div ng-app="VotingApp" ng-controller="VotingAppController" ng-init="refresh()">
        <div class="container-fluid">
            <div class="row">
                <div class="col-xs-8 col-xs-offset-2 text-center">
                    <h2>Service Fabric Voting Sample V2</h2>
                </div>
            </div>
        </div>
    </div>
    ```

1. Atualize a versão de **ApplicationTypeVersion** e **ServiceManifestVersion** para **2.0.0** no arquivo *Voting/VotingApplication/ApplicationManifest.xml*.

    ```xml
    <?xml version="1.0" encoding="utf-8" standalone="no"?>
    <ApplicationManifest xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="VotingApplicationType" ApplicationTypeVersion="2.0.0">
      <Description>Voting Application</Description>
      <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="VotingWebPkg" ServiceManifestVersion="2.0.0"/>
      </ServiceManifestImport>
      <ServiceManifestImport>
            <ServiceManifestRef ServiceManifestName="VotingDataServicePkg" ServiceManifestVersion="1.0.0"/>
        </ServiceManifestImport>
        <DefaultServices>
          <Service Name="VotingWeb">
             <StatelessService InstanceCount="1" ServiceTypeName="VotingWebType">
                <SingletonPartition/>
             </StatelessService>
          </Service>
       <Service Name="VotingDataService">
                <StatefulService MinReplicaSetSize="3" ServiceTypeName="VotingDataServiceType" TargetReplicaSetSize="3">
                    <UniformInt64Partition HighKey="9223372036854775807" LowKey="-9223372036854775808" PartitionCount="1"/>
                </StatefulService>
            </Service>
        </DefaultServices>
    </ApplicationManifest>
    ```

1. Atualize o campo **Versão** no **ServiceManifest** e o campo **Versão** na marca do **CodePackage** no arquivo *Voting/VotingApplication/VotingWebPkg/ServiceManifest.xml* para **2.0.0**.

    ```xml
    <CodePackage Name="Code" Version="2.0.0">
    <EntryPoint>
        <ExeHost>
        <Program>entryPoint.sh</Program>
        </ExeHost>
    </EntryPoint>
    </CodePackage>
    ```

1. Para inicializar um trabalho do Jenkins que execute uma atualização do aplicativo, envie por push suas novas alterações para o repositório do GitHub.

1. No Service Fabric Explorer, clique na lista suspensa **Aplicativos**. Para ver o status da atualização, clique na guia **Atualizações em Andamento**.

    ![Atualização em andamento](./media/service-fabric-tutorial-create-java-app/upgradejava.png)

1. Se você acessar **http://\<Host-IP>:8080**, verá que o aplicativo Voting com funcionalidade total está agora em execução.

    ![Local do aplicativo de votação](./media/service-fabric-tutorial-java-jenkins/votingv2.png)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Implantar o contêiner Jenkins do Service Fabric no seu computador
> * Configurar o ambiente Jenkins para implantação no Service Fabric
> * Atualizar seu aplicativo

* Confira outros [Exemplos de Java](https://github.com/Azure-Samples/service-fabric-java-getting-started)
