---
title: Reiniciar um Banco de Dados do Azure para MariaDB usando o portal do Azure
description: Este artigo descreve como restaurar um servidor no Banco de Dados do Azure para MariaDB usando o Portal do Azure.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 2/7/2019
ms.openlocfilehash: 232037562c4a84ee9217e2e89a0da2ffdc37d560
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60745720"
---
# <a name="restart-azure-database-for-mariadb-server-using-azure-portal"></a>Reiniciar um Banco de Dados do Azure para MariaDB usando o portal do Azure
Este tópico descreve como você pode reiniciar um servidor do Banco de Dados do Azure para MariaDB. Você talvez precise reiniciar o servidor por razões de manutenção, o que causa uma breve interrupção, conforme o servidor executa a operação.

A reinicialização do servidor será bloqueada se o serviço estiver ocupado. Por exemplo, o serviço pode estar processando uma operação solicitada anteriormente como o dimensionamento vCores.

O tempo necessário para concluir uma reinicialização depende do processo de recuperação do MariaDB. Para diminuir o tempo de reinicialização, é recomendável que você minimize a quantidade de atividade que ocorre no servidor antes da reinicialização.

## <a name="prerequisites"></a>Pré-requisitos
Para concluir este guia de instruções, você precisa:
- Um [banco de dados do Azure para MariaDB](./quickstart-create-mariadb-server-database-using-azure-portal.md)

## <a name="perform-server-restart"></a>Realizar a reinicialização do servidor

As etapas a seguir reiniciam o servidor MariaDB:

1. No portal do Azure, selecione o servidor do Banco de Dados do Azure para MariaDB.

2. Na barra de ferramentas da página de **Visão Geral** do servidor, clique em **Reiniciar**.

   ![Banco de Dados do Azure para MariaDB - Visão geral - Botão Reiniciar](./media/howto-restart-server-portal/2-server.png)

3. Clique em **Sim** para confirmar a reinicialização do servidor.

   ![Banco de Dados do Azure para MariaDB - Confirmar reinicialização](./media/howto-restart-server-portal/3-restart-confirm.png)

4. Observe que o status do servidor muda para "Reiniciando".

   ![Banco de Dados do Azure para MariaDB - Status da reinicialização](./media/howto-restart-server-portal/4-restarting-status.png)

5. Confirme se a reinicialização do servidor foi bem-sucedida.

   ![Banco de Dados do Azure para MariaDB - Resultado da reinicialização](./media/howto-restart-server-portal/5-restart-success.png)

## <a name="next-steps"></a>Próximas etapas

[Início Rápido: Criar Banco de Dados do Azure para MariaDB usando o portal do Azure](./quickstart-create-mariadb-server-database-using-azure-portal.md)