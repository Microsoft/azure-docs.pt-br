---
title: Restart-portal do Azure-banco de dados do Azure para PostgreSQL-servidor flexível
description: Este artigo descreve como executar operações de reinicialização no banco de dados do Azure para PostgreSQL por meio do portal do Azure.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 952bd6dddf9f276ed1a4a18f03799147f1902198
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "90932817"
---
# <a name="restart-azure-database-for-postgresql---flexible-server"></a>Reiniciar o banco de dados do Azure para PostgreSQL-servidor flexível

> [!IMPORTANT]
> O Servidor Flexível do Banco de Dados do Azure para PostgreSQL está em versão prévia

Este artigo fornece um procedimento passo a passo para executar a reinicialização do servidor flexível. Essa operação é útil para aplicar qualquer alteração de parâmetro estático que exija a reinicialização do servidor de banco de dados. O procedimento é o mesmo para servidores configurados com alta disponibilidade com redundância de zona. 

> [!IMPORTANT]
> Quando configurado com alta disponibilidade, os servidores primário e em espera são reiniciados ao mesmo tempo.

## <a name="pre-requisites"></a>Pré-requisitos

Para concluir este guia de instruções, você precisa:

-   Você deve ter um servidor flexível.

## <a name="restart-your-flexible-server"></a>Reinicie o servidor flexível

Siga estas etapas para reiniciar o servidor flexível.

1.  Na [portal do Azure](https://portal.azure.com/), escolha o servidor flexível que você deseja reiniciar.

2.  Clique em **visão geral** no painel esquerdo e clique em **reiniciar**.
   
     :::image type="content" source="./media/how-to-restart-server-portal/restart-base-page.png" alt-text="Reiniciar seleção":::

3.  Uma mensagem de confirmação pop-up será exibida.

4.  Clique em **Sim** se desejar continuar.
   
     :::image type="content" source="./media/how-to-restart-server-portal/restart-pop-up.png" alt-text="Reiniciar confirmação":::
 
6.  Será mostrada uma notificação de que a operação de reinicialização foi iniciada.

## <a name="next-steps"></a>Próximas etapas

-   Saiba mais sobre a [continuidade dos negócios](./concepts-business-continuity.md)
-   Saiba mais sobre [alta disponibilidade redundante de zona](./concepts-high-availability.md)
