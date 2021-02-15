---
title: Gerenciar o controle de acesso baseado em função do Azure no Azure Site Recovery
description: Este artigo descreve como aplicar o controle de acesso baseado em função do Azure (RBAC do Azure) para gerenciar o acesso Azure Site Recovery.
ms.service: site-recovery
ms.date: 04/08/2019
author: mayurigupta13
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: d3e1334f513e8ac587d639758d83ce080c5b4ab9
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2020
ms.locfileid: "92516895"
---
# <a name="manage-site-recovery-access-with-azure-role-based-access-control-azure-rbac"></a>Gerenciar o acesso de Site Recovery com o controle de acesso baseado em função do Azure (RBAC do Azure)

O Azure RBAC (controle de acesso baseado em função) permite o gerenciamento de acesso refinado para o Azure. Usando o RBAC do Azure, você pode separar as responsabilidades dentro de sua equipe e conceder apenas permissões de acesso específicas aos usuários, conforme necessário, para executar trabalhos específicos.

O Azure Site Recovery fornece 3 funções internas para controlar as operações de gerenciamento do Site Recovery. Saiba mais sobre as [funções internas do Azure](../role-based-access-control/built-in-roles.md)

* [Colaborador do Site Recovery](../role-based-access-control/built-in-roles.md#site-recovery-contributor) -essa função tem todas as permissões necessárias para gerenciar operações do Azure Site Recovery em um cofre dos Serviços de Recuperação. No entanto, um usuário com essa função não pode criar ou excluir um cofre dos Serviços de Recuperação ou atribuir direitos de acesso a outros usuários. Essa função é ideal para os administradores de recuperação de desastre que podem habilitar e gerenciar a recuperação de desastre para aplicativos ou organizações inteiras, conforme o caso.
* [Operador do Site Recovery](../role-based-access-control/built-in-roles.md#site-recovery-operator) - Essa função tem permissões para executar e gerenciar operações de Failover e Failback. Um usuário com essa função não pode habilitar ou desabilitar a replicação, criar ou excluir cofres, registrar nova infraestrutura ou atribuir direitos de acesso a outros usuários. Essa função é ideal para um operador de recuperação de desastre que pode executar failover em máquinas virtuais ou aplicativos quando instruído por administradores de TI ou proprietários do aplicativo em uma situação de desastre real ou simulada, como em uma simulação de recuperação de desastre. Após a resolução do desastre, o operador de recuperação de desastre pode proteger e fazer failback das máquinas virtuais novamente.
* [Leitor do Site Recovery](../role-based-access-control/built-in-roles.md#site-recovery-reader): essa função tem permissões para exibir todas as operações de gerenciamento do Site Recovery. Essa função é ideal para um executivo de monitoramento de TI que possa monitorar o estado atual de proteção e gerar tíquetes de suporte, se necessário.

Se você estiver procurando definir suas próprias funções para obter ainda mais controle, consulte como [criar funções personalizadas](../role-based-access-control/custom-roles.md) no Azure.

## <a name="permissions-required-to-enable-replication-for-new-virtual-machines"></a>Permissões necessárias para habilitar a replicação para novas máquinas virtuais
Quando uma nova máquina virtual é replicada para o Azure usando o Azure Site Recovery, os níveis de acesso do usuário associados são validados para garantir que o usuário tem as permissões necessárias para usar os recursos do Azure fornecidos para o Site Recovery.

Para habilitar a replicação para uma nova máquina virtual, o usuário deve ter:
* Permissão para criar uma máquina virtual no grupo de recursos selecionado
* Permissão para criar uma máquina virtual na rede virtual selecionada
* Permissão de gravação para a conta de armazenamento selecionada

Um usuário precisa das seguintes permissões para concluir a replicação de uma nova máquina virtual.

> [!IMPORTANT]
>Certifique-se de que as permissões relevantes sejam adicionadas pelo modelo de implantação (Resource Manager/Clássico) usado para implantação de recursos.

> [!NOTE]
> Se você estiver habilitando a replicação para uma VM do Azure e quiser permitir que Site Recovery gerencie atualizações, ao mesmo tempo em que permite a replicação, você também pode querer criar uma nova conta de automação, caso em que você precisará de permissão para criar uma conta de automação na mesma assinatura que o cofre também.

| **Tipo de recurso** | **Modelo de Implantação** | **Permissão** |
| --- | --- | --- |
| Computação | Gerenciador de Recursos | Microsoft.Compute/availabilitySets/read |
|  |  | Microsoft.Compute/virtualMachines/read |
|  |  | Microsoft.Compute/virtualMachines/write |
|  |  | Microsoft.Compute/virtualMachines/delete |
|  | Clássico | Microsoft.ClassicCompute/domainNames/read |
|  |  | Microsoft.ClassicCompute/domainNames/write |
|  |  | Microsoft.ClassicCompute/domainNames/delete |
|  |  | Microsoft.ClassicCompute/virtualMachines/read |
|  |  | Microsoft.ClassicCompute/virtualMachines/write |
|  |  | Microsoft.ClassicCompute/virtualMachines/delete |
| Rede | Gerenciador de Recursos | Microsoft.Network/networkInterfaces/read |
|  |  | Microsoft.Network/networkInterfaces/write |
|  |  | Microsoft.Network/networkInterfaces/delete |
|  |  | Microsoft.Network/networkInterfaces/join/action |
|  |  | Microsoft.Network/virtualNetworks/read |
|  |  | Microsoft.Network/virtualNetworks/subnets/read |
|  |  | Microsoft.Network/virtualNetworks/subnets/join/action |
|  | Clássico | Microsoft.ClassicNetwork/virtualNetworks/read |
|  |  | Microsoft.ClassicNetwork/virtualNetworks/join/action |
| Armazenamento | Gerenciador de Recursos | Microsoft.Storage/storageAccounts/read |
|  |  | Microsoft.Storage/storageAccounts/listkeys/action |
|  | Clássico | Microsoft.ClassicStorage/storageAccounts/read |
|  |  | Microsoft.ClassicStorage/storageAccounts/listKeys/action |
| Grupo de recursos | Gerenciador de Recursos | Microsoft.Resources/deployments/* |
|  |  | Microsoft.Resources/subscriptions/resourceGroups/read |

Considere usar as [funções internas](../role-based-access-control/built-in-roles.md) 'Colaborador de Máquina Virtual' e ‘Colaborador de Máquina Virtual Clássica’ para os modelos de implantação do Resource Manager e do Clássico respectivamente.

## <a name="next-steps"></a>Próximas etapas
* [Controle de acesso baseado em função do Azure (RBAC do Azure)](../role-based-access-control/role-assignments-portal.md): introdução ao RBAC do azure no portal do Azure.
* Saiba como gerenciar o acesso com:
  * [PowerShell](../role-based-access-control/role-assignments-powershell.md)
  * [CLI do Azure](../role-based-access-control/role-assignments-cli.md)
  * [REST API](../role-based-access-control/role-assignments-rest.md)
* [Solução de problemas do RBAC do Azure](../role-based-access-control/troubleshooting.md): Obtenha sugestões para corrigir problemas comuns.
