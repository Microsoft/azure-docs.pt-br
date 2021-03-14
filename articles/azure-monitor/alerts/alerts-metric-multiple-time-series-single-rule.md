---
title: Monitorar várias séries temporais em uma única regra de alerta de métrica
description: Alerta em escala usando uma única regra de alerta para várias séries temporais
author: harelbr
ms.author: harelbr
ms.topic: conceptual
ms.date: 01/11/2021
ms.openlocfilehash: bc3e77b560a94b45acf1ae696551dbefbabbdf0a
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102038196"
---
# <a name="monitor-multiple-time-series-in-a-single-metric-alert-rule"></a>Monitorar várias séries temporais em uma única regra de alerta de métrica

Uma única regra de alerta de métrica pode ser usada para monitorar uma ou várias séries temporais, facilitando o monitoramento de recursos em escala.

## <a name="metric-time-series"></a>Série temporal de métrica

Uma série temporal de métrica é uma série de medições (ou "valores de métrica") capturados em um período de tempo. 

Por exemplo: 

- A utilização da CPU de uma máquina virtual
- Os bytes de entrada (entrada) para uma conta de armazenamento
- O número de solicitações com falha de um aplicativo Web



## <a name="alert-rule-on-a-single-time-series"></a>Regra de alerta em uma única série temporal
Uma regra de alerta monitora uma única série temporal quando atende a todas as seguintes condições:
-   A regra monitora um único recurso de destino 
-   Contém uma única condição
-   Avalia uma métrica sem escolher dimensões (supondo que a métrica dê suporte a dimensões)

Um exemplo dessa regra de alerta (com apenas as propriedades relevantes mostradas):
-   Recurso de destino: *myVM1*
-   Métrica: *percentual de CPU*
-   Operador: *maior que*
-   Limite: *70*


Para essa regra de alerta, uma única série temporal de métrica é monitorada:
-   Percentual de CPU em que *Resource*= ' myVM1 ' > 70%

![Uma regra de alerta em uma única série temporal](media/alerts-metric-multiple-time-series-single-rule/simple-alert-rule.png)

## <a name="alert-rule-on-multiple-time-series"></a>Regra de alerta em várias séries temporais
Uma regra de alerta monitora várias séries temporais se ele usa pelo menos um dos seguintes recursos: 
-   Recursos múltiplos
-   Várias condições 
-   Várias dimensões


## <a name="multiple-resources-multi-resource"></a>Vários recursos (vários recursos)

Uma única regra de alerta de métrica pode monitorar vários recursos, desde que os recursos sejam do mesmo tipo e existam na mesma região do Azure. O uso desse tipo de regra reduz a complexidade e o número total de regras de alerta que você precisa manter. 

Um exemplo dessa regra de alerta:
-   Recurso de destino: *myVM1, myVM2*
-   Métrica: *percentual de CPU*
-   Operador: *maior que*
-   Limite: *70*

Para esta regra de alerta, duas séries temporais de métrica estão sendo monitoradas separadamente:
-   Percentual de CPU em que *Resource*= ' myVM1 ' > 70%
-   Percentual de CPU em que *Resource*= ' myVM2 ' > 70%

![Uma regra de alerta de vários recursos](media/alerts-metric-multiple-time-series-single-rule/multi-resource-alert-rule.png)
 
Em uma regra de alerta de vários recursos, a condição é avaliada **separadamente** para cada um dos recursos (ou com mais precisão, para cada uma das séries temporais correspondentes a cada recurso). Isso significa que os alertas também são acionados para cada recurso separadamente.

Por exemplo, suponha que definimos a regra de alerta acima para monitorar a CPU acima de 70%. No período de tempo avaliado (ou seja, os últimos 5 minutos)
-   A *porcentagem de CPU* de *myVM1* é maior que 70% 
-   A *porcentagem de CPU* de *myVM2* está em 50%

A regra de alerta é disparada em *myVM1*, mas não *myVM2*. Esses alertas disparados são independentes. Elas também podem ser resolvidas em momentos diferentes, dependendo do comportamento individual de cada uma das máquinas virtuais.

