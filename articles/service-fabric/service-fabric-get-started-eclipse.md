---
title: Plug-in do Service Fabric do Azure para Eclipse | Microsoft Docs
description: Comece a usar o plug-in do Service Fabric para Eclipse.
services: service-fabric
documentationcenter: java
author: sayantancs
manager: timlt
editor: 
ms.assetid: bf84458f-4b87-4de1-9844-19909e368deb
ms.service: service-fabric
ms.devlang: java
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/21/2016
ms.author: saysa
ms.openlocfilehash: 4fa77da8665908553072792d7f2ede47bf5567dd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="service-fabric-plug-in-for-eclipse-java-application-development"></a>Plug-in do Service Fabric para o desenvolvimento de aplicativos Eclipse Java
O Eclipse é um dos IDEs (ambientes de desenvolvimento integrado) mais amplamente usados para desenvolvedores de Java. Neste artigo, descreveremos como configurar seu ambiente de desenvolvimento do Eclipse para trabalhar com o Azure Service Fabric. Saiba como instalar o plug-in do Service Fabric, criar um aplicativo do Service Fabric e implantar o aplicativo do Service Fabric em um cluster do Service Fabric local ou remoto no Eclipse Neon.

## <a name="install-or-update-the-service-fabric-plug-in-in-eclipse-neon"></a>Instalar ou atualizar o plug-in do Service Fabric no Eclipse Neon
Você pode instalar um plug-in do Service Fabric no Eclipse. O plug-in pode ajudar a simplificar o processo de criação e implantação de serviços Java.

1.  Verifique se você tem a versão mais recente do Eclipse Neon e a versão mais recente do Buildship (1.0.17 ou uma versão posterior) instalado:
    -   Para verificar as versões dos componentes instalados, no Eclipse Neon, acesse **Ajuda** > **Detalhes da Instalação**.
    -   Para atualizar o Buildship, confira [Eclipse Buildship: plug-ins do Eclipse para Gradle][buildship-update].
    -   Para verificar e instalar atualizações para o Eclipse Neon, acesse **Ajuda** > **Verificar se Há Atualizações**.

2.  Para instalar o plug-in do Service Fabric, no Eclipse Neon, acesse **Ajuda** > **Instalar Novo Software**.
  1.    Na caixa de texto **Trabalhar com**, digite: **http://dl.microsoft.com/eclipse**.
  2.    Clique em **Adicionar**.

         ![Plug-in do Service Fabric para Eclipse Neon][sf-eclipse-plugin-install]
  3.    Selecione o plug-in do Service Fabric e clique em **Avançar**.
  4.    Conclua as etapas de instalação e aceite os Termos de Licença de Software da Microsoft.

Se já tiver o plug-in do Service Fabric instalado, verifique se você tem a versão mais recente. Para verificar se há atualizações disponíveis, acesse **Ajuda** > **Detalhes da Instalação**. Na lista de plug-ins instalados, selecione o Service Fabric e clique em **Atualizar**. As atualizações disponíveis serão instaladas.

> [!NOTE]
> Se a instalação ou atualização do plug-in do Service Fabric estiver lenta, isso poderá ser devido a uma configuração do Eclipse. O Eclipse coleta metadados sobre todas as alterações para atualizar sites que estão registrados com a instância do Eclipse. Para acelerar o processo de verificar e instalar uma atualização de plug-in do Service Fabric, acesse **Sites de Software Disponível**. Desmarque as caixas de seleção de todos os sites, exceto aquele que aponta para o local do plug-in do Service Fabric (http://dl.microsoft.com/eclipse/azure/servicefabric).

> [!NOTE]
>Se o Eclipse não está funcionando conforme o esperado no seu Mac ou você precisa executar como superusuário), vá para a pasta **ECLIPSE_INSTALLATION_PATH** e navegue até a subpasta **Eclipse.app/Contents/MacOS**. Inicie o Eclipse executando `./eclipse`.


## <a name="create-a-service-fabric-application-in-eclipse"></a>Criar um aplicativo do Service Fabric no Eclipse

1.  No Eclipse Neon, acesse **Arquivo** > **Novo** > **Outros**. Selecione **Projeto do Service Fabric** e clique em **Avançar**.

    ![Novo Projeto do Service Fabric página 1][create-application/p1]

2.  Insira um nome para o projeto e clique em **Avançar**.

    ![Novo Projeto do Service Fabric página 2][create-application/p2]

3.  Na lista de modelos, selecione **Modelo de Serviço**. Selecione o tipo de modelo de serviço (Ator, Sem Monitoração de Estado, Contêiner ou Convidado Binário) e clique em **Avançar**.

    ![Novo Projeto do Service Fabric página 3][create-application/p3]

