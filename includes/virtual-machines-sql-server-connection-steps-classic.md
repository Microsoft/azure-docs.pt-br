---
author: rothja
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 10/26/2018
ms.author: jroth
ms.openlocfilehash: 57f238a8f91df1271e91894b88a7f02118b1f123
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67171866"
---
### <a name="determine-the-dns-name-of-the-virtual-machine"></a>Determinar o nome DNS da máquina virtual
Para conectar-se ao Mecanismo de Banco de Dados do SQL Server em outro computador, você deve saber o nome DNS (Sistema de Nome de Domínio) da máquina virtual. (Esse é o nome que a Internet usa para identificar a máquina virtual. Você pode usar o endereço IP, mas o endereço IP pode ser alterado quando o Azure mover os recursos para redundância ou manutenção. O nome DNS será estável porque pode ser redirecionado para um novo endereço IP.)  

1. No Portal do Azure (ou na etapa anterior), selecione **Máquinas virtuais (clássico)** .
2. Selecione sua VM do SQL.
3. Na folha **Máquina Virtual**, copie o **Nome DNS** para a máquina virtual.
   
    ![Nome DNS](./media/virtual-machines-sql-server-connection-steps/sql-vm-dns-name.png)

### <a name="connect-to-the-database-engine-from-another-computer"></a>Conectar-se ao Mecanismo de Banco de Dados de outro computador
1. Em um computador conectado à Internet, abra o SQL Server Management Studio.
2. Na caixa de diálogo **Conectar ao Servidor** ou **Conectar ao Mecanismo de Banco de Dados**, na caixa **Nome do servidor**, digite o nome DNS da máquina virtual (determinado na tarefa anterior) e um número de porta pública de ponto de extremidade no formato *NomeDNS, númerodaporta*, como **mysqlvm.cloudapp.net,57500**.
   
    ![Conectar-se usando SSMS](./media/virtual-machines-sql-server-connection-steps/33Connect-SSMS.png)
   
    Se não lembrar o número da porta do ponto de extremidade público criado anteriormente, você pode encontrá-lo na área **Pontos de extremidade** da folha **Máquina Virtual**.
   
    ![Porta pública](./media/virtual-machines-sql-server-connection-steps/sql-vm-port-number.png)
3. Na caixa **Autenticação**, selecione **Autenticação do SQL Server**.
4. Na caixa **Logon** , digite o nome de um logon que você criou em uma tarefa anterior.
5. Na caixa **Senha** , digite a senha do logon que você criou em uma tarefa anterior.
6. Clique em **Conectar**.

