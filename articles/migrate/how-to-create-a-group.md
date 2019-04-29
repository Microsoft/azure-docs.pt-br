---
title: Agrupar máquinas para avaliação com as Migrações para Azure | Microsoft Docs
description: Descreve como agrupar máquinas antes de executar uma avaliação com o serviço Migrações para Azure.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 11/28/2018
ms.author: raynew
ms.openlocfilehash: c11d2f22fa08417107b0eecdd902b4521410b358
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60544902"
---
# <a name="group-machines-for-assessment"></a>Agrupar as máquinas para avaliação

Este artigo descreve como criar um grupo de máquinas para avaliação usando as [Migrações para Azure](migrate-overview.md). As Migrações para Azure avaliam máquinas no grupo para verificar se são adequadas para a migração para o Azure, e fornecem estimativas de dimensionamento e custo para execução da máquina no Azure. Se você souber quais os computadores precisam ser migrados juntos, crie manualmente o grupo de migrações para Azure usando o seguinte método. Se você não tiver muita certeza de quais computadores precisam ser agrupados, use a funcionalidade de visualização de dependência nas Migrações para Azure para criar grupos. [Saiba mais.](how-to-create-group-machine-dependencies.md)

> [!NOTE]
> A funcionalidade de visualização de dependências não está disponível no Azure Governamental.

## <a name="create-a-group"></a>Criar um grupo

1. Na **Visão geral** do projeto Migrações para Azure, em Gerenciar, clique em **Grupos** > **+ Grupo** e especifique um nome de grupo.
2. Adicione uma ou mais máquinas ao grupo e, em seguida, clique em  **Criar**.
3. Opcionalmente, você pode selecionar a execução de uma nova avaliação para o grupo.

    ![Criar um grupo](./media/how-to-create-a-group/create-group.png)

Depois que o grupo é criado, você pode modificá-lo selecionando-o na página **Grupos** e adicionando ou removendo computadores.

## <a name="next-steps"></a>Próximas etapas

- Saiba como usar o [mapeamento de dependência de máquina](how-to-create-group-machine-dependencies.md) para criar grupos com mais confiança.
- [Saiba mais](concepts-assessment-calculation.md) sobre como as avaliações são calculadas.
