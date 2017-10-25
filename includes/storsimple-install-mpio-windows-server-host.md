#### <a name="to-install-mpio-on-the-host"></a>Para instalar o MPIO no host
1. Abra o Gerenciador de Servidores no host do Windows Server. Por padrão, o Gerenciador de Servidores começa quando um membro do grupo Administradores faz logon em um computador que está executando o Windows Server 2012 R2 ou o Windows Server 2012. Se o Gerenciador do Servidor ainda não estiver aberto, clique em **Iniciar > Gerenciador do Servidor**.
   
    ![Gerenciador de Servidores](./media/storsimple-install-mpio-windows-server/IC740997.png)
2. Clique em **Gerenciador de Servidores > Painel de Controle > Adicionar funções e recursos**. Isso inicia o assistente **Adicionar Funções e Recursos** .
   
    ![Adicionar Assistente de Funções e Recursos 1](./media/storsimple-install-mpio-windows-server/IC740998.png)
3. No assistente **Adicionar Funções e Recursos** , faça o seguinte:
   
   * Na página **Antes de Começar**, clique em **Avançar**.
   * Na página **Selecionar tipo de instalação**, aceite a configuração padrão da instalação **Baseada em função ou recurso**. Clique em **Avançar**.
     
       ![Adicionar Assistente de Funções e Recursos 2](./media/storsimple-install-mpio-windows-server/IC740999.png)
   * Na página **Selecionar servidor de destino**, escolha **Selecionar um servidor no pool de servidores**. O servidor host deve ser descoberto automaticamente. Clique em **Avançar**.
   * Na página **Selecionar funções do servidor**, clique em **Avançar**.
   * Na página **Selecionar recursos**, selecione **Multipath I/O** e clique em **Avançar**.
     
       ![Adicionar Assistente de Funções e Recursos 5](./media/storsimple-install-mpio-windows-server/IC741000.png)
   * Na página **Confirmar seleções da instalação**, confirme a seleção e selecione **Reiniciar o servidor de destino automaticamente se necessário**, conforme mostrado abaixo. Clique em **Instalar**.
     
       ![Adicionar Assistente de Funções e Recursos 8](./media/storsimple-install-mpio-windows-server/IC741001.png)
   * Quando a instalação for concluída, você será notificado. Clique em **Fechar** para fechar o assistente.
     
       ![Adicionar Assistente de Funções e Recursos 9](./media/storsimple-install-mpio-windows-server/IC741002.png)

