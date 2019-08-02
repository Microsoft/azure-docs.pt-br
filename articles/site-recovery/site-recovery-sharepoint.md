---
title: Configurar a recuperação de desastres para um aplicativo do SharePoint de várias camadas usando o Azure Site Recovery | Microsoft Docs
description: Este artigo descreve como configurar a recuperação de desastres para um aplicativo do SharePoint de várias camadas usando os recursos do Azure Site Recovery.
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 6/27/2019
ms.author: sutalasi
ms.openlocfilehash: bc6d9e7214d2b7cd009e7562357bed420e49f185
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68325104"
---
# <a name="set-up-disaster-recovery-for-a-multi-tier-sharepoint-application-for-disaster-recovery-using-azure-site-recovery"></a>Configurar a recuperação de desastre para um aplicativo do SharePoint de várias camadas para recuperação de desastres usando o Azure Site Recovery

Este artigo descreve detalhadamente como proteger um aplicativo do SharePoint usando o [Azure Site Recovery](site-recovery-overview.md).


## <a name="overview"></a>Visão geral

O Microsoft SharePoint é um aplicativo avançado que pode ajudar um grupo ou departamento a organizar, colaborar com e compartilhar informações. O SharePoint pode fornecer portais de intranet, gerenciamento de arquivos e documentos, colaboração, redes sociais, extranets, sites, Enterprise Search e business intelligence. Ele também tem integração de sistemas, integração de processos e funcionalidades de automação de fluxo de trabalho. Normalmente, as organizações o consideram um aplicativo de camada 1, suscetível a tempo de inatividade e perda de dados.

Hoje, o Microsoft SharePoint não fornece nenhuma funcionalidade integrada de recuperação de desastre. Independentemente do tipo e escala de um desastre, a recuperação envolve o uso de um data center em espera para o qual você pode recuperar o farm. Data centers em espera são necessários para cenários nos quais não é possível recuperar sistemas redundantes locais e backups da interrupção no data center principal.

Uma boa solução de recuperação de desastre deve permitir a modelagem de planos de recuperação em torno de arquiteturas de aplicativo complexas, como o SharePoint. Ela também deve ter a capacidade de adicionar etapas personalizadas para lidar com mapeamentos de aplicativo entre várias camadas, fornecendo assim um failover de clique único com um RTO mais baixo em caso de desastre.

Este artigo descreve detalhadamente como proteger um aplicativo do SharePoint usando o [Azure Site Recovery](site-recovery-overview.md). Este artigo abordará as práticas recomendadas para a replicação de um aplicativo do SharePoint no Azure, como você pode fazer uma análise de recuperação de desastre e como é possível realizar o failover do aplicativo no Azure.

Você pode assistir ao vídeo abaixo sobre como recuperar um aplicativo de várias camadas para o Azure.

> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/Disaster-Recovery-of-load-balanced-multi-tier-applications-using-Azure-Site-Recovery/player]


## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, você precisa entender o seguinte:

1. [Replicar uma máquina virtual no Azure](site-recovery-vmware-to-azure.md)
2. Como [criar uma rede de recuperação](site-recovery-network-design.md)
3. [Executar um failover de teste no Azure](site-recovery-test-failover-to-azure.md)
4. [Executar um failover no Azure](site-recovery-failover.md)
5. Como [replicar um controlador de domínio](site-recovery-active-directory.md)
6. Como [replicar o SQL Server](site-recovery-sql.md)

## <a name="sharepoint-architecture"></a>Arquitetura do SharePoint

