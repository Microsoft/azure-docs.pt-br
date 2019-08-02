---
title: Integração e build contínuo para seus aplicativos do Linux do Azure Service Fabric usando o Jenkins | Microsoft Docs
description: Integração e build contínuo para seu aplicativo do Linux do Service Fabric usando o Jenkins
services: service-fabric
documentationcenter: java
author: sayantancs
manager: jpconnock
ms.service: service-fabric
ms.devlang: java
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/31/2018
ms.author: jeconnoc
ms.openlocfilehash: b757a0a5f3ce968b396fa89d5b32c18257d620c3
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2019
ms.locfileid: "67875091"
---
# <a name="use-jenkins-to-build-and-deploy-your-linux-applications"></a>Usar o Jenkins para criar e implantar seus aplicativos do Linux
Jenkins é uma ferramenta popular para implantação e integração contínua de seus aplicativos. Veja como criar e implantar o aplicativo do Service Fabric do Azure usando o Jenkins.

## <a name="topic-overview"></a>Visão geral do tópico
Este artigo aborda várias maneiras de configurar o ambiente do Jenkins e diferentes maneiras de implantar seu aplicativo em um cluster do Service Fabric, depois que ele for criado. Siga estas etapas gerais para instalar o Jenkins, extrair as alterações do GitHub, criar seu aplicativo e implantá-lo no cluster com êxito:

