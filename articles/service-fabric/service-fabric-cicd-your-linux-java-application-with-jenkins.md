---
title: "Compilação contínua e integração para o aplicativo Java Linux do Azure Service Fabric usando o Jenkins | Microsoft Docs"
description: "Compilação contínua e integração para o aplicativo Java Linux do Azure Service Fabric usando o Jenkins"
services: service-fabric
documentationcenter: java
author: sayantancs
manager: timlt
editor: 
ms.assetid: 02b51f11-5d78-4c54-bb68-8e128677783e
ms.service: service-fabric
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/23/2017
ms.author: saysa
ms.openlocfilehash: 8ba108ed107e2e023867bcc3b3b1b8cc159377ae
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-jenkins-to-build-and-deploy-your-linux-java-application"></a>Use o Jenkins para criar e implantar o aplicativo Java do Linux
Jenkins é uma ferramenta popular para implantação e integração contínua de seus aplicativos. Veja como criar e implantar o aplicativo do Service Fabric do Azure usando o Jenkins.

## <a name="general-prerequisites"></a>Pré-requisitos gerais
- Ter o Git instalado localmente. Você pode instalar a versão apropriada do Git da [página de downloads do Git](https://git-scm.com/downloads), com base no seu sistema operacional. Se for novo no Git, saiba mais sobre ele na [documentação do Git](https://git-scm.com/docs).
- Ter o plug-in Jenkins do Service Fabric à mão. Você pode baixá-lo de [Downloads do Service Fabric](https://servicefabricdownloads.blob.core.windows.net/jenkins/serviceFabric.hpi).

## <a name="set-up-jenkins-inside-a-service-fabric-cluster"></a>Configurar o Jenkins em um cluster do Service Fabric

Você pode configurar o Jenkins dentro ou fora de um cluster do Service Fabric. As próximas seções mostram como configurá-lo em um cluster usando uma conta de armazenamento do Azure para salvar o estado da instância do contêiner.

### <a name="prerequisites"></a>Pré-requisitos
1. Ter um cluster Linux do Service Fabric pronto. Um cluster do Service Fabric criado no portal do Azure já tem o Docker instalado. Se estiver realizando a execução localmente no cluster, verifique se o Docker está instalado usando o comando ``docker info``. Se não estiver instalado, instale-o adequadamente usando os seguintes comandos:

  ```sh
  sudo apt-get install wget
  wget -qO- https://get.docker.io/ | sh
  ```
2. Ter o aplicativo de contêiner do Service Fabric implantado no cluster, usando as seguintes etapas:

  ```sh
git clone https://github.com/Azure-Samples/service-fabric-java-getting-started.git
cd service-fabric-java-getting-started/Services/JenkinsDocker/
```

3. Você precisa dos detalhes da opção de conexão do compartilhamento de arquivos do armazenamento do Azure no qual deseja persistir o estado da instância do contêiner Jenkins. Se estiver usando o portal do Microsoft Azure para o mesmo, siga as etapas – Criar uma conta de armazenamento do Azure, digamos, ``sfjenkinsstorage1``. Crie um **Compartilhamento de Arquivos** nessa conta de armazenamento, digamos, ``sfjenkins``. Clique em **Conectar** no compartilhamento de arquivos e observe os valores exibidos em **Conectando por meio do Linux**. Digamos que isso seja parecido com o seguinte:
```sh
sudo mount -t cifs //sfjenkinsstorage1.file.core.windows.net/sfjenkins [mount point] -o vers=3.0,username=sfjenkinsstorage1,password=<storage_key>,dir_mode=0777,file_mode=0777
```

> [!NOTE]
> Para montar compartilhamentos de cifs, é necessário ter o pacote cifs-utils instalado nos nós do cluster. 
>

4. Atualize os valores de espaço reservado no script ```setupentrypoint.sh``` com os detalhes correspondentes do armazenamento do Azure.
```sh
vi JenkinsSF/JenkinsOnSF/Code/setupentrypoint.sh
```
Substitua ``[REMOTE_FILE_SHARE_LOCATION]`` pelo valor ``//sfjenkinsstorage1.file.core.windows.net/sfjenkins`` do resultado da conexão no ponto 3 acima.
Substitua ``[FILE_SHARE_CONNECT_OPTIONS_STRING]`` pelo valor ``vers=3.0,username=sfjenkinsstorage1,password=GB2NPUCQY9LDGeG9Bci5dJV91T6SrA7OxrYBUsFHyueR62viMrC6NIzyQLCKNz0o7pepGfGY+vTa9gxzEtfZHw==,dir_mode=0777,file_mode=0777`` no ponto 3 acima.

5. Conecte-se ao cluster e instale o aplicativo contêiner.
```azurecli
sfctl cluster select --endpoint http://PublicIPorFQDN:19080   # cluster connect command
bash Scripts/install.sh
```
Isso instala um contêiner Jenkins no cluster e pode ser monitorado usando o Service Fabric Explorer.

### <a name="steps"></a>Etapas
1. No navegador, acesse ``http://PublicIPorFQDN:8081``. Ela fornece o caminho da senha de administrador inicial necessária para entrar. Você pode continuar a usar o Jenkins como um usuário administrativo. Ou você pode criar e alterar o usuário depois de entrar com a conta do administrador inicial.

   > [!NOTE]
   > Certifique-se de que a porta 8081 está especificada como a porta do ponto de extremidade do aplicativo enquanto você está criando o aplicativo (e a porta está aberta no cluster).
   >

2. Obter a ID de instância do contêiner usando ``docker ps -a``.
3. Entre com SSH (Secure Shell) no contêiner e cole o caminho exibido no portal do Jenkins. Por exemplo, se o portal mostrar o caminho `PATH_TO_INITIAL_ADMIN_PASSWORD`, execute o seguinte:

  ```sh
  docker exec -t -i [first-four-digits-of-container-ID] /bin/bash   # This takes you inside Docker shell
  cat PATH_TO_INITIAL_ADMIN_PASSWORD
  ```

4. Configure o GitHub para trabalhar com o Jenkins, usando as etapas mencionadas em [Gerando uma nova chave SSH e adicionando-a ao agente SSH](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/).
    * Usar as instruções fornecidas pelo GitHub para gerar uma chave SSH e adicionar a chave SSH à conta do GitHub que está hospedando o repositório.
    * Execute os comandos mencionados no link anterior no shell Jenkins Docker (e não no host).
    * Para entrar no shell Jenkins do host, use o seguinte comando:

  ```sh
  docker exec -t -i [first-four-digits-of-container-ID] /bin/bash
  ```

## <a name="set-up-jenkins-outside-a-service-fabric-cluster"></a>Configurar o Jenkins fora de um cluster do Service Fabric

Você pode configurar o Jenkins dentro ou fora de um cluster do Service Fabric. As seções a seguir mostram como configurá-lo fora de um cluster.

### <a name="prerequisites"></a>Pré-requisitos
Você precisa ter o Docker instalado. Os comandos abaixo podem ser usados para instalar o Docker do terminal:

  ```sh
  sudo apt-get install wget
  wget -qO- https://get.docker.io/ | sh
  ```

Agora, ao executar ``docker info`` no terminal, você deve ver a saída executada pelo serviço Docker.

### <a name="steps"></a>Etapas
  1. Baixar a imagem de contêiner Jenkins do Service Fabric:``docker pull raunakpandya/jenkins:v1``
  2. Executar a imagem de contêiner:``docker run -itd -p 8080:8080 raunakpandya/jenkins:v1``
  3. Obter a ID de instância de imagem de contêiner. Você pode listar todos os contêineres do Docker com o comando ``docker ps –a``
  4. Entre no portal do Jenkins usando as seguintes etapas:

    * ```sh
    docker exec [first-four-digits-of-container-ID] cat /var/jenkins_home/secrets/initialAdminPassword
    ```
    Se a ID do contêiner for 2d24a73b5964, use 2d 24.
    * Esta senha é necessária para entrar no painel do Jenkins do portal, que é ``http://<HOST-IP>:8080``
    * Depois de entrar pela primeira vez, você pode criar a própria conta de usuário e usá-la futuramente ou pode continuar a usar a conta de administrador. Após criar um usuário, você precisa continuar com ele.
  5. Configure o GitHub para trabalhar com o Jenkins, usando as etapas mencionadas em [Gerando uma nova chave SSH e adicionando-a ao agente SSH](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/).
        * Usar as instruções fornecidas no GitHub para gerar uma chave SSH e adicionar a chave SSH à conta do GitHub que está hospedando o repositório.
        * Execute os comandos mencionados no link anterior no shell Jenkins Docker (e não no host).
      * Para fazer logon no shell Jenkins do host, use os seguintes comandos:

      ```sh
      docker exec -t -i [first-four-digits-of-container-ID] /bin/bash
      ```

Verifique se o cluster ou a máquina em que a imagem de contêiner Jenkins está hospedada tem um IP público. Isso permite que a instância do Jenkins receba notificações do GitHub.

## <a name="install-the-service-fabric-jenkins-plug-in-from-the-portal"></a>Instalar o plug-in do Jenkins do Service Fabric por meio do portal

1. Acesse ``http://PublicIPorFQDN:8081``
2. No painel do Jenkins, selecione **Gerenciar Jenkins** > **Gerenciar plug-ins** > **Avançado**.
Aqui, você pode carregar um plug-in. Selecione **Escolher arquivo** e selecione o arquivo **serviceFabric.hpi** que você baixou na etapa de pré-requisitos. Quando você seleciona **Carregar**, o Jenkins instala o plug-in automaticamente. Se solicitado, permita a reinicialização.

## <a name="create-and-configure-a-jenkins-job"></a>Criar e configurar um trabalho do Jenkins

1. Criar um **novo item** no painel.
2. Insira um nome de item (por exemplo, **MyJob**). Selecione **projeto em estilo livre**e clique em **OK**.
3. Acesse a página do trabalho e clique em **Configurar**.

   a. Na seção geral, em **Projeto GitHub**, especifique a URL do projeto GitHub. Essa URL hospeda o aplicativo Java do Service Fabric que você deseja integrar à integração contínua do Jenkins, no fluxo de implantação contínua (CI/CD) (por exemplo, ``https://github.com/sayantancs/SFJenkins``).

   b. Na seção **Gerenciamento de Código-Fonte**, selecione **Git**. Especifique a URL do repositório que hospeda o aplicativo Java do Service Fabric que você deseja integrar ao fluxo CI/CD do Jenkins (por exemplo, ``https://github.com/sayantancs/SFJenkins.git``). Você também pode especificar aqui quais ramificações devem ser criadas (por exemplo, **/mestre**).
4. Configure seu *GitHub* (que está hospedando o repositório) para que ele seja capaz de se comunicar com o Jenkins. Use as seguintes etapas:

   a. Vá para sua página do repositório GitHub. Vá para **Configurações** > **Integrações e Serviços**.

   b. Selecione **Adicionar Serviço**, digite **Jenkins** e selecione o **plug-in Jenkins-Github**.

   c. Insira a URL do webhook Jenkins (por padrão, ele deve ser ``http://<PublicIPorFQDN>:8081/github-webhook/``). Clique em **adicionar/atualizar serviço**.

   d. Um evento de teste é enviado para a instância do Jenkins. Você verá uma marca de seleção verde ao lado do webhook no GitHub, e o projeto será criado.

   e. Na seção **Criar Gatilhos**, selecione a opção de compilação desejada. Para este exemplo, você deseja disparar uma compilação sempre que ocorrer alguma envio para o repositório. Portanto, você seleciona **Gatilho de gancho do GitHub para sondagem GITScm**. (Anteriormente, essa opção se chamava **Compilar quando uma alteração for enviada ao GitHub**.)

   f. Na **seção Criar**, na lista suspensa **Adicionar etapa de compilação**, selecione a opção **Invocar Gradle Script**. No widget surgido, especifique o caminho para **Script da build raiz** para o aplicativo. Ele obtém o build.gradle no caminho especificado e funciona de maneira correspondente. Se você criar um projeto chamado ``MyActor``(usando o plug-in Eclipse ou gerador Yeoman), o script da build raiz deverá conter ``${WORKSPACE}/MyActor``. Confira a seguinte captura de tela para obter um exemplo dessa aparência:

    ![Ação Compilar do Jenkins no Service Fabric][build-step]

   g. No menu suspenso **Ações Pós-Compilação**, selecione **Implantar Projeto do Service Fabric**. Aqui, você precisa fornecer detalhes do cluster em que o aplicativo do Service Fabric compilado para o Jenkins deve ser implantado. Você também pode fornecer detalhes adicionais de aplicativos usados para implantar o aplicativo. Confira a seguinte captura de tela para obter um exemplo dessa aparência:

    ![Ação Compilar do Jenkins no Service Fabric][post-build-step]

   > [!NOTE]
   > Aqui, o cluster pode ser o mesmo que hospeda o aplicativo de contêiner Jenkins caso você esteja usando o Service Fabric para implantar a imagem de contêiner Jenkins.
   >

## <a name="next-steps"></a>Próximas etapas
O GitHub e o Jenkins agora estão configurados. Considere fazer algumas alterações de exemplo em seu projeto ``MyActor`` no repositório de exemplo, https://github.com/sayantancs/SFJenkins. Envie por push as alterações à ramificação ``master`` remota (ou a qualquer ramificação que você configurou para o trabalho). Isso dispara o trabalho do Jenkins, ``MyJob``, que você configurou. Busca as alterações do GitHub, compila-as e implanta o aplicativo no ponto de extremidade do cluster especificado nas ações pós-compilação.  

  <!-- Images -->
  [build-step]: ./media/service-fabric-cicd-your-linux-java-application-with-jenkins/build-step.png
  [post-build-step]: ./media/service-fabric-cicd-your-linux-java-application-with-jenkins/post-build-step.png
