---
title: Restore-portal do Azure-banco de dados do Azure para PostgreSQL-servidor flexível
description: Este artigo descreve como executar operações de restauração no banco de dados do Azure para PostgreSQL por meio do portal do Azure.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: e69bcb3d9e4dca4c45bf9a6fe8ed4d54e7f4a8cd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "90933156"
---
# <a name="point-in-time-restore-of-a-flexible-server"></a>Restauração pontual de um servidor flexível

> [!IMPORTANT]
> O Servidor Flexível do Banco de Dados do Azure para PostgreSQL está em versão prévia

Este artigo fornece um procedimento passo a passo para executar recuperações pontuais no servidor flexível usando backups. Você pode executar o para um ponto de restauração mais antigo ou um ponto de restauração personalizado dentro do período de retenção.

## <a name="pre-requisites"></a>Pré-requisitos

Para concluir este guia de instruções, você precisa:

-   Você deve ter um servidor de banco de dados do Azure para PostgreSQL-flexível. O mesmo procedimento também é aplicável para o servidor flexível configurado com redundância de zona.

## <a name="restoring-to-the-earliest-restore-point"></a>Restaurando para o ponto de restauração mais antigo

Siga estas etapas para restaurar seu servidor flexível usando um backup existente mais antigo.

1.  Na [portal do Azure](https://portal.azure.com/), escolha o servidor flexível do qual você deseja restaurar o backup.

2.  Clique em **visão geral** no painel esquerdo e clique em **restaurar**
   
   :::image type="content" source="./media/how-to-restore-server-portal/restore-overview.png" alt-text="Visão geral da restauração":::

3.  Restore Page será mostrado com uma opção para escolher entre o ponto de restauração mais antigo e o ponto de restauração personalizado.

4.  Selecione **ponto de restauração mais antigo** e forneça um novo nome de servidor no campo **restaurar para o novo servidor** . O carimbo de data/hora mais antigo no qual você pode restaurar é exibido. 
   
   :::image type="content" source="./media/how-to-restore-server-portal/restore-earliest.png" alt-text="Hora de restauração mais antiga":::

5.  Clique em **OK**.

6.  Uma notificação será mostrada de que a operação de restauração foi iniciada.

## <a name="restoring-to-a-custom-restore-point"></a>Restaurando para um ponto de restauração personalizado

Siga estas etapas para restaurar seu servidor flexível usando um backup existente mais antigo.

1.  Na [portal do Azure](https://portal.azure.com/), escolha o servidor flexível do qual você deseja restaurar o backup.

2.  Na página Visão geral, clique em **restaurar**.
 :::image type="content" source="./media/how-to-restore-server-portal/restore-overview.png" alt-text="Visão geral da restauração":::
    
3.  Restore Page será mostrado com uma opção para escolher entre o ponto de restauração mais antigo e o ponto de restauração personalizado.

4.  Escolha **ponto de restauração personalizado**.

5.  Selecione data e hora e forneça um novo nome de servidor no campo **restaurar para o novo servidor** . 
   
:::image type="content" source="./media/how-to-restore-server-portal/restore-custom.png" alt-text="Tempo de restauração personalizado":::
 
6.  Clique em **OK**.

7.  Uma notificação será mostrada de que a operação de restauração foi iniciada.

## <a name="next-steps"></a>Próximas etapas

-   Saiba mais sobre a [continuidade dos negócios](./concepts-business-continuity.md)
-   Saiba mais sobre [alta disponibilidade redundante de zona](./concepts-high-availability.md)
-   Saiba mais sobre [backup e recuperação](./concepts-backup-restore.md)
