---
title: Como restaurar um servidor no Banco de Dados do Azure para MySQL | Microsoft Docs
description: Este artigo descreve como restaurar um servidor no Banco de Dados do Azure para MySQL usando o Portal do Azure.
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 09/15/2017
ms.openlocfilehash: 6c1c0f8a0c0e59661b70b787b551b8cfdb024cda
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-back-up-and-restore-a-server-in-azure-database-for-mysql-by-using-the-azure-portal"></a>Como fazer backup e restaurar um servidor no Banco de Dados do Azure para MySQL usando o Portal do Azure

## <a name="backup-happens-automatically"></a>O backup ocorre automaticamente
Ao usar o Banco de Dados do Azure para MySQL, o serviço de banco de dados faz automaticamente um backup do serviço a cada cinco minutos. 

Os backups ficam disponíveis por sete dias ao usar a camada Basic e 35 dias ao usar a camada Standard. Para saber mais, confira [Camadas do Banco de Dados do Azure para MySQL](concepts-service-tiers.md)

Com esse recurso de backup automático você pode restaurar o servidor e todos os seus bancos de dados em um novo servidor em um ponto anterior no tempo.

## <a name="restore-in-the-azure-portal"></a>Restaurar no Portal do Azure
O Banco de Dados do Azure para MySQL permite a restauração do servidor em um ponto específico no tempo e em uma nova cópia do servidor. Use esse novo servidor para recuperar os dados. 

Por exemplo, se uma tabela for acidentalmente descartada ao meio-dia de hoje, é possível restaurá-la em um momento logo antes do meio-dia e recuperar a tabela e os dados dessa nova cópia do servidor.

As etapas a seguir restauram o exemplo de servidor para um ponto específico no tempo:

1. Entre no [Portal do Azure](https://portal.azure.com/)

2. Localizar seu servidor de Banco de Dados do Azure para MySQL. No painel esquerdo, selecione **Todos os recursos** e, depois, selecione o servidor na lista.

3.  Na parte superior da folha de visão geral do servidor, clique em **Restaurar** na barra de ferramentas. A folha Restaurar é aberta.
![clique no botão restaurar](./media/howto-restore-server-portal/click-restore-button.png)

4. Preencha o formulário Restaurar com as informações necessárias:

- **Ponto de restauração (UTC)**: usando o seletor de data e o seletor de hora, selecione um ponto no tempo para o qual restaurar. A hora especificada está no formato UTC, então provavelmente você precisará converter a hora local em UTC.
- **Restaurar em novo servidor**: forneça um novo nome de servidor no qual restaurar o servidor existente.
- **Local**: a opção de região é preenchida automaticamente com a região do servidor de origem e não pode ser alterada.
- **Tipo de preço**: a opção de tipo de preço é preenchida automaticamente com o mesmo tipo de preço do servidor de origem e não pode ser alterada aqui. 
![Restauração PITR](./media/howto-restore-server-portal/pitr-restore.png)

5. Clique em **OK** para restaurar o servidor em um ponto específico no tempo. 

6. Após a conclusão da restauração, localize o novo servidor criado para verificar se os bancos de dados foram restaurados conforme o esperado.

## <a name="next-steps"></a>Próximas etapas
- [Bibliotecas de conexão para o Banco de Dados do Azure para MySQL](concepts-connection-libraries.md).