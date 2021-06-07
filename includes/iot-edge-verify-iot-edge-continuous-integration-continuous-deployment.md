---
author: v-tcassi
ms.service: iot-edge
ms.topic: include
ms.date: 08/26/2020
ms.author: v-tcassi
ms.openlocfilehash: b5450e4846c3c49c89830ae65c50a95ee0c8d6eb
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104803251"
---
## <a name="verify-iot-edge-cicd-with-the-build-and-release-pipelines"></a>Verificar o CI/CD do IoT Edge com os pipelines de lançamento e de build

Para disparar um trabalho de build, você pode efetuar push da confirmação do repositório do código-fonte ou dispará-lo manualmente. Nesta seção, dispare manualmente o pipeline de CI/CD para testar o funcionamento dele. Em seguida, verifique se a implantação é bem-sucedida.

1. No menu do painel esquerdo, selecione **pipelines** e abra o pipeline de compilação que você criou no início deste artigo.

2. Você pode disparar um trabalho de compilação em seu pipeline de compilação selecionando o botão **executar pipeline** no canto superior direito.

    ![Disparar manualmente o pipeline de compilação usando o botão Executar pipeline](./media/iot-edge-verify-iot-edge-continuous-integration-continuous-deployment/manual-trigger.png)

3. Examine as configurações de **pipeline de execução** . Em seguida, selecione **executar**.

    ![Especifique as opções de pipeline de execução e selecione executar](./media/iot-edge-verify-iot-edge-continuous-integration-continuous-deployment/run-pipeline-settings.png)

4. Selecione o **trabalho do agente 1** para assistir ao progresso da execução. Você pode examinar os logs da saída do trabalho selecionando o trabalho. 

    ![Examinar a saída do log do trabalho](./media/iot-edge-verify-iot-edge-continuous-integration-continuous-deployment/view-job-run.png)

5. Se o pipeline de compilação for concluído com êxito, ele disparará uma liberação para o estágio de **desenvolvimento** . A versão de **desenvolvimento** bem-sucedida cria IOT Edge implantação para dispositivos de IOT Edge de destino.

    ![Versão para desenvolvimento](./media/iot-edge-verify-iot-edge-continuous-integration-continuous-deployment/pending-approval.png)

6. Clique em estágio de **desenvolvimento** para ver os logs de liberação.

    ![Logs de lançamento](./media/iot-edge-verify-iot-edge-continuous-integration-continuous-deployment/release-logs.png)

7. Se o seu pipeline estiver falhando, comece examinando os logs. Você pode exibir os logs navegando até o resumo de execução de pipeline e selecionando o trabalho e a tarefa. Se uma determinada tarefa estiver falhando, verifique os logs dessa tarefa. Para obter instruções detalhadas sobre como configurar e usar logs, consulte [examinar logs para diagnosticar problemas de pipeline](/azure/devops/pipelines/troubleshooting/review-logs).
