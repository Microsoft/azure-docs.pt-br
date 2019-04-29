---
title: Business Intelligence do SQL Server | Microsoft Docs
description: Este tópico usa recursos criados com o modelo de implantação clássica e descreve os recursos de BI (Business Intelligence) disponíveis para o SQL Server em execução em VMs (máquinas virtuais) do Azure.
services: virtual-machines-windows
documentationcenter: na
author: markingmyname
manager: erikre
editor: monicar
tags: azure-service-management
ms.assetid: c681e7a7-eeda-48aa-bc35-6277f4828244
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/30/2017
ms.author: maghan
ms.openlocfilehash: 29e851772e665b4130ee58b04c264d55bcd54523
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60609471"
---
# <a name="sql-server-business-intelligence-in-azure-virtual-machines"></a>Business Intelligence do SQL Server em máquinas virtuais do Azure
> [!IMPORTANT] 
> O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: [Resource Manager e clássico](../../../azure-resource-manager/resource-manager-deployment-model.md). Este artigo aborda o uso do modelo de implantação Clássica. A Microsoft recomenda que a maioria das implantações novas use o modelo do Gerenciador de Recursos.

A galeria de Máquinas Virtuais do Microsoft Azure inclui imagens com instalações do SQL Server. As edições do SQL Server com suporte nas imagens da galeria são os mesmos arquivos de instalação que você pode instalar em computadores e em máquinas virtuais locais. Este tópico resume os Recursos de BI (Business Intelligence) do SQL Server instalados nas imagens e as etapas de configuração exigidas após o provisionamento de uma máquina virtual. Este tópico também descreve as topologias de implantação com suporte para recursos de BI e as práticas recomendadas.

## <a name="license-considerations"></a>Considerações sobre licenças
Há duas maneiras de licenciar o SQL Server em Máquinas Virtuais do Microsoft Azure:

