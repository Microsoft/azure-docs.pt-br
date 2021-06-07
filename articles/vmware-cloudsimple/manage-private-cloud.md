---
title: Gerenciar a Nuvem Privada da Solução VMware da CloudSimple no Azure
description: Descreve as funcionalidades disponíveis para gerenciar seus recursos e sua atividade de Nuvem Privada da CloudSimple
author: Ajayan1008
ms.author: v-hborys
ms.date: 06/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 4f2f66c2e1e2e8aa596393d4c69a757138ab5a91
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97895199"
---
# <a name="manage-private-cloud-resources-and-activity"></a>Gerenciar recursos e a atividade da Nuvem Privada

Nuvens privadas são gerenciadas no portal da CloudSimple.  Verifique o status, os recursos disponíveis, a atividade na nuvem privada e outras configurações no portal da CloudSimple.

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="access-the-cloudsimple-portal"></a>Acessar o portal da CloudSimple

Acesse o [portal da CloudSimple](access-cloudsimple-portal.md).

## <a name="view-the-list-of-private-clouds"></a>Exibir a lista de Nuvens Privadas

A guia **Nuvens Privadas** na página **Recursos** lista todas as Nuvens Privadas em sua assinatura. As informações incluem o nome, o número de clusters vSphere, o local, o estado atual da nuvem privada e as informações do recurso.

![Página da Nuvem Privada](media/manage-private-cloud.png)

Selecione uma Nuvem Privada para obter mais informações e ações.

## <a name="private-cloud-summary"></a>Resumo da Nuvem Privada

Veja um resumo abrangente da Nuvem Privada selecionada.  A página Resumo inclui os servidores DNS implantados na Nuvem Privada.  É possível configurar o encaminhamento de DNS de servidores DNS locais para seus servidores DNS da Nuvem Privada.  Para obter mais informações sobre o encaminhamento de DNS, confira [Configurar o DNS para resolução de nomes para o vCenter de Nuvem Privada local](./on-premises-dns-setup.md).

![Resumo da Nuvem Privada](media/private-cloud-summary.png)

### <a name="available-actions"></a>Ações disponíveis

* [Iniciar cliente vSphere](./vcenter-access.md). Acessar o vCenter para esta Nuvem Privada.
* [Comprar nós](create-nodes.md). Adicionar nós a esta Nuvem Privada.
* [Expandir](expand-private-cloud.md). Adicionar nós a esta Nuvem Privada.
* **Atualizar**. Atualizar as informações sobre esta página.
* **Excluir**. É possível excluir a Nuvem Privada a qualquer momento. **Antes de excluir, verifique se você fez backup de todos os sistemas e dados.** A exclusão de uma Nuvem Privada exclui todas as VMs, a configuração do vCenter e os dados. Clique em **Excluir** na seção Resumo para a Nuvem Privada selecionada. Após a exclusão, todos os dados da Nuvem Privada são apagados em um processo de eliminação seguro e altamente compatível.
* [Alterar privilégios do vSphere](escalate-private-cloud-privileges.md).  Escalone seus privilégios nesta Nuvem Privada.

## <a name="private-cloud-vlanssubnets"></a>VLANs/sub-redes da Nuvem Privada

Veja a lista de VLANs/sub-redes definidas para a Nuvem Privada selecionada.  A lista inclui as sub-redes/VLANs de gerenciamento criadas quando a nuvem privada foi criada.

![Nuvem Privada – VLANs/sub-redes](media/private-cloud-vlans-subnets.png) 

### <a name="available-actions"></a>Ações disponíveis

* [Adicionar VLANS/sub-redes](./create-vlan-subnet.md). Adicionar uma VLAN/sub-rede a esta Nuvem Privada.

Selecionar uma VLAN/sub-rede para as ações a seguir
* [Anexar tabela de firewall](./firewall.md). Anexar uma tabela de firewall a esta Nuvem Privada.
* **Editar**
* **Excluir** (apenas VLANs/sub-redes definidas pelo usuário)

## <a name="private-cloud-activity"></a>Atividade de Nuvem Privada

Veja as informações a seguir para a Nuvem Privada selecionada.  As informações da atividade são uma lista filtrada de todas as atividades da Nuvem Privada selecionada.  Esta página mostra até 25 atividades recentes.

* Alertas recentes
* Eventos recentes
* Tarefas recentes
* Auditoria recente

![Nuvem Privada – Atividade](media/private-cloud-activity.png)

## <a name="cloud-racks"></a>Racks da Nuvem

Racks da nuvem são os blocos de construção de sua Nuvem Privada. Cada rack fornece uma unidade de capacidade. A CloudSimple configura automaticamente os racks da nuvem com base em suas seleções ao criar ou expandir uma Nuvem Privada.  Veja a lista completa de racks da nuvem, incluindo a Nuvem Privada a qual cada um é atribuído.

![Nuvem Privada – Racks da Nuvem](media/private-cloud-cloudracks.png)

## <a name="vsphere-management-network"></a>Rede de Gerenciamento do vSphere

Lista de recursos de gerenciamento e máquinas virtuais do VMware configurados no momento na Nuvem Privada. As informações incluem a versão do software, o FQDN (nome de domínio totalmente qualificado) e o endereço IP dos recursos.

![Nuvem Privada – Rede de Gerenciamento do vSphere](media/private-cloud-vsphere-management-network.png)

## <a name="next-steps"></a>Próximas etapas

* [Consumir VMs do VMware no Azure](quickstart-create-vmware-virtual-machine.md)
* Saiba mais sobre [Nuvens Privadas](cloudsimple-private-cloud.md)
