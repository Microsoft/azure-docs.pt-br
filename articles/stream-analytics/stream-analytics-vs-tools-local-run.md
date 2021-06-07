---
title: Testar Azure Stream Analytics consultas localmente no Visual Studio
description: Este artigo descreve como testar as consultas localmente com as Ferramentas do Azure Stream Analytics para o Microsoft Visual Studio.
author: su-jie
ms.author: sujie
ms.service: stream-analytics
ms.topic: how-to
ms.date: 07/10/2018
ms.openlocfilehash: b856826761f355e896cae48aa4a6fb62f5947e0b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98019917"
---
# <a name="test-stream-analytics-queries-locally-with-visual-studio"></a>Testar as consultas do Stream Analytics localmente com o Microsoft Visual Studio

Você pode usar as ferramentas do Azure Stream Analytics para Visual Studio para testar seus trabalhos do Stream Analytics localmente com dados de exemplo ou [dados dinâmicos](stream-analytics-live-data-local-testing.md). 

Use este [Início Rápido](stream-analytics-quick-create-vs.md) para aprender a criar um trabalho de Stream Analytics usando o Visual Studio.

## <a name="test-your-query"></a>Testar a consulta

No seu projeto Azure Stream Analytics, clique duas vezes em **Script.asaql** para abrir o script no editor. Você pode primeiro compilar a consulta para ver se há algum erro de sintaxe. O editor de consulta dá suporte ao IntelliSense, a cores de sintaxe e a um marcador de erro.

![Editor de consultas](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-query-01.png)
 
### <a name="add-local-input"></a>Adicionar entrada local

Para validar sua consulta em relação a dados estáticos locais, clique com botão direito de entrada e selecione **Adicionar entrada local**.
   
![Captura de tela que realça a opção de menu Adicionar entrada local.](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-add-local-input-01.png)
   
Na janela pop-up, selecione os dados de exemplo do caminho local e **Salvar**.
   
![Adicionar entrada local](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-add-local-input-02.png)
   
Um arquivo chamado **local_EntryStream.json** é adicionado automaticamente à pasta de entradas.
   
![Lista de arquivos da pasta de entrada local](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-add-local-input-03.png)
   
Selecione **Executar Localmente** no editor de consultas. Ou você pode pressionar F5.
   
![Executar Localmente](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-local-run-01.png)
   
A saída pode ser exibida em um formato de tabela diretamente do Microsoft Visual Studio.

![Saída em formato de tabela](./media/stream-analytics-vs-tools-local-run/stream-analytics-for-vs-local-result.png)

Você pode encontrar o caminho de saída da saída do console. Pressione qualquer tecla para abrir a pasta de resultados.
   
![Execução local](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-local-run-02.png)
   
Verifique os resultados na pasta local.
   
![Resultado de pasta local](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-local-run-03.png)
   

### <a name="sample-input"></a>Entrada de exemplo
Você também pode coletar a amostragem de dados de entrada de fontes de entrada para um arquivo local. Clique com o botão direito do mouse no arquivo de configuração de entrada e selecione **dados de exemplo**. 

![Dados de Amostra](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-sample-data-01.png)

Você pode apenas realizar amostra de streaming de dados dos Hubs de Eventos ou Hubs IoT. Não há suporte para outras fontes de entrada. Na caixa de diálogo pop-up, preencha o caminho local para salvar os dados e selecione **Exemplo**.

![Configuração de dados de exemplo](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-sample-data-02.png)
 
Você pode ver o progresso na janela de **saída** . 

![Saída de dados de exemplo](./media/stream-analytics-vs-tools-local-run/stream-analytics-tools-for-vs-sample-data-03.png)

## <a name="next-steps"></a>Próximas etapas

* [Início Rápido: criar um trabalho do Stream Analytics usando o Microsoft Visual Studio](stream-analytics-quick-create-vs.md)
* [Use o Microsoft Visual Studio para visualizar os trabalhos do Azure Stream Analytics](stream-analytics-vs-tools.md)
* [Testar dados dinâmicos localmente usando as ferramentas do Azure Stream Analytics para Visual Studio (versão prévia)](stream-analytics-live-data-local-testing.md)
* [Integrar e desenvolver continuamente com ferramentas do Stream Analytics](stream-analytics-tools-for-visual-studio-cicd.md)
