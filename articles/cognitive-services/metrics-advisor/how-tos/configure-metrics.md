---
title: Configurar sua instância do supervisor de métricas usando o portal da Web
titleSuffix: Azure Cognitive Services
description: Como configurar sua instância do revisor de métricas e ajustar os resultados da detecção de anomalias.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 09/10/2020
ms.author: mbullwin
ms.openlocfilehash: 7923dad3d47122c0ceb04d1240736e2b66a0dd64
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92048246"
---
# <a name="how-to-configure-metrics-and-fine-tune-detecting-configuration"></a>Como configurar métricas e ajuste fino da configuração de detecção

Use este artigo para começar a configurar sua instância do supervisor de métricas usando o portal da Web. Para procurar as métricas de um feed de dados específico, vá para a página **feeds de dados** e selecione um dos feeds. Isso exibirá uma lista de métricas associadas a ela.

:::image type="content" source="../media/metrics/select-metric.png" alt-text="Selecione uma métrica" lightbox="../media/metrics/select-metric.png":::

Clique em um dos nomes de métrica para ver seus detalhes. Nesta exibição detalhada, você pode alternar para outra métrica no mesmo feed de dados usando a lista suspensa no canto superior direito da tela.

Ao exibir os detalhes de uma métrica, você pode carregar uma série temporal, permitindo que o Orientador de métrica escolha uma para você ou especificando os valores a serem incluídos para cada dimensão. 

Você também pode selecionar intervalos de tempo e alterar o layout da página.

> [!NOTE]
> - A hora de início é inclusiva.
> - A hora de término é exclusiva. 

Você pode clicar na guia **incidentes** para exibir anomalias e encontrar um link para o [Hub de incidentes](diagnose-incident.md).

## <a name="tune-the-detecting-configuration"></a>Ajustar a configuração de detecção

Uma métrica pode aplicar uma ou mais configurações de detecção. Há uma configuração padrão para cada métrica, que pode ser editada ou adicionada, de acordo com suas necessidades de monitoramento.

### <a name="tune-the-configuration-for-all-series-in-current-metric"></a>Ajustar a configuração de todas as séries na métrica atual

Essa configuração será aplicada a todas as séries nessa métrica, exceto aquelas com uma configuração separada. Uma configuração de nível de métrica é aplicada por padrão quando os dados são integrados e são mostrados no painel esquerdo. Os usuários podem editar diretamente a configuração de nível de métrica na página métrica. 

Há parâmetros adicionais, como **direção**, e **anomalias válidas** que podem ser usadas para ajustar ainda mais a configuração. Você também pode combinar métodos de detecção diferentes. 

:::image type="content" source="../media/configuration-combination.png" alt-text="Combinação de configuração" lightbox="../media/configuration-combination.png":::

### <a name="tune-the-configuration-for-a-specific-series-or-group"></a>Ajustar a configuração de uma série ou grupo específico

Clique em **Configuração avançada** abaixo das opções de configuração de nível de métrica para ver a configuração de nível de grupo. Você pode adicionar uma configuração para uma série individual ou grupo de séries clicando no **+** ícone nessa janela. Os parâmetros são semelhantes aos parâmetros de configuração de nível de métrica, mas talvez seja necessário especificar pelo menos um valor de dimensão para uma configuração de nível de grupo para identificar um grupo de séries. E especifique todos os valores de dimensão para a configuração em nível de série para identificar uma série específica. 

Essa configuração será aplicada ao grupo de séries ou séries específicas, em vez da configuração de nível de métrica. Depois de definir as condições para esse grupo, salve-o.

:::image type="content" source="../media/advanced-configuration.png" alt-text="Configuração avançada" lightbox="../media/advanced-configuration.png":::

### <a name="anomaly-detection-methods"></a>Métodos de detecção de anomalias

O Metrics Advisor oferece vários métodos de detecção de anomalias. Você pode usar um ou combiná-los usando operadores lógicos clicando no **+** botão. 

**Detecção inteligente** 

A detecção inteligente é alimentada pelo aprendizado de máquina que aprende padrões de dados históricos e os utiliza para detecção futura. Ao usar esse método, a **sensibilidade** é o parâmetro mais importante para ajustar os resultados da detecção. Você pode arrastá-lo para um valor menor ou maior para afetar a visualização no lado direito da página. Escolha um que se ajuste aos seus dados e salve-o. 


