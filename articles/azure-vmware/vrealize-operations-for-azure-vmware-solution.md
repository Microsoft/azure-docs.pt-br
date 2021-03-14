---
title: Configurar operações de vRealize para a solução do Azure VMware
description: Saiba como configurar operações de vRealize para sua nuvem privada da solução Azure VMware.
ms.topic: how-to
ms.date: 01/26/2021
ms.openlocfilehash: 3d2617f893c34fde6a154f9f93b5bda520c5a91a
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101738422"
---
# <a name="set-up-vrealize-operations-for-azure-vmware-solution"></a>Configurar operações de vRealize para a solução do Azure VMware


o vRealize Operations Manager é uma plataforma de gerenciamento de operações que permite que os administradores de infraestrutura do VMware monitorem os recursos do sistema. Esses recursos do sistema podem ser objetos de nível de aplicativo ou de infraestrutura (físico e virtual). A maioria dos administradores de VMware usou operações de vRealize para monitorar e gerenciar os componentes de nuvem privada VMware – vCenter, ESXi, NSX-T, vSAN e VMware HCX.  Cada nuvem privada provisionada da solução Azure VMware inclui uma implantação do vCenter, NSX-T, vSAN e HCX dedicada. 

Revisão completa [antes de começar](#before-you-begin) e [pré-requisitos](#prerequisites) primeiro. Em seguida, vamos orientá-lo pelas duas topologias típicas de implantação:

> [!div class="checklist"]
> * [Operações de vRealize locais Gerenciando a implantação de solução do Azure VMware](#on-premises-vrealize-operations-managing-azure-vmware-solution-deployment)
> * [Operações de vRealize em execução na implantação da solução Azure VMware](#vrealize-operations-running-on-azure-vmware-solution-deployment)

## <a name="before-you-begin"></a>Antes de começar
* Examine a [documentação do produto vRealize Operations Manager](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html) para saber mais sobre a implantação de operações do vRealize. 
* Examine a [série de tutoriais](tutorial-network-checklist.md) do SDDC (Datacenter Definido pelo Software) da Solução VMware no Azure.
* Opcionalmente, examine a documentação do produto do [controlador remoto do VRealize Operations](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-263F9219-E801-4383-8A59-E84F3D01ED6B.html) para as operações vRealize locais Gerenciando a opção de implantação de solução do Azure VMware. 


## <a name="prerequisites"></a>Pré-requisitos
* [vRealize Operations Manager](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html) instalado.
* Uma VPN ou um Azure ExpressRoute configurado entre o local e a solução do Azure VMware SDDC.
* Uma nuvem privada da solução Azure VMware foi implantada no Azure.



## <a name="on-premises-vrealize-operations-managing-azure-vmware-solution-deployment"></a>Operações de vRealize locais Gerenciando a implantação de solução do Azure VMware
A maioria dos clientes tem uma implantação local existente de operações de vRealize para gerenciar um ou mais domínios vCenters no local. Quando eles provisionam uma nuvem privada da solução Azure VMware, eles conectam seu ambiente local com sua nuvem privada usando uma solução de VPN do Azure ExpressRoute ou camada 3.  

:::image type="content" source="media/vrealize-operations-manager/vrealize-operations-deployment-option-1.png" alt-text="Operações de vRealize locais Gerenciando a implantação de solução do Azure VMware" border="false":::

Para estender os recursos de operações do vRealize para a nuvem privada da solução Azure VMware, você cria uma [instância de adaptador para os recursos de nuvem privada](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.config.doc/GUID-640AD750-301E-4D36-8293-1BFEB67E2600.html). Ele coleta dados da nuvem privada da solução Azure VMware e os coloca em operações vRealize locais. A instância Operations Manager local do vRealize pode se conectar diretamente ao vCenter e ao NSX-T Manager na solução VMware do Azure. Opcionalmente, você pode implantar um coletor remoto de operações do vRealize na nuvem privada da solução Azure VMware. O coletor compacta e criptografa os dados coletados da nuvem privada antes que eles sejam enviados pela rede de ExpressRoute ou VPN para o vRealize Operations Manager em execução no local. 

> [!TIP]
> Consulte a [documentação do VMware](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html) para obter um guia passo a passo para a instalação do vRealize Operations Manager. 



## <a name="vrealize-operations-running-on-azure-vmware-solution-deployment"></a>Operações de vRealize em execução na implantação da solução Azure VMware

Outra opção é implantar uma instância do vRealize Operations Manager em um cluster vSphere na nuvem privada. 

>[!IMPORTANT]
>Atualmente, essa opção não tem suporte do VMware.

:::image type="content" source="media/vrealize-operations-manager/vrealize-operations-deployment-option-2.png" alt-text="Operações de vRealize em execução na solução VMware do Azure" border="false":::

Depois que a instância tiver sido implantada, você poderá configurar operações vRealize para coletar dados do vCenter, ESXi, NSX-T, vSAN e HCX. 



## <a name="known-limitations"></a>Limitações conhecidas

- O usuário **cloudadmin \@ vSphere. local** na solução VMware do Azure tem [privilégios limitados](concepts-role-based-access-control.md).  As VMs (máquinas virtuais) na solução do Azure VMware não dão suporte à coleta de memória no convidado usando ferramentas do VMware.  A utilização de memória ativa e consumida continua a funcionar nesse caso.
- A otimização de carga de trabalho para a tentativa de negócios baseada em host não funciona porque as soluções do Azure VMware gerenciam configurações de cluster, incluindo configurações de DRS.
- A otimização da carga de trabalho para o posicionamento entre clusters dentro do SDDC usando a intenção de negócios baseada em cluster tem suporte total com o vRealize Operations Manager 8,0 e em diante. No entanto, a otimização da carga de trabalho não reconhece os pools de recursos e coloca as VMs no nível do cluster. Um usuário pode corrigi-lo manualmente na interface vCenter Server da solução VMware do Azure.
- Você não pode entrar no vRealize Operations Manager usando suas credenciais de vCenter Server de solução do Azure VMware. 
- A solução Azure VMware não dá suporte ao plug-in vRealize Operations Manager.

Ao conectar a solução do Azure VMware vCenter ao vRealize Operations Manager usando uma conta vCenter Server Cloud, você verá um aviso:

:::image type="content" source="./media/vrealize-operations-manager/warning-adapter-instance-creation-succeeded.png" alt-text="Êxito na criação da instância do adaptador de aviso":::

O aviso ocorre porque o usuário **cloudadmin \@ vSphere. local** na solução Azure VMware não tem privilégios suficientes para fazer todas as ações vCenter Server necessárias para o registro. No entanto, os privilégios são suficientes para a instância do adaptador fazer a coleta de dados, como mostrado abaixo:

:::image type="content" source="./media/vrealize-operations-manager/adapter-instance-to-perform-data-collection.png" alt-text="Instância do adaptador para executar a coleta de dados":::

Para obter mais informações, consulte [privilégios necessários para configurar uma instância do adaptador do vCenter](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.core.doc/GUID-3BFFC92A-9902-4CF2-945E-EA453733B426.html).

<!-- LINKS - external -->


<!-- LINKS - internal -->




