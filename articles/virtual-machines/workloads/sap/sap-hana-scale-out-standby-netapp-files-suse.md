---
title: SAP HANA escalar horizontalmente com em espera com Azure NetApp Files no SLES | Microsoft Docs
description: Saiba como implantar um sistema de escalabilidade horizontal SAP HANA com o nó em espera em VMs do Azure usando Azure NetApp Files no SUSE Linux Enterprise Server.
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 02/01/2021
ms.author: radeltch
ms.openlocfilehash: d7b6f1d7ed01009af5e21006240a21442492cb07
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101673635"
---
# <a name="deploy-a-sap-hana-scale-out-system-with-standby-node-on-azure-vms-by-using-azure-netapp-files-on-suse-linux-enterprise-server"></a>Implantar um sistema de expansão SAP HANA com o nó em espera em VMs do Azure usando Azure NetApp Files no SUSE Linux Enterprise Server 

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all 
[anf-register]:https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register
[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

[2205917]:https://launchpad.support.sap.com/#/notes/2205917
[1944799]:https://launchpad.support.sap.com/#/notes/1944799
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736
[1900823]:https://launchpad.support.sap.com/#/notes/1900823

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[suse-ha-guide]:https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
[suse-drbd-guide]:https://www.suse.com/documentation/sle-ha-12/singlehtml/book_sleha_techguides/book_sleha_techguides.html
[suse-ha-12sp3-relnotes]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md


Este artigo descreve como implantar um sistema de SAP HANA altamente disponível em uma configuração de expansão com o modo de espera em VMs (máquinas virtuais) do Azure usando [Azure NetApp files](../../../azure-netapp-files/azure-netapp-files-introduction.md) para os volumes de armazenamento compartilhado.  

Nas configurações de exemplo, comandos de instalação e assim por diante, a instância do HANA é **03** e a ID do sistema Hana é **HN1**. Os exemplos são baseados em HANA 2,0 SP4 e SUSE Linux Enterprise Server para SAP 12 SP4. 

Antes de começar, consulte as seguintes notas e documentos do SAP:

* [Documentação do Azure NetApp Files][anf-azure-doc] 
* A nota SAP [1928533] inclui:  
  * Uma lista de tamanhos de VM do Azure com suporte para a implantação de software SAP
  * Informações importantes sobre capacidade para tamanhos de VM do Azure
  * Software SAP e combinações de SO (sistema operacional) e banco de dados com suporte
  * A versão de kernel do SAP necessária para Windows e Linux no Microsoft Azure
* Observação SAP [2015553]: lista os pré-requisitos para implantações de software SAP com suporte do SAP no Azure
* Nota SAP [2205917]: contém as configurações de so recomendadas para SuSE Linux Enterprise Server para aplicativos SAP
* Nota SAP [1944799]: contém diretrizes sap para SuSE Linux Enterprise Server para aplicativos SAP
* Nota SAP [2178632]: contém informações detalhadas sobre todas as métricas de monitoramento relatadas para SAP no Azure
* Nota SAP [2191498]: contém a versão necessária do agente de host do SAP para Linux no Azure
* Nota SAP [2243692]: contém informações sobre o licenciamento SAP no Linux no Azure
* Nota SAP [1984787]: contém informações gerais sobre o SuSE Linux Enterprise Server 12
* Nota SAP [1999351]: contém informações adicionais de solução de problemas para a extensão de monitoramento avançado do Azure para SAP
* Nota SAP [1900823]: contém informações sobre os requisitos de armazenamento de SAP Hana
* [Wiki da Comunidade SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes): contém todas as notas SAP necessárias para o Linux
* [Planejamento e implementação de Máquinas Virtuais do Azure para SAP no Linux][planning-guide]
* [Implantação de Máquinas Virtuais do Azure para SAP no Linux][deployment-guide]
* [Implantação de Máquinas Virtuais do Azure do DBMS para SAP no Linux][dbms-guide]
* [Guias de práticas recomendadas do SUSE SAP ha][suse-ha-guide]: contém todas as informações necessárias para configurar a alta disponibilidade do NetWeaver e SAP Hana a replicação do sistema local (para ser usada como uma linha de base geral; elas fornecem informações muito mais detalhadas)
* [Notas sobre a versão do SUSE High Availability Extension 12 SP3][suse-ha-12sp3-relnotes]
* [Aplicativos SAP NetApp no Microsoft Azure usando o Azure NetApp Files][anf-sap-applications-azure]
* [Volumes NFS v4.1 no Azure NetApp Files para SAP HANA](./hana-vm-operations-netapp.md)

## <a name="overview"></a>Visão geral

Um método para obter a alta disponibilidade do HANA é Configurando o failover automático do host. Para configurar o failover automático do host, você adiciona uma ou mais máquinas virtuais ao sistema HANA e as configura como nós em espera. Quando o nó ativo falha, um nó em espera é automaticamente assumido. Na configuração apresentada com as máquinas virtuais do Azure, você obtém o failover automático usando [NFS em Azure NetApp files](../../../azure-netapp-files/azure-netapp-files-introduction.md).  

> [!NOTE]
> O nó em espera precisa de acesso a todos os volumes de banco de dados. Os volumes HANA devem ser montados como volumes NFSv4. O mecanismo de bloqueio baseado em concessão de arquivo aprimorado no protocolo NFSv4 é usado para `I/O` isolamento. 

> [!IMPORTANT]
> Para criar a configuração com suporte, você deve implantar os volumes de dados e de log do HANA como volumes NFSv 4.1 e montá-los usando o protocolo NFSv 4.1. A configuração de failover automático do host HANA com o nó em espera não tem suporte com o NFSv3.

![Visão geral da Alta Disponibilidade do SAP NetWeaver](./media/high-availability-guide-suse-anf/sap-hana-scale-out-standby-netapp-files-suse.png)

No diagrama anterior, que segue SAP HANA recomendações de rede, três sub-redes são representadas em uma rede virtual do Azure: 
* Para comunicação do cliente
* Para comunicação com o sistema de armazenamento
* Para comunicação interna do HANA entre nós

Os volumes do Azure NetApp estão em sub-rede separada, [delegados para Azure NetApp files](../../../azure-netapp-files/azure-netapp-files-delegate-subnet.md).  

Para esta configuração de exemplo, as sub-redes são:  

  - `client` 10.23.0.0/24  
  - `storage` 10.23.2.0/24  
  - `hana` 10.23.3.0/24  
  - `anf` 10.23.1.0/26  

## <a name="set-up-the-azure-netapp-files-infrastructure"></a>Configurar a infraestrutura de Azure NetApp Files 

Antes de prosseguir com a instalação para Azure NetApp Files infraestrutura, familiarize-se com a [documentação do Azure NetApp files][anf-azure-doc]. 

Azure NetApp Files está disponível em várias [regiões do Azure](https://azure.microsoft.com/global-infrastructure/services/?products=netapp). Verifique se sua região do Azure selecionada oferece Azure NetApp Files.  

Para obter informações sobre a disponibilidade de Azure NetApp Files pela região do Azure, consulte [Azure NetApp files disponibilidade pela região do Azure][anf-avail-matrix].  

Antes de implantar Azure NetApp Files, solicite a integração com o Azure NetApp Files acessando [instruções de Azure NetApp files][anf-register]. 

### <a name="deploy-azure-netapp-files-resources"></a>Implantar recursos do Azure NetApp Files  

As instruções a seguir pressupõem que você já tenha implantado sua [rede virtual do Azure](../../../virtual-network/virtual-networks-overview.md). Os recursos Azure NetApp Files e as VMs, em que os recursos de Azure NetApp Files serão montados, devem ser implantados na mesma rede virtual do Azure ou em redes virtuais emparelhadas do Azure.  

1. Se você ainda não tiver implantado os recursos, solicite [a integração ao Azure NetApp files](../../../azure-netapp-files/azure-netapp-files-register.md).  

2. Crie uma conta do NetApp na região do Azure selecionada seguindo as instruções em [criar uma conta do NetApp](../../../azure-netapp-files/azure-netapp-files-create-netapp-account.md).  

3. Configure um pool de capacidade Azure NetApp Files seguindo as instruções em [configurar um pool de capacidade Azure NetApp files](../../../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md).  

   A arquitetura do HANA apresentada neste artigo usa um único pool de capacidade de Azure NetApp Files no nível de *ultra serviço* . Para cargas de trabalho do HANA no Azure, é recomendável usar um  [nível de serviço](../../../azure-netapp-files/azure-netapp-files-service-levels.md)Azure NetApp files *ultra* ou Premium.  

4. Delegue uma sub-rede para Azure NetApp Files, conforme descrito nas instruções em [delegar uma sub-rede para Azure NetApp files](../../../azure-netapp-files/azure-netapp-files-delegate-subnet.md).  

5. Implante Azure NetApp Files volumes seguindo as instruções em [criar um volume de NFS para Azure NetApp files](../../../azure-netapp-files/azure-netapp-files-create-volumes.md).  

   Enquanto estiver implantando os volumes, certifique-se de selecionar a versão **nfsv 4.1** . Atualmente, o acesso ao NFSv 4.1 requer que seja adicionado a uma lista de permissões. Implante os volumes na [sub-rede](/rest/api/virtualnetwork/subnets) designada do Azure NetApp Files. Os endereços IP dos volumes do Azure NetApp são atribuídos automaticamente. 
   
   Tenha em mente que os recursos Azure NetApp Files e as VMs do Azure devem estar na mesma rede virtual do Azure ou em redes virtuais emparelhadas do Azure. Por exemplo, **HN1**-data-Mnt00001, **HN1**-log-mnt00001 e assim por diante, são os nomes de volume e NFS://10.23.1.5/**HN1**-data-mnt00001, NFS://10.23.1.4/**HN1**-log-mnt00001 e assim por diante, são os caminhos de arquivo para os volumes de Azure NetApp files.  

   * volume **HN1**-data-mnt00001 (NFS://10.23.1.5/**HN1**-data-mnt00001)
   * volume **HN1**-data-mnt00002 (NFS://10.23.1.6/**HN1**-data-mnt00002)
   * volume **HN1**-log-mnt00001 (NFS://10.23.1.4/**HN1**-log-mnt00001)
   * volume **HN1**-log-mnt00002 (NFS://10.23.1.6/**HN1**-log-mnt00002)
   * volume **HN1**-compartilhado (NFS://10.23.1.4/**HN1**-Shared)
   
   Neste exemplo, usamos um volume Azure NetApp Files separado para cada volume de dados e de log do HANA. Para uma configuração mais otimizada de custo em sistemas menores ou não produtivos, é possível posicionar todas as montagens de dados e todos os logs montados em um único volume.  

### <a name="important-considerations"></a>Considerações importantes

Ao criar seu Azure NetApp Files para o SAP NetWeaver na arquitetura de alta disponibilidade do SUSE, esteja ciente das seguintes considerações importantes:

- O pool de capacidade mínima é 4 tebibytes (TiB).  
- O tamanho mínimo do volume é 100 Gibibytes (GiB).
- Azure NetApp Files e todas as máquinas virtuais em que os volumes de Azure NetApp Files serão montados devem estar na mesma rede virtual do Azure ou em [redes virtuais emparelhadas](../../../virtual-network/virtual-network-peering-overview.md) na mesma região.  
- A rede virtual selecionada deve ter uma sub-rede que é delegada a Azure NetApp Files.
- A taxa de transferência de um volume Azure NetApp Files é uma função da cota de volume e do nível de serviço, conforme documentado no [nível de serviço para Azure NetApp files](../../../azure-netapp-files/azure-netapp-files-service-levels.md). Quando você estiver dimensionando os volumes do Azure NetApp para o HANA, verifique se a taxa de transferência resultante atende aos requisitos do sistema do HANA.  
- Com a [política de exportação](../../../azure-netapp-files/azure-netapp-files-configure-export-policy.md)de Azure NetApp files, você pode controlar os clientes permitidos, o tipo de acesso (leitura/gravação, somente leitura e assim por diante). 
- O recurso de Azure NetApp Files ainda não reconhece a zona. Atualmente, o recurso não é implantado em todas as zonas de disponibilidade em uma região do Azure. Esteja atendo às possíveis implicações de latência em algumas regiões do Azure.  
-  

> [!IMPORTANT]
> Para cargas de trabalho do SAP HANA, baixa latência é fundamental. Trabalhe com seu representante da Microsoft para verificar se as máquinas virtuais e os volumes do Azure NetApp Files foram implantados bem próximos.  

### <a name="sizing-for-hana-database-on-azure-netapp-files"></a>Dimensionar o banco de dados do HANA no Azure NetApp Files

A taxa de transferência de um volume Azure NetApp Files é uma função do tamanho do volume e do nível de serviço, conforme documentado no [nível de serviço para Azure NetApp files](../../../azure-netapp-files/azure-netapp-files-service-levels.md). 

Conforme você projeta a infraestrutura para o SAP no Azure, esteja ciente de alguns requisitos mínimos de armazenamento pelo SAP, que se convertem em características de taxa de transferência mínima:

- Habilite a leitura/gravação em/Hana/log de 250 megabytes por segundo (MB/s) com tamanhos de e/s de 1 MB.  
- Habilite a atividade de leitura de pelo menos 400 MB/s para/Hana/data para os tamanhos de e/s de 16 MB e 64 MB.  
- Habilite a atividade de gravação de pelo menos 250 MB/s para/Hana/data com tamanhos de e/s de 16 MB e 64 MB. 

Os [limites de taxa de transferência do Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-service-levels.md) por 1 TiB de cota de volume são:
- Camada de armazenamento Premium-64 MiB/s  
- Camada de armazenamento ultra-128 MiB/s  

Para atender aos requisitos de taxa de transferência mínima do SAP para dados e log e as diretrizes para/Hana/Shared, os tamanhos recomendados seriam:

| Volume | Tamanho de<br>Camada de armazenamento Premium | Tamanho de<br>Camada de armazenamento Ultra | Protocolo do NFS com suporte |
| --- | --- | --- | --- |
| /hana/log/ | 4 TiB | 2 TiB | v4.1 |
| /hana/data | 6,3 TiB | 3,2 TiB | v4.1 |
| /hana/shared | Max (512 GB, 1xRAM) por 4 nós de trabalho | Max (512 GB, 1xRAM) por 4 nós de trabalho | v3 ou v4.1 |

A configuração de SAP HANA para o layout apresentado neste artigo, usando Azure NetApp Files camada de armazenamento ultra, seria:

| Volume | Tamanho de<br>Camada de armazenamento Ultra | Protocolo do NFS com suporte |
| --- | --- | --- |
| /hana/log/mnt00001 | 2 TiB | v4.1 |
| /hana/log/mnt00002 | 2 TiB | v4.1 |
| /hana/data/mnt00001 | 3,2 TiB | v4.1 |
| /hana/data/mnt00002 | 3,2 TiB | v4.1 |
| /hana/shared | 2 TiB | v3 ou v4.1 |

> [!NOTE]
> As recomendações de dimensionamento Azure NetApp Files declaradas aqui são destinadas a atender aos requisitos mínimos que o SAP recomenda para seus provedores de infraestrutura. Em implantações reais de clientes e cenários de carga de trabalho, esses tamanhos podem não ser suficientes. Use essas recomendações como um ponto de partida e adapte-se, com base nos requisitos de sua carga de trabalho específica.  

> [!TIP]
> Você pode redimensionar Azure NetApp Files volumes dinamicamente, sem precisar *desmontar* os volumes, parar as máquinas virtuais ou parar SAP Hana. Essa abordagem permite flexibilidade para atender às demandas de taxa de transferência esperada e inesperada do seu aplicativo.

## <a name="deploy-linux-virtual-machines-via-the-azure-portal"></a>Implantar máquinas virtuais do Linux por meio do portal do Azure

Primeiro, você precisa criar os volumes do Azure NetApp Files. Em seguida, execute as seguintes etapas:
1. Crie as [sub-redes da rede virtual do Azure](../../../virtual-network/virtual-network-manage-subnet.md) em sua [rede virtual do Azure](../../../virtual-network/virtual-networks-overview.md). 
1. Implante as VMs. 
1. Crie as interfaces de rede adicionais e anexe as interfaces de rede às VMs correspondentes.  

   Cada máquina virtual tem três interfaces de rede, que correspondem às três sub-redes da rede virtual do Azure `client` ( `storage` e `hana` ). 

   Para obter mais informações, consulte [criar uma máquina virtual Linux no Azure com várias placas de interface de rede](../../linux/multiple-nics.md).  

> [!IMPORTANT]
> Para cargas de trabalho do SAP HANA, baixa latência é fundamental. Para obter baixa latência, trabalhe com seu representante da Microsoft para garantir que as máquinas virtuais e os volumes de Azure NetApp Files sejam implantados de perto. Quando estiver integrando o [novo SAP Hana sistema](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxjSlHBUxkJBjmARn57skvdUQlJaV0ZBOE1PUkhOVk40WjZZQVJXRzI2RC4u) que está usando SAP Hana Azure NetApp files, envie as informações necessárias. 
 
As próximas instruções pressupõem que você já criou o grupo de recursos, a rede virtual do Azure e as três sub-redes da rede virtual do Azure: `client` `storage` e `hana` . Ao implantar as VMs, selecione a sub-rede do cliente, para que a interface de rede do cliente seja a interface principal nas VMs. Você também precisará configurar uma rota explícita para a Azure NetApp Files sub-rede delegada por meio do gateway de sub-rede de armazenamento. 

> [!IMPORTANT]
> Verifique se o sistema operacional selecionado é certificado pela SAP para SAP HANA nos tipos específicos de VM que você está usando. Para obter uma lista de tipos de VM certificados SAP HANA e versões de sistema operacional para esses tipos, vá para o site de [plataformas de IaaS certificado SAP Hana](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) . Clique nos detalhes do tipo de VM listado para obter a lista completa de versões de sistema operacional com suporte SAP HANA para esse tipo.  

1. Crie um conjunto de disponibilidade para SAP HANA. Certifique-se de definir o domínio de atualização máximo.  

2. Crie três máquinas virtuais (**hanadb1**, **hanadb2**, **hanadb3**) executando as seguintes etapas:  

   a. Use uma imagem SLES4SAP na galeria do Azure com suporte para SAP HANA. Usamos uma imagem do SLES4SAP 12 SP4 neste exemplo.  

   b. Selecione o conjunto de disponibilidade que você criou anteriormente para SAP HANA.  

   c. Selecione a sub-rede rede virtual do Azure cliente. Selecione [rede acelerada](../../../virtual-network/create-vm-accelerated-networking-cli.md).  

   Quando você implanta as máquinas virtuais, o nome da interface de rede é gerado automaticamente. Nestas instruções para simplificar, vamos nos referir às interfaces de rede geradas automaticamente, que são anexadas à sub-rede de rede virtual do Azure cliente, como **hanadb1-** Client, **hanadb2-** Client e **hanadb3-Client**. 

3. Crie três interfaces de rede, uma para cada máquina virtual, para a `storage` sub-rede da rede virtual (neste exemplo, **hanadb1**-Storage, **hanadb2-Storage** e **hanadb3-Storage**).  

4. Crie três interfaces de rede, uma para cada máquina virtual, para a `hana`  sub-rede de rede virtual (neste exemplo, **hanadb1-Hana**, **hanadb2-Hana** e **hanadb3-Hana**).  

5. Anexe as interfaces de rede virtual recém-criadas às máquinas virtuais correspondentes executando as seguintes etapas:  

    a. Vá para a máquina virtual na [portal do Azure](https://portal.azure.com/#home).  

    b. No painel esquerdo, selecione **máquinas virtuais**. Filtre o nome da máquina virtual (por exemplo, **hanadb1**) e, em seguida, selecione a máquina virtual.  

    c. No painel **visão geral** , selecione **parar** para desalocar a máquina virtual.  

    d. Selecione **rede** e, em seguida, anexe a interface de rede. Na lista suspensa **anexar interface de rede** , selecione as interfaces de rede já criadas para as `storage` sub-redes e `hana` .  
    
    e. Clique em **Salvar**. 
 
    f. Repita as etapas b a e para as máquinas virtuais restantes (em nosso exemplo,  **hanadb2** e **hanadb3**).
 
    g. Deixe as máquinas virtuais no estado parado por enquanto. Em seguida, Habilitaremos a [rede acelerada](../../../virtual-network/create-vm-accelerated-networking-cli.md) para todas as interfaces de rede recentemente anexadas.  

6. Habilite a rede acelerada para as interfaces de rede adicionais para as `storage` sub-redes e seguindo estas `hana` etapas:  

    a. Abra [Azure cloud Shell](https://azure.microsoft.com/features/cloud-shell/) no [portal do Azure](https://portal.azure.com/#home).  

    b. Execute os comandos a seguir para habilitar a rede acelerada para as interfaces de rede adicionais, que são anexadas às `storage` `hana` sub-redes e.  

    <pre><code>
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb1-storage</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb2-storage</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb3-storage</b> --accelerated-networking true
    
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb1-hana</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb2-hana</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb3-hana</b> --accelerated-networking true

    </code></pre>

7. Inicie as máquinas virtuais executando as seguintes etapas:  

    a. No painel esquerdo, selecione **máquinas virtuais**. Filtre o nome da máquina virtual (por exemplo, **hanadb1**) e, em seguida, selecione-o.  

    b. No painel **visão geral** , selecione **Iniciar**.  

## <a name="operating-system-configuration-and-preparation"></a>Configuração e preparação do sistema operacional

As instruções nas próximas seções são prefixadas com um dos seguintes:
* **[A]**: aplicável a todos os nós
* **[1]**: aplicável somente ao nó 1
* **[2]**: aplicável somente ao nó 2
* **[3]**: aplicável somente ao nó 3

Configure e prepare seu sistema operacional executando as seguintes etapas:

1. **[A]** manter os arquivos de host nas máquinas virtuais. Inclua entradas para todas as sub-redes. As entradas a seguir foram adicionadas a `/etc/hosts` para este exemplo.  

    <pre><code>
    # Storage
    10.23.2.4   hanadb1-storage
    10.23.2.5   hanadb2-storage
    10.23.2.6   hanadb3-storage
    # Client
    10.23.0.5   hanadb1
    10.23.0.6   hanadb2
    10.23.0.7   hanadb3
    # Hana
    10.23.3.4   hanadb1-hana
    10.23.3.5   hanadb2-hana
    10.23.3.6   hanadb3-hana
    </code></pre>

2. **[A]** altere as configurações de DHCP e de configuração de nuvem para o adaptador de rede para armazenamento para evitar alterações de nome de host não intencional.  

    As instruções a seguir pressupõem que a interface de rede de armazenamento é `eth1` . 

    <pre><code>
    vi /etc/sysconfig/network/dhcp
    # Change the following DHCP setting to "no"
    DHCLIENT_SET_HOSTNAME="no"
    vi /etc/sysconfig/network/ifcfg-<b>eth1</b>
    # Edit ifcfg-eth1 
    #Change CLOUD_NETCONFIG_MANAGE='yes' to "no"
    CLOUD_NETCONFIG_MANAGE='no'
    </code></pre>

2. **[A]** adicionar uma rota de rede, para que a comunicação com o Azure NetApp files seja por meio da interface de rede de armazenamento.  

    As instruções a seguir pressupõem que a interface de rede de armazenamento é `eth1` .  

    <pre><code>
    vi /etc/sysconfig/network/ifroute-<b>eth1</b>
    # Add the following routes 
    # RouterIPforStorageNetwork - - -
    # ANFNetwork/cidr RouterIPforStorageNetwork - -
    <b>10.23.2.1</b> - - -
    <b>10.23.1.0/26</b> <b>10.23.2.1</b> - -
    </code></pre>

    Reinicialize a VM para ativar as alterações.  

3. **[A]** preparar o sistema operacional para executar o SAP Hana em sistemas NetApp com NFS, conforme descrito em [aplicativos SAP da NetApp em Microsoft Azure usando Azure NetApp files][anf-sap-applications-azure]. Crie o arquivo de configuração */etc/sysctl.d/NetApp-Hana.conf* para as definições de configuração do NetApp.  

    <pre><code>
    vi /etc/sysctl.d/netapp-hana.conf
    # Add the following entries in the configuration file
    net.core.rmem_max = 16777216
    net.core.wmem_max = 16777216
    net.core.rmem_default = 16777216
    net.core.wmem_default = 16777216
    net.core.optmem_max = 16777216
    net.ipv4.tcp_rmem = 65536 16777216 16777216
    net.ipv4.tcp_wmem = 65536 16777216 16777216
    net.core.netdev_max_backlog = 300000
    net.ipv4.tcp_slow_start_after_idle=0
    net.ipv4.tcp_no_metrics_save = 1
    net.ipv4.tcp_moderate_rcvbuf = 1
    net.ipv4.tcp_window_scaling = 1
    net.ipv4.tcp_timestamps = 1
    net.ipv4.tcp_sack = 1
    </code></pre>

4. **[A]** criar arquivo de configuração */etc/sysctl.d/MS-AZ.conf* com a Microsoft para as definições de configuração do Azure.  

    <pre><code>
    vi /etc/sysctl.d/ms-az.conf
    # Add the following entries in the configuration file
    ipv6.conf.all.disable_ipv6 = 1
    net.ipv4.tcp_max_syn_backlog = 16348
    net.ipv4.conf.all.rp_filter = 0
    sunrpc.tcp_slot_table_entries = 128
    vm.swappiness=10
    </code></pre>

> [!TIP]
> Evite configurar net.ipv4.ip_local_port_range e net.ipv4.ip_local_reserved_ports explicitamente nos arquivos de configuração do sysctl para permitir que o agente de host do SAP gerencie os intervalos de porta. Para obter mais detalhes, consulte a observação do SAP [2382421](https://launchpad.support.sap.com/#/notes/2382421).  

4. **[A]** ajuste as configurações de SunRPC, conforme recomendado nos [aplicativos SAP da NetApp em Microsoft Azure usando Azure NetApp files][anf-sap-applications-azure].  

    <pre><code>
    vi /etc/modprobe.d/sunrpc.conf
    # Insert the following line
    options sunrpc tcp_max_slot_table_entries=128
    </code></pre>

## <a name="mount-the-azure-netapp-files-volumes"></a>Montar os volumes de Azure NetApp Files

1. **[A]** criar pontos de montagem para os volumes de banco de dados do Hana.  

    <pre><code>
    mkdir -p /hana/data/<b>HN1</b>/mnt00001
    mkdir -p /hana/data/<b>HN1</b>/mnt00002
    mkdir -p /hana/log/<b>HN1</b>/mnt00001
    mkdir -p /hana/log/<b>HN1</b>/mnt00002
    mkdir -p /hana/shared
    mkdir -p /usr/sap/<b>HN1</b>
    </code></pre>

2. **[1]** crie diretórios específicos de nó para/usr/SAP em **HN1**-Shared.  

    <pre><code>
    # Create a temporary directory to mount <b>HN1</b>-shared
    mkdir /mnt/tmp
    # if using NFSv3 for this volume, mount with the following command
    mount <b>10.23.1.4</b>:/<b>HN1</b>-shared /mnt/tmp
    # if using NFSv4.1 for this volume, mount with the following command
    mount -t nfs -o sec=sys,vers=4.1 <b>10.23.1.4</b>:/<b>HN1</b>-shared /mnt/tmp
    cd /mnt/tmp
    mkdir shared usr-sap-<b>hanadb1</b> usr-sap-<b>hanadb2</b> usr-sap-<b>hanadb3</b>
    # unmount /hana/shared
    cd
    umount /mnt/tmp
    </code></pre>

3. **[A]** verificar a configuração de domínio do NFS. Verifique se o domínio está configurado como o domínio de Azure NetApp Files padrão, ou seja, **`defaultv4iddomain.com`** e o mapeamento está definido como **ninguém**.  

    > [!IMPORTANT]
    > É preciso que você defina o domínio NFS em `/etc/idmapd.conf` na VM para corresponder à configuração de domínio padrão no Azure NetApp Files: **`defaultv4iddomain.com`** . Se houver uma incompatibilidade entre a configuração de domínio no cliente NFS (ou seja, a VM) e o servidor NFS, ou seja, a configuração da Azure NetApp, as permissões para arquivos nos volumes do Azure NetApp que forem montados nas VMs serão exibidas como `nobody`.  

    <pre><code>
    sudo cat /etc/idmapd.conf
    # Example
    [General]
    Verbosity = 0
    Pipefs-Directory = /var/lib/nfs/rpc_pipefs
    Domain = <b>defaultv4iddomain.com</b>
    [Mapping]
    Nobody-User = <b>nobody</b>
    Nobody-Group = <b>nobody</b>
    </code></pre>

4. **[A]** Verifique `nfs4_disable_idmapping`. Ele deve ser definido como **Y**. Para criar a estrutura de diretório em que `nfs4_disable_idmapping` está localizado, execute o comando mount. Você não poderá criar o diretório manualmente em /sys/modules, pois o acesso é reservado para o kernel e os drivers.  

    <pre><code>
    # Check nfs4_disable_idmapping 
    cat /sys/module/nfs/parameters/nfs4_disable_idmapping
    # If you need to set nfs4_disable_idmapping to Y
    mkdir /mnt/tmp
    mount 10.23.1.4:/HN1-shared /mnt/tmp
    umount  /mnt/tmp
    echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    # Make the configuration permanent
    echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
    </code></pre>

5. **[A]** crie o grupo de SAP Hana e o usuário manualmente. As IDs para os SAPs de grupo e o usuário **hn1** ADM devem ser definidas com as mesmas IDs, que são fornecidas durante a integração. (Neste exemplo, as IDs são definidas como **1001**.) Se as IDs não estiverem definidas corretamente, você não poderá acessar os volumes. As IDs para grupos de SAPs e contas de usuário **hn1** ADM e sapadm devem ser as mesmas em todas as máquinas virtuais.  

    <pre><code>
    # Create user group 
    sudo groupadd -g 1001 sapsys
    # Create  users 
    sudo useradd <b>hn1</b>adm -u 1001 -g 1001 -d /usr/sap/<b>HN1</b>/home -c "SAP HANA Database System" -s /bin/sh
    sudo useradd sapadm -u 1002 -g 1001 -d /home/sapadm -c "SAP Local Administrator" -s /bin/sh
    # Set the password  for both user ids
    sudo passwd hn1adm
    sudo passwd sapadm
    </code></pre>

6. **[A]** montar os volumes de Azure NetApp files compartilhados.  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.5:/<b>HN1</b>-data-mnt00001 /hana/data/<b>HN1</b>/mnt00001  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.23.1.6:/<b>HN1</b>-data-mnt00002 /hana/data/<b>HN1</b>/mnt00002  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.23.1.4:/<b>HN1</b>-log-mnt00001 /hana/log/<b>HN1</b>/mnt00001  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.23.1.6:/<b>HN1</b>-log-mnt00002 /hana/log/HN1/mnt00002  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.23.1.4:/<b>HN1</b>-shared/shared /hana/shared  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount all volumes
    sudo mount -a 
    </code></pre>

7. **[1]** montar os volumes específicos do nó em **hanadb1**.  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb1</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

8. **[2]** montar os volumes específicos do nó em **hanadb2**.  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb2</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

9. **[3]** montar os volumes específicos do nó em **hanadb3**.  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb3</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

10. **[A]** Verifique se todos os volumes do Hana estão montados com o protocolo NFS versão **NFSv4**.  

    <pre><code>
    sudo nfsstat -m
    # Verify that flag vers is set to <b>4.1</b> 
    # Example from <b>hanadb1</b>
    /hana/data/<b>HN1</b>/mnt00001 from 10.23.1.5:/<b>HN1</b>-data-mnt00001
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.5
    /hana/log/<b>HN1</b>/mnt00002 from 10.23.1.6:/<b>HN1</b>-log-mnt00002
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.6
    /hana/data/<b>HN1</b>/mnt00002 from 10.23.1.6:/<b>HN1</b>-data-mnt00002
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.6
    /hana/log/<b>HN1</b>/mnt00001 from 10.23.1.4:/<b>HN1</b>-log-mnt00001
    Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.4
    /usr/sap/<b>HN1</b> from 10.23.1.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb1</b>
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.4
    /hana/shared from 10.23.1.4:/<b>HN1</b>-shared/shared
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.4
    </code></pre>

## <a name="installation"></a>Instalação  

Neste exemplo para implantar SAP HANA na configuração de expansão com o nó em espera com o Azure, usamos o HANA 2,0 SP4.  

### <a name="prepare-for-hana-installation"></a>Preparar para a instalação do HANA

1. **[A]** antes da instalação do Hana, defina a senha raiz. Você pode desabilitar a senha raiz após a conclusão da instalação. Comando execute `root` as `passwd` .  

2. **[1]** Verifique se você pode fazer logon via SSH para **hanadb2** e **hanadb3**, sem ser solicitado a fornecer uma senha.  

    <pre><code>
    ssh root@<b>hanadb2</b>
    ssh root@<b>hanadb3</b>
    </code></pre>

3. **[A]** instale pacotes adicionais, que são necessários para o Hana 2,0 SP4. Para obter mais informações, consulte SAP Note [2593824](https://launchpad.support.sap.com/#/notes/2593824). 

    <pre><code>
    sudo zypper install libgcc_s1 libstdc++6 libatomic1 
    </code></pre>

4. **[2], [3]** Altere a propriedade de SAP HANA `data` e `log` diretórios para **hn1** ADM.   

    <pre><code>
    # Execute as root
    sudo chown hn1adm:sapsys /hana/data/<b>HN1</b>
    sudo chown hn1adm:sapsys /hana/log/<b>HN1</b>
    </code></pre>

### <a name="hana-installation"></a>Instalação do HANA

1. **[1]** instale o SAP Hana seguindo as instruções no [Guia de instalação e atualização do SAP Hana 2,0](https://help.sap.com/viewer/2c1988d620e04368aa4103bf26f17727/2.0.04/en-US/7eb0167eb35e4e2885415205b8383584.html). Neste exemplo, instalamos SAP HANA expansão com Master, um Worker e um nó em espera.  

   a. Inicie o programa **hdblcm** do diretório de software de instalação do Hana. Use o `internal_network` parâmetro e passe o espaço de endereço para sub-rede, que é usado para a comunicação interna do Hana entre nós.  

    <pre><code>
    ./hdblcm --internal_network=10.23.3.0/24
    </code></pre>

   b. No prompt, insira os seguintes valores:

     * Para **escolher uma ação**: insira **1** (para instalar)
     * Para obter **componentes adicionais para a instalação**: insira **2, 3**
     * Para o caminho de instalação: pressione Enter (o padrão é/Hana/Shared)
     * Para **nome do host local**: pressione ENTER para aceitar o padrão
     * Em deseja **adicionar hosts ao sistema?**: digite **y**
     * Para **Adicionar nomes de host separados por vírgulas**: insira **hanadb2, hanadb3**
     * Para o **nome de usuário raiz** [raiz]: pressione ENTER para aceitar o padrão
     * Para a **senha do usuário raiz**: Insira a senha do usuário raiz
     * Para funções para o host hanadb2: insira **1**  (para o trabalho)
     * Para o **grupo de failover de host** para o host hanadb2 [padrão]: pressione ENTER para aceitar o padrão
     * Para o **número de partição de armazenamento** para o host hanadb2 [<<assign automatically>>]: pressione ENTER para aceitar o padrão
     * Para o **grupo de trabalho** para o host hanadb2 [padrão]: pressione ENTER para aceitar o padrão
     * Para **selecionar funções** para o host hanadb3: insira **2** (para espera)
     * Para o **grupo de failover de host** para o host hanadb3 [padrão]: pressione ENTER para aceitar o padrão
     * Para o **grupo de trabalho** para o host hanadb3 [padrão]: pressione ENTER para aceitar o padrão
     * Para **SAP Hana ID do sistema**: insira **HN1**
     * Para **número de instância** [00]: insira **03**
     * Para o **grupo de trabalho do host local** [padrão]: pressione ENTER para aceitar o padrão
     * Para **selecionar uso do sistema/inserir índice [4]**: insira **4** (para personalizado)
     * Para o **local dos volumes de dados** [/Hana/data/HN1]: pressione ENTER para aceitar o padrão
     * Para o **local dos volumes de log** [/Hana/log/HN1]: pressione ENTER para aceitar o padrão
     * Para **restringir a alocação máxima de memória?** [n]: insira **n**
     * Para o **nome do host do certificado para o host hanadb1** [hanadb1]: pressione ENTER para aceitar o padrão
     * Para o **nome do host do certificado para o host hanadb2** [hanadb2]: pressione ENTER para aceitar o padrão
     * Para o **nome do host do certificado para o host hanadb3** [hanadb3]: pressione ENTER para aceitar o padrão
     * Para a **senha do administrador do sistema (hn1adm)**: Insira a senha
     * Para a **senha do usuário do banco de dados do sistema (sistema)**: Insira a senha do sistema
     * Para **Confirmar senha do usuário do banco de dados do sistema (sistema)**: Insira a senha do sistema
     * Para **reiniciar o sistema após a reinicialização do computador?** [n]: insira **n** 
     * Para **continuar (s/n)**: validar o resumo e, se tudo estiver correto, digite **y**


2. **[1]** verificar global.ini  

   Exiba global.ini e verifique se a configuração da comunicação interna SAP HANA entre nós está em vigor. Verifique a seção de **comunicação** . Ele deve ter o espaço de endereço para a `hana` sub-rede e `listeninterface` deve ser definido como `.internal` . Verifique a seção **internal_hostname_resolution** . Ele deve ter os endereços IP para as máquinas virtuais HANA que pertencem à `hana` sub-rede.  

   <pre><code>
    sudo cat /usr/sap/<b>HN1</b>/SYS/global/hdb/custom/config/global.ini
    # Example 
    #global.ini last modified 2019-09-10 00:12:45.192808 by hdbnameserve
    [communication]
    internal_network = <b>10.23.3/24</b>
    listeninterface = .internal
    [internal_hostname_resolution]
    <b>10.23.3.4</b> = <b>hanadb1</b>
    <b>10.23.3.5</b> = <b>hanadb2</b>
    <b>10.23.3.6</b> = <b>hanadb3</b>
   </code></pre>

3. **[1]** adicionar mapeamento de host para garantir que os endereços IP do cliente sejam usados para comunicação do cliente. Adicione `public_host_resolution` a seção e adicione os endereços IP correspondentes da sub-rede do cliente.  

   <pre><code>
    sudo vi /usr/sap/HN1/SYS/global/hdb/custom/config/global.ini
    #Add the section
    [public_hostname_resolution]
    map_<b>hanadb1</b> = <b>10.23.0.5</b>
    map_<b>hanadb2</b> = <b>10.23.0.6</b>
    map_<b>hanadb3</b> = <b>10.23.0.7</b>
   </code></pre>

4. **[1]** reinicie SAP Hana para ativar as alterações.  

   <pre><code>
    sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StopSystem HDB
    sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StartSystem HDB
   </code></pre>

5. **[1]** Verifique se a interface do cliente usará os endereços IP da `client` sub-rede para comunicação.  

   <pre><code>
    sudo -u hn1adm /usr/sap/HN1/HDB03/exe/hdbsql -u SYSTEM -p "<b>password</b>" -i 03 -d SYSTEMDB 'select * from SYS.M_HOST_INFORMATION'|grep net_publicname
    # Expected result
    "<b>hanadb3</b>","net_publicname","<b>10.23.0.7</b>"
    "<b>hanadb2</b>","net_publicname","<b>10.23.0.6</b>"
    "<b>hanadb1</b>","net_publicname","<b>10.23.0.5</b>"
   </code></pre>

   Para obter informações sobre como verificar a configuração, consulte SAP Note [2183363 – configuração de SAP Hana rede interna](https://launchpad.support.sap.com/#/notes/2183363).  

6. Para otimizar SAP HANA para o armazenamento de Azure NetApp Files subjacente, defina os seguintes parâmetros de SAP HANA:

   - `max_parallel_io_requests`**128**
   - `async_read_submit`**em**
   - `async_write_submit_active`**em**
   - `async_write_submit_blocks`**todos**

   Para obter mais informações, consulte [aplicativos SAP da NetApp em Microsoft Azure usando Azure NetApp files][anf-sap-applications-azure]. 

   A partir dos sistemas SAP HANA 2,0, você pode definir os parâmetros em `global.ini` . Para obter mais informações, consulte SAP Note [1999930](https://launchpad.support.sap.com/#/notes/1999930).  
   
   Para os sistemas SAP HANA 1,0 versões SPS12 e anteriores, esses parâmetros podem ser definidos durante a instalação, conforme descrito em SAP Note [2267798](https://launchpad.support.sap.com/#/notes/2267798).  

7. O armazenamento usado pelo Azure NetApp Files tem uma limitação de tamanho de arquivo de 16 terabytes (TB). SAP HANA não está ciente implicitamente da limitação de armazenamento e não criará automaticamente um novo arquivo de dados quando o limite de tamanho de arquivo de 16 TB for atingido. Como SAP HANA tenta aumentar o arquivo além de 16 TB, essa tentativa resultará em erros e, eventualmente, em uma falha do servidor de índice. 

   > [!IMPORTANT]
   > Para impedir que SAP HANA tentem aumentar os arquivos de dados além do [limite de 16 TB](../../../azure-netapp-files/azure-netapp-files-resource-limits.md) do subsistema de armazenamento, defina os parâmetros a seguir em `global.ini` .  
   > - datavolume_striping = true
   > - datavolume_striping_size_gb = 15000 para obter mais informações, consulte SAP Note [2400005](https://launchpad.support.sap.com/#/notes/2400005).
   > Lembre-se do SAP Note [2631285](https://launchpad.support.sap.com/#/notes/2631285). 

## <a name="test-sap-hana-failover"></a>Testar failover de SAP HANA 

> [!NOTE]
> Este artigo contém referências aos termos *mestre* e *subordinado*, termos que a Microsoft não usa mais. Quando esses termos forem removidos do software, nós os removeremos deste artigo.

1. Simular uma falha de nó em um nó de trabalho SAP HANA. Faça o seguinte: 

   a. Antes de simular a falha do nó, execute os seguintes comandos como **hn1** ADM para capturar o status do ambiente:  

   <pre><code>
    # Check the landscape status
    python /usr/sap/<b>HN1</b>/HDB<b>03</b>/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | yes    | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |
    # Check the instance status
    sapcontrol -nr <b>03</b>  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
   </code></pre>

   b. Para simular uma falha de nó, execute o seguinte comando como raiz no nó de trabalho, que é **hanadb2** neste caso:  
   
   <pre><code>
    echo b > /proc/sysrq-trigger
   </code></pre>

   c. Monitore o sistema para a conclusão do failover. Quando o failover for concluído, Capture o status, que deve ser semelhante ao seguinte:  

    <pre><code>
    # Check the instance status
    sapcontrol -nr <b>03</b>  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GRAY
    # Check the landscape status
    /usr/sap/HN1/HDB03/exe/python_support> python landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | no     | info   |          |        |         2 |         0 | default  | default  | master 2   | slave      | worker      | standby     | worker  | standby | default | -       |
    | hanadb3 | yes    | info   |          |        |         0 |         2 | default  | default  | master 3   | slave      | standby     | slave       | standby | worker  | default | default |
   </code></pre>

   > [!IMPORTANT]
   > Quando um nó experimenta um pane no kernel, evite atrasos com o failover de SAP HANA definindo `kernel.panic` como 20 segundos em *todas as* máquinas virtuais do Hana. A configuração é feita no `/etc/sysctl` . Reinicialize as máquinas virtuais para ativar a alteração. Se essa alteração não for executada, o failover poderá levar 10 ou mais minutos quando um nó estiver experimentando um pane no kernel.  

2. Elimine o servidor de nomes fazendo o seguinte:

   a. Antes do teste, verifique o status do ambiente executando os seguintes comandos como **hn1** ADM:  

   <pre><code>
    #Landscape status 
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | no     | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GRAY
   </code></pre>

   b. Execute os comandos a seguir como **hn1** ADM no nó mestre ativo, que é **hanadb1** neste caso:  

    <pre><code>
        hn1adm@hanadb1:/usr/sap/HN1/HDB03> HDB kill
    </code></pre>
    
    O nó em espera **hanadb3** assumirá o nó mestre. Este é o estado do recurso após a conclusão do teste de failover:  

    <pre><code>
        # Check the instance status
        sapcontrol -nr 03 -function GetSystemInstanceList
        GetSystemInstanceList
        OK
        hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
        hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
        hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GRAY
        hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
        # Check the landscape status
        python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
        | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
        |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
        |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
        | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
        | hanadb1 | no     | info   |          |        |         1 |         0 | default  | default  | master 1   | slave      | worker      | standby     | worker  | standby | default | -       |
        | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
        | hanadb3 | yes    | info   |          |        |         0 |         1 | default  | default  | master 3   | master     | standby     | master      | standby | worker  | default | default |
    </code></pre>

   c. Reinicie a instância do HANA em **hanadb1** (ou seja, na mesma máquina virtual, em que o servidor de nomes foi eliminado). O nó **hanadb1** se reassociará ao ambiente e manterá sua função em espera.  

   <pre><code>
    hn1adm@hanadb1:/usr/sap/HN1/HDB03> HDB start
   </code></pre>

   Depois que SAP HANA for iniciado em **hanadb1**, espere o seguinte status:  

   <pre><code>
    # Check the instance status
    sapcontrol -nr 03 -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
    # Check the landscape status
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | info   |          |        |         1 |         0 | default  | default  | master 1   | slave      | worker      | standby     | worker  | standby | default | -       |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | yes    | info   |          |        |         0 |         1 | default  | default  | master 3   | master     | standby     | master      | standby | worker  | default | default |
   </code></pre>

   d. Novamente, elimine o servidor de nomes no nó mestre ativo no momento (ou seja, no nó **hanadb3**).  
   
   <pre><code>
    hn1adm@hanadb3:/usr/sap/HN1/HDB03> HDB kill
   </code></pre>

   O nó **hanadb1** reiniciará a função do nó mestre. Após a conclusão do teste de failover, o status terá esta aparência:

   <pre><code>
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList & python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GRAY
    # Check the landscape status
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | no     | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |
   </code></pre>

   e. Inicie o SAP HANA no **hanadb3**, que estará pronto para servir como um nó em espera.  

   <pre><code>
    hn1adm@hanadb3:/usr/sap/HN1/HDB03> HDB start
   </code></pre>

   Depois que SAP HANA foi iniciado em **hanadb3**, o status é semelhante ao seguinte:  

   <pre><code>
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList & python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GRAY
    # Check the landscape status
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | no     | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |
   </code></pre>

## <a name="next-steps"></a>Próximas etapas

* [Planejamento e implementação de Máquinas Virtuais do Azure para o SAP][planning-guide]
* [Implantação de Máquinas Virtuais do Azure para SAP][deployment-guide]
* [Implantação do DBMS de Máquinas Virtuais do Azure para SAP][dbms-guide]
* [Volumes NFS v4.1 no Azure NetApp Files para SAP HANA](./hana-vm-operations-netapp.md)
* Para saber como estabelecer alta disponibilidade e planejar a recuperação de desastre de SAP HANA em VMs do Azure, consulte [alta disponibilidade de SAP Hana em VMS (máquinas virtuais) do Azure][sap-hana-ha].