Para obter mais informações sobre regras de alerta de vários recursos e os tipos de recursos com suporte para esse recurso, consulte [monitoramento em escala usando alertas de métrica no Azure monitor](alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor).

> [!NOTE] 
> Em uma regra de alerta de métrica que monitora vários recursos, apenas uma única condição é permitida.

## <a name="multiple-conditions-multi-condition"></a>Várias condições (várias condição)

Uma regra de alerta de métrica única também pode monitorar até cinco condições por regra de alerta. 

Por exemplo: 

- Recurso de destino: *myVM1*
- Condição1
  - Métrica: *percentual de CPU*
  - Operador: *maior que*
  - Limite: *70*
- Condição2
  - Métrica: *rede no total*
  - Operador: *maior que*
  - Limite: *20 MB*

Para esta regra de alerta, duas séries temporais de métrica estão sendo monitoradas:

- Percentual de CPU em que *Resource*= ' myVM1 ' > 70%
- Rede no total em que *Resource*= ' myVM1 ' > 20 MB

![Uma regra de alerta de várias condições](media/alerts-metric-multiple-time-series-single-rule/multi-condition-alert-rule.png)
 
Um operador ' AND ' é usado entre as condições. A regra de alerta dispara um alerta quando **todas as** condições são atendidas. O alerta acionado resolve se pelo menos uma das condições não for mais atendida. 

