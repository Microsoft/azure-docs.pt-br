---
title: Pipeline de CI/CD com o Azure DevOps Projects – Azure IoT Edge| Microsoft Docs
description: O Azure DevOps Projects facilita o uso inicial do Azure. Ele ajuda você a iniciar um aplicativo do Azure IoT Edge de sua escolha em algumas etapas rápidas.
author: shizn
manager: ''
ms.author: xshi
ms.date: 07/09/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 109a7e327217a342f485dd61b53115569f2346cd
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67722987"
---
# <a name="create-a-cicd-pipeline-for-iot-edge-with-azure-devops-projects"></a>Criar um pipeline de CI/CD para o IoT Edge com projetos de DevOps do Azure

Configure a integração contínua (CI) e a entrega contínua (CD) para seu aplicativo IoT Edge com os Azure DevOps Projects. O DevOps Projects do Azure simplifica a configuração inicial de um pipeline de build e de lançamento no Azure Pipelines.

Se você não tiver uma assinatura do Azure ativa, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

O DevOps Projects cria um pipeline de CI/CD no Azure DevOps. É possível criar uma nova organização do Azure DevOps ou usar uma existente. O DevOps Projects também cria recursos do Azure na assinatura do Azure de sua escolha.

1. Entre no [Portal do Microsoft Azure](https://portal.azure.com).

1. No painel esquerdo, selecione **Criar um recurso**, depois procure o **DevOps Projects**.  

1.  Selecione **Criar**.

## <a name="create-a-new-application-pipeline"></a>Criar um novo pipeline de aplicativo 

1. Seus módulos do Azure IoT Edge podem ser escritos em [C#](tutorial-csharp-module.md), [Node.js](tutorial-node-module.md), [Python](tutorial-python-module.md), [C](tutorial-c-module.md) e [Java](tutorial-java-module.md). Selecione sua linguagem preferida para iniciar um novo aplicativo: **.NET**, **Node.js**, **Python**, **C** ou **Java**. Selecione **Avançar** para continuar.

   ![Selecione o idioma para criar um novo aplicativo](./media/how-to-devops-project/select-language.png)

2. Selecione **IoT simples** como sua estrutura de aplicativo e selecione **próxima**.

   ![Selecione a estrutura de IoT simples](media/how-to-devops-project/select-iot.png)

3. Selecione **IoT Edge** como o serviço do Azure que implanta o aplicativo e selecione **Avançar**.

   ![Selecione o serviço IoT Edge](media/how-to-devops-project/select-iot-edge.png)

4. Crie uma nova organização do Azure DevOps gratuita ou escolha uma existente.

   1. Forneça um nome para o seu projeto. 

   2. Selecione sua organização do Azure DevOps. Se você não tiver uma organização existente, selecione **Configurações adicionais** para criar uma nova. 

   3. Selecione sua assinatura do Azure.

   4. Use o nome do Hub IoT gerado pelo nome do seu projeto, ou forneça outro nome.

   5. Selecione **Configurações adicionais** para configurar os recursos do Azure que o DevOps Projects cria em seu nome.

   6. Selecione **Concluído** para terminar de criar seu projeto. 

   ![Nomear e criar aplicativo](media/how-to-devops-project/select-devops.png)

Após alguns minutos, o painel do DevOps Projects é exibido no portal do Azure. Selecione o nome do projeto para ver o andamento. Talvez seja necessário atualizar a página. Um aplicativo de IoT Edge é configurado em um repositório em sua organização do Azure DevOps, é executado um build, e seu aplicativo é implantado no IoT Edge. Esse painel oferece visibilidade ao seu repositório de código, pipeline de CI/CD e seu aplicativo no Azure.

   ![Aplicativo de exibição no portal do DevOps](./media/how-to-devops-project/devops-portal.png)


## <a name="commit-code-changes-and-execute-cicd"></a>Confirmar as alterações de código e executar CI/CD

O DevOps Projects criou um repositório Git para seu projeto no Azure Repos. Nesta seção, você exibe o repositório e faz alterações de código no seu aplicativo.

1. Para navegar até o repositório criado para seu projeto, selecione **Repositórios** no menu do painel do projeto.  

   ![Exibir repositório gerado no Azure Repos](./media/how-to-devops-project/view-repositories.png)

2. As etapas a seguir mostram como usar o navegador da Web para fazer alterações de código. Se você, em vez disso, deseja clonar o repositório localmente, selecione **Clonar** no canto superior direito da janela. Use a URL fornecida para clonar seu repositório Git no Visual Studio Code ou na ferramenta de desenvolvimento desejada. 

3. O repositório já contém código para um módulo chamado **SampleModule** baseado na linguagem de aplicativo que você escolheu no processo de criação. Abra o arquivo **modules/SampleModule/module.json**.

   ![Abrir arquivo module.json no Azure Repos](./media/how-to-devops-project/open-module-json.png)

4. Selecione **Editar**, depois faça uma alteração no `"version"` em `"tag"`. Por exemplo, você pode atualizá-lo para `"version": "${BUILD_BUILDID}"` usar [variáveis de compilação do Azure DevOps](https://docs.microsoft.com/azure/devops/pipelines/build/variables?view=vsts#build-variables) como parte de sua marca de imagem de módulo do Azure IoT Edge.

   ![Editar versão para aceitar as variáveis de compilação](media/how-to-devops-project/update-module-json.png)

5. Selecione **Confirmar**, depois salve as alterações.

6. No navegador, volte ao painel do DevOps Projects no portal do Azure. Agora você deve ver uma compilação que está em andamento. As alterações feitas são automaticamente compiladas e implantadas via pipeline de CI/CD.

    ![Exibir o status em andamento](media/how-to-devops-project/ci-cd-in-progress.png)

## <a name="examine-the-cicd-pipeline"></a>Examinar o pipeline de CI/CD

Nas seções anteriores, o Azure DevOps Projects configurou automaticamente um pipeline de CI/CD para o seu aplicativo IoT Edge. Em seguida, você testou o pipeline de build confirmando alterações em um dos arquivos. Agora, explore e personalize o pipeline da maneira que achar necessário. Siga as etapas a seguir para se familiarizar com os pipelines de build e de lançamento do Azure DevOps.

1. Para exibir os pipelines de build no projeto do DevOps, selecione **Pipelines de Build** no menu do painel do projeto. Esse link abre uma guia de navegador e o pipeline de build do Azure DevOps para seu novo projeto.

   ![Exibir pipelines de build no Azure Pipelines](./media/how-to-devops-project/view-build-pipelines.png)

2. Selecione **Editar**.

    ![Editar o pipeline de build](media/how-to-devops-project/click-edit-button.png)

3. No painel aberto, você pode examinar as tarefas realizadas quando o pipeline de build é executado. O pipeline de build realiza várias tarefas, como efetuar buscas das fontes no repositório Git, compilar imagens do módulo IoT Edge, efetuar push de módulos IoT Edge para um registro de contêiner e publicar as saídas usadas para implantações. Para saber mais sobre as tarefas do Azure IoT Edge no Azure DevOps, confira [Configurar o Azure Pipelines para integração contínua](how-to-ci-cd.md#configure-continuous-integration).

4. Selecione o cabeçalho **Pipeline** na parte superior do pipeline de build para abrir os detalhes do pipeline. Altere o nome do pipeline de build para algo mais descritivo.

   ![Editar os detalhes do pipeline](./media/how-to-devops-project/edit-build-pipeline.png)

5. Selecione **Salvar e colocar na fila** selecione **Salvar**.

6. No menu do pipeline de build, selecione **Gatilhos** no menu. O DevOps Projects criou automaticamente um gatilho de CI e cada confirmação no repositório inicia um novo build.  Você pode optar por incluir ou excluir os branches do processo de CI.

7. Selecione **Retenção**. Dependendo do seu cenário, você pode especificar políticas para manter ou remover um determinado número de compilações.

8. Selecione **Histórico**. O painel de histórico contém uma trilha de auditoria das alterações recentes feitas no build. O Azure Pipelines controla quaisquer alterações feitas no pipeline de build e permite que você compare versões.

9. Quando você terminar de explorar o pipeline de build, navegue até o pipeline de lançamento correspondente. Selecione **Versões** em **Pipelines** e selecione **Editar** para exibir os detalhes do pipeline.

    ![Visualizar pipeline de lançamento](media/how-to-devops-project/release-pipeline.png)

10. Em **Artefatos**, selecione **Soltar**. A fonte observada por esse artefato é a saída do pipeline de build examinado nas etapas anteriores. 

11. Ao lado do ícone **Soltar**, selecione o **Gatilho de implantação contínua** que se parece com um raio. Esse pipeline de lançamento habilitou o gatilho, o qual executa uma implantação sempre que houver um novo artefato de build disponível. Outra opção é desabilitar o gatilho para que suas implantações exijam uma execução manual.  

12. No menu do pipeline de lançamento, selecione **Tarefas** e escolha o estágio **Desenvolvimento** na lista suspensa. O DevOps Projects criou um estágio de versão para você que cria um hub IoT, cria um dispositivo IoT Edge nesse hub, implanta o módulo de exemplo do pipeline de build e provisiona uma máquina virtual para ser executada como seu dispositivo IoT Edge. Para saber mais sobre as tarefas do Azure IoT Edge para Implantação Contínua, confira [Configurar o Azure Pipelines para implantação contínua](how-to-ci-cd.md#configure-continuous-deployment).

    ![Visualizar tarefas de implantação contínua](media/how-to-devops-project/dev-release.png)

13. À direita, selecione **Exibir versões**. Essa exibição mostra um histórico de versões.

14. Selecione o nome de uma versão para exibir mais informações sobre ela.


## <a name="clean-up-resources"></a>Limpar recursos

É possível excluir o Serviço de Aplicativo do Azure e outros recursos criados quando eles não forem mais precisos. Use a funcionalidade **Excluir** no painel do DevOps Projects.

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre as tarefas para o Azure IoT Edge em DevOps do Azure na [Integração contínua e implantação contínua no Azure IoT Edge](how-to-ci-cd.md)
* Entenda a implantação do IoT Edge em [Noções básicas sobre implantações do IoT Edge para dispositivos únicos ou em escala](module-deployment-monitoring.md)
* Siga as etapas para criar, atualizar ou excluir uma implantação em [Implantar e monitorar os módulos do IoT Edge em larga escala](how-to-deploy-monitor.md).
