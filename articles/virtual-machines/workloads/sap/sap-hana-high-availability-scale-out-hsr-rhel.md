---
title: Expansão de SAP HANA com HSR e pacemaker no RHEL | Microsoft Docs
description: Expansão de SAP HANA com HSR e pacemaker no RHEL
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
ms.openlocfilehash: dd000ca36bb0ad586b973b2bcdc638d8992b1a73
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101668632"
---
# <a name="high-availability-of-sap-hana-scale-out-system-on-red-hat-enterprise-linux"></a>Alta disponibilidade de SAP HANA sistema de expansão em Red Hat Enterprise Linux 

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
[2292690]:https://launchpad.support.sap.com/#/notes/2292690
[2455582]:https://launchpad.support.sap.com/#/notes/2455582
[2593824]:https://launchpad.support.sap.com/#/notes/2593824
[2009879]:https://launchpad.support.sap.com/#/notes/2009879

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md


Este artigo descreve como implantar um sistema de SAP HANA altamente disponível em uma configuração de expansão com HSR (replicação de sistema do HANA) e pacemaker no Azure Red Hat Enterprise Linux máquinas virtuais (VMs). Os sistemas de arquivos compartilhados na arquitetura apresentada são fornecidos pelo [Azure NetApp files](../../../azure-netapp-files/azure-netapp-files-introduction.md) e são montados via NFS.  

Nas configurações de exemplo, comandos de instalação e assim por diante, a instância do HANA é **03** e a ID do sistema Hana é **HN1**. Os exemplos são baseados em HANA 2,0 SP4 e Red Hat Enterprise Linux para SAP 7,6. 

Antes de começar, consulte as seguintes notas e documentos do SAP:

* A nota SAP [1928533] inclui:  
  * Uma lista de tamanhos de VM do Azure com suporte para a implantação de software SAP
  * Informações importantes sobre capacidade para tamanhos de VM do Azure
  * Software SAP e combinações de SO (sistema operacional) e banco de dados com suporte
  * A versão de kernel do SAP necessária para Windows e Linux no Microsoft Azure
