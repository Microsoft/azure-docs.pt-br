---
author: rothja
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 10/26/2018
ms.author: jroth
ms.openlocfilehash: 4e79fef08af8ff73ce63ab4732c9efd77e3a5d3f
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67171877"
---
### <a name="configure-a-dns-label-for-the-public-ip-address"></a>Configurar um rótulo de DNS para o endereço IP público

Para conectar o Mecanismo de Banco de Dados do SQL Server na Internet, considere criar um Rótulo DNS para seu endereço IP público. Você pode conectar pelo endereço IP, mas o rótulo DNS cria um Registro A que é mais fácil de identificar e abstrai o endereço IP público subjacente.

> [!NOTE]
> Rótulos de DNS não são obrigatórios se você planeja se conectar somente à instância do SQL Server na mesma Rede Virtual ou apenas localmente.

Para criar um rótulo de DNS, primeiro selecione **Máquinas Virtuais** no portal. Selecione sua VM do SQL Server para exibir suas propriedades.

1. Na visão geral da máquina virtual, selecione seu **Endereço IP público**.

    ![endereço ip público](./media/virtual-machines-sql-server-connection-steps/rm-public-ip-address.png)

1. Nas propriedades de seu Endereço IP Público, expanda **Configuração**.

1. Insira um nome para o rótulo de DNS. Esse nome é um registro A que pode ser usado para se conectar à sua VM do SQL Server por nome em vez de por endereço IP diretamente.

1. Clique no botão **Salvar** .

    ![rótulo de dns](./media/virtual-machines-sql-server-connection-steps/rm-dns-label.png)

### <a name="connect-to-the-database-engine-from-another-computer"></a>Conectar-se ao Mecanismo de Banco de Dados de outro computador

1. Em um computador conectado à Internet, abra o SSMS (SQL Server Management Studio). Se você não tiver o SQL Server Management Studio, poderá baixá-lo [aqui](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).

1. Na caixa de diálogo **Conectar ao Servidor** ou **Conectar ao Mecanismo de Banco de Dados**, edite o valor **Nome do servidor**. Insira o endereço IP ou o nome DNS completo da máquina virtual (determinado na tarefa anterior). Você também pode adicionar uma vírgula e fornecer a porta TCP do SQL Server. Por exemplo: `mysqlvmlabel.eastus.cloudapp.azure.com,1433`.

1. Na caixa **Autenticação**, selecione **Autenticação do SQL Server**.

1. Na caixa **Login** , digite o nome de um login válido do SQL.

1. Na caixa **Senha** , digite a senha de login.

1. Clique em **Conectar**.

    ![conexão ssms](./media/virtual-machines-sql-server-connection-steps/rm-ssms-connect.png)