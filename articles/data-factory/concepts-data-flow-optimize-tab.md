---
title: Guia Otimizar Fluxo de Dados de Mapeamento do Azure Data Factory
description: Otimizar Fluxos de Dados de Mapeamento do Azure Data Factory com Configurações de Partição da Guia Otimizar
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/31/2019
ms.openlocfilehash: 3802a8475d8a39a2f275dbc7fcf21ce69892a117
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61262221"
---
# <a name="mapping-data-flow-transformation-optimize-tab"></a>Guia Otimizar Transformação de Fluxo de Dados de Mapeamento

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Cada transformação de Fluxo de Dados tem uma guia "Otimizar". A guia otimizar contém configurações opcionais para configurar esquemas de particionamento para fluxos de dados.

<img src="media/data-flow/opt001.png" width="800">

A configuração padrão é "usar particionamento atual". O Particionamento Atual instrui o Azure Data Factory a usar o esquema de particionamento nativo para Fluxos de Dados em execução no Spark no Azure Databricks. Geralmente, esta é a abordagem recomendada.

No entanto, há situações em que talvez você queira ajustar o particionamento. Por exemplo, se você quer enviar as transformações para um único arquivo no Data Lake, escolha "partição única" na guia Otimizar para particionamento na Transformação do Coletor.

Outro caso em que você pode querer exercer controle sobre os esquemas de particionamento usados para as transformações de dados é no que diz respeito ao desempenho. Ajustar o particionamento de dados fornece um nível de controle sobre a distribuição dos dados nos nós de computação e otimizações de localidade de dados que podem ter efeitos tanto positivos como negativos no desempenho geral do fluxo de dados.

Caso queira alterar o particionamento em qualquer transformação, basta clicar na guia Otimizar e selecionar o botão de opção "Definir Particionamento". Em seguida, será apresentada uma série de opções para particionamento. O melhor método de particionamento a ser implementado será diferente com base nos volumes de dados, chaves candidatas, valores nulos e cardinalidade. A melhor prática é começar com o particionamento padrão e, em seguida, experimentar as diferentes opções de particionamento. É possível testar usando a execução de Depuração no Pipeline e, em seguida, exibir o tempo gasto em cada agrupamento de transformação, bem como o uso da partição na exibição de Monitoramento.

<img src="media/data-flow/opt002.png" width="600">

### <a name="round-robin"></a>Round Robin

Round Robin é uma partição simples que distribui automaticamente os dados igualmente pelas partições. Use Round Robin quando você não tiver chaves candidatas adequadas para implementar uma estratégia de particionamento sólida e inteligente. É possível definir o número de partições físicas.

### <a name="hash"></a>Hash

O Azure Data Factory produzirá um hash de colunas para produzir partições uniformes e, desse modo, as linhas com valores semelhantes ficarão na mesma partição. Ao usar a opção Hash, teste possíveis distorções de partição. É possível definir o número de partições físicas.

### <a name="dynamic-range"></a>Intervalo dinâmico

O intervalo dinâmico usará intervalos dinâmicos do Spark com base nas colunas ou expressões que você fornecer. É possível definir o número de partições físicas. 

### <a name="fixed-range"></a>Intervalo fixo

É necessário criar uma expressão que forneça um intervalo fixo para valores dentro das colunas de dados particionados. Para isso, será fundamental ter um bom conhecimento sobre os dados antes de usar essa opção para evitar distorção de partição. O valor inserido para a expressão será usado como parte de uma função de partição. É possível definir o número de partições físicas.

### <a name="key"></a>Chave

Se você tiver uma boa compreensão da cardinalidade dos dados, o particionamento de chaves poderá ser uma boa estratégia de partição. O particionamento de chaves criará partições para cada valor exclusivo na sua coluna. Não é possível definir o número de partições porque esse número será baseado em valores exclusivos nos dados.
