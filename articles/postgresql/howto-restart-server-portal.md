---
title: Reiniciar o servidor-portal do Azure-banco de dados do Azure para PostgreSQL-servidor único
description: Este artigo descreve como você pode reiniciar um banco de dados do Azure para PostgreSQL-servidor único usando o portal do Azure.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: how-to
ms.date: 12/20/2020
ms.openlocfilehash: 1a1afabd606df70ec60cf4fa7c8530ff95a0564f
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105604848"
---
# <a name="restart-azure-database-for-postgresql---single-server-using-the-azure-portal"></a>Reiniciar o banco de dados do Azure para PostgreSQL-servidor único usando o portal do Azure
Este tópico descreve como você pode reiniciar um servidor do Banco de Dados do Azure para PostgreSQL. Você talvez precise reiniciar o servidor por razões de manutenção, o que causa uma breve interrupção, conforme o servidor executa a operação.

A reinicialização do servidor será bloqueada se o serviço estiver ocupado. Por exemplo, o serviço pode estar processando uma operação solicitada anteriormente como o dimensionamento vCores.
 
> [!NOTE] 
> O tempo necessário para concluir uma reinicialização depende do processo de recuperação do PostgreSQL. Para diminuir o tempo de reinicialização, é recomendável que você minimize a quantidade de atividade que ocorre no servidor antes da reinicialização. Talvez você também queira aumentar a frequência do ponto de verificação. Você também pode ajustar os valores de parâmetro relacionados ao ponto de verificação, incluindo `max_wal_size` . Também é recomendável executar `CHECKPOINT` o comando antes de reiniciar o servidor.

## <a name="prerequisites"></a>Pré-requisitos
Para concluir este guia de instruções, você precisa:
- Um [banco de dados do Azure para servidor PostgreSQL](quickstart-create-server-database-portal.md)

## <a name="perform-server-restart"></a>Realizar a reinicialização do servidor

As etapas a seguir reiniciam o servidor PostgreSQL:

1. Na [portal do Azure](https://portal.azure.com/), selecione seu banco de dados do Azure para o servidor PostgreSQL.

2. Na barra de ferramentas da página de **Visão Geral** do servidor, clique em **Reiniciar**.

   :::image type="content" source="./media/howto-restart-server-portal/2-server.png" alt-text="Banco de Dados do Azure para PostgreSQL - Visão geral - botão Reiniciar":::

3. Clique em **Sim** para confirmar a reinicialização do servidor.

   :::image type="content" source="./media/howto-restart-server-portal/3-restart-confirm.png" alt-text="Banco de Dados do Azure para PostgreSQL - Restaurar confirmação":::

4. Observe que o status do servidor muda para "Reiniciando".

   :::image type="content" source="./media/howto-restart-server-portal/4-restarting-status.png" alt-text="Banco de Dados do Azure para PostgreSQL - Reiniciar status":::

5. Confirme se a reinicialização do servidor foi bem-sucedida.

   :::image type="content" source="./media/howto-restart-server-portal/5-restart-success.png" alt-text="Banco de Dados do Azure para PostgreSQL - Reiniciar com êxito":::

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre [como definir parâmetros no banco de dados do Azure para PostgreSQL](howto-configure-server-parameters-using-portal.md)
