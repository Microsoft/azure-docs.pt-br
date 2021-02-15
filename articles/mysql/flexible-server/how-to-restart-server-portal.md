---
title: Reinicie o servidor-portal do Azure-banco de dados do Azure para MySQL-servidor flexível
description: Este artigo descreve como você pode reiniciar um servidor flexível do banco de dados do Azure para MySQL usando o portal do Azure.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 10/26/2020
ms.openlocfilehash: 88a1524875f168b49f50f1684c650d5bc178bf38
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94542653"
---
# <a name="restart-azure-database-for-mysql-flexible-server-using-azure-portal"></a>Reiniciar o servidor flexível do banco de dados do Azure para MySQL usando o portal do Azure
Este tópico descreve como você pode reiniciar um servidor flexível do banco de dados do Azure para MySQL. Você talvez precise reiniciar o servidor por razões de manutenção, o que causa uma breve interrupção, conforme o servidor executa a operação.

A reinicialização do servidor será bloqueada se o serviço estiver ocupado. Por exemplo, o serviço pode estar processando uma operação solicitada anteriormente como o dimensionamento vCores.

O tempo necessário para concluir uma reinicialização depende do processo de recuperação do MySQL. Para diminuir o tempo de reinicialização, é recomendável que você minimize a quantidade de atividade que ocorre no servidor antes da reinicialização.

## <a name="prerequisites"></a>Pré-requisitos
Para concluir este guia de instruções, você precisa:
- Um [servidor flexível do banco de dados do Azure para MySQL](quickstart-create-server-portal.md)

## <a name="perform-server-restart"></a>Realizar a reinicialização do servidor

As etapas a seguir reiniciam o servidor MySQL:

1. Na portal do Azure, selecione o servidor flexível do banco de dados do Azure para MySQL.

2. Na barra de ferramentas da página de **Visão Geral** do servidor, clique em **Reiniciar**.

   :::image type="content" source="./media/how-to-restart-server-portal/2-server.png" alt-text="Banco de Dados do Azure para MySQL - Visão geral - botão Reiniciar":::

3. Clique em **Sim** para confirmar a reinicialização do servidor.

   :::image type="content" source="./media/how-to-restart-server-portal/3-restart-confirm.png" alt-text="Banco de Dados do Azure para MySQL - Restaurar confirmação":::

4. Observe que o status do servidor muda para "Reiniciando".

   :::image type="content" source="./media/how-to-restart-server-portal/4-restarting-status.png" alt-text="Banco de Dados do Azure para MySQL - Reiniciar status":::

5. Confirme se a reinicialização do servidor foi bem-sucedida.

   :::image type="content" source="./media/how-to-restart-server-portal/5-restart-success.png" alt-text="Banco de Dados do Azure para MySQL - Reiniciar status":::

## <a name="next-steps"></a>Próximas etapas

[Início rápido: criar um servidor flexível do banco de dados do Azure para MySQL usando o portal do Azure](quickstart-create-server-portal.md)
