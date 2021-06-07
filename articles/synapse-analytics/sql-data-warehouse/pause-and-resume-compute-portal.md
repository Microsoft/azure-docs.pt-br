---
title: 'Início Rápido: Pausar e retomar a computação no pool de SQL dedicado por meio do portal do Azure'
description: Use o portal do Azure para pausar a computação para o pool de SQL dedicado para economizar custos. Retomar computação quando você estiver pronto para usar o Data Warehouse.
services: synapse-analytics
author: julieMSFT
ms.author: jrasnick
manager: craigg
ms.reviewer: igorstan
ms.date: 11/23/2020
ms.topic: quickstart
ms.service: synapse-analytics
ms.subservice: sql-dw
ms.custom:
- seo-lt-2019
- azure-synapse
- mode-portal
ms.openlocfilehash: 27587f436013bcaccbacd668ce62f1d939f89e48
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107566810"
---
# <a name="quickstart-pause-and-resume-compute-in-dedicated-sql-pool-via-the-azure-portal"></a>Início Rápido: Pausar e retomar a computação no pool de SQL dedicado por meio do portal do Azure

Você pode usar o portal do Azure para pausar e retomar os recursos de computação do pool de SQL dedicado. Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Entre no [portal do Azure](https://portal.azure.com/).

## <a name="before-you-begin"></a>Antes de começar

Use [Criar e conectar – portal](../quickstart-create-sql-pool-portal.md) para criar um pool de SQL dedicado chamado **mySampleDataWarehouse**. 

## <a name="pause-compute"></a>Pausar computação

Para reduzir custos, você pode pausar e retomar os recursos de computação sob demanda. Por exemplo, se você não usar banco de dados durante a noite e nos finais de semana, você poderá pausá-lo durante esses períodos e retomá-lo durante o dia.
 
>[!NOTE]
>Você não será cobrado por recursos de computação enquanto o banco de dados estiver em pausa. No entanto, você continuará sendo cobrado pelo armazenamento. 

Siga estas etapas para pausar um pool de SQL dedicado:

1. Entre no [portal do Azure](https://portal.azure.com/).
2. Navegue até você é a página **Pool de SQL dedicado** para abrir o pool de SQL. 
3. Verifique se **Status** é **Online**.

    ![Computação on-line](././media/pause-and-resume-compute-portal/compute-online.png)

4. Para pausar o pool de SQL dedicado, clique no botão **Pausar**. 
5. Uma pergunta de confirmação será exibida perguntando se deseja continuar. Clique em **Sim**.
6. Aguarde alguns minutos e, em seguida, observe se **Status** indica **Pausando**.

    ![A captura de tela mostra a portal do Azure para um data warehouse de amostra com um valor de Status de Pausando.](./media/pause-and-resume-compute-portal/pausing.png)

7. Quando a operação de pausa for concluída, o status indica **Em Pausa** e o botão de opção é **Retomar**.
8. Os recursos de computação do pool de SQL dedicado agora estão offline. Você não será cobrado por computação até você reiniciar o serviço.

    ![Computação offline](././media/pause-and-resume-compute-portal/compute-offline.png)


## <a name="resume-compute"></a>Retomar a computação

Siga estas etapas para retomar um pool de SQL dedicado.

1. Navegue até você é a página **Pool de SQL dedicado** para abrir o pool de SQL.
3. Na página **mySampleDataWarehouse**, observe se **Status** indica **Pausado**.

    ![Computação offline](././media/pause-and-resume-compute-portal/compute-offline.png)

1. Para retomar o pool de SQL, clique em **Retomar**. 
1. Uma pergunta de confirmação será exibida perguntando se deseja continuar. Clique em **Sim**.
1. Observe se o **Status** indica **Continuando**.

    ![A captura de tela mostra a portal do Azure para um data warehouse de amostra com o botão Iniciar selecionado e um valor de Status de Retomando.](./media/pause-and-resume-compute-portal/resuming.png)

1. Quando o pool de SQL estiver novamente online, o status indicará **Online** e o botão de opção será **Pausar**.
1. Os recursos de computação para o pool de SQL estão online e você pode usar o serviço. Encargos de computação foram retomados.

    ![Computação on-line](././media/pause-and-resume-compute-portal/compute-online.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Você está sendo cobrado por unidades de data warehouse e pelos dados armazenados em seu pool de SQL dedicado. Esses recursos de computação e armazenamento são cobrados separadamente. 

- Se você quiser manter os dados no armazenamento, pause a computação.
- Se você quiser remover encargos futuros, poderá excluir o pool de SQL dedicado. 

Siga estas etapas para limpar os recursos conforme desejado.

1. Entre no [portal do Azure](https://portal.azure.com) e selecione seu pool de SQL dedicado.

    ![Limpar os recursos](./media/pause-and-resume-compute-portal/clean-up-resources.png)

1. Para pausar a computação, clique no botão **Pausar**. 

1. Para remover o pool de SQL dedicado para que não seja cobrado pela computação ou pelo armazenamento, clique em **Excluir**.



## <a name="next-steps"></a>Próximas etapas

Agora você pausou e retomou a computação para o pool de SQL dedicado. Passe para o próximo artigo para saber mais sobre como [Carregar dados para o pool de SQL dedicado](./load-data-from-azure-blob-storage-using-copy.md). Para obter informações adicionais sobre como gerenciar funcionalidades de computação, confira o artigo [Gerenciar visão geral de computação](sql-data-warehouse-manage-compute-overview.md).
