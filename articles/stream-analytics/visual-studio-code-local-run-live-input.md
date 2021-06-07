---
title: Testar Azure Stream Analytics consultas localmente contra entrada de fluxo ao vivo usando Visual Studio Code
description: Este artigo descreve como testar consultas localmente em relação à entrada de fluxo ao vivo usando Azure Stream Analytics Tools para Visual Studio Code.
ms.service: stream-analytics
author: su-jie
ms.author: sujie
ms.date: 11/14/2019
ms.topic: how-to
ms.openlocfilehash: dd4966ee75e9dc0ff401823e4291f8d299c8893c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93122884"
---
# <a name="test-stream-analytics-queries-locally-against-live-stream-input-by-using-visual-studio-code"></a>Testar Stream Analytics consultas localmente contra entrada de fluxo ao vivo usando Visual Studio Code

Você pode usar as ferramentas de Azure Stream Analytics para Visual Studio Code para testar seus trabalhos de Stream Analytics localmente em relação à entrada de fluxo ao vivo. A entrada pode vir de uma fonte como hubs de eventos do Azure ou Hub IoT do Azure. Os resultados de saída são enviados como arquivos JSON para uma pasta em seu projeto chamada **LocalRunOutputs**.

## <a name="prerequisites"></a>Pré-requisitos

* Instale o [SDK do .NET Core](https://dotnet.microsoft.com/download) e reinicie o Visual Studio Code.

* Use [este guia de início rápido](quick-create-visual-studio-code.md) para aprender a criar um trabalho de Stream Analytics usando Visual Studio Code.

## <a name="define-a-live-stream-input"></a>Definir uma entrada de fluxo ao vivo

1. Clique com o botão direito do mouse na pasta **Entradas** do projeto do Stream Analytics. Em seguida, selecione **ASA: Adicionar Entrada** no menu de contexto.

   ![Adicionar entrada por meio da pasta Entradas](./media/quick-create-visual-studio-code/add-input-from-inputs-folder.png)

   Você também pode selecionar **Ctrl + Shift + P** para abrir a paleta de comandos e inserir **asa: Adicionar entrada**.

   ![Adicionar uma entrada do Stream Analytics no Visual Studio Code](./media/quick-create-visual-studio-code/add-input.png)

2. Escolha um tipo de fonte de entrada na lista suspensa.

   ![Selecionar o hub IoT como a opção de entrada](./media/quick-create-visual-studio-code/iot-hub.png)

3. Se você adicionou a entrada por meio da paleta de comandos, escolha o script de consulta do Stream Analytics que usará a entrada. Ele deverá ser populado automaticamente com o caminho de arquivo para **myASAproj.asaql**.

   ![Selecionar um script do Stream Analytics no Visual Studio Code](./media/quick-create-visual-studio-code/asa-script.png)

4. Escolha **Selecionar em suas Assinaturas do Azure** no menu suspenso.

    ![Seleção nas assinaturas](./media/quick-create-visual-studio-code/add-input-select-subscription.png)

5. Configure o arquivo JSON recém-gerado. Use o recurso CodeLens para ajudar você a inserir uma cadeia de caracteres, fazer uma seleção em uma lista suspensa ou alterar o texto diretamente no arquivo. A captura de tela a seguir mostra **Selecionar em suas Assinaturas** como um exemplo.

   ![Configurar a entrada no Visual Studio Code](./media/quick-create-visual-studio-code/configure-input.png)

## <a name="preview-input"></a>Visualizar a entrada

Para verificar se os dados de entrada estão chegando, selecione **Visualizar dados** em seu arquivo de configuração de entrada ao vivo na linha superior. Alguns dados de entrada vêm de um hub IoT e são mostrados na janela de visualização. A visualização pode levar alguns segundos para aparecer.

 ![Visualizar a entrada dinâmica](./media/quick-create-visual-studio-code/preview-live-input.png)

## <a name="run-queries-locally"></a>Executar consultas localmente

Volte para o editor de consultas e selecione **executar localmente**. Em seguida, selecione **usar entrada ao vivo** na lista suspensa.

![Selecione "executar localmente" no editor de consultas](./media/vscode-local-run/run-locally.png)

![Selecione "usar entrada ao vivo"](./media/vscode-local-run-live-input/run-locally-use-live-input.png)

O resultado é mostrado na janela direita e atualizado a cada 3 segundos. Você pode selecionar **executar** para testar novamente. Você também pode selecionar **abrir na pasta** para ver os arquivos de resultado no explorador de arquivos e abri-los com Visual Studio Code ou uma ferramenta como o Excel. Observe que os arquivos de resultado estão disponíveis apenas no formato JSON.

A hora padrão para o trabalho começar a criar a saída é definida como **Now**. Você pode personalizar o tempo selecionando o botão **hora de início da saída** na janela resultado.

![Exibir resultado da execução local](./media/vscode-local-run-live-input/vscode-livetesting.gif)

## <a name="next-steps"></a>Próximas etapas

* [Explorar Azure Stream Analytics trabalhos com Visual Studio Code (versão prévia)](visual-studio-code-explore-jobs.md)

* [Configurar pipelines da CI/CD usando o pacote npm](./cicd-overview.md)