1. Benefícios de mobilidade da licença que fazem parte do Software Assurance. Para saber mais, consulte [Mobilidade de licenças por meio do Software Assurance no Azure](https://azure.microsoft.com/pricing/license-mobility/).
2. Taxa de pagamento por hora de Máquinas Virtuais do Azure com o SQL Server instalado. Consulte a seção "SQL Server" em [Preços de máquinas virtuais](https://azure.microsoft.com/pricing/details/virtual-machines/#Sql).

Para saber mais sobre licenciamento e taxas atuais, consulte [Perguntas frequentes sobre licenciamento de máquinas virtuais](https://azure.microsoft.com/pricing/licensing-faq/).

## <a name="sql-server-images-available-in-azure-virtual-machine-gallery"></a>Imagens do SQL Server disponíveis na galeria de Máquinas Virtuais do Azure
A galeria de Máquinas Virtuais do Microsoft Azure inclui várias imagens com o Microsoft SQL Server. O software instalado nas imagens da máquina virtual varia de acordo com a versão do sistema operacional e a versão do SQL Server. A lista de imagens disponíveis na galeria de Máquinas Virtuais do Azure é alterada com frequência.

<!--![SQL image in azure VM gallery](./media/virtual-machines-windows-classic-ps-sql-bi/IC741367.png)-->
![Imagem do SQL na Galeria de VM do Azure](./media/virtual-machines-windows-classic-ps-sql-bi/vm-sql-images.png)

![PowerShell](./media/virtual-machines-windows-classic-ps-sql-bi/IC660119.gif)  O script do PowerShell a seguir retorna a lista de imagens do Azure que contêm "SQL-Server" em ImageName:

    # assumes you have already uploaded a management certificate to your Microsoft Azure Subscription. View the thumbprint value from the "Subscriptions" menu in Azure portal.

    $subscriptionID = ""    # REQUIRED: Provide your subscription ID.
    $subscriptionName = "" # REQUIRED: Provide your subscription name.
    $thumbPrint = "" # REQUIRED: Provide your certificate thumbprint.
    $certificate = Get-Item cert:\currentuser\my\$thumbPrint # REQUIRED: If your certificate is in a different store, provide it here.-Ser  store is the one specified with the -ss parameter on MakeCert

    Set-AzureSubscription -SubscriptionName $subscriptionName -Certificate $certificate -SubscriptionID $subscriptionID

    Write-Host -foregroundcolor green "List of available gallery images where imagename contains 2016"
    Write-Host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
    get-azurevmimage | where {$_.ImageName -Like "*SQL-Server-2016*"} | select imagename,category, location, label, description

    Write-Host -foregroundcolor green "List of available gallery images where imagename contains 2014"
    Write-Host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
    get-azurevmimage | where {$_.ImageName -Like "*SQL-Server-2014*"} | select imagename,category, location, label, description

Para saber mais sobre edições e recursos com suporte do SQL Server, consulte o seguinte:

* [Edições do SQL Server](https://www.microsoft.com/sql-server/sql-server-2017-editions)
* [Recursos com suporte das edições do SQL Server 2016](https://msdn.microsoft.com/library/cc645993.aspx)

### <a name="bi-features-installed-on-the-sql-server-virtual-machine-gallery-images"></a>Recursos de BI instalados nas imagens da galeria de Máquinas Virtuais do SQL Server
A tabela a seguir resume os recursos de Business Intelligence instalados nas imagens comuns da galeria da Máquina Virtual do Microsoft Azure para o SQL Server:

* SQL Server 2016 SP1 Enterprise
* SQL Server 2016 SP1 Standard
* SQL Server 2014 SP2 Enterprise
* SQL Server 2014 SP2 Standard
* SQL Server 2012 SP3 Enterprise
* SQL Server 2012 SP3 Standard

| Recurso de BI do SQL Server | Instalado na galeria de imagens | Observações |
| --- | --- | --- |
| **Modo nativo do Reporting Services** |Sim |Instalado, mas exige configuração, incluindo a URL do Gerenciador de relatórios. Consulte a seção [Configurar o Reporting Services](#configure-reporting-services). |
| **Modo SharePoint do Reporting Services** |Não  |A imagem da galeria de Máquinas Virtuais do Microsoft Azure não inclui o SharePoint ou os arquivos de instalação do SharePoint. <sup>1</sup> |
| **Multidimensional e mineração de dados do Analysis Services (OLAP)** |Sim |Instalado e configurado como a instância padrão do Analysis Services |
| **Tabela do Analysis Services** |Não  |Com suporte em imagens do SQL Server 2012, 2014 e 2016, mas não está instalado por padrão. Instale outra instância do Analysis Services. Consulte a seção Instalar outros serviços e recursos do SQL Server neste tópico. |
| **Power Pivot do Analysis Services para SharePoint** |Não  |A imagem da galeria de Máquinas Virtuais do Microsoft Azure não inclui o SharePoint ou os arquivos de instalação do SharePoint. <sup>1</sup> |

<sup>1</sup> Para saber mais sobre o SharePoint e as máquinas virtuais do Azure, consulte Arquiteturas do [Microsoft Azure](https://technet.microsoft.com/library/dn635309.aspx) para SharePoint 2013 e Implantação do [SharePoint em máquinas virtuais do Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=34598).

![PowerShell](./media/virtual-machines-windows-classic-ps-sql-bi/IC660119.gif)  Execute o seguinte comando do PowerShell para obter uma lista de serviços instalados que contêm "SQL" no nome do serviço.

    get-service | Where-Object{ $_.DisplayName -like '*SQL*' } | Select DisplayName, status, servicetype, dependentservices | format-Table -AutoSize

## <a name="general-recommendations-and-best-practices"></a>Recomendações gerais e práticas recomendadas
* O tamanho mínimo recomendado para uma máquina virtual é **A3** ao usar o SQL Server Enterprise Edition. O tamanho de máquina virtual **A4** é recomendado para implantações de BI do SQL Server do Analysis Services e do Reporting Services.
  
    Para saber mais sobre os tamanhos atuais de VM, consulte [Tamanhos de máquina virtual para o Azure](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Uma das práticas recomendadas de gerenciamento de disco é armazenar arquivos de dados, log e backup em outras unidades diferentes de **C**: e **D**:. Por exemplo, crie discos de dados **E**: e **F**:.
  
  * A política de cache da unidade padrão **C**: não é ideal para o trabalho com dados.
  * A unidade **D**: é uma unidade temporária usada principalmente pelo arquivo de paginação. A unidade **D**: não é persistente e não é salva no armazenamento de blob. As tarefas de gerenciamento, como uma alteração no tamanho da máquina virtual, redefinem a unidade **D**:. Recomenda-se **NÃO** usar a unidade **D**: para arquivos de banco de dados, incluindo o tempdb.
    
    Para saber mais sobre como criar e anexar discos, consulte [Como anexar um disco de dados a uma máquina virtual](../classic/attach-disk-classic.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
* Pare ou desinstale os serviços que você não planeja usar. Por exemplo, se a máquina virtual for usada apenas para o Reporting Services, pare ou desinstale o Analysis Services e o SQL Server Integration Services. A imagem a seguir é um exemplo de como os serviços são iniciados por padrão.
  
    ![Serviços do SQL Server](./media/virtual-machines-windows-classic-ps-sql-bi/IC650107.gif)
  
  > [!NOTE]
  > O mecanismo de banco de dados SQL Server é exigido nos cenários de BI com suporte. Em uma topologia de VM de servidor único, o mecanismo de banco de dados deve estar em execução na mesma VM.
  
    Para saber mais, consulte o seguinte:  [Desinstalar o Reporting Services](https://msdn.microsoft.com/library/hh479745.aspx) e [desinstalar uma instância do Analysis Services](https://msdn.microsoft.com/library/ms143687.aspx).
* Verifique se há novas ‘Atualizações importantes’ no **Windows Update** . As imagens de máquina virtual do Microsoft Azure são atualizadas frequentemente; no entanto, é possível que atualizações importantes fiquem disponíveis no **Windows Update** após a última atualização da imagem da VM.

## <a name="example-deployment-topologies"></a>Exemplo de topologias de implantação
A seguir, exemplos de implantações que usam Máquinas Virtuais do Microsoft Azure. As topologias nesses diagramas são apenas algumas das topologias possíveis com recursos de BI do SQL Server e com Máquinas Virtuais do Microsoft Azure.

### <a name="single-virtual-machine"></a>Máquina Virtual individual
Analysis Services, Reporting Services, Mecanismo de Banco de Dados SQL Server e fontes de dados em uma única máquina virtual.

![cenário bi iaas com uma máquina virtual](./media/virtual-machines-windows-classic-ps-sql-bi/IC650108.gif)

### <a name="two-virtual-machines"></a>Duas Máquinas Virtuais
* Analysis Services, Reporting Services e o Mecanismo de Banco de Dados SQL Server em uma única máquina virtual. Essa implantação inclui os bancos de dados do servidor de relatório.
* Fontes de dados em uma segunda VM. A segunda VM inclui o Mecanismo de Banco de Dados SQL Server como uma fonte de dados.

![cenário bi iaas com duas máquinas virtuais](./media/virtual-machines-windows-classic-ps-sql-bi/IC650109.gif)

### <a name="mixed-azure--data-on-azure-sql-database"></a>Azure combinado – dados no banco de dados SQL do Azure
* Analysis Services, Reporting Services e o Mecanismo de Banco de Dados SQL Server em uma única máquina virtual. Essa implantação inclui os bancos de dados do servidor de relatório.
* A fonte de dados é o banco de dados SQL do Azure.

![cenários bi iaas com vm e AzureSQL como fonte de dados](./media/virtual-machines-windows-classic-ps-sql-bi/IC650110.gif)

### <a name="hybrid-data-on-premises"></a>Híbrido – dados locais
* Neste exemplo de implantação, o Analysis Services, o Reporting Services e o Mecanismo de Banco de Dados SQL Server são executados em uma única máquina virtual. A máquina virtual hospeda os bancos de dados do servidor de relatório. A máquina virtual é ingressada em um Domínio local por meio da Rede Virtual do Azure ou por alguma outra solução de túnel VPN.
* A fonte de dados é local.

![fontes de dados locais e da vm de cenários iaas de bi](./media/virtual-machines-windows-classic-ps-sql-bi/IC654384.gif)

## <a name="reporting-services-native-mode-configuration"></a>Configuração do modo Nativo do Reporting Services
A imagem da galeria de máquinas virtuais do SQL Server inclui a instalação do modo Nativo do Reporting Services. No entanto, o servidor de relatório não está configurado. As etapas desta seção configuram o servidor de relatório do Reporting Services. Para obter informações mais detalhadas sobre como configurar o modo Nativo do Reporting Services, consulte [Instalar o servidor de relatório no modo nativo do Reporting Services (SSRS)](https://msdn.microsoft.com/library/ms143711.aspx).

> [!NOTE]
> Para obter conteúdo semelhante que usa scripts do Windows PowerShell para configurar o servidor de relatório, consulte [Usar o PowerShell para criar uma VM do Azure com um servidor de relatório no modo nativo](../classic/ps-sql-report.md).

### <a name="connect-to-the-virtual-machine-and-start-the-reporting-services-configuration-manager"></a>Conectar-se à máquina virtual e iniciar o Gerenciador de Configuração do Reporting Services
Há dois fluxos de trabalho comuns para se conectar a uma máquina virtual do Azure:

* Para se conectar, clique no nome da máquina virtual e clique em **Conectar**. Uma conexão de Área de Trabalho Remota é aberta e o nome do computador é preenchido automaticamente.
  
    ![conectar-se à máquina virtual do azure](./media/virtual-machines-windows-classic-ps-sql-bi/IC650112.gif)
* Conecte-se à máquina virtual com a Conexão de Área de Trabalho Remota do Windows. Na interface de usuário da área de trabalho remota:
  
  1. Digite o **nome do serviço de nuvem** como o nome do computador.
  2. Digite dois-pontos (:) e o número da porta pública configurada para o ponto de extremidade da área de trabalho remota TCP.
     
      Myservice.cloudapp.net:63133
     
      Para obter mais informações, consulte [O que é um serviço de nuvem?](https://azure.microsoft.com/manage/services/cloud-services/what-is-a-cloud-service/).


**Inicie o Gerenciador de Configurações do Reporting Services**

No **Windows Server 2012/2016**:

1. Na tela **Iniciar**, digite **Reporting Services** para ver uma lista de aplicativos.
2. Clique com o botão direito do mouse no **Gerenciador de Configuração do Reporting Services** e clique em **Executar como Administrador**.

No **Windows Server 2008 R2**:

1. Clique em **Iniciar** e em **TODOS os Programas**.
2. Clique em **Microsoft SQL Server 2016**.
3. Clique em **Ferramentas de Configuração**.
4. Clique com o botão direito do mouse no **Gerenciador de Configuração do Reporting Services** e clique em **Executar como Administrador**.

Ou:

1. Clique em **Iniciar**.
2. Na caixa de diálogo **Pesquisar programas e arquivos**, digite **reporting services**. Se a VM estiver executando o Windows Server 2012, digite **reporting services** na tela Iniciar do Windows Server 2012.
3. Clique com o botão direito do mouse no **Gerenciador de Configuração do Reporting Services** e clique em **Executar como Administrador**.
   
    ![pesquisar gerenciador de configuração do ssrs](./media/virtual-machines-windows-classic-ps-sql-bi/IC650113.gif)

### <a name="configure-reporting-services"></a>Configurar o Reporting Services
**Conta de serviço e URL do serviço Web:**

1. Verifique se o **Nome do Servidor** é o nome do servidor local e clique em **Conectar**.
2. Observe o espaço em branco em **Nome do Banco de Dados do Servidor de Relatórios**. O banco de dados será criado quando a configuração for concluída.
3. Verifique se o **Status do Servidor de Relatórios** é **Iniciado**. Se você quiser verificar o serviço no Windows Server Manager, o serviço será o Serviço do Windows **SQL Server Reporting Services** .
4. Clique em **Conta de Serviço** e altere a conta como necessário. Se a máquina virtual for usada em um ambiente não ingressado no domínio, a conta interna do **ReportServer** será suficiente. Para saber mais sobre a conta de serviço, consulte [Conta de Serviço](https://msdn.microsoft.com/library/ms189964.aspx).
5. Clique em **URL do Serviço Web** no painel Esquerdo.
6. Clique em **Aplicar** para configurar os valores padrão.
7. Anote as **URLs do Serviço Web do Servidor de Relatório**. Observe que a porta TCP padrão é a 80 e faz parte da URL. Em uma etapa posterior, você criará um Ponto de Extremidade de Máquina Virtual do Microsoft Azure para a porta.
8. No painel **Resultados** , verifique se as ações foram concluídas com êxito.

**Banco de dados:**

1. Clique em **Banco de Dados** no painel esquerdo.
2. Clique em **Alterar Banco de Dados**.
3. Verifique se **Criar um novo banco de dados do servidor de relatório** está selecionada e clique em Avançar.
4. Verifique o **Nome do Servidor** e clique em **Testar Conexão**.
5. Se o resultado for **Teste de conexão bem-sucedido**, clique em **OK** e em **Avançar**.
6. Observe que o nome do banco de dados é **ReportServer** e o **Modo do Servidor de Relatório** é **Nativo**. Em seguida, clique em **Avançar**.
7. Clique em **Avançar** on the **Credenciais** .
8. Clique em **Avançar** on the **Resumo** .
9. Clique em **Avançar** on the **Progresso e Conclusão** .

**URL do Portal da Web ou URL do Gerenciador de Relatórios para as versões 2012 e 2014:**

1. Clique em **URL do Portal da Web** ou em **URL do Gerenciador de Relatórios** para as versões 2014 e 2012, no painel esquerdo.
2. Clique em **Aplicar**.
3. No painel **Resultados** , verifique se as ações foram concluídas com êxito.
4. Clique em **Sair**.

Para saber mais sobre permissões do servidor de relatório, consulte [Concedendo permissões em um Servidor de Relatório em Modo Nativo](https://msdn.microsoft.com/library/ms156014.aspx).

### <a name="browse-to-the-local-report-manager"></a>Navegue até o Gerenciador de Relatórios local
Para verificar a configuração, navegue até o gerenciador de relatórios na VM.

1. Na VM, inicie o Internet Explorer com privilégios de administrador.
2. Navegue até http: \/ /localhost/relatórios na VM.

### <a name="to-connect-to-remote-web-portal-or-report-manager-for-2014-and-2012"></a>Para conectar-se ao portal da Web Conexão Remota ou para o Gerenciador de Relatórios para as versões 2014 e 2012
Se você quiser se conectar ao portal da Web ou ao Gerenciador de Relatórios para as versões 2014 e 2012, na máquina virtual de um computador remoto, crie um novo Ponto de Extremidade TCP de máquina virtual. Por padrão, o servidor de relatório escuta solicitações HTTP na **porta 80**. Se você configurar as URLs do servidor de relatório para usar uma porta diferente, especifique o número da porta nas instruções a seguir.

1. Criar um ponto de extremidade para a máquina virtual da porta TCP 80 Para saber mais, consulte a seção [Pontos de extremidade de máquina virtual e portas de firewall](#virtual-machine-endpoints-and-firewall-ports) neste documento.
2. Abra a porta 80 no firewall da máquina virtual.
3. Navegue até o portal da Web ou até o gerenciador de relatórios usando o **Nome DNS** da Máquina Virtual do Azure como o nome do servidor na URL. Por exemplo: 
   
    **Servidor de relatório**: http://uebi.cloudapp.net/reportserver  **Portal da Web**: http://uebi.cloudapp.net/reports
   
    [Configurar um firewall para acesso ao servidor de relatório](https://msdn.microsoft.com/library/bb934283.aspx)

### <a name="to-create-and-publish-reports-to-the-azure-virtual-machine"></a>Para criar e publicar relatórios na máquina virtual do Azure
A tabela a seguir resume algumas opções disponíveis para publicação de relatórios existentes de um computador local para o servidor de relatório hospedado na Máquina Virtual do Microsoft Azure:

* **Construtor de Relatórios**: A máquina virtual inclui o clique-uma vez a versão do construtor de relatórios do Microsoft SQL Server para o SQL 2014 e 2012. Para iniciar o Construtor de relatórios pela primeira vez na máquina virtual com o SQL 2016:
  
  1. Inicie o navegador com privilégios administrativos.
  2. Navegue até o portal da Web, na máquina virtual, e selecione o ícone **Baixar** no canto superior direito.
  3. Selecione **Construtor de Relatórios**.
     
     Para saber mais, veja [Iniciar o Construtor de Relatórios](https://msdn.microsoft.com/library/ms159221.aspx).
* **SQL Server Data Tools**: VM:  SQL Server Data Tools está instalado na máquina virtual e pode ser usado para criar **projetos do servidor de relatório** e relatórios na máquina virtual. O SQL Server Data Tools pode publicar os relatórios no servidor de relatório na máquina virtual.
* **SQL Server Data Tools: remoto**:  no computador local, crie um projeto do Reporting Services no SQL Server Data Tools que contenha os relatórios do Reporting Services. Configure o projeto para conectar-se à URL do serviço Web.
  
    ![propriedades de projeto ssdt para projeto SSRS](./media/virtual-machines-windows-classic-ps-sql-bi/IC650114.gif)
* Criar um disco rígido .VHD que contém relatórios e carregue e anexe a unidade.
  
  1. Crie um disco rígido .VHD no computador local com seus relatórios.
  2. Crie e instale um certificado de gerenciamento.
  3. Carregue o arquivo VHD no Azure usando o cmdlet Add-AzureVHD [Criar e carregar um VHD do Windows Server no Azure](../classic/createupload-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
  4. Anexe o disco à máquina virtual.

## <a name="install-other-sql-server-services-and-features"></a>Instalar outros serviços e recursos do SQL Server
Para instalar serviços adicionais do SQL Server, como o Analysis Services no modo de tabela, execute o assistente de instalação do servidor SQL. Os arquivos de instalação estão no disco local da máquina virtual.

1. Clique em **Iniciar** e em **Todos os Programas**.
2. Clique em **Microsoft SQL Server 2016**, em **Microsoft SQL Server 2014** ou em **Microsoft SQL Server 2012** e clique em **Ferramentas de Configuração**.
3. Clique em **Central de Instalação do SQL Server**.

Ou execute C:\SQLServer_13.0_full\setup.exe, C:\SQLServer_12.0_full\setup.exe ou C:\SQLServer_11.0_full\setup.exe

> [!NOTE]
> Quando você executar pela primeira vez a instalação do SQL Server, é possível que outros arquivos de instalação sejam baixados e exijam a reinicialização da máquina virtual e da instalação do SQL Server.
> 
> Se você precisar personalizar repetidamente a imagem selecionada da Máquina Virtual do Microsoft Azure, considere a criação de sua própria imagem do SQL Server. A funcionalidade SysPrep do Analysis Services foi habilitada com o SQL Server 2012 SP1 CU2. Para saber mais, consulte [Considerações sobre a instalação do SQL Server usando SysPrep](https://msdn.microsoft.com/library/ee210754.aspx) e [Suporte do Sysprep para funções de servidor](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles).
> 
> 

### <a name="to-install-analysis-services-tabular-mode"></a>Para instalar o modo de tabela do Analysis Services
As etapas nesta seção **resumem** a instalação do modo de tabela do Analysis Services. Para saber mais, consulte o seguinte: 

* [Instalar o Analysis Services em Modo de Tabela](https://msdn.microsoft.com/library/hh231722.aspx)
* [Modelagem de tabela (tutorial da Adventure Works)](https://msdn.microsoft.com/library/140d0b43-9455-4907-9827-16564a904268)

**Para instalar o Modo de Tabela do Analysis Services:**

1. No assistente de instalação do SQL Server, clique em **Instalação** no painel esquerdo e clique em **Nova instalação autônoma do SQL Server ou adicionar recursos a uma instalação existente**.
   
   * Se a opção **Procurar Pasta** for exibida, navegue até c:\SQLServer_13.0_full, c:\SQLServer_12.0_full ou c:\SQLServer_11.0_full e clique em **Ok**.
2. Clique em **Avançar** na página de atualizações de produto.
3. Na página **Tipo de Instalação**, selecione **Executar uma nova instalação do SQL Server** e clique em **Avançar**.
4. Na página **Função de Instalação**, clique em **Instalação de Recursos do SQL Server**.
5. Na página **Seleção de Recurso**, clique em **Analysis Services**.
6. Na página **Configuração da Instância**, digite um nome descritivo, por exemplo, **Tabela**, nas caixas de texto **Instância Nomeada** e **Id da Instância**.
7. Na página **Configuração do Analysis Services**, selecione **Modo de Tabela**. Adicione o usuário atual à lista de permissões administrativas.
8. Conclua e feche o assistente de instalação do SQL Server.

## <a name="analysis-services-configuration"></a>Configuração do Analysis Services
### <a name="remote-access-to-analysis-services-server"></a>Acesso remoto ao servidor do Analysis Services
O servidor do Analysis Services dá suporte apenas à autenticação do Windows . Para acessar o Analysis Services remotamente de aplicativos cliente, por exemplo, do SQL Server Management Studio ou do SQL Server Data Tools, a máquina virtual deve estar adicionada ao seu domínio local, usando a Rede Virtual do Azure. Para saber mais, consulte [Rede Virtual do Azure](../../../virtual-network/virtual-networks-overview.md).

Uma **instância padrão** do Analysis Services escuta na porta TCP **2383**. Abra a porta no firewall das máquinas virtuais. Uma instância nomeada em cluster do Analysis Services também escuta na porta **2383**.

Para uma **instância nomeada** do Analysis Services, o serviço SQL Server Browser é necessário para o gerenciamento do acesso à porta. A configuração padrão do SQL Server Browser é a porta **2382**.

No firewall de máquinas virtuais, abra a porta **2382** e crie uma porta estática de instância nomeada do Analysis Services.

1. Para verificar as portas que já estão sendo utilizadas na VM, e qual processo está usando as portas, execute o seguinte comando com privilégios administrativos:
   
        netstat /ao
2. Use o SQL Server Management Studio para criar uma instância nomeada estática do Analysis Services atualizando o valor 'Porta' nas propriedades gerais da instância AS da tabela. Para saber mais, consulte "Usar uma porta fixa para uma instância padrão ou nomeada" em [Configurar o Firewall do Windows para permitir acesso ao Analysis Services](https://msdn.microsoft.com/library/ms174937.aspx#bkmk_fixed).
3. Reinicie a instância tabular do serviço Analysis Services.

Para saber mais, consulte a seção **Pontos de extremidade de máquina virtual e portas de firewall** neste documento.

## <a name="virtual-machine-endpoints-and-firewall-ports"></a>Pontos de extremidade de máquina virtual e portas de firewall
Esta seção resume os pontos de extremidade de máquina virtual do Microsoft Azure a serem criados e as portas a serem abertas nos firewalls da máquina virtual. A tabela a seguir resume as portas **TCP** para as quais devem ser criados pontos de extremidade, e as portas a serem abertas no firewall das máquinas virtuais.

* Se você estiver usando uma única VM e os dois itens a seguir forem verdadeiros, não será necessário criar pontos de extremidade de VM e abrir as portas no firewall na VM.
  
  * Você não se conecta remotamente aos recursos do SQL Server na VM. Estabelecer uma conexão de área de trabalho remota com a VM e acessar localmente os recursos do SQL Server na VM não é considerado uma conexão remota com os recursos do SQL Server.
  * Não associe a VM a um domínio local por meio da Rede Virtual do Azure ou por outra solução de encapsulamento de VPN.
* Se a máquina virtual não pertencer a um domínio, mas se você quiser se conectar remotamente aos recursos do SQL Server na VM:
  
  * Abra as portas no firewall na VM.
  * Crie pontos de extremidade de máquina virtual para as portas indicadas (*).
* Se a máquina virtual for ingressada em um domínio usando um túnel VPN, por exemplo, a Rede Virtual do Azure, os pontos de extremidade não serão necessários. No entanto, abra as portas no firewall na VM.
  
  | Porta | Type | DESCRIÇÃO |
  | --- | --- | --- |
  | **80** |TCP |Acesso remoto ao servidor de relatório (*). |
  | **1433** |TCP |SQL Server Management Studio (*). |
  | **1434** |UDP |SQL Server Browser. Necessário quando a VM ingressa em um domínio. |
  | **2382** |TCP |SQL Server Browser. |
  | **2383** |TCP |Instância padrão do SQL Server Analysis Services e instâncias nomeadas em cluster. |
  | **Definido pelo usuário** |TCP |Crie uma porta de instância nomeada estática do Analysis Services para um número de porta de sua escolha e desbloqueie o número da porta no firewall. |

Para saber mais sobre a criação de pontos de extremidade, consulte o seguinte:

* Criar pontos de extremidade:[Como configurar os pontos de extremidade para uma máquina virtual](../classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
* SQL Server: Consulte a seção "Etapas completas de configuração para se conectar à máquina virtual usando o SQL Server Management Studio" de [Provisionando uma máquina de Virtual do SQL Server no Azure](../sql/virtual-machines-windows-portal-sql-server-provision.md).

O diagrama a seguir ilustra as portas a serem abertas no firewall da VM a fim de permitir o acesso remoto a recursos e componentes na VM.

![portas a serem abertas para aplicativos de bi em VMs do Azure](./media/virtual-machines-windows-classic-ps-sql-bi/IC654385.gif)

## <a name="resources"></a>Recursos
* Leia a política de suporte para softwares de servidores da Microsoft usado no ambiente de máquina virtual do Azure. O tópico a seguir resume o suporte a recursos como BitLocker, Clustering de Failover e Balanceamento de Carga de Rede. [Suporte de software a servidores da Microsoft para máquinas virtuais do Azure](https://support.microsoft.com/kb/2721672)
* [Visão geral do SQL Server em máquinas virtuais do Azure](../sql/virtual-machines-windows-sql-server-iaas-overview.md)
* [Máquinas virtuais](https://azure.microsoft.com/documentation/services/virtual-machines/)
* [Provisionando uma máquina virtual do SQL Server no Azure](../sql/virtual-machines-windows-portal-sql-server-provision.md)
* [Como anexar um disco de dados à máquina virtual](../classic/attach-disk-classic.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [Migração de um banco de dados para o SQL Server em uma VM do Azure](../sql/virtual-machines-windows-migrate-sql.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fsqlclassic%2ftoc.json)
* [Determinar o modo de servidor de uma instância do Analysis Services](https://msdn.microsoft.com/library/gg471594.aspx)
* [Modelagem multidimensional (tutorial da Adventure Works)](https://technet.microsoft.com/library/ms170208.aspx)
* [Centro de Documentação do Azure](https://azure.microsoft.com/documentation/)
* [Usando o Power BI em um ambiente híbrido](https://msdn.microsoft.com/library/dn798994.aspx)

> [!NOTE]
> [Enviar comentários e informações de contato pelo Microsoft SQL Server Connect](https://connect.microsoft.com/SQLServer/Feedback)

### <a name="community-content"></a>Conteúdo da comunidade
* [Gerenciamento de banco de dados SQL do Azure com o PowerShell](https://azure.microsoft.com/blog/windows-azure-sql-database-management-with-powershell/)