1. Instale os [Pré-requisitos](#prerequisites).
1. Em seguida, siga as etapas em uma destas seções para configurar o Jenkins:
   * [Configurar o Jenkins em um cluster do Service Fabric](#set-up-jenkins-inside-a-service-fabric-cluster), 
   * [Configurar o Jenkins fora de um cluster do Service Fabric](#set-up-jenkins-outside-a-service-fabric-cluster) ou
   * [Instalar o plug-in do Service Fabric em um ambiente do Jenkins existente](#install-service-fabric-plugin-in-an-existing-jenkins-environment).
1. Depois de configurar o Jenkins, siga as etapas em [Criar e configurar um trabalho do Jenkins](#create-and-configure-a-jenkins-job) a fim de configurar o GitHub para disparar o Jenkins quando forem feitas alterações ao seu aplicativo e para configurar o pipeline de trabalho do Jenkins usando a etapa de compilação para extrair as alterações do GitHub e criar seu aplicativo. 
1. Por fim, configure a etapa de pós-compilação do trabalho do Jenkins para implantar seu aplicativo no cluster do Service Fabric. Há duas maneiras de configurar o Jenkins para implantar seu aplicativo em um cluster:    
   * No caso de ambientes de desenvolvimento e teste, use [Configurar a implantação usando o ponto de extremidade de gerenciamento do cluster](#configure-deployment-using-cluster-management-endpoint). Esse é o método de implantação mais simples de configurar.
   * No caso de ambientes de produção, use [Configurar a implantação usando as credenciais do Azure](#configure-deployment-using-azure-credentials). A Microsoft recomenda esse método para ambientes de produção porque, com as credenciais do Azure, você pode limitar o acesso de um trabalho do Jenkins aos recursos do Azure. 

## <a name="prerequisites"></a>pré-requisitos

- Instale o Git localmente. Você pode instalar a versão apropriada do Git da [página de downloads do Git](https://git-scm.com/downloads), com base no seu sistema operacional. Se o Git for novidade para você, saiba mais sobre ele na [documentação do Git](https://git-scm.com/docs).
- Este artigo usa o *exemplo da Introdução ao Service Fabric* no GitHub: [ https://github.com/Azure-Samples/service-fabric-java-getting-started ](https://github.com/Azure-Samples/service-fabric-java-getting-started) para compilar e implantar o aplicativo. Você pode bifurcar esse repositório para acompanhar ou, com algumas modificações nas instruções, usar seu próprio projeto do GitHub.


## <a name="install-service-fabric-plugin-in-an-existing-jenkins-environment"></a>Instalar o plug-in do Service Fabric em um ambiente do Jenkins existente
Se você vai adicionar o plug-in do Service Fabric a um ambiente do Jenkins existente, precisará do seguinte:

- A [CLI do Service Fabric](service-fabric-cli.md) (sfctl).

   > [!NOTE]
   > Instale a CLI no nível do sistema, em vez de no nível do usuário, para que o Jenkins possa executar comandos da CLI. 
   >

- Para implantar aplicativos Java, instale [o Gradle e o Open JDK 8.0](service-fabric-get-started-linux.md#set-up-java-development). 
- Para implantar aplicativos do .NET Core 2.0, instale o [SDK do .NET Core 2.0](service-fabric-get-started-linux.md#set-up-net-core-20-development). 

Depois de instalar os pré-requisitos necessários para seu ambiente, procure o plug-in do Azure Service Fabric no marketplace do Jenkins e instale-o.

Depois de instalar o plug-in, vá para [Criar e configurar um trabalho do Jenkins](#create-and-configure-a-jenkins-job).


## <a name="set-up-jenkins-inside-a-service-fabric-cluster"></a>Configurar o Jenkins em um cluster do Service Fabric

Você pode configurar o Jenkins dentro ou fora de um cluster do Service Fabric. As próximas seções mostram como configurá-lo em um cluster usando uma conta de armazenamento do Azure para salvar o estado da instância do contêiner.

### <a name="prerequisites"></a>pré-requisitos
- Instalar um cluster Linux do Service Fabric com o Docker instalado. Os clusters do Service Fabric em execução no Azure já têm o Docker instalado. Se você estiver executando o cluster localmente (ambiente de desenvolvimento OneBox), verifique se o Docker está instalado em seu computador com o comando `docker info`. Se não estiver instalado, instale-o usando os seguintes comandos:

   ```sh
   sudo apt-get install wget
   wget -qO- https://get.docker.io/ | sh
   ``` 

   > [!NOTE]
   > Verifique se a porta 8081 foi especificada como um ponto de extremidade personalizado no cluster. Se você estiver usando um cluster local, verifique se a porta 8081 está aberta no computador host e se ele tem um endereço IP público.
   >

### <a name="steps"></a>Etapas
1. Clone o aplicativo usando os seguintes comandos:
   ```sh
   git clone https://github.com/suhuruli/jenkins-container-application.git
   cd jenkins-container-application
   ```

1. Mantenha o estado do contêiner Jenkins em um compartilhamento de arquivos:
   1. Crie uma conta de armazenamento do Azure na **mesma região** do que o seu cluster com um nome como `sfjenkinsstorage1`.
   1. Crie um **Compartilhamento de Arquivos** na Conta de Armazenamento com um nome como `sfjenkins`.
   1. Clique em **Conectar** no compartilhamento de arquivos e observe os valores exibidos em **Conectando por meio do Linux**, o valor deve ser semelhantes a este:

      ```sh
      sudo mount -t cifs //sfjenkinsstorage1.file.core.windows.net/sfjenkins [mount point] -o vers=3.0,username=sfjenkinsstorage1,password=<storage_key>,dir_mode=0777,file_mode=0777
      ```

   > [!NOTE]
   > Para montar compartilhamentos de cifs, é necessário ter o pacote cifs-utils instalado nos nós do cluster.      
   >

1. Atualize os valores de espaço reservado no script `setupentrypoint.sh` com os detalhes do armazenamento do Azure da etapa 2.
   ```sh
   vi JenkinsSF/JenkinsOnSF/Code/setupentrypoint.sh
   ```
   * Substitua `[REMOTE_FILE_SHARE_LOCATION]` pelo valor `//sfjenkinsstorage1.file.core.windows.net/sfjenkins` do resultado da conexão da etapa 2 acima.
   * Substitua `[FILE_SHARE_CONNECT_OPTIONS_STRING]` pelo valor `vers=3.0,username=sfjenkinsstorage1,password=GB2NPUCQY9LDGeG9Bci5dJV91T6SrA7OxrYBUsFHyueR62viMrC6NIzyQLCKNz0o7pepGfGY+vTa9gxzEtfZHw==,dir_mode=0777,file_mode=0777` da etapa 2 acima.

1. **Somente cluster seguro:** 
   
   Para configurar a implantação de aplicativos em um cluster seguro do Jenkins, o certificado deve ser acessível dentro do contêiner do Jenkins. No arquivo *ApplicationManifest.xml*, na marca **ContainerHostPolicies**, adicione essa referência de certificado e atualize o valor da impressão digital com o do certificado de cluster.

   ```xml
   <CertificateRef Name="MyCert" X509FindValue="[Thumbprint]"/>
   ```

   Além disso, adicione as seguintes linhas na marca (raiz) **ApplicationManifest** no arquivo *ApplicationManifest.xml* e atualize o valor de impressão digital com o do certificado de cluster.

   ```xml
   <Certificates>
     <SecretsCertificate X509FindType="FindByThumbprint" X509FindValue="[Thumbprint]" />
   </Certificates> 
   ```

1. Conecte-se ao cluster e instale o aplicativo contêiner.

   **Cluster seguro**
   ```sh
   sfctl cluster select --endpoint https://PublicIPorFQDN:19080  --pem [Pem] --no-verify # cluster connect command
   bash Scripts/install.sh
   ```
   O comando anterior usa o certificado no formato PEM. Se o certificado estiver no formato PFX, use o comando a seguir para convertê-lo. Se o arquivo PFX não estiver protegido por senha, especifique o parâmetro **passin** como `-passin pass:`.
   ```sh
   openssl pkcs12 -in cert.pfx -out cert.pem -nodes -passin pass:MyPassword1234!
   ``` 
   
   **Cluster não seguro**
   ```sh
   sfctl cluster select --endpoint http://PublicIPorFQDN:19080 # cluster connect command
   bash Scripts/install.sh
   ```

   Isso instala um contêiner Jenkins no cluster e pode ser monitorado usando o Service Fabric Explorer.

   > [!NOTE]
   > O download da imagem do Jenkins no cluster pode demorar alguns minutos.
   >

1. No navegador, acesse `http://PublicIPorFQDN:8081`. Ela fornece o caminho da senha de administrador inicial necessária para entrar. 
1. Examine o Service Fabric Explorer para determinar em qual nó o contêiner Jenkins está em execução. Entre com SSH (Secure Shell) neste nó.
   ```sh
   ssh user@PublicIPorFQDN -p [port]
   ``` 
1. Obter a ID de instância do contêiner usando `docker ps -a`.
1. Entre com SSH (Secure Shell) no contêiner e cole o caminho exibido no portal do Jenkins. Por exemplo, se o portal mostrar o caminho `PATH_TO_INITIAL_ADMIN_PASSWORD`, execute os seguintes comandos:

   ```sh
   docker exec -t -i [first-four-digits-of-container-ID] /bin/bash   # This takes you inside Docker shell
   ```
   ```sh
   cat PATH_TO_INITIAL_ADMIN_PASSWORD # This displays the password value
   ```
1. Na página Introdução ao Jenkins, escolha a opção Selecionar plug-ins para instalação, marque a caixa de seleção **Nenhum** e clique em instalar.
1. Crie um usuário ou selecione para continuar como um administrador.

Depois de configurar o Jenkins, vá para [Criar e configurar um trabalho do Jenkins](#create-and-configure-a-jenkins-job).  

## <a name="set-up-jenkins-outside-a-service-fabric-cluster"></a>Configurar o Jenkins fora de um cluster do Service Fabric

Você pode configurar o Jenkins dentro ou fora de um cluster do Service Fabric. As seções a seguir mostram como configurá-lo fora de um cluster.

### <a name="prerequisites"></a>pré-requisitos
- Verifique se o Docker está instalado no computador. Os comandos abaixo podem ser usados para instalar o Docker do terminal:

  ```sh
  sudo apt-get install wget
  wget -qO- https://get.docker.io/ | sh
  ```

  Ao executar `docker info` no terminal, você deverá ver a saída executada pelo serviço Docker.

### <a name="steps"></a>Etapas
1. Baixar a imagem de contêiner Jenkins do Service Fabric: `docker pull rapatchi/jenkins:latest`. Esta imagem é fornecida com o plug-in do Service Fabric Jenkins pré-instalado.
1. Executar a imagem de contêiner:`docker run -itd -p 8080:8080 rapatchi/jenkins:latest`
1. Obter a ID de instância de imagem de contêiner. Você pode listar todos os contêineres do Docker com o comando `docker ps –a`
1. Entre no portal do Jenkins usando as seguintes etapas:

   1. Entrar em um shell Jenkins no seu host. Use os quatro primeiros dígitos da ID do contêiner. Por exemplo, se a ID do contêiner for `2d24a73b5964`, use `2d24`.

      ```sh
      docker exec -it [first-four-digits-of-container-ID] /bin/bash
      ```
   1. No shell Jenkins, obtenha a senha de administrador para a instância do contêiner:

      ```sh
      cat /var/jenkins_home/secrets/initialAdminPassword
      ```      
   1. Para entrar no painel do Jenkins, abra a seguinte URL em um navegador: `http://<HOST-IP>:8080`. Use a senha da etapa anterior para desbloquear o Jenkins.
   1. (Opcional). Depois de entrar pela primeira vez, você pode criar sua própria conta de usuário e usá-la futuramente ou pode continuar a usar a conta de administrador. Se você criar um usuário, precisará continuar com ele.
1. Configure o GitHub para trabalhar com o Jenkins usando as etapas em [Gerando uma nova chave SSH e adicionando-a ao agente SSH](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/).
   * Usar as instruções fornecidas no GitHub para gerar uma chave SSH e adicionar a chave SSH à conta do GitHub que está hospedando o repositório.
   * Execute os comandos mencionados no link anterior no shell Jenkins Docker (e não no host).
   * Para entrar no shell Jenkins do host, use o seguinte comando:

      ```sh
      docker exec -t -i [first-four-digits-of-container-ID] /bin/bash
      ```

Verifique se o cluster ou a máquina em que a imagem de contêiner Jenkins está hospedada tem um endereço IP público. Isso permite que a instância do Jenkins receba notificações do GitHub.

Depois de configurar o Jenkins, prossiga para a próxima seção, [Criar e configurar um trabalho do Jenkins](#create-and-configure-a-jenkins-job).

## <a name="create-and-configure-a-jenkins-job"></a>Criar e configurar um trabalho do Jenkins

As etapas nesta seção mostram como configurar um trabalho do Jenkins para responder a alterações em um repositório GitHub, como buscar essas alterações e como criá-las. No final desta seção, você será direcionado para as etapas finais a fim de configurar o trabalho para implantar no aplicativo de acordo com a natureza do ambiente, se desenvolvimento/teste ou produção. 

1. No painel do Jenkins, clique em **New Item**.
1. Insira um nome de item (por exemplo, **MyJob**). Selecione **projeto em estilo livre**e clique em **OK**.
1. A página de configuração do trabalho é aberta. (Para obter a configuração do painel do Jenkins, clique no trabalho e em **Configurar**.)

1. Na guia **Geral**, marque a caixa do **projeto GitHub** e especifique a URL do projeto GitHub. Essa URL hospeda o aplicativo Java do Service Fabric que você deseja integrar à integração contínua do Jenkins, no fluxo de implantação contínua (CI/CD) (por exemplo, `https://github.com/{your-github-account}/service-fabric-java-getting-started`).

1. Na guia **Gerenciamento de Código-Fonte**, selecione **Git**. Especifique a URL do repositório que hospeda o aplicativo Java do Service Fabric que você deseja integrar ao fluxo CI/CD do Jenkins (por exemplo, `https://github.com/{your-github-account}/service-fabric-java-getting-started`). Você também pode especificar qual ramificação criar (por exemplo, `/master`).
1. Configure o repositório *GitHub* para falar com o Jenkins:

   a. Na página repositório GitHub, vá para **Configurações** > **Integrações e Serviços**.

   b. Selecione **Adicionar Serviço**, digite **Jenkins** e selecione o **plug-in Jenkins-Github**.

   c. Insira a URL do webhook Jenkins (por padrão, ele deve ser `http://<PublicIPorFQDN>:8081/github-webhook/`). Clique em **adicionar/atualizar serviço**.

   d. Um evento de teste é enviado para a instância do Jenkins. Você verá uma marca de seleção verde ao lado do webhook no GitHub, e o projeto será criado.

1. Na guia **Build Triggers** do Jenkins, selecione a opção de compilação desejada. Neste exemplo, você deseja disparar uma build sempre que ocorrer um envio por push para o repositório; para isso, selecione **Gatilho de gancho GitHub para sondagem GITScm**. (Anteriormente, essa opção se chamava **Compilar quando uma alteração for enviada ao GitHub**.)
1. Na guia **Criar**, siga um destes procedimentos, dependendo de estar criando um aplicativo Java ou um aplicativo .NET Core:

   * **Para aplicativos Java:** na lista suspensa **Adicionar etapa de compilação**, selecione **Invocar script Gradle**. Clique em **Avançado**. No menu avançado, especifique o caminho do **script de compilação da raiz** para o aplicativo. Ele obtém o build.gradle no caminho especificado e funciona de maneira correspondente. Para o [aplicativo ActorCounter](https://github.com/Azure-Samples/service-fabric-java-getting-started/tree/master/reliable-services-actor-sample/Actors/ActorCounter), é: `${WORKSPACE}/reliable-services-actor-sample/Actors/ActorCounter`.

     ![Ação Compilar do Jenkins no Service Fabric][build-step]

   * **Para aplicativos .NET Core:** na lista suspensa **Adicionar etapa de compilação**, selecione **Executar Shell**. Na caixa de comando exibida, o diretório precisa primeiro ser alterado para o caminho em que o arquivo build.sh está localizado. Quando o diretório for alterado, o script build.sh poderá ser executado e criará o aplicativo.

      ```sh
      cd /var/jenkins_home/workspace/[Job Name]/[Path to build.sh]  # change directory to location of build.sh file
      ./build.sh
      ```

     As capturas de tela abaixo mostram um exemplo dos comandos usados para criar o exemplo de [Serviço de Contador](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started/tree/master/Services/CounterService) com o nome de trabalho do Jenkins CounterServiceApplication.

      ![Ação Compilar do Jenkins no Service Fabric][build-step-dotnet]

1. Para configurar o Jenkins para implantar seu aplicativo em um cluster do Service Fabric nas ações pós-compilação, é necessário saber o local do certificado do cluster em seu contêiner do Jenkins. Escolha um dos seguintes, dependendo de o contêiner Jenkins estar em execução dentro ou fora do seu cluster e de acordo com o local do certificado de cluster:

   * **Para o Jenkins em execução dentro de seu cluster:** O caminho para o certificado pode ser encontrado fazendo-se o eco do valor da variável de ambiente *Certificates_JenkinsOnSF_Code_MyCert_PEM* de dentro do contêiner.

      ```sh
      echo $Certificates_JenkinsOnSF_Code_MyCert_PEM
      ```
   
   * **Para o Jenkins em execução fora de seu cluster:** siga estas etapas para copiar o certificado de cluster para o contêiner:
     1. O certificado deve estar no formato PEM. Se você não tiver um arquivo PEM, poderá criar um do arquivo PFX de certificado. Se o arquivo PFX não estiver protegido por senha, execute o seguinte comando no seu host:

        ```sh
        openssl pkcs12 -in clustercert.pfx -out clustercert.pem -nodes -passin pass:
        ``` 

        Se o arquivo PFX estiver protegido por senha, inclua a senha no parâmetro `-passin`. Por exemplo:

        ```sh
        openssl pkcs12 -in clustercert.pfx -out clustercert.pem -nodes -passin pass:MyPassword1234!
        ``` 

     1. para obter a ID do contêiner para seu contêiner do Jenkins, execute `docker ps` no host.
     1. Copie o arquivo PEM no seu contêiner com o seguinte comando do Docker:
    
        ```sh
        docker cp clustercert.pem [first-four-digits-of-container-ID]:/var/jenkins_home
        ``` 

Está quase concluído! Mantenha o trabalho do Jenkins aberto. A única tarefa restante é configurar as etapas de pós-compilação para implantar seu aplicativo no cluster do Service Fabric:

* Para implantar em um ambiente de desenvolvimento ou teste, siga as etapas em [Configurar a implantação usando o ponto de extremidade de gerenciamento do cluster](#configure-deployment-using-cluster-management-endpoint).
* Para implantar em um ambiente de produção, siga as etapas em [Configurar a implantação usando as credenciais do Azure](#configure-deployment-using-azure-credentials).

## <a name="configure-deployment-using-cluster-management-endpoint"></a>Configurar a implantação usando o ponto de extremidade de gerenciamento do cluster
Para ambientes de desenvolvimento e teste, você pode usar o ponto de extremidade de gerenciamento do cluster para implantar o aplicativo. Configurar a ação de pós-compilação com o ponto de extremidade de gerenciamento do cluster para implantar o aplicativo exige o mínimo de configuração. Se você estiver implantando em um ambiente de produção, vá para [Configurar a implantação usando as credenciais do Azure](#configure-deployment-using-azure-credentials) a fim de configurar uma entidade de serviço do Azure Active Directory a ser usada durante a implantação.    

1. No trabalho do Jenkins, clique na guia **Ações de Pós-compilação**. 
1. No menu suspenso **Ações Pós-Compilação**, selecione **Implantar Projeto do Service Fabric**. 
1. Em **Configuração de Cluster do Service Fabric**, selecione o botão de opção **Preencher o Ponto de Extremidade de Gerenciamento do Service Fabric**.
1. Em **Host de Gerenciamento**, insira o ponto de extremidade de conexão do seu cluster; por exemplo, `{your-cluster}.eastus.cloudapp.azure.com`.
1. Em **Chave do Cliente** e **Certificado de Cliente**, insira o local do arquivo PEM em seu contêiner do Jenkins; por exemplo, `/var/jenkins_home/clustercert.pem`. (Você copiou o local do certificado na última etapa de [Criar e configurar um trabalho do Jenkins](#create-and-configure-a-jenkins-job).)
1. Em **Configuração de Aplicativo**, configure os campos **Nome do Aplicativo**, **Tipo de Aplicativo** e **Caminho para o Manifesto do Aplicativo** (relativo).

   ![Ação de pós-compilação do Service Fabric – configurar ponto de extremidade de gerenciamento](./media/service-fabric-cicd-your-linux-application-with-jenkins/post-build-endpoint.png)

1. Clique em **Verificar Configuração**. Quando a verificação tiver êxito, clique em **Salvar**. O pipeline de trabalho do Jenkins agora está totalmente configurado. Vá para [Próximas etapas](#next-steps) a fim de testar a implantação.

## <a name="configure-deployment-using-azure-credentials"></a>Configurar a implantação usando as credenciais do Azure
Para ambientes de produção, é recomendado configurar uma credencial do Azure para implantar seu aplicativo. Esta seção mostra como configurar uma entidade de serviço do Azure Active Directory para usar na implantação do aplicativo na ação de pós-compilação. Você pode atribuir entidades de serviço a funções em seu diretório para limitar as permissões do trabalho do Jenkins. 

Para ambientes de desenvolvimento e teste, você pode configurar as credenciais do Azure ou o ponto de extremidade de gerenciamento do cluster para implantar seu aplicativo. Para obter detalhes sobre como configurar um ponto de extremidade de gerenciamento do cluster, confira [Configurar a implantação usando o ponto de extremidade de gerenciamento do cluster](#configure-deployment-using-cluster-management-endpoint).   

1. Para criar uma entidade de serviço do Azure Active Directory e atribuir a ela permissões em sua assinatura do Azure, siga as etapas em [Usar o portal para criar um aplicativo e uma entidade de serviço do Azure Active Directory](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal). Preste atenção ao seguinte:

   * Ao seguir as etapas no tópico, certifique-se de copiar e salvar os valores a seguir: *ID do aplicativo*, *Chave do aplicativo*, *ID do diretório (ID do locatário)* e *ID da assinatura*. Necessário para configurar as credenciais do Azure no Jenkins.
   * Se você não tiver as [permissões necessárias](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#required-permissions) em seu diretório, precisará pedir ao administrador para conceder as permissões ou criar a entidade de serviço, ou precisará configurar o ponto de extremidade de gerenciamento para o cluster nas **Ações de Pós-Compilação** relativo ao seu trabalho no Jenkins.
   * Na seção [Criar um aplicativo do Azure Active Directory](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#create-an-azure-active-directory-application), você pode inserir qualquer URL adequada em **URL de logon**.
   * Na seção [Atribuir aplicativo a uma Função](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal), você pode atribuir ao aplicativo a função de *Leitor* no grupo de recursos para o cluster.

1. De volta ao trabalho do Jenkins, clique na guia **Ações de Pós-compilação**.
1. No menu suspenso **Ações Pós-Compilação**, selecione **Implantar Projeto do Service Fabric**. 
1. Em **Configuração de Cluster do Service Fabric**, selecione o botão de opção **Selecionar o Cluster do Service Fabric**. Clique em **Adicionar** lado das **Credenciais do Azure**. Clique em **Jenkins** para selecionar o provedor de credenciais do Jenkins.
1. No provedor de credenciais do Jenkins, selecione **Entidade de Serviço do Microsoft Azure** no menu suspenso **Tipo**.
1. Use os valores que você salvou ao definir a entidade de serviço na etapa 1 para definir os seguintes campos:

   * **ID do cliente**: *ID do aplicativo*
   * **Segredo do cliente**: *Chave do aplicativo*
   * **ID do locatário**: *ID do Diretório*
   * **ID da assinatura**: *ID da assinatura*
1. Insira uma **ID** descritiva, que será usada para selecionar a credencial do Jenkins, e uma breve **Descrição**. Em seguida, clique em **Verificar entidade de serviço**. Se a verificação for bem-sucedida, clique em **Adicionar**.

   ![Jenkins do Service Fabric inserir credenciais do Azure](./media/service-fabric-cicd-your-linux-application-with-jenkins/enter-azure-credentials.png)
1. De volta à opção **Configuração de Cluster do Service Fabric**, verifique se a nova credencial está selecionada em **Credenciais do Azure**. 
1. Na lista suspensa **Grupo de Recursos**, selecione o grupo de recursos do cluster em que você deseja implantar o aplicativo.
1. Na lista suspensa **Service Fabric**, selecione o cluster em que você deseja implantar o aplicativo.
1. Em **Chave do Cliente** e **Certificado de Cliente**, insira o local do arquivo PEM em seu contêiner do Jenkins. Por exemplo, `/var/jenkins_home/clustercert.pem`. 
1. Em **Configuração de Aplicativo**, configure os campos **Nome do Aplicativo**, **Tipo de Aplicativo** e **Caminho para o Manifesto do Aplicativo** (relativo).
    ![Ação de pós-compilação do Jenkins no Service Fabric – configurar as credenciais do Azure](./media/service-fabric-cicd-your-linux-application-with-jenkins/post-build-credentials.png)
1. Clique em **Verificar Configuração**. Quando a verificação tiver êxito, clique em **Salvar**. O pipeline de trabalho do Jenkins agora está totalmente configurado. Continue nas [Próximas etapas](#next-steps) a fim de testar a implantação.

## <a name="troubleshooting-the-jenkins-plugin"></a>O plug-in do Jenkins de solução de problemas

Se você encontrar bugs com os plug-ins do Jenkins, registre um problema no [JIRA do Jenkins](https://issues.jenkins-ci.org/) para o componente específico.

## <a name="next-steps"></a>Próximas etapas
O GitHub e o Jenkins agora estão configurados. Considere fazer alguma alteração de exemplo no projeto `reliable-services-actor-sample/Actors/ActorCounter` na sua bifurcação do repositório, https://github.com/Azure-Samples/service-fabric-java-getting-started. Envie as alterações à ramificação `master` remota (ou a qualquer ramificação configurada para o trabalho) por push. Isso dispara o trabalho do Jenkins, `MyJob`, que você configurou. Ele busca as alterações do GitHub, compila-as e implanta o aplicativo no cluster especificado nas ações pós-compilação.  

  <!-- Images -->
  [build-step]: ./media/service-fabric-cicd-your-linux-application-with-jenkins/build-step.png
  [build-step-dotnet]: ./media/service-fabric-cicd-your-linux-application-with-jenkins/build-step-dotnet.png