4.  Insira os detalhes e o nome do serviço e clique em **Concluir**.

    ![Novo Projeto do Service Fabric página 4][create-application/p4]

5. Ao criar seu primeiro projeto do Service Fabric, na caixa de diálogo **Abrir Perspectiva Associada**, clique em **Sim**.

    ![Novo Projeto do Service Fabric página 5][create-application/p5]

6.  O novo projeto tem esta aparência:

    ![Novo Projeto do Service Fabric página 6][create-application/p6]

## <a name="build-and-deploy-a-service-fabric-application-in-eclipse"></a>Criar e implantar um aplicativo do Service Fabric no Eclipse

1.  Clique com o botão direito do mouse no novo aplicativo do Service Fabric e selecione **Service Fabric**.

    ![Menu de atalho do Service Fabric][publish/RightClick]

2. No submenu, selecione a opção desejada:
    -   Para compilar o aplicativo sem limpeza, clique em **Compilar Aplicativo**.
    -   Para fazer uma compilação limpa do aplicativo, clique em **Recompilar o Aplicativo**.
    -   Para limpar a aplicação de artefatos compilados, clique em **Limpar Aplicativo**.

3.  Nesse menu, você também pode implantar, desimplantar e publicar o aplicativo:
    -   Para implantar no cluster local, clique em **Implantar Aplicativo**.
    -   Na caixa de diálogo **Publicar Aplicativo**, selecione um perfil de publicação:
        -  **Local.json**
        -  **Cloud.json**

     Esses arquivos JSON (JavaScript Object Notation) armazenam informações (como pontos de extremidade de conexão e informações de segurança) que são necessárias para se conectar ao cluster local ou de nuvem (Azure).

  ![Menu Publicar do Service Fabric][publish/Publish]

Uma maneira alternativa de implantar o aplicativo do Service Fabric é usar as configurações de execução do Eclipse.

  1.    Acesse **Executar** > **Configurações de Execução**.
  2.    Em **Projeto do Gradle**, selecione a configuração de execução **ServiceFabricDeployer**.
  3.    No painel direito, na guia **Argumentos**, para **publishProfile**, selecione **local** ou **nuvem**.  O padrão é **local**. Para implantar em um computador remoto ou cluster de nuvem, selecione **nuvem**.
  4.    Para garantir que as informações adequadas sejam preenchidas nos perfis de publicação, edite **Local.json** ou **Cloud.json** conforme necessário. Você pode adicionar ou atualizar detalhes de ponto de extremidade e credenciais de segurança.
  5.    Verifique se **Diretório de Trabalho** aponta para o aplicativo que você deseja implantar. Para alterar o aplicativo, clique no botão **Espaço de trabalho** e selecione o aplicativo desejado.
  6.    Clique em **Aplicar** e em **Executar**.

Seu aplicativo será compilado e implantado em poucos instantes. Você pode monitorar o status da implantação no Service Fabric Explorer.  

## <a name="add-a-service-fabric-service-to-your-service-fabric-application"></a>Adicionar um serviço do Service Fabric ao aplicativo do Service Fabric

Para adicionar um serviço do Service Fabric a um aplicativo do Service Fabric existente, siga estas etapas:

1.  Clique com o botão direito do mouse no projeto ao qual você deseja adicionar um serviço e clique no **Service Fabric**.

    ![Adicionar Serviço do Service Fabric Página 1][add-service/p1]

2.  Clique em **Adicionar Serviço do Service Fabric**e conclua o conjunto de etapas para adicionar um serviço ao projeto.
3.  Selecione o modelo de serviço que você deseja adicionar ao projeto e clique em **Avançar**.

    ![Adicionar Serviço do Service Fabric Página 2][add-service/p2]

4.  Insira o nome do serviço (e outros detalhes, conforme necessário) e clique no botão **Adicionar Serviço**.  

    ![Adicionar Serviço do Service Fabric Página 3][add-service/p3]

5.  Depois que o serviço é adicionado, a estrutura geral do projeto é semelhante ao seguinte projeto:

    ![Adicionar Serviço do Service Fabric Página 4][add-service/p4]

## <a name="edit-manifest-versions-of-your-service-fabric-java-application"></a>Editar versões de manifesto do aplicativo Java do Service Fabric

Para editar versões de manifesto, clique o botão direito no projeto, acesse **Service Fabric** e selecione **Editar versões de manifesto...**  do menu suspenso. No assistente, você pode atualizar as versões de manifesto para o manifesto do aplicativo, o manifesto do serviço e as versões para o **código**, **Config** e pacotes de **dados**.

