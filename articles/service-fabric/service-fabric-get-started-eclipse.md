---
title: Plug-in do Service Fabric do Azure para Eclipse | Microsoft Docs
description: Comece a usar o plug-in do Service Fabric para Eclipse.
services: service-fabric
documentationcenter: java
author: rapatchi
manager: chackdan
editor: ''
ms.assetid: bf84458f-4b87-4de1-9844-19909e368deb
ms.service: service-fabric
ms.devlang: java
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/06/2018
ms.author: rapatchi
ms.openlocfilehash: c33ecce5610dbef0dce13aa95f04ae4f0620603b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60949942"
---
# <a name="service-fabric-plug-in-for-eclipse-java-application-development"></a>Plug-in do Service Fabric para o desenvolvimento de aplicativos Eclipse Java
O Eclipse é um dos IDEs (ambientes de desenvolvimento integrado) mais amplamente usados para desenvolvedores de Java. Neste artigo, descreveremos como configurar seu ambiente de desenvolvimento do Eclipse para trabalhar com o Azure Service Fabric. Saiba como instalar o plug-in do Service Fabric e criar e implantar o aplicativo do Service Fabric em um cluster do Service Fabric local ou remoto no Eclipse. 

> [!NOTE]
> Atualmente, não há suporte para o plugin Eclipse no Windows. 

## <a name="install-or-update-the-service-fabric-plug-in-in-eclipse"></a>Instalar ou atualizar o plug-in do Service Fabric no Eclipse
Você pode instalar um plug-in do Service Fabric no Eclipse. O plug-in pode ajudar a simplificar o processo de criação e implantação de serviços Java.

> [!IMPORTANT]
> O plug-in do Service Fabric requer o Eclipse Neon ou uma versão posterior. Consulte as instruções após essa observação para saber como verificar qual versão do Eclipse você possui. Se você tiver uma versão anterior do Eclipse instalada, pode baixar as versões mais recentes no [site do Eclipse](https://www.eclipse.org). Não é recomendável instalar por cima de (substituir) uma instalação existente do Eclipse. Você pode removê-la antes de executar o instalador ou instalar a versão mais recente em um diretório diferente. 
> 
> No Ubuntu, é recomendável instalar diretamente a partir do site do Eclipse em vez de usar um instalador de pacote (`apt` ou `apt-get`). Isso garante que você obtenha a versão mais atual do Eclipse. 

Instale o Eclipse Neon ou posterior do [site do Eclipse](https://www.eclipse.org).  Instale também a versão 2.2.1 ou posterior do Buildship (plug-in do Service Fabric não é compatível com versões anteriores do Buildship):
-   Para verificar as versões dos componentes instalados, no Eclipse, acesse **Ajuda** > **Sobre o Eclipse** > **Detalhes da Instalação**.
-   Para atualizar o Buildship, confira [Eclipse Buildship: Plug-ins do Eclipse para o Gradle][buildship-update].
-   Para verificar e instalar atualizações para o Eclipse, acesse **Ajuda** > **Verificar se há Atualizações**.

Instale o plug-in do Service Fabric, no Eclipse, acesse **Ajuda** > **Instalar Novo Software**.
1. No **trabalhar com** , digite https:\//dl.microsoft.com/eclipse.
2. Clique em **Adicionar**.

   ![Plug-in do Service Fabric para Eclipse][sf-eclipse-plugin-install]
3. Selecione o plug-in do Service Fabric e clique em **Avançar**.
4. Conclua as etapas de instalação e aceite os Termos de Licença de Software da Microsoft.
  
Se você já tiver o plug-in do Service Fabric instalado, instale a versão mais recente. 
1. Para verificar se há atualizações disponíveis, acesse **Ajuda** > **Sobre o Eclipse** > **Detalhes da Instalação**. 
2. Na lista de plug-ins instalados, selecione o Service Fabric e clique em **Atualizar**. As atualizações disponíveis serão instaladas.
3. Após a atualização do plug-in do Service Fabric, atualize também o projeto Gradle.  Clique com botão direito em **build.gradle** e selecione **Atualizar**.

> [!NOTE]
> Se a instalação ou atualização do plug-in do Service Fabric estiver lenta, isso poderá ser devido a uma configuração do Eclipse. O Eclipse coleta metadados sobre todas as alterações para atualizar sites que estão registrados com a instância do Eclipse. Para acelerar o processo de verificar e instalar uma atualização de plug-in do Service Fabric, acesse **Sites de Software Disponível**. Desmarque as caixas de seleção para todos os sites, exceto aquele que aponta para o local do plug-in do Service Fabric (https:\//dl.microsoft.com/eclipse/azure/servicefabric).

> [!NOTE]
>Se o Eclipse não está funcionando conforme o esperado no seu Mac ou você precisa executar como superusuário), vá para a pasta **ECLIPSE_INSTALLATION_PATH** e navegue até a subpasta **Eclipse.app/Contents/MacOS**. Inicie o Eclipse executando `./eclipse`.


## <a name="create-a-service-fabric-application-in-eclipse"></a>Criar um aplicativo do Service Fabric no Eclipse

1.  No Eclipse, acesse **Arquivo** > **Novo** > **Outros**. Selecione **Projeto do Service Fabric** e clique em **Avançar**.

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

## <a name="build-a-service-fabric-application-in-eclipse"></a>Construa um aplicativo do Service Fabric no Eclipse

1.  Clique com o botão direito do mouse no novo aplicativo do Service Fabric e selecione **Service Fabric**.

    ![Menu de atalho do Service Fabric][publish/RightClick]

2. No menu de contexto, selecione uma das seguintes opções:
    -   Para compilar o aplicativo sem limpeza, clique em **Compilar Aplicativo**.
    -   Para fazer uma compilação limpa do aplicativo, clique em **Recompilar o Aplicativo**.
    -   Para limpar a aplicação de artefatos compilados, clique em **Limpar Aplicativo**.
     
## <a name="deploy-a-service-fabric-application-to-the-local-cluster-with-eclipse"></a>Implemente um aplicativo do Service Fabric no cluster local com o Eclipse

Depois de criar seu aplicativo do Service Fabric, siga estas etapas para implantá-lo no cluster local.

1. Se você não iniciou o cluster local, siga as instruções em [Configurar um cluster local](./service-fabric-get-started-linux.md#set-up-a-local-cluster) para iniciar seu cluster local e verifique se ele está em execução.
2. Clique com o botão direito do mouse no aplicativo Service Fabric e selecione **Service Fabric**.

    ![Menu de atalho do Service Fabric][publish/RightClick]

3.  No menu de contexto, clique em **implantar aplicativo**.
4.  Você pode acompanhar o progresso da operação de implantação na janela do console.
5.  Para verificar se seu aplicativo está em execução, abra o Service Fabric Explorer em seu cluster local em uma janela do navegador [http://localhost:19080/Explorer](http://localhost:19080/Explorer). Expanda o nó **Aplicativos** e verifique se seu aplicativo está em execução. 

Para aprender como depurar seu aplicativo no Eclipse usando o cluster local, consulte [Depurar um serviço Java no Eclipse](./service-fabric-debugging-your-application-java.md).

Você também pode implantar seu aplicativo no cluster local com o comando **Publish Application**:

1. Clique com o botão direito do mouse no aplicativo Service Fabric e selecione **Service Fabric**.
2. No menu de contexto, clique em **Publicar aplicativo...**.
3. Na janela **Publicar aplicativo**, escolha **PublishProfiles / Local.json** como o Perfil de destino e clique em **Publicar**.

    ![Caixa de diálogo Publicar local](./media/service-fabric-get-started-eclipse/localjson.png)

    Por padrão, o perfil de publicação Local.json é configurado para publicar no cluster local. Para obter mais informações sobre os parâmetros de conexão e de terminal presentes nos perfis de publicação, consulte a próxima seção.

## <a name="publish-your-service-fabric-application-to-azure-with-eclipse"></a>Publique seu aplicativo do Service Fabric no Azure com o Eclipse

Para publicar seu aplicativo na nuvem, siga estas etapas:

1. Para publicar seu aplicativo em um cluster seguro na nuvem, você precisa de um certificado X.509 para usar para se comunicar com seu cluster. Em ambientes de teste e desenvolvimento, o certificado usado geralmente é o certificado de cluster. Em ambientes de produção, o certificado deve ser um certificado de cliente que seja diferente do certificado de cluster. Você precisa do certificado e da chave privada. O arquivo de certificado (e chave) deve ter o formato PEM. Você pode criar um arquivo PEM que contenha o certificado e a chave privada de um arquivo PFX com o seguinte comando openssl:

    ```bash
    openssl pkcs12 -in your-cert-file.pfx -out your-cert-file.pem -nodes -passin pass:your-pfx-password
    ```

   Se o arquivo PFX não for protegido por senha, use `--passin pass:` para o último parâmetro.

2. Abra o **Cloud** do arquivo sob o **PublishProfiles** diretório. Você precisa configurar o ponto de extremidade do cluster e as credenciais de segurança adequadamente para seu cluster.

   - O `ConnectionIPOrURL` campo contém o endereço IP ou URL do cluster. Observe que o valor não contém o esquema de URL (`https://`).
   - Por padrão o `ConnectionPort`campo deve ser`19080`, a menos que você explicitamente foram alterados essa porta para seu cluster.
   - O campo `ClientKey` deve apontar para um arquivo .pem ou .key formatado em PEM em sua máquina local que contenha a chave privada para seu cliente ou certificado de cluster.
   - O campo `ClientCert` deve apontar para um arquivo .pem ou .crt formatado em PEM em sua máquina local que contenha os dados de certificado para seu cliente ou cluster. Certificado. 

     ```bash
     {
         "ClusterConnectionParameters":
         {
            "ConnectionIPOrURL": "lnxxug0tlqm5.westus.cloudapp.azure.com",
            "ConnectionPort": "19080",
            "ClientKey": "[path_to_your_pem_file_on_local_machine]",
            "ClientCert": "[path_to_your_pem_file_on_local_machine]"
         }
     }
     ```

2. Clique com o botão direito do mouse no aplicativo Service Fabric e selecione **Service Fabric**.
3. No menu de contexto, clique em **Publicar aplicativo...**.
3. Na janela **Publicar aplicativo**, escolha **PublishProfiles / Cloud.json** como o Perfil de destino e clique em **Publicar**.

    ![Caixa de diálogo Publicar em nuvem](./media/service-fabric-get-started-eclipse/cloudjson.png)

4. Você pode acompanhar o andamento da operação de publicação na janela do console.
5. Para verificar se seu aplicativo está em execução, abra o Service Fabric Explorer no cluster do Azure em uma janela do navegador. No exemplo acima, isso seria: `https://lnxxug0tlqm5.westus.cloudapp.azure.com:19080/Explorer`. Expanda o nó **Aplicativos** e verifique se seu aplicativo está em execução. 


Em clusters seguros do Linux, se seu aplicativo contiver serviços do Reliable Services, você também precisará configurar um certificado que seus serviços possam usar para chamar APIs de tempo de execução do Service Fabric. Para obter mais informações, consulte [Configurar um aplicativo de Reliable Services para executar em clusters do Linux](./service-fabric-configure-certificates-linux.md#configure-a-reliable-services-app-to-run-on-linux-clusters).

Para obter uma rápida explicação de como implantar um aplicativo Reliable Services do Service Fabric escrito em Java em um cluster Linux seguro, confira [Início Rápido: Implante um aplicativo Reliable Services Java](./service-fabric-quickstart-java-reliable-services.md).

## <a name="deploy-a-service-fabric-application-by-using-eclipse-run-configurations"></a>Implantar um aplicativo do Service Fabric usando configurações de execução do Eclipse

Uma maneira alternativa de implantar o aplicativo do Service Fabric é usar as configurações de execução do Eclipse.

1. No Eclipse, acesse **executados** > **configurações de execução**.
2. Em **Projeto do Gradle**, selecione a configuração de execução **ServiceFabricDeployer**.
3. No painel direito, na guia **Argumentos**, certifique-se de que o **ip**, **porta**, **clientCert** e **clientKey** parâmetros são definidos adequadamente para sua implementação. Por padrão, os parâmetros são definidos para implantar no cluster local, como na captura de tela a seguir. Para publicar seu aplicativo no Azure, você pode modificar os parâmetros para conter os detalhes do ponto de extremidade e as credenciais de segurança do cluster do Azure. Para obter mais informações, consulte a seção anterior, [Publique seu aplicativo do Service Fabric no Azure com o Eclipse](#publish-your-service-fabric-application-to-azure-with-eclipse).

    ![Local da caixa de diálogo de configuração de execução](./media/service-fabric-get-started-eclipse/run-config-local.png)

5. Certifique-se de que **Working Directory** aponta para o aplicativo que você deseja implantar. Para alterar o aplicativo, clique no botão **Workspace** e selecione o aplicativo desejado.
6. Clique em **Aplicar** e em **Executar**.

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

Para um cenário de atualização, digamos que você tenha criado o projeto **App1** usando o plug-in do Service Fabric do Eclipse. Você o implantou usando o plug-in para criar um aplicativo chamado **fabric:/App1Application**. O tipo de aplicativo é **App1ApplicationType** e a versão do aplicativo é 1.0. Agora, você deseja atualizar o aplicativo sem interromper a disponibilidade.

Primeiro, faça as alterações no aplicativo e recrie o serviço modificado. Atualize o arquivo de manifesto do serviço modificado (ServiceManifest.xml) com as versões atualizadas para o serviço (e Código, Configuração ou Dados, conforme relevante). Modifique também o manifesto do aplicativo (ApplicationManifest.xml) com o número da versão atualizada do aplicativo e o serviço modificado.  

Para atualizar o aplicativo usando o Eclipse, você pode criar um perfil de configuração de execução duplicada. Em seguida, use-o para atualizar o aplicativo conforme necessário.

1.  Acesse **Executar** > **Configurações de Execução**. No painel esquerdo, clique na seta pequena à esquerda de **Projeto do Gradle**.
2.  Clique com o botão direito do mouse em **ServiceFabricDeployer**e selecione **Duplicar**. Digite um novo nome para essa configuração, por exemplo, **ServiceFabricUpgrader**.
3.  No painel direito, na guia **Argumentos**, altere **-Pconfig='deploy'** para **-Pconfig='upgrade'** e clique em **Aplicar**.

Esse processo cria e salva um perfil de configuração de execução que você pode usar a qualquer momento para atualizar o aplicativo. Também obtém a versão mais recente do tipo de aplicativo atualizado do arquivo de manifesto do aplicativo.

A atualização do aplicativo leva alguns minutos. Você pode monitorar a atualização do aplicativo no Service Fabric Explorer.

## <a name="migrating-old-service-fabric-java-applications-to-be-used-with-maven"></a>Migrando os antigos aplicativos Java do Service Fabric para serem usados com o Maven
Recentemente, movemos as bibliotecas Java do Service Fabric do SDK Java do Service Fabric para o repositório Maven. Embora os novos aplicativos gerados usando o Eclipse gerem projetos atualizados mais recentes (que serão capazes de trabalhar com o Maven), você poderá atualizar os aplicativos Java sem estado ou do ator existentes do Service Fabric, que estavam usando o SDK Java do Service Fabric antes, para usar as dependências Java do Service Fabric a partir do Maven. Siga as etapas mencionadas [aqui](service-fabric-migrate-old-javaapp-to-use-maven.md) para garantir o funcionamento do seu aplicativo mais antigo com o Maven.

## <a name="next-steps"></a>Próximas etapas

- Para obter as etapas rápidas da criação do aplicativo Reliable Services Java e implantá-lo localmente e no Azure, confira [Início Rápido: Implante um aplicativo Reliable Services Java](./service-fabric-quickstart-java-reliable-services.md).
- Para saber como depurar um aplicativo Java em seu cluster local, consulte [depurar um serviço Java no Eclipse](./service-fabric-debugging-your-application-java.md).
- Para saber como monitorar e diagnosticar aplicativos do Service Fabric, consulte [monitorar e diagnosticar serviços em uma configuração de desenvolvimento do computador local](./service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md).

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