O SharePoint pode ser implantado em um ou mais servidores usando topologias em camadas e funções de servidor para implementar um design de farm que cumpra metas e objetivos específicos. Um farm grande e de alta demanda típico do SharePoint Server, que dá suporte a um grande número de usuários simultâneos e um grande número de itens de conteúdo usam o agrupamento de serviços como parte de sua estratégia de escalabilidade. Essa abordagem envolve a execução de serviços em servidores dedicados, agrupando esses serviços e depois expandindo os servidores como um grupo. A topologia a seguir ilustra o serviço agrupamento de serviços e servidores para um farm do SharePoint Server de três camadas. Consulte a documentação do SharePoint e arquiteturas de linha de produto para obter diretrizes detalhadas sobre diferentes topologias do SharePoint. Você pode encontrar mais detalhes sobre a implantação do SharePoint 2013 [neste documento](https://technet.microsoft.com/library/cc303422.aspx).



![Padrão de Implantação 1](./media/site-recovery-sharepoint/sharepointarch.png)


## <a name="site-recovery-support"></a>Suporte do Site Recovery

Para criar este artigo, usamos as máquinas virtuais VMware com o Windows Server 2012 R2 Enterprise. Foram usados o SharePoint 2013 Enterprise edition e o SQL Server 2014 Enterprise edition. Já que a replicação do Site Recovery é independente do aplicativo, as recomendações fornecidas aqui devem servir também para os cenários a seguir.

### <a name="source-and-target"></a>Origem e destino

**Cenário** | **Para um site secundário** | **Para o Azure**
--- | --- | ---
**Hyper-V** | Sim | Sim
**VMware** | Sim | Sim
**Servidor físico** | Sim | Sim
**As tabelas** | ND | Sim

### <a name="sharepoint-versions"></a>Versões do SharePoint
Há suporte para as seguintes versões do SharePoint Server.

* SharePoint Server 2013 Standard
* SharePoint Server 2013 Enterprise
* SharePoint Server 2016 Standard
* SharePoint Server 2016 Enterprise

### <a name="things-to-keep-in-mind"></a>Algumas coisas que se deve manter em mente

Se você estiver usando um cluster baseado em disco compartilhado como qualquer camada em seu aplicativo, você não poderá usar a replicação do Site Recovery para replicar essas máquinas virtuais. Você pode usar replicação nativa fornecida pelo aplicativo e, em seguida, usar um [plano de recuperação](site-recovery-create-recovery-plans.md) para fazer failover de todas as camadas.

## <a name="replicating-virtual-machines"></a>Replicação de máquinas virtuais

Siga [estas diretrizes](site-recovery-vmware-to-azure.md) para começar a replicar a máquina virtual para o Azure.

* Depois que a replicação for concluída, certifique-se de ir para cada máquina virtual de cada camada e selecionar o mesmo conjunto de disponibilidade em 'Item replicado > Configurações > Propriedades > Computação e Rede'. Por exemplo, se sua camada Web tem 3 VMs, certifique-se de que todas as 3 VMs estejam configuradas para fazer parte do mesmo conjunto de disponibilidade no Azure.

    ![Definir Conjunto de Disponibilidade](./media/site-recovery-sharepoint/select-av-set.png)

* Para obter diretrizes sobre como proteger o Active Directory e DNS, consulte o documento [Proteger o Active Directory e DNS](site-recovery-active-directory.md).

* Para obter diretrizes sobre como proteger a camada de banco de dados em execução no SQL Server, consulte o documento [Proteger o SQL Server](site-recovery-active-directory.md).

## <a name="networking-configuration"></a>Configuração de rede

### <a name="network-properties"></a>Propriedades da rede

* Para as VMs de camada da Web e de Aplicativo, defina configurações de rede no Portal do Azure para que as VMs sejam anexadas à rede de DR correta após o failover.

    ![Selecionar Rede](./media/site-recovery-sharepoint/select-network.png)


* Se você estiver usando um IP estático, especifique o IP que você deseja usar na máquina virtual no campo **IP de Destino**

    ![Definir o IP Estático](./media/site-recovery-sharepoint/set-static-ip.png)

### <a name="dns-and-traffic-routing"></a>Roteamento de Tráfego e de DNS

Para sites voltados para a Internet, [crie um perfil do Gerenciador de Tráfego do tipo 'Prioridade'](../traffic-manager/traffic-manager-create-profile.md) na assinatura do Azure. Em seguida, configure seu perfil do Gerenciador de Tráfego e de DNS da maneira descrita a seguir.


| **Onde** | **Source** | **Destino**|
| --- | --- | --- |
| DNS público | DNS público para sites do SharePoint <br/><br/> Por exemplo: sharepoint.contoso.com | Gerenciador de Tráfego <br/><br/> contososharepoint.trafficmanager.net |
| DNS local | sharepointonprem.contoso.com | IP público no farm local |


No perfil do Gerenciador de Tráfego, [crie os pontos de extremidade primário e de recuperação](../traffic-manager/traffic-manager-configure-priority-routing-method.md). Use o ponto de extremidade externo como ponto de extremidade local e o IP público como ponto de extremidade do Azure. Verifique se a prioridade está definida como mais alta para o ponto de extremidade local.

Hospede uma página de teste em uma porta específica (por exemplo, 800) na camada da Web do SharePoint para o Gerenciador de Tráfego para detectar automaticamente o failover de postagem de disponibilidade. Essa é uma solução alternativa caso você não possa habilitar a autenticação anônima em nenhum dos seus sites do SharePoint.

[Configure o perfil do Gerenciador de Tráfego](../traffic-manager/traffic-manager-configure-priority-routing-method.md) com as configurações abaixo.

* Método de roteamento – 'Prioridade'
* Vida útil (TTL) do DNS – '30 segundos'
* Configurações do monitor de ponto de extremidade – se você pode habilitar a autenticação anônima, pode também fornecer um ponto de extremidade de site específico. Ou então, você pode usar uma página de teste em uma porta específica (por exemplo, 800).

## <a name="creating-a-recovery-plan"></a>Criar um plano de recuperação

Um plano de recuperação permite o sequenciamento do failover de várias camadas em um aplicativo de várias camadas, mantendo assim a consistência do aplicativo. Execute as etapas abaixo ao criar um plano de recuperação para um aplicativo Web de várias camadas. [Saiba mais sobre a criação de um plano de recuperação](site-recovery-runbook-automation.md#customize-the-recovery-plan).

### <a name="adding-virtual-machines-to-failover-groups"></a>Adicionar máquinas virtuais aos grupos de failover

1. Crie um plano de recuperação adicionando as VMs de camada da Web e de Aplicativo.
2. Clique em 'Personalizar' para agrupar as VMs. Por padrão, todas as VMs são parte do 'Grupo 1'.

    ![Personalizar RP](./media/site-recovery-sharepoint/rp-groups.png)

3. Crie outro grupo (Grupo 2) e mova as VMs de camada da Web para o novo grupo. Suas VMs de camada de aplicativo devem ser parte do 'Grupo 1' e as VMs de camada da Web devem ser parte do 'Grupo 2'. Isso é para garantir que a inicialização das VMs de camada de Aplicativo ocorra primeiro, seguidas das VMs de camada da Web.


### <a name="adding-scripts-to-the-recovery-plan"></a>Adicionar scripts ao plano de recuperação

Você pode implantar os scripts do Azure Site Recovery mais comumente usados em sua conta de automação clicando no botão 'Implantar no Azure' abaixo. Quando você estiver usando qualquer script publicado, verifique se seguiu as diretrizes no script.

[![Implantar no Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

1. Adicione um script de ação prévia para o 'Grupo 1' para fazer failover do grupo de Disponibilidade do SQL. Use o script 'ASR-SQL-FailoverAG' publicado nos scripts de exemplo. Verifique se você seguiu as diretrizes no script e faça adequadamente as alterações necessárias no script.

    ![Add-AG-Script-Step-1](./media/site-recovery-sharepoint/add-ag-script-step1.png)

    ![Add-AG-Script-Step-2](./media/site-recovery-sharepoint/add-ag-script-step2.png)

2. Adicione um script de ação posterior para anexar um balanceador de carga a máquinas virtuais da camada da Web (Grupo 2) nas quais o failover foi realizado. Use o script 'ASR-AddSingleLoadBalancer' publicado nos scripts de exemplo. Verifique se você seguiu as diretrizes no script e faça adequadamente as alterações necessárias no script.

    ![Add-LB-Script-Step-1](./media/site-recovery-sharepoint/add-lb-script-step1.png)

    ![Add-LB-Script-Step-2](./media/site-recovery-sharepoint/add-lb-script-step2.png)

3. Adicione uma etapa manual para atualizar os registros DNS para apontar para o novo farm no Azure.

    * Para sites voltados para a Internet, não há atualizações DNS necessárias após o failover. Siga as etapas descritas na seção 'Diretrizes de rede' para configurar o Gerenciador de Tráfego. Se o perfil do Gerenciador de Tráfego foi configurado conforme descrito na seção anterior, adicione um script para abrir a porta fictícia (800, no exemplo) na VM do Azure.

    * Para sites internos, adicione uma etapa manual para atualizar o registro DNS para apontar para o IP do balanceador de carga da nova VM de camada da Web.

4. Adicione uma etapa manual para restaurar o aplicativo de pesquisa de um backup ou iniciar um novo serviço de pesquisa.

5. Para restaurar o aplicativo do serviço Search de um backup, execute as etapas abaixo.

    * Este método presume que um backup do Aplicativo de Serviço de Pesquisa foi executado antes do evento catastrófico e que o backup está disponível no site de DR.
    * Isso pode ser conseguido facilmente agendando o backup (por exemplo, uma vez por dia) e usando um procedimento de cópia para colocar o backup no site de DR. Os procedimentos de cópia podem incluir programas em script, como o AzCopy (Cópia do Azure) ou a configuração de DFSR (Serviços de Replicação de Arquivos Distribuído).
    * Agora que o farm do SharePoint está em execução, navegue para a Administração Central, clique em 'Backup e Restauração' e selecione Restaurar. A restauração pergunta o local de backup especificado (talvez seja necessário atualizar o valor). Selecione o backup do Aplicativo de Serviço Search que você deseja restaurar.
    * A pesquisa é restaurada. Tenha em mente que a restauração espera encontrar a mesma topologia (mesmo número de servidores) e as mesmas letras de unidades de disco rígido atribuídas a esses servidores. Para obter mais informações, consulte o documento ['Restaurar aplicativo de serviço Search no SharePoint 2013'](https://technet.microsoft.com/library/ee748654.aspx).


6. Para iniciar com um novo aplicativo de serviço Search, execute as etapas a seguir.

    * Este método presume que um backup do banco de dados "Administração de Pesquisa" está disponível no site de DR.
    * Já que os outros bancos de dados do Aplicativo de Serviço de Pesquisa não são replicados, eles precisam ser recriados. Para fazer isso, navegue até a Administração Central e exclua o Aplicativo do Serviço Search. Em todos os servidores que hospedam o Índice de Pesquisa, exclua os arquivos de índice.
    * Recrie o Aplicativo de Serviço Search, resultando na recriação dos bancos de dados. É recomendável ter um script preparado que recria este aplicativo de serviço, pois não é possível executar todas as ações por meio da GUI. Por exemplo, configurar o local da unidade de índice e a topologia de pesquisa só é possível usando cmdlets do PowerShell do SharePoint. Use o cmdlet Restore-SPEnterpriseSearchServiceApplication do Windows PowerShell e especifique o banco de dados de Administração de Pesquisa encaminhado com log e replicado, Search_Service__DB. Esse cmdlet fornece à pesquisa uma configuração, um esquema, propriedades gerenciadas, regras e fontes e cria um conjunto padrão de outros componentes.
    * Já que o Aplicativo de Serviço Search tem de ser recriado, você deve iniciar um rastreamento completo para cada fonte de conteúdo restaurar o Serviço Search. Você perde algumas informações de análise do farm local, assim como as recomendações de pesquisa.

7. Depois que todas as etapas forem concluídas, salvar o plano de recuperação e o plano de recuperação final terá aparência semelhante à seguinte.

    ![RP Salvo](./media/site-recovery-sharepoint/saved-rp.png)

## <a name="doing-a-test-failover"></a>Executar um failover de teste
Siga [este guia](site-recovery-test-failover-to-azure.md) fazer um failover de teste.

1.  Acesse o Portal do Azure e selecione seu cofre do Serviço de Recuperação.
2.  Clique no plano de recuperação criado para o aplicativo do SharePoint.
3.  Clique em 'Failover de Teste'.
4.  Selecione o ponto e recuperação e a rede virtual do Azure para iniciar o processo de failover de teste.
5.  Quando o ambiente secundário estiver funcionando, você poderá executar sua validações.
6.  Depois que as validações forem concluídas, você poderá clicar em 'Limpar failover de teste' no plano de recuperação e o ambiente de failover de teste será limpo.

Para obter diretrizes sobre como fazer failover de teste do AD e DNS, consulte o documento [Considerações sobre failover de teste para o AD e DNS](site-recovery-active-directory.md#test-failover-considerations).

Para obter orientação sobre como fazer failover de teste para grupos de disponibilidade AlwaysOn do SQL, consulte [executando o aplicativo de recuperação de desastre com Azure site Recovery e fazendo failover de teste](site-recovery-sql.md#disaster-recovery-of-an-application) .

## <a name="doing-a-failover"></a>Executar um failover
Siga [estas diretrizes](site-recovery-failover.md) para fazer um failover.

1.  Vá para o Portal do Azure e selecione seu cofre dos Serviços de Recuperação.
2.  Clique no plano de recuperação criado para o aplicativo do SharePoint.
3.  Clique em 'Failover'.
4.  Selecione o ponto de recuperação para iniciar o processo de failover.

## <a name="next-steps"></a>Próximas etapas
Você pode saber mais sobre [replicar outros aplicativos](site-recovery-workload.md) usando o Site Recovery.
