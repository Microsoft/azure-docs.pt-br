---
title: Proteger o Active Directory e o DNS com o Azure Site Recovery | Microsoft Docs
description: "Este artigo descreve como implementar uma solução de recuperação de desastre para o Active Directory usando o Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: prateek9us
manager: gauravd
editor: 
ms.assetid: af1d9b26-1956-46ef-bd05-c545980b72dc
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 7/20/2017
ms.author: pratshar
ms.openlocfilehash: 197441fc24c178695d4eada6db59f503b21672ad
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="protect-active-directory-and-dns-with-azure-site-recovery"></a>Proteger o Active Directory e o DNS com o Azure Site Recovery
Aplicativos empresariais como o SharePoint, o Dynamics AX e o SAP dependem do Active Directory e de uma infraestrutura de DNS para funcionar corretamente. Quando você cria uma solução de recuperação de desastre para aplicativos, é importante se lembrar de que você precisa proteger e recuperar o Active Directory e o DNS antes dos outros componentes de aplicativo, para garantir que tudo funcione corretamente quando ocorrer um desastre.

O Site Recovery é um serviço do Azure que fornece recuperação de desastre por meio da orquestração de replicação, failover e recuperação de máquinas virtuais. O Site Recovery dá suporte a vários cenários de replicação, com o intuito de proteger e executar failover de forma consistente de máquinas virtuais e aplicativos em nuvens privadas, públicas ou de hoster.

Com a Recuperação de Site, você pode criar um plano totalmente automatizado de recuperação de desastre para o Active Directory. Quando ocorrem interrupções, você pode iniciar um failover em poucos segundos de qualquer lugar para que o Active Directory esteja em funcionamento em alguns minutos. Se você implantou o Active Directory para vários aplicativos, como o SharePoint e o SAP, em seu site primário e deseja executar failover de todo o site, é possível executar o failover do Active Directory primeiro usando a Recuperação de Site e depois executar o failover dos outros aplicativos que usam planos de recuperação específicos do aplicativo.

Este artigo explica como criar uma solução de recuperação de desastre para o Active Directory, como executar failovers de teste planejados e não planejados usando um plano de recuperação de um clique, as configurações com suporte e os pré-requisitos.  Antes de começar, você deve estar familiarizado com o Active Directory e o Azure Site Recovery.

## <a name="replicating-domain-controller"></a>Replicando o controlador de domínio

