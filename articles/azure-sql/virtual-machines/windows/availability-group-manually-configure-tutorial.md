---
title: 'Tutorial: Configurar um grupo de disponibilidade Always On do SQL Server'
description: Este tutorial mostra como criar um grupo de disponibilidade Always On do SQL Server em Máquinas Virtuais do Azure.
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.assetid: 08a00342-fee2-4afe-8824-0db1ed4b8fca
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/30/2018
ms.author: mathoma
ms.custom: seo-lt-2019
ms.openlocfilehash: feab48f32396bcc89621433930c9a9f4689d8286
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97355436"
---
# <a name="tutorial-manually-configure-an-availability-group-sql-server-on-azure-vms"></a>Tutorial: Configurar um grupo de disponibilidade manualmente (SQL Server em VMs do Azure)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Este tutorial mostra como criar um grupo de disponibilidade Always On para SQL Server em VMs (Máquinas Virtuais) do Azure. O tutorial completo cria um grupo de disponibilidade com uma réplica de banco de dados em dois SQL Servers.

Embora este artigo configure manualmente o ambiente do grupo de disponibilidade, também é possível fazer isso usando o [portal do Azure](availability-group-azure-portal-configure.md), o [PowerShell ou a CLI do Azure](availability-group-az-commandline-configure.md) ou os [modelos de Início Rápido do Azure](availability-group-quickstart-template-configure.md). 


**Tempo estimado**: Isso levará cerca de 30 minutos para ser concluído, depois que os [pré-requisitos](availability-group-manually-configure-prerequisites-tutorial.md) forem atendidos.


## <a name="prerequisites"></a>Pré-requisitos

O tutorial supõe que você tem uma compreensão básica dos grupos de disponibilidade Always On do SQL Server. Se você precisar de mais informações, confira [Visão geral dos grupos de disponibilidade Always On (SQL Server)](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server).

