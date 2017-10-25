* Em **Adicionar vCenter**, especifique um nome amigável para o host vSphere ou servidor vCenter e então especifique o endereço IP ou o FQDN do servidor. Deixe a porta como 443, a menos que os servidores do VMware estejam configurados para escutar solicitações em uma porta diferente. Selecione a conta que deve se conectar ao VMware vCenter ou ao servidor vSphere ESXi. Clique em **OK**.

    ![VMware](./media/site-recovery-add-vcenter/vmware-server.png)

   > [!NOTE]
   > Se você estiver adicionando o servidor VMware vCenter ou o host VMware vSphere com uma conta que não tem privilégios de administrador no vCenter ou no servidor host, verifique se a conta tem estes privilégios habilitados: Data center, Armazenamento de Dados, Pasta, Host, Rede, Recursos, Máquina virtual e Comutador Distribuído do vSphere. Além disso, o servidor VMware vCenter precisa do privilégio de exibição do Armazenamento habilitado.
