---
author: rothja
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 10/26/2018
ms.author: jroth
ms.openlocfilehash: 297317ff33d88d6390220980ef35f2538579e310
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67171863"
---
### <a name="open-tcp-ports-in-the-windows-firewall-for-the-default-instance-of-the-database-engine"></a>Abrir portas TCP no firewall do Windows para a instância padrão do Mecanismo de Banco de Dados
1. Conecte-se à máquina virtual usando a Área de Trabalho Remota. Para obter instruções detalhadas sobre como se conectar à VM, confira [Abrir uma VM do SQL com a Área de Trabalho Remota](../articles/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision.md#remotedesktop).
2. Uma vez conectado na tela inicial, digite **WF. msc**e pressione ENTER.
   
    ![Iniciar o Programa de Firewall](./media/virtual-machines-sql-server-connection-steps/12Open-WF.png)
3. No **Firewall do Windows com Segurança Avançada**, no painel esquerdo, clique com o botão direito do mouse em **Regras de Entrada** e, em seguida, clique em **Nova Regra** no painel de ações.
   
    ![Nova Regra](./media/virtual-machines-sql-server-connection-steps/13New-FW-Rule.png)
4. Na caixa de diálogo **Assistente para Nova Regra de Entrada**, em **Tipo de Regra**, selecione **Porta** e clique em **Avançar**.
5. Na caixa de diálogo **Protocolo e Portas**, use o **TCP** padrão. Na caixa **Portas locais específicas**, digite o número da porta da instância do Mecanismo de Banco de Dados (**1433** para a instância padrão ou sua opção para a porta privada na etapa de ponto de extremidade).
   
    ![Porta TCP 1433](./media/virtual-machines-sql-server-connection-steps/14Port-1433.png)
6. Clique em **Avançar**.
7. Na caixa de diálogo **Ação**, selecione **Permitir a conexão** e clique em **Avançar**.
   
    **Observação de segurança:** Selecionando **permitir a conexão se ela for segura** pode fornecer segurança adicional. Selecione essa opção se você desejar configurar opções de segurança adicionais em seu ambiente.
   
    ![Permitir Conexões](./media/virtual-machines-sql-server-connection-steps/15Allow-Connection.png)
8. Na caixa de diálogo **Perfil**, selecione **Público**, **Privado** e **Domínio**. Em seguida, clique em **Próximo**.
   
    **Observação de segurança:**  Selecionando **pública** permite o acesso pela internet. Sempre que possível, selecione um perfil mais restritivo.
   
    ![Perfil Público](./media/virtual-machines-sql-server-connection-steps/16Public-Private-Domain-Profile.png)
9. Na caixa de diálogo **Nome**, digite um nome e uma descrição para essa regra, e clique em **Concluir**.
   
    ![Nome da Regra](./media/virtual-machines-sql-server-connection-steps/17Rule-Name.png)

Abrir portas adicionais para outros componentes conforme necessário. Para obter mais informações, consulte [Configurando o Firewall do Windows para permitir acesso ao SQL Server (a página pode estar em inglês)](https://msdn.microsoft.com/library/cc646023.aspx).

### <a name="configure-sql-server-to-listen-on-the-tcp-protocol"></a>Configurar o SQL Server para escutar no protocolo TCP

[!INCLUDE [Enable TCP](virtual-machines-sql-server-connection-tcp-protocol.md)]

### <a name="configure-sql-server-for-mixed-mode-authentication"></a>Configurar o SQL Server para autenticação de modo misto
O Mecanismo de Banco de Dados do SQL Server não pode usar a Autenticação do Windows sem um ambiente de domínio. Para conectar-se ao Mecanismo de Banco de Dados de outro computador, configure o SQL Server para a autenticação de modo misto. A autenticação de modo misto permite a Autenticação do SQL Server e a Autenticação do Windows.

> [!NOTE]
> A configuração da autenticação de modo misto pode não ser necessária se você tiver configurado uma Rede Virtual do Azure com um ambiente de domínio configurado.
> 
> 

1. Enquanto estiver conectado à máquina virtual, na página inicial, digite **SQL Server Management Studio** e clique no ícone selecionado.
   
    Na primeira vez que você abrir o Management Studio ele deve criar o ambiente do Management Studio dos usuários. Isso pode demorar alguns instantes.
2. O Management Studio apresenta a caixa de diálogo **Conectar ao Servidor** . Na caixa **Nome do servidor**, digite o nome da máquina virtual para conectar ao Mecanismo de Banco de Dados com o Pesquisador de Objetos (em vez do nome de máquina virtual, você também pode usar **(local)** ou um único ponto como o **Nome do servidor**). Selecione **autenticação do Windows**e deixe ***your_VM_name\your_local_administrator*** no **nome de usuário** caixa. Clique em **Conectar**.
   
    ![Conectar-se ao servidor](./media/virtual-machines-sql-server-connection-steps/19Connect-to-Server.png)
3. No SQL Server Management Studio Object Explorer, clique com o botão direito do mouse no nome da instância do SQL Server (o nome da máquina virtual) e, em seguida, clique em **Propriedades**.
   
    ![Propriedades do servidor](./media/virtual-machines-sql-server-connection-steps/20Server-Properties.png)
4. Na página **Segurança**, em **Autenticação do servidor**, selecione **Modo de Autenticação do SQL Server e do Windows** e clique em **OK**.
   
    ![Selecionar modo de autenticação](./media/virtual-machines-sql-server-connection-steps/21Mixed-Mode.png)
5. Na caixa de diálogo do SQL Server Management Studio, clique em **OK** para confirmar a necessidade de reiniciar o SQL Server.
6. No Object Explorer, clique com o botão direito do mouse no seu servidor, e em seguida, clique em **Reiniciar**. (Se o SQL Server Agent estiver em execução, ele também deverá ser reinicializado.)
   
    ![Reiniciar](./media/virtual-machines-sql-server-connection-steps/22Restart2.png)
7. Na caixa de diálogo do SQL Server Management Studio, clique em **Sim** para concordar que você quer reiniciar o SQL Server.

### <a name="create-sql-server-authentication-logins"></a>Criar logons de autenticação do SQL Server
Para conectar-se ao Mecanismo de Banco de Dados de outro computador, você deve criar pelo menos um logon de autenticação do SQL Server.

1. No SQL Server Management Studio Object Explorer, expanda a pasta da instância do servidor na qual você deseja criar o novo logon.
2. Clique com o botão direito do mouse na pasta **Segurança**, aponte para **Novo** e selecione **Logon...** .
   
    ![Novo Logon](./media/virtual-machines-sql-server-connection-steps/23New-Login.png)
3. Na caixa de diálogo **Logon - Novo**, na página **Geral**, digite o nome do novo usuário na caixa **Nome de logon**.
4. Selecione **Autenticação do SQL Server**.
5. Na caixa **Senha** , digite uma senha para o novo usuário. Insira novamente essa senha na caixa **Confirmar Senha** .
6. Selecione as opções de aplicação de senha necessárias (**Aplicar política de senha**, **Aplicar expiração de senha** e **O usuário deve alterar a senha no próximo logon**). Se você estiver usando esse logon por conta própria, não será preciso exigir uma alteração de senha no próximo logon.
7. Na lista **Banco de Dados Padrão** , selecione um banco de dados padrão para o logon. **mestre** é o padrão para essa opção. Se você ainda não tiver criado um usuário de banco de dados, mantenha essa definição como **mestre**.
   
    ![Propriedades de Logon](./media/virtual-machines-sql-server-connection-steps/24Test-Login.png)
8. Se esse for o primeiro logon que você está criando, talvez você queira designá-lo como um administrador do SQL Server. Em caso afirmativo, na página **Funções de Servidor**, marque **sysadmin**.
   
   > [!NOTE]
   > Os membros da função de servidor fixa sysadmin tem controle total sobre o Mecanismo de Banco de Dados. Você deve restringir cuidadosamente a associação nessa função.
   > 
   > 
   
   ![sysadmin](./media/virtual-machines-sql-server-connection-steps/25sysadmin.png)
9. Clique em OK.

Para obter mais informações sobre logons do SQL Server, consulte [Criar um logon (a página pode estar em inglês)](https://msdn.microsoft.com/library/aa337562.aspx).

