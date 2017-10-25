O ouvinte do grupo de disponibilidade é um nome de rede e endereço IP que o grupo de disponibilidade do SQL Server escuta. Para criar o ouvinte do grupo de disponibilidade, faça o seguinte:

1. <a name="getnet"></a>Obtenha o nome do recurso de rede de cluster.

    a. Use o RDP para se conectar à máquina virtual do Azure que hospeda a réplica primária. 

    b. Abra o Gerenciador de Cluster de Failover.

    c. Selecione o nó **Redes** e observe o nome da rede do cluster. Use esse nome na variável `$ClusterNetworkName` no script do PowerShell. Na imagem a seguir, o nome da rede de clusters é **Rede de Cluster 1**:

   ![Nome da rede de clusters](./media/virtual-machines-ag-listener-configure/90-clusternetworkname.png)

2. <a name="addcap"></a>Adicionar o ponto de acesso para cliente.  
    O ponto de acesso do cliente é o nome da rede que os aplicativos usam para se conectar aos bancos de dados em um grupo de disponibilidade. Crie o ponto de acesso de cliente no Gerenciador de Cluster de Failover.

    a. Expanda o nome do cluster e, em seguida, clique em **Funções**.

    b. No painel **Funções**, clique com o botão direito do mouse no nome do grupo de disponibilidade e, em seguida, selecione **Adicionar recurso** > **Ponto de acesso para o cliente**.

   ![Ponto de acesso para cliente](./media/virtual-machines-ag-listener-configure/92-addclientaccesspoint.png)

    c. Na caixa **Nome**, crie um nome para o novo ouvinte. 
   O nome para o novo ouvinte é o nome da rede que o aplicativo usará para se conectar aos bancos de dados no grupo de disponibilidade do SQL Server.
   
    d. Para concluir a criação do ouvinte, clique em **Avançar** duas vezes e, em seguida, clique em **Concluir**. Não coloque o ouvinte ou o recurso online neste momento.

3. <a name="congroup"></a>Configurar o recurso de IP do grupo de disponibilidade.

    a. Clique na guia **Recursos**e expanda o ponto de acesso para cliente que você acabou de criar.  
    O ponto de acesso para cliente está offline.

   ![Ponto de acesso para cliente](./media/virtual-machines-ag-listener-configure/94-newclientaccesspoint.png) 

    b. Clique com o botão direito do mouse no recurso de IP e clique em Propriedades. Anote o nome do endereço IP e use-o na variável `$IPResourceName` no script do PowerShell.

    c. Em **Endereço IP**, clique em **Endereço IP Estático**. Defina o endereço IP como o mesmo endereço que você usou quando definiu o endereço do balanceador de carga no portal do Azure.

   ![Recurso de IP](./media/virtual-machines-ag-listener-configure/96-ipresource.png) 

    <!-----------------------I don't see this option on server 2016
    1. Disable NetBIOS for this address and click **OK**. Repeat this step for each IP resource if your solution spans multiple Azure VNets. 
    ------------------------->

4. <a name = "dependencyGroup"></a>Torne o recurso de grupo de disponibilidade do SQL Server dependente do ponto de acesso para cliente.

    a. No Gerenciador de Cluster de Failover, clique em **Funções** e em seu grupo de disponibilidade.

    b. Na guia **Recursos** em **Outros Recursos**, com o botão direito do mouse no grupo de recursos de disponibilidade e clique em **Propriedades**. 

    c. Na guia dependências, adicione o nome do recurso de ponto de acesso ao cliente (o ouvinte).

   ![Recurso de IP](./media/virtual-machines-ag-listener-configure/97-propertiesdependencies.png) 

    d. Clique em **OK**.

5. <a name="listname"></a>Torne o recurso de ponto de acesso de cliente dependente do endereço IP.

    a. No Gerenciador de Cluster de Failover, clique em **Funções** e em seu grupo de disponibilidade. 

    b. Na guia **Recursos**, clique com o botão direito do mouse no recurso do ponto de acesso no **Nome do Servidor** e clique em **Propriedades**. 

   ![Recurso de IP](./media/virtual-machines-ag-listener-configure/98-dependencies.png) 

    c. Selecione a guia **Dependências** . Verifique se o endereço IP é uma dependência. Se não for, defina uma dependência no endereço IP. Se houver vários recursos listados, verifique se os endereços IP têm dependências OR, e não AND. Clique em **OK**. 

   ![Recurso de IP](./media/virtual-machines-ag-listener-configure/98-propertiesdependencies.png) 

    d. Clique com o botão direito do mouse no nome do ouvinte e, em seguida, clique em **Colocar online**. 

    >[!TIP]
    >Você pode validar se as dependências estão configuradas corretamente. No Gerenciador de Cluster de Failover, vá para funções, clique no grupo de disponibilidade, clique em **Mais Ações**e clique em **Mostrar Relatório de Dependências**. Quando as dependências são configuradas corretamente, o grupo de disponibilidade é dependente do nome da rede e o nome da rede depende do endereço IP. 


6. <a name="setparam"></a>Definir os parâmetros do cluster no PowerShell.
    
    a. Copie o script do PowerShell a seguir em uma de suas instâncias do SQL Server. Atualize as variáveis para o seu ambiente.     
    
    ```PowerShell
    $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
    $IPResourceName = "<IPResourceName>" # the IP Address resource name
    $ILBIP = “<n.n.n.n>” # the IP Address of the Internal Load Balancer (ILB). This is the static IP address for the load balancer you configured in the Azure portal.
    [int]$ProbePort = <nnnnn>
    
    Import-Module FailoverClusters
    
    Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
    ```

    b. Defina os parâmetros de cluster executando o script do PowerShell em um dos nós do cluster.  

    > [!NOTE]
    > Se suas instâncias do SQL Server estiverem em regiões separadas, você precisará executar o script do PowerShell duas vezes. Na primeira vez, use o `$ILBIP` e `$ProbePort` da primeira região. Na segunda vez, use o `$ILBIP` e `$ProbePort` da segunda região. O nome da rede de cluster e o nome do recurso de IP do cluster são os mesmos. 