Se você marcar a opção **atualizar automaticamente o aplicativo e as versões de serviço** e, em seguida, atualizar uma versão, as versões de manifesto serão atualizadas automaticamente. Para dar um exemplo, primeiro selecione a caixa de seleção, e atualize a versão do **código** de 0.0.0 para 0.0.1 e clique em **Concluir**, em seguida, a versão do manifesto do serviço e a versão do manifesto do aplicativo serão atualizadas automaticamente para 0.0.1.

## <a name="upgrade-your-service-fabric-java-application"></a>Atualizar seu aplicativo Java do Service Fabric

Para um cenário de atualização, digamos que você tenha criado o projeto **App1** usando o plug-in do Service Fabric do Eclipse. Você o implantou usando o plug-in para criar um aplicativo chamado **fabric:/App1Application**. O tipo de aplicativo é **App1AppicationType** e a versão do aplicativo é 1.0. Agora, você deseja atualizar o aplicativo sem interromper a disponibilidade.

Primeiro, faça as alterações no aplicativo e recrie o serviço modificado. Atualize o arquivo de manifesto do serviço modificado (ServiceManifest.xml) com as versões atualizadas para o serviço (e Código, Configuração ou Dados, conforme relevante). Modifique também o manifesto do aplicativo (ApplicationManifest.xml) com o número da versão atualizada do aplicativo e o serviço modificado.  

Para atualizar o aplicativo usando o Eclipse Neon, você pode criar um perfil de configuração de execução duplicada. Em seguida, use-o para atualizar o aplicativo conforme necessário.

1.  Acesse **Executar** > **Configurações de Execução**. No painel esquerdo, clique na seta pequena à esquerda de **Projeto do Gradle**.
2.  Clique com o botão direito do mouse em **ServiceFabricDeployer**e selecione **Duplicar**. Digite um novo nome para essa configuração, por exemplo, **ServiceFabricUpgrader**.
3.  No painel direito, na guia **Argumentos**, altere **-Pconfig='deploy'** para **-Pconfig='upgrade'**e clique em **Aplicar**.

Esse processo cria e salva um perfil de configuração de execução que você pode usar a qualquer momento para atualizar o aplicativo. Também obtém a versão mais recente do tipo de aplicativo atualizado do arquivo de manifesto do aplicativo.

A atualização do aplicativo leva alguns minutos. Você pode monitorar a atualização do aplicativo no Service Fabric Explorer.

## <a name="migrating-old-service-fabric-java-applications-to-be-used-with-maven"></a>Migrando os antigos aplicativos Java do Service Fabric para serem usados com o Maven
Recentemente, movemos as bibliotecas Java do Service Fabric do SDK Java do Service Fabric para o repositório Maven. Embora os novos aplicativos gerados usando o Eclipse gerem projetos atualizados mais recentes (que serão capazes de trabalhar com o Maven), você poderá atualizar os aplicativos Java sem estado ou do ator existentes do Service Fabric, que estavam usando o SDK Java do Service Fabric antes, para usar as dependências Java do Service Fabric a partir do Maven. Siga as etapas mencionadas [aqui](service-fabric-migrate-old-javaapp-to-use-maven.md) para garantir o funcionamento do seu aplicativo mais antigo com o Maven.

<!-- Images -->

[sf-eclipse-plugin-install]: ./media/service-fabric-get-started-eclipse/service-fabric-eclipse-plugin.png

[create-application/p1]:./media/service-fabric-get-started-eclipse/create-application/p1.png
[create-application/p2]:./media/service-fabric-get-started-eclipse/create-application/p2.png
[create-application/p3]:./media/service-fabric-get-started-eclipse/create-application/p3.png
[create-application/p4]:./media/service-fabric-get-started-eclipse/create-application/p4.png
[create-application/p5]:./media/service-fabric-get-started-eclipse/create-application/p5.png
[create-application/p6]:./media/service-fabric-get-started-eclipse/create-application/p6.png

[publish/Publish]: ./media/service-fabric-get-started-eclipse/publish/Publish.png
[publish/RightClick]: ./media/service-fabric-get-started-eclipse/publish/RightClick.png

[add-service/p1]: ./media/service-fabric-get-started-eclipse/add-service/p1.png
[add-service/p2]: ./media/service-fabric-get-started-eclipse/add-service/p2.png
[add-service/p3]: ./media/service-fabric-get-started-eclipse/add-service/p3.png
[add-service/p4]: ./media/service-fabric-get-started-eclipse/add-service/p4.png

<!-- Links -->
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship
