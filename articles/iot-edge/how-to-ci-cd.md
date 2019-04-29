---
title: Integração contínua e implantação contínua – Azure IoT Edge | Microsoft Docs
description: Configurar a integração contínua e a implantação contínua – Azure IoT Edge com Azure DevOps, Azure Pipelines
author: shizn
manager: philmea
ms.author: xshi
ms.date: 01/22/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: f449449c542ce6ac04daa58ff37a3577f0d75aee
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61221683"
---
# <a name="continuous-integration-and-continuous-deployment-to-azure-iot-edge"></a>Integração contínua e implantação contínua no Azure IoT Edge

Você pode facilmente adotar o DevOps com seus aplicativos do Azure IoT Edge com as tarefas internas do Azure IoT Edge no Azure Pipelines. Este artigo demonstra como você pode usar a integração contínua e recursos de implantação contínua de Azure Pipelines para criar, testar e implantar aplicativos de forma rápida e eficiente para o Azure IoT Edge. 

Neste artigo, você aprenderá como usar as tarefas internas do Azure IoT Edge para Azure Pipelines a fim de criar dois pipelines para sua solução do IoT Edge. O primeiro usa seu código e compila a solução, enviando as imagens de módulo para o registro de contêiner e criando um manifesto de implantação. O segundo implanta seus módulos em dispositivos do IoT Edge de destino.  

![Diagrama – ramificações de CI e CD para desenvolvimento e produção](./media/how-to-ci-cd/cd.png)


## <a name="prerequisites"></a>Pré-requisitos

