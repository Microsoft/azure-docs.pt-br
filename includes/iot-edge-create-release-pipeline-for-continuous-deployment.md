---
author: v-tcassi
ms.service: iot-edge
ms.topic: include
ms.date: 08/26/2020
ms.author: v-tcassi
ms.openlocfilehash: 706b2306fbe9f2a744d2874a8b55f78fa2fc8e4d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89301432"
---
## <a name="create-a-release-pipeline-for-continuous-deployment"></a>Criar um pipeline de liberação para implantação contínua

Nesta seção, crie um pipeline de lançamento configurado para ser executado automaticamente quando o pipeline de build deixar de ter artefatos e para mostrar logs de implantação no Azure Pipelines.

Crie um novo pipeline e adicione um novo estágio:

1. Na guia **versões** em **pipelines**, escolha **+ novo pipeline**. Ou, se você já tiver pipelines de lançamento, escolha o botão **+Novo** e clique em **+Novo pipeline de lançamento**.  

    ![Adicionar um pipeline de liberação usando o botão + novo pipeline](./media/iot-edge-create-release-pipeline-for-continuous-deployment/add-release-pipeline.png)

2. Quando tiver que selecionar um modelo, escolha começar com um **Trabalho vazio**.

    ![Iniciar com um trabalho vazio para o pipeline de liberação](./media/iot-edge-create-release-pipeline-for-continuous-deployment/start-with-empty-release-job.png)

3. O novo pipeline de lançamento é iniciado com um estágio, chamado **Estágio 1**. Renomeie o estágio 1 como **dev** e trate-o como um pipeline de implantação contínua para seu ambiente de desenvolvimento. Normalmente, os pipelines de implantação contínua têm vários estágios, incluindo **desenvolvimento**, **preparo**e **produção**. Você pode usar nomes diferentes e criar mais com base em sua prática DevOps. Feche a janela de detalhes do estágio depois de renomeá-lo.

   Você também pode renomear o pipeline de liberação selecionando o texto "novo pipeline de liberação" na parte superior.

4. Vincule a versão aos artefatos de build publicados pelo pipeline de build. Clique em **Adicionar** na área de artefatos.

   ![Clique em Adicionar na área artefatos da interface](./media/iot-edge-create-release-pipeline-for-continuous-deployment/add-artifacts.png)

5. Na **página Adicionar um artefato**, selecione **Compilar** como o **tipo de origem**. Escolha o projeto e o pipeline de compilação que você criou. Se desejar, você pode alterar o **alias de origem** para algo mais descritivo. Em seguida, selecione **Adicionar**.

   ![Na página Adicionar um artefato, selecione Adicionar para criar o artefato](./media/iot-edge-create-release-pipeline-for-continuous-deployment/add-artifact.png)

6. Abra os gatilhos de artefato e selecione o botão de alternância para habilitar o gatilho de implantação contínua. Agora, uma nova versão será criada sempre que um novo build estiver disponível.

   ![Abra os gatilhos de artefato e alterne para habilitar o gatilho de implantação contínua](./media/iot-edge-create-release-pipeline-for-continuous-deployment/add-trigger.png)

7. O estágio de **desenvolvimento** é pré-configurado com um trabalho e zero tarefas. No menu pipeline, selecione **tarefas** e escolha o estágio **desenvolvimento** . Selecione o **trabalho do agente** e altere seu **nome de exibição** para p e **r**. Você pode configurar detalhes sobre o trabalho do Agent, mas a tarefa de implantação é insensível à plataforma para que você possa usar qualquer **especificação de agente** no **pool de agentes**escolhido.

   ![Exibir as tarefas para o estágio de desenvolvimento na guia tarefas](./media/iot-edge-create-release-pipeline-for-continuous-deployment/view-stage-tasks.png)

8. No trabalho de QA, selecione o sinal de adição ( **+** ) para adicionar duas tarefas. Pesquise e adicione **Azure IOT Edge** duas vezes.