> [!NOTE]
> Há restrições ao usar dimensões em uma regra de alerta com várias condições. Para obter mais informações, consulte [restrições ao usar dimensões em uma regra de alerta de métrica com várias condições](alerts-troubleshoot-metric.md#restrictions-when-using-dimensions-in-a-metric-alert-rule-with-multiple-conditions).


## <a name="multiple-dimensions-multi-dimension"></a>Várias dimensões (várias dimensões)

Uma regra de alerta de métrica única também pode monitorar vários valores de dimensão de uma métrica. As dimensões de uma métrica são pares de nome-valor que contêm dados adicionais para descrever o valor da métrica. Por exemplo, a métrica de **Transações** de uma conta de armazenamento tem uma dimensão chamada **nome da API**, descrevendo o nome da API chamada por cada transação (por exemplo, getBlob, DeleteBlob, PutPage). O uso de dimensões é opcional, mas permite filtrar a métrica e monitorar apenas séries temporais específicas, em vez de monitorar a métrica como uma agregação de todos os valores dimensionais agrupados. 

Por exemplo, você pode optar por um alerta disparado quando o número de transações estiver alto em todos os nomes de API (que são os dados agregados) ou dividi-lo em apenas alertas quando o número de transações for alto para nomes de API específicos. 

Um exemplo de uma regra de alerta monitorando várias dimensões é:

- Recurso de destino: *myStorage1*
- Métrica: *Transações*
- Dimensões
  * Nome da API = *getBlob, DeleteBlob, PutPage*
- Operador: *maior que*
- Limite: *70*

Para esta regra de alerta, três séries temporais de métrica estão sendo monitoradas:

- Transações em que *Resource*= ' myStorage1 ' e *nome da API*= ' getblob ' > 70
- Transações em que *Resource*= ' myStorage1 ' e *nome da API*= ' DeleteBlob ' > 70
- Transações em que *Resource*= ' myStorage1 ' e *nome da API*= ' PutPage ' > 70

![Uma regra de alerta de várias dimensões com valores de uma dimensão](media/alerts-metric-multiple-time-series-single-rule/multi-dimension-1.png)

Uma regra de alerta de métrica de várias dimensões também pode monitorar vários valores de dimensão de dimensões **diferentes** de uma métrica. Nesse caso, a regra de alerta monitora **separadamente** todas as combinações de valor das dimensões dos valores de dimensão selecionados.

Um exemplo desse tipo de regra de alerta:

- Recurso de destino: *myStorage1*
- Métrica: *Transações*
- Dimensões
  * Nome da API = *getBlob, DeleteBlob, PutPage*
  * Autenticação = *SAS, AccountKey*
- Operador: *maior que*
- Limite: *70*

Para esta regra de alerta, seis séries temporais de métrica estão sendo monitoradas separadamente:

- Transações em que *Resource*= ' myStorage1 ' e *nome da API*= ' getBlob ' e *Authentication*= ' SAS ' > 70
- Transações em que *Resource*= ' myStorage1 ' e *nome da API*= ' getBlob ' e *Authentication*= ' AccountKey ' > 70
- Transações em que *Resource*= ' myStorage1 ' e *nome da API*= ' DeleteBlob ' e *Authentication*= ' SAS ' > 70
- Transações em que *Resource*= ' myStorage1 ' e *nome da API*= ' DeleteBlob ' e *Authentication*= ' AccountKey ' > 70
- Transações em que *Resource*= ' myStorage1 ' e *nome da API*= ' PutPage ' e *Authentication*= ' SAS ' > 70
- Transações em que *Resource*= ' myStorage1 ' e *nome da API*= ' PutPage ' e *Authentication*= ' AccountKey ' > 70

![Uma regra de alerta de várias dimensões com valores de várias dimensões](media/alerts-metric-multiple-time-series-single-rule/multi-dimension-2.png)
 
### <a name="advanced-multi-dimension-features"></a>Recursos avançados de várias dimensões

1.  **Selecionando todas as dimensões atuais e futuras** – você pode optar por monitorar todos os valores possíveis de uma dimensão, incluindo valores futuros. Essa regra de alerta será dimensionada automaticamente para monitorar todos os valores da dimensão sem a necessidade de modificar a regra de alerta toda vez que um valor de dimensão for adicionado ou removido.
2.  **Excluindo dimensões** – selecionar o operador ' ≠ ' (excluir) para um valor de dimensão é equivalente a selecionar todos os outros valores dessa dimensão, incluindo valores futuros.
3.  **Dimensões novas e personalizadas** – os valores de dimensão exibidos no portal do Azure são baseados nos dados de métrica coletados no último dia. Se o valor de dimensão que você está procurando ainda não for emitido, você poderá adicionar um valor de dimensão personalizado.
4. **Correspondência de dimensões com um prefixo** -você pode optar por monitorar todos os valores de dimensão que começam com um padrão específico, selecionando o operador "começa com" e inserindo um prefixo personalizado.

![Recursos avançados de várias dimensões](media/alerts-metric-multiple-time-series-single-rule/advanced-features.png)


## <a name="metric-alerts-pricing"></a>Preços de alertas de métrica

O preço das regras de alerta de métrica está disponível na [página de preços do Azure monitor](https://azure.microsoft.com/pricing/details/monitor/).

Ao criar uma regra de alerta de métrica, a estimativa de preço fornecida é baseada nos recursos selecionados e no número de séries temporais monitoradas, que é determinado da configuração da regra e dos valores atuais da métrica. No entanto, o encargo mensal é baseado em avaliações reais da série temporal e, portanto, pode ser diferente da estimativa original se alguma série temporal não tiver dados a serem avaliados ou se a regra de alerta usar recursos que possam torná-lo dimensionado dinamicamente.

Por exemplo, uma regra de alerta pode mostrar uma estimativa de preço alto se ele aproveitar o recurso de várias dimensões e um grande número de combinações de valores de dimensão for selecionado, resultando no monitoramento de muitas séries temporais. Mas o encargo real para essa regra de alerta pode ser menor se nem todas as séries temporais resultantes das combinações de valores de dimensão realmente têm dados a serem avaliados.

## <a name="number-of-time-series-monitored-by-a-single-alert-rule"></a>Número de séries temporais monitoradas por uma única regra de alerta

Para evitar custos excedentes, cada regra de alerta pode monitorar até 5000 séries temporais por padrão. Para aumentar esse limite de sua assinatura, abra um tíquete de suporte.


## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre monitoramento em escala usando alertas de métrica e [limites dinâmicos](../alerts/alerts-dynamic-thresholds.md).
