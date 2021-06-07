---
title: Visualize os trabalhos do Azure Stream Analytics no Microsoft Visual Studio
description: Saiba como exibir, iniciar e parar, testar conexões, verificar resultados e exportar seus trabalhos de Azure Stream Analytics usando o Visual Studio.
author: su-jie
ms.author: sujie
ms.service: stream-analytics
ms.topic: how-to
ms.date: 07/10/2018
ms.openlocfilehash: 4b568fa8d9e2fb8bd00acb27b0dc3f7c45c09c57
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98019118"
---
# <a name="use-visual-studio-to-view-azure-stream-analytics-jobs"></a>Use o Microsoft Visual Studio para visualizar os trabalhos do Azure Stream Analytics

Ferramentas de Stream Analytics do Azure para o Microsoft Visual Studio tornam fácil para os desenvolvedores gerenciarem seus trabalhos do Stream Analytics diretamente do IDE. Com as ferramentas do Azure Stream Analytics é possível:
- [Criar novos trabalhos](stream-analytics-quick-create-vs.md)
- Iniciar, parar, e [monitorar trabalhos](stream-analytics-monitor-jobs-use-vs.md)
- Veriricar resultados de trabalho
- Exportar os trabalhos existentes para um projeto
- Testar conexões de entrada e saídas
- [Executar consultas localmente](stream-analytics-vs-tools-local-run.md)

Saber como [instalar as ferramentas do Azure Stream Analytics Tools para o Microsoft Visual Studio](stream-analytics-tools-for-visual-studio-install.md).

## <a name="explore-the-job-view"></a>Explrar a exibição do trabalho

Você pode usar o modo de exibição de trabalho para interagir com trabalhos do Azure Stream Analytics do Microsoft Visual Studio.

### <a name="open-the-job-view"></a>Abrir a exibição do trabalho

1. No **Gerenciador de Servidores**, selecione **Trabalhos do Stream Analytics** e, em seguida, selecione **Atualizar**. Seu trabalho deve aparecer em **Trabalhos do Stream Analytics**.

    ![Lista de gerenciador de servidores do Stream Analytics](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-list-jobs-01.png)

2. Expanda o nó de trabalho e clique duas vezes no nó **Exibição de Trabalho** para abrir uma exibição do trabalho.
    
   ![Nó de trabalho expandido](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-job-view-01.png)

### <a name="start-and-stop-jobs"></a>Iniciar e parar trabalhos

Os trabalhos do Stream Analytics do Azure podem ser gerenciados totalmente da exibição do trabalho no Microsoft Visual Studio. Use os controles para iniciar, parar ou excluir um trabalho.
    
   ![Controles de trabalho do Stream Analytics](./media/stream-analytics-vs-tools/azure-stream-analytics-job-view-controls.png)

## <a name="check-job-results"></a>Veriricar resultados de trabalho

Atualmente, as ferramentas do Stream Analytics para o Visual Studio dão suporte a visualização de saída para o Azure Data Lake Storage e o armazenamento de blobs. Para exibir o resultado, simplesmente clique duas vezes no nó de saída do diagrama de trabalho na **Exibição de Trabalho** e insira as credenciais apropriadas.

   ![Saída do Trabalho do Stream Analytics](./media/stream-analytics-vs-tools/stream-analytics-blob-preview.png)

## <a name="export-jobs-to-a-project"></a>Exportar trabalhos para um projeto

Há duas maneiras que você pode usar para exportar um trabalho para um projeto.

1. No **Gerenciador de Servidores**, no nó Trabalhos do Stream Analytics, clique com o botão direito do mouse no nó do trabalho. Selecione **Exportar para Novo Projeto do Stream Analytics**.
    
   ![Exportar trabalhos para o projeto](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-export-job-01.png)
    
    O projeto gerado aparece no **Gerenciador de Soluções**.
    
   ![Gerenciador de soluções](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-export-job-02.png)

2. Na exibição de trabalho, selecione **Gerar Projeto**.
    
   ![Gerar Projeto da exibição de trabalho](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-export-job-03.png)

## <a name="test-connections"></a>Conexões de teste

Conexões de entrada e saídas podem ser testadas da **Exibição de Trabalho** selecionando uma opção da lista suspensa de **Conexão de teste**.

   ![Lista suspensa da Conexão de Teste](./media/stream-analytics-vs-tools/stream-analytics-test-connection-dropdown.png)

Os resultados da **Conexão de Teste** são exibidos na janela de **Saída**.

   ![Testar os resultados de conexão](./media/stream-analytics-vs-tools/stream-analytics-test-connection-results.png)

## <a name="next-steps"></a>Próximas etapas

* [Monitorar e gerenciar trabalhos do Azure Stream Analytics usando o Microsoft Visual Studio](stream-analytics-monitor-jobs-use-vs.md)
* [Início Rápido: criar um trabalho do Stream Analytics usando o Microsoft Visual Studio](stream-analytics-quick-create-vs.md)
* [Integrar e desenvolver continuamente com ferramentas do Stream Analytics](stream-analytics-tools-for-visual-studio-cicd.md)
