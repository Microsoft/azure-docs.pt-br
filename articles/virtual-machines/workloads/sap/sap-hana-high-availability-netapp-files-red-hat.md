---
title: Alta disponibilidade de SAP HANA escalar verticalmente com seja no RHEL | Microsoft Docs
description: Estabeleça alta disponibilidade de SAP HANA com o seja em VMs (máquinas virtuais) do Azure.
services: virtual-machines-linux
documentationcenter: ''
author: rdeltcheva
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/17/2021
ms.author: radeltch
ms.openlocfilehash: c5f94329920f8c850c0a47dd607ade8e83658b29
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104599911"
---
# <a name="high-availability-of-sap-hana-scale-up-with-azure-netapp-files-on-red-hat-enterprise-linux"></a>Alta disponibilidade de SAP HANA escalar verticalmente com Azure NetApp Files no Red Hat Enterprise Linux

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

Este artigo descreve como configurar a replicação de sistema SAP HANA em implantação de expansão, quando os sistemas de arquivos do HANA são montados via NFS, usando Azure NetApp Files (seja). No exemplo configurações e comandos de instalação, o número da instância **03** e a ID do sistema Hana **HN1** são usados. A replicação do SAP HANA consiste em um nó primário e, pelo menos, um nó secundário.

Quando as etapas deste documento são marcadas com os seguintes prefixos, o significado é o seguinte:

- **[A]**: a etapa se aplica a todos os nós
- **[1]**: a etapa aplica-se somente a Node1
- **[2]**: a etapa aplica-se somente a NODE2
 
Primeiro, leia os seguintes documentos e Notas SAP:

