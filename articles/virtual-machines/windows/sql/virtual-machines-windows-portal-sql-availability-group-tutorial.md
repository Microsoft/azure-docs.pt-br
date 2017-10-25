---
title: "Grupos de Disponibilidade do SQL Server - máquinas virtuais do Azure - tutorial | Microsoft Docs"
description: "Este tutorial mostra como criar um grupo de disponibilidade Always On do SQL Server em Máquinas Virtuais do Azure."
services: virtual-machines
documentationCenter: na
authors: MikeRayMSFT
manager: jhubbard
editor: monicar
tags: azure-service-management
ms.assetid: 08a00342-fee2-4afe-8824-0db1ed4b8fca
ms.service: virtual-machines-sql
ms.devlang: na
ms.custom: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/09/2017
ms.author: mikeray
ms.openlocfilehash: 228ca9ca5fddc493d27bfd6a40df5ee7306d6aa9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="configure-always-on-availability-group-in-azure-vm-manually"></a>Configurar grupos de disponibilidade Sempre ativo na VM do Azure manualmente

Este tutorial mostra como criar um grupo de disponibilidade Always On do SQL Server em Máquinas Virtuais do Azure. O tutorial completo cria um Grupo de Disponibilidade com uma réplica do banco de dados em dois SQL Servers.

**Tempo estimado**: cerca de 30 minutos para ser concluído depois que os pré-requisitos forem atendidos.

O diagrama ilustra o que você cria no tutorial.

![Grupo de Disponibilidade](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/00-EndstateSampleNoELB.png)

## <a name="prerequisites"></a>Pré-requisitos

O tutorial supõe que você tem uma compreensão básica dos Grupos de Disponibilidade Always On do SQL Server. Se você precisa saber mais, confira [Visão geral dos Grupos de Disponibilidade Always On (SQL Server)](http://msdn.microsoft.com/library/ff877884.aspx).

A tabela a seguir lista os pré-requisitos que precisam ser concluídos antes de iniciar este tutorial:

|  |Requisito |Descrição |
|----- |----- |----- |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png) | Dois servidores SQL | -Em um conjunto de disponibilidade do Azure <br/> -Em um único domínio <br/> -Com o recurso Cluster de Failover instalado |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)| Windows Server | Compartilhamento de arquivos para a testemunha do cluster |  
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|Conta de serviço do SQL Server | Conta do domínio |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|Conta do serviço SQL Server Agent | Conta do domínio |  
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|Portas de firewall abertas | - SQL Server: **1433** para instância padrão <br/> - Ponto de extremidade de espelhamento de banco de dados: **5022** ou outra porta disponível <br/> - Investigação do Azure Load Balancer: **59999** ou outra porta disponível |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|Adicionar o recurso Cluster de Failover à VM | Os dois SQL Servers precisam desse recurso |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|Conta do domínio de instalação | - Administrador local em cada SQL Server <br/> - Membro da função de servidor fixa sysadmin do SQL Server para cada instância do SQL Server  |


Antes de iniciar o tutorial, você precisará [Concluir os pré-requisitos para a criação de Grupos de Disponibilidade AlwaysOn em Máquinas Virtuais do Azure](virtual-machines-windows-portal-sql-availability-group-prereq.md). Se esses pré-requisitos já foram concluídos,vá para [Criar Cluster](#CreateCluster).


<!--**Procedure**: *This is the first “step”. Make titles H2’s and short and clear – H2’s appear in the right pane on the web page and are important for navigation.*-->

<a name="CreateCluster"></a>
## Criar o cluster

Depois de concluir os pré-requisitos, a primeira etapa é criar um cluster de failover do Windows Server que inclui dois SQL Servers e um servidor testemunha.  

1. Faça RDP para o primeiro SQL Server usando uma conta de domínio que seja de administrador no SQL Server e no servidor testemunha.

   >[!TIP]
   >Se você seguiu o [documento de pré-requisitos](virtual-machines-windows-portal-sql-availability-group-prereq.md), criou uma conta chamada **CORP\Install**. Use essa conta.

2. No painel **Gerenciador do Servidor** selecione **Ferramentas**, e clique em **Gerenciador de Cluster de Failover**.
3. No painel esquerdo, clique com botão direito do mouse em **Gerenciador de Cluster de Failover** e clique em **Criar um Cluster**.
   ![Criar cluster](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/40-createcluster.png)
4. No Assistente para Criação de Cluster, crie um cluster de um nó percorrendo as páginas com as configurações na seguinte tabela:

   | Página | Configurações |
   | --- | --- |
   | Antes de começar |Usar padrões |
   | Selecionar Servidores |Digite o primeiro nome do SQL Server em **Digite o nome do servidor** e clique em **Adicionar**. |
   | Aviso de Validação |Selecione **Não. Eu não preciso de suporte da Microsoft para este cluster e, portanto, não desejo executar testes de validação. Continuar a criar o cluster quando eu clicar em Avançar**. |
   | Ponto de Acesso para Administrar o Cluster |Digite um nome de cluster, por exemplo, **SQLAGCluster1** em **Nome do Cluster**.|
   | Confirmação |Use os padrões, a menos que você esteja usando Espaços de Armazenamento. Consulte a observação após esta tabela. |

### <a name="set-the-cluster-ip-address"></a>Definir o endereço IP do cluster

1. Em **Gerenciador de Cluster de Failover**, role para baixo até **Recursos Principais de Cluster** e expanda os detalhes do cluster. Você verá os recursos de **Nome** e de **Endereço IP** no estado **Com Falha**. O recurso de endereço IP não pode ficar online porque o cluster recebeu o mesmo endereço IP que a própria máquina, ou seja, um endereço duplicado.

2. Clique com o botão direito do mouse no recurso **Endereço IP** com falha e clique em **Propriedades**.

   ![Propriedades do Cluster](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/42_IPProperties.png)

3. Selecione **Endereço IP estático** e especifique um endereço disponível da sub-rede onde o SQL Server está na caixa de texto Endereço. Em seguida, clique em **OK**.
4. Na seção **Recursos Principais do Cluster**, clique com o botão direito do mouse no nome do cluster e clique em **Colocar Online**. Em seguida, aguarde até que ambos os recursos estejam online. Quando o recurso de nome de cluster fica online, ele atualiza o servidor DC com uma nova conta de computador do AD. Use essa conta do AD para executar o serviço clusterizado do Grupo de Disponibilidade posteriormente.

### <a name="addNode"></a>Adicionar o outro SQL Server ao cluster

Adicione o outro SQL Server ao cluster.

1. Na árvore do navegador, clique com o botão direito do mouse no cluster e clique em **Adicionar Nó**.

    ![Adicionar Nó ao Cluster](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/44-addnode.png)

1. No **Assistente para Adicionar Nó**, clique em **Avançar**. Na página **Selecionar Servidores**, adicione o segundo SQL Server. Digite o nome do servidor em **Digite o nome do servidor** e clique em **Adicionar**. Quando terminar, clique em **Avançar**.

1. Na página **Aviso de Validação**, clique em **Não** (em um cenário de produção, você deve executar os testes de validação). Em seguida, clique em **Avançar**.

8. Na página **Confirmação**, se você estiver usando Espaços de Armazenamento, desmarque a caixa de seleção **Adicione todo o armazenamento qualificado ao cluster.**

   ![Adicionar Confirmação do Nó](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/46-addnodeconfirmation.png)

    >[!WARNING]
   >Se você estiver usando Espaços de Armazenamento e não desmarcar **Adicionar todo o armazenamento qualificado ao cluster**, o Windows desconectará os discos virtuais durante o processo de clustering. Como resultado, eles não aparecem no Gerenciador ou Explorador de Discos até que os espaços de armazenamento sejam removidos do cluster e reanexados usando o PowerShell. Espaços de Armazenamento agrupam vários discos em pools de armazenamento. Para obter mais informações, consulte [Espaços de Armazenamento](https://technet.microsoft.com/library/hh831739).

1. Clique em **Avançar**.

1. Clique em **Concluir**.

   O Gerenciador de Cluster de Failover mostra que o cluster tem um novo nó e o lista no contêiner **Nós**.

10. Faça logoff da sessão de área de trabalho remota.

### <a name="add-a-cluster-quorum-file-share"></a>Adicionar um compartilhamento de arquivos de quorum de cluster

Neste exemplo, o cluster do Windows usa um compartilhamento de arquivos para criar um quorum de cluster. Este tutorial usa um quorum Maioria de Compartilhamento de Arquivos e Nós. Para saber mais, confira [Noções básicas sobre configurações de quorum em um cluster de failover](http://technet.microsoft.com/library/cc731739.aspx).

1. Conecte-se ao servidor membro de testemunha de compartilhamento de arquivos com uma sessão de área de trabalho remota.

1. No **Gerenciador do Servidor**, clique em **Ferramentas**. Abra **Gerenciamento do Computador**.

1. Clique em **Pastas Compartilhadas**.

1. Clique com botão direito do mouse em **Compartilhamentos** e clique em **Novo Compartilhamento...** .

   ![Novo compartilhamento](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/48-newshare.png)

   Use **Criar um Assistente de Pasta Compartilhada** para criar um compartilhamento.

1. Em **Caminho da Pasta**, clique em **Procurar** e localize ou crie um caminho para a pasta compartilhada. Clique em **Avançar**.

1. Em **Nome, Descrição e Configurações**, verifique o nome de compartilhamento e o caminho. Clique em **Avançar**.

1. Em **Permissões de Pasta Compartilhada**, defina **Personalizar permissões**. Clique em **Personalizar...**.

1. Em **Personalizar Permissões**, clique em **Adicionar...** .

1. Verifique se a conta usada para criar o cluster tem controle total.

   ![Novo compartilhamento](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/50-filesharepermissions.png)

1. Clique em **OK**.

1. Em **Permissões de Pasta Compartilhada**, clique em **Concluir**. Clique em **Concluir** novamente.  

1. Logoff do servidor

### <a name="configure-cluster-quorum"></a>Configurar quorum do cluster

Em seguida, configure o quorum do cluster.

1. Conecte-se ao primeiro nó de cluster com a área de trabalho remota.

1. Em **Gerenciador de Cluster de Failover**, clique com o botão direito do mouse no cluster, aponte para **Mais Ações**e clique em **Configurar Quorum do Cluster...** .

   ![Novo compartilhamento](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/52-configurequorum.png)

1. No **Assistente para Configurar Quorum do Cluster**, clique em **Avançar**.

1. Em **Selecionar Opção de Configuração de Quorum**, escolha **Selecionar a testemunha de quorum**e clique em **Avançar**.

1. Em **Selecionar Testemunha de Quorum**, clique em **Configurar testemunha de compartilhamento de arquivos**.

   >[!TIP]
   >O Windows Server 2016 dá suporte a testemunha de nuvem. Se você escolher esse tipo de testemunha, não precisará de testemunha de compartilhamento de arquivos. Para saber mais, confira [Implantar uma testemunha de nuvem para um Cluster de Failover](http://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness). Este tutorial usa uma testemunha de compartilhamento de arquivos, o que tem suporte de sistemas operacionais anteriores.

1. Em **Configurar Testemunha de Compartilhamento de Arquivos**, insira o caminho para o compartilhamento criado. Clique em **Avançar**.

1. Verifique as configurações em **Confirmação**. Clique em **Avançar**.

1. Clique em **Concluir**.

Os recursos principais de cluster são configurados com uma testemunha de compartilhamento de arquivos.

## <a name="enable-availability-groups"></a>Habilitar Grupos de Disponibilidade

Em seguida, habilite o recurso **Grupos de Disponibilidade AlwaysOn**. Siga estas etapas em ambos os servidores SQL.

1. Na tela **Iniciar**, inicie o **SQL Server Configuration Manager**.
2. Na árvore do navegador, clique em **Serviços do SQL Server**, clique com o botão direito do mouse no serviço **SQL Server (MSSQLSERVER)** e clique em **Propriedades**.
3. Clique na guia **Alta Disponibilidade AlwaysOn** e selecione **Habilitar Grupos de Disponibilidade AlwaysOn**, da seguinte forma:

    ![Habilitar Grupos de Disponibilidade AlwaysOn](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/54-enableAlwaysOn.png)

4. Clique em **Aplicar**. Clique em **OK** no diálogo pop-up.

5. Reinicie o serviço SQL Server.

Repita essas etapas no outro SQL Server.

<!-----------------
## <a name="endpoint-firewall"></a>Open firewall for the database mirroring endpoint

Each instance of SQL Server that participates in an Availability Group requires a database mirroring endpoint. This endpoint is a TCP port for the instance of SQL Server that is used to synchronize the database replicas in the Availability Groups on that instance.

On both SQL Servers, open the firewall for the TCP port for the database mirroring endpoint.

1. On the first SQL Server **Start** screen, launch **Windows Firewall with Advanced Security**.
2. In the left pane, select **Inbound Rules**. On the right pane, click **New Rule**.
3. For **Rule Type**, choose **Port**.
1. For the port, specify TCP and choose an unused TCP port number. For example, type *5022* and click **Next**.

   >[!NOTE]
   >For this example, we're using TCP port 5022. You can use any available port.

5. In the **Action** page, keep **Allow the connection** selected and click **Next**.
6. In the **Profile** page, accept the default settings and click **Next**.
7. In the **Name** page, specify a rule name, such as **Default Instance Mirroring Endpoint** in the **Name** text box, then click **Finish**.

Repeat these steps on the second SQL Server.
-------------------------->

## <a name="create-a-database-on-the-first-sql-server"></a>Criar um banco de dados no primeiro SQL Server

1. Inicie o arquivo RDP para o primeiro SQL Server com uma conta de domínio que seja membro da função de servidor fixa sysadmin.
1. Abra o SQL Server Management Studio e conecte-se ao primeiro SQL Server.
7. No **Pesquisador de Objetos**, clique com o botão direito do mouse em **Bancos de Dados** e em **Novo Banco de Dados**.
8. Em **Nome do banco de dados**, digite **MyDB1** e clique em **OK**.

### <a name="backupshare"></a>Criar um compartilhamento de backup

1. No primeiro SQL Server em **Gerenciador do Servidor**, clique em **Ferramentas**. Abra **Gerenciamento do Computador**.

1. Clique em **Pastas Compartilhadas**.

1. Clique com botão direito do mouse em **Compartilhamentos** e clique em **Novo Compartilhamento...** .

   ![Novo compartilhamento](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/48-newshare.png)

   Use **Criar um Assistente de Pasta Compartilhada** para criar um compartilhamento.

1. Em **Caminho da Pasta**, clique em **Procurar** e localize ou crie um caminho para a pasta compartilhada de backup do banco de dados. Clique em **Avançar**.

1. Em **Nome, Descrição e Configurações**, verifique o nome de compartilhamento e o caminho. Clique em **Avançar**.

1. Em **Permissões de Pasta Compartilhada**, defina **Personalizar permissões**. Clique em **Personalizar...**.

1. Em **Personalizar Permissões**, clique em **Adicionar...** .

1. Verifique se as contas de serviço do SQL Server e do SQL Server Agent para ambos os servidores têm controle total.

   ![Novo compartilhamento](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/68-backupsharepermission.png)

1. Clique em **OK**.

1. Em **Permissões de Pasta Compartilhada**, clique em **Concluir**. Clique em **Concluir** novamente.  

### <a name="take-a-full-backup-of-the-database"></a>Fazer um backup completo do banco de dados

Você precisa fazer backup do novo banco de dados para inicializar a cadeia de logs. Se você não fizer um backup do novo banco de dados, ele não poderá ser incluído em um Grupo de Disponibilidade.

1. Em **Pesquisador de Objetos**, clique com o botão direito do mouse no banco de dados, aponte para **Tarefas...**  e clique em **Fazer Backup**.

1. Clique em **OK** para realizar um backup completo no local de backup padrão.

## <a name="create-the-availability-group"></a>Criar o Grupo de Disponibilidade
Agora você está pronto para configurar um Grupo de Disponibilidade usando as seguintes etapas:

* Crie um banco de dados no SQL Server primeiro.
* Faça um backup completo e um backup de log de transações do banco de dados
* Restaure os backups completo e de log no segundo SQL Server com a opção **NORECOVERY**
* Crie o Grupo de Disponibilidade (**AG1**) com confirmação síncrona, failover automático e réplicas secundárias legíveis

### <a name="create-the-availability-group"></a>Crie o Grupo de Disponibilidade:

1. Na sessão da área de trabalho remota para o primeiro SQL Server. Em **Pesquisador de Objetos** no SSMS, clique com o botão direito do mouse em **Alta Disponibilidade AlwaysOn** e clique em **Assistente de Novo Grupo de Disponibilidade**.

    ![Inicie o Assistente de Novo Grupo de Disponibilidade](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/56-newagwiz.png)

2. Na página **Introdução**, clique em **Avançar**. Na página **Especificar Nome do Grupo de Disponibilidade**, digite um nome para o Grupo de Disponibilidade, por exemplo, **AG1**, em **Nome do Grupo de Disponibilidade**. Clique em **Avançar**.

    ![Novo assistente de AG, especifique o nome do AG](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/58-newagname.png)

3. Na página **Selecionar Bancos de Dados**, selecione seu banco de dados e clique em **Avançar**.

   >[!NOTE]
   >Esse banco de dados atende aos pré-requisitos para um Grupo de Disponibilidade, pois você fez pelo menos um backup completo na réplica primária pretendida.

   ![Novo assistente AG, selecione os bancos de dados](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/60-newagselectdatabase.png)
4. Na página **Especificar Réplicas**, clique em **Adicionar Réplica**.

   ![Novo assistente AG, especifique as réplicas](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/62-newagaddreplica.png)
5. A caixa de diálogo **Conectar ao Servidor** é aberta. Digite o nome do segundo servidor em **Nome do servidor**. Clique em **Conectar**.

   De volta à página **Especificar Réplicas**, você verá o segundo servidor listado em **Réplicas de Disponibilidade**. Configure as réplicas como mostrado abaixo.

   ![Novo assistente de AG, Especificar Réplicas (Completo)](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/64-newagreplica.png)

6. Clique em **Pontos de extremidade** para ver o ponto de extremidade de espelhamento de banco de dados para esse Grupo de Disponibilidade. Use a mesma porta que você usou ao definir a [regra de firewall para pontos de extremidade de espelhamento de banco de dados](virtual-machines-windows-portal-sql-availability-group-prereq.md#endpoint-firewall).

    ![Novo assistente de AG, selecionar sincronização de dados inicial](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/66-endpoint.png)

8. Na página **Selecionar Sincronização de Dados Inicial** , selecione **Completo** e especifique um local de rede compartilhada. Para o local, use o [compartilhamento de backup criado](#backupshare). No exemplo, era **\\\\\<First SQL Server\>\Backup\**. Clique em **Avançar**.

   >[!NOTE]
   >A sincronização completa usa um backup completo do banco de dados na primeira instância do SQL Server e o restaura na segunda instância. Em caso de grandes bancos de dados, a sincronização completa não é recomendada porque pode levar muito tempo. Você pode reduzir esse tempo manualmente usando um backup do banco de dados e restaurando-o com `NO RECOVERY`. Se o banco de dados já foi restaurado com `NO RECOVERY` no segundo SQL Server antes da configuração do Grupo de Disponibilidade, escolha **Somente junção**. Se você quiser usar o backup depois de configurar o Grupo de Disponibilidade, escolha **Ignorar sincronização inicial de dados**.

    ![Novo assistente de AG, selecionar sincronização de dados inicial](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/70-datasynchronization.png)

9. Na página **Validação**, clique em **Avançar**. O arquivo deve ser semelhante à seguinte imagem:

    ![Novo assistente AG, Validação](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/72-validation.png)

    >[!NOTE]
    >Há um aviso para a configuração de ouvinte porque você não configurou um ouvinte de Grupo de Disponibilidade. Você pode ignorar esse aviso porque, nas máquinas virtuais do Azure, o ouvinte é criado depois do balanceador de carga do Azure.

10. Na página **Resumo**, clique em **Concluir** e aguarde enquanto o assistente configura o novo Grupo de Disponibilidade. Na página **Progresso**, você pode clicar em **Mais detalhes** para exibir o progresso detalhado. Quando o assistente for concluído, inspecione a página **Resultados** para verificar se o Grupo de Disponibilidade foi criado com êxito.

     ![Novo assistente de AG, Resultados](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/74-results.png)
11. Clique em **Fechar** para sair do assistente.

### <a name="check-the-availability-group"></a>Conferir o Grupo de Disponibilidade

1. Em **Pesquisador de Objetos**, expanda **Alta Disponibilidade AlwaysOn** e expanda **Grupos de Disponibilidade**. Agora você poderá ver o novo Grupo de Disponibilidade neste contêiner. Clique com botão direito do mouse no Grupo de Disponibilidade e clique em **Mostrar Painel**.

   ![Mostrar Painel de AG](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/76-showdashboard.png)

   O **Painel AlwaysOn** deve ser semelhante ao mostrado abaixo.

   ![Painel de AG](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/78-agdashboard.png)

   Você pode ver as réplicas, o modo de failover de cada réplica e o estado de sincronização.

2. No **Gerenciador de Cluster de Failover**, clique em seu cluster. Selecione **funções**. O nome do Grupo de Disponibilidade usado é uma função no cluster. Esse Grupo de Disponibilidade não tem um endereço IP para conexões de cliente porque você não configurou um ouvinte. Você configurará o ouvinte depois de criar um balanceador de carga do Azure.

   ![AG no Gerenciador de Cluster de Failover](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/80-clustermanager.png)

   > [!WARNING]
   > Não tente fazer failover do Grupo de Disponibilidade no Gerenciador de Cluster de Failover. Todas as operações de failover devem ser executadas no **Painel AlwaysOn** no SSMS. Para obter mais informações, consulte [Restrições do uso do Gerenciador de Cluster de Failover com Grupos de Disponibilidade](https://msdn.microsoft.com/library/ff929171.aspx).
    >

Agora você tem um Grupo de Disponibilidade com réplicas em duas instâncias do SQL Server. Você pode mover o Grupo de Disponibilidade entre instâncias. Você ainda não pode se conectar ao Grupo de Disponibilidade porque não tem um ouvinte. Nas máquinas virtuais do Azure, o ouvinte precisa de um balanceador de carga. A próxima etapa é criar o balanceador de carga no Azure.

<a name="configure-internal-load-balancer"></a>

## <a name="create-an-azure-load-balancer"></a>Criar um balanceador de carga do Azure

Nas máquinas virtuais do Azure, um Grupo de Disponibilidade do SQL Server precisa de um balanceador de carga. O balanceador de carga contém o endereço IP do ouvinte do Grupo de Disponibilidade. Esta seção resume como criar o balanceador de carga no portal do Azure.

1. No portal do Azure, vá para o grupo de recursos em que estão seus SQL Servers e clique em **+Adicionar**.
2. Pesquise pelo **Balanceador de Carga**. Escolha o balanceador de carga publicado pela Microsoft.

   ![AG no Gerenciador de Cluster de Failover](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/82-azureloadbalancer.png)

1.  Clique em **Criar**.
3. Configure os seguintes parâmetros para o balanceador de carga.

   | Configuração | Campo |
   | --- | --- |
   | **Nome** |Use um nome para o balanceador de carga, por exemplo, **sqlLB**. |
   | **Tipo** |Interna |
   | **Rede virtual** |Use o nome da rede virtual do Azure. |
   | **Sub-rede** |Use o nome da sub-rede em que a máquina virtual está.  |
   | **Atribuição de endereço IP** |Estático |
   | **Endereço IP** |Use um endereço disponível da sub-rede. |
   | **Assinatura** |Use a mesma assinatura da máquina virtual. |
   | **Localidade** |Use o mesmo local da máquina virtual. |

   A folha do portal do Azure deve ser semelhante a esta:

   ![Criar balanceador de carga](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/84-createloadbalancer.png)

1. Clique em **Criar** para criar o balanceador de carga.

Para configurar o balanceador de carga, você precisará criar um pool de back-end, um teste e definir regras de balanceamento de carga. Faça isso no portal do Azure.

### <a name="add-backend-pool"></a>Adicionar pool de back-end

1. No portal do Azure, vá para o grupo de disponibilidade. Talvez seja necessário atualizar a exibição para ver o balanceador de carga recém-criado.

   ![Encontrar o Balanceador de Carga no Grupo de Recursos](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/86-findloadbalancer.png)

1. Clique no balanceador de carga, clique em **Pools de back-end**e clique em **+Adicionar**. Configure o pool de back-end da seguinte maneira:

   | Configuração | Descrição | Exemplo
   | --- | --- |---
   | **Nome** | Digite um nome de texto | SQLLBBE
   | **Associado a** | Selecione uma opção na lista | Conjunto de disponibilidade
   | **Conjunto de disponibilidade** | Use um nome do conjunto de disponibilidade em que suas VMs do SQL Server estão | sqlAvailabilitySet |
   | **Máquinas virtuais** |Os dois nomes de VM do Azure SQL Server | sqlserver-0, sqlserver-1

1. Digite o nome do pool de back-end.

1. Clique em **+ Adicionar uma máquina virtual**.

1. Para o conjunto de disponibilidade, escolha o conjunto de disponibilidade em que os SQL Servers estão.

1. No caso das máquinas virtuais, inclua ambos os SQL Servers. Não inclua o servidor de testemunha de compartilhamento de arquivos.

1. Clique em **OK** para criar o pool de back-end.

### <a name="set-the-probe"></a>Definir a investigação

1. Clique no balanceador de carga, clique em **Investigação de integridade**e clique em **+Adicionar**.

1. Defina a investigação de integridade da seguinte maneira:

   | Configuração | Descrição | Exemplo
   | --- | --- |---
   | **Nome** | Texto | SQLAlwaysOnEndPointProbe |
   | **Protocolo** | Escolher TCP | TCP |
   | **Porta** | Qualquer porta não utilizada | 59999 |
   | **Intervalo**  | O tempo entre as tentativas de investigação, em segundos |5 |
   | **Limite não íntegro** | O número de falhas de investigação consecutivas que deve ocorrer para uma máquina virtual ser considerada não íntegra  | 2 |

1. Clique em **OK** para definir a investigação de integridade.

### <a name="set-the-load-balancing-rules"></a>Definir as regras de balanceamento de carga

1. Clique no balanceador de carga, clique em **Regras de balanceamento de carga**e clique em **+Adicionar**.

1. Defina as regras de balanceamento de carga da forma indicada a seguir.
   | Configuração | Descrição | Exemplo
   | --- | --- |---
   | **Nome** | Texto | SQLAlwaysOnEndPointListener |
   | **Endereço IP de front-end** | Escolher um endereço |Use o endereço que você criou ao criar o balanceador de carga. |
   | **Protocolo** | Escolher TCP |TCP |
   | **Porta** | Usar a porta para a instância do SQL Server | 1433 |
   | **Porta de back-end** | Este campo não é usado quando o IP flutuante é definido para o retorno de servidor direto | 1433 |
   | **Investigação** |O nome especificado para o teste | SQLAlwaysOnEndPointProbe |
   | **Persistência de sessão** | Lista suspensa | **Nenhum** |
   | **Tempo limite de ociosidade** | Minutos para manter uma conexão TCP aberta | 4 |
   | **IP flutuante (retorno de servidor direto)** | |Habilitado |

   > [!WARNING]
   > O retorno de servidor direto é definido durante a criação. Ele não pode ser alterado.

1. Clique em **OK** para definir as regras de balanceamento de carga.

## <a name="configure-listener"></a> Configurar o ouvinte

A próxima etapa é configurar um ouvinte do Grupo de Disponibilidade no cluster de failover.

> [!NOTE]
> Este tutorial mostra como criar um ouvinte usando um endereço IP de ILB. Para criar um ou mais ouvintes usando um ou mais endereços IP, veja [Criar ouvinte e balanceador de carga do Grupo de Disponibilidade | Azure](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
>
>

[!INCLUDE [ag-listener-configure](../../../../includes/virtual-machines-ag-listener-configure.md)]

## <a name="set-listener-port"></a>Definir porta do ouvinte

No SQL Server Management Studio, defina a porta do ouvinte.

1. Inicie o SQL Server Management Studio e conecte-se à réplica principal.

1. Navegue até **Alta Disponibilidade do AlwaysOn** | **Grupos de Disponibilidade** | **Ouvintes do Grupo de Disponibilidade**.

1. Agora você deve ver o nome do ouvinte que você criou no Gerenciador de Cluster de Failover. Clique com o botão direito do mouse no nome do ouvinte e clique em **Propriedades**.

1. Na caixa **Porta**, especifique o número da porta para o ouvinte do Grupo de Disponibilidade usando o $EndpointPort usado anteriormente (1433 era o padrão) e depois clique em **OK**.

Agora você tem um Grupo de Disponibilidade do SQL Server em máquinas virtuais do Azure, em execução no modo de Resource Manager.

## <a name="test-connection-to-listener"></a>Testar conexão com o ouvinte

Para testar a conexão:

1. RDP para um SQL Server que está na mesma rede virtual, mas não tem a réplica. Você pode usar o outro SQL Server no cluster.

1. Use o utilitário **sqlcmd** para testar a conexão. Por exemplo, o script a seguir estabelece uma conexão de **sqlcmd** com a réplica primária por meio do ouvinte com autenticação do Windows:

    ```
    sqlcmd -S <listenerName> -E
    ```

    Se o ouvinte estiver usando uma porta diferente da porta padrão (1433), especifique a porta na cadeia de conexão. Por exemplo, o comando sqlcmd a seguir conecta-se a um ouvinte na porta 1435:

    ```
    sqlcmd -S <listenerName>,1435 -E
    ```

A conexão SQLCMD se conecta automaticamente a qualquer instância do SQL Server que hospede a réplica primária.

> [!TIP]
> Verifique se a porta especificada está aberta no firewall dos servidores SQL. Ambos os servidores exigem uma regra de entrada para a porta TCP que você usa. Para saber mais, confira [Adicionar ou Editar Regra de Firewall](http://technet.microsoft.com/library/cc753558.aspx).
>
>



<!--**Notes**: *Notes provide just-in-time info: A Note is “by the way” info, an Important is info users need to complete a task, Tip is for shortcuts. Don’t overdo*.-->


<!--**Procedures**: *This is the second “step." They often include substeps. Again, use a short title that tells users what they’ll do*. *("Configure a new web project.")*-->

<!--**UI**: *Note the format for documenting the UI: bold for UI elements and arrow keys for sequence. (Ex. Click **File > New > Project**.)*-->

<!--**Screenshot**: *Screenshots really help users. But don’t include too many since they’re difficult to maintain. Highlight areas you are referring to in red.*-->

<!--**No. of steps**: *Make sure the number of steps within a procedure is 10 or fewer. Seven steps is ideal. Break up long procedure logically.*-->


<!--**Next steps**: *Reiterate what users have done, and give them interesting and useful next steps so they want to go on.*-->

## <a name="next-steps"></a>Próximas etapas

- [Adicionar um endereço IP a um balanceador de carga para um segundo grupo de disponibilidade](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md#Add-IP).
