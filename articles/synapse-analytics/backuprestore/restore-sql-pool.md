---
title: Restaurar um pool SQL dedicado existente
description: Guia de instruções para restaurar um pool SQL dedicado existente.
services: synapse-analytics
author: joannapea
manager: igorstan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 10/29/2020
ms.author: joanpo
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 86150107273b247bef2c74e5b8c5272d7148587e
ms.sourcegitcommit: 46c5ffd69fa7bc71102737d1fab4338ca782b6f1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2020
ms.locfileid: "94332029"
---
# <a name="restore-an-existing-dedicated-sql-pool"></a>Restaurar um pool SQL dedicado existente

Neste artigo, você aprenderá a restaurar um pool SQL dedicado existente na análise de Synapse do Azure usando o portal do Azure e o Synapse Studio. Este artigo se aplica a restaurações e restaurações geográficas. 

## <a name="restore-an-existing-dedicated-sql-pool-through-the-synapse-studio"></a>Restaurar um pool SQL dedicado existente por meio do Synapse Studio

1. Entre no [portal do Azure](https://portal.azure.com/).
2. Navegue até seu espaço de trabalho Synapse. 
3. Em Introdução-> Abra o Synapse Studio, selecione **abrir**.

    ![ Synapse Studio](../media/sql-pools/open-synapse-studio.png)
4. No painel de navegação à esquerda, selecione **dados**.
5. Selecione **Gerenciar Pools**. 
6. Selecione **+ novo** para criar um novo pool dedicado do SQL. 
7. Na guia configurações adicionais, selecione um ponto de restauração do qual restaurar. 

    Se você quiser executar uma restauração geográfica, selecione o espaço de trabalho e o pool SQL dedicado que você deseja recuperar. 

8. Selecione **Pontos de Restauração Automática** ou **Pontos de Restauração Definido Pelo Usuário**. 

    ![Pontos de restauração](../media/sql-pools/restore-point.PNG)

    Se o pool SQL dedicado não tiver pontos de restauração automáticos, aguarde algumas horas ou crie um ponto de restauração definido pelo usuário antes de restaurar. Para User-Defined pontos de restauração, selecione um existente ou crie um novo.

    Se você estiver restaurando um backup geográfico, basta selecionar o espaço de trabalho localizado na região de origem e o pool de SQL dedicado que você deseja restaurar. 

9. Selecione **Examinar + criar**.

## <a name="restore-an-existing-dedicated-sql-pool-through-the-azure-portal"></a>Restaurar um pool SQL dedicado existente por meio do portal do Azure

1. Entre no [portal do Azure](https://portal.azure.com/).
2. Navegue até o pool do SQL dedicado do qual você deseja restaurar.
3. Na parte superior da folha Visão Geral, selecione **Restaurar**.

    ![ Visão Geral de Restauração](../media/sql-pools/restore-sqlpool-01.png)

4. Selecione **Pontos de Restauração Automática** ou **Pontos de Restauração Definido Pelo Usuário**. 

    Se o pool SQL dedicado não tiver pontos de restauração automáticos, aguarde algumas horas ou crie um ponto de restauração definido pelo usuário antes de restaurar. 

    Se você quiser executar uma restauração geográfica, selecione o espaço de trabalho e o pool SQL dedicado que você deseja recuperar. 

5. Selecione **Examinar + criar**.

## <a name="next-steps"></a>Próximas etapas

- [Criar um ponto de restauração](sqlpool-create-restore-point.md)