9. Selecione a primeira tarefa de **Azure IOT Edge** e configure-a com os seguintes valores:

    | Parâmetro | Descrição |
    | --- | --- |
    | Nome de exibição | O nome de exibição é atualizado automaticamente quando o campo de ação é alterado. |
    | Ação | Selecione `Generate deployment manifest`. |
    | .template.jsno arquivo | Especifique o caminho: `$(System.DefaultWorkingDirectory)/Drop/drop/deployment.template.json` . O caminho é publicado a partir do pipeline de compilação. |
    | Plataforma padrão | Selecione o sistema operacional apropriado para seus módulos com base em seu dispositivo de IoT Edge de destino. |
    | Caminho de saída| Coloque o caminho `$(System.DefaultWorkingDirectory)/Drop/drop/configs/deployment.json` . Esse caminho é o arquivo de manifesto de implantação IoT Edge final. |

    Essas configurações ajudam a substituir as URLs de imagem de módulo no `deployment.template.json` arquivo. O **manifesto de implantação de geração** também ajuda a substituir as variáveis pelo valor exato que você definiu no `deployment.template.json` arquivo. No VS/VS Code, você está especificando o valor real em um `.env` arquivo. Em Azure Pipelines, defina o valor na guia **variáveis de pipeline de liberação** . Mova para a guia **variáveis** e configure o nome e o valor da seguinte maneira:

    * **ACR_ADDRESS**: o valor do **servidor de logon** do registro de contêiner do Azure. Você pode recuperar o servidor de Logon da página Visão Geral do seu registro de contêiner no portal do Azure.
    * **ACR_PASSWORD**: sua senha de registro de contêiner do Azure.
    * **ACR_USER**: seu nome de usuário do registro de contêiner do Azure.

    Se você tiver outras variáveis em seu projeto, poderá especificar o nome e o valor nessa guia. O **manifesto de implantação de geração** só pode reconhecer as variáveis que estão em `${VARIABLE}` tipo. Verifique se você está usando esse tipo em seus `*.template.json` arquivos.

    ![Configurar as variáveis para o pipeline de liberação na guia variáveis](./media/iot-edge-create-release-pipeline-for-continuous-deployment/configure-variables.png)

10. Selecione a segunda tarefa de **Azure IOT Edge** e configure-a com os seguintes valores:

    | Parâmetro | Descrição |
    | --- | --- |
    | Nome de exibição | O nome de exibição é atualizado automaticamente quando o campo de ação é alterado. |
    | Ação | Selecione `Deploy to IoT Edge devices`. |
    | Arquivo de implantação | Coloque o caminho `$(System.DefaultWorkingDirectory)/Drop/drop/configs/deployment.json` . Esse caminho é o arquivo IoT Edge arquivo de manifesto de implantação. |
    | Assinatura do Azure | selecione a assinatura que contém seu Hub IoT.|
    | Nome do Hub IoT | Selecione seu Hub IoT.|
    | Escolher um/vários dispositivos | Escolha se deseja que o pipeline de liberação seja implantado em um ou vários dispositivos. Se quiser implantar em um único dispositivo, insira a **ID do dispositivo do IoT Edge**. Se estiver implantando em vários dispositivos, especifique a **condição de destino** do dispositivo. A condição de destino é um filtro para corresponder a um conjunto de dispositivos IoT Edge no Hub IoT. Se você quiser usar marcas de dispositivo como a condição, será necessário atualizar as marcas de dispositivos correspondentes com o dispositivo de Hub IoT. Atualize a **ID de implantação do IoT Edge** e a **prioridade de implantação do IoT Edge** nas configurações avançadas. Para saber mais sobre como criar uma implantação em vários dispositivos, confira [Entender as implantações automáticas do IoT Edge](../articles/iot-edge/module-deployment-monitoring.md). |
    | ID do dispositivo ou condição de destino | Dependendo da seleção anterior, especifique uma ID do dispositivo ou uma [condição de destino](../articles/iot-edge/module-deployment-monitoring.md#target-condition) para implantar em vários dispositivos. |
    | Avançado | Para a ID de implantação do IoT Edge, especifique `$(System.TeamProject)-$(Release.EnvironmentName)` . Essa variável mapeia o projeto e o nome da versão com sua ID de implantação IoT Edge. |

    ![Adicionar tarefas de Azure IoT Edge para o estágio de desenvolvimento](./media/iot-edge-create-release-pipeline-for-continuous-deployment/add-quality-assurance-task.png)

11. Selecione **Salvar** para salvar as alterações no novo pipeline de lançamento. Retorne ao modo de exibição de pipeline selecionando a guia **pipeline** no menu.
