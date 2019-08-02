---
title: Perguntas Frequentes – solução CloudSimple do VMware
description: Perguntas frequentes para solução de VMware do Azure por CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 05/24/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: e187a4a75a27e206a632388f1e20a94da032eb08
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67595354"
---
# <a name="frequently-asked-questions-about-vmware-solution-by-cloudsimple"></a>Perguntas frequentes sobre a solução do VMware CloudSimple

Perguntas frequentes e respostas sobre a solução CloudSimple do VMware do Azure que ajudam você a entendem o serviço e como usá-lo. As perguntas e respostas são organizadas nas seguintes categorias:

* Serviço CloudSimple
* Conectividade
* Rede
* Segurança
* Computação
* Armazenamento
* VMware
* Integração do Azure
 
## <a name="cloudsimple-service"></a>Serviço CloudSimple

**O que é a solução CloudSimple do VMware do Azure?**

Solução de VMware do Azure por CloudSimple transforma e estende as cargas de trabalho do VMware para nuvens privadas e dedicadas no Azure em minutos. A solução provisiona, gerencia a infraestrutura e orquestra a cargas de trabalho entre local e o Azure. Como seus aplicativos são executados exatamente o mesmo local e no Azure, você aproveitar a elasticidade e serviços de nuvem sem a complexidade de rearquitetar seus aplicativos. CloudSimple reduz o custo total de propriedade com um modelo de consumo de nuvem que fornece sob demanda provisionamento pague à medida que você se expandir e a otimização da capacidade. Para recursos, benefícios e cenários, consulte [qual é a solução VMware do Azure CloudSimple?](cloudsimple-vmware-solutions-overview.md).

**O que é uma nuvem privada de CloudSimple?**

Você provisionar uma nuvem privada e dedicada que consiste em uma computação de alto desempenho, armazenamento e ambiente de rede implantado na infraestrutura do Microsoft Azure (espaço de hardware e datacenter) em locais do Azure. A nuvem privada fornece uma plataforma nativa do VMware como um serviço. Em termos de VMware, cada nuvem privada contém exatamente uma instância do vCenter Server. O vCenter Server gerencia vários nós de ESXi contidos em um ou mais clusters do vSphere, juntamente com o armazenamento vSAN correspondente. Um serviço de CloudSimple pode conter várias nuvens privadas em sua assinatura do Azure. Para obter mais informações sobre nuvens privadas, consulte [visão geral da nuvem privada](cloudsimple-private-cloud.md).

**Onde o serviço CloudSimple está disponível?**

CloudSimple está disponível nas regiões Leste dos EUA e Oeste dos EUA.

**Como habilitar o CloudSimple minha assinatura?**

Entre em contato com seu representante de conta da Microsoft em [ azurevmwaresales@microsoft.com ](mailto:azurevmwaresales@microsoft.com) para ativar sua assinatura para o serviço CloudSimple. Forneça sua ID de assinatura no email para o qual você deseja que o serviço CloudSimple habilitado. 

**Como acessar o portal do CloudSimple?**

