---
title: Entender os preços do compartilhamento de dados do Azure
description: Saiba como o preço do compartilhamento de dados do Azure funciona
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: conceptual
ms.date: 08/11/2020
ms.openlocfilehash: 1c2c58e206a70a3801c712df3b5582409712d3c8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88136724"
---
# <a name="understand-azure-data-share-pricing"></a>Entender os preços do compartilhamento de dados do Azure

Para o compartilhamento baseado em instantâneos, o compartilhamento de dados do Azure cobra pelo instantâneo do DataSet e pela execução do instantâneo. Este artigo explica como estimar a execução de instantâneo e instantâneo do conjunto de dados usando informações de histórico de instantâneo. Atualmente, o provedor de dados é cobrado para o instantâneo do conjunto e a execução do instantâneo.

## <a name="dataset-snapshot"></a>Instantâneo de conjunto de

O instantâneo do conjunto de uma é a operação para mover o conjunto de conjuntos de origem para destino. Quando o instantâneo é obtido para um compartilhamento, o instantâneo de cada conjunto de um no compartilhamento é uma operação de instantâneo de conjunto de uma. Siga as etapas abaixo para exibir uma lista de instantâneos de conjunto de 

1. Em portal do Azure, navegue até o recurso de compartilhamento de dados.

1. Selecione um compartilhamento do compartilhamento enviado ou do compartilhamento recebido.

1. Clique na guia **histórico** .

1. Clique na hora de início de um instantâneo.
 
    ![Histórico de instantâneos](./media/concepts/concepts-pricing/pricing-snapshot-history.png "Histórico de instantâneos") 

1. Exiba a lista de operações de instantâneo de conjunto de Cada item de linha corresponde a uma operação de instantâneo de conjunto de linhas. Neste exemplo, há dois instantâneos de DataSet.

    ![Operação de instantâneo de DataSet](./media/concepts/concepts-pricing/pricing-dataset-snapshot.png "Operação de instantâneo de DataSet")

## <a name="snapshot-execution"></a>Execução de instantâneo

A execução do instantâneo inclui os recursos necessários para mover um conjunto de uma fonte para o destino. Para cada operação de instantâneo de conjunto de consulta, a execução de instantâneo é calculada como o número de vCores multiplicado pela duração do instantâneo. Os encargos são rateado por minuto e arredondados. O número de vCore é selecionado dinamicamente com base no padrão de dados e par de destino de origem. Siga as etapas abaixo para exibir a hora de início do instantâneo, a hora de término e o vCores usados para uma operação de instantâneo de conjunto de linha.

1. Em portal do Azure, navegue até o recurso de compartilhamento de dados.

1. Selecione um compartilhamento do compartilhamento enviado ou do compartilhamento recebido.

1. Clique na guia **histórico** .

1. Clique na hora de início de um instantâneo.

    ![Histórico de instantâneos](./media/concepts/concepts-pricing/pricing-snapshot-history.png "Histórico de instantâneos") 

1. Clique no status de uma operação de instantâneo de conjunto de uma.

    ![Status do instantâneo do conjunto de um](./media/concepts/concepts-pricing/pricing-snapshot-status.png "Status do instantâneo do conjunto de um")

1. Exiba a hora de início, a hora de término e o vCores usados para esta operação de instantâneo de conjunto de data. 

    ![Execução de instantâneo](./media/concepts/concepts-pricing/pricing-snapshot-execution.png "Execução de instantâneo")

## <a name="next-steps"></a>Próximas etapas

- Obter informações de preços mais recentes- [preços de compartilhamento de dados do Azure](https://azure.microsoft.com/pricing/details/data-share/)
- Use a calculadora de preços do Azure para estimar o custo- [calculadora de preços do Azure](https://azure.microsoft.com/pricing/calculator/)
