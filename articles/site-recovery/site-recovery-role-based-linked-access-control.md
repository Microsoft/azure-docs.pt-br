---
title: "Usando o Controle de Acesso Baseado em Função para gerenciar o Azure Site Recovery | Microsoft Docs"
description: "Este artigo descreve como aplicar e usar o RBAC (Controle de Acesso Baseado em Função) para gerenciar suas implantações do Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: mayanknayar
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2017
ms.author: manayar
ms.openlocfilehash: 9dd74014bf05234a83c7678b67b42b96cd8b8d64
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-role-based-access-control-to-manage-azure-site-recovery-deployments"></a>Usar o Controle de Acesso Baseado em Função para gerenciar implantações do Azure Site Recovery

O RBAC (controle de acesso baseado em função) do Azure permite o gerenciamento de acesso refinado para o Azure. Usando o RBAC, você pode separar as responsabilidades dentro de sua equipe e conceder somente permissões de acesso específicas aos usuários conforme necessário para executar trabalhos específicos.

O Azure Site Recovery fornece 3 funções internas para controlar as operações de gerenciamento do Site Recovery. Saiba mais sobre as [funções internas do RBAC do Azure](../active-directory/role-based-access-built-in-roles.md)

* [Colaborador do Site Recovery](../active-directory/role-based-access-built-in-roles.md#site-recovery-contributor) -essa função tem todas as permissões necessárias para gerenciar operações do Azure Site Recovery em um cofre dos Serviços de Recuperação. No entanto, um usuário com essa função não pode criar ou excluir um cofre dos Serviços de Recuperação ou atribuir direitos de acesso a outros usuários. Essa função é ideal para os administradores de recuperação de desastre que podem habilitar e gerenciar a recuperação de desastre para aplicativos ou organizações inteiras, conforme o caso.
* [Operador do Site Recovery](../active-directory/role-based-access-built-in-roles.md#site-recovery-operator) - essa função tem permissões para executar e gerenciar operações de Failover e Failback. Um usuário com essa função não pode habilitar ou desabilitar a replicação, criar ou excluir cofres, registrar nova infraestrutura ou atribuir direitos de acesso a outros usuários. Essa função é ideal para um operador de recuperação de desastre que pode executar failover em máquinas virtuais ou aplicativos quando instruído por administradores de TI ou proprietários do aplicativo em uma situação de desastre real ou simulada, como em uma simulação de recuperação de desastre. Após a resolução do desastre, o operador de recuperação de desastre pode proteger e fazer failback das máquinas virtuais novamente.
* [Leitor do Site Recovery](../active-directory/role-based-access-built-in-roles.md#site-recovery-reader): essa função tem permissões para exibir todas as operações de gerenciamento do Site Recovery. Essa função é ideal para um executivo de monitoramento de TI que possa monitorar o estado atual de proteção e gerar tíquetes de suporte, se necessário.

Se você pretende definir suas próprias funções para ter ainda mais controle, confira como [criar Funções personalizadas](../active-directory/role-based-access-control-custom-roles.md) no Azure.

## <a name="permissions-required-to-enable-replication-for-new-virtual-machines"></a>Permissões necessárias para habilitar a replicação para novas máquinas virtuais
Quando uma nova máquina virtual é replicada para o Azure usando o Azure Site Recovery, os níveis de acesso do usuário associados são validados para garantir que o usuário tem as permissões necessárias para usar os recursos do Azure fornecidos para o Site Recovery.

Para habilitar a replicação para uma nova máquina virtual, o usuário deve ter:
* Permissão para criar uma máquina virtual no grupo de recursos selecionado
* Permissão para criar uma máquina virtual na rede virtual selecionada
* Permissão de gravação para a conta de armazenamento selecionada

Um usuário precisa das seguintes permissões para concluir a replicação de uma nova máquina virtual.

> [!IMPORTANT]
>Certifique-se de que as permissões relevantes sejam adicionadas pelo modelo de implantação (Resource Manager/Clássico) usado para implantação de recursos.

| **Tipo de recurso** | **Modelo de implantação** | **Permissão** |
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

Considere usar as [funções internas](../active-directory/role-based-access-built-in-roles.md) 'Colaborador de Máquina Virtual' e ‘Colaborador de Máquina Virtual Clássica’ para os modelos de implantação do Resource Manager e do Clássico respectivamente.

## <a name="next-steps"></a>Próximas etapas
* [Controle de Acesso Baseado em Função](../active-directory/role-based-access-control-configure.md): introdução ao RBAC no portal do Azure.
* Saiba como gerenciar o acesso com:
  * [PowerShell](../active-directory/role-based-access-control-manage-access-powershell.md)
  * [CLI do Azure](../active-directory/role-based-access-control-manage-access-azure-cli.md)
  * [API REST](../active-directory/role-based-access-control-manage-access-rest.md)
* [Solução de problemas de Controle de Acesso Baseado em Função](../active-directory/role-based-access-control-troubleshooting.md): obtenha sugestões para corrigir problemas comuns.
