---
title: Grupos de disponibilidade do SQL Server - Máquinas Virtuais do Azure - Pré-requisitos | Microsoft Docs
description: Este tutorial mostra como configurar os pré-requisitos para a criação de um grupo de disponibilidade Always On do SQL Server nas VMs do Azure.
services: virtual-machines
documentationCenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: c492db4c-3faa-4645-849f-5a1a663be55a
ms.service: virtual-machines-sql
ms.devlang: na
ms.custom: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/29/2018
ms.author: mikeray
ms.openlocfilehash: 1d0f3bfa03eb4bafdd10222e28782c318848b7f7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60592168"
---
# <a name="complete-the-prerequisites-for-creating-always-on-availability-groups-on-azure-virtual-machines"></a>Conclua os pré-requisitos para a criação de grupos de disponibilidade AlwaysOn em máquinas virtuais do Azure

Este tutorial mostra como concluir os pré-requisitos para criar um [grupo de disponibilidade Always On do SQL Server nas máquinas virtuais (VMs) do Azure](virtual-machines-windows-portal-sql-availability-group-tutorial.md). Quando você tiver concluído os pré-requisitos, terá um controlador de domínio, duas VMs do SQL Server e um servidor testemunha em um único grupo de recursos.

**Tempo estimado**: Pode levar algumas horas para concluir os pré-requisitos. Grande parte desse tempo é gasto na criação de máquinas virtuais.

O diagrama a seguir ilustra o que você cria no tutorial.

![Grupo de disponibilidade](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/00-EndstateSampleNoELB.png)

## <a name="review-availability-group-documentation"></a>Leia a documentação do grupo de disponibilidade

