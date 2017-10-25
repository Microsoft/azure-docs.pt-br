---
title: "Configurar a alta disponibilidade para máquinas virtuais do Azure Resource Manager | Microsoft Docs"
description: "Este tutorial mostra como criar um grupo de disponibilidade Always On com as máquinas virtuais do Azure no modo do Azure Resource Manager."
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: jhubbard
editor: 
tags: azure-resource-manager
ms.assetid: 64e85527-d5c8-40d9-bbe2-13045d25fc68
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/17/2017
ms.author: mikeray
ms.openlocfilehash: d430febee23081b26eee0a68d4beb43228549f52
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="configure-always-on-availability-groups-in-azure-virtual-machines-automatically-resource-manager"></a>Configurar um grupo de disponibilidade AlwaysOn nas máquinas virtuais do Azure automaticamente: Resource Manager

Este tutorial mostra como criar um grupo de disponibilidade do SQL Server que usa máquinas virtuais do Azure Resource Manager. O tutorial usa folhas do Azure para configurar um modelo. É possível examinar as configurações padrão, digitar as configurações necessárias e atualizar as folhas no portal enquanto você acompanha este tutorial.

O tutorial completo cria um grupo de disponibilidade do SQL Server nas máquinas virtuais do Azure incluindo os seguintes elementos:

* Uma rede virtual que tem várias sub-redes, incluindo uma de front-end e uma de back-end
* Dois controladores de domínio que têm um domínio do Active Directory
* Duas máquinas virtuais que executam o SQL Server e são implantadas na sub-rede de back-end e adicionadas ao domínio do Active Directory
* Um cluster de failover de 3 nós com o modelo de quorum Maioria dos Nós
* Um grupo de disponibilidade que tem duas réplicas de confirmação síncrona de um banco de dados de disponibilidade

A ilustração a seguir representa a solução completa.

![Arquitetura de laboratório de teste para grupos de disponibilidade no Azure](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/0-EndstateSample.png)

Todos os recursos desta solução pertencem a um único grupo de recursos.

Antes de iniciar este tutorial, confirme o seguinte:

* Você já tem uma conta do Azure. Se não tiver uma, [inscreva-se para uma conta de avaliação](http://azure.microsoft.com/pricing/free-trial/).
* Você já sabe como usar a GUI para provisionar uma máquina virtual do SQL Server da galeria de máquinas virtuais. Para saber mais, veja [Provisionamento de uma máquina virtual do SQL Server no Azure](virtual-machines-windows-portal-sql-server-provision.md).
* Você já tem uma compreensão sólida dos grupos de disponibilidade. Para obter mais informações, confira [Grupos de disponibilidade Always On (SQL Server)](http://msdn.microsoft.com/library/hh510230.aspx).

> [!NOTE]
> Se você estiver interessado em usar os grupos de disponibilidade com o SharePoint, confira também [Configure SQL Server 2012 Always On Availability Groups for SharePoint 2013](http://technet.microsoft.com/library/jj715261.aspx)(Configurar grupos de disponibilidade AlwaysOn do SQL Server 2012 para o SharePoint 2013).
>
>

Neste tutorial, use o portal do Azure para:

* Escolha o modelo AlwaysOn no portal.
* Examinar as configurações de modelo e atualizar alguns parâmetros de configuração para o seu ambiente.
* Monitorar o Azure enquanto ele cria todo o ambiente.
* Conectar a um controlador de domínio e, em seguida, a um servidor que executa o SQL Server.

[!INCLUDE [availability-group-template](../../../../includes/virtual-machines-windows-portal-sql-alwayson-ag-template.md)]

## <a name="provision-the-cluster-from-the-gallery"></a>Provisionar o cluster da galeria
O Azure fornece uma imagem da galeria para a solução inteira. Para localizar o modelo:

1. Entre no portal do Azure usando sua conta.
2. No portal do Azure, clique em **+ Novo** para abrir a folha **Novo**.
3. Na folha **Novo**, pesquise por **AlwaysOn**.
   ![Localizar o modelo AlwaysOn](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/16-findalwayson.png)
4. Nos resultados da pesquisa, localize **Cluster AlwaysOn do SQL Server**.
   ![Modelo AlwaysOn](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/17-alwaysontemplate.png)
5. Em **Selecionar um modelo de implantação**, escolha **Resource Manager**.

### <a name="basics"></a>Noções básicas
Clique em **Básico** e defina as seguintes configurações:

* **Nome de usuário do administrador** é uma conta de usuário que tem permissões de administrador de domínio e é um membro da função de servidor fixa sysadmin do SQL Server em ambas as instâncias do SQL Server. Neste tutorial, use **DomainAdmin**.
* **Senha** é a senha da conta de administrador de domínio. Use uma senha complexa. Confirme a senha.
* **Assinatura** é a assinatura que será cobrada pelo Azure para executar todos os recursos implantados do grupo de disponibilidade. Se a conta tiver várias assinaturas, você poderá especificar outra assinatura.
* **Grupo de recursos** é o nome do grupo ao qual pertencem todos os recursos do Azure criados por esse modelo. Neste tutorial, use **SQL-HA-RG**. Para saber mais, consulte [Visão geral do Azure Resource Manager](../../../azure-resource-manager/resource-group-overview.md#resource-groups).
* **Localização** é a região do Azure onde o tutorial cria os recursos. Escolha uma região do Azure.

A seguinte captura de tela é uma folha **Básico** preenchida:

![Noções básicas](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/1-basics.png)

Clique em **OK**.

### <a name="domain-and-network-settings"></a>Configurações de rede e de domínio
Esse modelo da galeria do Azure cria um domínio e controladores de domínio. Ele também cria uma rede e duas sub-redes. O modelo não pode criar servidores em um domínio ou rede virtual existente. A próxima etapa define as configurações de rede e de domínio.

Na folha **Configurações de rede e de domínio**, examine os valores predefinidos das configurações de rede e de domínio:

* **Nome de domínio raiz da floresta** é o nome do domínio do Active Directory que hospeda o cluster. Neste tutorial, use **contoso.com**.
* **Nome da Rede Virtual** é o nome de rede da rede virtual do Azure. Para o tutorial, use **autohaVNET**.
* **Domain Controller subnet name (Nome da sub-rede do Controlador de Domínio)** é o nome de uma parte da rede virtual que hospeda o controlador de domínio. Use **subnet-1**. Essa sub-rede usa o prefixo de endereço **10.0.0.0/24**.
* **SQL Server subnet name (Nome da sub-rede do SQL Server)** é o nome da parte da rede virtual que hospeda os servidores que executam o SQL Server e a testemunha de compartilhamento de arquivos. Use **subnet-2**. Essa sub-rede usa o prefixo de endereço **10.0.1.0/26**.

Para saber mais sobre as redes virtuais no Azure, confira [Visão geral da Rede Virtual](../../../virtual-network/virtual-networks-overview.md).  

As **Configurações de rede e de domínio** devem ser parecidas com a seguinte captura de tela:

![Configurações de rede e de domínio](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/2-domain.png)

Se necessário, você pode alterar esses valores. Para este tutorial, use os valores predefinidos.

Examine as configurações e clique em **OK**.

### <a name="availability-group-settings"></a>Configurações de grupo de disponibilidade
Em **Configurações de grupo de disponibilidade**, examine os valores predefinidos para o grupo de disponibilidade e o ouvinte.

* **Nome do grupo de disponibilidade** é o nome do recurso clusterizado do grupo de disponibilidade. Neste tutorial, use **Contoso-ag**.
* **Nome do ouvinte do grupo de disponibilidade** é usado pelo cluster e pelo balanceador de carga interno. Clientes que se conectam ao SQL Server podem usar esse nome para se conectar à réplica do banco de dados apropriada. Neste tutorial, use **Contoso-listener**.
* **Porta do ouvinte do grupo de disponibilidade** especifica a porta TCP do ouvinte do SQL Server. Neste tutorial, use a porta padrão **1433**.

Se necessário, você pode alterar esses valores. Para este tutorial, use os valores predefinidos.  

![configurações de grupo de disponibilidade](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/3-availabilitygroup.png)

Clique em **OK**.

### <a name="virtual-machine-size-storage-settings"></a>Tamanho da máquina virtual, configurações de armazenamento
Em **Tamanho da VM, configurações de armazenamento**, escolha um tamanho da máquina virtual do SQL Server e examine as outras configurações.

* **SQL Server virtual machine size (Tamanho da máquina virtual do SQL Server)** é o tamanho de ambas as máquinas virtuais que executam o SQL Server. Escolha um tamanho de máquina virtual apropriado para sua carga de trabalho. Se você estiver criando esse ambiente para o tutorial, use **DS2**. Para cargas de trabalho de produção, escolha um tamanho de máquina virtual que possa dar suporte à carga de trabalho. Várias cargas de trabalho de produção exigem **DS4** ou maior. O modelo cria duas máquinas virtuais desse tamanho e instala o SQL Server em cada uma delas. Para obter mais informações, confira [Tamanhos das máquinas virtuais](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

> [!NOTE]
> O Azure instala a Enterprise Edition do SQL Server. O custo depende da edição e do tamanho da máquina virtual. Para obter informações detalhadas sobre os custos atuais, confira [Máquinas virtuais - preço](http://azure.microsoft.com/pricing/details/virtual-machines/#Sql).
>
>

* **Domain controller virtual machine size (Tamanho de máquina virtual do controlador de domínio)** é o tamanho da máquina virtual para os controladores de domínio. Neste tutorial, use **D2**.
* **File Share Witness virtual machine size (Tamanho da máquina virtual da testemunha de compartilhamento de arquivos)** é o tamanho da máquina virtual para a testemunha de compartilhamento de arquivos. Neste tutorial, use **A1**.
* **SQL Storage account (Conta de armazenamento SQL)** é o nome da conta de armazenamento que mantém os dados do SQL Server e os discos do sistema operacional. Neste tutorial, use **alwaysonsql01**.
* **DC Storage account (Conta de armazenamento do controlador de domínio)** é o nome da conta de armazenamento para os controladores de domínio. Neste tutorial, use **alwaysondc01**.
* **SQL Server data disk size (Tamanho do disco de dados do SQL Server)** em TB é o tamanho do disco de dados do SQL Server em TB. Especifique um número de 1 a 4. Neste tutorial, use **1**.
* **Storage optimization (Otimização de armazenamento)** define as configurações de armazenamento específicas para as máquinas virtuais do SQL Server com base no tipo de carga de trabalho. Todas as máquinas virtuais do SQL Server neste cenário usam Armazenamento Premium com o cache de host de disco do Azure definido como somente leitura. Além disso, você pode otimizar as configurações do SQL Server para a carga de trabalho escolhendo uma destas três configurações:

  * **Carga de trabalho geral** não define nenhuma configuração específica.
  * **Processamento transacional** define os sinalizadores de rastreamento 1117 e 1118.
  * **Data warehousing** define os sinalizadores de rastreamento 1117 e 610.

Neste tutorial, use a **Carga de trabalho geral**.

![Configurações de armazenamento de tamanho da VM](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/4-vm.png)

Examine as configurações e clique em **OK**.

#### <a name="a-note-about-storage"></a>Uma observação sobre armazenamento
As otimizações adicionais dependem do tamanho dos discos de dados do SQL Server. Para cada terabyte de disco de dados, o Azure adiciona mais 1 TB de Armazenamento Premium. Quando um servidor requer 2 TB ou mais, o modelo cria um pool de armazenamento em cada servidor máquina virtual do SQL Server. Um pool de armazenamento é uma forma de virtualização de armazenamento em que vários discos estão configurados para fornecer maior capacidade, flexibilidade e desempenho.  Em seguida, o modelo cria um espaço de armazenamento no pool de armazenamento e apresenta um único disco de dados para o sistema operacional. O modelo designa o disco como o disco de dados do SQL Server. O modelo ajusta o pool de armazenamento para o SQL Server usando as seguintes configurações:

* Tamanho de faixa é a configuração de intercalação do disco virtual. As cargas de trabalho transacionais usam 64 KB. As cargas de trabalho do data warehouse usam 256 KB.
* A resiliência é simples (sem resiliência).

> [!NOTE]
> O armazenamento Premium do Azure é redundante localmente e mantém três cópias dos dados em uma única região; portanto, não é necessário ter resiliência adicional no pool de armazenamento.
>
>

* A contagem de colunas é igual ao número de discos no pool de armazenamento.

Para saber mais sobre o espaço de armazenamento e os pools de armazenamento, veja:

* [Visão geral de Espaços de Armazenamento](http://technet.microsoft.com/library/hh831739.aspx)
* [Backup do Windows Server e pools de armazenamento](http://technet.microsoft.com/library/dn390929.aspx)

Para saber mais sobre as práticas recomendadas de configuração do SQL Server, confira [Práticas recomendadas relacionadas ao desempenho para o SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-performance.md).

### <a name="sql-server-settings"></a>Configurações do SQL Server
Em **Configurações do SQL Server** , examine e modifique o prefixo do nome da máquina virtual do SQL Server, a versão do SQL Server, a conta do serviço e senha do SQL Server e a agenda de manutenção de aplicação de patch automática do SQL.

* **SQL Server Name Prefix (Prefixo do Nome do SQL Server)** é usado para criar um nome para cada máquina virtual do SQL Server. Neste tutorial, use **sqlserver**. O modelo nomeia as máquinas virtuais do SQL Server *sqlserver-0* e *sqlserver-1*.
* **Versão do SQL Server** é a versão do SQL Server. Neste tutorial, use **SQL Server 2014**. Você também pode escolher **SQL Server 2012** ou **SQL Server 2016**.
* **SQL Server service account user name (Nome de usuário da conta de serviço do SQL Server)** é o nome da conta de domínio para o serviço do SQL Server. Neste tutorial, use **sqlservice**.
* **Senha** é a senha para a conta de serviço do SQL Server.  Use uma senha complexa. Confirme a senha.
* **Agendamento de manutenção de Aplicação de Patch Automática do SQL** identifica o dia da semana em que o Azure aplica o patch automaticamente nos SQL Servers. Neste tutorial, digite **Domingo**.
* **Hora de início da manutenção de Aplicação de Patch Automática do SQL** é a hora do dia da região do Azure em que a aplicação de patch automática é iniciada.

> [!NOTE]
> A janela de aplicação de patches para cada máquina virtual é alterada em uma hora. Apenas uma máquina virtual é corrigida por vez para evitar a interrupção dos serviços.
>
>

![Configurações do SQL Server](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/5-sql.png)

Examine as configurações e clique em **OK**.

### <a name="summary"></a>Resumo
Na página Resumo do Azure, valide as configurações. Você também pode baixar o modelo. Examine o resumo. Clique em **OK**.

### <a name="buy"></a>Comprar
Essa folha final contém os **Termos de uso** e a **Política de Privacidade**. Examine essas informações. Quando estiver pronto para que o Azure comece a criar as máquinas virtuais e todos os outros recursos necessários para o grupo de disponibilidade, clique em **Criar**.

O portal do Azure cria o grupo de recursos e todos os recursos.

## <a name="monitor-deployment"></a>Monitorar implantação
Monitore o progresso da implantação no portal do Azure. Um ícone que representa a implantação está fixado automaticamente ao painel do portal do Azure.

![Painel do Azure](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups/11-deploydashboard.png)

## <a name="connect-to-sql-server"></a>Conectar-se ao SQL Server
As novas instâncias do SQL Server estão em execução em máquinas virtuais que têm endereços IP conectados à Internet. Você pode utilizar a área de trabalho remota (RDP) diretamente para cada máquina virtual do SQL Server.

Para usar o RDP no SQL Server, siga estas etapas:

1. No painel do portal do Azure, verifique se a implantação foi bem-sucedida.
2. Clique em **Recursos**.
3. Na folha **Recursos**, clique em **sqlserver-0**, que é o nome do computador de uma das máquinas virtuais que está executando o SQL Server.
4. Na folha de **sqlserver-0**, clique em **Conectar**. O navegador pergunta se você deseja abrir ou salvar o objeto de conexão remota. Clique em **Abrir**.
5. **Conexão de Área de Trabalho Remota** pode avisar você de que o distribuidor dessa conexão remota não pode ser identificado. Clique em **Conectar**.
6. A segurança do Windows solicita que você insira suas credenciais para se conectar ao endereço IP do controlador de domínio primário. Clique em **Usar outra conta**. Em **Nome de usuário**, digite **contoso\DomainAdmin**. Você configurou essa conta quando definiu o nome de usuário administrador no modelo. Use a senha complexa que você escolheu quando configurou o modelo.
7. **Área de Trabalho Remota** pode avisar você de que o computador remoto não pôde ser autenticado devido a problemas com o certificado de segurança. Ela mostra o nome do certificado de segurança. Se você seguiu o tutorial, o nome será **sqlserver-0.contoso.com**. Clique em **Sim**.

Agora você está conectado à máquina virtual do SQL Server com protocolo RDP. É possível abrir o SQL Server Management Studio, conectar-se à instância padrão do SQL Server e verificar se o grupo de disponibilidade está configurado.