No modo de detecção inteligente, os parâmetros de versão de sensibilidade e de limite são usados para ajustar o resultado da detecção de anomalias.

A sensibilidade pode afetar a largura do intervalo de valores esperado de cada ponto. Quando aumentado, o intervalo de valores esperado será mais rígido e mais anomalias serão relatadas:

:::image type="content" source="../media/metrics/smart-detection-high-sensitivity.png" alt-text="Detecção inteligente com alta sensibilidade":::

Quando a sensibilidade for desativada, o intervalo de valores esperado será mais largo e menos anomalias serão relatadas:

:::image type="content" source="../media/metrics/smart-detection-low-sensitivity.png" alt-text="Detecção inteligente com baixa sensibilidade":::

**Alterar limite** 

O limite de alteração normalmente é usado quando os dados da métrica geralmente permanecem em um determinado intervalo. O limite é definido de acordo com a **porcentagem de alteração**. O modo de **alteração do limite** é capaz de detectar anomalias nos cenários:

* Seus dados normalmente são estáveis e suaves. Você deseja ser notificado quando houver flutuações.
* Seus dados normalmente são bastante instáveis e flutua muito. Você deseja ser notificado quando ele se tornar muito estável ou simples.

Use as seguintes etapas para usar este modo:

1. Selecione o **limite de alteração** como seu método de detecção de anomalias ao definir as configurações de detecção de anomalias para suas métricas ou série temporal.
    
    :::image type="content" source="../media/metrics/change-threshold.png" alt-text="alterar limite":::

2. Selecione o **fora do intervalo** ou **no parâmetro intervalo** com base em seu cenário.

    Se você quiser detectar flutuações, selecione **fora do intervalo**. Por exemplo, com as configurações abaixo, todos os pontos de dados que forem alterados em 10% em comparação com o anterior serão detectados como uma exceção.
    :::image type="content" source="../media/metrics/out-of-the-range.png" alt-text="parâmetro fora do intervalo":::

    Se você quiser detectar linhas simples em seus dados, selecione **no intervalo**. Por exemplo, com as configurações abaixo, todos os pontos de dados que forem alterados em 0, 1% em comparação com o anterior serão detectados como uma exceção. Como o limite é tão pequeno (0, 1%), ele detecta linhas simples nos dados como exceções.

    :::image type="content" source="../media/metrics/in-the-range.png" alt-text="No parâmetro de intervalo":::

3. Defina a porcentagem de alterações que serão contadas como uma anomalia e quais pontos de dados capturados anteriormente serão usados para comparação. Essa comparação sempre está entre o ponto de dados atual e um único ponto de dados N aponta antes dele.
    
    A **direção** só será válida se você estiver usando o **fora do modo de intervalo** :
    
    * A **configuração configura** a detecção para detectar apenas anomalias quando (ponto de dados atual) – (ponto de dados de comparação) > **+** porcentagem de limite.
    * **Down** configura a detecção para detectar apenas anomalias quando (ponto de dados atual) – (comparando o ponto de dados) < **-** porcentagem de limite.
 
**Limite rígido**

 O limite rígido é um método básico para detecção de anomalias. Você pode definir um limite superior e/ou inferior para determinar o intervalo de valores esperado. Os pontos que ficarem fora do limite serão identificados como uma anomalia. 


## <a name="preset-events"></a>Eventos predefinidos

Às vezes, eventos e ocorrências esperados (como feriados) podem gerar dados anormais. Usando eventos predefinidos, você pode adicionar sinalizadores à saída de detecção de anomalias durante os horários especificados. Esse recurso deve ser configurado depois que o feed de dados é integrado. Cada métrica pode ter apenas uma configuração de evento predefinida.

> [!Note]
> A configuração de evento predefinido levará os feriados em consideração durante a detecção de anomalias e poderá alterar os resultados. Ele será aplicado aos pontos de dados ingeridos depois que você salvar a configuração. 

Clique no botão **Configurar evento predefinido** ao lado da lista suspensa métricas em cada página de detalhes de métrica.
 
:::image type="content" source="../media/metrics/preset-event-button.png" alt-text="botão de evento predefinido":::