Você precisa configurar a [replicação do Site Recovery](#enable-protection-using-site-recovery) em pelo menos uma máquina virtual que hospeda o Controlador de Domínio e o DNS. Se você tiver [vários controladores de domínio](#environment-with-multiple-domain-controllers) em seu ambiente, além de replicar a máquina virtual do controlador de domínio com o Site Recovery, também precisará configurar um [controlador de domínio adicional](#protect-active-directory-with-active-directory-replication) no site de destino (datacenter local secundário ou do Azure). 

### <a name="single-domain-controller-environment"></a>Ambiente de controlador de domínio único
Se você tiver alguns aplicativos e um único controlador de domínio e desejar executar failover de todo o site, é recomendável usar o Site Recovery para replicar o controlador de domínio no site secundário (esteja você executando failover no Azure ou em um site secundário). A mesma máquina virtual controladora/DNS replicada também pode ser usada para o [failover de teste](#test-failover-considerations).

### <a name="environment-with-multiple-domain-controllers"></a>Ambiente com vários controladores de domínio
Se você tiver muitos aplicativos e houver mais de um controlador de domínio no ambiente ou se planeja executar failover de alguns aplicativos por vez, é recomendável, além de replicar a máquina virtual do controlador de domínio com o Site Recovery, configurar também um [controlador de domínio adicional](#protect-active-directory-with-active-directory-replication) no site de destino (Azure ou um datacenter local secundário). Para [failover de teste](#test-failover-considerations), use o controlador de domínio replicado pelo Site Recovery; para failover, use o controlador de domínio adicional no site de destino. 


As seções a seguir explicam como habilitar a proteção para um controlador de domínio na Recuperação de Site e como configurar um controlador de domínio no Azure.

## <a name="prerequisites"></a>Pré-requisitos
* Uma implantação local do servidor DNS e do Active Directory.
* Um cofre dos Serviços do Azure Site Recovery em uma assinatura do Microsoft Azure.
* Se estiver replicando no Azure, execute a ferramenta de Avaliação de Prontidão de Máquina Virtual do Azure nas VMs para garantir que sejam compatíveis com as VMs do Azure e com os Serviços do Azure Site Recovery.

## <a name="enable-protection-using-site-recovery"></a>Habilitar a proteção com a Recuperação de Site
### <a name="protect-the-virtual-machine"></a>Proteger a máquina virtual
Habilite a proteção da máquina virtual do DNS/controlador de domínio na Site Recovery. Defina configurações da Recuperação de Site de acordo com o tipo de máquina virtual (Hyper-V ou VMware). O controlador de domínio replicado usando o Site Recovery é usado para [failover de teste](#test-failover-considerations). Verifique se ele atende aos seguintes requisitos:

1. O controlador de domínio é um servidor de catálogo global
2. O controlador de domínio deve ser o proprietário da função FSMO para as funções que serão necessárias durante um failover de teste (do contrário, essa funções precisarão ser [aproveitadas](http://aka.ms/ad_seize_fsmo) após o failover)

### <a name="configure-virtual-machine-network-settings"></a>Definir configurações de rede da máquina virtual
Para a máquina virtual do controlador de domínio/DNS, defina as configurações de rede do Site Recovery de modo que a máquina virtual seja conectada à rede correta após o failover. 

![Configurações de rede da VM](./media/site-recovery-active-directory/DNS-Target-IP.png)

## <a name="protect-active-directory-with-active-directory-replication"></a>Proteger o Active Directory com a replicação do Active Directory
### <a name="site-to-site-protection"></a>Proteção site a site
Crie um controlador de domínio no site secundário. Ao promover o servidor para uma função de controlador de domínio, especifique o mesmo nome de domínio que está sendo usado no site primário. Você pode usar o snap-in dos **Sites e Serviços do Active Directory** para definir as configurações no objeto de link de site ao qual os sites serão adicionados. Ao definir as configurações em um link de site, você pode controlar quando a replicação ocorre entre dois ou mais sites e com que frequência. Para saber mais, veja [Agendamento da replicação entre sites](https://technet.microsoft.com/library/cc731862.aspx).

### <a name="site-to-azure-protection"></a>Proteção Site ao Azure
Siga estas instruções para [criar um controlador de domínio em uma rede virtual do Azure](../active-directory/active-directory-install-replica-active-directory-domain-controller.md). Ao promover o servidor para uma função de controlador de domínio, especifique o mesmo nome de domínio usado no site primário.

Depois disso, [reconfigure o servidor DNS para a rede virtual](../active-directory/active-directory-install-replica-active-directory-domain-controller.md#reconfigure-dns-server-for-the-virtual-network)para usar o servidor DNS no Azure.

![Rede do Azure](./media/site-recovery-active-directory/azure-network.png)

**DNS na Rede de produção do Azure**

## <a name="test-failover-considerations"></a>considerações sobre failover de teste
O failover de teste ocorre em uma rede isolada da rede de produção para que não ocorra impactos nas cargas de trabalho de produção.

A maioria dos aplicativos também exige a presença de um controlador de domínio e de um servidor DNS para funcionar. Portanto, antes do failover do aplicativo, um controlador de domínio precisa ser criado na rede isolada para ser usado para failover de teste. A maneira mais fácil de fazer isso é replicar uma máquina virtual do controlador de domínio/DNS com o Site Recovery. Em seguida, execute um failover de teste da máquina virtual do controlador de domínio antes de executar um failover de teste do plano de recuperação para o aplicativo. Veja como fazer isso:

1. [Replique](site-recovery-replicate-vmware-to-azure.md) a máquina virtual do controlador de domínio/DNS usando o Site Recovery.
1. Crie uma rede isolada. Qualquer rede virtual criada no Azure é isolada por padrão de outras redes. Recomendamos que o intervalo de endereços IP dessa rede seja o mesmo de sua rede de produção. Não habilite a conectividade site a site nessa rede.
1. Forneça um endereço IP de DNS na rede criada, como o endereço IP que você espera que a máquina virtual do DNS obtenha. Se estiver replicando no Azure, forneça o endereço IP da VM que é usada no failover na configuração **IP de Destino** das configurações **Computação e Rede**. 

    ![IP de destino](./media/site-recovery-active-directory/DNS-Target-IP.png) **IP de destino**

    ![Rede de teste do Azure](./media/site-recovery-active-directory/azure-test-network.png)

    **DNS na Rede de teste do Azure**

> [!TIP]
> O Site Recovery tenta criar máquinas virtuais de teste em uma sub-rede de mesmo nome e usando o mesmo IP fornecido nas configurações de **Computação e Rede** da máquina virtual. Se a sub-rede de mesmo nome não estiver disponível na rede virtual do Azure fornecida para failover de teste, então a máquina virtual de teste será criada na primeira sub-rede em ordem alfabética. Se o IP de destino fizer parte da sub-rede escolhida, o Site Recovery tentará criar a máquina virtual do failover de teste usando o IP de destino. Se o IP de destino não fizer parte da sub-rede escolhida, a máquina virtual do failover de teste será criada usando qualquer IP disponível na sub-rede escolhida. 
>
>


1. Se você estiver replicando em outro site local e estiver usando DHCP, siga as instruções de modo a [configurar o DNS e o DHCP para failover de teste](site-recovery-test-failover-vmm-to-vmm.md#prepare-dhcp)
1. Execute um failover de teste na máquina virtual do controlador de domínio da rede isolada. Use o ponto de recuperação **consistente com o aplicativo** mais recente disponível da máquina virtual do controlador de domínio para fazer o failover de teste. 
1. Execute um failover de teste para o plano de recuperação que contém máquinas virtuais do aplicativo. 
1. Depois que o teste for concluído, **limpe o failover de teste** na máquina virtual do controlador de domínio. Essa etapa exclui o controlador de domínio que foi criado para o failover de teste.


### <a name="removing-reference-to-other-domain-controllers"></a>Remover a referência a outros controladores de domínio
Ao fazer um failover de teste, você não coloca todos os controladores de domínio na rede de teste. Para remover a referência a outros controladores de domínio no ambiente de produção, talvez seja necessário [aproveitar as funções FSMO do Active Directory](http://aka.ms/ad_seize_fsmo) e fazer a [limpeza dos metadados](https://technet.microsoft.com/library/cc816907.aspx) de controladores de domínio ausentes. 



> [!IMPORTANT]
> Algumas das configurações descritas na seção a seguir não são as configurações padrão do controlador de domínio. Se não quiser fazer essas alterações em um controlador de domínio de produção, você poderá criar um controlador de domínio dedicado a ser usado para o failover de teste do Site Recovery e fazer as alterações nele.  
>
>

### <a name="issues-because-of-virtualization-safeguards"></a>Problemas devido às defesas da virtualização 

Começando com o Windows Server 2012, [defesas adicionais foram inseridas no Active Directory Domain Services](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100). Essas defesas ajudam a proteger os controladores de domínio virtualizados contra Reversões de USN, desde que a plataforma do hipervisor subjacente ofereça suporte a VM-GenerationID. O Azure dá suporte a VM-GenerationID, o que significa que os controladores de domínio que executam o Windows Server 2012 ou posterior nas máquinas virtuais do Azure têm as proteções adicionais. 


Quando a VM-GenerationID é redefinida, a invocationID do banco de dados do AD DS também é redefinida, a pool RID é descartada e SYSVOL é marcado como não autoritativo. Para saber mais, confira [Introdução à virtualização do Active Directory Domain Services](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100) e [Safely Virtualizing DFSR](https://blogs.technet.microsoft.com/filecab/2013/04/05/safely-virtualizing-dfsr/) (Virtualização segura do DFSR)

Fazer failover no Azure pode causar a redefinição de VM-GenerationID e isso aciona as defesas adicionais quando a máquina virtual do controlador de domínio é iniciada no Azure. Isso pode resultar em um **atraso significativo** na capacidade de o usuário fazer logon na máquina virtual do controlador de domínio. Uma vez que esse controlador de domínio seria usado apenas um failover de teste, as defesas da virtualização não são necessárias. Para garantir que VM-GenerationID para a máquina virtual do controlador de domínio não mude, você pode alterar o valor DWORD a seguir para 4 no controlador de domínio local.

        
        HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\gencounter\Start
 

#### <a name="symptoms-of-virtualization-safeguards"></a>Sintomas das defesas da virtualização
 
Se as defesas da virtualização entrarem em ação após um failover de teste, você poderá ver um ou mais dos seguintes sintomas:  

Alteração da ID de geração

![Alteração da ID de Geração](./media/site-recovery-active-directory/Event2170.png)

Alteração da ID de invocação

![Alteração da ID de Invocação](./media/site-recovery-active-directory/Event1109.png)

Compartilhamentos Sysvol e Netlogon não estão disponíveis

![Compartilhamento Sysvol](./media/site-recovery-active-directory/sysvolshare.png)

![NTFRS Sysvol](./media/site-recovery-active-directory/Event13565.png)

Todos os bancos de dados DFSR são excluídos

![Exclusão do BD DFSR](./media/site-recovery-active-directory/Event2208.png)


> [!IMPORTANT]
> Algumas das configurações descritas na seção a seguir não são as configurações padrão do controlador de domínio. Se não quiser fazer essas alterações em um controlador de domínio de produção, você poderá criar um controlador de domínio dedicado a ser usado para o failover de teste do Site Recovery e fazer as alterações nele.  
>
>


### <a name="troubleshooting-domain-controller-issues-during-test-failover"></a>Solucionando problemas do controlador de domínio durante o failover de teste


Em um prompt de comando, execute o seguinte comando para verificar se as pastas SYSVOL e NETLOGON estão compartilhadas:

    NET SHARE

No prompt de comando, execute o comando a seguir para garantir que o controlador de domínio esteja funcionando corretamente.

    dcdiag /v > dcdiag.txt

No log de saída, procure o texto a seguir para confirmar se o controlador de domínio está funcionando bem. 

* "passou no teste Connectivity"
* "passou no teste Advertising"
* "passou no teste MachineAccount"

Se as condições anteriores forem atendidas, é provável que o controlador de domínio esteja funcionando bem. Caso contrário, tente as etapas a seguir.


* Faça uma restauração autoritativa do controlador de domínio.
    * Embora [não seja recomendável usar a replicação FRS](https://blogs.technet.microsoft.com/filecab/2014/06/25/the-end-is-nigh-for-frs/), mas se ainda a estiver usando, siga as etapas fornecidas [aqui](https://support.microsoft.com/kb/290762) para fazer uma restauração autoritativa. Você pode ler mais sobre os Burflags mencionados no link anterior [aqui](https://blogs.technet.microsoft.com/janelewis/2006/09/18/d2-and-d4-what-is-it-for/).
    * Se estiver usando replicação DFSR, siga as etapas disponíveis [aqui](https://support.microsoft.com/kb/2218556) para fazer uma restauração autoritativa. Você também pode usar funções do Powershell disponíveis neste [link](https://blogs.technet.microsoft.com/thbouche/2013/08/28/dfsr-sysvol-authoritative-non-authoritative-restore-powershell-functions/) para essa finalidade. 
    
* Ignore o requisito de sincronização inicial definindo a chave de registro a seguir para 0 no controlador de domínio local. Se esse DWORD não existir, você poderá criá-lo no nó 'Parameters'. Saiba mais sobre isso [aqui](https://support.microsoft.com/kb/2001093)

        HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\NTDS\Parameters\Repl Perform Initial Synchronizations

* Desabilite o requisito de que um servidor de catálogo global está disponível para validar o logon do usuário definindo a chave de registro a seguir para 1 no controlador de domínio local. Se esse DWORD não existir, você poderá criá-lo no nó 'Lsa'. Saiba mais sobre isso [aqui](http://support.microsoft.com/kb/241789)

        HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Lsa\IgnoreGCFailures



### <a name="dns-and-domain-controller-on-different-machines"></a>DNS e controlador de domínio em computadores diferentes
Caso o DNS não esteja na mesma máquina virtual que o controlador de domínio, será necessário criar uma VM do DNS para o failover de teste. Caso eles estejam na mesma VM, você pode ignorar esta seção.

Você pode usar um servidor DNS atualizado e criar todas as zonas necessárias. Por exemplo, se o domínio do Active Directory for contoso.com, é possível criar uma zona DNS com o nome contoso.com. As entradas que correspondem ao Active Directory devem ser atualizadas no DNS, da seguinte maneira:

1. Assegure-se de que essas configurações estão definidas antes que outra máquina virtual apareça no plano de recuperação:
   
   * A zona deve ser nomeada depois do nome raiz da floresta.
   * A zona deve ter backup em arquivo.
   * A zona deve ser habilitada para atualizações seguras e não seguras.
   * O resolvedor da máquina virtual do controlador de domínio deve apontar para o endereço IP da máquina virtual do DNS.
2. Execute o seguinte comando na máquina virtual do controlador de domínio:
   
    `nltest /dsregdns`
3. Adicionar uma zona no servidor DNS, permitir atualizações não seguras e adicionar uma entrada para ela no DNS:
   
        dnscmd /zoneadd contoso.com  /Primary
        dnscmd /recordadd contoso.com  contoso.com. SOA %computername%.contoso.com. hostmaster. 1 15 10 1 1
        dnscmd /recordadd contoso.com %computername%  A <IP_OF_DNS_VM>
        dnscmd /config contoso.com /allowupdate 1

## <a name="next-steps"></a>Próximas etapas
Leia [Quais cargas de trabalho posso proteger?](site-recovery-workload.md) para saber mais sobre como proteger as cargas de trabalho corporativas com o Azure Site Recovery.