- A Nota SAP [1928533](https://launchpad.support.sap.com/#/notes/1928533), que tem:
    - A lista de tamanhos de VM do Azure que têm suporte para a implantação de software SAP.
    - Informações importantes sobre capacidade para tamanhos de VM do Azure.
    - O software SAP e combinações de SO (sistema operacional) e banco de dados com suporte.
    - A versão do kernel do SAP necessária para Windows e Linux no Microsoft Azure.
- A Nota SAP [2015553](https://launchpad.support.sap.com/#/notes/2015553) lista pré-requisitos para implantações de software SAP com suporte do SAP no Azure.
- O SAP Note [405827](https://launchpad.support.sap.com/#/notes/405827) lista o sistema de arquivos recomendado para o ambiente Hana.
- A observação do SAP [2002167](https://launchpad.support.sap.com/#/notes/2002167) tem as configurações do sistema operacional recomendadas para Red Hat Enterprise Linux.
- A observação do SAP [2009879](https://launchpad.support.sap.com/#/notes/2009879) tem diretrizes SAP HANA para Red Hat Enterprise Linux.
- A Nota SAP [2178632](https://launchpad.support.sap.com/#/notes/2178632) contém informações detalhadas sobre todas as métricas de monitoramentos relatadas para o SAP no Azure.
- A Nota SAP [2191498](https://launchpad.support.sap.com/#/notes/2191498) tem a versão necessária do SAP Host Agent para Linux no Azure.
- A Nota SAP [2243692](https://launchpad.support.sap.com/#/notes/2243692) tem informações sobre o licenciamento do SAP no Linux no Azure.
- A Nota SAP [1999351](https://launchpad.support.sap.com/#/notes/1999351) tem informações de solução de problemas adicionais para a Extensão de Monitoramento Avançado do Azure para SAP.
- O [SAP Community wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) tem todas as notas SAP necessárias para o Linux.
- [Planejamento e implementação de Máquinas Virtuais do Azure para SAP no Linux][planning-guide]
- [Implantação de Máquinas Virtuais do Azure para SAP no Linux][deployment-guide]
- [Implantação de Máquinas Virtuais do Azure do DBMS para SAP no Linux][dbms-guide]
- [SAP HANA a replicação do sistema no cluster pacemaker.](https://access.redhat.com/articles/3004101)
- Documentação geral do RHEL
    - [Visão geral do complemento de alta disponibilidade](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
    - [Administração de Add-On de alta disponibilidade.](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
    - [Referência de Add-On de alta disponibilidade.](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
    - [Configurar a replicação do sistema SAP HANA no Scale-Up em um cluster pacemaker quando os sistemas de recursos do HANA estiverem em compartilhamentos NFS](https://access.redhat.com/solutions/5156571)
- Documentação do RHEL específica do Azure:
    - [Políticas de suporte para clusters de alta disponibilidade RHEL-Máquinas Virtuais do Microsoft Azure como membros do cluster.](https://access.redhat.com/articles/3131341)
    - [Instalando e configurando um Red Hat Enterprise Linux 7,4 (e posterior) High-Availability cluster no Microsoft Azure.](https://access.redhat.com/articles/3252491)
    - [Instale o SAP HANA no Red Hat Enterprise Linux para uso no Microsoft Azure.](https://access.redhat.com/solutions/3193782)
    - [Configurar a replicação do sistema de expansão SAP HANA do cluster pacemaker quando os sistemas de arquivos do HANA estiverem em compartilhamentos NFS](https://access.redhat.com/solutions/5156571)
- [Aplicativos SAP NetApp no Microsoft Azure usando o Azure NetApp Files](https://www.netapp.com/us/media/tr-4746.pdf)
- [Volumes NFS v4.1 no Azure NetApp Files para SAP HANA](./hana-vm-operations-netapp.md)

## <a name="overview"></a>Visão geral

Tradicionalmente no ambiente de expansão, todos os sistemas de arquivos para SAP HANA são montados no armazenamento local. A configuração da alta disponibilidade do SAP HANA replicação do sistema no Red Hat Enterprise Linux é publicada no guia [Configurar a replicação do sistema SAP Hana no RHEL](./sap-hana-high-availability-rhel.md)

Para obter SAP HANA alta disponibilidade do sistema de expansão em compartilhamentos NFS [Azure NetApp files](../../../azure-netapp-files/index.yml) , precisamos de uma configuração de recurso adicional no cluster para que os recursos do Hana sejam recuperados, quando um nó perde o acesso aos compartilhamentos NFS em seja.  O cluster gerencia as montagens de NFS, permitindo que ele monitore a integridade dos recursos. As dependências entre as montagens do sistema de arquivos e os recursos de SAP HANA são impostas.  

![Escala vertical do SAP HANA HA em seja](./media/sap-hana-high-availability-rhel/sap-hana-scale-up-netapp-files-red-hat.png)

Os sistemas de SAP HANA são montados em compartilhamentos NFS usando Azure NetApp Files em cada nó. Os sistemas de arquivos/Hana/data,/Hana/log e/Hana/Shared são exclusivos para cada nó. 

Montado em Node1 (**hanadb1**)

- 10.32.2.4:/**hanadb1**-data-mnt00001 no/Hana/data
- 10.32.2.4:/**hanadb1**-log-mnt00001 no/Hana/log
- 10.32.2.4:/**hanadb1**-Shared-mnt00001 no/Hana/Shared

Montado em NODE2 (**hanadb2**)

- 10.32.2.4:/**hanadb2**-data-mnt00001 no/Hana/data
- 10.32.2.4:/**hanadb2**-log-mnt00001 no/Hana/log
- 10.32.2.4:/**hanadb2**-Shared-mnt00001 no/Hana/Shared

> [!NOTE]
> Os sistemas de arquivos/Hana/Shared,/Hana/data e/Hana/log não são compartilhados entre os dois nós. Cada nó de cluster tem seus próprios sistemas de arquivos separados.   

A configuração de replicação de sistema do SAP HANA usa um nome de host virtual dedicado e endereços IP virtuais. No Azure, um balanceador de carga é necessário para usar um endereço IP virtual. A lista a seguir mostra a configuração do balanceador de carga:

- Configuração de front-end: endereço IP 10.32.0.10 para hn1-DB
- Configuração de back-end: Conectado aos adaptadores de rede primários de todas as máquinas virtuais que devem ser parte do HANA System Replication
- Porta de Investigação: Porta 62503
- Regras de balanceamento de carga: 30313 TCP, 30315 TCP, 30317 TCP, 30340 TCP, 30341 TCP, 30342 TCP (se estiver usando o Azure Load Balancer básico)  

## <a name="set-up-the-azure-netapp-file-infrastructure"></a>Configurar a infraestrutura de arquivos do Azure NetApp

Antes de prosseguir com a configuração da infraestrutura do Azure NetApp Files, familiarize-se com a [documentação dos arquivos](../../../azure-netapp-files/index.yml)do Azure NetApp.

Azure NetApp Files está disponível em várias [regiões do Azure](https://azure.microsoft.com/global-infrastructure/services/?products=netapp). Verifique se sua região do Azure selecionada oferece Azure NetApp Files.

Para obter informações sobre a disponibilidade de Azure NetApp Files pela região do Azure, consulte [Azure NetApp files disponibilidade pela região do Azure](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all).

Antes de implantar Azure NetApp Files, solicite a integração com o Azure NetApp Files acessando [instruções de Azure NetApp files](../../../azure-netapp-files/azure-netapp-files-register.md).

### <a name="deploy-azure-netapp-files-resources"></a>Implantar recursos do Azure NetApp Files

As instruções a seguir pressupõem que você já tenha implantado sua [rede virtual do Azure](../../../virtual-network/virtual-networks-overview.md). Os recursos Azure NetApp Files e as VMs, em que os recursos de Azure NetApp Files serão montados, devem ser implantados na mesma rede virtual do Azure ou em redes virtuais emparelhadas do Azure.

1. Se você ainda não tiver implantado os recursos, solicite [a integração ao Azure NetApp files](../../../azure-netapp-files/azure-netapp-files-register.md).

2. Crie uma conta do NetApp na região do Azure selecionada seguindo as instruções em [criar uma conta do NetApp](../../../azure-netapp-files/azure-netapp-files-create-netapp-account.md).

3.  Configure um pool de capacidade Azure NetApp Files seguindo as instruções em [configurar um pool de capacidade Azure NetApp files](../../../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md).

    A arquitetura do HANA apresentada neste artigo usa um único pool de capacidade de Azure NetApp Files no nível de *ultra* serviço. Para cargas de trabalho do HANA no Azure, é recomendável usar um  [nível de serviço](../../../azure-netapp-files/azure-netapp-files-service-levels.md)Azure NetApp files *ultra* ou Premium.

4.  Delegue uma sub-rede para Azure NetApp Files, conforme descrito nas instruções em [delegar uma sub-rede para Azure NetApp files](../../../azure-netapp-files/azure-netapp-files-delegate-subnet.md).

5.  Implante Azure NetApp Files volumes seguindo as instruções em [criar um volume de NFS para Azure NetApp files](../../../azure-netapp-files/azure-netapp-files-create-volumes.md).

    Ao implantar os volumes, certifique-se de selecionar a versão NFSv 4.1. Implante os volumes na sub-rede designada do Azure NetApp Files. Os endereços IP dos volumes do Azure NetApp são atribuídos automaticamente.

    Tenha em mente que os recursos Azure NetApp Files e as VMs do Azure devem estar na mesma rede virtual do Azure ou em redes virtuais emparelhadas do Azure. Por exemplo, hanadb1-data-mnt00001, hanadb1-log-mnt00001 e assim por diante, são os nomes de volume e nfs://10.32.2.4/hanadb1-data-mnt00001, nfs://10.32.2.4/hanadb1-log-mnt00001 e assim por diante, são os caminhos de arquivo para os volumes de Azure NetApp Files.
    
    Em **hanadb1**

    - Volume hanadb1-data-mnt00001 (nfs://10.32.2.4:/hanadb1-data-mnt00001) 
    - Volume hanadb1-log-mnt00001 (nfs://10.32.2.4:/hanadb1-log-mnt00001)
    - Volume hanadb1-Shared-mnt00001 (nfs://10.32.2.4:/hanadb1-shared-mnt00001)
    
    Em **hanadb2**

    - Volume hanadb2-data-mnt00001 (nfs://10.32.2.4:/hanadb2-data-mnt00001)
    - Volume hanadb2-log-mnt00001 (nfs://10.32.2.4:/hanadb2-log-mnt00001)
    - Volume hanadb2-Shared-mnt00001 (nfs://10.32.2.4:/hanadb2-shared-mnt00001)

### <a name="important-considerations"></a>Considerações importantes

Ao criar seu Azure NetApp Files para SAP HANA sistemas de expansão, esteja ciente da seguinte consideração:

- O pool de capacidade mínima é 4 tebibytes (TiB).
- O tamanho mínimo do volume é 100 Gibibytes (GiB).
- Azure NetApp Files e todas as máquinas virtuais em que os volumes de Azure NetApp Files serão montados devem estar na mesma rede virtual do Azure ou em [redes virtuais emparelhadas](../../../virtual-network/virtual-network-peering-overview.md) na mesma região.
- A rede virtual selecionada deve ter uma sub-rede que é delegada para Azure NetApp Files.
- A taxa de transferência de um volume Azure NetApp Files é uma função da cota de volume e do nível de serviço, conforme documentado no [nível de serviço para Azure NetApp files](../../../azure-netapp-files/azure-netapp-files-service-levels.md). Quando você estiver dimensionando os volumes do Azure NetApp para o HANA, verifique se a taxa de transferência resultante atende aos requisitos do sistema do HANA.
- Com a [política de exportação](../../../azure-netapp-files/azure-netapp-files-configure-export-policy.md)de Azure NetApp files, você pode controlar os clientes permitidos, o tipo de acesso (leitura/gravação, somente leitura e assim por diante).
- O recurso de Azure NetApp Files ainda não reconhece a zona. Atualmente, o recurso não é implantado em todas as zonas de disponibilidade em uma região do Azure. Esteja atendo às possíveis implicações de latência em algumas regiões do Azure.

> [!IMPORTANT]
> Para cargas de trabalho do SAP HANA, baixa latência é fundamental. Trabalhe com seu representante da Microsoft para garantir que as máquinas virtuais e os volumes de Azure NetApp Files sejam implantados na proximidade.

### <a name="sizing-of-hana-database-on-azure-netapp-files"></a>Dimensionamento do banco de dados do HANA no Azure NetApp Files

A taxa de transferência de um volume Azure NetApp Files é uma função do tamanho do volume e do nível de serviço, conforme documentado no [nível de serviço para Azure NetApp files](../../../azure-netapp-files/azure-netapp-files-service-levels.md).

Conforme você projeta a infraestrutura para o SAP no Azure, esteja ciente de alguns requisitos mínimos de armazenamento pelo SAP, que se convertem em características de taxa de transferência mínima:

- Leitura-gravação em/Hana/log de 250 megabytes por segundo (MB/s) com tamanhos de e/s de 1 MB.
- Leia a atividade de pelo menos 400 MB/s para/Hana/data para os tamanhos de e/s de 16 MB e 64 MB.
- Atividade de gravação de pelo menos 250 MB/s para/Hana/data com tamanhos de e/s de 16 MB e 64 MB.

Os [limites de taxa de transferência do Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-service-levels.md) por 1 TiB de cota de volume são:

- Camada de armazenamento Premium-64 MiB/s.
- Camada de armazenamento ultra-128 MiB/s.

Para atender aos requisitos de produtividade mínima do SAP para/Hana/data e/Hana/log, e as diretrizes para o/Hana/Shared, os tamanhos recomendados seriam:

|    Volume    | Tamanho da camada de armazenamento Premium | Tamanho da camada de armazenamento ultra | Protocolo NFS com suporte |
| :----------: | :--------------------------: | :------------------------: | :--------------------: |
|  /hana/log   |            4 TiB             |           2 TiB            |          v4.1          |
|  /hana/data  |           6,3 TiB            |          3,2 TiB           |          v4.1          |
| /hana/shared |           1 x RAM            |          1 x RAM           |          v3 ou v4.1    |


> [!NOTE]
> As recomendações de dimensionamento Azure NetApp Files declaradas aqui são destinadas a atender aos requisitos mínimos que o SAP recomenda para seus provedores de infraestrutura. Em implantações reais de clientes e cenários de carga de trabalho, esses tamanhos podem não ser suficientes. Use essas recomendações como um ponto de partida e adapte-se, com base nos requisitos de sua carga de trabalho específica.

> [!TIP]
> Você pode redimensionar Azure NetApp Files volumes dinamicamente, sem precisar *desmontar* os volumes, parar as máquinas virtuais ou parar SAP Hana. Essa abordagem permite flexibilidade para atender às demandas de taxa de transferência esperada e inesperada do seu aplicativo.

> [!NOTE]
> Todos os comandos para montar o/Hana/Shared neste artigo são apresentados para volumes do NFSv 4.1/Hana/Shared.
> Se você implantou os volumes/Hana/Shared como volumes NFSv3, não se esqueça de ajustar os comandos mount para/Hana/Shared para NFSv3.


## <a name="deploy-linux-virtual-machine-via-azure-portal"></a>Implantar a máquina virtual do Linux via portal do Azure 

Primeiro, você precisa criar os volumes do Azure NetApp Files. Em seguida, execute as seguintes etapas:

1.  Crie um grupos de recursos.
2.  Crie uma rede virtual.
3.  Crie um conjunto de disponibilidade. Defina o máximo de domínio de atualização.
4.  Crie um balanceador de carga (interno). Recomenda-se o Standard Load Balancer.
    Selecione a rede virtual criada na etapa 2.
5.  Crie a máquina virtual 1 (**hanadb1**). 
6.  Crie a máquina virtual 2 (**hanadb2**).  
7.  Ao criar a máquina virtual, não adicionaremos nenhum disco, pois todos os nossos pontos de montagem estarão em compartilhamentos NFS de Azure NetApp Files. 

> [!IMPORTANT]
> Não há suporte para IP flutuante em uma configuração de IP secundário de NIC em cenários de balanceamento de carga. Para obter detalhes, consulte [limitações do Azure Load Balancer](../../../load-balancer/load-balancer-multivip-overview.md#limitations). Se você precisar de um endereço IP adicional para a VM, implante uma segunda NIC.    

> [!NOTE] 
> Quando as VMs sem endereços IP públicos forem colocadas no pool de back-end do Standard Azure Load Balancer (sem endereço IP público), não haverá nenhuma conectividade de saída com a Internet se não houver configuração adicional a fim de permitir o roteamento para pontos de extremidade públicos. Para obter detalhes sobre como alcançar conectividade de saída, veja [Conectividade de ponto de extremidade público para Máquinas Virtuais usando o Azure Standard Load Balancer em cenários de alta disponibilidade do SAP](./high-availability-guide-standard-load-balancer-outbound-connections.md).

8.  Se estiver usando o Standard Load Balancer, siga estas etapas de configuração:
    1.  Primeiro, crie um pool de IP de front-end:
        1.  Abra o balanceador de carga, selecione **pool de front-end** e selecione **Adicionar**.
        1.  Insira o nome do novo pool de IP front-end (por exemplo, **hana-frontend**).
        1.  Defina a **atribuição** como **estática** e insira o endereço IP (por exemplo, **10.32.0.10**).
        1.  Selecione **OK**.
        1.  Depois que o novo pool de IP de front-end for criado, anote o endereço IP do pool.
    1.  Em seguida, crie um pool de back-end:
        1.  Abra o balanceador de carga, selecione **pools de back-end** e selecione **Adicionar**.
        1.  Insira o nome do novo pool de back-end (por exemplo, **hana-backend**).
        1.  Selecione **Adicionar uma máquina virtual**.
        1.  Selecione **Máquina virtual**.
        1.  Selecione as máquinas virtuais do cluster SAP HANA e os respectivos endereços IP.
        1.  Selecione **Adicionar**.
    1.  Em seguida, crie uma investigação de integridade:
        1.  Abra o balanceador de carga, selecione **investigações de integridade** e selecione **Adicionar**.
        1.  Insira o nome da nova investigação de integridade (por exemplo, **hana-hp**).
        1.  Selecione TCP como o protocolo e porta 625 **03**. Mantenha o valor do **Intervalo** como 5 e o valor **Limite não íntegro** como 2.
        1.  Selecione **OK**.
    1.  Em seguida, crie as regras de balanceamento de carga:
        1.  Abra o balanceador de carga, selecione **Regras de balanceamento de carga** e selecione **Adicionar**.
        1.  Insira o nome da nova regra do balanceador de carga (por exemplo **hana-lb**).
        1.  Selecione o endereço IP de front-end, o pool de back-end e a investigação de integridade que você criou anteriormente (por exemplo, **hana-frontend**, **hana-backend** e **hana-hp**).
        1.  Selecione **Portas de HA**.
        1.  Aumente o **tempo limite de ociosidade** para 30 minutos.
        1.  Certifique-se de **habilitar IP Flutuante**.
        1.  Selecione **OK**.


9. Como alternativa, se o seu cenário impõe o uso do balanceador de carga básico, siga estas etapas de configuração:
    1.  Configure o balanceador de carga. Primeiro, crie um pool de IP de front-end:
        1.  Abra o balanceador de carga, selecione **pool de front-end** e selecione **Adicionar**.
        1.  Insira o nome do novo pool de IP front-end (por exemplo, **hana-frontend**).
        1.  Defina a **atribuição** como **estática** e insira o endereço IP (por exemplo, **10.32.0.10**).
        1.  Selecione **OK**.
        1.  Depois que o novo pool de IP de front-end for criado, anote o endereço IP do pool.
    1.  Em seguida, crie um pool de back-end:
        1.  Abra o balanceador de carga, selecione **pools de back-end** e selecione **Adicionar**.
        1.  Insira o nome do novo pool de back-end (por exemplo, **hana-backend**).
        1.  Selecione **Adicionar uma máquina virtual**.
        1.  Selecione o conjunto de disponibilidade criado na etapa 3.
        1.  Selecione as máquinas virtuais do cluster do SAP HANA.
        1.  Selecione **OK**.
    1.  Em seguida, crie uma investigação de integridade:
        1.  Abra o balanceador de carga, selecione **investigações de integridade** e selecione **Adicionar**.
        1.  Insira o nome da nova investigação de integridade (por exemplo, **hana-hp**).
        1.  Selecione **TCP** como o protocolo e porta 625 **03**. Mantenha o valor do **Intervalo** como 5 e o valor **Limite não íntegro** como 2.
        1.  Selecione **OK**.
    1.  Para o SAP HANA 1.0, crie as regras de balanceamento de carga:
        1.  Abra o balanceador de carga, selecione **Regras de balanceamento de carga** e selecione **Adicionar**.
        1.  Insira o nome da nova regra do balanceador de carga (por exemplo, hana-lb-3 **03** 15).
        1.  Selecione o endereço IP de front-end, o pool de back-end e a investigação de integridade que você criou anteriormente (por exemplo, **hana-frontend**).
        1.  Mantenha o **Protocolo** definido como **TCP** e insira a porta 3 **03** 15.
        1.  Aumente o **tempo limite de ociosidade** para 30 minutos.
        1.  Certifique-se de **habilitar IP Flutuante**.
        1.  Selecione **OK**.
        1.  Repita essas etapas para a porta 3 **03** 17.
    1.  Para o SAP HANA 2.0, crie as regras de balanceamento de carga para o banco de dados do sistema:
        1.  Abra o balanceador de carga, selecione **Regras de balanceamento de carga** e selecione **Adicionar**.
        1.  Insira o nome da nova regra do balanceador de carga (por exemplo, hana-lb-3 **03** 13).
        1.  Selecione o endereço IP de front-end, o pool de back-end e a investigação de integridade que você criou anteriormente (por exemplo, **hana-frontend**).
        1.  Mantenha o **Protocolo** definido como **TCP** e insira a porta 3 **03** 13.
        1.  Aumente o **tempo limite de ociosidade** para 30 minutos.
        1.  Certifique-se de **habilitar IP Flutuante**.
        1.  Selecione **OK**.
        1.  Repita essas etapas para a porta 3 **03** 14.
    1.  Para o SAP HANA 2.0, primeiro crie as regras de balanceamento de carga para o banco de dados do locatário:
        1.  Abra o balanceador de carga, selecione **Regras de balanceamento de carga** e selecione **Adicionar**.
        1.  Insira o nome da nova regra do balanceador de carga (por exemplo hana-lb-3 **03** 40).
        1.  Selecione o endereço IP de front-end, o pool de back-end e a investigação de integridade criados anteriormente (por exemplo, **hana-frontend**).
        1.  Mantenha o **Protocolo** definido como **TCP** e insira a porta 3 **03** 40.
        1.  Aumente o **tempo limite de ociosidade** para 30 minutos.
        1.  Certifique-se de **habilitar IP Flutuante**.
        1.  Selecione **OK**.
        1.  Repita essas etapas para portas 3 **03** 41 e 3 **03** 42.

Para obter mais informações sobre as portas necessárias para SAP HANA, leia o capítulo [conexões a bancos de dados de locatário](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6/latest/en-US/7a9343c9f2a2436faa3cfdb5ca00c052.html) no guia [SAP Hana bancos de dados de locatários](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6) ou SAP Note [2388694](https://launchpad.support.sap.com/#/notes/2388694).

> [!IMPORTANT]
> Não habilite carimbos de data/hora de TCP em VMs do Azure posicionadas de forma subjacente em relação ao Azure Load Balancer. Habilitar carimbos de data/hora de TCP fará com que as investigações de integridade falhem. Defina o parâmetro **net.ipv4.tcp_timestamps** para **0**. Para obter detalhes, veja [Investigações de integridade do Load Balancer](../../../load-balancer/load-balancer-custom-probe-overview.md). Veja também a nota SAP [2382421](https://launchpad.support.sap.com/#/notes/2382421).

## <a name="mount-the-azure-netapp-files-volume"></a>Montar o volume de Azure NetApp Files

1. **[A]** criar pontos de montagem para os volumes de banco de dados do Hana. 

    ```
    mkdir -p /hana/data
    mkdir -p /hana/log
    mkdir -p /hana/shared
    ```

2. **[A]** verificar a configuração de domínio do NFS. Verifique se o domínio está configurado como o domínio de Azure NetApp Files padrão, ou seja, **defaultv4iddomain.com** e o mapeamento está definido como **ninguém**.

    ```
    sudo cat /etc/idmapd.conf
    # Example
    [General]
    Domain = defaultv4iddomain.com
    [Mapping]
    Nobody-User = nobody
    Nobody-Group = nobody
    ```

    > [!IMPORTANT]    
    > Certifique-se de definir o domínio NFS em/etc/idmapd.conf na VM para corresponder à configuração de domínio padrão em Azure NetApp Files: **defaultv4iddomain.com**. Se houver uma incompatibilidade entre a configuração de domínio no cliente NFS (ou seja, a VM) e o servidor NFS, ou seja, a configuração da Azure NetApp, as permissões para arquivos nos volumes do Azure NetApp que são montados nas VMs serão exibidas como ninguém.
    

3. **[1]** montar os volumes específicos do nó em Node1 (**hanadb1**) 

    ```
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb1-shared-mnt00001 /hana/shared
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb1-log-mnt00001 /hana/log
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb1-data-mnt00001 /hana/data
    ```
    
4.  **[2]** montar os volumes específicos do nó em NODE2 (**hanadb2**)
    
    ```
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb2-shared-mnt00001 /hana/shared
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb2-log-mnt00001 /hana/log
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb2-data-mnt00001 /hana/data
    ```

5. **[A]** Verifique se todos os volumes do Hana estão montados com o protocolo NFS versão NFSv4.

    ```
    sudo nfsstat -m
    
    # Verify that flag vers is set to 4.1 
    # Example from hanadb1
    
    /hana/log from 10.32.2.4:/hanadb1-log-mnt00001
    Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.32.0.4,local_lock=none,addr=10.32.2.4
    /hana/data from 10.32.2.4:/hanadb1-data-mnt00001
    Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.32.0.4,local_lock=none,addr=10.32.2.4
    /hana/shared from 10.32.2.4:/hanadb1-shared-mnt00001
    Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.32.0.4,local_lock=none,addr=10.32.2.4
    ```

6. **[A]** verificar **nfs4_disable_idmapping**. Ele deve ser definido como **Y**. Para criar a estrutura de diretório onde **nfs4_disable_idmapping** está localizado, execute o comando Mount. Você não poderá criar o diretório manualmente em /sys/modules, pois o acesso é reservado para o kernel e os drivers.

    ```
    # Check nfs4_disable_idmapping 
    cat /sys/module/nfs/parameters/nfs4_disable_idmapping
    
    # If you need to set nfs4_disable_idmapping to Y
    echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    
    # Make the configuration permanent
    echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
    ```

   Para obter mais detalhes sobre como alterar **nfs_disable_idmapping** parâmetro, consulte [https://access.redhat.com/solutions/1749883](https://access.redhat.com/solutions/1749883) . 


## <a name="sap-hana-installation"></a>Instalação do SAP HANA

1. **[A]** Configure a resolução do nome do host para todos os hosts.

   É possível usar um servidor DNS ou modificar o arquivo /etc/hosts em todos os nós. Este exemplo mostra como usar o arquivo /etc/hosts. Substitua o endereço IP e o nome do host nos comandos a seguir:

   ```
   sudo vi /etc/hosts
   # Insert the following lines in the /etc/hosts file. Change the IP address and hostname to match your environment  
   10.32.0.4   hanadb1
   10.32.0.5   hanadb2
   ```

2. **[A]** RHEL para configuração do Hana

   Configure o RHEL conforme descrito na observação abaixo do SAP com base na sua versão do RHEL

   - [2292690 - Banco de dados do SAP HANA: configurações do sistema operacional recomendadas para RHEL 7](https://launchpad.support.sap.com/#/notes/2292690)
   - [2777782-SAP HANA DB: configurações de so recomendadas para RHEL 8](https://launchpad.support.sap.com/#/notes/2777782)
   - [2455582-Linux: executando aplicativos SAP compilados com GCC 6. x](https://launchpad.support.sap.com/#/notes/2455582)
   - [2593824-Linux: executando aplicativos SAP compilados com GCC 7. x](https://launchpad.support.sap.com/#/notes/2593824) 
   - [2886607-Linux: executando aplicativos SAP compilados com o GCC 9. x](https://launchpad.support.sap.com/#/notes/2886607)

3. **[A]**  Instalar o SAP HANA

   Iniciado com o HANA 2,0 SPS 01, MDC é a opção padrão. Quando você instala o sistema HANA, o SYSTEMDB e um locatário com o mesmo SID serão criados juntos. Em algum caso, você não deseja o locatário padrão. Caso não queira criar um locatário inicial junto com a instalação, você pode seguir a observação do SAP [2629711](https://launchpad.support.sap.com/#/notes/2629711)

    Execute o **hdblcm** programa a partir do DVD do HANA. Insira os valores a seguir no prompt:  
    Escolha a instalação: insira **1** (para instalar)  
    Selecione os componentes adicionais para instalação: Insira **1**.  
    Insira o caminho de instalação [/Hana/Shared]: pressione ENTER para aceitar o padrão  
    Insira o nome do host local [..]: pressione ENTER para aceitar o padrão  
    Você deseja adicionar outros hosts ao sistema? (s/n) [n]: **n**  
    Insira SAP HANA ID do sistema: insira **HN1**.  
    Insira o número da instância [00]: Enter **03**  
    Selecione o modo de banco de dados/insira o índice [1]: pressione ENTER para aceitar o padrão  
    Selecione uso do sistema/inserir índice [4]: Enter **4** (para personalizado)  
    Insira o local dos volumes de dados [/Hana/data]: pressione ENTER para aceitar o padrão  
    Insira o local dos volumes de log [/Hana/log]: pressione ENTER para aceitar o padrão  
    Restringir a alocação máxima de memória? [n]: pressione ENTER para aceitar o padrão  
    Insira o nome do host do certificado para o host '... ' [...]: pressione ENTER para aceitar o padrão  
    Insira a senha do usuário do agente de host do SAP (sapadm): Insira a senha do usuário do agente do host  
    Confirmar senha do usuário do agente de host SAP (sapadm): Insira a senha de usuário do agente de host novamente para confirmar  
    Insira a senha do administrador do sistema (hn1adm): Insira a senha do administrador do sistema  
    Confirmar senha do administrador do sistema (hn1adm): Insira a senha do administrador do sistema novamente para confirmar  
    Insira o diretório base do administrador do sistema [/usr/sap/HN1/home]: pressione ENTER para aceitar o padrão  
    Insira o Shell de logon do administrador do sistema [/bin/sh]: pressione ENTER para aceitar o padrão  
    Insira a ID de usuário do administrador do sistema [1001]: pressione ENTER para aceitar o padrão  
    Inserir ID do grupo de usuários (SAPs) [79]: pressione ENTER para aceitar o padrão  
    Inserir senha do usuário do banco de dados (sistema): Insira a senha de usuário do banco de dados  
    Confirmar senha do usuário do banco de dados (sistema): Insira a senha de usuário do banco de dados novamente para confirmar  
    Reiniciar o sistema após a reinicialização do computador? [n]: pressione ENTER para aceitar o padrão  
    Deseja continuar? (s/n): Valide o resumo. Digite **y** para continuar  

4. **[A]** Atualizar o Agente do Host do SAP

   Baixe o último arquivo do Agente de Host do SAP no [Centro de Software do SAP](https://launchpad.support.sap.com/#/softwarecenter) e execute o comando a seguir para atualizar o agente. Substitua o caminho do arquivo para apontar para o arquivo que você baixou:

   ```
   sudo /usr/sap/hostctrl/exe/saphostexec -upgrade -archive <path to SAP Host Agent SAR>
   ```

5. **[A]**  Configurar o firewall

   Crie a regra de firewall para a porta de investigação do Azure Load Balancer.

   ```
   sudo firewall-cmd --zone=public --add-port=62503/tcp
   sudo firewall-cmd --zone=public --add-port=62503/tcp –permanent
   ```

## <a name="configure-sap-hana-system-replication"></a>Configurar a replicação do sistema do SAP HANA

Siga as etapas em configurar [SAP Hana replicação do sistema](./sap-hana-high-availability-rhel.md#configure-sap-hana-20-system-replication) para configurar a replicação do sistema SAP Hana. 

## <a name="cluster-configuration"></a>Configuração do cluster

Esta seção descreve as etapas necessárias para que o cluster opere de forma direta quando o SAP HANA é instalado em compartilhamentos NFS usando Azure NetApp Files. 

### <a name="create-a-pacemaker-cluster"></a>Criar um cluster do Pacemaker

Siga as etapas em [Configurando pacemaker em Red Hat Enterprise Linux](./high-availability-guide-rhel-pacemaker.md) no Azure para criar um cluster pacemaker básico para esse servidor Hana.

### <a name="configure-filesystem-resources"></a>Configurar recursos do sistema de arquivos

Neste exemplo, cada nó de cluster tem seus próprios sistemas de filenfs do HANA/Hana/Shared,/Hana/data e/Hana/log.   

1. **[1]** colocar o cluster no modo de manutenção.

   ```
   pcs property set maintenance-mode=true
   ```

2. **[1]** crie os recursos do sistema de arquivos para as montagens **hanadb1** .

    ```
    pcs resource create hana_data1 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb1-data-mnt00001 directory=/hana/data fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb1_nfs
    pcs resource create hana_log1 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb1-log-mnt00001 directory=/hana/log fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb1_nfs
    pcs resource create hana_shared1 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb1-shared-mnt00001 directory=/hana/shared fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb1_nfs
    ```

3. **[2]** crie os recursos do sistema de arquivos para as montagens **hanadb2** .

    ```
    pcs resource create hana_data2 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb2-data-mnt00001 directory=/hana/data fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb2_nfs
    pcs resource create hana_log2 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb2-log-mnt00001 directory=/hana/log fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb2_nfs
    pcs resource create hana_shared2 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb2-shared-mnt00001 directory=/hana/shared fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb2_nfs
    ```

    `OCF_CHECK_LEVEL=20` o atributo é adicionado à operação monitor para que cada monitor execute um teste de leitura/gravação no sistema de arquivos. Sem esse atributo, a operação monitor só verifica se o sistema de arquivos está montado. Isso pode ser um problema porque, quando a conectividade é perdida, o sistema de arquivos pode permanecer montado apesar de estar inacessível.

    `on-fail=fence` o atributo também é adicionado à operação monitor. Com essa opção, se a operação do monitor falhar em um nó, esse nó será imediatamente isolado. Sem essa opção, o comportamento padrão é parar todos os recursos que dependem do recurso com falha, reiniciar o recurso com falha e, em seguida, iniciar todos os recursos que dependem do recurso com falha. Esse comportamento não só pode levar muito tempo quando um recurso SAPHana depende do recurso com falha, mas também pode falhar completamente. O recurso SAPHana não poderá parar com êxito se o servidor NFS que contém os executáveis do HANA estiver inacessível.

4. **[1]** Configurando restrições de local

   Configure restrições de local para garantir que os recursos que gerenciam montagens exclusivas hanadb1 nunca possam ser executados em hanadb2 e vice-versa.

    ```
    pcs constraint location hanadb1_nfs rule score=-INFINITY resource-discovery=never \#uname eq hanadb2
    pcs constraint location hanadb2_nfs rule score=-INFINITY resource-discovery=never \#uname eq hanadb1
    ```

    A `resource-discovery=never` opção é definida porque as montagens exclusivas para cada nó compartilham o mesmo ponto de montagem. Por exemplo, `hana_data1` usa o ponto `/hana/data` de montagem e `hana_data2` também usa o ponto de montagem `/hana/data` . Isso pode causar um falso positivo para uma operação de investigação, quando o estado do recurso é verificado na inicialização do cluster, e isso pode, por sua vez, causar um comportamento de recuperação desnecessário. Isso pode ser evitado pela configuração `resource-discovery=never`

5. **[1]** configurando recursos de atributo

   Configurar recursos de atributo. Esses atributos serão definidos como true se todas as montagens NFS de um nó (/Hana/data,/Hana/log e/Hana/Data) forem montadas e serão definidas como false caso contrário.

   ```
   pcs resource create hana_nfs1_active ocf:pacemaker:attribute active_value=true inactive_value=false name=hana_nfs1_active
   pcs resource create hana_nfs2_active ocf:pacemaker:attribute active_value=true inactive_value=false name=hana_nfs2_active
   ```

6. **[1]** Configurando restrições de local

   Configure restrições de local para garantir que o recurso de atributo hanadb1's nunca seja executado em hanadb2 e vice-versa.

   ```
   pcs constraint location hana_nfs1_active avoids hanadb2
   pcs constraint location hana_nfs2_active avoids hanadb1
   ```

7. **[1]** criando restrições de ordenação

   Configure restrições de ordenação para que os recursos de atributo de um nó sejam iniciados somente depois que todas as montagens NFS do nó forem montadas.
   
    ```
    pcs constraint order hanadb1_nfs then hana_nfs1_active
    pcs constraint order hanadb2_nfs then hana_nfs2_active
    ```

   > [!TIP]
   > Se sua configuração inclui sistemas de arquivos, fora do grupo `hanadb1_nfs` ou `hanadb2_nfs` , então inclua a `sequential=false` opção, de modo que não haja nenhuma dependência de ordenação entre os sistemas de arquivos. Todos os sistemas de arquivos devem iniciar antes `hana_nfs1_active` , mas não precisam iniciar em nenhum pedido em relação um ao outro. Para obter mais detalhes, consulte [como fazer configurar a replicação do sistema SAP Hana no Scale-Up em um cluster pacemaker quando os sistemas de recursos do Hana estiverem em compartilhamentos NFS](https://access.redhat.com/solutions/5156571)

### <a name="configure-sap-hana-cluster-resources"></a>Configurar SAP HANA recursos de cluster

1. Siga as etapas em [criar SAP Hana recursos de cluster](./sap-hana-high-availability-rhel.md#create-sap-hana-cluster-resources) para criar os recursos de SAP Hana no cluster. Uma vez que SAP HANA recursos são criados, precisamos criar uma restrição de regra de local entre SAP HANA recursos e sistemas de sistema (montagens NFS)

2. **[1]** configurar restrições entre os recursos de SAP Hana e as montagens de NFS

   As restrições de regra de local serão definidas para que os SAP HANA recursos possam ser executados em um nó somente se todas as montagens NFS do nó estiverem montadas.

    ```
    pcs constraint location SAPHanaTopology_HN1_03-clone rule score=-INFINITY hana_nfs1_active ne true and hana_nfs2_active ne true
    # On RHEL 7.x
    pcs constraint location SAPHana_HN1_03-master rule score=-INFINITY hana_nfs1_active ne true and hana_nfs2_active ne true
    # On RHEL 8.x
    pcs constraint location SAPHana_HN1_03-clone rule score=-INFINITY hana_nfs1_active ne true and hana_nfs2_active ne true
    # Take the cluster out of maintenance mode
    sudo pcs property set maintenance-mode=false
    ```

   Verificar o status do cluster e de todos os recursos
   > [!NOTE]
   > Este artigo contém referências ao termo *subordinado*, um termo que a Microsoft não usa mais. Quando o termo for removido do software, também o removeremos deste artigo.
   
    ```
    sudo pcs status
    
    Online: [ hanadb1 hanadb2 ]
    
    Full list of resources:
    
    rsc_hdb_azr_agt(stonith:fence_azure_arm):  Started hanadb1
    
    Resource Group: hanadb1_nfs
    hana_data1 (ocf::heartbeat:Filesystem):Started hanadb1
    hana_log1  (ocf::heartbeat:Filesystem):Started hanadb1
    hana_shared1   (ocf::heartbeat:Filesystem):Started hanadb1
    
    Resource Group: hanadb2_nfs
    hana_data2 (ocf::heartbeat:Filesystem):Started hanadb2
    hana_log2  (ocf::heartbeat:Filesystem):Started hanadb2
    hana_shared2   (ocf::heartbeat:Filesystem):Started hanadb2
    
    hana_nfs1_active   (ocf::pacemaker:attribute): Started hanadb1
    hana_nfs2_active   (ocf::pacemaker:attribute): Started hanadb2
    
    Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hanadb1 hanadb2 ]
    
    Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hanadb1 ]
    Slaves: [ hanadb2 ]
    
    Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):  Started hanadb1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):   Started hanadb1
    ```

## <a name="configure-hana-activeread-enabled-system-replication-in-pacemaker-cluster"></a>Configurar a replicação de sistema habilitada para leitura/ativo do HANA no cluster pacemaker

Começando com o SAP HANA 2,0 SPS 01, o SAP permite as configurações ativas/de leitura habilitadas para replicação de sistema SAP HANA, em que os sistemas secundários de replicação de sistema SAP HANA podem ser usados ativamente para cargas de trabalho com uso intenso de leitura. Para dar suporte a essa configuração em um cluster, um segundo endereço IP virtual é necessário, o que permite que os clientes acessem o banco de dados de SAP HANA habilitado para leitura secundário. Para garantir que o site de replicação secundária ainda possa ser acessado depois que um tomada tiver ocorrido, o cluster precisará mover o endereço IP virtual com o secundário do recurso SAPHana.

A configuração adicional, necessária para gerenciar a replicação de sistema habilitada para o HANA ativo/leitura em um cluster de alta disponibilidade do Red Hat com o segundo IP virtual, é descrita em [Configurar a replicação de sistema do Hana active/Read Enabled no cluster pacemaker](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability-rhel#configure-hana-activeread-enabled-system-replication-in-pacemaker-cluster).  

Antes de continuar, verifique se você configurou totalmente o cluster de alta disponibilidade do Red Hat, gerenciando SAP HANA banco de dados, conforme descrito nos segmentos acima da documentação.    


## <a name="test-the-cluster-setup"></a>Testar a configuração do cluster

Esta seção descreve como é possível testar a configuração. 

1. Antes de iniciar um teste, certifique-se de que pacemaker não tenha nenhuma ação com falha (por meio do status de PCs), não há nenhuma restrição de local inesperada (por exemplo, sobras de um teste de migração) e que a replicação do sistema HANA está no estado de sincronização, por exemplo, com systemReplicationStatus:

    ```
    sudo su - hn1adm -c "python /usr/sap/HN1/HDB03/exe/python_support/systemReplicationStatus.py"
    ```

2. Verificar a configuração do cluster para um cenário de falha quando um nó perde o acesso ao compartilhamento NFS (/Hana/Shared)

   Os agentes de recurso de SAP HANA dependem de binários, armazenados em `/hana/shared` para executar operações durante o failover. O sistema  `/hana/shared` de arquivos é montado em NFS no cenário apresentado.  
   É difícil simular uma falha, em que um dos servidores perde o acesso ao compartilhamento NFS. Um teste que pode ser executado é montar novamente o sistema de arquivos como somente leitura.
   Essa abordagem valida que o cluster será capaz de realizar failover, se o acesso a `/hana/shared` for perdido no nó ativo.     


   **Resultado esperado:** Ao fazer `/hana/shared` como sistema de arquivos somente leitura, o `OCF_CHECK_LEVEL` atributo do recurso `hana_shared1` que executa a operação de leitura/gravação no sistema de arquivos falhará, pois não é possível gravar nada no sistema de arquivos e executará o failover de recurso do Hana.  O mesmo resultado é esperado quando o nó do HANA perde o acesso aos compartilhamentos NFS. 

   Estado do recurso antes de iniciar o teste:

    ```
    sudo pcs status
    # Example output
    Full list of resources:
     rsc_hdb_azr_agt        (stonith:fence_azure_arm):      Started hanadb1

     Resource Group: hanadb1_nfs
         hana_data1 (ocf::heartbeat:Filesystem):    Started hanadb1
         hana_log1  (ocf::heartbeat:Filesystem):    Started hanadb1
         hana_shared1       (ocf::heartbeat:Filesystem):    Started hanadb1

    Resource Group: hanadb2_nfs
         hana_data2 (ocf::heartbeat:Filesystem):    Started hanadb2
         hana_log2  (ocf::heartbeat:Filesystem):    Started hanadb2
         hana_shared2       (ocf::heartbeat:Filesystem):    Started hanadb2

     hana_nfs1_active       (ocf::pacemaker:attribute):     Started hanadb1
     hana_nfs2_active       (ocf::pacemaker:attribute):     Started hanadb2

     Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
         Started: [ hanadb1 hanadb2 ]

     Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
         Masters: [ hanadb1 ]
         Slaves: [ hanadb2 ]

     Resource Group: g_ip_HN1_03
         nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hanadb1
         vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hanadb1
    ```

   Você pode inserir/Hana/Shared no modo somente leitura no nó do cluster ativo, usando o comando abaixo:

    ```
    sudo mount -o ro 10.32.2.4:/hanadb1-shared-mnt00001 /hana/shared
    ```

   hanadb1 será reinicializado ou estado desligado com base na ação definida em stonith ( `pcs property show stonith-action` ).  Depois que o servidor (hanadb1) estiver inoperante, a mudança de recurso do HANA para o hanadb2. Você pode verificar o status do cluster em hanadb2.

    ```
    pcs status

    Full list of resources:

     rsc_hdb_azr_agt        (stonith:fence_azure_arm):      Started hanadb2

     Resource Group: hanadb1_nfs
         hana_data1 (ocf::heartbeat:Filesystem):    Stopped
         hana_log1  (ocf::heartbeat:Filesystem):    Stopped
         hana_shared1       (ocf::heartbeat:Filesystem):    Stopped

     Resource Group: hanadb2_nfs
         hana_data2 (ocf::heartbeat:Filesystem):    Started hanadb2
         hana_log2  (ocf::heartbeat:Filesystem):    Started hanadb2
         hana_shared2       (ocf::heartbeat:Filesystem):    Started hanadb2

     hana_nfs1_active       (ocf::pacemaker:attribute):     Stopped
     hana_nfs2_active       (ocf::pacemaker:attribute):     Started hanadb2

     Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
         Started: [ hanadb2 ]
         Stopped: [ hanadb1 ]

     Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
         Masters: [ hanadb2 ]
         Stopped: [ hanadb1 ]

     Resource Group: g_ip_HN1_03
         nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hanadb2
         vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hanadb2
    ```

   É recomendável testar exaustivamente a configuração de cluster SAP HANA, executando também os testes descritos em [instalação SAP Hana replicação do sistema no RHEL](./sap-hana-high-availability-rhel.md#test-the-cluster-setup).

## <a name="next-steps"></a>Próximas etapas

* [Planejamento e implementação de Máquinas Virtuais do Azure para o SAP][planning-guide]
* [Implantação de Máquinas Virtuais do Azure para SAP][deployment-guide]
* [Implantação do DBMS de Máquinas Virtuais do Azure para SAP][dbms-guide]
* [Volumes NFS v4.1 no Azure NetApp Files para SAP HANA](./hana-vm-operations-netapp.md)