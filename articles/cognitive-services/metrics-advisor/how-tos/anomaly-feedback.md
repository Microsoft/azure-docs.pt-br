---
title: Fornecer comentários de anomalias para o serviço do Orientador de métricas
titleSuffix: Azure Cognitive Services
description: Saiba como enviar comentários sobre anomalias encontradas por sua instância do supervisor de métricas e ajustar os resultados.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 11/24/2020
ms.author: mbullwin
ms.openlocfilehash: 96a6dfebdd0047d5a6f4ff3e295ab96dd8c7226a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96184780"
---
# <a name="provide-anomaly-feedback"></a>Fornecer comentários de anomalias

Os comentários do usuário são um dos métodos mais importantes para descobrir defeitos no sistema de detecção de anomalias. Aqui, fornecemos uma maneira para os usuários marcarem resultados de detecção incorretos diretamente em uma série temporal e aplicaremos os comentários imediatamente. Dessa forma, um usuário pode ensinar ao sistema de detecção de anomalias como fazer a detecção de anomalias para uma série temporal específica por meio de interações ativas. 

> [!NOTE]
> Atualmente, os comentários afetarão apenas os resultados da detecção de anomalias por **detecção inteligente** , mas não **limite rígido** e **limite de alteração**.

## <a name="how-to-give-time-series-feedback"></a>Como dar comentários de série temporal

Você pode fornecer comentários da página de detalhes da métrica em qualquer série. Basta selecionar qualquer ponto e você verá a caixa de diálogo de comentários abaixo. Ele mostra as dimensões da série que você escolheu. Você pode selecionar novamente valores de dimensão ou até mesmo remover alguns deles para obter um lote de dados de série temporal. Depois de escolher a série temporal, selecione o botão **Adicionar** para adicionar os comentários, há quatro tipos de comentários que você pode dar. Para acrescentar vários itens de comentários, selecione o botão **salvar** depois de concluir as anotações.

:::image type="content" source="../media/feedback/click-on-any-point.png" alt-text="Grafo com dados de série temporal com uma linha azul e pontos vermelhos em vários pontos. Caixa vermelha ao redor de um ponto com o texto: selecione qualquer ponto":::

:::image type="content" source="../media/feedback/select-or-remove.png" alt-text="Caixa de diálogo adicionar comentários com duas dimensões e a opção de selecionar ou remover dimensões e adicionar comentários.":::

### <a name="mark-the-anomaly-point-type"></a>Marcar o tipo de ponto de anomalias

Conforme mostrado na imagem abaixo, a caixa de diálogo de comentários preencherá automaticamente o carimbo de data/hora do seu ponto escolhido, embora você possa editar esse valor. Em seguida, selecione se deseja identificar este item como um `Anomaly` , `NotAnomaly` ou `AutoDetect` .

:::image type="content" source="../media/feedback/anomaly-value.png" alt-text="Menu suspenso com opções de anomalia, não anomalia e detecção automática":::

A seleção aplicará seus comentários ao processamento de detecção de anomalias futuro da mesma série. Os pontos processados não serão recalculados. Isso significa que se você marcou uma anomalia como não anomalia, vamos suprimir anomalias semelhantes no futuro e, se você marcou um `NotAnomaly` ponto como `Anomaly` , teremos a tendência de detectar pontos semelhantes `Anomaly` no futuro. Se `AutoDetect` for escolhido, todos os comentários anteriores sobre o mesmo ponto serão ignorados no futuro.

## <a name="provide-feedback-for-multiple-continuous-points"></a>Fornecer comentários para vários pontos contínuos 

Se desejar fornecer comentários de anomalias para vários pontos contínuos ao mesmo tempo, selecione o grupo de pontos que você deseja anotar. Você verá o intervalo de tempo escolhido preenchido automaticamente quando fornecer comentários de anomalias.

:::image type="content" source="../media/feedback/continuous-anomaly-feedback.png" alt-text="Menu de comentários de anomalias com anomalias selecionadas e um intervalo de tempo específico":::

Para exibir se um ponto individual é afetado por seus comentários de anomalias, ao navegar por uma série temporal, selecione um único ponto. Se seu resultado de detecção de anomalias tiver sido alterado por comentários, a dica de ferramenta mostrará **afetados por comentários: verdadeiro**. Se ele mostrar **afetado por comentários: false**, isso significa que um cálculo de comentários de anomalias foi realizado para esse ponto, mas o resultado da detecção de anomalias não deve ser alterado.

:::image type="content" source="../media/feedback/affected-by-feedback.png" alt-text="Exibição de dica de ferramenta com as palavras: afetadas por comentários: verdadeiro, realçado em vermelho":::

Há algumas situações em que não sugerimos fornecer comentários:

- A anomalia é causada por um feriado. É recomendável usar um evento predefinido para resolver esse tipo de alarme falso, pois ele será mais preciso.
- A anomalia é causada por uma alteração de fonte de dados conhecida. Por exemplo, uma alteração de sistema upstream ocorreu nesse momento. Nessa situação, espera-se que haja um alerta de anomalias, pois nosso sistema não sabia o que causou a alteração do valor e quando alterações de valor semelhantes ocorrerão novamente. Portanto, não sugerimos anotar esse tipo de problema como `NotAnomaly` .

## <a name="change-points"></a>Pontos de alteração