Na janela que aparece, configure as opções de acordo com seu uso. Verifique se **habilitar evento de feriado** está selecionado para usar a configuração. 

A seção de **eventos de feriados** ajuda a suprimir anomalias desnecessárias detectadas durante os feriados. Há duas opções para a opção de **estratégia** que você pode aplicar:

* **Suprimir feriado**: suprime todas as anomalias e alertas em resultados de detecção de anomalias durante o período de Natal.
* **Feriado como fim de semana**: calcula os valores médios esperados de vários finais de semana correspondentes antes do feriado e baseia o status de anomalia desses valores.

Há vários outros valores que podem ser configurados:

|Opção  |Descrição  |
|---------|---------|
|**Escolher uma dimensão como país**     | Escolha uma dimensão que contenha informações de país. Por exemplo, um código de país.         |
|**Mapeamento de código do país**     | O mapeamento entre um [código de país](https://wikipedia.org/wiki/ISO_3166-1_alpha-2)padrão e os dados de país da dimensão escolhida.        |
|**Opções de feriado**    | Se deve levar em conta todos os feriados, apenas feriados PTO (tempo de pagamento) ou apenas feriados não PTO.         |
|**Dias para expandir**    |  Os dias afetados antes e depois de um feriado.        |


A seção **ciclo de evento** pode ser usada em alguns cenários para ajudar a reduzir os alertas desnecessários usando padrões cíclicos nos dados. Por exemplo: 

- Métricas que têm vários padrões ou ciclos, como um padrão semanal e mensal. 
- Métricas que não têm um padrão claro, mas os dados são comparáveis ano a ano (YoY), mês a mês (MoM), semana a semana (WoW) ou dia a dia (DoD).
 
Nem todas as opções são selecionáveis para cada granularidade. As opções disponíveis por granularidade estão abaixo:

| Granularidade | YoY | MoM | WoW | DoD |
|:-|:-|:-|:-|:-|
| Anualmente | X | X | X | X |
| Mensal | X | X | X | X |
| Semanalmente | ✔ | X | X | X |
| Diariamente | ✔ | ✔ | ✔ | X |
| A cada hora | ✔ | ✔ | ✔ | ✔ |
| Por minuto | X | X | X | X |
| Segundo | X | X | X | X |
| Personalizar | ✔ | ✔ | ✔ | ✔ |

X-não disponível.  
✔-Disponível.
  
\* Ao usar uma granularidade personalizada em segundos, só estará disponível se a métrica tiver mais de uma hora e menos de um dia.

O ciclo de evento é usado para reduzir anomalias se eles seguirem um padrão cíclico, mas ele relatará uma anomalia se vários pontos de dados não seguirem o padrão. O **modo estrito** é usado para habilitar o relatório de anomalias se mesmo um ponto de dados não seguir o padrão. 

:::image type="content" source="../media/metrics/preset-events.png" alt-text="configuração de evento predefinida":::

## <a name="view-recent-incidents"></a>Exibir incidentes recentes

O Metrics Advisor detecta anomalias em todos os seus dados de série temporal à medida que eles são ingeridos. No entanto, nem todas as anomalias precisam ser escalonadas, pois podem não ter um grande impacto. A agregação será executada em anomalias para agrupar os relacionados a incidentes. Você pode exibir esses incidentes na guia **incidente** da página de detalhes de métricas. 

Clique em um incidente para ir para a página **análise de incidentes** , em que você pode ver mais detalhes sobre ele. Clique em **gerenciar incidentes no novo hub de incidente** para localizar a página do [Hub de incidentes](diagnose-incident.md) , na qual você pode encontrar todos os incidentes sob a métrica específica. 

## <a name="subscribe-anomalies-for-notification"></a>Assinar anomalias para notificação

Se você quiser ser notificado sempre que uma anomalia for detectada, poderá assinar alertas para a métrica usando um gancho. Consulte [configurar alertas e obter notificações usando um gancho](alerts.md) para obter mais informações.


## <a name="next-steps"></a>Próximas etapas 
- [Configurar alertas e obter notificações usando um web hook](alerts.md)
- [Ajustar a detecção de anomalias usando os comentários](anomaly-feedback.md)
- [Diagnosticar um incidente](diagnose-incident.md).

