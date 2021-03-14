---
title: Conceitos-identidade e acesso
description: Saiba mais sobre os conceitos de identidade e acesso da solução do Azure VMware
ms.topic: conceptual
ms.date: 02/02/2021
ms.openlocfilehash: 68f4ce9136cca1cf9bf0824395e31704d8ed1a17
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100364878"
---
# <a name="azure-vmware-solution-identity-concepts"></a>Conceitos de identidade da solução VMware do Azure

As nuvens privadas da solução Azure VMware são provisionadas com um servidor vCenter e com o Gerenciador de NSX-T. Use o vCenter para gerenciar cargas de trabalho de máquina virtual (VM). Você usa o Gerenciador de NSX-T para estender a nuvem privada.

Gerenciamento de acesso e identidade use privilégios de grupo CloudAdmin para o vCenter e direitos de administrador restritos para o Gerenciador de NSX-T. Ele garante que sua plataforma de nuvem privada seja automaticamente atualizada com os recursos e patches mais recentes.  Para obter mais informações, consulte o [artigo conceitos de atualizações de nuvem privada][concepts-upgrades].

## <a name="vcenter-access-and-identity"></a>acesso e identidade do vCenter

O grupo CloudAdmin fornece os privilégios no vCenter. Você gerencia o grupo localmente no vCenter. Outra opção é por meio da integração do logon único LDAP do vCenter com o Azure Active Directory. Você habilita essa integração depois de implantar sua nuvem privada. 

A tabela mostra os privilégios **CloudAdmin** e **CloudGlobalAdmin** .

|  Conjunto de privilégios           | CloudAdmin | CloudGlobalAdmin | Comentário |
| :---                     |    :---:   |       :---:      |   :--:  |
|  Alarmes                  | Um usuário CloudAdmin tem todos os privilégios de alarmes no Compute-ResourcePool e nas VMs.     |          --        |  -- |
|  Implantação automática             |  --  |        --        |  A Microsoft faz o gerenciamento de hosts.  |
|  Certificados            |  --  |        --       |  A Microsoft faz o gerenciamento de certificados.  |
|  Biblioteca de conteúdo         | Um usuário CloudAdmin tem privilégios para criar e usar arquivos em uma biblioteca de conteúdo.    |         Habilitado com SSO.         |  A Microsoft distribuirá arquivos na biblioteca de conteúdo para hosts ESXi.  |
|  Datacenter              |  --  |        --          |  A Microsoft faz todas as operações de data center.  |
|  Repositório de dados               | Datastore. AllocateSpace, datastore. procurar, Datastore.Config, datastore. Excluirfile, datastore. filemanagement, datastore. UpdateVirtualMachineMetadata     |    --    |   -- |
|  Gerenciador de Agentes ESX       |  --  |         --       |  A Microsoft faz todas as operações.  |
|  Pasta                  |  Um usuário CloudAdmin tem todos os privilégios de pasta.     |  --  |  --  |
|  Global                  |  Global. CancelTask, global. GlobalTag, global. Health, global. LogEvent, global. ManageCustomFields, global. promanagers, global. autocustomfield, Global.SystemTag         |                  |    |
|  Host                    |  Host. HBR. HbrManagement      |        --          |  A Microsoft faz todas as outras operações de host.  |
|  InventoryService        |  InventoryService. marcação      |        --          |  --  |
|  Rede                 |  Network.Assign    |                  |  A Microsoft faz todas as outras operações de rede.  |
|  Permissões             |  --  |        --       |  A Microsoft faz todas as operações de permissões.  |
|  Armazenamento controlado por perfil  |  --  |        --       |  A Microsoft faz todas as operações de perfil.  |
|  Recurso                |  Um usuário CloudAdmin tem todos os privilégios de recurso.        |      --       | --   |
|  Tarefa agendada          |  Um usuário CloudAdmin tem todos os privilégios de ScheduleTask.   |   --   | -- |
|  Sessões                |  Sessions. GlobalMessage, Sessions. ValidateSession      |   --   |  A Microsoft realiza todas as outras operações de sessão.  |
|  Exibições de armazenamento           |  StorageViews. exibição   |        --          |  A Microsoft faz todas as outras operações de exibição de armazenamento (configure service).  |
|  Tarefas                   |  --  |  --   |  A Microsoft gerencia extensões que gerenciam tarefas.  |
|  vApp                    |  Um usuário CloudAdmin tem todos os privilégios de vApp.  |  --  |  --  |
|  Máquina Virtual         |  Um usuário CloudAdmin tem todos os privilégios de VirtualMachine.  |  --  |  --  |
|  vService                |  Um usuário CloudAdmin tem todos os privilégios de vService.  |  --  |  --  |

## <a name="nsx-t-manager-access-and-identity"></a>Acesso e identidade do NSX-T Manager

Use a conta de *administrador* para acessar o Gerenciador de NSX-T. Ele tem privilégios totais e permite criar e gerenciar gateways de camada 1 (T1), segmentos (comutadores lógicos) e todos os serviços. Os privilégios fornecem acesso ao gateway do NSX-T Tier-0 (T0). Uma alteração no gateway de T0 pode resultar em desempenho de rede degradado ou sem acesso à nuvem privada. Abra uma solicitação de suporte no portal do Azure para solicitar qualquer alteração em seu gateway do NSX-T T0.
  
## <a name="next-steps"></a>Próximas etapas

Agora que você abordou os conceitos de acesso e identidade da solução Azure VMware, talvez queira saber mais sobre:

- [Conceitos de atualização da nuvem privada](concepts-upgrades.md).
- [controle de acesso baseado em função do vSphere para a solução do Azure VMware](concepts-role-based-access-control.md).
- [Como habilitar o recurso de solução VMware do Azure](enable-azure-vmware-solution.md).

<!-- LINKS - external -->

<!-- LINKS - internal -->
[concepts-upgrades]: ./concepts-upgrades.md