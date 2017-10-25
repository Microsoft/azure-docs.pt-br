1. Clicar em **Conectar** cria e baixa um arquivo .rdp (Protocolo de Área de Trabalho Remota). Clique em **Abrir** para usar esse arquivo.
2. Você receberá um aviso de que o .rdp é proveniente de um editor desconhecido. Isso é normal. Na janela de Área de Trabalho Remota, clique em **Conectar** para continuar.
   
    ![Captura de tela de um aviso sobre um editor desconhecido.](./media/virtual-machines-log-on-win-server/rdp-warn.png)
3. Na janela **Segurança do Windows**, digite as credenciais de uma conta na máquina virtual e clique em **OK**.
   
     **Conta local** - normalmente, são o nome de usuário e a senha da conta local especificados por você no momento da criação da máquina virtual. Nesse caso, o domínio é o nome da máquina virtual e é inserido como *nomedavm*&#92;*nome de usuário*.  
   
    **VM Ingressada no Domínio** – se a VM pertencer a um domínio, digite o nome de usuário no formato *Domínio*&#92;*Nome de usuário*. A conta precisa estar no grupo Administradores ou ter privilégios de acesso remoto concedidos à VM.
   
    **Controlador de domínio** - se a VM for um controlador de domínio, digite o nome de usuário e a senha da conta de administrador para esse domínio.
4. Clique em **Sim** para verificar a identidade da máquina virtual e concluir o logon.
   
   ![Captura de tela mostrando uma mensagem sobre como verificar a identidade da VM.](./media/virtual-machines-log-on-win-server/cert-warning.png)

