---
title: Azure Monitor visualizações de bloco da pasta de trabalho
description: Saiba mais sobre todas as visualizações de bloco de pasta de trabalho Azure Monitor.
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: lagayhar
ms.openlocfilehash: 07de9fcd01559e59a1bcd5d8928075aebcf79cc7
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100605429"
---
# <a name="tile-visualizations"></a>Visualizações do bloco

Os blocos são uma maneira útil de apresentar dados resumidos em pastas de trabalho. A imagem abaixo mostra um caso de uso comum de blocos com o resumo de nível de aplicativo sobre uma grade detalhada.

[![Captura de tela da exibição de resumo do bloco](./media/workbooks-tile-visualizations/tiles-summary.png)](./media/workbooks-tile-visualizations/tiles-summary.png#lightbox)

Suporte a blocos da pasta de trabalho mostrando um título, subtítulo, texto grande, ícones, gradientes baseados em métrica, linha/barras do Spark, rodapé, etc.

## <a name="adding-a-tile"></a>Adicionando um bloco

1. Alterne a pasta de trabalho para o modo de edição clicando no item _Editar_ barra de ferramentas.
2. Selecione **Adicionar** e *Adicionar consulta* para adicionar um controle de consulta de log à pasta de trabalho.
3. Selecione o tipo de consulta como **log**, tipo de recurso (por exemplo, Application insights) e os recursos a serem direcionados.
4. Use o editor de consultas para inserir o KQL para sua análise.

    ```kusto
    requests
    | summarize Requests = count() by appName, name
    | top 7 by Requests desc
    ```

5. Defina tamanho como **completo**.
6. Defina a visualização como **blocos**.
7. Selecione o botão **configurações de bloco** para abrir o painel configurações.
    1. No *título*, defina:
        * Usar coluna: `name` .
    2. *À esquerda*, defina:
        * Usar coluna: `Requests` .
        * Renderizador de coluna: `Big Number` .
        * Paleta de cores: `Green to Red`
        * Valor mínimo: `0` .
    3. Na *parte inferior*, defina:
        * Usar coluna: `appName` .
8. Selecione o botão **salvar e fechar** na parte inferior do painel.

[![Captura de tela da exibição de Resumo de bloco com as configurações de consulta e bloco acima.](./media/workbooks-tile-visualizations/tile-settings.png)](./media/workbooks-tile-visualizations/tile-settings.png#lightbox)

Os blocos no modo de leitura:

[![Captura de tela da exibição de Resumo de bloco no modo de leitura.](./media/workbooks-tile-visualizations/tiles-read-mode.png)](./media/workbooks-tile-visualizations/tiles-read-mode.png#lightbox)

## <a name="spark-lines-in-tiles"></a>Linhas do Spark em blocos

1. Alterne a pasta de trabalho para o modo de edição clicando no item _Editar_ barra de ferramentas.
2. Adicione um parâmetro de intervalo de tempo chamado `TimeRange` .
    1. Selecione **Adicionar** e *adicionar parâmetros*.
    2. No controle de parâmetro, selecione **Adicionar parâmetro**.
    3. Insira `TimeRange` no campo *nome do parâmetro* e escolha `Time range picker` para *tipo de parâmetro*.
    4. Selecione **salvar** na parte superior do painel e, em seguida, selecione **edição concluída** no controle de parâmetro.
3. Selecione **Adicionar** e *Adicionar consulta* para adicionar um controle de consulta de log abaixo do controle de parâmetro.
4. Selecione o tipo de consulta como **log**, tipo de recurso (por exemplo, Application insights) e os recursos a serem direcionados.
5. Use o editor de consultas para inserir o KQL para sua análise.

    ```kusto
    let topRequests = requests
    | summarize Requests = count() by appName, name
    | top 7 by Requests desc;
    let topRequestNames = topRequests | project name;
    requests
    | where name in (topRequestNames)
    | make-series Trend = count() default = 0 on timestamp from {TimeRange:start} to {TimeRange:end} step {TimeRange:grain} by name
    | join (topRequests) on name
    | project-away name1, timestamp
    ```

6. Selecione **Executar Consulta**. (Certifique-se de definir `TimeRange` como um valor de sua escolha antes de executar a consulta.)
7. Defina a *Visualização* como "blocos".
8. Defina o *tamanho* como "completo".
9. Selecione **configurações de bloco**.
    1. No *bloco*, defina:
        * Usar coluna: `name` .
    2. No *subbloco*, defina:
        *  Usar coluna: `appNAme` .
    3. *À esquerda*, defina:
        *  Usar coluna: `Requests` .
        * Renderizador de coluna: `Big Number` .
        * Paleta de cores: `Green to Red` .
        * Valor mínimo: `0` .
    4. Na *parte inferior*, defina:
        * Usar coluna: `Trend` .
        * Renderizador de coluna: `Spark line` .
        * Paleta de cores: `Green to Red` .
        * Valor mínimo: `0` .
10. Selecione **salvar e fechar** na parte inferior do painel.

![Captura de tela da visualização de bloco com uma linha Spark](./media/workbooks-tile-visualizations/spark-line.png)

## <a name="tile-sizes"></a>Tamanhos de bloco

O autor tem uma opção para definir a largura do bloco nas configurações do bloco.

* `fixed` (padrão)

    O comportamento padrão dos blocos é ter a mesma largura fixa, aproximadamente 160 pixels de largura, mais o espaço em volta dos blocos.

    ![Captura de tela exibindo blocos de largura fixa](./media/workbooks-tile-visualizations/tiles-fixed.png)
* `auto`

    Cada título será reduzido ou ampliado para se ajustar ao seu conteúdo no entanto, os blocos são limitados à largura da exibição de blocos (sem rolagem horizontal).

    ![Captura de tela exibindo blocos de largura automática](./media/workbooks-tile-visualizations/tiles-auto.png)
* `full size`

    Cada título sempre será a largura total da exibição de blocos, um título por linha.

     ![Captura de tela exibindo blocos de largura de tamanho completo](./media/workbooks-tile-visualizations/tiles-full.png)

## <a name="next-steps"></a>Próximas etapas

* Os blocos também dão suporte ao renderizador de barra de composição. Para saber mais, visite a [documentação da barra de composição](workbooks-composite-bar.md).
* Para saber mais sobre parâmetros de tempo, como `TimeRange` visitar [parâmetros de tempo da pasta de trabalho documentação](workbooks-time.md).