Às vezes, a alteração de tendência de dados afetará os resultados da detecção de anomalias. Quando é feita uma decisão sobre se um ponto é uma anomalia ou não, a última janela de dados de histórico será levada em consideração. Quando sua série temporal tiver uma alteração de tendência, você poderá marcar o ponto de alteração exato, isso ajudará nosso detector de anomalias em análise futura.

Como mostra a figura a seguir, você pode selecionar `ChangePoint` para o tipo de comentário e selecionar `ChangePoint` , `NotChangePoint` ou `AutoDetect` na lista suspensa.

:::image type="content" source="../media/feedback/changepoint.png" alt-text="Menu alterar ponto com DropDown contendo opções para ChangePoint, NotChangePoint e AutoDetect":::

> [!NOTE]
> Se os dados forem alterados, você só precisará marcar um ponto como um `ChangePoint` , portanto, se marcou um `timerange` , iremos preencher o carimbo de data e hora do último ponto automaticamente. Nesse caso, sua anotação afetará apenas os resultados da detecção de anomalias após 12 pontos.

## <a name="seasonality"></a>Sazonalidade

Para dados sazonais, quando executamos a detecção de anomalias, uma etapa é estimar o período (sazonalidade) da série temporal e aplicá-lo à fase de detecção de anomalias. Às vezes, é difícil identificar um período preciso e o período também pode ser alterado. Um período definido incorretamente pode ter efeitos colaterais sobre os resultados da detecção de anomalias. Você pode encontrar o período atual de uma dica de ferramenta, seu nome é `Min Period` .

:::image type="content" source="../media/feedback/min-period.png" alt-text="Sobreposição de ToolTip com o período de palavras e o número sete realçado em vermelho.":::

Você pode fornecer comentários para o período para corrigir esse tipo de erro de detecção de anomalias. Como mostra a figura, você pode definir um valor de período. A unidade `interval` significa uma granularidade. Aqui, zero intervalos significa que os dados não são sazonais. Você também pode selecionar `AutoDetect` se deseja cancelar os comentários anteriores e deixar que o pipeline detecte o período automaticamente. 
 
> [!NOTE]
> Ao definir o período, você não precisa atribuir um carimbo de data/hora ou um intervalo de tempo, o período afetará as detecções de anomalias futuras em uma série temporal a partir do momento em que você fornecer comentários.


:::image type="content" source="../media/feedback/period-feedback.png" alt-text="Menu com o período de detecção automática definido como 28 e o intervalo definido como zero indicando não sazonal.":::

## <a name="provide-comment-feedback"></a>Fornecer comentários de comentário

Você também pode adicionar comentários para anotar e fornecer contexto aos seus dados. Para adicionar comentários, selecione um intervalo de tempo e adicione o texto para o comentário.

:::image type="content" source="../media/feedback/feedback-comment.png" alt-text="Menu com a capacidade de definir um intervalo de tempo e uma caixa para adicionar um comentário baseado em texto":::

## <a name="time-series-batch-feedback"></a>Comentários de lote de série temporal

Conforme descrito anteriormente, o modal de comentários permite que você reselecione ou remova valores de dimensão para obter um lote de série temporal definido por um filtro de dimensão. Você também pode abrir essa janela restrita clicando no botão "+" para obter comentários no painel esquerdo e selecionar dimensões e valores de dimensão.

:::image type="content" source="../media/feedback/feedback-time-series.png" alt-text="Menu com um sinal de adição azul realçado em vermelho ao lado dos comentários do Word":::

:::image type="content" source="../media/feedback/feedback-dimension.png" alt-text="Adicionar o menu de comentários com duas dimensões indicadas por Dim1 e Dim2":::

## <a name="how-to-view-feedback-history"></a>Como exibir o histórico de comentários

Há duas maneiras de exibir o histórico de comentários. Você pode selecionar o botão histórico de comentários no painel esquerdo e verá uma lista de comentários modal. Ele lista todos os comentários que você recebeu antes de um único filtro de série ou de dimensão.

:::image type="content" source="../media/feedback/feedback-history-options.png" alt-text="Menu de lista de comentários '":::

Outra maneira de exibir o histórico de comentários é de uma série. Você verá vários botões no canto superior direito de cada série. Selecione o botão Mostrar comentários e a linha mudará de mostrar pontos de anomalias para mostrar as entradas de comentários. O sinalizador verde representa um ponto de alteração e os pontos azuis são outros pontos de comentário. Você também pode selecioná-los e receberá uma lista de comentários com janela restrita que lista os detalhes dos comentários fornecidos para esse ponto.

:::image type="content" source="../media/feedback/feedback-history-graph.png" alt-text="Grafo de histórico de comentários":::

:::image type="content" source="../media/feedback/feedback-list.png" alt-text="Menu lista de comentários com duas dimensões":::

> [!NOTE]
> Qualquer pessoa que tenha acesso à métrica tem permissão para fornecer comentários, para que você possa ver os comentários fornecidos por outros proprietários do feed de dados. Se você editar o mesmo ponto que outra pessoa, seus comentários substituirão a entrada de comentários anterior.       

## <a name="next-steps"></a>Próximas etapas
- [Diagnosticar um incidente](diagnose-incident.md).
- [Configurar métricas e ajustar a configuração de detecção](configure-metrics.md)
- [Configurar alertas e obter notificações usando um web hook](../how-tos/alerts.md)