---
title: Stop/Start-portal do Azure-banco de dados do Azure para servidor MySQL
description: Este artigo descreve como parar/iniciar operações no banco de dados do Azure para MySQL.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 09/21/2020
ms.openlocfilehash: d297d215d4b0edfdd67b603ba4707bf02057ad78
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100516865"
---
# <a name="stopstart-an-azure-database-for-mysql"></a>Parar/iniciar um banco de dados do Azure para MySQL

> [!IMPORTANT]
>  Quando você **interrompe** o servidor, ele permanece nesse estado para os próximos 7 dias em um Stretch. Se você não **iniciá** -lo manualmente durante esse tempo, o servidor será iniciado automaticamente no final de 7 dias. Você pode optar por **interrompê** -lo novamente se não estiver usando o servidor.

Este artigo fornece um procedimento passo a passo para executar a interrupção e o início do servidor único.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de instruções, você precisa:

-   Você deve ter um servidor único do banco de dados do Azure para MySQL.

> [!NOTE]
> Consulte a limitação do uso de [parar/iniciar](concepts-servers.md#limitations-of-stopstart-operation)

## <a name="how-to-stopstart-the-azure-database-for-mysql-using-azure-portal"></a>Como parar/iniciar o banco de dados do Azure para MySQL usando portal do Azure

### <a name="stop-a-running-server"></a>Parar um servidor em execução

1.  Na [portal do Azure](https://portal.azure.com/), escolha o servidor MySQL que você deseja parar.

2.  Na página **visão geral** , clique no botão **parar** na barra de ferramentas.

    :::image type="content" source="./media/howto-stop-start-server/mysql-stop-server.png" alt-text="Servidor de parada do banco de dados do Azure para MySQL":::

    > [!NOTE]
    > Depois que o servidor for interrompido, as outras operações de gerenciamento não estarão disponíveis para o servidor único.

### <a name="start-a-stopped-server"></a>Iniciar um servidor interrompido

1.  Na [portal do Azure](https://portal.azure.com/), escolha o servidor único que você deseja iniciar.

2.  Na página **visão geral** , clique no botão **Iniciar** na barra de ferramentas.

    :::image type="content" source="./media/howto-stop-start-server/mysql-start-server.png" alt-text="Servidor inicial do banco de dados do Azure para MySQL":::

    > [!NOTE]
    > Depois que o servidor é iniciado, todas as operações de gerenciamento agora estão disponíveis para o servidor único.

## <a name="how-to-stopstart-the-azure-database-for-mysql-using-cli"></a>Como parar/iniciar o banco de dados do Azure para MySQL usando a CLI

### <a name="stop-a-running-server"></a>Parar um servidor em execução

1.  Na [portal do Azure](https://portal.azure.com/), escolha o servidor MySQL que você deseja parar.

2.  Na página **visão geral** , clique no botão **parar** na barra de ferramentas.

    ```azurecli-interactive
    az mysql server stop --name <server-name> -g <resource-group-name>
    ```
    > [!NOTE]
    > Depois que o servidor for interrompido, as outras operações de gerenciamento não estarão disponíveis para o servidor único.

### <a name="start-a-stopped-server"></a>Iniciar um servidor interrompido

1.  Na [portal do Azure](https://portal.azure.com/), escolha o servidor único que você deseja iniciar.

2.  Na página **visão geral** , clique no botão **Iniciar** na barra de ferramentas.

    ```azurecli-interactive
    az mysql server start --name <server-name> -g <resource-group-name>
    ```
    > [!NOTE]
    > Depois que o servidor é iniciado, todas as operações de gerenciamento agora estão disponíveis para o servidor único.

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre [como criar alertas sobre métricas](howto-alert-on-metric.md).
