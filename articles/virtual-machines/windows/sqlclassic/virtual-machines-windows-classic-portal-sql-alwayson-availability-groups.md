---
title: "Configurar grupo de disponibilidade AlwaysOn em Máquinas Virtuais do Azure (clássico) | Microsoft Docs"
description: "Crie um grupo de disponibilidade AlwaysOn em Máquinas Virtuais do Azure. Este tutorial usa principalmente a interface do usuário e ferramentas em vez de scripts."
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: 8d48a3d2-79f8-4ab0-9327-2f30ee0b2ff1
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/17/2017
ms.author: mikeray
ms.openlocfilehash: b360fe9f28eeb9b10c82fce729165b1b572ac3c6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="configure-always-on-availability-group-in-azure-virtual-machines-classic"></a>Configurar grupo de disponibilidade AlwaysOn em Máquinas Virtuais do Azure (clássico)
> [!div class="op_single_selector"]
> * [Clássico: Interface de usuário](../classic/portal-sql-alwayson-availability-groups.md)
> * [Clássico: PowerShell](../classic/ps-sql-alwayson-availability-groups.md)
<br/>

Antes de começar, considere que agora você pode concluir esta tarefa no modelo do Azure Resource Manager. O modelo do Azure Resource Manager é recomendável para novas implantações. Consulte [Grupos de disponibilidade AlwaysOn do SQL Server em máquinas virtuais do Azure](../sql/virtual-machines-windows-portal-sql-availability-group-overview.md).

> [!IMPORTANT] 
> A Microsoft recomenda que a maioria das implantações novas use o modelo do Gerenciador de Recursos. O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: [Gerenciador de Recursos e clássico](../../../azure-resource-manager/resource-manager-deployment-model.md). Este artigo explica como usar o modelo de implantação clássico. 

Para concluir essa tarefa com o modelo do Azure Resource Manager, consulte [Grupos de disponibilidade AlwaysOn do SQL Server em máquinas virtuais do Azure](../sql/virtual-machines-windows-portal-sql-availability-group-overview.md).

Este tutorial completo mostra como implementar os grupos de disponibilidade usando o AlwaysOn do SQL Server em execução em Máquinas Virtuais do Azure.

Ao final do tutorial, sua solução SQL Server AlwaysOn no Azure consistirá nos seguintes elementos:

* Uma rede virtual que contém várias sub-redes, incluindo uma sub-rede de front-end e uma de back-end
* Um controlador de domínio com um domínio do Active Directory (Azure AD)
* Duas máquinas virtuais que executam o SQL Server e são implantadas na sub-rede de back-end e adicionadas ao domínio do Azure AD
* Um cluster de failover de 3 nós com o modelo de quorum Maioria dos Nós
* Um grupo de disponibilidade que tem duas réplicas de confirmação síncrona de um banco de dados de disponibilidade

A ilustração abaixo é uma representação gráfica da solução.

![Arquitetura de laboratório de teste para grupos de disponibilidade no Azure](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC791912.png)

Observe que se trata de uma das configurações possíveis. Por exemplo,é possível minimizar o número de máquinas virtuais para um grupo de disponibilidade de duas réplicas. Essa configuração economiza horas de computação no Azure usando o controlador de domínio como testemunha de compartilhamento de arquivos quorum em um cluster de dois nós. Este método reduz a contagem de máquina virtual por um da configuração ilustrada.

Este tutorial pressupõe o seguinte:

