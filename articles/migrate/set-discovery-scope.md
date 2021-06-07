---
title: Definir o escopo para a descoberta de servidores no VMware com as migrações para Azure
description: Descreve como definir o escopo de descoberta para servidores hospedados em avaliação e migração do VMware com migrações para Azure.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: how-to
ms.date: 03/13/2021
ms.openlocfilehash: 29ac42da6560a717f12cd256fdd71282e0bd313f
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104775350"
---
# <a name="set-discovery-scope-for-servers-in-vmware-environment"></a>Definir o escopo de descoberta para servidores no ambiente VMware

Este artigo descreve como limitar o escopo de descoberta de servidores no ambiente VMware quando você estiver:

- Descobrindo servidores com o [dispositivo migrações para Azure](migrate-appliance-architecture.md) quando você estiver usando a ferramenta migrações para Azure: descoberta e avaliação.
- Descobrindo servidores com o [dispositivo migrações para Azure](migrate-appliance-architecture.md) quando você estiver usando a ferramenta migrações para Azure: Server Migration, para migração sem agente de servidores do ambiente VMware para o Azure.

Quando você configura o dispositivo, ele se conecta ao vCenter Server e inicia a descoberta. Antes de conectar o dispositivo ao vCenter Server, você pode limitar a descoberta a datacenters do vCenter Server, clusters, uma pasta de clusters, hosts, uma pasta de hosts ou servidores individuais. Para definir o escopo, você atribui permissões na conta que o dispositivo usa para acessar o vCenter Server.

## <a name="before-you-start"></a>Antes de começar

Se você não tiver configurado uma conta de usuário do vCenter que a migração do Azure usa para descoberta, faça isso agora para [avaliação](./tutorial-discover-vmware.md#prepare-vmware) ou [migração sem agente](./migrate-support-matrix-vmware-migration.md#agentless-migration).


## <a name="assign-permissions-and-roles"></a>Atribuir permissões e funções

Você pode atribuir permissões em objetos de inventário do VMware usando um dos dois métodos:

- Na conta usada pelo dispositivo, atribua uma função com as permissões necessárias nos objetos que você deseja definir como escopo.
- Como alternativa, atribua uma função à conta no nível do datacenter e propague para os objetos filho. Em seguida, dê à conta uma função **sem acesso** , para cada objeto que você não deseja no escopo. Não recomendamos essa abordagem, pois ela é complicada e pode expor controles de acesso, pois cada novo objeto filho recebe automaticamente o acesso herdado do pai.

Não é possível delimitar a descoberta de inventário no nível da pasta do vCenter Server. Se você precisar delimitar a descoberta para servidores em uma pasta, crie um usuário e conceda acesso individualmente a cada servidor necessário. Há suporte para as pastas host e cluster.


### <a name="assign-a-role-for-assessment"></a>Atribuir uma função para avaliação

1. Na conta do vCenter do dispositivo que você está usando para descoberta, aplique a função **somente leitura** para todos os objetos pai que hospedam os servidores que você deseja descobrir e avaliar (host, cluster, pasta hosts, pasta clusters, até Datacenter).
2. Propague essas permissões para objetos filho na hierarquia.

    ![Atribuir permissões](./media/tutorial-assess-vmware/assign-perms.png)

### <a name="assign-a-role-for-agentless-migration"></a>Atribuir uma função para a migração sem agente

1. Na conta do vCenter do dispositivo que você está usando para migração, aplique uma função definida pelo usuário que tenha as [permissões necessárias](migrate-support-matrix-vmware-migration.md#vmware-requirements-agentless)para todos os objetos pai que hospedam os servidores que você deseja descobrir e migrar.
2. Você pode nomear a função com algo mais fácil de identificar. Por exemplo, <em>Azure_Migrate</em>.

## <a name="work-around-for-server-folder-restriction"></a>Solução alternativa para a restrição de pasta do servidor

Atualmente, a ferramenta migrações para Azure: descoberta e avaliação não pode descobrir servidores se o acesso for concedido no nível da pasta do servidor do vCenter. Se você quiser fazer o escopo de sua descoberta e avaliação por pastas do servidor, use essa solução alternativa.

1. Atribua permissões somente leitura em todos os servidores localizados nas pastas nas quais você deseja definir o escopo para descoberta e avaliação.
2. Conceda acesso somente leitura a todos os objetos pai que hospedam o host dos servidores, o cluster, a pasta hosts, a pasta clusters, até o datacenter. Não é necessário propagar as permissões para todos os objetos filho.
3. Para usar as credenciais para descoberta, selecione o datacenter como **escopo da coleção**.


A configuração do controle de acesso baseado em função garante que a conta de usuário do vCenter correspondente tenha acesso somente a servidores específicos do locatário.


## <a name="next-steps"></a>Próximas etapas

[Configurar o dispositivo](how-to-set-up-appliance-vmware.md)