---
title: Como restaurar um servidor no Banco de Dados do Azure para PostgreSQL | Microsoft Docs
description: Este artigo descreve como restaurar um servidor no Banco de Dados do Azure para PostgreSQL usando o Portal do Azure.
services: postgresql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 07/20/2017
ms.openlocfilehash: 3fbdb7741481bd3620466c3489d3609f9ea6961f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-backup-and-restore-a-server-in-azure-database-for-postgresql-using-the-azure-portal"></a>Como fazer backup e restaurar um servidor no Banco de Dados do Azure para PostgreSQL usando o Portal do Azure

## <a name="backup-happens-automatically"></a>O backup ocorre automaticamente
Ao usar o Banco de Dados do Azure para PostgreSQL, o serviço de banco de dados faz automaticamente um backup do serviço a cada cinco minutos. 

Os backups ficam disponíveis por sete dias ao usar a camada Basic e 35 dias ao usar a camada Standard. Para saber mais, confira [Camadas do Banco de Dados do Azure para PostgreSQL](concepts-service-tiers.md)

Com esse recurso de backup automático você pode restaurar o servidor e todos os seus bancos de dados em um novo servidor em um ponto anterior no tempo.

## <a name="restore-in-the-azure-portal"></a>Restaurar no Portal do Azure
O Banco de Dados do Azure para PostgreSQL permite a restauração do servidor em um ponto anterior no tempo e em uma nova cópia do servidor. Use esse novo servidor para recuperar os dados. 

Por exemplo, se uma tabela for acidentalmente descartada ao meio-dia de hoje, você poderá restaurar em um momento logo antes do meio-dia e recuperar a tabela e os dados dessa nova cópia do servidor.

As etapas a seguir restauram o exemplo de servidor para um ponto no tempo:
1. Entre no [Portal do Azure](https://portal.azure.com/)
2. Localize seu servidor de Banco de Dados do Azure para PostgreSQL. No Portal do Azure, clique em **Todos os Recursos** no menu esquerdo e digite o nome, como **mypgserver-20170401**, para procurar o servidor existente. Clique no nome do servidor listado nos resultados da pesquisa. A página **Visão geral** do servidor é aberta e oferece outras opções de configuração.

   ![Portal do Azure - Pesquisar para localizar o servidor](media/postgresql-howto-restore-server-portal/1-locate.png)

3. Na parte superior da folha de visão geral do servidor, clique em **Restaurar** na barra de ferramentas. A folha Restaurar é aberta.

   ![Banco de Dados do Azure para PostgreSQL - Visão geral - botão Restaurar](./media/postgresql-howto-restore-server-portal/2_server.png)

4. Preencha o formulário Restaurar com as informações necessárias:

   ![Banco de Dados do Azure para PostgreSQL - Informações de restauração ](./media/postgresql-howto-restore-server-portal/3_restore.png)
  - **Ponto de restauração**: selecione um ponto no tempo anterior à alteração do servidor
  - **Servidor de destino**: forneça um novo nome de servidor no qual você deseja restaurar
  - **Local**: não é possível selecionar a região; por padrão, ela é igual ao servidor de origem
  - **Tipo de preço**: não é possível alterar esse valor ao restaurar um servidor. Ele é igual ao servidor de origem. 

5. Clique em **OK** para restaurar o servidor em um ponto no tempo. 

6. Após a conclusão da restauração, localize o novo servidor criado para verificar se os dados foram restaurados conforme o esperado.

## <a name="next-steps"></a>Próximas etapas
- [Bibliotecas de conexão para o Banco de Dados do Azure para PostgreSQL](concepts-connection-libraries.md)
