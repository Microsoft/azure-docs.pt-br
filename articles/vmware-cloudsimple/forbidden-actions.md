---
title: Ações proibidas durante o acesso elevado
description: O mecanismo do VMware reverte as alterações para garantir que o serviço permaneça sem interrupção quando o mecanismo do VMware detectar qualquer uma das ações proibidas a seguir.
titleSuffix: Azure VMware Solution by CloudSimple
ms.date: 10/28/2020
ms.topic: article
ms.service: azure-vmware-cloudsimple
author: divka78
ms.author: dikamath
ms.openlocfilehash: 81a09a9cfe749de56f7306a8d5e1ae5db01295fc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92915385"
---
# <a name="forbidden-actions-during-elevated-access"></a>Ações proibidas durante o acesso elevado

Durante o intervalo de tempo de elevação, algumas ações são proibidas. Quando o mecanismo do VMware detecta qualquer uma das seguintes ações proibidas, o mecanismo do VMware reverte as alterações para garantir que o serviço permaneça sem interrupção.

## <a name="cluster-actions"></a>Ações de cluster

- Removendo um cluster do vCenter.
- Alterar HA (alta disponibilidade) de vSphere em um cluster.
- Adicionar um host ao cluster do vCenter.
- Removendo um host do cluster do vCenter.

## <a name="host-actions"></a>Ações do host

- Removendo repositórios de armazenamento em um host ESXi.
- Desinstalando o agente do vCenter do host.
- Modificando a configuração do host.
- Fazendo alterações nos perfis de host.
- Colocando um host no modo de manutenção.

## <a name="network-actions"></a>Ações de rede

- Excluindo o comutador virtual distribuído padrão (DVS) em uma nuvem privada.
- Removendo um host do DVS padrão.
- Importando qualquer configuração de DVS.
- Reconfiguração de qualquer configuração de DVS.
- Atualizando qualquer DVS.
- Excluindo o o gerenciamento de porta.
- Editando o gerenciamento de porta.

## <a name="roles-and-permissions-actions"></a>Ações de funções e permissões

- Criando uma função global.
- Modificando ou excluindo permissão para qualquer objeto de gerenciamento.
- Modificando ou removendo qualquer função padrão.
- Aumente os privilégios de uma função para maior do que a função de proprietário da nuvem.

## <a name="other-actions"></a>Outras ações

- Removendo qualquer licença padrão:
  - vCenter Server
  - Nós ESXi
  - NSX-T
  - HCX
- Modificando ou excluindo o pool de recursos de gerenciamento.
- Clonando VMs de gerenciamento.


## <a name="next-steps"></a>Próximas etapas
[Manutenção e atualizações do CloudSimple](cloudsimple-maintenance-updates.md) 
