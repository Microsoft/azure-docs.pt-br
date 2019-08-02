---
title: 'Início Rápido: Expandir a computação no SQL Data Warehouse do Azure – Portal do Azure | Microsoft Docs'
description: Dimensionar a computação no SQL Data Warehouse do Azure no Portal do Azure. Escale horizontalmente a computação para melhorar o desempenho ou reduza a escala da computação para economizar custos.
services: sql-data-warehouse
author: Antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: implement
ms.date: 04/17/2018
ms.author: anvang
ms.reviewer: jrasnick
ms.openlocfilehash: b392bfe44c61b46009ab1ab7f87fb6ef874a7f88
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68641067"
---
## <a name="quickstart-scale-compute-in-azure-sql-data-warehouse-in-the-azure-portal"></a>Início Rápido: Dimensionar a computação no SQL Data Warehouse do Azure no Portal do Azure

Dimensionar a computação no SQL Data Warehouse do Azure no Portal do Azure. [Escale horizontalmente a computação](sql-data-warehouse-manage-compute-overview.md) para melhorar o desempenho ou reduza a escala da computação para economizar custos. 

Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Entre no [Portal do Azure](https://portal.azure.com/).

## <a name="before-you-begin"></a>Antes de começar

Você pode dimensionar um data warehouse que você já tem ou usar o [Início Rápido: Criar e conectar – portal](create-data-warehouse-portal.md) para criar um data warehouse chamado **mySampleDataWarehouse**.  Este início rápido dimensiona o **mySampleDataWarehouse**.

>[!Note]
>O data warehouse precisa estar online para ser dimensionado. 

## <a name="scale-compute"></a>Computação de escala

Os recursos de computação do SQL Data Warehouse podem ser dimensionados com o aumento ou a diminuição das unidades de data warehouse. O Início Rápido [Criar e conectar – portal] (create-data-warehouse-portal.md) criou **mySampleDataWarehouse** e o inicializou com 400 DWUs. As seguintes etapas ajustam as DWUs do **mySampleDataWarehouse**.

Para alterar unidades de data warehouse:

1. Clique em **SQL data warehouses** na página esquerda do portal do Azure.
2. Selecione **mySampleDataWarehouse** na página **SQL data warehouses**. O data warehouse é aberto.
3. Clique em **Escala**.

    ![Clique em Escala.](media/quickstart-scale-compute-portal/click-scale.png)

2. No painel Escala, mova o controle deslizante para a esquerda ou direita para alterar a configuração de DWU.

    ![Mover o controle deslizante](media/quickstart-scale-compute-portal/scale-dwu.png)

3. Clique em **Save** (Salvar). Será exibida uma mensagem de confirmação. Clique em **sim** para confirmar ou em **não** para cancelar.

    ![Clique em Salvar](media/quickstart-scale-compute-portal/confirm-change.png)



## <a name="next-steps"></a>Próximas etapas
Agora você aprendeu a dimensionar a computação para seu data warehouse. Para saber mais sobre o SQL Data Warehouse do Azure, prossiga para o tutorial de carregamento de dados.

> [!div class="nextstepaction"]
>[Carregar dados no SQL Data Warehouse](load-data-from-azure-blob-storage-using-polybase.md)
