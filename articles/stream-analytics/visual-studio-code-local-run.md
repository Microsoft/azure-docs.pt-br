---
title: Testar um trabalho de Azure Stream Analytics localmente com dados de exemplo usando Visual Studio Code
description: Este artigo descreve como testar consultas localmente com dados de exemplo usando ferramentas de Azure Stream Analytics para Visual Studio Code.
ms.service: stream-analytics
author: su-jie
ms.author: sujie
ms.date: 11/10/2019
ms.topic: how-to
ms.openlocfilehash: bbd83fb3ef3225fc19c48bb4c5962d6559cf32f8
ms.sourcegitcommit: d488a97dc11038d9cef77a0235d034677212c8b3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/21/2020
ms.locfileid: "97708442"
---
# <a name="test-stream-analytics-queries-locally-with-sample-data-using-visual-studio-code"></a>Testar consultas do Stream Analytics localmente com os dados de exemplo usando o Visual Studio Code

Você pode usar as ferramentas de Azure Stream Analytics para Visual Studio Code para testar seus trabalhos de Stream Analytics localmente com dados de exemplo. Você pode encontrar os resultados da consulta em arquivos JSON na pasta **LocalRunOutputs** do seu projeto.

## <a name="prerequisites"></a>Pré-requisitos

* Instale o [SDK do .NET Core](https://dotnet.microsoft.com/download) e reinicie o Visual Studio Code.

* Use este guia de [início rápido](quick-create-visual-studio-code.md) para aprender a criar um trabalho de Stream Analytics usando Visual Studio Code.

## <a name="prepare-sample-data"></a>Preparar os dados de exemplo

Primeiro, você precisa preparar arquivos de dados de entrada de exemplo. Se você já tiver alguns arquivos de dados de exemplo em seu computador, poderá ignorar essa etapa e passar para a próxima.

1. Clique em **Visualizar dados** no arquivo de configuração de entrada na linha superior. Alguns dados de entrada serão buscados do Hub IoT e mostrados na janela de visualização. Observe que isso pode demorar um pouco.

2. Depois que os dados forem mostrados, clique em **salvar como** para salvar os dados em um arquivo local.

 ![Visualizar a entrada dinâmica](./media/quick-create-visual-studio-code/preview-live-input.png)

## <a name="define-a-local-input"></a>Definir uma entrada local

1. Clique em **input.jsna** pasta entradas em seu projeto Stream Analytics. Em seguida, selecione **Adicionar entrada local** na linha superior.

    ![Adicionar entrada local do projeto](./media/quick-create-visual-studio-code/add-input-from-project.png)

    Você também pode usar **Ctrl + Shift + P** para abrir a paleta de comandos e inserir **asa: Adicionar entrada**.

   ![Adicionar entrada do Stream Analytics no VS Code](./media/quick-create-visual-studio-code/add-input.png)

2. Selecione **entrada local**.

    ![Adicionar entrada local do ASA no Visual Studio Code](./media/vscode-local-run/add-local-input.png)

3. Selecione **+ nova entrada local**.

    ![Adicionar uma nova entrada local do ASA no Visual Studio Code](./media/vscode-local-run/add-new-local-input.png)

4. Insira o mesmo alias de entrada que você usou em sua consulta.

    ![Adicionar um novo alias de entrada local do ASA](./media/vscode-local-run/new-local-input-alias.png)

5. NaLocalInput_Input.jsgerada recentemente **no** arquivo, insira o caminho do arquivo no qual o arquivo de dados local está localizado.

    ![Insira o caminho do arquivo local no Visual Studio](./media/vscode-local-run/local-file-path.png)

6. Selecione **Visualizar dados** para visualizar os dados de entrada. O tipo de serialização para seus dados será detectado automaticamente se seu JSON ou CSV. Use o seletor para exibir seus dados em formato de **tabela** ou **bruto** . A tabela a seguir é um exemplo de dados no **formato de tabela**:

     ![Visualizar dados locais em formato de tabela](./media/vscode-local-run/local-file-preview-table.png)

    A tabela a seguir é um exemplo de dados no **formato bruto**:

    ![Visualizar dados locais em formato bruto](./media/vscode-local-run/local-file-preview-raw.png)

## <a name="run-queries-locally"></a>Executar consultas localmente

Volte para o editor de consultas e selecione **executar localmente**. Em seguida, selecione **usar entrada local** na lista suspensa.

![Selecione executar localmente no editor de consultas](./media/vscode-local-run/run-locally.png)

![Usar entrada local](./media/vscode-local-run/run-locally-use-local-input.png)

O resultado é mostrado na janela direita. Você pode clicar em **executar** para testar novamente. Você também pode selecionar **abrir na pasta** para ver os arquivos de resultado no explorador de arquivos e abri-los ainda mais com outras ferramentas. Observe que os arquivos de resultado estão disponíveis apenas no formato JSON.

![Exibir resultado da execução local](./media/vscode-local-run/run-locally-result.png)

## <a name="next-steps"></a>Próximas etapas

* [Testar Azure Stream Analytics trabalhos localmente com a entrada ao vivo usando Visual Studio Code](visual-studio-code-local-run-live-input.md)

* [Explorar Azure Stream Analytics trabalhos com Visual Studio Code (versão prévia)](visual-studio-code-explore-jobs.md)
