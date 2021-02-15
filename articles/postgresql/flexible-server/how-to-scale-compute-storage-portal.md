---
title: Operações de escala-portal do Azure-banco de dados do Azure para PostgreSQL-servidor flexível
description: Este artigo descreve como executar operações de escala no banco de dados do Azure para PostgreSQL por meio do portal do Azure.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 1542bba53b51ffdf2129953a81e5d13975ade434
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90933232"
---
# <a name="scale-operations-in-flexible-server"></a>Operações de dimensionamento no servidor flexível

> [!IMPORTANT]
> O Servidor Flexível do Banco de Dados do Azure para PostgreSQL está em versão prévia

Este artigo fornece etapas para executar operações de dimensionamento para computação e armazenamento. Você poderá alterar suas camadas de computação entre SKUs de intermitência, de uso geral e otimizados para memória, incluindo a escolha do número de vCores que é adequado para executar seu aplicativo. Você também pode escalar verticalmente o armazenamento. Os IOPS esperados são mostrados com base na camada de computação, vCores e a capacidade de armazenamento. A estimativa de custo também é mostrada com base na sua seleção.

> [!IMPORTANT]
> Você não pode reduzir o armazenamento.

## <a name="pre-requisites"></a>Pré-requisitos

Para concluir este guia de instruções, você precisa:

-   Você deve ter um servidor de banco de dados do Azure para PostgreSQL-flexível. O mesmo procedimento também é aplicável para o servidor flexível configurado com redundância de zona.
> [!IMPORTANT]
> Quando configurado com alta disponibilidade, você não pode escolher SKU expansível. Durante a operação de dimensionamento, o standby é dimensionado primeiro para o tamanho desejado, o servidor primário é submetido a failover e o primário é dimensionado. 

## <a name="scaling-the-compute-tier-and-size"></a>Dimensionando a camada e o tamanho da computação

Siga estas etapas para escolher a camada de computação.
 
1.  Na [portal do Azure](https://portal.azure.com/), escolha o servidor flexível do qual você deseja restaurar o backup.

2.  Clique em **computação + armazenamento**.

3.  Uma página com as configurações atuais é exibida.
 :::image type="content" source="./media/how-to-scale-compute-storage-portal/click-compute-storage.png" alt-text="computação + exibição de armazenamento":::

4.  Você pode escolher a classe de computação entre camadas de intermitência, de uso geral e com otimização de memória.
   :::image type="content" source="./media/how-to-scale-compute-storage-portal/list-compute-tiers.png" alt-text="computação + exibição de armazenamento":::


5.  Se você estiver bom com os tamanhos de memória e vCores padrão, poderá ignorar a próxima etapa.

6.  Se você quiser alterar o número de vCores, clique no menu suspenso do **tamanho da computação** e clique no número desejado de VCores/memória na lista.
    
    - Camada de computação expansível: :::image type="content" source="./media/how-to-scale-compute-storage-portal/compute-burstable-dropdown.png" alt-text="computação + exibição de armazenamento":::

    - Camada de computação de uso geral: :::image type="content" source="./media/how-to-scale-compute-storage-portal/compute-general-purpose-dropdown.png" alt-text="computação + exibição de armazenamento":::

    - Camada de computação com otimização de memória: :::image type="content" source="./media/how-to-scale-compute-storage-portal/compute-memory-optimized-dropdown.png" alt-text="computação + exibição de armazenamento":::

7.  Clique em **Salvar**. 
8.  Você verá uma mensagem de confirmação. Clique em **OK** se desejar continuar. 
9.  Uma notificação sobre a operação de dimensionamento em andamento.


## <a name="scaling-storage-size"></a>Dimensionamento do tamanho do armazenamento

Siga estas etapas para aumentar o tamanho do armazenamento.

1.  Na [portal do Azure](https://portal.azure.com/), escolha o servidor flexível para o qual você deseja aumentar o tamanho do armazenamento.
2.  Clique em **computação + armazenamento**.

3.  Uma página com as configurações atuais é exibida.
   
:::image type="content" source="./media/how-to-scale-compute-storage-portal/click-compute-storage.png" alt-text="computação + exibição de armazenamento":::
4.  O tamanho do armazenamento de campo **em GIB** com uma barra de slides é mostrado com o tamanho atual.

5.  Deslize a barra para o tamanho desejado. O número de IOPS correspondente é mostrado. O IOPS depende da camada de computação e do tamanho. As informações de custo também são mostradas. 

 :::image type="content" source="./media/how-to-scale-compute-storage-portal/storage-scaleup.png" alt-text="computação + exibição de armazenamento":::

6.  Se você estiver bem com o tamanho do armazenamento, clique em **salvar**. 
7.  Você verá uma mensagem de confirmação. Clique em **OK** se desejar continuar. 
8.  Uma notificação sobre a operação de dimensionamento em andamento.

## <a name="next-steps"></a>Próximas etapas

-   Saiba mais sobre a [continuidade dos negócios](./concepts-business-continuity.md)
-   Saiba mais sobre a [alta disponibilidade](./concepts-high-availability.md)
-   Saiba mais sobre [backup e recuperação](./concepts-backup-restore.md)
