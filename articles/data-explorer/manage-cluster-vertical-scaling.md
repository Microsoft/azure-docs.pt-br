---
title: Gerenciar o dimensionamento vertical do cluster (Scale up) no Azure Data Explorer para acomodar a demanda em alteração
description: Este artigo descreve as etapas para escalar verticalmente e reduzir um cluster de Data Explorer do Azure com base na demanda em alteração.
author: radennis
ms.author: radennis
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/14/2019
ms.openlocfilehash: 80bbdf3a5d936719b06782cd78d56088b36cb21d
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2019
ms.locfileid: "67985473"
---
# <a name="manage-cluster-vertical-scaling-scale-up-in-azure-data-explorer-to-accommodate-changing-demand"></a>Gerenciar o dimensionamento vertical do cluster (Scale up) no Azure Data Explorer para acomodar a demanda em alteração

Dimensionar um cluster apropriadamente é essencial para o desempenho do Azure Data Explorer. Um tamanho de cluster estático pode levar a subutilização ou superutilização, e nenhuma dessas condições é a ideal.

Como a demanda em um cluster não pode ser prevista com precisão absoluta, uma abordagem melhor é *dimensionar* um cluster, adicionar e remover recursos de capacidade e CPU com alteração de demanda. 

Há dois fluxos de trabalho para dimensionar um cluster de Data Explorer do Azure:

* [Dimensionamento horizontal](manage-cluster-horizontal-scaling.md), também chamado de expansão e saída.
* Dimensionamento vertical, também chamado de expansão e redução.

Este artigo explica o fluxo de trabalho de dimensionamento vertical:

## <a name="configure-vertical-scaling"></a>Configurar o dimensionamento vertical

1. Na portal do Azure, vá para o recurso de cluster de Data Explorer do Azure. Em **configurações**, selecione **escalar verticalmente**.

1. Na janela **escalar verticalmente** , você verá uma lista de SKUs disponíveis para o cluster. Por exemplo, na figura a seguir, somente quatro SKUs estão disponíveis.

    ![Escalar verticalmente](media/manage-cluster-vertical-scaling/scale-up.png)

    As SKUs estão desabilitadas porque são a SKU atual ou não estão disponíveis na região onde o cluster está localizado.

1. Para alterar sua SKU, selecione uma nova SKU e clique em **selecionar**.

> [!NOTE]
> * O processo de dimensionamento vertical pode levar alguns minutos e, durante esse tempo, o cluster será suspenso. 
> * Reduzir verticalmente pode prejudicar o desempenho do cluster.
> * O preço é uma estimativa das máquinas virtuais do cluster e dos custos do serviço Data Explorer do Azure. Outros custos não são incluídos. Confira a página estimador de [custo](https://dataexplorer.azure.com/AzureDataExplorerCostEstimator.html) de data Explorer do Azure para obter uma estimativa e a [página de preços](https://azure.microsoft.com/pricing/details/data-explorer/) do Azure data Explorer para obter informações de preços completas.

Agora você configurou o dimensionamento vertical para o cluster de Data Explorer do Azure. Adicione outra regra para um dimensionamento horizontal. Se você precisar de assistência com problemas de dimensionamento de cluster, [abra uma solicitação de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) no portal do Azure.

## <a name="next-steps"></a>Próximas etapas

* [Gerencie o dimensionamento horizontal do cluster](manage-cluster-horizontal-scaling.md) para expandir dinamicamente a contagem de instâncias com base nas métricas que você especificar.

* Monitore o uso de recursos seguindo este artigo: [Monitore o desempenho, a integridade e o uso do Azure data Explorer com métricas](using-metrics.md).

