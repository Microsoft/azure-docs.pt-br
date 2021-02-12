---
title: Solução VMware no Azure pela CloudSimple
description: Saiba mais sobre as Solução VMware da CloudSimple no Azure, incluindo uma visão geral, inícios rápidos, conceitos, tutoriais e guias de instruções.
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.custom: seo-azure-migrate
keywords: suporte de vms, solução vmware da cloudsimple no azure, cloudsimple no azure, ferramentas de vms, documentação de vmware
ms.openlocfilehash: a3514a011f940b82702984737d0a93c5da402b55
ms.sourcegitcommit: 48e5379c373f8bd98bc6de439482248cd07ae883
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/12/2021
ms.locfileid: "98108374"
---
# <a name="azure-vmware-solution-by-cloudsimple"></a>Solução VMware no Azure pela CloudSimple

Seja bem-vindo ao único portal de ajuda da Solução VMware no Azure da CloudSimple.
No site de documentação, é possível aprender sobre os seguintes tópicos:

## <a name="overview"></a>Visão geral

Saiba mais sobre a Solução VMware no Azure da CloudSimple

* Saiba mais sobre os recursos, os benefícios e os cenários de uso em [O que é a Solução VMware no Azure da CloudSimple](cloudsimple-vmware-solutions-overview.md)
* Examine os [principais conceitos de administração](key-concepts.md)

## <a name="quickstart"></a>Guia de Início Rápido

Saiba como começar a usar a solução

* Entenda como [iniciar o serviço e a capacidade de compra](quickstart-create-cloudsimple-service.md)
* Saiba como criar um novo ambiente de VMware em [Configurar um ambiente de nuvem privada](quickstart-create-private-cloud.md)
* Saiba como unificar o gerenciamento na VMware e no Azure lendo o artigo [Consumir VMs do VMware no Azure](quickstart-create-vmware-virtual-machine.md)

## <a name="concepts"></a>Conceitos

Saiba mais sobre os seguintes conceitos

* Um [Serviço do CloudSimple](cloudsimple-service.md) (também conhecido como "Solução VMware no Azure da CloudSimple – Serviço"). Esse recurso deve ser criado uma vez por região.
* Capacidade de compra para o ambiente por meio da criação de um ou mais recursos de [nó da CloudSimple](cloudsimple-node.md). Esses recursos também são conhecidos como "Solução VMware no Azure da CloudSimple – Nó".
* Iniciar e configurar o ambiente do VMware usando as [nuvens privadas](cloudsimple-private-cloud.md).
* Unificar o gerenciamento usando as [Máquinas Virtuais da CloudSimple](cloudsimple-virtual-machines.md) (também conhecidas como "Solução VMware no Azure da CloudSimple – Máquina Virtual").
* Criar a rede subjacente usando [VLANs/sub-redes](cloudsimple-vlans-subnets.md).
* Segmentar e proteger a rede subjacente usando o recurso [Tabela de Firewall](cloudsimple-firewall-tables.md).
* Ter acesso seguro aos ambientes de VMware na WAN usando [Gateways de VPN](cloudsimple-vpn-gateways.md).
* Habilitar o acesso público para cargas de trabalho usando o [IP Público](cloudsimple-public-ip-address.md).
* Estabelecer conectividade com as Redes Virtuais do Azure e as redes locais usando a [Conexão de Rede do Azure](cloudsimple-azure-network-connection.md).
* Configurar destinos de alerta de email por meio do [Gerenciamento de Contas](cloudsimple-account.md).
* Exibir logs de usuário e a atividade do sistema usando as telas de [Gerenciamento de Atividades](cloudsimple-activity.md).
* Entender os vários [componentes de VMware](vmware-components.md).

## <a name="tutorials"></a>Tutoriais

Saiba como executar tarefas comuns, como:

* [Criar um Serviço da CloudSimple](create-cloudsimple-service.md) uma vez por região em que você quer implantar ambientes do VMware.
* Gerenciar a funcionalidade do serviço principal no [portal da CloudSimple](access-cloudsimple-portal.md).
* Habilitar a capacidade e otimizar a cobrança pela infraestrutura ao [Adquirir nós da CloudSimple](create-nodes.md).
* Gerenciar as configurações de ambiente do VMware com as Nuvens Privadas. É possível [criar](create-private-cloud.md), [gerenciar](manage-private-cloud.md), [expandir](expand-private-cloud.md) ou [reduzir](shrink-private-cloud.md) as Nuvens Privadas.
* Habilitar o gerenciamento unificado ao [mapear as Assinaturas do Azure](azure-subscription-mapping.md).
* Monitorar as atividades do usuário e do sistema usando as [páginas de Atividade](monitor-activity.md).
* Configurar a rede dos ambientes ao [criar e gerenciar sub-redes](create-vlan-subnet.md).
* Segmentar e proteger o ambiente usando [Tabelas e regras de firewall](firewall.md).
* Habilitar o acesso de entrada à Internet para cargas de trabalho ao [Alocar IPs públicos](public-ips.md).
* Habilitar a conectividade das redes internas ou das estações de trabalho do cliente ao [configurar o VPN](vpn-gateway.md).
* Habilitar as comunicações dos [ambientes locais](on-premises-connection.md), bem como das [Redes Virtuais do Azure](virtual-network-connection.md).
* Configurar destinos de alerta e exibir a capacidade adquirida total no [resumo da conta](account.md)
* Exibir os [usuários](users.md) que acessaram o portal da CloudSimple.
* Gerenciar máquinas virtuais do VMware no portal do Azure:
    * [Criar máquinas virtuais](azure-create-vm.md) no portal do Azure.
    * [Gerenciar as máquinas virtuais](azure-manage-vm.md) que você criou.

## <a name="how-to-guides"></a>Guias de instruções

Estes guias descrevem soluções para metas como:

* [Proteger o ambiente](private-cloud-secure.md)
* Instalar ferramentas de terceiros, habilitar outros usuários e uma origem de autenticação externa no vSphere usando a [elevação de privilégio](escalate-privileges.md).
* Configurar o acesso a vários serviços do VMware ao [configurar o DNS local](on-premises-dns-setup.md).
* Habilitar a alocação de nome e endereço para as cargas de trabalho ao [configurar o DNS e o DHCP da carga de trabalho](dns-dhcp-setup.md).
* Entender como o serviço garante a segurança e a funcionalidade na plataforma por meio de [atualizações e upgrades](vmware-components.md#updates-and-upgrades) do serviço.
* Economizar o TCO no backup ao criar uma arquitetura de backup de exemplo com um [software de backup de terceiros, como o Veeam](backup-workloads-veeam.md).
* Criar um ambiente seguro ao habilitar a criptografia em repouso com um [software de criptografia KMS de terceiros](vsan-encryption.md).
* Estender o gerenciamento do Azure AD (Azure Active Directory) para o VMware configurando a [origem da identidade do Azure AD](azure-ad.md).
