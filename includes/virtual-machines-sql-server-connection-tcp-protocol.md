---
author: rothja
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 10/26/2018
ms.author: jroth
ms.openlocfilehash: 8b919608dfc562d8db77619d5215a6828a53a4aa
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67171864"
---
1. Ainda conectado à máquina virtual com a área de trabalho remota, procure **Configuration Manager**:

    ![Abrir SSCM](./media/virtual-machines-sql-server-connection-tcp-protocol/sql-server-configuration-manager.png)

1. No SQL Server Configuration Manager, no painel do console, expanda **Configuração de Rede do SQL Server**.

1. No painel do console, clique em **Protocolos para MSSQLSERVER** (nome da instância padrão.) No painel de detalhes, clique com botão direito em **TCP** e clique em **Habilitar** se essa opção não estiver habilitada.

    ![Habilitar TCP](./media/virtual-machines-sql-server-connection-tcp-protocol/enable-tcp.png)

1. No painel do console, clique em **Serviços do SQL Server**. No painel de detalhes, clique com o botão direito do mouse em **SQL Server (*nome da instância*)** (a instância padrão é **SQL Server (MSSQLSERVER)** ) e, em seguida, clique em **Reiniciar**, para parar e reiniciar a instância do SQL Server.

    ![Reiniciar o Mecanismo de Banco de Dados](./media/virtual-machines-sql-server-connection-tcp-protocol/restart-sql-server.png)

1. Feche o SQL Server Configuration Manager.

Para obter mais informações sobre como habilitar protocolos para o Mecanismo de Banco de Dados do SQL Server, consulte [Habilitar ou desabilitar um protocolo de rede de servidor (a página pode estar em inglês)](https://msdn.microsoft.com/library/ms191294.aspx).