Antes de iniciar o tutorial, você precisará [Concluir os pré-requisitos para a criação de grupos de disponibilidade Always On em Máquinas Virtuais do Azure](availability-group-manually-configure-prerequisites-tutorial.md). Se esses pré-requisitos já foram concluídos,vá para [Criar Cluster](#CreateCluster).

A tabela a seguir lista os pré-requisitos que precisam ser concluídos antes de iniciar este tutorial:

| Requisito |Descrição |
|----- |----- |----- |
|:::image type="icon" source="./media/availability-group-manually-configure-tutorial/square.png" border="false":::   **Duas instâncias do SQL Server** | -Em um conjunto de disponibilidade do Azure <br/> -Em um único domínio <br/> -Com o recurso Cluster de Failover instalado |
|:::image type="icon" source="./media/availability-group-manually-configure-tutorial/square.png" border="false":::   **Windows Server** | Compartilhamento de arquivos para a testemunha do cluster |  
|:::image type="icon" source="./media/availability-group-manually-configure-tutorial/square.png" border="false":::   **Conta de serviço do SQL Server** | Conta do domínio |
|:::image type="icon" source="./media/availability-group-manually-configure-tutorial/square.png" border="false":::   **Conta do serviço SQL Server Agent** | Conta do domínio |  
|:::image type="icon" source="./media/availability-group-manually-configure-tutorial/square.png" border="false":::   **Portas de firewall abertas** | - SQL Server: **1433** para instância padrão <br/> - Ponto de extremidade de espelhamento de banco de dados: **5022** ou qualquer porta disponível <br/> - Investigação de integridade de endereço IP do balanceador de carga do grupo de disponibilidade: **59999** ou qualquer porta disponível <br/> - Investigação de integridade de endereço IP do balanceador de carga do núcleo do cluster: **58888** ou qualquer porta disponível |
|:::image type="icon" source="./media/availability-group-manually-configure-tutorial/square.png" border="false":::   **Adicionar o recurso Cluster de Failover à VM** | Ambas as instâncias SQL Server exigem esse recurso |
|:::image type="icon" source="./media/availability-group-manually-configure-tutorial/square.png" border="false":::   **Conta do domínio de instalação** | - Administrador local em cada SQL Server <br/> - Membro da função de servidor fixa sysadmin do SQL Server para cada instância do SQL Server  |

>[!NOTE]
> Muitas das etapas fornecidas neste tutorial agora podem ser automatizadas com o [portal do Azure](availability-group-azure-portal-configure.md) e o [PowerShell e a CLI do Az](./availability-group-az-commandline-configure.md) e os [Modelos de Início Rápido do Azure](availability-group-quickstart-template-configure.md).


<!--**Procedure**: *This is the first "step". Make titles H2's and short and clear – H2's appear in the right pane on the web page and are important for navigation.*-->

<a name="CreateCluster"></a>

## <a name="create-the-cluster"></a>Criar o cluster

Depois de concluir os pré-requisitos, a primeira etapa é criar um cluster de failover do Windows Server que inclui dois SQL Servers e um servidor testemunha.

1. Use o Protocolo RDP para se conectar ao primeiro SQL Server. Use uma conta de domínio que seja de administrador no SQL Server e no servidor testemunha.

   >[!TIP]
   >Se você seguiu o [documento de pré-requisitos](availability-group-manually-configure-prerequisites-tutorial.md), criou uma conta chamada **CORP\Install**. Use essa conta.

2. No painel **Gerenciador do Servidor** selecione **Ferramentas** e, em seguida, selecione **Gerenciador de Cluster de Failover**.
3. No painel esquerdo, clique com botão direito do mouse em **Gerenciador de Cluster de Failover** e selecione **Criar um Cluster**.

   ![Criar Cluster](./media/availability-group-manually-configure-tutorial/40-createcluster.png)

4. No Assistente para Criação de Cluster, crie um cluster de um nó percorrendo as páginas com as configurações na seguinte tabela:

   | Página | Configurações |
   | --- | --- |
   | Antes de começar |Usar padrões |
   | Selecionar Servidores |Digite o primeiro nome do SQL Server em **Digite o nome do servidor** e selecione **Adicionar**. |
   | Aviso de Validação |Selecione **Não. Eu não preciso de suporte da Microsoft para este cluster e, portanto, não desejo executar testes de validação. Continuar a criar o cluster quando eu selecionar Avançar**. |
   | Ponto de Acesso para Administrar o Cluster |Digite um nome de cluster, por exemplo, **SQLAGCluster1** em **Nome do Cluster**.|
   | Confirmação |Use os padrões, a menos que você esteja usando Espaços de Armazenamento. Consulte a observação após esta tabela. |

### <a name="set-the-windows-server-failover-cluster-ip-address"></a>Definir o endereço IP de cluster de failover do Windows Server

  > [!NOTE]
  > No Windows Server 2019, o cluster cria um **Nome do Servidor Distribuído** em vez do **Nome de Rede de Cluster**. Se estiver usando o Windows Server 2019, ignore todas as etapas que se referem ao nome do cluster principal neste tutorial. Crie um nome da rede de cluster usando o [PowerShell](failover-cluster-instance-storage-spaces-direct-manually-configure.md#create-failover-cluster). Examine o blog [Cluster de failover: objeto de rede de cluster](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97) para obter mais informações. 

1. Em **Gerenciador de Cluster de Failover**, role para baixo até **Recursos Principais de Cluster** e expanda os detalhes do cluster. Você verá os recursos de **Nome** e de **Endereço IP** no estado **Com Falha**. O recurso de endereço IP não pode ficar online porque o cluster recebeu o mesmo endereço IP que a própria máquina, ou seja, um endereço duplicado.

2. Clique com o botão direito do mouse no recurso **Endereço IP** com falha e selecione **Propriedades**.

   ![Propriedades do Cluster](./media/availability-group-manually-configure-tutorial/42_IPProperties.png)

3. Selecione **Endereço IP Estático** e especifique um endereço disponível na mesma sub-rede das máquinas virtuais.

4. Na seção **Recursos Principais do Cluster**, clique com o botão direito do mouse no nome do cluster e selecione **Colocar Online**. Aguarde até que ambos os recursos estejam online. Quando o recurso de nome do cluster fica online, ele atualiza o servidor do DC (controlador de domínio) com uma nova conta de computador do AD (Active Directory). Use essa conta do AD para executar o serviço clusterizado do grupo de disponibilidade posteriormente.

### <a name="add-the-other-sql-server-to-cluster"></a><a name="addNode"></a>Adicionar o outro SQL Server ao cluster

Adicione o outro SQL Server ao cluster.

1. Na árvore do navegador, clique com o botão direito do mouse no cluster e selecione **Adicionar Nó**.

    ![Adicionar Nó ao Cluster](./media/availability-group-manually-configure-tutorial/44-addnode.png)

1. No **Assistente para Adicionar Nó**, selecione **Avançar**. Na página **Selecionar Servidores**, adicione o segundo SQL Server. Digite o nome do servidor em **Digite o nome do servidor** e selecione **Adicionar**. Quando terminar, selecione **Avançar**.

1. Na página **Aviso de Validação**, selecione **Não** (em um cenário de produção, você deve executar os testes de validação). Em seguida, selecione **Avançar**.

8. Na página **Confirmação**, se você estiver usando Espaços de Armazenamento, desmarque a caixa de seleção **Adicione todo o armazenamento qualificado ao cluster.**

   ![Adicionar Confirmação do Nó](./media/availability-group-manually-configure-tutorial/46-addnodeconfirmation.png)

   >[!WARNING]
   >Se você estiver usando Espaços de Armazenamento e não desmarcar **Adicionar todo o armazenamento qualificado ao cluster**, o Windows desconectará os discos virtuais durante o processo de clustering. Como resultado, eles não aparecem no Explorador ou Gerenciador de Discos até que os espaços de armazenamento sejam removidos do cluster e reanexados usando o PowerShell. Espaços de Armazenamento agrupam vários discos em pools de armazenamento. Para obter mais informações, consulte [Espaços de Armazenamento](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831739(v=ws.11)).
   >

1. Selecione **Avançar**.

1. Selecione **Concluir**.

   O Gerenciador de Cluster de Failover mostra que o cluster tem um novo nó e o lista no contêiner **Nós**.

10. Faça logoff da sessão de área de trabalho remota.

### <a name="add-a-cluster-quorum-file-share"></a>Adicionar um compartilhamento de arquivos de quorum de cluster

Neste exemplo, o cluster do Windows usa um compartilhamento de arquivo para criar um quorum de cluster. Este tutorial usa um quorum Maioria de Compartilhamento de Arquivos e Nós. Para saber mais, confira [Noções básicas sobre configurações de quorum em um cluster de failover](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731739(v=ws.11)).

1. Conecte-se ao servidor membro de testemunha de compartilhamento de arquivos com uma sessão de área de trabalho remota.

1. Em **Gerenciador do Servidor**, selecione **Ferramentas**. Abra **Gerenciamento de Computador**.

1. Selecione **Pastas Compartilhadas**.

1. Clique com botão direito do mouse em **Compartilhamentos** e selecione **Novo Compartilhamento...** .

   ![Clique com o botão direito do mouse em compartilhamentos e selecione novo compartilhamento](./media/availability-group-manually-configure-tutorial/48-newshare.png)

   Use **Criar um Assistente de Pasta Compartilhada** para criar um compartilhamento.

1. Em **Caminho da Pasta**, selecione **Procurar** e localize ou crie um caminho para a pasta compartilhada. Selecione **Avançar**.

1. Em **Nome, Descrição e Configurações**, verifique o nome de compartilhamento e o caminho. Selecione **Avançar**.

1. Em **Permissões de Pasta Compartilhada**, defina **Personalizar permissões**. Selecione **Personalizado...** .

1. Em **Personalizar Permissões**, selecione **Adicionar...** .

1. Verifique se a conta usada para criar o cluster tem controle total.

   ![Verifique se a conta usada para criar o cluster tem controle total](./media/availability-group-manually-configure-tutorial/50-filesharepermissions.png)

1. Selecione **OK**.

1. Em **Permissões de Pasta Compartilhada**, selecione **Concluir**. Selecione **Concluir** novamente.  

1. Logoff do servidor

### <a name="configure-the-cluster-quorum"></a>Configurar o quorum do cluster

Em seguida, configure o quorum do cluster.

1. Conecte-se ao primeiro nó de cluster com a área de trabalho remota.

1. Em **Gerenciador de Cluster de Failover**, clique com o botão direito do mouse no cluster, aponte para **Mais Ações** e selecione **Configurar Quorum do Cluster...** .

   ![Selecione definir configurações de quorum do cluster](./media/availability-group-manually-configure-tutorial/52-configurequorum.png)

1. No **Assistente para Configurar Quorum do Cluster**, selecione **Avançar**.

1. Em **Selecionar Opção de Configuração de Quorum**, escolha **Selecionar a testemunha de quorum** e selecione **Avançar**.

1. Em **Selecionar Testemunha de Quorum**, selecione **Configurar testemunha de compartilhamento de arquivos**.

   >[!TIP]
   >O Windows Server 2016 dá suporte a testemunha de nuvem. Se você escolher esse tipo de testemunha, não precisará de testemunha de compartilhamento de arquivos. Para saber mais, confira [Implantar uma testemunha de nuvem para um Cluster de Failover](/windows-server/failover-clustering/deploy-cloud-witness). Este tutorial usa uma testemunha de compartilhamento de arquivos, o que tem suporte de sistemas operacionais anteriores.
   >

1. Em **Configurar Testemunha de Compartilhamento de Arquivos**, insira o caminho para o compartilhamento criado. Selecione **Avançar**.

1. Verifique as configurações em **Confirmação**. Selecione **Avançar**.

1. Selecione **Concluir**.

Os recursos principais de cluster são configurados com uma testemunha de compartilhamento de arquivos.

## <a name="enable-availability-groups"></a>Habilitar os grupos de disponibilidade

Em seguida, habilite o recurso **Grupos de disponibilidade AlwaysOn**. Siga estas etapas em ambos os servidores SQL.

1. Na tela **Iniciar**, inicie o **SQL Server Configuration Manager**.
2. Na árvore do navegador, selecione **Serviços do SQL Server**, clique com o botão direito do mouse no serviço **SQL Server (MSSQLSERVER)** e selecione **Propriedades**.
3. Selecione a guia **Alta Disponibilidade AlwaysOn** e clique em **Habilitar grupos de disponibilidade AlwaysOn**, do seguinte modo:

    ![Habilitar grupos de disponibilidade AlwaysOn](./media/availability-group-manually-configure-tutorial/54-enableAlwaysOn.png)

4. Escolha **Aplicar**. Selecione **OK** na caixa de diálogo pop-up.

5. Reinicie o serviço SQL Server.

Repita essas etapas no outro SQL Server.

<!-----------------
## <a name="endpoint-firewall"></a>Open firewall for the database mirroring endpoint

Each instance of SQL Server that participates in an availability group requires a database mirroring endpoint. This endpoint is a TCP port for the instance of SQL Server that is used to synchronize the database replicas in the availability groups on that instance.

On both SQL Servers, open the firewall for the TCP port for the database mirroring endpoint.

1. On the first SQL Server **Start** screen, launch **Windows Firewall with Advanced Security**.
2. In the left pane, select **Inbound Rules**. On the right pane, select **New Rule**.
3. For **Rule Type**, choose **Port**.
1. For the port, specify TCP and choose an unused TCP port number. For example, type *5022* and select **Next**.

   >[!NOTE]
   >For this example, we're using TCP port 5022. You can use any available port.

5. In the **Action** page, keep **Allow the connection** selected and select **Next**.
6. In the **Profile** page, accept the default settings and select **Next**.
7. In the **Name** page, specify a rule name, such as **Default Instance Mirroring Endpoint** in the **Name** text box, then select **Finish**.

Repeat these steps on the second SQL Server.
-------------------------->

## <a name="create-a-database-on-the-first-sql-server"></a>Criar um banco de dados no primeiro SQL Server

1. Inicie o arquivo RDP para o primeiro SQL Server com uma conta de domínio que seja membro da função de servidor fixa sysadmin.
1. Abra o SQL Server Management Studio e conecte-se ao primeiro SQL Server.
7. No **Pesquisador de Objetos**, clique com o botão direito do mouse em **Bancos de Dados** e selecione **Novo Banco de Dados**.
8. Em **Nome do banco de dados**, digite **MyDB1** e selecione **OK**.

### <a name="create-a-backup-share"></a><a name="backupshare"></a>Criar um compartilhamento de backup

1. No primeiro SQL Server em **Gerenciador do Servidor**, selecione **Ferramentas**. Abra **Gerenciamento de Computador**.

1. Selecione **Pastas Compartilhadas**.

1. Clique com botão direito do mouse em **Compartilhamentos** e selecione **Novo Compartilhamento...** .

   ![Selecione Novo Compartilhamento](./media/availability-group-manually-configure-tutorial/48-newshare.png)

   Use **Criar um Assistente de Pasta Compartilhada** para criar um compartilhamento.

1. Em **Caminho da Pasta**, selecione **Procurar** e localize ou crie um caminho para a pasta compartilhada de backup do banco de dados. Selecione **Avançar**.

1. Em **Nome, Descrição e Configurações**, verifique o nome de compartilhamento e o caminho. Selecione **Avançar**.

1. Em **Permissões de Pasta Compartilhada**, defina **Personalizar permissões**. Selecione **Personalizado...** .

1. Em **Personalizar Permissões**, selecione **Adicionar...** .

1. Verifique se as contas de serviço do SQL Server e do SQL Server Agent para ambos os servidores têm controle total.

   ![Verifique se as contas de serviço do SQL Server e do SQL Server Agent para ambos os servidores têm controle total.](./media/availability-group-manually-configure-tutorial/68-backupsharepermission.png)

1. Selecione **OK**.

1. Em **Permissões de Pasta Compartilhada**, selecione **Concluir**. Selecione **Concluir** novamente.  

### <a name="take-a-full-backup-of-the-database"></a>Fazer um backup completo do banco de dados

Você precisa fazer backup do novo banco de dados para inicializar a cadeia de logs. Se você não fizer um backup do novo banco de dados, ele não poderá ser incluído em um grupo de disponibilidade.

1. Em **Pesquisador de Objetos**, clique com o botão direito do mouse no banco de dados, aponte para **Tarefas...** e selecione **Fazer Backup**.

1. Selecione **OK** para realizar um backup completo no local de backup padrão.

## <a name="create-the-availability-group"></a>Crie o grupo de disponibilidade

Agora você está pronto para configurar um grupo de disponibilidade usando as seguintes etapas:

* Crie um banco de dados no SQL Server primeiro.
* Faça um backup completo e um backup de log de transações do banco de dados.
* Restaure os backups completo e de log no segundo SQL Server com a opção **NORECOVERY**.
* Crie o grupo de disponibilidade (**AG1**) com confirmação síncrona, failover automático e réplicas secundárias legíveis.

### <a name="create-the-availability-group"></a>Crie o Grupo de Disponibilidade:

1. Na sessão da área de trabalho remota para o primeiro SQL Server. Em **Pesquisador de Objetos** no SSMS, clique com o botão direito do mouse em **Alta Disponibilidade AlwaysOn** e selecione **Assistente de novo grupo de disponibilidade**.

    ![Iniciar o Assistente de novo grupo de disponibilidade](./media/availability-group-manually-configure-tutorial/56-newagwiz.png)

2. Na página **Introdução**, selecione **Avançar**. Na página **Especificar nome do grupo de disponibilidade**, digite um nome para o grupo de disponibilidade em **Nome do grupo de disponibilidade**. Por exemplo, **AG1**. Selecione **Avançar**.

    ![Assistente de novo grupo de disponibilidade, Especificar o Nome do grupo de disponibilidade](./media/availability-group-manually-configure-tutorial/58-newagname.png)

3. Na página **Selecionar Bancos de Dados**, selecione seu banco de dados e, em seguida, **Avançar**.

   >[!NOTE]
   >Esse banco de dados atende aos pré-requisitos para um grupo de disponibilidade, pois você fez pelo menos um backup completo na réplica primária pretendida.
   >

   ![Assistente de novo grupo de disponibilidade, Selecionar Bancos de Dados](./media/availability-group-manually-configure-tutorial/60-newagselectdatabase.png)

4. Na página **Especificar Réplicas**, selecione **Adicionar Réplica**.

   ![Assistente de novo grupo de disponibilidade, Especificar Réplicas](./media/availability-group-manually-configure-tutorial/62-newagaddreplica.png)

5. A caixa de diálogo **Conectar ao Servidor** é aberta. Digite o nome do segundo servidor em **Nome do servidor**. Selecione **Conectar**.

   De volta à página **Especificar Réplicas**, você verá o segundo servidor listado em **Réplicas de Disponibilidade**. Configure as réplicas como mostrado abaixo.

   ![Assistente de novo grupo de disponibilidade, Especificar Réplicas (Concluído)](./media/availability-group-manually-configure-tutorial/64-newagreplica.png)

6. Selecione **Pontos de extremidade** para ver o ponto de extremidade de espelhamento de banco de dados desse grupo de disponibilidade. Use a mesma porta que você usou ao definir a [regra de firewall para pontos de extremidade de espelhamento de banco de dados](availability-group-manually-configure-prerequisites-tutorial.md#endpoint-firewall).

    ![Assistente de novo grupo de disponibilidade, Selecionar Sincronização Inicial de Dados](./media/availability-group-manually-configure-tutorial/66-endpoint.png)

8. Na página **Selecionar Sincronização de Dados Inicial** , selecione **Completo** e especifique um local de rede compartilhada. Para o local, use o [compartilhamento de backup criado](#backupshare). No exemplo, era **\\\\<Primeiro SQL Server\>\Backup\\** . Selecione **Avançar**.

   >[!NOTE]
   >A sincronização completa usa um backup completo do banco de dados na primeira instância do SQL Server e o restaura na segunda instância. Em caso de grandes bancos de dados, a sincronização completa não é recomendada porque pode levar muito tempo. Você pode reduzir esse tempo manualmente usando um backup do banco de dados e restaurando-o com `NO RECOVERY`. Se o banco de dados já foi restaurado com `NO RECOVERY` no segundo SQL Server antes da configuração do grupo de disponibilidade, escolha **Somente junção**. Se você quiser usar o backup depois de configurar o grupo de disponibilidade, escolha **Ignorar sincronização inicial de dados**.
   >

   ![Escolha Ignorar sincronização de dados inicial](./media/availability-group-manually-configure-tutorial/70-datasynchronization.png)

9. Na página **Validação**, selecione **Avançar**. O arquivo deve ser semelhante à seguinte imagem:

    ![Assistente de novo grupo de disponibilidade, Validação](./media/availability-group-manually-configure-tutorial/72-validation.png)

    >[!NOTE]
    >Há um aviso para a configuração de ouvinte porque você não configurou um ouvinte de grupo de disponibilidade. Você pode ignorar esse aviso porque, nas máquinas virtuais do Azure, o ouvinte é criado depois do balanceador de carga do Azure.

10. Na página **Resumo**, selecione **Concluir** e aguarde enquanto o assistente configura o novo grupo de disponibilidade. Na página **Progresso**, você pode selecionar **Mais detalhes** para exibir o progresso detalhado. Quando o assistente concluir, inspecione a página **Resultados** para verificar se o grupo de disponibilidade foi criado com êxito.

     ![Assistente de novo grupo de disponibilidade, Resultados](./media/availability-group-manually-configure-tutorial/74-results.png)

11. Selecione **Fechar** para sair do assistente.

### <a name="check-the-availability-group"></a>Conferir o grupo de disponibilidade

1. No **Pesquisador de Objetos**, expanda **Alta Disponibilidade AlwaysOn** e, em seguida, expanda os **grupos de disponibilidade**. Agora você poderá ver o novo grupo de disponibilidade neste contêiner. Clique com o botão direito do mouse no grupo de disponibilidade e selecione **Mostrar Painel**.

   ![Mostrar Painel do grupo de disponibilidade](./media/availability-group-manually-configure-tutorial/76-showdashboard.png)

   O **Painel do AlwaysOn** deve ser semelhante à seguinte captura de tela:

   ![Painel do grupo de disponibilidade](./media/availability-group-manually-configure-tutorial/78-agdashboard.png)

   Você pode ver as réplicas, o modo de failover de cada réplica e o estado de sincronização.

2. No **Gerenciador de Cluster de Failover**, selecione seu cluster. Selecione **funções**. O nome do grupo de disponibilidade usado é uma função no cluster. Esse grupo de disponibilidade não tem um endereço IP para conexões de cliente, pois você não configurou um ouvinte. Você configurará o ouvinte depois de criar um balanceador de carga do Azure.

   ![Grupo de disponibilidade no Gerenciador de Cluster de Failover](./media/availability-group-manually-configure-tutorial/80-clustermanager.png)

   > [!WARNING]
   > Não tente fazer failover do grupo de disponibilidade no Gerenciador de Cluster de Failover. Todas as operações de failover devem ser executadas no **Painel AlwaysOn** no SSMS. Para obter mais informações, confira [Restrições do uso do Gerenciador de Cluster de Failover com grupos de disponibilidade](/sql/database-engine/availability-groups/windows/failover-clustering-and-always-on-availability-groups-sql-server).
    >

Agora você tem um grupo de disponibilidade com réplicas em duas instâncias do SQL Server. Você pode mover o grupo de disponibilidade entre instâncias. Você ainda não pode se conectar ao grupo de disponibilidade porque não tem um ouvinte. Nas máquinas virtuais do Azure, o ouvinte precisa de um balanceador de carga. A próxima etapa é criar o balanceador de carga no Azure.

<a name="configure-internal-load-balancer"></a>

## <a name="create-an-azure-load-balancer"></a>Criar um balanceador de carga do Azure

[!INCLUDE [sql-ag-use-dnn-listener](../../includes/sql-ag-use-dnn-listener.md)]

Nas máquinas virtuais do Azure, um grupo de disponibilidade do SQL Server precisa de um balanceador de carga. O balanceador de carga contém os endereços IP dos ouvintes do grupo de disponibilidade e o Cluster de Failover do Windows Server. Esta seção resume como criar o balanceador de carga no portal do Azure.

Um balanceador de carga no Azure pode ser um Standard Load Balancer ou um Balanceador de Carga Básico. O Standard Load Balancer possui mais recursos que o Balanceador de Carga Básico. Para um grupo de disponibilidade, o Standard Load Balancer é necessário se você usar uma Zona de Disponibilidade (em vez de um Conjunto de Disponibilidade). Para obter detalhes sobre a diferença entre os SKUs do balanceador de carga, confira [Comparação de SKUs do Load Balancer](../../../load-balancer/skus.md).

1. No portal do Azure, vá para o grupo de recursos em que estão seus SQL Servers e selecione **+Adicionar**.
1. Pesquise pelo **Balanceador de Carga**. Escolha o balanceador de carga publicado pela Microsoft.

   ![Escolha o balanceador de carga publicado pela Microsoft](./media/availability-group-manually-configure-tutorial/82-azureloadbalancer.png)

1. Selecione **Criar**.
1. Configure os seguintes parâmetros para o balanceador de carga.

   | Configuração | Campo |
   | --- | --- |
   | **Nome** |Use um nome para o balanceador de carga, por exemplo, **sqlLB**. |
   | **Tipo** |Interna |
   | **Rede virtual** |Use o nome da rede virtual do Azure. |
   | **Sub-rede** |Use o nome da sub-rede em que a máquina virtual está.  |
   | **Atribuição de endereço IP** |Estático |
   | **Endereço IP** |Use um endereço disponível da sub-rede. Use este endereço para o ouvinte do grupo de disponibilidade. Observe que isso é diferente do endereço IP de cluster.  |
   | **Assinatura** |Use a mesma assinatura da máquina virtual. |
   | **Localidade** |Use o mesmo local da máquina virtual. |

   A folha do portal do Azure deve ser semelhante a esta:

   ![Criar balanceador de carga](./media/availability-group-manually-configure-tutorial/84-createloadbalancer.png)

1. Selecione **Criar** para criar o balanceador de carga.

Para configurar o balanceador de carga, você precisará criar um pool de back-end, um teste e definir regras de balanceamento de carga. Faça isso no portal do Azure.

### <a name="add-a-backend-pool-for-the-availability-group-listener"></a>Adicionar um pool de back-end ao ouvinte do grupo de disponibilidade

1. No portal do Azure, vá para o grupo de disponibilidade. Talvez seja necessário atualizar a exibição para ver o balanceador de carga recém-criado.

   ![Encontrar o Balanceador de Carga no Grupo de Recursos](./media/availability-group-manually-configure-tutorial/86-findloadbalancer.png)

1. Selecione o balanceador de carga, em seguida, **Pools de back-end** e, por fim, **+Adicionar**.

1. Digite um nome para o pool de back-end.

1. Associe o pool de back-end com o conjunto de disponibilidade que contém as VMs.

1. Em **Configurações de IP de rede de destino**, marque **MÁQUINA VIRTUAL** e escolha ambas as máquinas virtuais que hospedarão réplicas do grupo de disponibilidade. Não inclua o servidor de testemunha de compartilhamento de arquivos.

   >[!NOTE]
   >Se ambas as máquinas virtuais não forem especificadas, as conexões terão êxito apenas para a réplica primária.

1. Selecione **OK** para criar o pool de back-end.

### <a name="set-the-probe"></a>Definir a investigação

1. Selecione o balanceador de carga, escolha **investigações de integridade** e, em seguida, selecione **+Adicionar**.

1. Defina a investigação de integridade do ouvinte, conforme a seguir:

   | Configuração | Descrição | Exemplo
   | --- | --- |---
   | **Nome** | Texto | SQLAlwaysOnEndPointProbe |
   | **Protocolo** | Escolher TCP | TCP |
   | **Porta** | Qualquer porta não utilizada | 59999 |
   | **Intervalo**  | O tempo entre as tentativas de investigação, em segundos |5 |
   | **Limite não íntegro** | O número de falhas de investigação consecutivas que deve ocorrer para uma máquina virtual ser considerada não íntegra  | 2 |

1. Selecione **OK** para definir a investigação de integridade.

### <a name="set-the-load-balancing-rules"></a>Definir as regras de balanceamento de carga

1. Selecione o balanceador de carga, escolha **Regras de balanceamento de carga** e selecione **+Adicionar**.

1. Defina as regras de balanceamento de carga do ouvinte, conforme a seguir.

   | Configuração | Descrição | Exemplo
   | --- | --- |---
   | **Nome** | Texto | SQLAlwaysOnEndPointListener |
   | **Endereço IP de front-end** | Escolher um endereço |Use o endereço que você criou ao criar o balanceador de carga. |
   | **Protocolo** | Escolher TCP |TCP |
   | **Porta** | Usar a porta para o ouvinte do grupo de disponibilidade | 1433 |
   | **Porta de back-end** | Este campo não é usado quando o IP flutuante é definido para o retorno de servidor direto | 1433 |
   | **Investigação** |O nome especificado para o teste | SQLAlwaysOnEndPointProbe |
   | **Persistência de sessão** | Lista suspensa | **Nenhuma** |
   | **Tempo limite de ociosidade** | Minutos para manter uma conexão TCP aberta | 4 |
   | **IP flutuante (retorno de servidor direto)** | |habilitado |

   > [!WARNING]
   > O retorno de servidor direto é definido durante a criação. Ele não pode ser alterado.
   >

1. Selecione **OK** para definir as regras de balanceamento de carga do ouvinte.

### <a name="add-the-cluster-core-ip-address-for-the-windows-server-failover-cluster-wsfc"></a>Adicionar o endereço IP principal do cluster para o WSFC (Cluster de Failover do Windows Server)

O endereço IP do WSFC também precisa estar no balanceador de carga.

1. No portal do Azure, acesse o mesmo balanceador de carga do Azure. Selecione **configuração IP de Front-end** e, em seguida, **+Adicionar**. Use o Endereço IP configurado para o WSFC nos recursos principais do cluster. Defina o endereço IP como estático.

1. No balanceador de carga, selecione **Investigações de integridade** e, em seguida, **+Adicionar**.

1. Defina a investigação de integridade do endereço IP principal de cluster do WSFC, conforme a seguir:

   | Configuração | Descrição | Exemplo
   | --- | --- |---
   | **Nome** | Texto | WSFCEndPointProbe |
   | **Protocolo** | Escolher TCP | TCP |
   | **Porta** | Qualquer porta não utilizada | 58888 |
   | **Intervalo**  | O tempo entre as tentativas de investigação, em segundos |5 |
   | **Limite não íntegro** | O número de falhas de investigação consecutivas que deve ocorrer para uma máquina virtual ser considerada não íntegra  | 2 |

1. Selecione **OK** para definir a investigação de integridade.

1. Definir as regras de balanceamento de carga. Selecione **Regras de balanceamento de carga** e, em seguida, **+Adicionar**.

1. Defina as regras de balanceamento de carga de endereço IP do núcleo do cluster, conforme a seguir.

   | Configuração | Descrição | Exemplo
   | --- | --- |---
   | **Nome** | Texto | WSFCEndPoint |
   | **Endereço IP de front-end** | Escolher um endereço |Use o endereço que você criou quando configurou o endereço IP do WSFC. Isso é diferente do endereço IP do ouvinte |
   | **Protocolo** | Escolher TCP |TCP |
   | **Porta** | Use a porta para o endereço IP de cluster. Essa é uma porta disponível que não é usada para a porta de investigação do ouvinte. | 58888 |
   | **Porta de back-end** | Este campo não é usado quando o IP flutuante é definido para o retorno de servidor direto | 58888 |
   | **Investigação** |O nome especificado para o teste | WSFCEndPointProbe |
   | **Persistência de sessão** | Lista suspensa | **Nenhuma** |
   | **Tempo limite de ociosidade** | Minutos para manter uma conexão TCP aberta | 4 |
   | **IP flutuante (retorno de servidor direto)** | |habilitado |

   > [!WARNING]
   > O retorno de servidor direto é definido durante a criação. Ele não pode ser alterado.
   >

1. Selecione **OK** para definir as regras de balanceamento de carga.

## <a name="configure-the-listener"></a><a name="configure-listener"></a> Configurar o ouvinte

A próxima etapa é configurar um ouvinte do grupo de disponibilidade no cluster de failover.

> [!NOTE]
> Este tutorial mostra como criar um ouvinte usando um endereço IP de ILB. Para criar um ou mais ouvintes usando um ou mais endereços IP, veja [Criar o ouvinte do grupo de disponibilidade e balanceador de carga | Azure](availability-group-listener-powershell-configure.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
>

[!INCLUDE [ag-listener-configure](../../../../includes/virtual-machines-ag-listener-configure.md)]

## <a name="set-listener-port"></a>Definir porta do ouvinte

No SQL Server Management Studio, defina a porta do ouvinte.

1. Inicie o SQL Server Management Studio e conecte-se à réplica principal.

1. Navegue até **Alta Disponibilidade AlwaysOn** > **grupos de disponibilidade** > **Ouvintes do grupo de disponibilidade**.

1. Agora você deve ver o nome do ouvinte que você criou no Gerenciador de Cluster de Failover. Clique com o botão direito do mouse no nome do ouvinte e selecione **Propriedades**.

1. Na caixa **Porta**, especifique o número da porta do ouvinte do grupo de disponibilidade. O padrão é 1433. Selecione **OK**.

Agora você tem um grupo de disponibilidade do SQL Server em máquinas virtuais do Azure, em execução no modo de Resource Manager.

## <a name="test-connection-to-listener"></a>Testar conexão com o ouvinte

Para testar a conexão:

1. Use o RDP para se conectar a um SQL Server que esteja na mesma rede virtual, mas não tenha a réplica. Você pode usar o outro SQL Server no cluster.

1. Use o utilitário **sqlcmd** para testar a conexão. Por exemplo, o script a seguir estabelece uma conexão de **sqlcmd** com a réplica primária por meio do ouvinte com autenticação do Windows:

   ```cmd
   sqlcmd -S <listenerName> -E
   ```

   Se o ouvinte estiver usando uma porta diferente da porta padrão (1433), especifique a porta na cadeia de conexão. Por exemplo, o comando `sqlcmd` a seguir conecta-se a um ouvinte na porta 1435:

   ```cmd
   sqlcmd -S <listenerName>,1435 -E
   ```

A conexão SQLCMD se conecta automaticamente a qualquer instância do SQL Server que hospede a réplica primária.

> [!TIP]
> Verifique se a porta especificada está aberta no firewall dos servidores SQL. Ambos os servidores exigem uma regra de entrada para a porta TCP que você usa. Para saber mais, confira [Adicionar ou Editar Regra de Firewall](/previous-versions/orphan-topics/ws.11/cc753558(v=ws.11)).
>

## <a name="next-steps"></a>Próximas etapas

- [Adicionar um endereço IP a um balanceador de carga para um segundo grupo de disponibilidade](availability-group-listener-powershell-configure.md#Add-IP).