---
title: Criar um ponto de restauração definido pelo usuário para um pool SQL dedicado
description: Saiba como usar o portal do Azure para criar um ponto de restauração definido pelo usuário para o pool do SQL dedicado no Azure Synapse Analytics.
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
ms.openlocfilehash: 21fd20100095040fda9f72b00e17147ff560fbca
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/13/2020
ms.locfileid: "94579531"
---
# <a name="user-defined-restore-points"></a>Pontos de restauração definidos pelo usuário

Neste artigo, você aprenderá a criar um novo ponto de restauração definido pelo usuário para um pool SQL dedicado no Azure Synapse Analytics usando o portal do Azure.

## <a name="create-user-defined-restore-points-through-the-azure-portal"></a>Criar pontos de restauração definidos pelo usuário por meio do portal do Azure

Os pontos de restauração definidos pelo usuário também podem ser criados por meio de portal do Azure.

1. Entre em sua conta do [portal do Azure](https://portal.azure.com/) .

2. Navegue até o pool SQL dedicado para o qual você deseja criar um ponto de restauração.

3. Selecione **visão geral** no painel esquerdo, selecione **+ novo ponto de restauração**. Se o botão novo ponto de restauração não estiver habilitado, verifique se o pool do SQL dedicado não está pausado.

    ![Novo Ponto de Restauração](../media/sql-pools/create-sqlpool-restore-point-01.png)

4. Especifique um nome para o ponto de restauração definido pelo usuário e clique em **aplicar**. Os pontos de restauração definidos pelo usuário têm um período de retenção padrão de sete dias.

    ![Nome do ponto de restauração](../media/sql-pools/create-sqlpool-restore-point-02.png)

## <a name="next-steps"></a>Próximas etapas

- [Restaurar um pool SQL dedicado existente](restore-sql-pool.md)

