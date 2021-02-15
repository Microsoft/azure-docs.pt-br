---
title: Exibição de execução de vértice no Data Lake Tools para Visual Studio
description: Este artigo descreve como usar o modo de exibição de Execução de Vértice para examinar trabalhos do Data Lake Analytics.
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 10/13/2016
ms.openlocfilehash: 3fba7bdaa5db1d812fbcd479e5f1eab50c8d1032
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92215851"
---
# <a name="use-the-vertex-execution-view-in-data-lake-tools-for-visual-studio"></a>Usar o Modo de Exibição de Execução de Vértice nas Ferramentas do Data Lake para Visual Studio
Saiba como usar o Modo de Exibição de Execução de Vértice para examinar trabalhos do Data Lake Analytics.


## <a name="open-the-vertex-execution-view"></a>Abrir o Modo de Exibição de Execução de Vértice
Abra um trabalho de U-SQL em Ferramentas do Data Lake para Visual Studio. Clique em **Exibição de Execuções de Vértice** no canto inferior esquerdo. Talvez você receba uma solicitação para carregar perfis primeiro e isso pode demorar algum tempo dependendo de sua conectividade de rede.

![Captura de tela que mostra a exibição de execução de vértice das ferramentas de Data Lake Analytics](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-open-vertex-execution-view.png)

## <a name="understand-vertex-execution-view"></a>Noções básicas do Modo de Exibição de Execução de Vértice
A Exibição de Execuções de Vértice tem três partes:

![Captura de tela que mostra o modo de exibição de execução de vértice com os painéis "seletor de vértice" e central-superior e centro-inferior realçados.](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view.png)

O **seletor de vértice** à esquerda permite selecionar vértices pelos recursos (como 10 principais dados lidos ou optar por estágio). Um dos filtros mais usados é para ver os **vértices no caminho crítico**. O **Caminho crítico** é a cadeia mais longa de vértices de um trabalho U-SQL. Entender o caminho crítico é útil para otimizar seus trabalhos verificando quais vértice demoram mais.
  
![Captura de tela que mostra o painel superior do modo de exibição de execução de vértice que exibe o "status de execução de todos os vértices".](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view-pane2.png)

O painel central superior mostra o **status de execução de todos os vértices**.
  
![Captura de tela que mostra a exibição de execução de vértice no painel inferior central que exibe informações sobre cada vértice.](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view-pane3.png)

O painel central inferior mostra informações sobre cada vértice:
* Nome do Processo: o nome da instância do vértice. Ele é composto por diferentes partes em StageName|VertexName|VertexRunInstance. Por exemplo, o vértice de SV7_Split[62].v1 representa a segunda instância em execução (.v1, índice que começa em 0) do vértice número 62 no estágio SV7_Split.
* Leitura/Gravação Total de Dados: os dados foram lidos/gravados por esse vértice.
* Estado/Status de Saída: o status final quando o vértice é encerrado.
* Código de Saída/Tipo de Falha: o erro no momento da falha do vértice.
* Motivo da Criação: por que o vértice foi criado.
* Latência do Recurso/Latência do processo/ Latência da Fila de PN: o tempo necessário para o vértice aguardar recursos, processar dados e permanecer na fila.
* GUID do Processo/Criador: o GUID do vértice em execução no momento ou de seu criador.
* Versão: a enésima instância do vértice em execução (o sistema pode agendar novas instâncias de um vértice por vários motivos, por exemplo, para failover, redundância de computação etc.)
* Hora da Criação da Versão.
* Hora de Início da Criação do Processo/Hora de Enfileiramento do Processo/Hora de Início do processo/Hora de Conclusão do Processo: quando o processo do vértice começa a criação; quando o processo do vértice começa a enfileirar; quando o processo do vértice especificado começa; quando o vértice especificado é concluído.

## <a name="next-steps"></a>Próximas etapas
* Para registrar em log informações de diagnóstico, consulte [acessando logs de diagnóstico para Azure data Lake Analytics](data-lake-analytics-diagnostic-logs.md)
* Para ver uma consulta mais complexa, consulte [Analisar logs de site usando a Análise Data Lake do Azure](data-lake-analytics-analyze-weblogs.md).
* Para ver detalhes do trabalho, confira [Usar o Navegador de Trabalhos e o Modo de Exibição de Trabalho para trabalhos do Azure Data Lake Analytics](data-lake-analytics-data-lake-tools-view-jobs.md)