Você pode acessar o portal de CloudSimple do portal do Azure. Para obter informações sobre como acessar o portal de CloudSimple, consulte [acessar a solução VMware pelo portal CloudSimple do portal do Azure](https://docs.azure.cloudsimple.com/access-cloudsimple-portal).

**Como aumentar a capacidade para uma nuvem privada?**

Provisionar nós do portal do Azure e expanda sua nuvem privada a partir do portal CloudSimple. Você pode expandir sua nuvem privada, adicionando nós a um cluster existente do vSphere ou criando um novo cluster do vSphere. Para obter informações sobre o procedimento, consulte [expandir uma nuvem privada de CloudSimple](https://docs.azure.cloudsimple.com/expand-private-cloud).

**O que acontece com meu nuvem privada durante a manutenção?**

CloudSimple fornece notificações periódicas dias antes da manutenção agendada. Manutenção é feita de maneira sem interrupções para garantir a disponibilidade da sua nuvem privada. Manutenção pode ser dos seguintes tipos:

- **Infraestrutura CloudSimple**: A infraestrutura de CloudSimple foi projetada para ser altamente disponível. Durante a manutenção, conectividade e a disponibilidade da sua nuvem privada é assegurada Atualizando componentes redundantes, um por vez sem qualquer impacto. Você tem acesso a seu vCenter de nuvem privada, todas as máquinas virtuais, conexão de internet da sua nuvem privada e conexões no local ou no Azure.
- **Portal de CloudSimple**: Durante a manutenção, alguns recursos no portal CloudSimple podem não estar acessíveis ou podem ser desabilitados. A notificação de manutenção inclui informações sobre o que pode ser feito no portal.

## <a name="connectivity"></a>Conectividade

**Quais são minhas opções de conectividade à minha rede de região CloudSimple?**

CloudSimple fornece três opções diferentes de conectividade para conectar-se à sua rede de região CloudSimple. Todas as três opções podem ser usadas juntos:

- Conexão de ExpressRoute do Azure do seu datacenter local à rede da região de CloudSimple: Uma alta velocidade baixa latência seguro privada conexão que liga o circuito de ExpressRoute do local com seu circuito do CloudSimple ExpressRoute usando o alcance Global. Para configurar a conexão, consulte [conectar-se do local a CloudSimple usando o ExpressRoute](https://docs.azure.cloudsimple.com/on-premises-connection).
- Conexão de ExpressRoute de sua rede virtual do Azure à sua rede de região CloudSimple: Uma alta velocidade e baixa latência seguro privada conexão que se associa a sua rede virtual no Azure com seu circuito do CloudSimple ExpressRoute usando gateways de rede virtual. Para configurar a conexão, consulte [seu ambiente de nuvem privada CloudSimple se conectar à rede virtual do Azure usando o ExpressRoute](https://docs.azure.cloudsimple.com/azure-expressroute-connection).
- Conexão de VPN site a site do seu datacenter local à sua rede de região CloudSimple: Uma rede privada virtual do dispositivo VPN local para sua região de nuvem privada CloudSimple. Para configurar a conexão, consulte [configurar uma conexão VPN entre sua rede local e a nuvem privada de CloudSimple](https://docs.azure.cloudsimple.com/set-up-vpn).

**Como conectar a uma nuvem privada?**

Você pode exibir detalhes da sua nuvem privada no portal do CloudSimple. Para se conectar ao vCenter que corresponde à sua nuvem privada, certifique-se de que uma conexão de rede é estabelecida por meio do site a site, ponto a site ou ExpressRoute. Em seguida, inicie o portal de CloudSimple do portal do Azure. Selecione **inicie o cliente vSphere** na home page ou na página de detalhes da nuvem privada.

**O que é a vantagem de um circuito do ExpressRoute?**

Um circuito do ExpressRoute do Azure fornece uma conexão segura de alta velocidade e baixa latência. CloudSimple fornece um circuito dedicado do ExpressRoute por região por cliente. Usando este circuito, você pode estabelecer uma conexão segura de local e sua assinatura do Azure.

**Quais são os custos de rede para conectar e CloudSimple? Existem quaisquer encargos de saída e para CloudSimple para o Azure? Existem quaisquer encargos de saída entre regiões?**

Não há nenhum custo para a saída da rede. As taxas padrão do Azure se aplicam a qualquer tráfego de saída de sua rede virtual ou de um circuito de ExpressRoute do local.

## <a name="networking"></a>Rede

**Quais recursos de rede estão disponíveis para minha nuvem privada?**

Você pode provisionar VLANs e as tabelas de firewall e sub-redes. Você pode atribuir endereços IP públicos e mapear para uma máquina virtual que é executado em sua nuvem privada. Para obter mais informações, consulte [visão geral de VLANs e sub-redes](cloudsimple-vlans-subnets.md), [visão geral de tabelas de Firewall](cloudsimple-firewall-tables.md), e [visão geral do endereço IP público](cloudsimple-public-ip-address.md).

**Como configurar sub-redes diferentes para meus aplicativos em minha nuvem privada?**

Você pode criar VLANs em sua nuvem privada do seu portal CloudSimple. Depois de criar a VLAN, você pode criar um grupo de portas distribuído em seu vCenter de nuvem privada usando a VLAN e criar máquinas virtuais conectadas ao grupo de portas distribuído. Você pode habilitar uma tabela de firewall para a sub-rede ou VLAN e definir regras de firewall para proteger o tráfego de rede.

**Quais configurações de firewall estão disponíveis para meu nuvens privadas?**

Você pode configurar regras para tráfego Norte-Sul e Leste-Oeste. As regras são definidas em uma tabela de firewall. A tabela de firewall pode ser anexada a VLANs em sua nuvem privada. Para o procedimento de instalação, consulte [configurar tabelas de firewall e regras para nuvens privadas](https://docs.azure.cloudsimple.com/firewall).

**Posso atribuir endereços IP públicos para VMs em meu ambiente de nuvem privada?**

No portal do CloudSimple, você pode facilmente alocar um novo endereço IP público e associá-lo a um endereço IP privado da sua máquina virtual ou um dispositivo. Você também pode criar novas regras de firewall ou aplique regras de firewall existente para permitir o tráfego de portas específicas e específico define de endereços IP no portal. Para o procedimento de instalação, consulte [alocar endereços IP públicos para um ambiente de nuvem privada](https://docs.azure.cloudsimple.com/public-ips).

## <a name="security"></a>Segurança

**Quais são minhas opções de segurança em CloudSimple?**

Uma nuvem privada de CloudSimple fornece os seguintes recursos de segurança para proteger o ambiente de nuvem privada:

- **Dados com criptografia em repouso:** Você pode criptografar dados em repouso que reside no armazenamento vSAN na sua nuvem privada. vSAN dá suporte a um servidor de gerenciamento de chaves externas, que pode ser implantado em seu ambiente local ou de rede virtual do Azure. Para obter mais informações, consulte [configurar a criptografia de vSAN para sua nuvem privada de CloudSimple](https://docs.azure.cloudsimple.com/vsan-encryption).
- **Segurança de rede:** Controle baixo tráfego de rede de e para sua nuvem privada da internet, no local e dentro de sub-redes da sua nuvem privada por meio de regras de firewall.
- **Conexão segura e privada:** Conexão segura e privada entre sua rede local e sua assinatura do Azure.

## <a name="compute"></a>Computação

**Que tipo de hosts estão disponíveis?**

CloudSimple oferece dois tipos de host:

* **Nó CS28**: CPU:2 2,2 GHz, 28 núcleos no total, 48 HT. RAM: 256 GB. Armazenamento: Cache de NVMe 1600 GB, 5760 GB de dados (todos os Flash). Rede: 2x25Gbe NIC.
* **Nó CS36**: CPU 2 x 2.3 GHz, total de 36 núcleos, 72 HT. RAM: 512 GB. Armazenamento: Cache de NVMe GB 3200 11.520-GB de dados (todos os Flash). Rede: 2x25Gbe NIC.

**Como são tratadas as falhas de hardware?**

Toda a infraestrutura de CloudSimple é monitorado continuamente pela plataforma CloudSimple e suas equipes de operações de serviço. Se for detectada uma falha de hardware, um novo nó é adicionado à sua nuvem privada. O nó com falha for removido para garantir a alta disponibilidade da sua nuvem privada.

## <a name="storage"></a>Armazenamento

**O tipo de armazenamento tem suporte em uma nuvem privada?**

Oferece CloudSimple **VMware flash All vSAN armazenamento** com cada nuvem privada. Cada vSphere é criado com seu próprio repositório de dados vSAN. Para obter mais informações, consulte [componentes de VMware de nuvem privada – armazenamento vSAN](https://docs.azure.cloudsimple.com/vmware-components/#vsan-storage).

**É a criptografia de dados com suporte?**
Sim. Você pode configurar o armazenamento vSAN em sua nuvem privada para usar um servidor de gerenciamento de chaves (KMS), que é implantado localmente ou no Azure para a criptografia de dados armazenados em vSAN.

**Como os discos com falha são tratados?**

Monitoramento de CloudSimple monitora continuamente a todos os componentes de hardware da nuvem privada. Se for detectada uma falha de disco ou um disco é identificado como falhando com base na heurística, um novo nó é adicionado automaticamente para a nuvem privada. O nó com um disco com falha ou falhando é removido da nuvem privada.

## <a name="vmware"></a>VMware

**Como executar o carregamento de grande escala e a migração de aplicativos e dados do local?**

CloudSimple fornece uma solução nativa do vSphere VMware. Qualquer ferramenta usada para a migração de dados em massa pode ser usada com uma nuvem privada de CloudSimple. Algumas das opções disponíveis são:

- HCX VMware para migração em massa de dados.
- Migração fria de dados usando o Storage vMotion do local para CloudSimple.

**Posso instalar as ferramentas do VMware?**

CloudSimple fornece uma solução nativa do vSphere VMware. Qualquer ferramenta usada para gerenciar um ambiente do vSphere locais podem ser usados em CloudSimple. CloudSimple dá suporte a um modelo de bring-your-própria licença (BYOL) para instalar as ferramentas do VMware.

**Como são gerenciadas de atualizações?**

CloudSimple gerencia e atualiza todos os componentes da infraestrutura da sua nuvem privada de uma maneira perfeita de sem interrupções. Qualquer atualização ou patch de segurança lançado por fornecedores de VMware ou de infraestrutura está agendada para atualização assim que ela será qualificada por CloudSimple.

CloudSimple não realiza upgrades ou atualizações de aplicativos instalados na nuvem privada.

## <a name="azure-integration"></a>Integração do Azure

**Quais serviços do Azure têm suporte?**

CloudSimple fornece conexão de ExpressRoute do Azure à sua assinatura no Azure. Todos os serviços que são executados em sua assinatura tem conectividade de rede para sua nuvem privada e podem se conectar à sua nuvem privada. Exemplos:

- **Azure Active Directory**: Usar o Azure Active Directory como uma fonte de identidade para seu vCenter CloudSimple.
- **Armazenamento do Azure**: Use o armazenamento para armazenar backups, imagens e outros dados da sua nuvem privada.
- **Aplicativos híbridos**: Você pode criar a arquitetura do aplicativo que abrange a nuvens públicas e privadas. Por exemplo, você pode criar servidores web no Azure que o aplicativo de acesso e os servidores de banco de dados em uma nuvem privada de CloudSimple.
- **O Azure Monitor** e **Central de segurança do Azure**: Carga de trabalho que executa o VMware pode usar o Monitor e a Central de segurança para registro em log, métricas de desempenho e gerenciamento de segurança.

**Como faço para mapear Meus locatários de VMware para o Azure?**

CloudSimple fornece a capacidade exclusiva de gerenciar suas VMs do VMware em uma nuvem privada do portal do Azure. Um pool de recursos do vCenter configurado com as restrições de recurso que você deseja que pode ser mapeado para sua assinatura pelo administrador global. 

**Quais benefícios de licenciamento obter com o Azure?**

Com CloudSimple, você pode aproveitar o benefício híbrido do Azure e economize até 90% nas licenças para preservar seus investimentos em licenças da Microsoft e reduza o custo total de propriedade em comparação comparada outras nuvens. Você também obter outras atualizações de segurança para Windows Server 2008 e Microsoft SQL Server 2008. Manter os custos baixos com BYOL para a nuvem para aplicativos comuns como Veeam, Zerto e outros. 