* Um repositório do Azure Repos. Se você não tem um, [crie um novo repositório Git no seu projeto](https://docs.microsoft.com/azure/devops/repos/git/create-new-repo?view=vsts&tabs=new-nav).
* Uma solução do IoT Edge confirmada e enviada para seu repositório. Se você quiser criar uma nova solução de amostra para testar este artigo, siga as etapas em [Desenvolver e depurar módulos no Visual Studio Code](how-to-vs-code-develop-module.md) ou [Desenvolver e depurar módulos C# no Visual Studio](how-to-visual-studio-develop-csharp-module.md).
   * Para este artigo, você só precisa da pasta da solução criada pelos modelos do IoT Edge no Visual Studio Code ou no Visual Studio. Não é necessário compilar, efetuar push, implantar ou depurar esse código antes de continuar. Você configura esses processos no Azure Pipelines. 
   * Se você está criando uma nova solução, clone o repositório localmente primeiro. Depois, ao criar a solução, escolha criá-la diretamente na pasta do repositório. É possível confirmar e efetuar push dos novos arquivos facilmente por lá. 
* Um registro de contêiner em que você possa efetuar push de imagens de módulo. Você pode usar um [Registro de Contêiner do Azure](https://docs.microsoft.com/azure/container-registry/) ou um registro de terceiros. 
* Um [Hub IoT](../iot-hub/iot-hub-create-through-portal.md) ativo com dispositivos do IoT Edge, pelo menos, para testar as fases separadas de implantação de teste e produção. Você pode seguir os artigos de início rápido para criar um dispositivo do IoT Edge no [Linux](quickstart-linux.md) ou [Windows](quickstart.md)


Para saber mais sobre como usar o Azure Repos, confira [Compartilhar seu código com o Visual Studio e o Azure Repos](https://docs.microsoft.com/azure/devops/repos/git/share-your-code-in-git-vs?view=vsts).

## <a name="configure-continuous-integration"></a>Configurar a integração contínua
Nesta seção, você criará um novo pipeline de build. Configure o pipeline para ser executado automaticamente quando você fizer check-in de alterações na solução de amostra do IoT Edge e para publicar logs de build.

>[!NOTE]
>Este artigo usa o designer visual do Azure DevOps. Antes de seguir as etapas desta seção, desative versão prévia do recurso da nova experiência de criação de pipeline do YAML. 
>1. No Azure DevOps, selecione o ícone do seu perfil e a **Versão prévia do recurso**.
>2. Desative **Nova experiência de criação de pipeline YAML**. 
>
>Para saber mais, confira [Criar um pipeline de build](https://docs.microsoft.com/azure/devops/pipelines/get-started-designer?view=vsts&tabs=new-nav#create-a-build-pipeline).

1. Logon em sua organização de DevOps do Azure (**https:\//dev.azure.com/{your organização} /**) e abra o projeto que contém seu repositório de solução de IoT Edge.

   Neste artigo, criamos um repositório chamado **IoTEdgeRepo**. Esse repositório contém **IoTEdgeSolution**, que tem o código para um módulo denominado **filtermodule**. 

   ![Abrir seu projeto do DevOps](./media/how-to-ci-cd/init-project.png)

2. Navegue até Azure Pipelines em seu projeto. Abra a guia **Builds** e selecione **Novo pipeline**. Ou, se você já tem um pipelines de build, selecione o botão **Novo**. Em seguida, escolha **Novo pipeline de build**.

    ![Criar um novo pipeline de build](./media/how-to-ci-cd/add-new-build.png)

3. Siga os prompts para criar o pipeline. 

   1. Forneça as informações de origem para o novo pipeline de build. Selecione **Git do Azure Repos** como a origem, depois selecione o projeto, o repositório e o branch em que se encontra o código da solução do IoT Edge. Em seguida, selecione **Continuar**. 

      ![Selecionar a fonte do pipeline](./media/how-to-ci-cd/pipeline-source.png)

   2. Selecione **Trabalho vazio** ao invés de um modelo. 

      ![Comece com um processo vazio](./media/how-to-ci-cd/start-with-empty.png)

4. Após a criação do pipeline, você será direcionado para o editor de pipeline. Na descrição do pipeline, escolha o pool de agentes correto com base em sua plataforma de destino: 
    
   * Se você quiser compilar os módulos na plataforma amd64 para contêineres do Linux, escolha **Ubuntu 1604 hospedado**

   * Se quiser criar os módulos na plataforma amd64 para contêineres do Windows 1809, você precisará [configurar o agente auto-hospedado no Windows](https://docs.microsoft.com/azure/devops/pipelines/agents/v2-windows?view=vsts).

   * Se quiser criar os módulos na plataforma arm32v7 para contêineres do Linux, você precisará [configurar o agente auto-hospedado no Linux](https://blogs.msdn.microsoft.com/iotdev/2018/11/13/setup-azure-iot-edge-ci-cd-pipeline-with-arm-agent/).
    
     ![Configurar o pool de agentes de build](./media/how-to-ci-cd/configure-env.png)

5. O pipeline vem pré-configurado com um trabalho chamado **Trabalho do agente 1**. Clique no sinal de adição (**+**) para adicionar três tarefas ao trabalho: **Azure IoT Edge** duas vezes e **Publicar artefatos de build** uma vez. Passe o mouse sobre o nome de cada tarefa para ver o botão **Adicionar**.

   ![Adicionar tarefa do Azure IoT Edge](./media/how-to-ci-cd/add-iot-edge-task.png)

   Quando as três tarefas forem adicionadas, seu trabalho do agente será semelhante ao exemplo a seguir:
    
   ![Três tarefas no pipeline de build](./media/how-to-ci-cd/add-tasks.png)

6. Selecione a primeira tarefa **Azure IoT Edge** para editá-la. Essa tarefa compila todos os módulos na solução com a plataforma de destino especificada, além de gerar o arquivo **deployment.json**, que informa aos dispositivos do IoT Edge como configurar a implantação.

   * **Nome de exibição**: aceite o padrão **Azure IoT Edge – Compilar imagens de módulo**.
   * **Ação**: aceite o padrão **Compilar imagens de módulo**. 
   * **Arquivo .template.json**: selecione as reticências (**...**) e navegue até o arquivo **deployment.template.json** no repositório que contém sua solução do IoT Edge. 
   * **Plataforma padrão**: selecione a plataforma adequada para os módulos com base no dispositivo do IoT Edge de destino. 
   * **Variáveis de saída**: as variáveis de saída incluem um nome de referência que você pode usar para configurar o caminho de arquivo onde o arquivo deployment.json será gerado. Defina um nome de referência fácil de lembrar, como **edge**. 

7. Selecione a segunda tarefa **Azure IoT Edge** para editá-la. Essa tarefa efetua push de todas as imagens de módulo para o registro de contêiner selecionado. Ela também adiciona suas credenciais de registro de contêiner ao arquivo **deployment.json** para que seu dispositivo do IoT Edge possa acessar as imagens de módulo. 

   * **Nome de exibição**: o nome de exibição é atualizado automaticamente quando o campo de ação é alterado. 
   * **Ação**: use a lista suspensa para selecionar **Efetuar push de imagens de módulo**. 
   * **Tipo de registro de contêiner**: selecione o tipo de registro de contêiner usado para armazenar as imagens de módulo. Dependendo do tipo de registro escolhido, o formulário é alterado. Se você escolher **Registro de Contêiner do Azure**, use as listas suspensas para selecionar a assinatura do Azure e o nome do registro de contêiner. Se você escolher **Registro de Contêiner Genérico**, selecione **Novo** para criar uma conexão de serviço de registro. 
   * **Arquivo .template.json**: selecione as reticências (**...**) e navegue até o arquivo **deployment.template.json** no repositório que contém sua solução do IoT Edge. 
   * **Plataforma padrão**: selecione a mesma plataforma em que estão as imagens dos módulos compilados.

   Se você tiver vários registros de contêiner para hospedar suas imagens de módulo, você precisará duplicar essa tarefa, selecione o registro de contêiner diferente e usar **Ignorar módulos** nas configurações avançadas para ignorar as imagens que não são para esse registro específico.

8. Selecione a tarefa **Publicar artefatos de build** para editá-la. Forneça o caminho de arquivo para o arquivo de implantação gerado pela tarefa de build. Defina o valor de **Caminho de publicação** para coincidir com a variável de saída definida na tarefa de compilação de módulo. Por exemplo, `$(edge.DEPLOYMENT_FILE_PATH)`. Deixe os outros valores com seus padrões. 

9. Abra a guia **Gatilhos** e marque a caixa **Ativar integração contínua**. Verifique se o branch que contém seu código está incluído.

    ![Ativar o gatilho de integração contínua](./media/how-to-ci-cd/configure-trigger.png)

10. Salve o novo pipeline de build com o botão **Salvar**.

Agora esse pipeline está configurado para ser executado automaticamente quando você efetuar push do novo código para seu repositório. A última tarefa, publicar os artefatos do pipeline, dispara um pipeline de lançamento. Acesse a próxima seção para compilar o pipeline de lançamento. 

## <a name="configure-continuous-deployment"></a>configurar uma implantação contínua
Nesta seção, crie um pipeline de lançamento configurado para ser executado automaticamente quando o pipeline de build deixar de ter artefatos e para mostrar logs de implantação no Azure Pipelines.

Nesta seção, você criará dois estágios diferentes, um para implantações de teste e outro para implantações de produção. 

### <a name="create-test-stage"></a>Criar estágio de teste

Crie um novo pipeline e configure o primeiro estágio para implantações de garantia de qualidade. 

1. Na guia **Versões**, escolha **+ Novo pipeline**. Ou, se você já tiver pipelines de lançamento, escolha o botão **+Novo** e clique em **+Novo pipeline de lançamento**.  

    ![Adicionar pipeline de lançamento](./media/how-to-ci-cd/add-release-pipeline.png)

2. Quando tiver que selecionar um modelo, escolha começar com um **Trabalho vazio**.

    ![Começar um trabalho vazio](./media/how-to-ci-cd/start-with-empty-job.png)

3. O novo pipeline de lançamento é iniciado com um estágio, chamado **Estágio 1**. Renomeie o Estágio 1 como **QA**, para Garantia de Qualidade, e trate-o como um ambiente de teste. Geralmente, os pipelines de implantação contínua têm vários estágios. Você pode criar mais com base em sua prática no DevOps. Feche a janela de detalhes do estágio depois de renomeá-lo. 

    ![Criar um estágio de ambiente de teste](./media/how-to-ci-cd/QA-env.png)

4. Vincule a versão aos artefatos de build publicados pelo pipeline de build. Clique em **Adicionar** na área de artefatos.

   ![Adicionar artefatos](./media/how-to-ci-cd/add-artifacts.png)  
    
5. Em **Adicionar uma página de artefato**, selecione **Build** como o tipo de origem. Em seguida, selecione o projeto e o pipeline de build que você criou. Em seguida, selecione **Adicionar**.

   ![Adicionar um artefato de build](./media/how-to-ci-cd/add-an-artifact.png)

6. Abra os gatilhos de artefato e selecione o botão de alternância para habilitar o gatilho de implantação contínua. Agora, uma nova versão será criada sempre que um novo build estiver disponível.

   ![Configurar um gatilho de implantação contínua](./media/how-to-ci-cd/add-a-trigger.png)

7. O estágio **QA** é pré-configurado com um trabalho e nenhuma tarefa. No menu do pipeline, selecione **Tarefas**, depois escolha o estágio **QA**.  Selecione o trabalho e a contagem de tarefas para configurar as tarefas nesse estágio.

    ![Configurar tarefas de Garantia de Qualidade](./media/how-to-ci-cd/view-stage-tasks.png)

8. No estágio QA, você deve ver um **Trabalho do agente** padrão. É possível configurar detalhes sobre o trabalho do agente, mas a tarefa de implantação não diferencia a plataforma, de modo que você pode escolher **VS2017 hospedado** ou **Ubuntu 1604 hospedado** no **Pool de agente** (ou qualquer outro agente gerenciado por você). 

9. Selecione o sinal de adição (**+**) para adicionar uma tarefa. Pesquise e adicione **Azure IoT Edge**. 

    ![Adicionar tarefas para Garantia de Qualidade](./media/how-to-ci-cd/add-task-qa.png)

10. Selecione a nova tarefa do Azure IoT Edge e configure-a com os seguintes valores:

    * **Nome de exibição**: o nome de exibição é atualizado automaticamente quando o campo de ação é alterado. 
    * **Ação**: use a lista suspensa para selecionar **Implantar no dispositivo do Azure IoT Edge**. A alteração do valor de ação também atualiza o nome de exibição da tarefa para haver correspondência.
    * **Assinatura do Azure**: selecione a assinatura que contém seu Hub IoT.
    * **Nome do Hub IoT**: Selecione seu Hub IoT. 
    * **Escolher um/vários dispositivos**: escolha se o pipeline de lançamento deve ser implantado em um ou vários dispositivos. 
      * Se quiser implantar em um único dispositivo, insira a **ID do dispositivo do IoT Edge**. 
      * Se estiver implantando em vários dispositivos, especifique a **condição de destino** do dispositivo. A condição de destino é um filtro para corresponder a um conjunto de dispositivos do Edge no Hub IoT. Se você quiser usar Marcas de Dispositivo como a condição, será necessário atualizar as Marcas de dispositivos correspondentes com o dispositivo gêmeo do Hub IoT. Atualize a **ID de implantação do IoT Edge** e a **prioridade de implantação do IoT Edge** nas configurações avançadas. Para saber mais sobre como criar uma implantação em vários dispositivos, confira [Entender as implantações automáticas do IoT Edge](module-deployment-monitoring.md).

11. Selecione **Salvar** para salvar as alterações no novo pipeline de lançamento. Retorne para o modo de exibição do pipeline selecionando **Pipeline** no menu. 

### <a name="create-production-stage"></a>Criar um estágio de produção

Crie um segundo estágio no seu pipeline de lançamento para a implantação de produção. 

1. Para criar um segundo estágio de produção, clone o estágio QA. Passe o mouse sobre o estágio QA e selecione o botão Clonar. 

    ![Clonar estágio](./media/how-to-ci-cd/clone-stage.png)

2. Selecione o novo estágio, chamado **Cópia de QA**, para abrir suas propriedades. Altere o nome do estágio para **PROD**, de produção. Feche a janela de propriedades do estágio. 

3. Para abrir as tarefas do estágio PROD, selecione **Tarefas** no menu do pipeline, depois escolha o estágio **PROD**. 

4. Selecione a tarefa do Azure IoT Edge a configurar para o ambiente de produção. As configurações de implantação provavelmente são as mesmas para os estágios QA e PROD, exceto pelo fato de que você direciona para um dispositivo ou conjunto de dispositivos diferente na produção. Atualize o campo de ID do dispositivo ou os campos de condição de destino e ID de implantação em seus dispositivos de produção. 

5. Salve ele com o botão **Salvar**. Em seguida, clique em **Pipeline** para voltar à exibição do pipeline.
    
6. Da forma como esse pipeline de lançamento está configurado atualmente, o artefato de build acionará o estágio **QA** e, em seguida, o estágio **PROD** toda vez que um novo build for concluído. No entanto, geralmente convém integrar alguns casos de teste nos dispositivos de garantia de qualidade e aprovar manualmente a implantação para produção. Use as seguintes etapas para criar uma condição de aprovação para o estágio PROD:

    1. Abra o painel de configuração **Condições de pré-implantação**.

        ![Abrir Condições de pré-implantação](./media/how-to-ci-cd/pre-deploy-conditions.png)    

    2. Mude o botão de alternância da condição **Aprovações de pré-implantação** para **Habilitado**. Adicione um ou mais usuários ou grupos no campo **Aprovadores** e personalize outras políticas de aprovação desejadas. Para salvar suas alterações, feche o painel de condições de pré-implantação.
    
       ![Definir condições](./media/how-to-ci-cd/set-pre-deployment-conditions.png)


7. Salve o pipeline de lançamento com o botão **Salvar**. 

    
## <a name="verify-iot-edge-cicd-with-the-build-and-release-pipelines"></a>Verificar o CI/CD do IoT Edge com os pipelines de lançamento e de build

Para disparar um trabalho de build, você pode efetuar push da confirmação do repositório do código-fonte ou dispará-lo manualmente. Nesta seção, dispare manualmente o pipeline de CI/CD para testar o funcionamento dele. Em seguida, verifique se a implantação é bem-sucedida.

1. Navegue até o pipeline de build criado no início deste artigo. 

2. Você pode disparar um trabalho de build em seu pipeline de build clicando no botão **Fila** como na captura de tela a seguir.

    ![Gatilho manual](./media/how-to-ci-cd/manual-trigger.png)

3. Selecione-o para acompanhar seu progresso. Se o pipeline de build concluir com êxito, ele disparará uma versão para o estágio **QA**. 

    ![Logs de build](./media/how-to-ci-cd/build-logs.png)

4. A implantação bem-sucedida para o estágio **QA** dispara uma notificação para o aprovador. Verifique se os módulos foram implantados com sucesso nos dispositivos de destino com o estágio QA. Em seguida, navegue até o pipeline de lançamento e aprove o encaminhamento da versão para o estágio PROD, selecionando o botão **PROD** e **Aprovar**. 

    ![Aprovação pendente](./media/how-to-ci-cd/pending-approval.png)

5. Depois de o aprovador aprovar essa alteração, ela poderá ser implantada em **PROD**.

## <a name="next-steps"></a>Próximas etapas

* Entenda a implantação do IoT Edge em [Noções básicas sobre implantações do IoT Edge para dispositivos únicos ou em escala](module-deployment-monitoring.md)
* Siga as etapas para criar, atualizar ou excluir uma implantação em [Implantar e monitorar os módulos do IoT Edge em larga escala](how-to-deploy-monitor.md).
