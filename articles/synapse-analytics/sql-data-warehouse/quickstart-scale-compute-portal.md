---
title: 'Início Rápido: Dimensionar a computação para o pool de SQL do Synapse (portal do Azure)'
description: Você pode dimensionar a computação para o pool de SQL do Synapse (data warehouse) usando o portal do Azure.
services: synapse-analytics
author: Antvgski
ms.author: anvang
manager: craigg
ms.reviewer: jrasnick
ms.date: 04/28/2020
ms.topic: quickstart
ms.service: synapse-analytics
ms.subservice: sql-dw
ms.custom:
- seo-lt-2019
- azure-synapse
- mode-portal
ms.openlocfilehash: aa339274a1ff68764fa5573d9c031e84f290e57c
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107534229"
---
# <a name="quickstart-scale-compute-for-synapse-sql-pool-with-the-azure-portal"></a>Início Rápido: Dimensionar a computação para o pool de SQL do Synapse com o portal do Azure

Você pode dimensionar a computação para o pool de SQL do Synapse (data warehouse) usando o portal do Azure. [Escale horizontalmente a computação](sql-data-warehouse-manage-compute-overview.md) para melhorar o desempenho ou reduza a escala da computação para economizar custos. 

Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Entre no [portal do Azure](https://portal.azure.com/).

## <a name="before-you-begin"></a>Antes de começar

Você pode escalar um pool de SQL que você já tem ou usar o [Início Rápido: criar e conectar – portal](create-data-warehouse-portal.md) para criar um pool de SQL chamado **mySampleDataWarehouse**. Este início rápido dimensiona o **mySampleDataWarehouse**.

>[!IMPORTANT] 
>Seu pool de SQL deve estar online para ser dimensionado. 

## <a name="scale-compute"></a>Computação de escala

Os recursos de computação do pool de SQL podem ser dimensionados com o aumento ou a diminuição das unidades de data warehouse. O [Início rápido: criar e conectar – portal](create-data-warehouse-portal.md) criou **mySampleDataWarehouse** e o inicializou com 400 DWUs. As seguintes etapas ajustam as DWUs do **mySampleDataWarehouse**.

Para alterar unidades de data warehouse:

1. Clique em **Azure Synapse Analytics (antigo SQL DW)** na página à esquerda do portal do Azure.
2. Selecione **mySampleDataWarehouse** na página **Azure Synapse Analytics (antigo SQL DW)** . O pool de SQL é aberto.
3. Clique em **Escala**.

    ![Clique em Escala.](./media/quickstart-scale-compute-portal/click-scale.png)

2. No painel Escala, mova o controle deslizante para a esquerda ou direita para alterar a configuração de DWU. Em seguida, selecione Escalar.

    ![Mover o controle deslizante](./media/quickstart-scale-compute-portal/scale-dwu.png)

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre o pool de SQL, prossiga para o tutorial [Carregar dados para o pool de SQL](./load-data-from-azure-blob-storage-using-copy.md).
