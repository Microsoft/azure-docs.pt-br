---
title: Solução do Azure VMware por CloudSimple-use a nuvem privada como local de desastre para cargas de trabalho locais
description: Descreve como configurar sua nuvem privada do CloudSimple como um site de recuperação de desastre para cargas de trabalho do VMware locais
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 5dd1f157984cdb300cc3b375d71654e5da54d1ae
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/05/2021
ms.locfileid: "97898123"
---
# <a name="set-up-cloudsimple-private-cloud-as-a-disaster-recovery-site-for-on-premises-vmware-workloads"></a>Configurar a nuvem privada do CloudSimple como um site de recuperação de desastre para cargas de trabalho do VMware locais

Sua nuvem privada do CloudSimple pode ser configurada como um site de recuperação para aplicativos locais para fornecer continuidade dos negócios em caso de desastre. A solução de recuperação baseia-se na replicação virtual zerto como a plataforma de replicação e orquestração. A infraestrutura crítica e as máquinas virtuais de aplicativo podem ser replicadas continuamente do seu vCenter local para sua nuvem privada. Você pode usar sua nuvem privada para testes de failover e para garantir a disponibilidade do seu aplicativo durante um desastre. Uma abordagem semelhante pode ser seguida para configurar a nuvem privada como um site primário que é protegido por um site de recuperação em um local diferente.

> [!NOTE]
> Consulte as considerações de dimensionamento de documento zerto [para a replicação virtual zerto](https://s3.amazonaws.com/zertodownload_docs/5.5U3/Zerto%20Virtual%20Replication%20Sizing.pdf) para obter diretrizes sobre como dimensionar seu ambiente de recuperação de desastre.

A solução CloudSimple:

* Elimina a necessidade de configurar um datacenter especificamente para recuperação de desastres (DR).
* Permite que você aproveite os locais do Azure onde o CloudSimple é implantado para resiliência geográfica mundial.
* Oferece uma opção para reduzir os custos de implantação e o custo total de propriedade para a recuperação de desastre.

A solução exige que você:

* Instale, configure e gerencie o zerto em sua nuvem privada.
* Forneça suas próprias licenças para zerto quando a nuvem privada for o site protegido. Você pode emparelhar o zerto em execução no site do CloudSimple com seu site local para licenciamento.

A figura a seguir mostra a arquitetura da solução zerto.

![Arquitetura](media/cloudsimple-zerto-architecture.png)

## <a name="how-to-deploy-the-solution"></a>Como implantar a solução

As seções a seguir descrevem como implantar uma solução de DR usando a replicação virtual zerto em sua nuvem privada.

1. [Pré-requisitos](#prerequisites)
2. [Configuração opcional na nuvem privada do CloudSimple](#optional-configuration-on-your-private-cloud)
3. [Configurar ZVM e VRA na nuvem privada CloudSimple](#set-up-zvm-and-vra-on-your-private-cloud)
4. [Configurar o grupo de proteção virtual zerto](#set-up-zerto-virtual-protection-group)

### <a name="prerequisites"></a>Pré-requisitos

Para habilitar a replicação virtual zerto do seu ambiente local para sua nuvem privada, conclua os seguintes pré-requisitos.

1. [Configure uma conexão VPN site a site entre sua rede local e sua nuvem privada do CloudSimple](set-up-vpn.md).
2. [Configure a pesquisa de DNS para que seus componentes de gerenciamento de nuvem privada sejam encaminhados para servidores DNS da nuvem privada](on-premises-dns-setup.md).  Para habilitar o encaminhamento de pesquisa de DNS, crie uma entrada de zona de encaminhamento no servidor DNS local para `*.cloudsimple.io` servidores DNS CloudSimple.
3. Configure a pesquisa de DNS para que os componentes do vCenter locais sejam encaminhados para servidores DNS locais.  Os servidores DNS devem estar acessíveis em sua nuvem privada do CloudSimple por meio de VPN site a site. Para obter assistência, envie uma [solicitação de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest), fornecendo as informações a seguir.  

    * Nome de domínio DNS local
    * Endereços IP do servidor DNS local

4. Instale um Windows Server em sua nuvem privada. O servidor é usado para instalar o zerto virtual Manager.
5. [Escalonar seus privilégios de CloudSimple](escalate-private-cloud-privileges.md).
6. Crie um novo usuário em sua nuvem privada vCenter com a função administrativa para usar como a conta de serviço do zerto virtual Manager.

### <a name="optional-configuration-on-your-private-cloud"></a>Configuração opcional em sua nuvem privada

1. Crie um ou mais pools de recursos em seu vCenter de nuvem privada para usar como pools de recursos de destino para VMs do seu ambiente local.
2. Crie uma ou mais pastas em sua nuvem privada vCenter para usar como pastas de destino para VMs do seu ambiente local.
3. Crie VLANs para a rede de failover e configure as regras de firewall. Abra uma [solicitação de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) para obter assistência.
4. Crie grupos de portas distribuídas para rede de failover e rede de teste para testar o failover de VMs.
5. Instale [servidores DHCP e DNS](dns-dhcp-setup.md) ou use um Active Directory controlador de domínio em seu ambiente de nuvem privada.

### <a name="set-up-zvm-and-vra-on-your-private-cloud"></a>Configurar o ZVM e o VRA em sua nuvem privada

1. Instale o zerto virtual Manager (ZVM) no Windows Server em sua nuvem privada.
2. Entre no ZVM usando a conta de serviço criada nas etapas anteriores.
3. Configure o licenciamento para o Gerenciador virtual zerto.
4. Instale o VRA (zerto virtual Replication Appliance) nos hosts ESXi da sua nuvem privada.
5. Emparelhe sua nuvem privada ZVM com sua ZVM local.

### <a name="set-up-zerto-virtual-protection-group"></a>Configurar o grupo de proteção virtual zerto

1. Crie um novo grupo de proteção virtual (VPG) e especifique a prioridade para o VPG.
2. Selecione as máquinas virtuais que exigem proteção para continuidade dos negócios e personalize a ordem de inicialização, se necessário.
3. Selecione o site de recuperação como sua nuvem privada e o servidor de recuperação padrão como o cluster de nuvem privada ou o grupo de recursos que você criou. Selecione **vsanDatastore** para o repositório de armazenamento de recuperação em sua nuvem privada.

    ![VPG](media/cloudsimple-zerto-vpg.png)

    > [!NOTE]
    > Você pode personalizar a opção de host para VMs individuais na opção configurações da VM.

4. Personalize as opções de armazenamento conforme necessário.
5. Especifique as redes de recuperação a serem usadas para rede de failover e rede de teste de failover como os grupos de portas distribuídas criadas anteriormente e personalize os scripts de recuperação conforme necessário.
6. Personalize as configurações de rede para VMs individuais, se necessário, e crie o VPG.
7. Failover de teste após a conclusão da replicação.

## <a name="reference"></a>Referência

[Documentação do zerto](https://www.zerto.com/myzerto/technical-documentation/)
