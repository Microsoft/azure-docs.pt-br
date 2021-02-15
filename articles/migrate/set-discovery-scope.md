---
title: Definir o escopo para a descoberta de VM VMware com migrações para Azure
description: Descreve como definir o escopo de descoberta para avaliação e migração de VM VMware com migrações para Azure.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: how-to
ms.date: 06/09/2020
ms.openlocfilehash: dc5bd178c837deea7a22fb3be5ba438085c0e748
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/07/2020
ms.locfileid: "96753545"
---
# <a name="set-discovery-scope-for-vmware-vms"></a>Definir escopo de descoberta para VMs VMware

Este artigo descreve como limitar o escopo de VMs VMware que você descobre quando você está:

- Descobrindo VMs com o [dispositivo migrações para Azure](migrate-appliance-architecture.md) quando você estiver usando a ferramenta migrações para Azure: Server Assessment.
- Descoberta de VMs com o [dispositivo migrações para Azure](migrate-appliance-architecture.md) quando você estiver usando a ferramenta migrações para Azure: Server Migration, para a migração sem agente de VMs VMware para o Azure.

Quando você configura o dispositivo, ele se conecta ao vCenter Server e inicia a descoberta. Antes de conectar o dispositivo ao vCenter Server, você pode limitar a descoberta a datacenters de vCenter Server, clusters, uma pasta de clusters, hosts, uma pasta de hosts ou VMs individuais. Para definir o escopo, você atribui permissões na conta que o dispositivo usa para acessar o vCenter Server.

## <a name="before-you-start"></a>Antes de começar

Se você não tiver configurado uma conta de usuário do vCenter que a migração do Azure usa para descoberta, faça isso agora para [avaliação](./tutorial-discover-vmware.md#prepare-vmware) ou [migração sem agente](./migrate-support-matrix-vmware-migration.md#agentless-migration).


## <a name="assign-permissions-and-roles"></a>Atribuir permissões e funções

Você pode atribuir permissões em objetos de inventário do VMware usando um dos dois métodos:

- Na conta usada pelo dispositivo, atribua uma função com as permissões necessárias nos objetos que você deseja definir como escopo.
- Como alternativa, atribua uma função à conta no nível do datacenter e propague para os objetos filho. Em seguida, dê à conta uma função **sem acesso** , para cada objeto que você não deseja no escopo. Não recomendamos essa abordagem, pois ela é complicada e pode expor controles de acesso, pois cada novo objeto filho recebe automaticamente o acesso herdado do pai.

Não é possível delimitar a descoberta de inventário no nível da pasta da VM do vCenter. Se você precisar delimitar a descoberta para VMs em uma pasta de VM, crie um usuário e conceda acesso individualmente a cada VM necessária. Há suporte para as pastas host e cluster.


### <a name="assign-a-role-for-assessment"></a>Atribuir uma função para avaliação

1. Na conta do vCenter do dispositivo que você está usando para descoberta, aplique a função **somente leitura** para todos os objetos pai que hospedam VMs que você deseja descobrir e avaliar (host, cluster, pasta hosts, pasta clusters, até Datacenter).
2. Propague essas permissões para objetos filho na hierarquia.

    ![Atribuir permissões](./media/tutorial-assess-vmware/assign-perms.png)

### <a name="assign-a-role-for-agentless-migration"></a>Atribuir uma função para a migração sem agente

1. Na conta do vCenter do dispositivo que você está usando para migração, aplique uma função definida pelo usuário que tenha as [permissões necessárias](migrate-support-matrix-vmware-migration.md#vmware-requirements-agentless)para todos os objetos pai que hospedam as VMs que você deseja descobrir e migrar.
2. Você pode nomear a função com algo mais fácil de identificar. Por exemplo, <em>Azure_Migrate</em>.

## <a name="work-around-vm-folder-restriction"></a>Contornar a restrição da pasta da VM

Atualmente, a ferramenta de avaliação do servidor não pode descobrir VMs se o acesso for concedido no nível da pasta da VM do vCenter. Se você quiser fazer o escopo de sua descoberta e avaliação por pastas de VM, use essa solução alternativa.

1. Atribua permissões somente leitura em todas as VMs localizadas nas pastas de VM nas quais você deseja definir o escopo para descoberta e avaliação.
2. Conceda acesso somente leitura a todos os objetos pai que hospedam o host das VMs, o cluster, a pasta hosts, a pasta clusters, até o datacenter. Não é necessário propagar as permissões para todos os objetos filho.
3. Para usar as credenciais para descoberta, selecione o datacenter como **escopo da coleção**.


A configuração do controle de acesso baseado em função garante que a conta de usuário do vCenter correspondente tenha acesso a apenas VMs específicas do locatário.


## <a name="next-steps"></a>Próximas etapas

[Configurar o dispositivo](how-to-set-up-appliance-vmware.md)