---
title: Transformação de seleção do Fluxo de Dados de Mapeamento do Azure Data Factory
description: Transformação de seleção do Fluxo de Dados de Mapeamento do Azure Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: 15c74637a2dc42ec44f582878b5505d94637cd7b
ms.sourcegitcommit: da0a8676b3c5283fddcd94cdd9044c3b99815046
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68314209"
---
# <a name="azure-data-factory-mapping-data-flow-select-transformation"></a>Transformação de seleção do Fluxo de Dados de Mapeamento do Azure Data Factory
[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Use essa transformação para a seletividade da coluna (reduzindo o número de colunas), colunas de alias e nomes de fluxo e reordenar colunas.

## <a name="how-to-use-select-transformation"></a>Como usar a transformação selecionar
A Transformação de seleção permite que você crie um alias para um fluxo inteiro ou para colunas nesse fluxo, atribua nomes diferentes (aliases) e, em seguida, referencie esses novos nomes posteriormente em seu fluxo de dados. Essa transformação é útil para cenários de autojunção. Uma maneira de implementar uma autojunção no Fluxo de Dados do ADF é usar um fluxo, ramificá-lo com “Novo branch” e, imediatamente depois, adicionar uma transformação “de seleção”. Esse fluxo agora terá um novo nome que pode ser usado para ingressar de volta ao fluxo original, criando uma autojunção:

![Autojunção](media/data-flow/selfjoin.png "Autojunção")

No diagrama acima, a Transformação de seleção está na parte superior. Isso é atribuir alias ao fluxo original como “OrigSourceBatting”. Na transformação junção realçada abaixo dela, você pode ver que usamos essa transmissão de alias Select como a junção à direita, permitindo que possamos fazer referência à mesma chave tanto no lado esquerdo & direito da junção interna.

A seleção também pode ser usada como uma maneira de anular a seleção de colunas do seu fluxo de dados. Por exemplo, se você tiver seis colunas definidas no seu coletor, mas desejar escolher apenas três específicas para transformar e, em seguida, fluir para o coletor, será possível selecionar apenas essas três usando a transformação de seleção.

> [!NOTE]
> É necessário desativar “Selecionar tudo” para escolher apenas colunas específicas

![Transformação de seleção](media/data-flow/select001.png "Alias de seleção")

## <a name="options"></a>Opções
* A configuração padrão para "Seleção" é incluir todas as colunas de entrada e manter esses nomes originais. É possível atribuir um alias ao fluxo definindo o nome da transformação de seleção.
* Para atribuir alias a colunas individuais, desmarque “Selecionar tudo” e use o mapeamento de colunas na parte inferior.
* Escolha ignorar duplicatas para eliminar colunas duplicadas dos metadados de entrada ou saída.

![Ignorar duplicatas](media/data-flow/select-skip-dup.png "Ignorar duplicatas")

## <a name="next-steps"></a>Próximas etapas
* Depois de usar Select para renomear, reordenar e alias de colunas, use a [transformação do coletor](data-flow-sink.md) para colocar seus dados em um repositório de dados.