* Você já tem uma conta do Azure.
* Você já sabe como usar a GUI na galeria da máquina virtual para provisionar uma máquina virtual clássica que executa o SQL Server.
* Você já tem uma compreensão sólida dos grupos de disponibilidade AlwaysOn. Para obter mais informações, confira [Grupos de disponibilidade Always On (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx).

> [!NOTE]
> Se você estiver interessado em usar os grupos de disponibilidade Always On com o SharePoint, consulte também [Configure SQL Server 2012 Always On Availability Groups for SharePoint 2013](https://technet.microsoft.com/library/jj715261.aspx)(Configurar grupos de disponibilidade AlwaysOn do SQL Server 2012 para o SharePoint 2013).
> 
> 

## <a name="create-the-virtual-network-and-domain-controller-server"></a>Criar a rede virtual e o servidor de controlador de domínio
Comece com uma nova conta de avaliação do Azure. Após configurar sua conta, você deverá estar na tela inicial do portal clássico do Azure.

1. Clique no **novo** no canto esquerdo da parte inferior da página, conforme mostrado na seguinte captura de tela.
   
    ![Clique em Novo no portal](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665511.gif)
2. Clique em **Serviços de Rede** > **Rede Virtual** > **Criação Personalizada**, conforme mostrado na seguinte captura de tela.
   
    ![Criar rede virtual](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665512.gif)
3. Na caixa de diálogo **CREATE A VIRTUAL NETWORK** crie uma nova rede virtual percorrendo as páginas e usando as configurações na tabela a seguir. 
   
   | Página | Configurações |
   | --- | --- |
   | Detalhes de rede virtual |**NAME = ContosoNET**<br/>**REGION = Oeste dos EUA** |
   | Conectividade de VPN e servidores DNS |Nenhum |
   | Espaços de Endereço da Rede Virtual |As configurações são mostradas na seguinte captura de tela: ![Criar rede virtual](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784620.png) |
4. Crie a máquina virtual que será usada como o Controlador de Domínio (DC). Clique em **Novo** > **Computação** > **Máquina virtual** > **Da Galeria**, conforme mostrado na seguinte captura de tela.
   
    ![Criar uma máquina virtual](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784621.png)
5. Na caixa de diálogo **CREATE A VIRTUAL MACHINE** configure uma nova máquina virtual percorrendo pelas páginas e usando as configurações na tabela a seguir. 
   
   | Página | Configurações |
   | --- | --- |
   | Selecionar o sistema operacional da máquina virtual |Windows Server 2012 R2 Datacenter |
   | Configuração de máquina virtual |**VERSION RELEASE DATE** = (latest)<br/>**VIRTUAL MACHINE NAME** = ContosoDC<br/>**TIER** = STANDARD<br/>**TAMANHO** = A2 (2 núcleos)<br/>**NEW USER NAME** = AzureAdmin<br/>**NEW PASSWORD** = Contoso!000<br/>**CONFIRM** = Contoso!000 |
   | Configuração de máquina virtual |**CLOUD SERVICE** = Criar um novo serviço de nuvem<br/>**CLOUD SERVICE DNS NAME** = um nome de serviço de nuvem exclusivo<br/>**DNS NAME** = um nome exclusivo (ex: ContosoDC123)<br/>**REGION/AFFINITY GROUP/VIRTUAL NETWORK** = ContosoNET<br/>**VIRTUAL NETWORK SUBNETS** = Back(10.10.2.0/24)<br/>**CONTA DE ARMAZENAMENTO** = Use uma conta de armazenamento gerada automaticamente<br/>**AVAILABILITY SET** = (Nenhum) |
   | Opções de máquina virtual |Usar padrões |

Após configurar a nova máquina virtual, aguarde a máquina virtual ser provisionada. Esse processo demora algum tempo para concluir. Se você clicar na guia **Máquina Virtual** no portal clássico do Azure, poderá ver os estados de ciclo de ContosoDC desde **Iniciando (Provisionamento)** até **Parado**, **Iniciando**, **Em execução (Provisionamento)** e, finalmente, **Em execução**.

O servidor de DC agora foi provisionado com êxito. Em seguida, você configurará o domínio do Active Directory nesse servidor de DC.

## <a name="configure-the-domain-controller"></a>Configurar o controlador de domínio
Nas etapas a seguir, você poderá configurar o computador ContosoDC como um controlador de domínio para corp.contoso.com.

1. No portal, selecione o computador **ContosoDC** . Na guia **Painel**, clique em **Conectar** para abrir uma área de trabalho remota (RDP) para acesso à área de trabalho remota.
   
    ![Conectar à Máquina Virtual](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784622.png)
2. Entre com sua conta de administrador configurada (**\AzureAdmin**) e senha (**Contoso!000**).
3. Por padrão, o painel **Gerenciador do Servidor** deve ser exibido.
4. Clique no link **Adicionar funções e recursos** no painel.
   
    ![Adicionar Funções ao Gerenciador de Servidores](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784623.png)
5. Clique em **Avançar** até alcançar a seção **Funções de Servidor**.
6. Selecione **Active Directory Domain Services** e as funções do **Servidor DNS**. Quando solicitado, adicione mais recursos que exigem essas funções.
   
   > [!NOTE]
   > Você receberá um aviso de validação de que não há nenhum endereço IP estático. Se você estiver testando a configuração, clique em **Continuar**. Para cenários de produção, [use o PowerShell para definir o endereço IP estático do computador do controlador de domínio](../../../virtual-network/virtual-networks-reserved-private-ip.md).
   > 
   > 
   
    ![Adicionar Caixa de Diálogo de Funções](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784624.png)
7. Clique em **Avançar** até alcançar a seção **Confirmação**. Marque a caixa de seleção **Reiniciar cada servidor de destino automaticamente, se necessário** .
8. Clique em **Instalar**.
9. Depois que os recursos forem instalados, retorne para o painel **Gerenciador do Servidor** .
10. Selecione a nova opção **AD DS** no painel esquerdo.
11. Clique no link **Mais** na barra de aviso amarela.
    
     ![Caixa de diálogo do AD DS na máquina virtual do servidor DNS](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784625.png)
12. Na coluna **Ação** da caixa de diálogo **Todos os Detalhes de Tarefa do Servidor**, clique em **Promover este servidor a um controlador de domínio**.
13. No **Assistente de Configuração dos Serviços de Domínio do Active Directory**, use os seguintes valores:
    
    | Página | Configuração |
    | --- | --- |
    | Configuração de Implantação |**Adicionar uma nova floresta** = selecionado<br/>**Nome do domínio raiz** = corp.contoso.com |
    | Opções de Controlador de Domínio |**Senha** = Contoso!000<br/>**Confirmar Senha** = Contoso!000 |
14. Clique em **Avançar** para percorrer as outras páginas do assistente. Na página **Verificação de pré-requisitos**, verifique se a seguinte mensagem é exibida: **Todas as verificações de pré-requisitos passaram com êxito**. Observe que você deve examinar todas as mensagens de aviso aplicáveis, porém é possível continuar com a instalação.
15. Clique em **Instalar**. A máquina virtual **ContosoDC** será reinicializada automaticamente.

## <a name="configure-domain-accounts"></a>Configurar contas de domínio
As próximas etapas configuram as contas do Active Directory (AD) para uso posterior.

1. Entre novamente no computador **ContosoDC** .
2. No **Gerenciador de Servidores**, clique em **Ferramenta** > **Centro Administrativo do Active Directory**.
   
    ![Centro Administrativo do Active Directory](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784626.png)
3. No **Centro Administrativo do Active Directory**, selecione **corp (local)** no painel esquerdo.
4. No painel direito **Tarefas**, clique em **Novo** > **Usuário**. Use as configurações a seguir:
   
   | Configuração | Valor |
   | --- | --- |
   | **Nome** |Instalar |
   | **SamAccountName do usuário** |Instalar |
   | **Senha** |Contoso!000 |
   | **Confirmar senha** |Contoso!000 |
   | **Outras opções de senha** |Selecionado |
   | **A senha nunca expira** |Verificado |
5. Clique em **OK** para criar o usuário de **Instalação**. Essa conta será usada para configurar o cluster de failover e o grupo de disponibilidade.
6. Crie dois usuários adicionais, **CORP\SQLSvc1** e **CORP\SQLSvc2**, com as mesmas etapas. Essas contas serão usadas para instâncias do SQL Server. Em seguida, você precisará fornecer a **CORP\Install** as permissões necessárias para configurar o cluster de failover do Windows.
7. No **Centro Administrativo do Active Directory**, clique em **corp (local)** no painel esquerdo. No painel **Tarefas**, clique em **Propriedades**.
   
    ![Propriedades do usuário CORP](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784627.png)
8. Selecione **Extensões** e clique no botão **Avançado** na guia **Segurança**.
9. Na caixa de diálogo **Configurações de Segurança Avançada para corp**  clique em **Adicionar**.
10. Clique em **Selecionar uma entidade**, procure **CORP\Install**e, em seguida, clique em **OK**.
11. Selecione as permissões **Ler todas as propriedades** e **Criar objetos de computador**.
    
     ![Permissões de usuário corp](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784628.png)
12. Clique em **OK** e em **OK** novamente. Feche a janela de propriedades corporativas.

Agora que você concluiu a configuração do Active Directory e dos objetos de usuário, você criará três máquinas virtuais do SQL Server e irá associá-las a esse domínio.

## <a name="create-the-sql-server-virtual-machines"></a>Criar máquinas virtuais do SQL Server
Criar três máquinas virtuais do SQL Server. Uma é para um nó de cluster e duas são para o SQL Server. Para criar cada uma das máquinas virtuais, volte ao portal clássico do Azure, clique em **Novo** > **Computação** > **Máquina Virtual** > **Da Galeria**. Em seguida, use os modelos na tabela a seguir para ajudá-lo a criar as máquinas virtuais.

| Página | VM1 | VM2 | VM3 |
| --- | --- | --- | --- |
| Selecionar o sistema operacional da máquina virtual |**Windows Server 2012 R2 Datacenter** |**SQL Server 2014 RTM Enterprise** |**SQL Server 2014 RTM Enterprise** |
| Configuração de máquina virtual |**VERSION RELEASE DATE** = (latest)<br/>**VIRTUAL MACHINE NAME** = ContosoWSFCNode<br/>**TIER** = STANDARD<br/>**TAMANHO** = A2 (2 núcleos)<br/>**NEW USER NAME** = AzureAdmin<br/>**NEW PASSWORD** = Contoso!000<br/>**CONFIRM** = Contoso!000 |**VERSION RELEASE DATE** = (latest)<br/>**VIRTUAL MACHINE NAME** = ContosoSQL1<br/>**TIER** = STANDARD<br/>**TAMANHO** = A3 (4 núcleos)<br/>**NEW USER NAME** = AzureAdmin<br/>**NEW PASSWORD** = Contoso!000<br/>**CONFIRM** = Contoso!000 |**VERSION RELEASE DATE** = (latest)<br/>**VIRTUAL MACHINE NAME** = ContosoSQL2<br/>**TIER** = STANDARD<br/>**TAMANHO** = A3 (4 núcleos)<br/>**NEW USER NAME** = AzureAdmin<br/>**NEW PASSWORD** = Contoso!000<br/>**CONFIRM** = Contoso!000 |
| Configuração de máquina virtual |**CLOUD SERVICE** = nome DNS do serviço de nuvem exclusivo criado anteriormente (ex: ContosoDC123)<br/>**REGION/AFFINITY GROUP/VIRTUAL NETWORK** = ContosoNET<br/>**VIRTUAL NETWORK SUBNETS** = Back(10.10.2.0/24)<br/>**CONTA DE ARMAZENAMENTO** = Use uma conta de armazenamento gerada automaticamente<br/>**AVAILABILITY SET** = Criar um conjunto de disponibilidade<br/>**AVAILABILITY SET NAME** = SQLHADR |**CLOUD SERVICE** = nome DNS do serviço de nuvem exclusivo criado anteriormente (ex: ContosoDC123)<br/>**REGION/AFFINITY GROUP/VIRTUAL NETWORK** = ContosoNET<br/>**VIRTUAL NETWORK SUBNETS** = Back(10.10.2.0/24)<br/>**CONTA DE ARMAZENAMENTO** = Use uma conta de armazenamento gerada automaticamente<br/>**AVAILABILITY SET** = SQLHADR (Você também pode configurar o conjunto de disponibilidade após a criação da máquina. Todas as três máquinas devem ser atribuídas ao conjunto de disponibilidade SQLHADR.) |**CLOUD SERVICE** = nome DNS do serviço de nuvem exclusivo criado anteriormente (ex: ContosoDC123)<br/>**REGION/AFFINITY GROUP/VIRTUAL NETWORK** = ContosoNET<br/>**VIRTUAL NETWORK SUBNETS** = Back(10.10.2.0/24)<br/>**CONTA DE ARMAZENAMENTO** = Use uma conta de armazenamento gerada automaticamente<br/>**AVAILABILITY SET** = SQLHADR (Você também pode configurar o conjunto de disponibilidade após a criação da máquina. Todas as três máquinas devem ser atribuídas ao conjunto de disponibilidade SQLHADR.) |
| Opções de máquina virtual |Usar padrões |Usar padrões |Usar padrões |

<br/>

> [!NOTE]
> A configuração anterior sugere máquinas virtuais de camada STANDARD, porque máquinas de camada BÁSICA não dão suporte aos pontos de extremidade. Você precisa de pontos de extremidade com balanceamento de carga para criar um ouvinte do grupo de disponibilidade posteriormente. Além disso, os tamanhos de máquina sugeridos aqui servem para testar grupos de disponibilidade em máquinas virtuais do Azure. Para obter o melhor desempenho em cargas de trabalho de produção, consulte as recomendações de configuração e tamanhos de máquina do SQL Server em [Práticas recomendadas relacionadas ao desempenho para o SQL Server em máquinas virtuais do Azure](../sql/virtual-machines-windows-sql-performance.md).
> 
> 

Depois que as três máquinas virtuais forem totalmente provisionadas, você precisará ingressá-las no domínio **corp.contoso.com** e conceder direitos administrativos CORP\Install às máquinas. Para fazer isso, use as seguintes etapas para cada uma das três máquinas virtuais.

1. Primeiro, altere o endereço do servidor DNS preferencial. Comece baixando o arquivo RDP de cada máquina virtual em seu diretório local selecionando a máquina virtual na lista e clicando no botão **Conectar** . Para selecionar uma máquina virtual, clique em qualquer lugar, exceto na primeira célula da linha, conforme mostrado na seguinte captura de tela.
   
    ![Baixe o arquivo RDP](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC664953.jpg)
2. Abra o arquivo RDP que você baixou e entre na máquina virtual usando sua conta de administrador configurada (**BUILTIN\AzureAdmin**) e senha (**Contoso!000**).
3. Quando estiver conectado, você deverá ver o painel **Gerenciador de Servidores** . No painel esquerdo, clique em **Servidor Local** .
4. Clique no link **IPv4 address assigned by DHCP, IPv6 enabled**.
5. Na caixa de diálogo **Conexões de Rede** , clique no ícone de rede.
   
    ![Alterar o servidor DNS preferencial da máquina virtual](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784629.png)
6. Na barra de comandos, clique em **Alterar as configurações dessa conexão**. (Dependendo do tamanho da janela, talvez você precise clicar na seta dupla à direita para ver esse comando).
7. Selecione **Internet Protocol Version 4 (TCP/IPv4)** e, em seguida, clique em **Propriedades**.
8. Selecione **Usar os seguintes endereços de servidor DNS e, em seguida, especifique** e. em seguida, especifique **10.10.2.4** no **Servidor DNS preferencial**.
9. O endereço **10.10.2.4** é o endereço atribuído a uma máquina virtual na sub-rede 10.10.2.0/24 em uma rede virtual do Azure. Essa máquina virtual é **ContosoDC**. Para verificar o endereço ID de **ContosoDC**, use **nslookup contosodc** na janela do prompt de comando, como mostrado na seguinte captura de tela.
   
    ![Use NSLOOKUP para localizar o endereço IP para controlador de domínio](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC664954.jpg)
10. Clique em **OK** > **Fechar** para confirmar as alterações. Você pode adicionar a máquina virtual para **corp.contoso.com**.
11. Volte para a janela **Servidor Local** e clique no link **GRUPO DE TRABALHO**.
12. Na seção **Nome do Computador**, clique em **Alterar**.
13. Selecione a caixa de seleção **Domínio** digite **corp.contoso.com** na caixa de texto e, em seguida, clique em **OK**.
14. Na caixa de diálogo **Segurança do Windows**, especifique as credenciais para a conta de administrador de domínio padrão (**CORP\AzureAdmin**) e a senha (**Contoso!000**).
15. Quando a mensagem "Bem-vindo ao domínio corp.contoso.com" for exibida, clique em **OK**.
16. Clique em **fechar** > **Reiniciar Agora** na caixa de diálogo.

### <a name="add-the-corpinstall-user-as-an-administrator-on-each-virtual-machine"></a>Adicione o usuário Corp\Install como um administrador em cada máquina virtual
1. Aguarde até que a máquina virtual reinicie e, em seguida, abra o RDP arquivo novamente para se conectar à máquina virtual usando a conta **BUILTIN\AzureAdmin**.
2. Em **Gerenciador de Servidores** clique **Ferramentas** > **Gerenciamento do Computador**.
   
    ![Gerenciamento do Computador](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784630.png)
3. Na caixa de diálogo **Gerenciamento do Computador**, expanda **Usuários e Grupos Locais** e, em seguida, selecione **Grupos**.
4. Clique duas vezes no grupo **Administradores** .
5. Na caixa de diálogo **Propriedades de Administradores**, clique no botão **Adicionar**.
6. Insira o usuário **CORP\Install** e clique em **OK**. Quando solicitado a fornecer credenciais, use a conta **AzureAdmin** com a senha **Contoso!000**.
7. Clique em **OK** para fechar a caixa do diálogo **Propriedades do Administrador**.

### <a name="add-the-failover-clustering-feature-to-each-virtual-machine"></a>Adicionar o recurso de Cluster de Failover para cada máquina virtual
1. No painel **Gerenciador do Servidor**, clique em **Adicionar funções e recursos**.
2. No **Assistente para Adicionar Funções e Recursos**, clique em **Avançar** até chegar à página **Recursos**.
3. Selecione **Clustering de Failover**. Quando solicitado, adicione outros recursos dependentes.
   
    ![Adicionar o recurso de Cluster de Failover para máquina virtual](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784631.png)
4. Clique em **Avançar** e em **Instalar** na página **Confirmação**.
5. Quando a instalação do recurso **Clustering de Failover** for concluída, clique em **Fechar**.
6. Saia da máquina virtual.
7. Repita as etapas nesta seção para todos os três servidores: **ContosoWSFCNode**, **ContosoSQL1** e **ContosoSQL2**.

As máquinas virtuais do SQL Server agora estão provisionadas e em execução, mas cada uma tem as opções padrão para o SQL Server.

## <a name="create-the-failover-cluster"></a>Criar o cluster de failover
Nesta seção, você cria o cluster de failover que hospedará o grupo de disponibilidade que será criado posteriormente. Por enquanto, é necessário ter feito o seguinte para cada uma das três máquinas virtuais que serão usadas no cluster de failover:

* A máquina virtual foi provisionada com êxito no Azure
* Ingressou na máquina virtual para o domínio
* **CORP\Install** adicionado ao grupo de administradores local
* Adicionado o recurso de cluster de failover

Todos são pré-requisitos em cada máquina virtual para poder ingressá-la no cluster de failover.

Além disso, observe que a rede virtual do Azure não se comporta da mesma forma que uma rede local. Você precisa criar o cluster na seguinte ordem:

1. Crie um cluster de nó único em um nó (**ContosoSQL1**).
2. Modifique o endereço IP do cluster para um endereço IP não usado (**10.10.2.101**).
3. Deixe o nome do cluster online.
4. Adicione outros nós (**ContosoSQL2** e **ContosoWSFCNode**).

Use as etapas a seguir para concluir as tarefas que configuram completamente o cluster.

1. Abra o arquivo RDP para **ContosoSQL1** e entre usando a conta de domínio **CORP\Install**.
2. No painel **Server Manager**, clique em **Ferramentas** > **Gerenciador de Cluster de Failover**.
3. No painel esquerdo, clique com o botão direito do mouse em **Gerenciador de Cluster de Failover** e clique em **Criar um Cluster**, conforme mostrado na seguinte captura de tela.
   
    ![Criar cluster](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784632.png)
4. No Assistente para Criação de Cluster, crie um cluster de um nó percorrendo as páginas e use as configurações na seguinte tabela:
   
   | Página | Configurações |
   | --- | --- |
   | Antes de começar |Usar padrões |
   | Selecionar Servidores |Digite **ContosoSQL1** em **Inserir nome do servidor** e clique em **Adicionar** |
   | Aviso de Validação |Selecione **Não. Eu não preciso de suporte da Microsoft para este cluster e, portanto, não desejo executar testes de validação. Continuar a criar o cluster quando eu clicar em Avançar**. |
   | Ponto de Acesso para Administrar o Cluster |Digite **Cluster1** em **Nome do Cluster** |
   | Confirmação |Use os padrões, a menos que você esteja usando Espaços de Armazenamento. Consulte o aviso após esta tabela. |
   
   > [!WARNING]
   > Se você estiver usando [Espaços de Armazenamento](https://technet.microsoft.com/library/hh831739), que agrupam vários discos em pools de armazenamento, desmarque a caixa de seleção **Adicione todo o armazenamento qualificado ao cluster** na página **Confirmação**. Se você não desmarcar essa opção, os discos virtuais serão desanexados durante o processo de clustering. Como resultado, eles também não aparecerão no Gerenciador ou Explorador de Discos até que os espaços de armazenamento sejam removidos do cluster e reanexados usando o PowerShell.
   > 
   > 
5. No painel esquerdo, expanda o **Gerenciador de Cluster de Failover** e clique em **Cluster1.corp.contoso.com**.
6. No painel central, role para baixo até a seção **Recursos Principais do Cluster** e expanda os detalhes **Nome: Clutser1**. Você verá os recursos de **Nome** e de **Endereço IP** no estado **Com Falha**. O recurso de endereço IP não pode ficar online porque o cluster recebeu o mesmo endereço IP que a própria máquina, que é um endereço duplicado.
7. Clique com o botão direito do mouse no recurso **Endereço IP** com falha e clique em **Propriedades**.
   
    ![Propriedades do cluster](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784633.png)
8. Selecione **Endereço IP Estático**, especifique **10.10.2.101** na caixa de texto **Endereço** e clique em **OK**.
9. Na seção **Recursos Principais do Cluster**, clique com o botão direito do mouse em **Nome: Cluster1** e clique em **Colocar Online**. Aguarde até que ambos os recursos estejam online. Quando o recurso de nome de cluster fica online, o servidor DC é atualizado com uma nova conta de computador do Active Directory. Essa conta Active Directory será usada para executar o serviço clusterizado do grupo de disponibilidade posteriormente.
10. Adicione os nós restantes ao cluster. Na árvore do navegador, clique com o botão direito do mouse em **Cluster1.corp.contoso.com** e, em seguida, clique **Adiconar Nó**, conforme mostrado na seguinte captura de tela.
    
     ![Adicionar nó ao cluster](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784634.png)
11. Em **Assistente para Adicionar Nós**, clique em **Avançar** na página **Selecionar servidores**, adicione **ContosoSQL2** e **ContosoWSFCNode** à lista, digitando o nome do servidor em **Insira o nome do servidor** e, em seguia, clique em **Adicionar**. Quando terminar, clique em **Avançar**.
12. Na página **Aviso de Validação**, clique em **Não** em um cenário de produção, você deve executar os testes de validação. Em seguida, clique em **Avançar**.
13. Na página **Confirmação**, clique em **Avançar** para adicionar os nós.
    
    > [!WARNING]
    > Se você estiver usando [Espaços de Armazenamento](https://technet.microsoft.com/library/hh831739), que agrupam vários discos em pools de armazenamento, desmarque a caixa de seleção **Adicione todo o armazenamento qualificado ao cluster** . Se você não desmarcar essa opção, os discos virtuais serão desanexados durante o processo de clustering. Como resultado, eles também não aparecerão no Gerenciador ou Explorador de Discos até que os espaços de armazenamento sejam removidos do cluster e reanexados usando o PowerShell.
    > 
    > 
14. Depois que os nós forem adicionados ao cluster, clique em **Concluir**. O Gerenciador de Cluster de Failover agora deve mostrar que o cluster tem três nós e listá-los no contêiner **Nós** .
15. Saia da sessão de área de trabalho remota.

## <a name="prepare-the-sql-server-instances-for-availability-groups"></a>Preparar instâncias do SQL Server para o grupos de disponibilidade
Nesta seção, você fará o seguinte em ambos os **ContosoSQL1** e **contosoSQL2**:

* Adicionar um logon para **NT AUTHORITY\System** com um conjunto de permissões necessárias para a instância padrão do SQL Server.
* Adicionar **CORP\Install** como uma função de sysadmin à instância padrão do SQL Server.
* Abra o firewall para acesso remoto do SQL Server.
* Habilitar o recurso de grupos de disponibilidade AlwaysOn.
* Alterar a conta de serviço do SQL Server para **CORP\SQLSvc1** e **CORP\SQLSvc2**, respectivamente.

Essas ações podem ser executadas em qualquer ordem. No entanto, as etapas a seguir as percorrerá em ordem. Siga as etapas para ambos **ContosoSQL1** e **ContosoSQL2**:

1. Se você não tiver saído da sessão de área de trabalho remota para a máquina virtual, faça isso agora.
2. Abra os arquivos RDP para **ContosoSQL1** e **ContosoSQL2** e entre como **BUILTIN\AzureAdmin**.
3. Adicione **NT AUTHORITY\System** aos logons do SQL Server com as permissões necessárias. Abra o **SQL Server Management Studio**.
4. Clique em **Conectar** para se conectar à instância padrão do SQL Server.
5. No **Pesquisador de Objetos**, expanda **Segurança** e depois **Logons**.
6. Clique com o botão direito do mouse no logon **NT AUTHORITY\System** e, em seguida, clique em **Propriedades**.
7. Na página **Protegíveis**, para o servidor local, selecione **Conceder** para as seguintes permissões e, em seguida, clique em **OK**.
   
   * Alterar qualquer grupo de disponibilidade
   * Conectar o SQL
   * Exibir o estado do servidor
8. Adicionar **CORP\Install** como uma função **sysadmin** para a instância padrão do SQL Server. Em **Pesquisador de Objetos**, clique com o botão direito do mouse em **Logons** e, em seguida, clique em **Novo Logon**.
9. Digite **CORP\Install** em **Nome de logon**.
10. Na página **Funções de Servidor**, selecione **sysadmin** e, em seguida, clique em **OK**. Depois que o logon for criado, você pode vê-lo expandindo **Logons** in **Pesquisador de Objetos**.
11. Para criar uma regra de firewall para SQL Server, na tela **Iniciar**, abra **Firewall do Windows com Segurança Avançada**.
12. No painel esquerdo, selecione **Regras de Entrada**. No painel direito, clique em **Nova Regra**.
13. Na página**Tipo de Regra** , clique em **Programa** > **Avançar**.
14. Na página **Programa**, selecione **Caminho do Programa**, digite **%ProgramFiles%\Microsoft SQL Server\MSSQL12.MSSQLSERVER\MSSQL\Binn\sqlservr.exe** na caixa de texto e, em seguida, clique em **Avançar**. Se você estiver seguindo essas instruções, mas usando o SQL Server 2012, o diretório do SQL Server será **MSSQL11.MSSQLSERVER**.
15. Na página **Ação**, mantenha selecionado **Permitir a conexão** e, em seguida, clique em **Avançar**.
16. Na página **Perfil**, aceite as configurações padrão e, em seguida, clique em **Avançar**.
17. Na página **Nome**, especifique um nome de regra, como **SQL Server (Regra de Programa)**, na caixa de texto **Nome** e, em seguida, clique em **Concluir**.
18. Para habilitar o recurso **Grupos de Disponibilidade AlwaysOn** na tela **Iniciar**, abra o **SQL Server Configuration Manager**.
19. Na árvore do navegador, clique em **Serviços do SQL Server**, clique com o botão direito do mouse no serviço **SQL Server (MSSQLSERVER)** e, em seguida, clique em **Propriedades**.
20. Clique na guia **Alta Disponibilidade AlwaysOn**, selecione **Habilitar Grupos de Disponibilidade AlwaysOn** conforme mostrado na seguinte tela de captura e, em seguida, e clique em **Aplicar**. Clique em **OK** na caixa de diálogo e não feche a caixa de diálogo **Properties** por enquanto. Você reiniciará o serviço SQL Server depois de alterar a conta de serviço.
    
     ![Habilitar grupos de disponibilidade AlwaysOn](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665520.gif)
21. Para alterar a conta de serviço do SQL Server, clique na guia **Log On** digite **CORP\SQLSvc1** (para **ContosoSQL1**) ou **CORP\SQLSvc2** (para **ContosoSQL2**) em **Nome da Conta**, preencha e confirme a senha e, em seguida, clique em **OK**.
22. Na caixa de diálogo, clique em **Sim** para reiniciar o serviço do SQL Server. Depois que o serviço SQL Server for reiniciado, as alterações feitas na caixa de diálogo **Propriedades** entrarão em vigor.
23. Saia das máquinas virtuais.

## <a name="create-the-availability-group"></a>Crie o grupo de disponibilidade
Agora você está pronto para configurar um grupo de disponibilidade. Abaixo está uma descrição do que você deve fazer:

* Crie um novo banco de dados (**MyDB1**) em **ContosoSQL1**.
* Faça um backup completo e um backup de log de transações do banco de dados.
* Restaure os backups completo e de log para **ContosoSQL2** com a opção **NORECOVERY**.
* Crie o grupo de disponibilidade (**AG1**) com confirmação síncrona, failover automático e réplicas secundárias legíveis.

### <a name="create-the-mydb1-database-on-contososql1"></a>Crie o banco de dados MyDB1 no ContosoSQL1
1. Se você ainda não tiver saído das sessões de área de trabalho remota para **ContosoSQL1** e **ContosoSQL2**, faça isso agora.
2. Abra o arquivo RDP para **ContosoSQL1** e entre como **CORP\Install**.
3. No **Explorador de Arquivos**, em **C:\\**, crie um diretório chamado **backup**. Você usará esse diretório para fazer backup e restaurar o banco de dados.
4. Clique com o botão direito do mouse no novo diretório, aponte o mouse para **Compartilhar com**, e clique em **Pessoas específicas**, conforme mostrado na seguinte captura de tela.
   
    ![Criar uma pasta de backup](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665521.gif)
5. Adicione **CORP\SQLSvc1**, e conceda a permissão **Read/Write**. Adicione **CORP\SQLSvc2**e conceda a permissão **Leitura**, conforme mostrado na seguinte captura de tela e, em seguida, clique em **Compartilhamento**. Depois que o processo de compartilhamento de arquivos for concluído, clique em **Concluído**.
   
    ![Conceder permissões para a pasta de Backup](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665522.gif)
6. Para criar o banco de dados, a partir do menu **Iniciar**, abra o **SQL Server Management Studio** e, em seguida, clique em **Conectar** para se conectar à instância padrão do SQL Server.
7. No **Pesquisador de Objetos**, clique com o botão direito do mouse em **Banco de Dados** e, em seguida, clique em **Novo Banco de Dados**.
8. Em **Nome do banco de dados**, digite **MyDB1** e, em seguida, clique em **OK**.

### <a name="make-a-full-backup-of-mydb1-and-restore-it-on-contososql2"></a>Faça um backup completo de MyDB1 e restaure-o no ContosoSQL2
1. Para fazer um backup completo do banco de dados, em **Pesquisador de Objetos**, expanda **Bancos de Dados**, clique com o botão direito do mouse **MyDB1**, aponte o mouse para **Tarefas** e, em seguida, clique em **Fazer Backup**.
2. Na seção **Origem**, mantenha o **Tipo de backup** definido como **Completo**. Na seção **Destino**, clique em **Remover** para remover o caminho de arquivo padrão para o arquivo de backup.
3. Na seção **Destino**, clique em **Adicionar**.
4. Na caixa de texto **Nome do arquivo**, digite  **\\ContosoSQL1\backup\MyDB1.bak**, clique em **OK**e, em seguida, clique em **OK** novamente para fazer backup de banco de dados. Quando a operação de backup for concluída, clique em **OK** novamente para fechar a caixa de diálogo.
5. Para fazer um backup de log de transações do banco de dados, em **Pesquisador de Objetos**, expanda **Bancos de Dados**, clique com o botão direito do mouse **MyDB1**, aponte o mouse para **Tarefas** e, em seguida, clique em **Fazer Backup**.
6. No tipo **Backup**, selecione **Log de Transações**. Mantenha o caminho do arquivo de **Destino** definido para aquele especificado anteriormente e, em seguida, clique em **OK**. Depois que a operação de backup for concluída, clique em **OK** novamente.
7. Para restaurar backups de log de transações do banco de dados completamente **ContosoSQL2**, abra o arquivo RDP para **ContosoSQL2**e entre como **CORP\Install**. Deixe a sessão de área de trabalho remota para **ContosoSQL1** aberta.
8. No menu **Iniciar**, abra o **SQL Server Management Studio** e, em seguida, clique em **Conectar** para se conectar à instância padrão do SQL Server.
9. No **Pesquisador de Objetos**, clique com o botão direito do mouse em **Bancos de Dados** e, em seguida, em **Restaurar Banco de Dados**.
10. Na seção **Origem**, selecione **Dispositivo** e clique nas reticências **…** .
11. Em **Selecionar dispositivos de backup**, clique em **Adicionar**.
12. Em **Local do Arquivo de Backup**, digite **\\ContosoSQL1\backup**, clique em **Atualizar**, selecione **MyDB1.bak**, clique em **OK**e, em seguida, clique em **OK** novamente. Agora, será possível ver o backup completo e o backup de log no painel **Conjuntos de backup para restauração**.
13. Vá para a página **Opções** , selecione **RESTORE WITH NORECOVERY** em **Estado de recuperação** e, em seguida, clique em **OK** para restaurar o banco de dados. Depois que a operação de restauração for concluída, clique em **OK** novamente.

### <a name="create-the-availability-group"></a>Crie o grupo de disponibilidade
1. Volte para a sessão de área de trabalho remota para **ContosoSQL1**. No **Pesquisador de Objetos** no SQL Server Management Studio, clique com o botão direito do mouse em **Alta Disponibilidade Always On** e, em seguida, clique em **Assistente para Novo Grupo de Disponibilidade**, conforme mostrado na seguinte captura de tela.
   
    ![Inicie o Assistente de Novo Grupo de Disponibilidade](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665523.gif)
2. Na página **Introdução** clique em **Avançar**. Na página **Especificar Nome do Grupo de Disponibilidade**, digite **AG1** em **Nome do grupo de disponibilidade** e clique em **Avançar** novamente.
   
    ![Assistente para Novo Grupo de Disponibilidade, Especificar o nome do grupo de disponibilidade](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665524.gif)
3. Na página **Selecionar Bancos de Dados**, selecione **MyDB1** e, em seguida, clique em **Avançar**. Esse banco de dados atende aos pré-requisitos para um grupo de disponibilidade, pois você fez pelo menos um backup completo na réplica primária pretendida.
   
    ![Assistente de Novo Grupo de Disponibilidade, Selecionar bancos de dados](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665525.gif)
4. na página **Especificar Réplicas**, clique em **Adicionar Réplica**.
   
    ![Assistente de Novo Grupo de Disponibilidade, Selecionar réplicas](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665526.gif)
5. na caixa de diálogo **Conectar-se ao Servidor**, digite **ContosoSQL2** em **Nome do Servidor**e, em seguida, clique em **Conectar**.
   
    ![Assistente de Novo Grupo de Disponibilidade, Conectar-se ao servidor](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665527.gif)
6. De volta à página **Especificar Réplicas**, você verá agora **ContosoSQL2** listado em **Réplicas Disponíveis**. Configure as réplicas conforme mostrado na seguinte captura de tela. Quando tiver terminado, clique em **Avançar**.
   
    ![Assistente de Novo Grupo de Disponibilidade, Selecionar réplicas (Concluído)](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665528.gif)
7. Na página **Selecionar Sincronização de Dados Inicial**, selecione **Somente ingressar** e, em seguida, clique em **Avançar**. Você já executou a sincronização de dados manualmente quando obteve os backups completos e de transações em **ContosoSQL1** e os restaurou em **ContosoSQL2**. Você pode preferir não realizar as operações de backup e restauração no seu banco de dados e selecionar **Completo** para permitir que o Assistente de Novo Grupo de Disponibilidade execute a sincronização de dados para você. No entanto, isso não é recomendado para grandes bancos de dados que se encontram em algumas empresas.
   
    ![Assistente de Novo Grupo de Disponibilidade, Selecionar sincronização inicial de dados](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665529.gif)
8. Na página **Validação**, clique em **Avançar**. Esta página deve ser semelhante à seguinte captura de tela. Há um aviso para a configuração de ouvinte porque você não configurou um ouvinte de grupo de disponibilidade. Você pode ignorar esse aviso, pois este tutorial não configura um ouvinte. Para configurar o ouvinte após a conclusão deste tutorial, veja [Configurar um ouvinte de ILB para Grupos de Disponibilidade AlwaysOn no Azure](../classic/ps-sql-int-listener.md).
   
    ![Assistente de Novo Grupo de Disponibilidade](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665530.gif)
9. Na página **Resumo**, clique em, em seguida, clique em **Concluir** e aguarde enquanto o assistente configura o novo grupo de disponibilidade. Na página **Progresso**, você pode clicar em **Mais detalhes** para exibir o progresso detalhado. Quando o assistente for concluído, inspecione a página **Resultados** para verificar se o grupo de disponibilidade foi criado com êxito, mostrado na seguinte captura de tela e, em seguida, clique em **Fechar** para sair do assistente.
   
    ![Assistente de Novo Grupo de Disponibilidade](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665531.gif)
10. No **Pesquisador de Objetos**, expanda **Alta Disponibilidade AlwaysOn** e, em seguida, expanda **Grupos de Disponibilidade**. Agora você poderá ver o novo grupo de disponibilidade neste contêiner. Clique com o botão direito do mouse em **AG1 (Primário)** e, em seguida, clique em **Mostrar Painel**.
    
     ![Mostrar Painel do Grupo de Disponibilidade](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665532.gif)
11. O **Painel do AlwaysOn** deve ser semelhante a da seguinte captura de tela. Você pode ver as réplicas, o modo de failover de cada réplica e o estado de sincronização.
    
     ![Painel do Grupo de Disponibilidade](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665533.gif)
12. Retorne ao **Gerenciador do Servidor**, clique em **Ferramentas** e abra o **Gerenciador de Cluster de Failover**.
13. Expanda **Cluster1.corp.contoso.com** e depois expanda **Serviços e Aplicativos**. Selecione **Funções** e observe se a função do grupo de disponibilidade **AG1** foi criada. Observe que o AG1 não tem nenhum endereço IP pelo qual os clientes do banco de dados podem se conectar ao grupo de disponibilidade porque você não configurou um ouvinte. Você pode se conectar diretamente ao nó principal para operações de leitura/gravação e o nó secundário para consultas somente leitura.
    
     ![AG no Gerenciador de Cluster de Failover](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665534.gif)

> [!WARNING]
> Não tente fazer failover do grupo de disponibilidade no Gerenciador de Cluster de Failover. Todas as operações de failover devem ser executadas no **Painel do AlwaysOn** no SQL Server Management Studio. Para obter mais informações, consulte [Restrições do uso do Gerenciador de Cluster de Failover com Grupos de Disponibilidade](https://msdn.microsoft.com/library/ff929171.aspx).
> 
> 

## <a name="next-steps"></a>Próximas etapas
Agora você implementou com êxito o SQL Server AlwaysOn criando um grupo de disponibilidade no Azure. Para configurar um ouvinte para este grupo de disponibilidade, veja [Configurar um ouvinte de ILB para grupos de disponibilidade Always On no Azure](../classic/ps-sql-int-listener.md).

Para obter outras informações sobre como usar o SQL Server no Azure, veja [SQL Server em Máquinas Virtuais do Azure](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

