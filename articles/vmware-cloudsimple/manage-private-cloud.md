---
title: Gerenciar a solução VMware do Azure pela nuvem privada de CloudSimple
description: Descreve os recursos disponíveis para gerenciar seus recursos de nuvem privada CloudSimple e atividade
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 05a2fb451b3acce1011c1d5f4cf17f0a865d57d0
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/22/2019
ms.locfileid: "67333135"
---
# <a name="manage-private-cloud-resources-and-activity"></a>Gerenciar a atividade e recursos da nuvem privada

Nuvens privadas são gerenciadas no portal de CloudSimple.  Verifique o status, recursos disponíveis, a atividade na nuvem privada e outras configurações do portal CloudSimple.

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="access-the-cloudsimple-portal"></a>Acessar o portal da CloudSimple

Acesso a [CloudSimple portal](access-cloudsimple-portal.md).

## <a name="view-the-list-of-private-clouds"></a>Exibir a lista de nuvens privadas

O **nuvens privadas** guia o **recursos** página lista todas as nuvens privadas em sua assinatura. As informações incluem o nome, número de vSphere clusters, local, estado atual das informações de nuvem, recursos e privadas.

![Página de nuvem privada](media/manage-private-cloud.png)

Selecione uma nuvem privada para ações e informações adicionais.

## <a name="private-cloud-summary"></a>Resumo de nuvem privada

Exiba um resumo abrangente de nuvem privada selecionada.  Página de resumo inclui os servidores DNS implantados na nuvem privada.  Você pode configurar o DNS de encaminhamento de servidores DNS local para seus servidores DNS de nuvem privada.  Para obter mais informações sobre o encaminhamento de DNS, consulte [configurar o DNS para resolução de nome para o vCenter de nuvem privada no local](https://docs.azure.cloudsimple.com/on-premises-dns-setup/).

![Resumo da nuvem privada](media/private-cloud-summary.png)

### <a name="available-actions"></a>Ações disponíveis

* [Iniciar o cliente do vSphere](https://docs.azure.cloudsimple.com/vsphere-access/). Acesso do vCenter para essa nuvem privada.
* [Nós de compra](create-nodes.md). Adicione nós a essa nuvem privada.
* [Expand](expand-private-cloud.md). Adicione nós a essa nuvem privada.
* **Refresh**. Atualize as informações nesta página.
* **Excluir**. Você pode excluir a nuvem privada a qualquer momento. **Antes de excluir, certifique-se de que você fez backup de todos os sistemas e dados.** A exclusão de uma nuvem privada exclui todas as VMs, configuração do vCenter e dados. Clique em **excluir** na seção de resumo para a nuvem privada selecionada. Após a exclusão, todos os dados de nuvem privada é apagado em um processo de eliminação de seguro e altamente compatível.
* [Alterar os privilégios de vSphere](escalate-private-cloud-privileges.md).  Escalone os privilégios nesta nuvem privada.

## <a name="private-cloud-vlanssubnets"></a>Private Cloud VLANS/sub-redes

Exiba a lista de VLANs/sub-redes definidas para a nuvem privada selecionada.  A lista inclui o gerenciamento de VLANs/sub-redes criadas quando a nuvem privada foi criada.

![Nuvem privada - VLANs/sub-redes](media/private-cloud-vlans-subnets.png) 

### <a name="available-actions"></a>Ações disponíveis

* [Adicionar VLANS/sub-redes](https://docs.azure.cloudsimple.com/create-vlan-subnet/). Adicione um VLAN/subconjunto para essa nuvem privada.

Selecione uma sub-rede/VLAN para as seguintes ações
* [Anexar tabela firewall](https://docs.azure.cloudsimple.com/firewall/). Anexe a uma tabela de firewall para essa nuvem privada.
* **Editar**
* **Excluir** (somente definido pelo usuário VLANs/sub-redes)

## <a name="private-cloud-activity"></a>Atividade de nuvem privada

Exiba as seguintes informações para a nuvem privada selecionada.  As informações de atividade são uma lista filtrada de todas as atividades para a nuvem privada selecionada.  Esta página mostra as atividades recentes até 25.

* Alertas recentes
* Eventos recentes
* Tarefas recentes
* Auditoria recente

![Nuvem privada - atividade](media/private-cloud-activity.png)

## <a name="cloud-racks"></a>Racks de nuvem

Racks de nuvem são os blocos de construção da sua nuvem privada. Cada rack fornece uma unidade de capacidade. CloudSimple configura automaticamente os racks de nuvem com base nas suas seleções ao criar ou expandir uma nuvem privada.  Exibir a lista completa de racks de nuvem, incluindo a nuvem privada que cada um é atribuído a.

![Nuvem privada - Racks de nuvem](media/private-cloud-cloudracks.png)

## <a name="vsphere-management-network"></a>Rede de gerenciamento do vSphere

Lista de recursos de gerenciamento do VMware e máquinas virtuais que estão configuradas atualmente sobre a nuvem privada. As informações incluem a versão do software, o nome de domínio totalmente qualificado (FQDN) e o endereço IP dos recursos.

![Nuvem privada - vSphere rede de gerenciamento](media/private-cloud-vsphere-management-network.png)

## <a name="next-steps"></a>Próximas etapas

* [Consuma a VMs do VMware no Azure](quickstart-create-vmware-virtual-machine.md)
* Saiba mais sobre [nuvens privadas](cloudsimple-private-cloud.md)