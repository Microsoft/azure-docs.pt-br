---
title: Escalar verticalmente um cluster do Data Explorer do Azure para acomodar a demanda de alteração
description: Este artigo descreve as etapas para escalar verticalmente e reduzir verticalmente um cluster do Gerenciador de dados do Azure com base na demanda de alteração.
author: radennis
ms.author: radennis
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/05/2019
ms.openlocfilehash: 1f130f79b6b6924559e1693e1eef8ced2972b3d5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60758681"
---
# <a name="manage-cluster-scale-up-to-accommodate-changing-demand"></a>Gerenciar o aumento do cluster para acomodar as mudanças de demanda

Há dois fluxos de trabalho para dimensionar um cluster do Gerenciador de dados do Azure: escalar verticalmente e [expansão](manage-cluster-scale-out.md). Este artigo mostra como gerenciar o aumento de escala do cluster.

Dimensionar um cluster apropriadamente é essencial para o desempenho do Azure Data Explorer. Mas por demanda em um cluster não pode ser prevista com exatidão absoluta. Um tamanho de cluster estático pode levar a subutilização ou superutilização, nenhum deles é ideal. Uma abordagem melhor é *escala* um cluster, adicionando e removendo a capacidade e os recursos de CPU com a demanda de alteração. 

## <a name="steps-to-scale-up"></a>Etapas para escalar verticalmente
1. Vá para seu cluster. Sob **as configurações**, selecione **escalar verticalmente**.

    Você vê uma lista de SKUs disponíveis. Por exemplo, na figura a seguir, apenas quatro SKUs estão disponíveis.

    ![Escalar verticalmente](media/manage-cluster-scale-up/scale-up.png)

    SKUs estão desabilitados porque eles são o SKU atual, ou eles não estão disponíveis na região em que o cluster está localizado.

1. Para alterar a SKU, selecione a SKU desejado e escolha o **selecionar** botão.

> [!NOTE]
> O processo de expansão pode levar alguns minutos e, durante esse tempo o cluster será suspenso. Observe que reduzir verticalmente pode prejudicar o desempenho do cluster.

Agora, você fez uma operação de ampliação ou redução de escala para o cluster do Gerenciador de dados do Azure.

## <a name="next-steps"></a>Próximas etapas
Você também pode [gerenciar o cluster de escala horizontal](manage-cluster-scale-out.md) que dinamicamente escalar horizontalmente a contagem de instâncias com base nas métricas que você especificar.

Se precisar de ajuda com problemas de dimensionamento de cluster [abrir uma solicitação de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) no portal do Azure.

Monitore o uso de recursos, seguindo este artigo: [Monitorar o desempenho do Data Explorer do Azure, integridade e uso com métricas](using-metrics.md).