* Observação SAP [2015553]: lista os pré-requisitos para implantações de software SAP com suporte do SAP no Azure
* Observação SAP [2002167] tem configurações do sistema operacional recomendadas para Red Hat Enterprise Linux
* Nota SAP [2009879] tem diretrizes SAP HANA para Red Hat Enterprise Linux
* Nota SAP [2178632]: contém informações detalhadas sobre todas as métricas de monitoramento relatadas para SAP no Azure
* Nota SAP [2191498]: contém a versão necessária do agente de host do SAP para Linux no Azure
* Nota SAP [2243692]: contém informações sobre o licenciamento SAP no Linux no Azure
* Nota SAP [1999351]: contém informações adicionais de solução de problemas para a extensão de monitoramento avançado do Azure para SAP
* Nota SAP [1900823]: contém informações sobre os requisitos de armazenamento de SAP Hana
* [Wiki da Comunidade SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes): contém todas as notas SAP necessárias para o Linux
* [Planejamento e implementação de Máquinas Virtuais do Azure para SAP no Linux][planning-guide]
* [Implantação de Máquinas Virtuais do Azure para SAP no Linux][deployment-guide]
* [Implantação de Máquinas Virtuais do Azure do DBMS para SAP no Linux][dbms-guide]
* [Requisitos de rede SAP HANA](https://www.sap.com/documents/2016/08/1cd2c2fb-807c-0010-82c7-eda71af511fa.html)
* Documentação geral do RHEL
  * [Visão geral do complemento de alta disponibilidade](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Administração de complemento de alta disponibilidade](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Referência de complemento de alta disponibilidade](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
  * [Guia de rede Red Hat Enterprise Linux](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/networking_guide)
  * [Como fazer configurar SAP HANA replicação do sistema Scale-Out em um cluster pacemaker com os sistemas de arquivos do HANA em compartilhamentos NFS](https://access.redhat.com/solutions/5423971)
* Documentação do RHEL específica do Azure:
  * [Instalar o SAP HANA no Red Hat Enterprise Linux para uso no Microsoft Azure](https://access.redhat.com/public-cloud/microsoft-azure)
  * [Solução Red Hat Enterprise Linux para SAP HANA Scale-Out e replicação do sistema](https://access.redhat.com/solutions/4386601)
* [Aplicativos SAP NetApp no Microsoft Azure usando o Azure NetApp Files][anf-sap-applications-azure]
* [Documentação do Azure NetApp Files][anf-azure-doc] 
* [Volumes NFS v4.1 no Azure NetApp Files para SAP HANA](./hana-vm-operations-netapp.md)

## <a name="overview"></a>Visão geral

Um método para obter a alta disponibilidade do HANA para instalações de expansão do HANA é configurar a replicação do sistema HANA e proteger a solução com o cluster pacemaker para permitir o failover automático. Quando um nó ativo falha, o cluster executa o failover dos recursos do HANA para o outro site.  
A configuração apresentada mostra três nós do HANA em cada site, além do nó do maior fabricante para evitar o cenário de divisão-Brain. As instruções podem ser adaptadas para incluir mais VMs como nós de BD do HANA.  

O sistema de arquivos compartilhado do HANA `/hana/shared` na arquitetura apresentada é fornecido pelo [Azure NetApp files](../../../azure-netapp-files/azure-netapp-files-introduction.md). Ele é montado por meio do NFSv 4.1 em cada nó do HANA no mesmo site de replicação do sistema do HANA. Sistemas de arquivos `/hana/data` e `/hana/log` sistemas de arquivos locais e não são compartilhados entre os nós do BD Hana. SAP HANA será instalado no modo não compartilhado. 

> [!TIP]
> Para configurações de armazenamento SAP HANA recomendadas, consulte [SAP Hana configurações de armazenamento de VMs do Azure](./hana-vm-operations-storage.md).   

[![Expansão de SAP HANA com o cluster HSR e pacemaker](./media/sap-hana-high-availability-rhel/sap-hana-high-availability-scale-out-hsr-rhel.png)](./media/sap-hana-high-availability-rhel/sap-hana-high-availability-scale-out-hsr-rhel-detail.png#lightbox)

No diagrama anterior, três sub-redes são representadas em uma rede virtual do Azure, seguindo as recomendações de rede SAP HANA: 
* para comunicação do cliente- `client` 10.23.0.0/24  
* para comunicação interna do HANA entre nós- `inter` 10.23.1.128/26  
* para replicação de sistema do HANA- `hsr` 10.23.1.192/26  

Como `/hana/data` e `/hana/log` são implantados em discos locais, não é necessário implantar uma sub-rede separada e separar placas de rede virtual para comunicação com o armazenamento.  

Os volumes do Azure NetApp são implantados em uma sub-rede separada, [delegado para Azure NetApp Files] ( https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet: `anf` 10.23.1.0/26.   

## <a name="set-up-the-infrastructure"></a>Configurar a infraestrutura

Nas instruções a seguir, presumimos que você já criou o grupo de recursos, a rede virtual do Azure com três sub-redes de rede do Azure: `client` `inter` e `hsr` .

### <a name="deploy-linux-virtual-machines-via-the-azure-portal"></a>Implantar máquinas virtuais do Linux por meio do portal do Azure
1. Implante as VMs do Azure.  
Para a configuração apresentada neste documento, implante sete máquinas virtuais: 
   - três máquinas virtuais para servir como nós de banco de BD do HANA para o site 1 da replicação do HANA: **Hana-S1-DB1**, **Hana-S1-DB2** e **Hana-S1-DB3**  
   - três máquinas virtuais para servir como nós de BD do HANA para o site 2 de replicação do HANA: **Hana-S2-DB1**, **Hana-S2-DB2** e **Hana-S2-DB3**  
   - uma pequena máquina virtual para servir como o *principal fabricante*: **Hana-s-mm**

   As VMs, implantadas como nós do SAP BD HANA, devem ser certificadas pelo SAP para HANA, conforme publicado no [diretório de Hardware SAP Hana](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Ao implantar os nós do HANA DB, verifique se a [rede acelerada](../../../virtual-network/create-vm-accelerated-networking-cli.md) está selecionada.  
  
   Para o nó do fabricante principal, você pode implantar uma VM pequena, pois essa VM não executa nenhum dos recursos de SAP HANA. A VM de criador de maioria é usada na configuração do cluster para obter um número ímpar de nós de cluster em um cenário de divisão-cérebro. A VM de maior fabricante precisa apenas de uma interface de rede virtual na `client` sub-rede neste exemplo.        

   Implantar discos gerenciados locais para o `/hana/data` e o `/hana/log` . A configuração de armazenamento mínima recomendada para o `/hana/data` e `/hana/log` é descrita em [SAP Hana configurações de armazenamento de VMs do Azure](./hana-vm-operations-storage.md).

   Implante a interface de rede primária para cada VM na `client` sub-rede da rede virtual.  
   Quando a VM é implantada via portal do Azure, o nome da interface de rede é gerado automaticamente. Nestas instruções para simplificar, vamos nos referir às interfaces de rede primárias geradas automaticamente, que são anexadas à `client` sub-rede da rede virtual do Azure como **Hana-S1-DB1-Client**, **Hana-S1-DB2-Client**, **Hana-S1-DB3-Client** e assim por diante.  


   > [!IMPORTANT]
   > Verifique se o sistema operacional selecionado é certificado pela SAP para SAP HANA nos tipos específicos de VM que você está usando. Para obter uma lista de tipos de VM certificados SAP HANA e versões de sistema operacional para esses tipos, vá para o site de [plataformas de IaaS certificado SAP Hana](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) . Clique nos detalhes do tipo de VM listado para obter a lista completa de versões de sistema operacional com suporte SAP HANA para esse tipo.  
  

2. Crie seis interfaces de rede, uma para cada máquina virtual do HANA DB, na `inter` sub-rede da rede virtual (neste exemplo **, Hana-S1-DB1-inter**, **Hana-S1-DB2-inter**, **Hana-S1-DB3-inter**, **Hana-S2-DB1-inter**, **Hana-S2-DB2-inter** e **Hana-S2-DB3-inter**).  

3. Crie seis interfaces de rede, uma para cada máquina virtual do HANA DB, na `hsr` sub-rede da rede virtual (neste exemplo, **Hana-S1-DB1-HSR**, **Hana-S1-DB2-HSR**, **Hana-S1-DB3-HSR**, **Hana-S2-DB1-HSR**, **Hana-S2-DB2-HSR** e **Hana-S2-DB3-HSR**).  

4. Anexe as interfaces de rede virtual recém-criadas às máquinas virtuais correspondentes:  

    a. Vá para a máquina virtual na [portal do Azure](https://portal.azure.com/#home).  

    b. No painel esquerdo, selecione **máquinas virtuais**. Filtre o nome da máquina virtual (por exemplo, **Hana-S1-DB1**) e, em seguida, selecione a máquina virtual.  

    c. No painel **visão geral** , selecione **parar** para desalocar a máquina virtual.  

    d. Selecione **rede** e, em seguida, anexe a interface de rede. Na lista suspensa **anexar interface de rede** , selecione as interfaces de rede já criadas para as `inter` sub-redes e `hsr` .  
    
    e. Clique em **Salvar**. 
 
    f. Repita as etapas b a e para as máquinas virtuais restantes (em nosso exemplo,  **Hana-S1-DB2**, **Hana-S1-DB3**, **Hana-S2-DB1**, **Hana-S2-DB2** e **Hana-S2-DB3**).
 
    g. Deixe as máquinas virtuais no estado parado por enquanto. Em seguida, Habilitaremos a [rede acelerada](../../../virtual-network/create-vm-accelerated-networking-cli.md) para todas as interfaces de rede recentemente anexadas.  

5. Habilite a rede acelerada para as interfaces de rede adicionais para as `inter` sub-redes e seguindo estas `hsr` etapas:  

    a. Abra [Azure cloud Shell](https://azure.microsoft.com/features/cloud-shell/) no [portal do Azure](https://portal.azure.com/#home).  

    b. Execute os comandos a seguir para habilitar a rede acelerada para as interfaces de rede adicionais, que são anexadas às `inter` `hsr` sub-redes e.  

    ```azurecli
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db1-inter --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db2-inter --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db3-inter --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db1-inter --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db2-inter --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db3-inter --accelerated-networking true
    
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db1-hsr --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db2-hsr --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db3-hsr --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db1-hsr --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db2-hsr --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db3-hsr --accelerated-networking true
    ```

7. Iniciar as máquinas virtuais do BD HANA

### <a name="deploy-azure-load-balancer"></a>Implantar Azure Load Balancer

1. É recomendável usar o balanceador de carga padrão. Siga estas etapas de configuração para implantar o balanceador de carga padrão:
   1. Primeiro, crie um pool de IP de front-end:

      1. Abra o balanceador de carga, selecione **pool de front-end** e selecione **Adicionar**.
      1. Insira o nome do novo pool de IP front-end (por exemplo, **hana-frontend**).
      1. Defina a **atribuição** como **estática** e insira o endereço IP (por exemplo, **10.23.0.18**).
      1. Selecione **OK**.
      1. Depois que o novo pool de IP de front-end for criado, anote o endereço IP do pool.

   1. Em seguida, crie um pool de back-end e adicione todas as VMs de cluster ao pool de back-end:

      1. Abra o balanceador de carga, selecione **pools de back-end** e selecione **Adicionar**.
      1. Insira o nome do novo pool de back-end (por exemplo, **hana-backend**).
      1. Selecione **Adicionar uma máquina virtual**.
      1. Selecione **Máquina virtual**.
      1. Selecione as máquinas virtuais do cluster SAP HANA e seus endereços IP para a `client` sub-rede.
      1. Selecione **Adicionar**.

   1. Em seguida, crie uma investigação de integridade:

      1. Abra o balanceador de carga, selecione **investigações de integridade** e selecione **Adicionar**.
      1. Insira o nome da nova investigação de integridade (por exemplo, **hana-hp**).
      1. Selecione **TCP** como o protocolo e porta 625 **03**. Mantenha o valor do **Intervalo** como 5 e o valor **Limite não íntegro** como 2.
      1. Selecione **OK**.

   1. Em seguida, crie as regras de balanceamento de carga:
   
      1. Abra o balanceador de carga, selecione **Regras de balanceamento de carga** e selecione **Adicionar**.
      1. Insira o nome da nova regra do balanceador de carga (por exemplo **hana-lb**).
      1. Selecione o endereço IP de front-end, o pool de back-end e a investigação de integridade que você criou anteriormente (por exemplo, **hana-frontend**, **hana-backend** e **hana-hp**).
      1. Selecione **Portas de HA**.
      1. Aumente o **tempo limite de ociosidade** para 30 minutos.
      1. Certifique-se de **habilitar IP Flutuante**.
      1. Selecione **OK**.

   > [!IMPORTANT]
   > Não há suporte para IP flutuante em uma configuração de IP secundário de NIC em cenários de balanceamento de carga. Para obter detalhes, consulte [limitações do Azure Load Balancer](../../../load-balancer/load-balancer-multivip-overview.md#limitations). Se você precisar de um endereço IP adicional para a VM, implante uma segunda NIC.    
   
   > [!Note]
   > Quando as VMs sem endereços IP públicos forem colocadas no pool de back-end do Standard Azure Load Balancer (sem endereço IP público), não haverá nenhuma conectividade de saída com a Internet se não houver configuração adicional a fim de permitir o roteamento para pontos de extremidade públicos. Para obter detalhes sobre como alcançar conectividade de saída, confira [Conectividade de ponto de extremidade público para Máquinas Virtuais usando o Azure Standard Load Balancer em cenários de alta disponibilidade do SAP](./high-availability-guide-standard-load-balancer-outbound-connections.md).  


   > [!IMPORTANT]
   > Não habilite carimbos de data/hora de TCP em VMs do Azure posicionadas subjacentemente ao Azure Load Balancer. Habilitar carimbos de data/hora de TCP fará com que as investigações de integridade falhem. Defina o parâmetro **net.ipv4.tcp_timestamps** para **0**. Para obter detalhes, veja [Investigações de integridade do Load Balancer](../../../load-balancer/load-balancer-custom-probe-overview.md).
   > Veja também a nota SAP [2382421](https://launchpad.support.sap.com/#/notes/2382421).  

### <a name="deploy-the-azure-netapp-files-infrastructure"></a>Implantar a infraestrutura de Azure NetApp Files 

Implante os volumes seja para o `/hana/shared` sistema de arquivos. Você precisará de um `/hana/shared` volume separado para cada site de replicação do sistema do Hana. Para obter mais informações, consulte [Configurar a infraestrutura de Azure NetApp files](./sap-hana-scale-out-standby-netapp-files-rhel.md#set-up-the-azure-netapp-files-infrastructure).

Neste exemplo, os seguintes volumes de Azure NetApp Files foram usados: 

* volume **HN1**-Shared-s1 (NFS://10.23.1.7/**HN1**-Shared-S1)
* volume **HN1**-Shared-s2 (NFS://10.23.1.7/**HN1**-Shared-S2)

## <a name="operating-system-configuration-and-preparation"></a>Configuração e preparação do sistema operacional

As instruções nas próximas seções são prefixadas com uma das seguintes abreviações:
* **[A]**: aplicável a todos os nós
* **[Ah]**: aplicável a todos os nós de BD do Hana
* **[M]**: aplicável ao nó do criador de maioria
* **[AH1]**: aplicável a todos os nós de BD do Hana no site 1
* **[AH2]**: aplicável a todos os nós de BD do Hana no site 2
* **[1]**: aplicável somente ao nó 1 do Hana DB, site 1
* **[2]**: aplicável somente ao nó 1 do Hana DB, site 2


Configure e prepare seu sistema operacional executando as seguintes etapas:

1. **[A]** manter os arquivos de host nas máquinas virtuais. Inclua entradas para todas as sub-redes. As entradas a seguir foram adicionadas a `/etc/hosts` para este exemplo.  

    ```bash
     # Client subnet
     10.23.0.11 hana-s1-db1
     10.23.0.12 hana-s1-db1
     10.23.0.13 hana-s1-db2
     10.23.0.14 hana-s2-db1
     10.23.0.15 hana-s2-db2
     10.23.0.16 hana-s2-db3
     10.23.0.17 hana-s-mm
     # Internode subnet
     10.23.1.138 hana-s1-db1-inter
     10.23.1.139 hana-s1-db2-inter
     10.23.1.140 hana-s1-db3-inter
     10.23.1.141 hana-s2-db1-inter
     10.23.1.142 hana-s2-db2-inter
     10.23.1.143 hana-s2-db3-inter
     # HSR subnet
     10.23.1.202 hana-s1-db1-hsr
     10.23.1.203 hana-s1-db2-hsr
     10.23.1.204 hana-s1-db3-hsr
     10.23.1.205 hana-s2-db1-hsr
     10.23.1.206 hana-s2-db2-hsr
     10.23.1.207 hana-s2-db3-hsr
    ```


2. **[A]** instalar o pacote de cliente NFS.  

    ```yum install nfs-utils ```


3. **[Ah]** Configuração do Red Hat para HANA.  

    Configure o RHEL conforme descrito em <https://access.redhat.com/solutions/2447641> e nas seguintes notas SAP:  
   - [2292690 - Banco de dados do SAP HANA: configurações do sistema operacional recomendadas para RHEL 7](https://launchpad.support.sap.com/#/notes/2292690)
   - [2777782-SAP HANA DB: configurações de so recomendadas para RHEL 8](https://launchpad.support.sap.com/#/notes/2777782)
   - [2455582-Linux: executando aplicativos SAP compilados com GCC 6. x](https://launchpad.support.sap.com/#/notes/2455582)
   - [2593824-Linux: executando aplicativos SAP compilados com GCC 7. x](https://launchpad.support.sap.com/#/notes/2593824) 
   - [2886607-Linux: executando aplicativos SAP compilados com o GCC 9. x](https://launchpad.support.sap.com/#/notes/2886607)

## <a name="prepare-the-file-systems"></a>Preparar os sistemas de arquivos
### <a name="mount-the-shared-file-systems"></a>Montar os sistemas de arquivos compartilhados

Neste exemplo, os sistemas de arquivos compartilhados do HANA são implantados em Azure NetApp Files e montados em NFSv4.  

1. **[Ah]** Crie pontos de montagem para os volumes de banco de dados do HANA.  

    ```bash
    mkdir -p /hana/shared
    ```

2. **[Ah]** Verifique a configuração de domínio do NFS. Verifique se o domínio está configurado como o domínio de Azure NetApp Files padrão, ou seja, **`defaultv4iddomain.com`** e se o mapeamento está definido como **ninguém**.  
   Esta etapa só será necessária se você estiver usando o Azure NetAppFiles NFSv 4.1.  

    > [!IMPORTANT]
    > É preciso que você defina o domínio NFS em `/etc/idmapd.conf` na VM para corresponder à configuração de domínio padrão no Azure NetApp Files: **`defaultv4iddomain.com`** . Se houver uma incompatibilidade entre a configuração de domínio no cliente NFS (ou seja, a VM) e o servidor NFS, ou seja, a configuração da Azure NetApp, as permissões para arquivos nos volumes do Azure NetApp que forem montados nas VMs serão exibidas como `nobody`.  

    ```bash
    sudo cat /etc/idmapd.conf
    # Example
    [General]
    Domain = defaultv4iddomain.com
    [Mapping]
    Nobody-User = nobody
    Nobody-Group = nobody
    ```

3. **[Ah]** Verifique `nfs4_disable_idmapping` . Ele deve ser definido como **Y**. Para criar a estrutura de diretório em que `nfs4_disable_idmapping` está localizado, execute o comando mount. Você não poderá criar o diretório manualmente em /sys/modules, pois o acesso é reservado para o kernel e os drivers.  
   Esta etapa só será necessária se você estiver usando o Azure NetAppFiles NFSv 4.1.  

    ```bash
    # Check nfs4_disable_idmapping 
    cat /sys/module/nfs/parameters/nfs4_disable_idmapping
    # If you need to set nfs4_disable_idmapping to Y
    mkdir /mnt/tmp
    mount 10.9.0.4:/HN1-shared /mnt/tmp
    umount  /mnt/tmp
    echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    # Make the configuration permanent
    echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
    ```

   Para obter mais informações sobre como alterar o `nfs4_disable_idmapping` parâmetro, consulte https://access.redhat.com/solutions/1749883 .

4. **[AH1]** Monte os volumes de Azure NetApp Files compartilhados nas VMs do SITE1 HANA DB.  

    ```bash
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.23.1.7:/HN1-shared-s1 /hana/shared
    ```

5. **[AH2]** Monte os volumes de Azure NetApp Files compartilhados nas VMs do SITE2 HANA DB.  

    ```bash
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.23.1.7:/HN1-shared-s2 /hana/shared
    ```


10. **[Ah]** Verifique se os `/hana/shared/` sistemas de arquivos correspondentes estão montados em todas as VMs do Hana DB com o protocolo NFS versão **NFSv4**.  

    ```bash
    sudo nfsstat -m
    # Verify that flag vers is set to 4.1 
    # Example from SITE 1, hana-s1-db1
    /hana/shared from 10.23.1.7:/HN1-shared-s1
     Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.0.11,local_lock=none,addr=10.23.1.7
    # Example from SITE 2, hana-s2-db1
    /hana/shared from 10.23.1.7:/HN1-shared-s2
     Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.0.14,local_lock=none,addr=10.23.1.7
    ```

### <a name="prepare-the-data-and-log-local-file-systems"></a>Preparar os dados e registrar em log os sistemas de arquivos locais
Na configuração apresentada, os sistemas `/hana/data` de arquivos e `/hana/log` são implantados no disco gerenciado e são anexados localmente a cada VM do BD Hana. Você precisará executar as etapas para criar os volumes de dados e de log locais em cada máquina virtual do BD HANA. 

Configure o layout do disco com o  **LVM (Gerenciador de volumes lógicos)**. O exemplo a seguir pressupõe que cada máquina virtual do HANA tem três discos de dados anexados, que são usados para criar dois volumes.

1. **[Ah]** Listar todos os discos disponíveis:
    ```bash
    ls /dev/disk/azure/scsi1/lun*
    ```

   Saída de exemplo:

    ```bash
    /dev/disk/azure/scsi1/lun0  /dev/disk/azure/scsi1/lun1  /dev/disk/azure/scsi1/lun2 
    ```

2. **[Ah]** Crie volumes físicos para todos os discos que você deseja usar:
    ```bash
    sudo pvcreate /dev/disk/azure/scsi1/lun0
    sudo pvcreate /dev/disk/azure/scsi1/lun1
    sudo pvcreate /dev/disk/azure/scsi1/lun2
    ```

3. **[Ah]** Crie um grupo de volumes para os arquivos de dados. Use um grupo de volumes para os arquivos de log e outro para o diretório compartilhado do SAP HANA:
    ```bash
    sudo vgcreate vg_hana_data_HN1 /dev/disk/azure/scsi1/lun0 /dev/disk/azure/scsi1/lun1
    sudo vgcreate vg_hana_log_HN1 /dev/disk/azure/scsi1/lun2
    ```

4. **[Ah]** Crie os volumes lógicos. 
   Um volume linear é criado quando você usa `lvcreate` sem a opção `-i`. Sugerimos que você crie um volume distribuído para melhorar o desempenho de E/S e alinhe os tamanhos de distribuição aos valores documentados em [Configurações de armazenamento de VM do SAP HANA](./hana-vm-operations-storage.md). O argumento `-i` deve ser o número de volumes físicos subjacentes e o argumento `-I` é o tamanho da distribuição. Neste documento, dois volumes físicos são usados para o volume de dados, portanto, o argumento da chave `-i` é definido com **2**. O tamanho da distribuição para o volume de dados é **256 KiB**. Um volume físico é usado para o volume do log, portanto, nenhuma opção `-i` ou `-I` é usada explicitamente para os comandos do volume do log.  

   > [!IMPORTANT]
   > Use a `-i` opção e defina-a como o número do volume físico subjacente quando você usar mais de um volume físico para todos os volumes de dados ou de log. Use a opção `-I` para especificar o tamanho da distribuição, quando criar um volume distribuído.  
   > Veja [Configurações de armazenamento de VM do SAP HANA](./hana-vm-operations-storage.md) para ver as configurações de armazenamento recomendadas, incluindo tamanhos de distribuição e número de discos.  

    ```bash
    sudo lvcreate -i 2 -I 256 -l 100%FREE -n hana_data vg_hana_data_HN1
    sudo lvcreate -l 100%FREE -n hana_log vg_hana_log_HN1
    sudo mkfs.xfs /dev/vg_hana_data_HN1/hana_data
    sudo mkfs.xfs /dev/vg_hana_log_HN1/hana_log
    ```

5. **[Ah]** Crie os diretórios de montagem e copie o UUID de todos os volumes lógicos:
    ```bash
    sudo mkdir -p /hana/data/HN1
    sudo mkdir -p /hana/log/HN1
    # Write down the ID of /dev/vg_hana_data_HN1/hana_data and /dev/vg_hana_log_HN1/hana_log
    sudo blkid
    ```

6. **[Ah]** Criar `fstab` entradas para os volumes lógicos e montar:
    ```bash
    sudo vi /etc/fstab
    ```

   Insira a seguinte linha no arquivo `/etc/fstab`:

    ```bash
    /dev/disk/by-uuid/UUID of /dev/mapper/vg_hana_data_HN1-hana_data /hana/data/HN1 xfs  defaults,nofail  0  2
    /dev/disk/by-uuid/UUID of /dev/mapper/vg_hana_log_HN1-hana_log /hana/log/HN1 xfs  defaults,nofail  0  2
    ```

   Monte os novos volumes:

    ```bash
    sudo mount -a
    ```

## <a name="installation"></a>Instalação  

Neste exemplo para implantar SAP HANA na configuração de expansão com HSR em VMs do Azure, usamos o HANA 2,0 SP4.  

### <a name="prepare-for-hana-installation"></a>Preparar para a instalação do HANA

1. **[Ah]** Antes da instalação do HANA, defina a senha raiz. Você pode desabilitar a senha raiz após a conclusão da instalação. Comando execute `root` as `passwd` .  

2. **[1, 2]** alterar as permissões em `/hana/shared` 
    ```bash
    chmod 775 /hana/shared
    ```

3. **[1]** Verifique se você pode fazer logon via SSH para as VMs do BD Hana neste site **Hana-S1-DB2** e **Hana-S1-DB3**, sem ser solicitado a fornecer uma senha.  
   Se esse não for o caso, troque chaves SSH, conforme documentado em [usando a autenticação baseada em chave](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/deployment_guide/s2-ssh-configuration-keypairs).  
    ```bash
    ssh root@hana-s1-db2
    ssh root@hana-s1-db3
    ```

4. **[2]** Verifique se você pode fazer logon via SSH para as VMs do BD Hana neste site **Hana-S2-DB2** e **Hana-S2-DB3**, sem ser solicitado a fornecer uma senha.  
   Se esse não for o caso, troque chaves SSH, conforme documentado em [usando a autenticação baseada em chave](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/deployment_guide/s2-ssh-configuration-keypairs).  
    ```bash
    ssh root@hana-s2-db2
    ssh root@hana-s2-db3
    ```

5. **[Ah]** Instale pacotes adicionais, que são necessários para o HANA 2,0 SP4. Para obter mais informações, consulte SAP Note [2593824](https://launchpad.support.sap.com/#/notes/2593824) para RHEL 7. 

    ```bash
    # If using RHEL 7
    yum install libgcc_s1 libstdc++6 compat-sap-c++-7 libatomic1
    # If using RHEL 8
    yum install libatomic libtool-ltdl.x86_64
    ```


6. **[A]** desabilite o firewall temporariamente, para que ele não interfira na instalação do Hana. Você pode habilitá-lo novamente depois que a instalação do HANA for concluída. 
    ```bash
    # Execute as root
    systemctl stop firewalld
    systemctl disable firewalld
    ```

### <a name="hana-installation-on-the-first-node-on-each-site"></a>Instalação do HANA no primeiro nó de cada site

1. **[1]** instale o SAP Hana seguindo as instruções no [Guia de instalação e atualização do SAP Hana 2,0](https://help.sap.com/viewer/2c1988d620e04368aa4103bf26f17727/2.0.04/en-US/7eb0167eb35e4e2885415205b8383584.html). Nas instruções a seguir, mostramos o SAP HANA instalação no primeiro nó no SITE 1.   

   a. Inicie o programa **hdblcm** `root` do diretório de software de instalação do Hana. Use o `internal_network` parâmetro e passe o espaço de endereço para sub-rede, que é usado para a comunicação interna do Hana entre nós.  

    ```bash
    ./hdblcm --internal_network=10.23.1.128/26
    ```

   b. No prompt, insira os seguintes valores:

     * Para **escolher uma ação**: insira **1** (para instalar)
     * Para obter **componentes adicionais para a instalação**: insira **2, 3**
     * Para o caminho de instalação: pressione Enter (o padrão é/Hana/Shared)
     * Para **nome do host local**: pressione ENTER para aceitar o padrão
     * Para **que deseja adicionar hosts ao sistema?**: insira **n**
     * Para **SAP Hana ID do sistema**: insira **HN1**
     * Para **número de instância** [00]: insira **03**
     * Para o **grupo de trabalho do host local** [padrão]: pressione ENTER para aceitar o padrão
     * Para **selecionar uso do sistema/inserir índice [4]**: insira **4** (para personalizado)
     * Para o **local dos volumes de dados** [/Hana/data/HN1]: pressione ENTER para aceitar o padrão
     * Para o **local dos volumes de log** [/Hana/log/HN1]: pressione ENTER para aceitar o padrão
     * Para **restringir a alocação máxima de memória?** [n]: insira **n**
     * Para o **nome do host do certificado para o host Hana-S1-DB1** [Hana-S1-DB1]: pressione ENTER para aceitar o padrão
     * Para a **senha do usuário do agente de host do SAP (sapadm)**: Insira a senha
     * Para **confirmar a senha do usuário do agente de host SAP (sapadm)**: Insira a senha
     * Para a **senha do administrador do sistema (hn1adm)**: Insira a senha
     * Para o **diretório base do administrador do sistema** [/usr/SAP/HN1/Home]: pressione ENTER para aceitar o padrão
     * Para o **Shell de logon do administrador do sistema** [/bin/sh]: pressione ENTER para aceitar o padrão
     * Para a **ID de usuário do administrador do sistema** [1001]: pressione ENTER para aceitar o padrão
     * Para **Inserir ID do grupo de usuários (SAPs)** [79]: pressione ENTER para aceitar o padrão
     * Para a **senha do usuário do banco de dados do sistema (sistema)**: Insira a senha do sistema
     * Para **Confirmar senha do usuário do banco de dados do sistema (sistema)**: Insira a senha do sistema
     * Para **reiniciar o sistema após a reinicialização do computador?** [n]: insira **n** 
     * Para **continuar (s/n)**: validar o resumo e, se tudo estiver correto, digite **y**

2. **[2]** repita a etapa anterior para instalar SAP Hana no primeiro nó no site 2.   

3. **[1, 2]** verificar global.ini  

   Exiba global.ini e verifique se a configuração da comunicação interna SAP HANA entre nós está em vigor. Verifique a seção de **comunicação** . Ele deve ter o espaço de endereço para a `inter` sub-rede e `listeninterface` deve ser definido como `.internal` . Verifique a seção **internal_hostname_resolution** . Ele deve ter os endereços IP para as máquinas virtuais HANA que pertencem à `inter` sub-rede.  

   ```bash
     sudo cat /usr/sap/HN1/SYS/global/hdb/custom/config/global.ini
     # Example from SITE1 
     [communication]
     internal_network = 10.23.1.128/26
     listeninterface = .internal
     [internal_hostname_resolution]
     10.23.1.138 = hana-s1-db1
     10.23.1.139 = hana-s1-db2
     10.23.1.140 = hana-s1-db3
   ```

4. **[1, 2]** Prepare-se `global.ini` para a instalação em um ambiente não compartilhado, conforme descrito em SAP Note [2080991](https://launchpad.support.sap.com/#/notes/0002080991).  

   ```bash
    sudo vi /usr/sap/HN1/SYS/global/hdb/custom/config/global.ini
    [persistence]
    basepath_shared = no
   ```

4. **[1, 2]** reinicie SAP Hana para ativar as alterações.  

   ```bash
    sudo -u hn1adm /usr/sap/hostctrl/exe/sapcontrol -nr 03 -function StopSystem
    sudo -u hn1adm /usr/sap/hostctrl/exe/sapcontrol -nr 03 -function StartSystem
   ```

6. **[1, 2]** Verifique se a interface do cliente usará os endereços IP da `client` sub-rede para comunicação.  

    ```bash
    # Execute as hn1adm
    /usr/sap/HN1/HDB03/exe/hdbsql -u SYSTEM -p "password" -i 03 -d SYSTEMDB 'select * from SYS.M_HOST_INFORMATION'|grep net_publicname
    # Expected result - example from SITE 2
    "hana-s2-db1","net_publicname","10.23.0.14"
   ```

   Para obter informações sobre como verificar a configuração, consulte SAP Note [2183363 – configuração de SAP Hana rede interna](https://launchpad.support.sap.com/#/notes/2183363).  

7. **[Ah]** Altere as permissões nos diretórios de dados e de log para evitar o erro de instalação do HANA.  

   ```bash
    sudo chmod o+w -R /hana/data /hana/log
   ```

8. **[1]** instale os nós do Hana secundários. As instruções de exemplo nesta etapa são para o SITE 1.  
   a. Inicie o programa **hdblcm** residente como `root` .    
    ```bash
     cd /hana/shared/HN1/hdblcm
     ./hdblcm 
    ```

   b. No prompt, insira os seguintes valores:

     * Para **escolher uma ação**: digite **2** (para adicionar hosts)
     * Para **Inserir nomes de host separados por vírgula a serem adicionados**: Hana-S1-DB2, Hana-S1-db3
     * Para obter **componentes adicionais para a instalação**: insira **2, 3**
     * Para **Inserir nome de usuário raiz [raiz]**: pressione ENTER para aceitar o padrão
     * Para **selecionar funções para o host ' Hana-S1-DB2 ' [1]**: 1 (para o trabalho)
     * Para **Inserir grupo de failover de host para o host ' Hana-S1-DB2 ' [padrão]**: pressione ENTER para aceitar o padrão
     * Para **Inserir o número de partição de armazenamento para o host ' Hana-S1-DB2 ' [<<assign automatically>>]**: pressione ENTER para aceitar o padrão
     * Para **Inserir grupo de trabalho para o host ' Hana-S1-DB2 ' [padrão]**: pressione ENTER para aceitar o padrão
     * Para **funções SELECT para o host ' Hana-S1-DB3 ' [1]**: 1 (para o trabalho)
     * Para **Inserir grupo de failover de host para o host ' Hana-S1-DB3 ' [padrão]**: pressione ENTER para aceitar o padrão
     * Para **Inserir o número da partição de armazenamento para o host ' Hana-S1-DB3 ' [<<assign automatically>>]**: pressione ENTER para aceitar o padrão
     * Para **Inserir grupo de trabalho para o host ' Hana-S1-DB3 ' [padrão]**: pressione ENTER para aceitar o padrão
     * Para a **senha do administrador do sistema (hn1adm)**: Insira a senha
     * Para **Inserir a senha do usuário do agente de host SAP (sapadm)**: Insira a senha
     * Para **confirmar a senha do usuário do agente de host SAP (sapadm)**: Insira a senha
     * Para o **nome do host do certificado para o host Hana-S1-DB2** [Hana-S1-DB2]: pressione ENTER para aceitar o padrão
     * Para o **nome do host do certificado para o host Hana-S1-DB3** [Hana-S1-DB3]: pressione ENTER para aceitar o padrão
     * Para **continuar (s/n)**: validar o resumo e, se tudo estiver correto, digite **y**

9. **[2]** repita a etapa anterior para instalar os nós de SAP Hana secundários no site 2.   

## <a name="configure-sap-hana-20-system-replication"></a>Configurar a replicação do Sistema SAP HANA 2.0

1. **[1]** configurar a replicação do sistema no site 1:

   Faça backup dos bancos de dados como **hn1** ADM:

    ```bash
    hdbsql -d SYSTEMDB -u SYSTEM -p "passwd" -i 03 "BACKUP DATA USING FILE ('initialbackupSYS')"
    hdbsql -d HN1 -u SYSTEM -p "passwd" -i 03 "BACKUP DATA USING FILE ('initialbackupHN1')"
    ```

   Copie os arquivos PKI do sistema para o site secundário:

    ```bash
    scp /usr/sap/HN1/SYS/global/security/rsecssfs/data/SSFS_HN1.DAT hana-s2-db1:/usr/sap/HN1/SYS/global/security/rsecssfs/data/
    scp /usr/sap/HN1/SYS/global/security/rsecssfs/key/SSFS_HN1.KEY  hana-s2-db1:/usr/sap/HN1/SYS/global/security/rsecssfs/key/
    ```

   Crie o site primário:

    ```bash
    hdbnsutil -sr_enable --name=HANA_S1
    ```

2. **[2]** configurar a replicação do sistema no site 2:
    
   Registre o segundo site para iniciar a replicação do sistema. Execute o comando a seguir como <hanasid\>adm:

    ```bash
    sapcontrol -nr 03 -function StopWait 600 10
    hdbnsutil -sr_register --remoteHost=hana-s1-db1 --remoteInstance=03 --replicationMode=sync --name=HANA_S2
    sapcontrol -nr 03 -function StartSystem
    ```

3. **[1]**  Verificar status de replicação

   Verifique o status de replicação e aguarde até que todos os bancos de dados estão em sincronizado.

    ```bash
    sudo su - hn1adm -c "python /usr/sap/HN1/HDB03/exe/python_support/systemReplicationStatus.py"
    # | Database | Host          | Port  | Service Name | Volume ID | Site ID | Site Name | Secondary     | Secondary | Secondary | Secondary | Secondary     | Replication | Replication | Replication    |
    # |          |               |       |              |           |         |           | Host          | Port      | Site ID   | Site Name | Active Status | Mode        | Status      | Status Details |
    # | -------- | ------------- | ----- | ------------ | --------- | ------- | --------- | ------------- | --------- | --------- | --------- | ------------- | ----------- | ----------- | -------------- |
    # | HN1      | hana-s1-db3   | 30303 | indexserver  |         5 |       1 | HANA_S1   | hana-s2-db3   |     30303 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
    # | SYSTEMDB | hana-s1-db1   | 30301 | nameserver   |         1 |       1 | HANA_S1   | hana-s2-db1   |     30301 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
    # | HN1      | hana-s1-db1   | 30307 | xsengine     |         2 |       1 | HANA_S1   | hana-s2-db1   |     30307 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
    # | HN1      | hana-s1-db1   | 30303 | indexserver  |         3 |       1 | HANA_S1   | hana-s2-db1   |     30303 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
    # | HN1      | hana-s1-db2   | 30303 | indexserver  |         4 |       1 | HANA_S1   | hana-s2-db2   |     30303 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
    #
    # status system replication site "2": ACTIVE
    # overall system replication status: ACTIVE
    #
    # Local System Replication State
    #   
    # mode: PRIMARY
    # site id: 1
    # site name: HANA_S1
    ```

4. **[1, 2]** altere a configuração do Hana para que a comunicação para a replicação do sistema Hana se direcionada por meio das interfaces de rede virtual de replicação do sistema Hana.   
   - Parar o HANA em ambos os sites
    ```bash
    sudo -u hn1adm /usr/sap/hostctrl/exe/sapcontrol -nr 03 -function StopSystem HDB
    ```

   - Editar global.ini para adicionar o mapeamento de host para replicação de sistema do HANA: Use os endereços IP da `hsr` sub-rede.  
    ```bash
    sudo vi /usr/sap/HN1/SYS/global/hdb/custom/config/global.ini
    #Add the section
    [system_replication_hostname_resolution]
    10.23.1.202 = hana-s1-db1
    10.23.1.203 = hana-s1-db2
    10.23.1.204 = hana-s1-db3
    10.23.1.205 = hana-s2-db1
    10.23.1.206 = hana-s2-db2
    10.23.1.207 = hana-s2-db3
    ```

   - Iniciar o HANA em ambos os sites
   ```bash
    sudo -u hn1adm /usr/sap/hostctrl/exe/sapcontrol -nr 03 -function StartSystem HDB
   ```

   Para obter mais informações, consulte [resolução de nomes de host para replicação do sistema](https://help.sap.com/viewer/eb3777d5495d46c5b2fa773206bbfb46/1.0.12/en-US/c0cba1cb2ba34ec89f45b48b2157ec7b.html).  

5. **[Ah]** Reabilitar o firewall.  
   - Reabilitar o firewall
       ```bash
       # Execute as root
       systemctl start firewalld
       systemctl enable firewalld
       ```

   - Abra as portas de firewall necessárias. Você precisará ajustar as portas para o seu número de instância do HANA.  

       > [!IMPORTANT]
       > Crie regras de firewall para permitir a comunicação entre nós do HANA e o tráfego do cliente. As portas necessárias estão listadas em [Portas TCP / IP de todos os produtos SAP](https://help.sap.com/viewer/ports). Os comandos a seguir são apenas um exemplo. Nesse cenário, com o número de sistema usado 03.

       ```bash
        # Execute as root
        sudo firewall-cmd --zone=public --add-port=30301/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30301/tcp
        sudo firewall-cmd --zone=public --add-port=30303/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30303/tcp
        sudo firewall-cmd --zone=public --add-port=30306/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30306/tcp
        sudo firewall-cmd --zone=public --add-port=30307/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30307/tcp
        sudo firewall-cmd --zone=public --add-port=30313/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30313/tcp
        sudo firewall-cmd --zone=public --add-port=30315/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30315/tcp
        sudo firewall-cmd --zone=public --add-port=30317/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30317/tcp
        sudo firewall-cmd --zone=public --add-port=30340/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30340/tcp
        sudo firewall-cmd --zone=public --add-port=30341/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30341/tcp
        sudo firewall-cmd --zone=public --add-port=30342/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30342/tcp
        sudo firewall-cmd --zone=public --add-port=1128/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=1128/tcp
        sudo firewall-cmd --zone=public --add-port=1129/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=1129/tcp
        sudo firewall-cmd --zone=public --add-port=40302/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40302/tcp
        sudo firewall-cmd --zone=public --add-port=40301/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40301/tcp
        sudo firewall-cmd --zone=public --add-port=40307/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40307/tcp
        sudo firewall-cmd --zone=public --add-port=40303/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40303/tcp
        sudo firewall-cmd --zone=public --add-port=40340/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40340/tcp
        sudo firewall-cmd --zone=public --add-port=50313/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=50313/tcp
        sudo firewall-cmd --zone=public --add-port=50314/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=50314/tcp
        sudo firewall-cmd --zone=public --add-port=30310/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30310/tcp
        sudo firewall-cmd --zone=public --add-port=30302/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30302/tcp
       ```

## <a name="create-a-pacemaker-cluster"></a>Criar um cluster do Pacemaker

Siga as etapas em [Configurando o Pacemaker no Red Hat Enterprise Linux no Azure](high-availability-guide-rhel-pacemaker.md) para criar um cluster básico do Pacemaker para esse servidor HANA.
Inclua todas as máquinas virtuais, incluindo o criador principal no cluster.  

> [!IMPORTANT]
> Não defina como `quorum expected-votes` 2, pois esse não é um cluster de dois nós.  
> Verifique se a propriedade de cluster `concurrent-fencing`  está habilitada, para que o isolamento de nó seja desserializado.   

## <a name="create-file-system-resources"></a>Criar recursos do sistema de arquivos

1. **[1, 2]** parar SAP Hana em ambos os sites de replicação. Execute as <SID \> ADM.  

    ```bash
    sapcontrol -nr 03 -function StopSystem
    ```

2. **[Ah]** Desmonte o sistema de arquivos `/hana/shared` , que foi montado temporariamente para a instalação em todas as VMs do BD Hana. Você precisará interromper todos os processos e sessões que estão usando o sistema de arquivos, antes de poder desmontá-lo. 
 
    ```bash
    umount /hana/shared 
    ```

3. **[1]** crie os recursos de cluster do sistema de arquivos para `/hana/shared` no estado desabilitado. Os recursos são criados com a opção `--disabled` , porque você precisa definir as restrições de local, antes que as montagens sejam habilitadas.  

    ```bash
    # /hana/shared file system for site 1
    pcs resource create fs_hana_shared_s1 --disabled ocf:heartbeat:Filesystem device=10.23.1.7:/HN1-shared-s1  directory=/hana/shared \
    fstype=nfs options='defaults,rw,hard,timeo=600,rsize=262144,wsize=262144,proto=tcp,intr,noatime,sec=sys,vers=4.1,lock,_netdev' op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 \
    op start interval=0 timeout=120 op stop interval=0 timeout=120

    # /hana/shared file system for site 2   
    pcs resource create fs_hana_shared_s2 --disabled ocf:heartbeat:Filesystem device=10.23.1.7:/HN1-shared-s1 directory=/hana/shared \
    fstype=nfs options='defaults,rw,hard,timeo=600,rsize=262144,wsize=262144,proto=tcp,intr,noatime,sec=sys,vers=4.1,lock,_netdev' op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 \
    op start interval=0 timeout=120 op stop interval=0 timeout=120

    # clone the /hana/shared file system resources for both site1 and site2
    pcs resource clone fs_hana_shared_s1 meta clone-node-max=1 interleave=true
    pcs resource clone fs_hana_shared_s2 meta clone-node-max=1 interleave=true
    ```
 
   `OCF_CHECK_LEVEL=20` o atributo é adicionado à operação monitor, para que as operações de monitor executem um teste de leitura/gravação no sistema de arquivos. Sem esse atributo, a operação monitor só verifica se o sistema de arquivos está montado. Isso pode ser um problema porque, quando a conectividade é perdida, o sistema de arquivos pode permanecer montado, apesar de estar inacessível.  

   `on-fail=fence` o atributo também é adicionado à operação monitor. Com essa opção, se a operação do monitor falhar em um nó, esse nó será imediatamente isolado. Sem essa opção, o comportamento padrão é parar todos os recursos que dependem do recurso com falha, reiniciar o recurso com falha e, em seguida, iniciar todos os recursos que dependem do recurso com falha. Esse comportamento não só pode levar muito tempo quando um recurso SAPHana depende do recurso com falha, mas também pode falhar completamente. O recurso SAPHana não pode parar com êxito, se o compartilhamento NFS que contém os binários HANA estiver inacessível.  

4. **[1]** configurar e verificar os atributos do nó. Todos os nós do SAP HANA DB no site de replicação 1 são atribuídos ao atributo `S1` e todos os nós do SAP Hana DB no site de replicação 2 são atribuídos ao atributo `S2` .  

    ```bash
    # HANA replication site 1
    pcs node attribute hana-s1-db1 NFS_SID_SITE=S1
    pcs node attribute hana-s1-db2 NFS_SID_SITE=S1
    pcs node attribute hana-s1-db3 NFS_SID_SITE=S1
    # HANA replication site 2
    pcs node attribute hana-s2-db1 NFS_SID_SITE=S2
    pcs node attribute hana-s2-db2 NFS_SID_SITE=S2
    pcs node attribute hana-s2-db3 NFS_SID_SITE=S2
    #To verify the attribute assignment to nodes execute
    pcs node attribute
    ```

5. **[1]** configurar as restrições, que determinam onde os sistemas de arquivos NFS serão montados e habilitarão os recursos do sistema de arquivos.  
    ```bash
    # Configure the constraints
    pcs constraint location fs_hana_shared_s1-clone rule resource-discovery=never score=-INFINITY NFS_SID_SITE ne S1
    pcs constraint location fs_hana_shared_s2-clone rule resource-discovery=never score=-INFINITY NFS_SID_SITE ne S2
    # Enable the file system resources
    pcs resource enable fs_hana_shared_s1
    pcs resource enable fs_hana_shared_s2
    ```

   Quando você habilitar os recursos do sistema de arquivos, o cluster montará os `/hana/shared` sistemas de arquivos.
 
6. **[Ah]** Verifique se os volumes seja estão montados em `/hana/shared` todas as VMs do Hana DB em ambos os sites.

    ```bash
    sudo nfsstat -m
    # Verify that flag vers is set to 4.1 
    # Example from SITE 1, hana-s1-db1
    /hana/shared from 10.23.1.7:/HN1-shared-s1
     Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.0.11,local_lock=none,addr=10.23.1.7
    # Example from SITE 2, hana-s2-db1
    /hana/shared from 10.23.1.7:/HN1-shared-s2
     Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.0.14,local_lock=none,addr=10.23.1.7
    ```

7. **[1]** configurar os recursos de atributo. Configure as restrições, que definirão os atributos como `true` , se as montagens NFS para `hana/shared` estiverem montadas.  

    ```bash
    # Configure the attribure resources
    pcs resource create hana_nfs_s1_active ocf:pacemaker:attribute active_value=true inactive_value=false name=hana_nfs_s1_active
    pcs resource create hana_nfs_s2_active ocf:pacemaker:attribute active_value=true inactive_value=false name=hana_nfs_s2_active
    # Clone the attribure resources
    pcs resource clone hana_nfs_s1_active meta clone-node-max=1 interleave=true
    pcs resource clone hana_nfs_s2_active meta clone-node-max=1 interleave=true
    # Configure the constraints, which will set the attribute values
    pcs constraint order fs_hana_shared_s1-clone then hana_nfs_s1_active-clone
    pcs constraint order fs_hana_shared_s2-clone then hana_nfs_s2_active-clone
    ```

   > [!TIP]
   > Se sua configuração incluir outros sistemas de arquivos, além `hana/shared` de/, que são montados em NFS, inclua a `sequential=false` opção para que não haja nenhuma dependência de ordenação entre os sistemas de arquivos. Todos os sistemas de arquivos montados em NFS devem iniciar, antes do recurso de atributo correspondente, mas não precisam iniciar em nenhum pedido em relação uns aos outros. Para obter mais informações, consulte [como fazer configurar SAP HANA Scale-Out HSR em um cluster do pacemaker quando os sistemas de arquivos do Hana forem compartilhamentos NFS](https://access.redhat.com/solutions/5423971).  

8. **[1]** Coloque pacemaker no modo de manutenção, em preparação para a criação dos recursos de cluster do Hana.  
    ```bash
    pcs property set maintenance-mode=true
    ```

## <a name="create-sap-hana-cluster-resources"></a>Crie os recursos de cluster do SAP HANA

1. **[A]** instalar o agente de recursos de expansão do Hana em todos os nós de cluster, incluindo o criador principal.    

    ```bash
    yum install -y resource-agents-sap-hana-scaleout 
    ```


   > [!NOTE]
   > Consulte [políticas de suporte para clusters de ha RHEL-gerenciamento de SAP Hana em um cluster](https://access.redhat.com/articles/3397471) para a versão mínima com suporte do pacote `resource-agents-sap-hana-scaleout` para a versão do seu sistema operacional.  

2. **[1, 2]** instale o "gancho de replicação do sistema" do Hana. O gancho precisa ser instalado em um nó de BD do HANA em cada site de replicação do sistema. SAP HANA ainda deve estar inoperante.        

   1. Preparar o gancho como `root` 
    ```bash
     mkdir -p /hana/shared/myHooks
     cp /usr/share/SAPHanaSR-ScaleOut/SAPHanaSR.py /hana/shared/myHooks
     chown -R hn1adm:sapsys /hana/shared/myHooks
    ```

   2. Ajustá `global.ini`
    ```bash
    # add to global.ini
    [ha_dr_provider_SAPHanaSR]
    provider = SAPHanaSR
    path = /hana/shared/myHooks
    execution_order = 1
    
    [trace]
    ha_dr_saphanasr = info
    ```

3. **[Ah]** O cluster requer configuração de sudors no nó de cluster para <SID \> ADM. Neste exemplo, isso é obtido com a criação de um novo arquivo. Execute os comandos como `root` .    
    ```bash
    cat << EOF > /etc/sudoers.d/20-saphana
    # SAPHanaSR-ScaleOut needs for srHook
     Cmnd_Alias SOK = /usr/sbin/crm_attribute -n hana_hn1_glob_srHook -v SOK -t crm_config -s SAPHanaSR
     Cmnd_Alias SFAIL = /usr/sbin/crm_attribute -n hana_hn1_glob_srHook -v SFAIL -t crm_config -s SAPHanaSR
     hn1adm ALL=(ALL) NOPASSWD: SOK, SFAIL
     EOF
    ```

4. **[1, 2]** iniciar SAP Hana em ambos os sites de replicação. Execute as <SID \> ADM.  

    ```bash
    sapcontrol -nr 03 -function StartSystem 
    ```

5. **[1]** Verifique a instalação do gancho. Execute como <SID \> ADM no site de replicação do sistema do Hana ativo.   

    ```bash
    cdtrace
     awk '/ha_dr_SAPHanaSR.*crm_attribute/ \
     { printf "%s %s %s %s\n",$2,$3,$5,$16 }' nameserver_*

     # Example entries
     # 2020-07-21 22:04:32.364379 ha_dr_SAPHanaSR SFAIL
     # 2020-07-21 22:04:46.905661 ha_dr_SAPHanaSR SFAIL
     # 2020-07-21 22:04:52.092016 ha_dr_SAPHanaSR SFAIL
     # 2020-07-21 22:04:52.782774 ha_dr_SAPHanaSR SFAIL
     # 2020-07-21 22:04:53.117492 ha_dr_SAPHanaSR SFAIL
     # 2020-07-21 22:06:35.599324 ha_dr_SAPHanaSR SOK
    ```

6. **[1]** crie os recursos de cluster do Hana. Execute os comandos a seguir como `root` .    
   1. Certifique-se de que o cluster já está no modo de manutenção.  
    
   2. Em seguida, crie o recurso de topologia do HANA.  
      Se estiver criando um cluster RHEL **7. x** , use os seguintes comandos:  
      ```bash
      pcs resource create SAPHanaTopology_HN1_HDB03 SAPHanaTopologyScaleOut \
       SID=HN1 InstanceNumber=03 \
       op start timeout=600 op stop timeout=300 op monitor interval=10 timeout=600

      pcs resource clone SAPHanaTopology_HN1_HDB03 meta clone-node-max=1 interleave=true
      ```

      Se estiver criando um cluster RHEL **8. x** , use os seguintes comandos:  
      ```bash
      pcs resource create SAPHanaTopology_HN1_HDB03 SAPHanaTopology \
       SID=HN1 InstanceNumber=03 meta clone-node-max=1 interleave=true \
       op methods interval=0s timeout=5 \
       op start timeout=600 op stop timeout=300 op monitor interval=10 timeout=600

      pcs resource clone SAPHanaTopology_HN1_HDB03 meta clone-node-max=1 interleave=true
      ```

   3. Em seguida, crie o recurso de instância do HANA.  
      > [!NOTE]
      > Este artigo contém referências ao termo *subordinado*, um termo que a Microsoft não usa mais. Quando o termo for removido do software, também o removeremos deste artigo.  
 
      Se estiver criando um cluster RHEL **7. x** , use os seguintes comandos:    
      ```bash
      pcs resource create SAPHana_HN1_HDB03 SAPHanaController \
       SID=HN1 InstanceNumber=03 PREFER_SITE_TAKEOVER=true DUPLICATE_PRIMARY_TIMEOUT=7200 AUTOMATED_REGISTER=false \
       op start interval=0 timeout=3600 op stop interval=0 timeout=3600 op promote interval=0 timeout=3600 \
       op monitor interval=60 role="Master" timeout=700 op monitor interval=61 role="Slave" timeout=700
     
      pcs resource master msl_SAPHana_HN1_HDB03 SAPHana_HN1_HDB03 \
       meta master-max="1" clone-node-max=1 interleave=true
      ```

      Se estiver criando um cluster RHEL **8. x** , use os seguintes comandos:  
      ```bash
      pcs resource create SAPHana_HN1_HDB03 SAPHanaController \
       SID=HN1 InstanceNumber=03 PREFER_SITE_TAKEOVER=true DUPLICATE_PRIMARY_TIMEOUT=7200 AUTOMATED_REGISTER=false \
       op demote interval=0s timeout=320 op methods interval=0s timeout=5 \
       op start interval=0 timeout=3600 op stop interval=0 timeout=3600 op promote interval=0 timeout=3600 \
       op monitor interval=60 role="Master" timeout=700 op monitor interval=61 role="Slave" timeout=700
     
      pcs resource promotable SAPHana_HN1_HDB03 \
       meta master-max="1" clone-node-max=1 interleave=true
      ```
      > [!IMPORTANT]
      > Recomendamos como uma prática recomendada que você só defina AUTOMATED_REGISTER como **não**, enquanto executa testes de failover completos, para evitar que a instância primária com falha seja registrada automaticamente como secundária. Depois que os testes de failover forem concluídos com êxito, defina AUTOMATED_REGISTER como **Sim**, de modo que após a replicação do sistema tomada possa ser retomada automaticamente. 

   4. Criar IP virtual e recursos associados.  
      ```bash
      pcs resource create vip_HN1_03 ocf:heartbeat:IPaddr2 ip=10.23.0.18 op monitor interval="10s" timeout="20s"
      sudo pcs resource create nc_HN1_03 azure-lb port=62503
      sudo pcs resource group add g_ip_HN1_03 nc_HN1_03 vip_HN1_03
      ```

   5. Criar as restrições de cluster  
      Se estiver criando um cluster RHEL **7. x** , use os seguintes comandos:  
      ```bash
      #Start HANA topology, before the HANA instance
      pcs constraint order SAPHanaTopology_HN1_HDB03-clone then msl_SAPHana_HN1_HDB03

      pcs constraint colocation add g_ip_HN1_03 with master msl_SAPHana_HN1_HDB03 4000
      #HANA resources are only allowed to run on a node, if the node's NFS file systems are mounted. The constraint also avoids the majority maker node
      pcs constraint location SAPHanaTopology_HN1_HDB03-clone rule resource-discovery=never score=-INFINITY hana_nfs_s1_active ne true and hana_nfs_s2_active ne true
      ```
 
      Se estiver criando um cluster RHEL **8. x** , use os seguintes comandos:  
      ```bash
      #Start HANA topology, before the HANA instance
      pcs constraint order SAPHanaTopology_HN1_HDB03-clone then SAPHana_HN1_HDB03-clone

      pcs constraint colocation add g_ip_HN1_03 with master SAPHana_HN1_HDB03-clone 4000
      #HANA resources are only allowed to run on a node, if the node's NFS file systems are mounted. The constraint also avoids the majority maker node
      pcs constraint location SAPHanaTopology_HN1_HDB03-clone rule resource-discovery=never score=-INFINITY hana_nfs_s1_active ne true and hana_nfs_s2_active ne true
      ```

7. **[1]** Coloque o cluster fora do modo de manutenção. Certifique-se de que o status do cluster está correto e que todos os recursos foram iniciados.  
    ```bash
    sudo pcs property set maintenance-mode=false
    #If there are failed cluster resources, you may need to run the next command
    pcs resource cleanup
    ```
  
   > [!NOTE]
   > Os tempos limite na configuração acima são apenas exemplos e devem ser adaptados à configuração específica do HANA. Por exemplo, talvez seja necessário aumentar o tempo limite de início, se o banco de dados do SAP HANA demorar mais para iniciar.
  
## <a name="test-sap-hana-failover"></a>Testar failover de SAP HANA 

1. Antes de iniciar um teste, verifique o cluster e SAP HANA status de replicação do sistema.  

   a. Verifique se não há nenhuma ação de cluster com falha  
     ```bash
     #Verify that there are no failed cluster actions
     pcs status
     # Example
     #Stack: corosync
     #Current DC: hana-s-mm (version 1.1.19-8.el7_6.5-c3c624ea3d) - partition with quorum
     #Last updated: Thu Sep 24 06:00:20 2020
     #Last change: Thu Sep 24 05:59:17 2020 by root via crm_attribute on hana-s1-db1
     #
     #7 nodes configured
     #45 resources configured
     #
     #Online: [ hana-s-mm hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     #
     #Active resources:
     #
     #rsc_st_azure    (stonith:fence_azure_arm):      Started hana-s-mm
     #Clone Set: fs_hana_shared_s1-clone [fs_hana_shared_s1]
     #    Started: [ hana--s1-db1 hana-s1-db2 hana-s1-db3 ]
     #Clone Set: fs_hana_shared_s2-clone [fs_hana_shared_s2]
     #    Started: [ hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     #Clone Set: hana_nfs_s1_active-clone [hana_nfs_s1_active]
     #    Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 ]
     #Clone Set: hana_nfs_s2_active-clone [hana_nfs_s2_active]
     #    Started: [ hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     #Clone Set: SAPHanaTopology_HN1_HDB03-clone [SAPHanaTopology_HN1_HDB03]
     #    Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     #Master/Slave Set: msl_SAPHana_HN1_HDB03 [SAPHana_HN1_HDB03]
     #    Masters: [ hana-s1-db1 ]
     #    Slaves: [ hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     #Resource Group: g_ip_HN1_03
     #    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hana-s1-db1
     #    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hana-s1-db1
     ```

   b. Verifique se a replicação do sistema SAP HANA está em sincronia

      ```bash
      # Verify HANA HSR is in sync
      sudo su - hn1adm -c "python /usr/sap/HN1/HDB03/exe/python_support/systemReplicationStatus.py"
      #| Database | Host        | Port  | Service Name | Volume ID | Site ID | Site Name | Secondary     | Secondary| Secondary | Secondary | Secondary     | Replication | Replication | Replication    |
      #|          |             |       |              |           |         |           | Host          | Port     | Site ID   | Site Name | Active Status | Mode        | Status      | Status Details |
      #| -------- | ----------- | ----- | ------------ | --------- | ------- | --------- | ------------- | -------- | --------- | --------- | ------------- | ----------- | ----------- | -------------- |
      #| HN1      | hana-s1-db3 | 30303 | indexserver  |         5 |       2 | HANA_S1   | hana-s2-db3 |     30303  |         1 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
      #| HN1      | hana-s1-db2 | 30303 | indexserver  |         4 |       2 | HANA_S1   | hana-s2-db2 |     30303  |         1 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |  
      #| SYSTEMDB | hana-s1-db1 | 30301 | nameserver   |         1 |       2 | HANA_S1   | hana-s2-db1 |     30301  |         1 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
      #| HN1      | hana-s1-db1 | 30307 | xsengine     |         2 |       2 | HANA_S1   | hana-s2-db1 |     30307  |         1 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
      #| HN1      | hana-s1-db1 | 30303 | indexserver  |         3 |       2 | HANA_S1   | hana-s2-db1 |     30303  |         1 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |

      #status system replication site "1": ACTIVE
      #overall system replication status: ACTIVE

      #Local System Replication State
      #~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

      #mode: PRIMARY
      #site id: 1
      #site name: HANA_S1
      ```

2. Verifique a configuração do cluster para um cenário de falha, quando um nó perde o acesso ao compartilhamento NFS ( `/hana/shared` ).  

   Os agentes de recurso de SAP HANA dependem de binários, armazenados em `/hana/shared` para executar operações durante o failover. O sistema `/hana/shared` de arquivos é montado em NFS na configuração apresentada. Um teste que pode ser executado é remontar o sistema de `/hana/shared` arquivos como *somente leitura*. Essa abordagem valida que o cluster fará failover, se o acesso a `/hana/shared` for perdido no site de replicação do sistema ativo.  

   **Resultado esperado**: quando você remonta `/hana/shared` como *somente leitura*, a operação de monitoramento que executa a operação de leitura/gravação no sistema de arquivos falhará, pois não é possível gravar no sistema de arquivos e irá disparar o failover de recurso do Hana. O mesmo resultado é esperado quando o nó do HANA perde o acesso ao compartilhamento NFS.  
     
   Você pode verificar o estado dos recursos de cluster executando `crm_mon` ou `pcs status` . Estado do recurso antes de iniciar o teste:
      ```bash
      # Output of crm_mon
      #7 nodes configured
      #45 resources configured

      #Online: [ hana-s-mm hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      #
      #Active resources:

      #rsc_st_azure    (stonith:fence_azure_arm):      Started hana-s-mm
      # Clone Set: fs_hana_shared_s1-clone [fs_hana_shared_s1]
      #    Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 ]
      # Clone Set: fs_hana_shared_s2-clone [fs_hana_shared_s2]
      #     Started: [ hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      # Clone Set: hana_nfs_s1_active-clone [hana_nfs_s1_active]
      #     Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 ]
      # Clone Set: hana_nfs_s2_active-clone [hana_nfs_s2_active]
      #     Started: [ hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      # Clone Set: SAPHanaTopology_HN1_HDB03-clone [SAPHanaTopology_HN1_HDB03]
      #     Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      # Master/Slave Set: msl_SAPHana_HN1_HDB03 [SAPHana_HN1_HDB03]
      #     Masters: [ hana-s1-db1 ]
      #     Slaves: [ hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      # Resource Group: g_ip_HN1_03
      #     nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hana-s1-db1
      #     vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hana-s1-db1
      ```

   Para simular uma falha para `/hana/shared` em uma das VMs do site de replicação primária, execute o seguinte comando:
      ```bash
      # Execute as root 
      mount -o ro /hana/shared
      # Or if the above command returns an error
      sudo mount -o ro 10.23.1.7/HN1-shared-s1 /hana/shared
      ```
  
   A VM do HANA, que perdeu o acesso ao para `/hana/shared` , deve ser reiniciada ou interrompida, dependendo da configuração do cluster. Os recursos de cluster são migrados para outro site de replicação do sistema do HANA.  
         
   Se o cluster não tiver iniciado na VM, isso foi reiniciado, inicie o cluster executando: 

      ```bash
      # Start the cluster 
      pcs cluster start
      ```
   
   Quando o cluster for iniciado, o sistema de arquivos `/hana/shared` será montado automaticamente.     
   Se você definir AUTOMATED_REGISTER = "false", será necessário configurar a replicação de sistema SAP HANA no site secundário. Nesse caso, você pode executar esses comandos para reconfigurar SAP HANA como secundário.   

      ```bash
      # Execute on the secondary 
      su - hn1adm
      # Make sure HANA is not running on the secondary site. If it is started, stop HANA
      sapcontrol -nr 03 -function StopWait 600 10
      # Register the HANA secondary site
      hdbnsutil -sr_register --name=HANA_S1 --remoteHost=hana-s2-db1 --remoteInstance=03 --replicationMode=sync
      # Switch back to root and cleanup failed resources
      pcs resource cleanup SAPHana_HN1_HDB03
      ```

   O estado dos recursos, após o teste: 

      ```bash
      # Output of crm_mon
      #7 nodes configured
      #45 resources configured
    
      #Online: [ hana-s-mm hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      
      #Active resources:
    
      #rsc_st_azure    (stonith:fence_azure_arm):      Started hana-s-mm
      # Clone Set: fs_hana_shared_s1-clone [fs_hana_shared_s1]
      #    Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 ]
      # Clone Set: fs_hana_shared_s2-clone [fs_hana_shared_s2]
      #     Started: [ hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      # Clone Set: hana_nfs_s1_active-clone [hana_nfs_s1_active]
      #     Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 ]
      # Clone Set: hana_nfs_s2_active-clone [hana_nfs_s2_active]
      #     Started: [ hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      # Clone Set: SAPHanaTopology_HN1_HDB03-clone [SAPHanaTopology_HN1_HDB03]
      #     Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      # Master/Slave Set: msl_SAPHana_HN1_HDB03 [SAPHana_HN1_HDB03]
      #     Masters: [ hana-s2-db1 ]
      #     Slaves: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db2 hana-s2-db3 ]
      # Resource Group: g_ip_HN1_03
      #     nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hana-s2-db1
      #     vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hana-s2-db1
      ```


É recomendável testar exaustivamente a configuração de cluster SAP HANA, executando também os testes, documentados em [ha para SAP Hana em VMs do Azure no RHEL](./sap-hana-high-availability-rhel.md#test-the-cluster-setup).


## <a name="next-steps"></a>Próximas etapas

* [Planejamento e implementação de Máquinas Virtuais do Azure para o SAP][planning-guide]
* [Implantação de Máquinas Virtuais do Azure para SAP][deployment-guide]
* [Implantação do DBMS de Máquinas Virtuais do Azure para SAP][dbms-guide]
* [Volumes NFS v4.1 no Azure NetApp Files para SAP HANA](./hana-vm-operations-netapp.md)
* Para saber como estabelecer alta disponibilidade e planejar a recuperação de desastre de SAP HANA em VMs do Azure, consulte [alta disponibilidade de SAP Hana em VMS (máquinas virtuais) do Azure][sap-hana-ha].