Este tutorial supõe que você tem uma compreensão básica dos grupos de disponibilidade Always On do SQL Server. Se você não estiver familiarizado com essa tecnologia, veja [Visão geral dos Grupos de Disponibilidade AlwaysOn (SQL Server)](https://msdn.microsoft.com/library/ff877884.aspx).


## <a name="create-an-azure-account"></a>Criar uma conta do Azure
Você precisa de uma conta do Azure. Você pode [abrir uma conta gratuita do Azure](https://signup.azure.com/signup?offer=ms-azr-0044p&appId=102&ref=azureplat-generic&redirectURL=https:%2F%2Fazure.microsoft.com%2Fget-started%2Fwelcome-to-azure%2F&correlationId=24f9d452-1909-40d7-b609-2245aa7351a6&l=en-US) ou [ativar os benefícios de assinante do Visual Studio](https://docs.microsoft.com/visualstudio/subscriptions/subscriber-benefits).

## <a name="create-a-resource-group"></a>Criar um grupo de recursos
1. Entre no [Portal do Azure](https://portal.azure.com).
2. Clique em **+** para criar um novo objeto no portal.

   ![Novo objeto](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/01-portalplus.png)

3. Digite **grupo de recursos** na janela de pesquisa do **Marketplace**.

   ![Grupo de recursos](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/01-resourcegroupsymbol.png)
4. Clique em **Grupo de recursos**.
5. Clique em **Criar**.
6. Na caixa **Nome do grupo de recursos**, digite um nome para o grupo de recursos. Por exemplo, digite **sql-ha-rg**.
7. Se você tiver várias assinaturas do Azure, verifique se a assinatura é aquela na qual você deseja criar o grupo de disponibilidade.
8. Selecione um local. A localização é a região do Azure onde você deseja criar o grupo de disponibilidade. Este artigo compila todos os recursos em um local do Azure.
9. Verifique se a opção **Fixar no painel** está marcada. Essa configuração opcional coloca um atalho para o grupo de recursos no painel do Portal do Azure.

   ![Grupo de recursos](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/01-resourcegroup.png)

10. Clique em **Criar** para criar o grupo de recursos.

O Azure cria o grupo de recursos e fixa um atalho para esse grupo de recursos no portal.

## <a name="create-the-network-and-subnets"></a>Criar a rede e as sub-redes
A próxima etapa é criar as redes e sub-redes no grupo de recursos do Azure.

A solução usa uma rede virtual com duas sub-redes. A [Visão geral da rede virtual](../../../virtual-network/virtual-networks-overview.md) fornece mais informações sobre as redes no Azure.

Para criar a rede virtual:

1. No Portal do Azure no seu grupo de recursos, clique em **+ Adicionar**. 

   ![Novo item](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/02-newiteminrg.png)
2. Pesquise pela **rede virtual**.

     ![Pesquisar pela rede virtual](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/04-findvirtualnetwork.png)
3. Clique em **Rede virtual**.
4. Na **Rede virtual**, clique no modelo de implantação **Gerenciador de Recursos** e depois em **Criar**.

    A tabela a seguir mostra as configurações para a rede virtual:

   | **Campo** | Value |
   | --- | --- |
   | **Nome** |autoHAVNET |
   | **Espaço de endereço** |10.33.0.0/24 |
   | **Nome da sub-rede** |Administrador |
   | **Intervalo de endereços da sub-rede** |10.33.0.0/29 |
   | **Assinatura** |Especifique a assinatura que você pretende usar. **Assinatura** estará em branco se você tiver apenas uma assinatura. |
   | **Grupo de recursos** |Escolha **Usar existente** e escolha o nome do grupo de recursos. |
   | **Localidade** |Especifique a localização do Azure. |

   Observe que o espaço de endereço e o intervalo de endereços da sub-rede podem ser diferentes daqueles na tabela. Dependendo da sua assinatura, o portal sugere um espaço de endereço disponível e o intervalo de endereços de sub-rede correspondente. Se não houver espaço de endereço disponível suficiente, use uma assinatura diferente.

   O exemplo usa o nome de sub-rede **Admin**. Esta sub-rede destina-se aos controladores de domínio.

5. Clique em **Criar**.

   ![Configurar a rede virtual](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/06-configurevirtualnetwork.png)

O Azure faz com que você retorne ao painel do portal e lhe notifica quando a nova rede é criada.

### <a name="create-a-second-subnet"></a>Criar uma segunda sub-rede
A nova rede virtual tem uma sub-rede, chamada **Admin**. Os controladores de domínio usam essa sub-rede. As VMs do SQL Server usam uma segunda sub-rede chamada **SQL**. Para configurar essa sub-rede:

1. No seu painel, clique no grupo de recursos que você criou, **SQL-HA-RG**. Localize a rede no grupo de recursos em **Recursos**.

    Se **SQL-HA-RG** não estiver visível, encontre-o clicando em **Grupos de Recursos** e filtrando pelo nome do grupo de recursos.
2. Clique em **autoHAVNET** na lista de recursos. 
3. Na rede virtual **autoHAVNET**, em **Configurações**, selecione **Sub-redes**.

    Observe a sub-rede que você já criou.

   ![Configurar a rede virtual](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/07-addsubnet.png)
5. Crie uma segunda sub-rede. Clique em **+ Subnet (+ Sub-rede)**.
6. Em **Adicionar sub-rede**, configure a sub-rede digitando **sqlsubnet** em **Nome**. O Azure especifica automaticamente um **Intervalo de endereços**válido. Verifique se este intervalo de endereços tem pelo menos 10 endereços nele. Em um ambiente de produção, você poderá exigir mais endereços.
7. Clique em **OK**.

    ![Configurar a rede virtual](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/08-configuresubnet.png)

A tabela a seguir resume as definições de configuração de rede:

| **Campo** | Value |
| --- | --- |
| **Nome** |**autoHAVNET** |
| **Espaço de endereço** |Este valor depende dos espaços de endereço disponíveis em sua assinatura. Um valor típico é 10.0.0.0/16. |
| **Nome da sub-rede** |**admin** |
| **Intervalo de endereços da sub-rede** |Este valor depende dos intervalos de endereço disponíveis em sua assinatura. Um valor típico é 10.0.0.0/24. |
| **Nome da sub-rede** |**sqlsubnet** |
| **Intervalo de endereços da sub-rede** |Este valor depende dos intervalos de endereço disponíveis em sua assinatura. Um valor típico é 10.0.1.0/24. |
| **Assinatura** |Especifique a assinatura que você pretende usar. |
| **Grupo de recursos** |**SQL-HA-RG** |
| **Localidade** |Especifique o mesmo local que você escolheu para o grupo de recursos. |

## <a name="create-availability-sets"></a>Criar conjuntos de disponibilidade

Antes de criar máquinas virtuais, você precisará criar conjuntos de disponibilidade. Os conjuntos de disponibilidade reduzem o tempo de inatividade para eventos de manutenção planejados ou não. Um conjunto de disponibilidade do Azure é um grupo lógico de recursos que o Azure coloca em domínios de falha física e em domínios de atualização. Um domínio de falha garante que os membros do conjunto de disponibilidade terão recursos de energia e rede separados. Um domínio de atualização garante que os membros do conjunto de disponibilidade não serão desativados para manutenção ao mesmo tempo. Para saber mais, veja [Gerenciar a disponibilidade de máquinas virtuais](../manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Você precisará de dois conjuntos de disponibilidade. Um é para os controladores de domínio. A segunda é para as VMs do SQL Server.

Para criar um conjunto de disponibilidade, acesse o grupo de recursos e clique em **Adicionar**. Filtre os resultados digitando **Conjunto de disponibilidade**. Clique em **Conjunto de Disponibilidade** nos resultados e clique em **Criar**.

Configure dois conjuntos de disponibilidade de acordo com os parâmetros na tabela a seguir:

| **Campo** | Conjunto de disponibilidade do controlador de domínio | Conjunto de disponibilidade do SQL Server |
| --- | --- | --- |
| **Nome** |adavailabilityset |sqlavailabilityset |
| **Grupo de recursos** |SQL-HA-RG |SQL-HA-RG |
| **Domínios de falha** |3 |3 |
| **Domínios de atualização** |5 |3 |

Depois de criar os conjuntos de disponibilidade, retorne ao grupo de recursos no Portal do Azure.

## <a name="create-domain-controllers"></a>Criar controladores de domínio
Depois de criar a rede, sub-redes, conjuntos de disponibilidade e um balanceador de carga para a Internet, você estará pronto para criar as máquinas virtuais para os controladores de domínio.

### <a name="create-virtual-machines-for-the-domain-controllers"></a>Criar máquinas virtuais para os controladores de domínio
Para criar e configurar os controladores de domínio, retorne para o grupo de recursos **SQL-HA-RG** .

1. Clique em **Adicionar**. 
2. Digite **Windows Server 2016 Datacenter**.
3. Clique em **Windows Server 2016 Datacenter**. Em **Windows Server 2016 Datacenter**, verifique se o modelo de implantação está definido como **Gerenciador de Recursos** e, em seguida, clique em **Criar**. 

Repita as etapas anteriores para criar duas máquinas virtuais. Nomeie as duas máquinas virtuais:

* ad-primary-dc
* ad-secondary-dc

  > [!NOTE]
  > A máquina virtual **ad-secondary-dc** é opcional para fornecer alta disponibilidade para o Active Directory Domain Services.
  >
  >

A tabela a seguir mostra as configurações para esses dois computadores:

| **Campo** | Value |
| --- | --- |
| **Nome** |Primeiro controlador de domínio: *ad-primary-dc*.</br>Segundo controlador de domínio *ad-secondary-dc*. |
| **Tipo de disco da VM** |SSD |
| **Nome de usuário** |DomainAdmin |
| **Senha** |Contoso!0000 |
| **Assinatura** |*Sua assinatura* |
| **Grupo de recursos** |SQL-HA-RG |
| **Localidade** |*Seu local* |
| **Tamanho** |DS1_V2 |
| **Armazenamento** | **Usar discos gerenciados** - **Sim** |
| **Rede virtual** |autoHAVNET |
| **Sub-rede** |administrador |
| **Endereço IP público** |*Mesmo nome que a VM* |
| **Grupo de segurança de rede** |*Mesmo nome que a VM* |
| **Conjunto de disponibilidade** |adavailabilityset </br>**Domínios de falha**: 2 </br>**Domínio de atualização**: 2|
| **Diagnostics** |Enabled |
| **Conta de armazenamento de diagnóstico** |*Criada automaticamente* |

   >[!IMPORTANT]
   >Você só pode colocar uma VM em um conjunto de disponibilidade ao criá-la. Não é possível alterar o conjunto de disponibilidade após a criação da VM. Consulte [Gerenciar a disponibilidade das máquinas virtuais](../manage-availability.md).

O Azure cria as máquinas virtuais.

Depois que as máquinas virtuais forem criadas, configure o controlador de domínio.

### <a name="configure-the-domain-controller"></a>Configurar o controlador de domínio
Nas etapas a seguir, configure o computador **ad-primary-dc** como um controlador de domínio para corp.contoso.com.

1. No portal, abra o grupo de recursos **SQL-HA-RG** e selecione o computador **ad-primary-dc**. Em **ad-primary-dc**, clique em **Conectar** para abrir um arquivo RDP para acesso à área de trabalho remota.

    ![Conectar-se a uma máquina virtual](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/20-connectrdp.png)
2. Entre com sua conta de administrador (**\DomainAdmin**) e senha (**Contoso!0000**) configuradas.
3. Por padrão, o painel **Gerenciador do Servidor** deve ser exibido.
4. Clique no link **Adicionar funções e recursos** no painel.

    ![Gerenciador de servidores - adicionar funções](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/22-addfeatures.png)
5. Selecione **Avançar** até alcançar a seção **Funções de Servidor**.
6. Selecione **Active Directory Domain Services** e as funções do **Servidor DNS**. Quando for solicitado, acrescente os recursos adicionais que são necessários para essas funções.

   > [!NOTE]
   > Windows avisa que não há nenhum endereço IP estático. Se você estiver testando a configuração, clique em **Continuar**. Nos cenários de produção, defina o endereço IP como estático no Portal do Azure ou [use o PowerShell para definir o endereço IP estático do computador do controlador de domínio](../../../virtual-network/virtual-networks-reserved-private-ip.md).
   >
   >

    ![Caixa de diálogo Adicionar Funções](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/23-addroles.png)
7. Clique em **Avançar** até alcançar a seção **Confirmação**. Marque a caixa de seleção **Reiniciar cada servidor de destino automaticamente, se necessário** .
8. Clique em **Instalar**.
9. Depois que os recursos forem instalados, retorne para o painel **Gerenciador do Servidor** .
10. Selecione a nova opção **AD DS** no painel esquerdo.
11. Clique no link **Mais** na barra de aviso amarela.

    ![Caixa de diálogo do AD DS na VM do servidor DNS](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/24-addsmore.png)
12. Na coluna **Ação** da caixa de diálogo **Todos os Detalhes de Tarefa do Servidor**, clique em **Promover este servidor a um controlador de domínio**.
13. No **Assistente de Configuração dos Serviços de Domínio do Active Directory**, use os seguintes valores:

    | **Página** | Configuração |
    | --- | --- |
    | **Configuração de Implantação** |**Adicionar uma nova floresta**<br/> **Nome do domínio raiz** = corp.contoso.com |
    | **Opções de Controlador de Domínio** |**Senha DSRM** = Contoso!0000<br/>**Confirmar Senha** = Contoso!0000 |
14. Clique em **Avançar** para percorrer as outras páginas do assistente. Na página **Verificação de pré-requisitos**, verifique se a seguinte mensagem é exibida: **Todas as verificações de pré-requisitos passaram com êxito**. Você pode examinar todas as mensagens de aviso aplicáveis, porém é possível continuar com a instalação.
15. Clique em **Instalar**. A máquina virtual **ad-primary-dc** é reinicializada automaticamente.

### <a name="note-the-ip-address-of-the-primary-domain-controller"></a>Anote o endereço IP do controlador de domínio primário

Use o controlador de domínio primário para DNS. Anote endereço IP do controlador de domínio primário.

Uma maneira de obter o endereço IP do controlador de domínio primário é por meio do Portal do Azure.

1. No Portal do Azure, abra o grupo de recursos.

2. Clique no controlador de domínio primário.

3. No controlador de domínio primário, clique em **Interfaces de rede**.

![Interfaces de rede](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/25-primarydcip.png)

Anote o endereço IP privado para este servidor.

### <a name="configure-the-virtual-network-dns"></a>Configurar o DNS da rede virtual
Depois de criar o primeiro controlador de domínio e habilitar o DNS no primeiro servidor, configure a rede virtual para usar esse servidor para DNS.

1. No Portal do Azure, clique na rede virtual.

2. Em **Configurações**, clique em **Servidor DNS**.

3. Clique em **Personalizado** e digite o endereço IP privado do controlador de domínio primário.

4. Clique em **Salvar**.

### <a name="configure-the-second-domain-controller"></a>Configurar o segundo controlador de domínio
Após a reinicialização do controlador de domínio primário, você pode configurar o segundo controlador de domínio. Esta etapa opcional é para alta disponibilidade. Siga estas etapas para configurar o segundo controlador de domínio:

1. No portal, abra o grupo de recursos **SQL-HA-RG** e selecione o computador **ad-secondary-dc**. Em **ad-secondary-dc**, clique em **Conectar** para abrir um arquivo RDP para acesso à área de trabalho remota.
2. Entre na VM usando sua conta de administrador (**BUILTIN\DomainAdmin**) e senha (**Contoso!0000**) configuradas.
3. Altere o endereço do servidor DNS preferencial para o endereço do controlador de domínio.
4. Em **Central de Rede e Compartilhamento**, clique no adaptador de rede.
   ![Interface de rede](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/26-networkinterface.png)

5. Clique em **Propriedades**.
6. Clique em **Protocolo IP versão 4 (TCP/IPv4)** e clique em **Propriedades**.
7. Selecione **Usar os seguintes endereços de servidor DNS** e especifique o endereço do controlador de domínio primário no **Servidor DNS preferencial**.
8. Clique em **OK** e em **Fechar** para confirmar as alterações. Agora é possível ingressar a VM em **corp.contoso.com**.

   >[!IMPORTANT]
   >Se você perder a conexão com a Área de Trabalho Remota após alterar a configuração do DNS, acesse o Portal do Azure e reinicie a máquina virtual.

9. Na Área de Trabalho Remota para o controlador de domínio secundário, abra **Painel do Gerenciador do Servidor**.
10. Clique no link **Adicionar funções e recursos** no painel.

    ![Gerenciador de servidores - adicionar funções](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/22-addfeatures.png)
11. Selecione **Avançar** até alcançar a seção **Funções de Servidor**.
12. Selecione **Active Directory Domain Services** e as funções do **Servidor DNS**. Quando for solicitado, acrescente os recursos adicionais que são necessários para essas funções.
13. Depois que os recursos forem instalados, retorne para o painel **Gerenciador do Servidor** .
14. Selecione a nova opção **AD DS** no painel esquerdo.
15. Clique no link **Mais** na barra de aviso amarela.
16. Na coluna **Ação** da caixa de diálogo **Todos os Detalhes de Tarefa do Servidor**, clique em **Promover este servidor a um controlador de domínio**.
17. Em **Configuração de Implantação**, selecione **Adicionar um controlador de domínio a um domínio existente**.
    ![Configuração de Implantação](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/28-deploymentconfig.png)
18. Clique em **Selecionar**.
19. Conecte-se usando a conta de administrador (**CORP.CONTOSO.COM\domainadmin**) e senha (**Contoso!0000**).
20. Em **Selecione um domínio na floresta**, clique em seu domínio e, em seguida, clique em **OK**.
21. Em **Opções do Controlador de Domínio**, use os valores padrão e defina uma senha de DSRM.

    >[!NOTE]
    >A página **Opções de DNS** poderá avisar que não foi possível criar uma delegação para este servidor DNS. Você pode ignorar esse aviso em ambientes que não são de produção.
22. Clique em **Avançar** até atingir a caixa de diálogo de **Pré-requisitos**. Em seguida, clique em **Instalar**.

Depois que o servidor concluir as alterações de configuração, reinicie-o.

### <a name="add-the-private-ip-address-to-the-second-domain-controller-to-the-vpn-dns-server"></a>Adicione o Endereço IP Privado ao segundo controlador de domínio ao Servidor DNS de VPN

No Portal do Azure, na rede virtual, altere o Servidor DNS para incluir o endereço IP do controlador de domínio secundário. Essa configuração permite a redundância de serviço DNS.

### <a name="DomainAccounts"></a> Configurar as contas de domínio

Nas próximas etapas, você configurará as contas do Active Directory. A tabela abaixo mostra as contas:

| |Conta de instalação<br/> |sqlserver-0 <br/>Conta do SQL Server e do serviço SQL Agent |sqlserver-1<br/>Conta do SQL Server e do serviço SQL Agent
| --- | --- | --- | ---
|**Nome** |Instalar |SQLSvc1 | SQLSvc2
|**SamAccountName do usuário** |Instalar |SQLSvc1 | SQLSvc2

Use as etapas a seguir para criar cada conta.

1. Entre no computador **ad-primary-dc** .
2. No **Gerenciador do Servidor**, selecione **Ferramentas** e clique em **Centro Administrativo do Active Directory**.   
3. Selecione **corp (local)** no painel à esquerda.
4. No painel **Tarefas** à direita, selecione **Novo** e clique em **Usuário**.
   ![Centro Administrativo do Active Directory](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/29-addcnewuser.png)

   >[!TIP]
   >Defina uma senha complexa para cada conta.<br/> Para ambientes de não produção, defina a conta de usuário para nunca expirar.

5. Clique em **OK** para criar o usuário.
6. Repita as etapas anteriores para cada uma das três contas.

### <a name="grant-the-required-permissions-to-the-installation-account"></a>Conceda as permissões necessárias para a conta de instalação
1. No **Centro Administrativo do Active Directory**, selecione **corp (local)** no painel esquerdo. Em seguida, no painel **Tarefas** à direita, clique em **Propriedades**.

    ![Propriedades do usuário CORP](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/31-addcproperties.png)
2. Selecione **Extensões** e clique no botão **Avançado** na guia **Segurança**.
3. Na caixa de diálogo **Configurações de Segurança Avançada para corp**  clique em **Adicionar**.
4. Clique em **Selecionar uma entidade**, procure **CORP\Install**e, em seguida, clique em **OK**.
5. Marque a caixa de diálogo **Ler todas as propriedades**.

6. Marque a caixa de seleção **Criar objetos de computador**.

     ![Permissões de usuário corp](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/33-addpermissions.png)
7. Clique em **OK** e em **OK** novamente. Feche a janela de propriedades **corporativas**.

Agora que você concluiu a configuração do Active Directory e dos objetos de usuário, crie duas VMs do SQL Server, uma VM de servidor testemunha. Em seguida, faça com que todas as três ingressem no domínio.

## <a name="create-sql-server-vms"></a>Criar VMs do SQL Server

Crie três máquinas virtuais adicionais. A solução requer duas máquinas virtuais com instâncias do SQL Server. Uma terceira máquina virtual funcionará como uma testemunha. O Windows Server 2016 pode usar uma [testemunha em nuvem](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness), no entanto, para manter a consistência com sistemas operacionais anteriores, este documento usa uma máquina virtual para uma testemunha.  

Antes de prosseguir, considere as seguintes decisões design.

* **Armazenamento – Azure Managed Disks**

   Para o armazenamento da máquina virtual, use o Azure Managed Disks. A Microsoft recomenda Managed Disks para máquinas virtuais do SQL Server. O Managed Disks lida com o armazenamento nos bastidores. Além disso, quando as máquinas virtuais com Managed Disks estão no mesmo conjunto de disponibilidade, o Azure distribui os recursos de armazenamento para fornecer a redundância apropriada. Para obter informações adicionais, consulte [Visão geral do Azure Managed Disks](../managed-disks-overview.md). Para obter informações específicas sobre discos gerenciados em um conjunto de disponibilidade, consulte [Usar discos gerenciados para VMs no conjunto de disponibilidade](../manage-availability.md#use-managed-disks-for-vms-in-an-availability-set).

* **Rede – Endereços IP privados em produção**

   Para as máquinas virtuais, este tutorial usa endereços IP públicos. Um endereço IP público permite a conexão remota diretamente com a máquina virtual pela Internet – isso facilita as etapas de configuração. Em ambientes de produção, a Microsoft recomenda somente endereços IP privados para reduzir a superfície de vulnerabilidade do recurso da VM da instância do SQL Server.

### <a name="create-and-configure-the-sql-server-vms"></a>Criar e configurar as VMs do SQL Server
Em seguida, crie três VMs: duas VMs do SQL Server e uma VM para um nó de cluster adicional. Para criar cada uma das VMs, volte para o grupo de recursos **SQL-HA-RG**, clique em **Adicionar**, pesquise pelo item da galeria apropriado, clique em **Máquina Virtual** e, em seguida, clique em **Da Galeria**. Use as informações na tabela a seguir para ajudar a criar as VMs:


| Página | VM1 | VM2 | VM3 |
| --- | --- | --- | --- |
| Selecione o item da galeria apropriado |**Windows Server 2016 Datacenter** |**SQL Server 2016 SP1 Enterprise no Windows Server 2016** |**SQL Server 2016 SP1 Enterprise no Windows Server 2016** |
|  **Noções básicas** |**Nome** = cluster-fsw<br/>**Nome de usuário** = DomainAdmin<br/>**Senha** = Contoso!0000<br/>**Assinatura** = Sua assinatura<br/>**Grupo de recursos** = SQL-HA-RG<br/>**Local** = Seu local do Azure |**Nome** = sqlserver-0<br/>**Nome de usuário** = DomainAdmin<br/>**Senha** = Contoso!0000<br/>**Assinatura** = Sua assinatura<br/>**Grupo de recursos** = SQL-HA-RG<br/>**Local** = Seu local do Azure |**Nome** = sqlserver-1<br/>**Nome de usuário** = DomainAdmin<br/>**Senha** = Contoso!0000<br/>**Assinatura** = Sua assinatura<br/>**Grupo de recursos** = SQL-HA-RG<br/>**Local** = Seu local do Azure |
|  **Tamanho** |**TAMANHO** = DS1\_V2 (1 vCPU, 3,5 GB) |**TAMANHO** = DS2\_V2 (2 vCPUs, 7 GB)</br>O tamanho deve dar suporte ao armazenamento SSD (Suporte a disco Premium. )) |**TAMANHO** = DS2\_V2 (2 vCPUs, 7 GB) |
|  **Configurações** |**Armazenamento**: Usar discos gerenciados.<br/>**Rede virtual** = autoHAVNET<br/>**Sub-rede** = sqlsubnet(10.1.1.0/24)<br/>**Endereço IP público** gerado automaticamente.<br/>**Grupo de segurança de rede** = Nenhum<br/>**Monitorando Diagnóstico** = Habilitado<br/>**Conta de armazenamento de diagnóstico** = Use uma conta de armazenamento gerada automaticamente<br/>**Conjunto de disponibilidade** = sqlAvailabilitySet<br/> |**Armazenamento**: Usar discos gerenciados.<br/>**Rede virtual** = autoHAVNET<br/>**Sub-rede** = sqlsubnet(10.1.1.0/24)<br/>**Endereço IP público** gerado automaticamente.<br/>**Grupo de segurança de rede** = Nenhum<br/>**Monitorando Diagnóstico** = Habilitado<br/>**Conta de armazenamento de diagnóstico** = Use uma conta de armazenamento gerada automaticamente<br/>**Conjunto de disponibilidade** = sqlAvailabilitySet<br/> |**Armazenamento**: Usar discos gerenciados.<br/>**Rede virtual** = autoHAVNET<br/>**Sub-rede** = sqlsubnet(10.1.1.0/24)<br/>**Endereço IP público** gerado automaticamente.<br/>**Grupo de segurança de rede** = Nenhum<br/>**Monitorando Diagnóstico** = Habilitado<br/>**Conta de armazenamento de diagnóstico** = Use uma conta de armazenamento gerada automaticamente<br/>**Conjunto de disponibilidade** = sqlAvailabilitySet<br/> |
|  **Definições do SQL Server** |Não aplicável |**Conectividade SQL** = Particular (em rede virtual)<br/>**Porta** = 1433<br/>**Autenticação SQL** = Desabilitar<br/>**Configuração de armazenamento** = Geral<br/>**Aplicação de patch automatizada** = Domingo às 2:00<br/>**Backup automatizado** = Desabilitado</br>**Integração do Cofre de Chaves do Azure** = Desabilitado |**Conectividade SQL** = Particular (em rede virtual)<br/>**Porta** = 1433<br/>**Autenticação SQL** = Desabilitar<br/>**Configuração de armazenamento** = Geral<br/>**Aplicação de patch automatizada** = Domingo às 2:00<br/>**Backup automatizado** = Desabilitado</br>**Integração do Cofre de Chaves do Azure** = Desabilitado |

<br/>

> [!NOTE]
> Os tamanhos de computador sugeridos aqui servem para testar grupos de disponibilidade em VMs do Azure. Para obter o melhor desempenho em cargas de trabalho de produção, veja as recomendações de configuração e tamanhos de máquina do SQL Server em [Práticas recomendadas relacionadas ao desempenho para o SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-performance.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
>
>

Depois que as três VMs forem totalmente provisionadas, você precisará ingressá-las no domínio **corp.contoso.com** e conceder direitos administrativos CORP\Install às máquinas.

### <a name="joinDomain"></a>Ingressar os servidores no domínio

Agora é possível ingressar as VMs em **corp.contoso.com**. Execute as etapas a seguir para as VMs do SQL Server e para o servidor testemunha do compartilhamento de arquivos:

1. Conecte-se remotamente à máquina virtual com **BUILTIN\DomainAdmin**.
2. Em **Gerenciador do Servidor**, clique em **Servidor Local**.
3. Clique no link **WORKGROUP**.
4. Na seção **Nome do Computador**, clique em **Alterar**.
5. Marque a caixa de seleção **Domínio** e digite **corp.contoso.com** na caixa de texto. Clique em **OK**.
6. Na caixa de diálogo pop-up **Segurança do Windows**, especifique as credenciais para a conta de administrador de domínio padrão (**CORP\DomainAdmin**) e a senha (**Contoso!0000**).
7. Quando a mensagem "Bem-vindo ao domínio corp.contoso.com" for exibida, clique em **OK**.
8. Clique em **Fechar** e em **Reiniciar Agora** na caixa de diálogo pop-up.

### <a name="add-the-corpinstall-user-as-an-administrator-on-each-cluster-vm"></a>Adicione o usuário Corp\Install como um administrador em cada VM de cluster

Depois de reiniciar cada máquina virtual como um membro do domínio, adicione **CORP\Install** como um membro do grupo administradores local.

1. Aguarde até que a VM seja reiniciada e inicialize o arquivo RDP novamente do controlador de domínio primário para entrar no **sqlserver-0** usando a conta **CORP\DomainAdmin**.
   >[!TIP]
   >Verifique se você entrou com a conta de administrador de domínio. Nas etapas anteriores, você estava usando a conta de administrador BUILT IN. Agora que o servidor está no domínio, use a conta de domínio. Na sua sessão de RDP, especifique *DOMÍNIO*\\*nome de usuário*.

2. Em **Gerenciador do Servidor**, selecione **Ferramentas** e clique em **Gerenciamento do Computador**.
3. Na janela **Gerenciamento do Computador**, expanda **Usuários e Grupos Locais** e selecione **Grupos**.
4. Clique duas vezes no grupo **Administradores** .
5. Na caixa de diálogo **Propriedades de Administradores**, clique no botão **Adicionar**.
6. Insira o usuário **CORP\Install** e clique em **OK**.
7. Clique em **OK** para fechar a caixa do diálogo **Propriedades do Administrador**.
8. Repita as etapas anteriores em **sqlserver-1** e **cluster-fsw**.

### <a name="setServiceAccount"></a>Defina as contas de serviço do SQL Server

Em cada VM do SQL Server, defina a conta de serviço do SQL Server. Use as contas que você criou quando configurou as contas de domínio.

1. Abra o **SQL Server Configuration Manager**.
2. Clique com o botão direito do mouse no serviço do SQL Server e em **Propriedades**.
3. Defina a conta e a senha.
4. Repita essas etapas no outra VM do SQL Server.  

Para grupos de disponibilidade do SQL Server, cada VM do SQL Server precisa ser executado como uma conta de domínio.

### <a name="create-a-sign-in-on-each-sql-server-vm-for-the-installation-account"></a>Criar uma entrada em cada VM do SQL Server para a conta de instalação

Use a conta de instalação (CORP\install) para configurar o grupo de disponibilidade. Essa conta precisa ser membro da função de servidor fixa **sysadmin** de cada VM do SQL Server. As seguintes etapas criam uma entrada para a conta de instalação:

1. Conectar-se ao servidor por meio do protocolo RDP usando a conta  *\<MachineName\>\DomainAdmin*.

1. Abra o SQL Server Management Studio e conecte-se ao SQL Server da instância local.

1. Em **Pesquisador de Objetos**, clique em **Segurança**.

1. Clique com botão direito do mouse em **Logons**. Clique em **Novo Logon**.

1. Em **Logon – Novo**, clique em **Pesquisar**.

1. Clique em **Locais**.

1. Insira as credenciais da rede do administrador de domínio.

1. Use a conta de instalação.

1. Defina a entrada como um membro da função de servidor fixa **sysadmin**.

1. Clique em **OK**.

Repita as etapas anteriores na outra VM do SQL Server.

## <a name="add-failover-clustering-features-to-both-sql-server-vms"></a>Adicione os Recursos de Clustering de Failover em ambas as VMs do SQL Server

Para adicionar os Recursos de Clustering de Failover, realize as etapas a seguir em ambas as VMs do SQL Server:

1. Conecte-se à máquina virtual do SQL Server por meio do protocolo RDP usando a conta *CORP\install*. Abra o **Painel do Gerenciador do Servidor**.
2. Clique no link **Adicionar funções e recursos** no painel.

    ![Gerenciador de servidores - adicionar funções](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/22-addfeatures.png)
3. Selecione **Avançar** até alcançar a seção **Recursos do Servidor**.
4. Em **Recursos**, selecione **Clustering de Failover**.
5. Adicione os recursos adicionais necessários.
6. Clique em **Instalar** para adicionar os recursos.

Repita essas etapas na outra VM do SQL Server.

  >[!NOTE]
  > Nesta etapa, junto com, na verdade, ingressar em VMs do SQL Server ao cluster de failover, agora pode ser automatizada com [da CLI de VM do Azure SQL](virtual-machines-windows-sql-availability-group-cli.md) e [modelos de início rápido do Azure](virtual-machines-windows-sql-availability-group-quickstart-template.md).


## <a name="a-nameendpoint-firewall-configure-the-firewall-on-each-sql-server-vm"></a><a name="endpoint-firewall"> Configurar o firewall em cada VM do SQL Server

A solução requer que as seguintes portas TCP estejam abertas no firewall:

- **VM do SQL Server**:<br/>
   Porta 1433 para uma instância padrão do SQL Server.
- **Investigação do Azure Load Balancer:**<br/>
   Qualquer porta disponível. Os exemplos geralmente usam 59999.
- **Ponto de extremidade de espelhamento de banco de dados:** <br/>
   Qualquer porta disponível. Os exemplos geralmente usam 5022.

As portas do firewall precisam ser abertas em ambas as VMs do SQL Server.

O método de abrir as portas depende da solução de firewall usada. A próxima seção explica como abrir portas no Firewall do Windows. Abra as portas necessárias em cada VM do SQL Server.

### <a name="open-a-tcp-port-in-the-firewall"></a>Abrir uma porta TCP em um firewall

1. Na tela **Iniciar** do primeiro SQL Server, inicialize **Firewall do Windows com Segurança Avançada**.
2. No painel esquerdo, selecione **Regras de Entrada**. No painel direito, clique em **Nova Regra**.
3. Para **Tipo de Regra**, escolha **Porta**.
4. Para a porta, especifique **TCP** e digite os números de porta apropriados. Veja os exemplos a seguir:

   ![Firewall do SQL](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/35-tcpports.png)

5. Clique em **Avançar**.
6. Na página **Ação**, mantenha selecionado **Permitir a conexão** e, em seguida, clique em **Avançar**.
7. Na página **Perfil**, aceite as configurações padrão e, em seguida, clique em **Avançar**.
8. Na página **Nome**, especifique um nome de regra, como **Investigação do Azure LB** na caixa de texto **Nome** e clique em **Concluir**.

Repita essas etapas na segunda VM do SQL Server.

## <a name="configure-system-account-permissions"></a>Configurar permissões de conta do sistema

Para criar uma conta para a conta do sistema e conceder as permissões apropriadas, conclua as seguintes etapas em cada instância do SQL Server:

1. Crie uma conta para `[NT AUTHORITY\SYSTEM]` em cada instância do SQL Server. O script a seguir cria essa conta:

   ```sql
   USE [master]
   GO
   CREATE LOGIN [NT AUTHORITY\SYSTEM] FROM WINDOWS WITH DEFAULT_DATABASE=[master]
   GO 
   ```

1. Conceda as seguintes permissões para `[NT AUTHORITY\SYSTEM]` em cada instância do SQL Server:

   - `ALTER ANY AVAILABILITY GROUP`
   - `CONNECT SQL`
   - `VIEW SERVER STATE`

   O script a seguir concede estas permissões:

   ```sql
   GRANT ALTER ANY AVAILABILITY GROUP TO [NT AUTHORITY\SYSTEM]
   GO
   GRANT CONNECT SQL TO [NT AUTHORITY\SYSTEM]
   GO
   GRANT VIEW SERVER STATE TO [NT AUTHORITY\SYSTEM]
   GO 
   ```

## <a name="next-steps"></a>Próximas etapas

* [Criar um grupo de disponibilidade AlwaysOn do SQL Server em máquinas virtuais do Azure](virtual-machines-windows-portal-sql-availability-group-tutorial.md)
