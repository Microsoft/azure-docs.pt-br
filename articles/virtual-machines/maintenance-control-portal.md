---
title: Controle de manutenção para máquinas virtuais do Azure usando o portal do Azure
description: Saiba como controlar quando a manutenção é aplicada às suas VMs do Azure usando o controle de manutenção e a portal do Azure.
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 04/22/2020
ms.author: cynthn
ms.openlocfilehash: b174e2631131e6bf26d7b1cb62442c8a99102e2e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91397276"
---
# <a name="control-updates-with-maintenance-control-and-the-azure-portal"></a>Controlar atualizações com o controle de manutenção e o portal do Azure

O controle de manutenção permite que você decida quando aplicar atualizações às VMs isoladas e aos hosts dedicados do Azure. Este tópico aborda as opções de portal do Azure para o controle de manutenção. Para obter mais informações sobre os benefícios de usar o controle de manutenção, suas limitações e outras opções de gerenciamento, consulte [gerenciando atualizações de plataforma com o controle de manutenção](maintenance-control.md).

## <a name="create-a-maintenance-configuration"></a>Criar uma configuração de manutenção

1. Entre no portal do Azure.

1. Procure **configurações de manutenção**.

   ![Captura de tela mostrando como abrir configurações de manutenção](media/virtual-machines-maintenance-control-portal/maintenance-configurations-search.png)

1. Clique em **Adicionar**.

   ![Captura de tela mostrando como adicionar uma configuração de manutenção](media/virtual-machines-maintenance-control-portal/maintenance-configurations-add.png)

1. Escolha uma assinatura e um grupo de recursos, forneça um nome para a configuração e escolha uma região. Clique em **Avançar**.

   ![Captura de tela mostrando noções básicas de configuração de manutenção](media/virtual-machines-maintenance-control-portal/maintenance-configurations-basics.png)

1. Adicione marcas e valores. Clique em **Avançar**.

   ![Captura de tela mostrando como adicionar marcas a uma configuração de manutenção](media/virtual-machines-maintenance-control-portal/maintenance-configurations-tags.png)

1. Examine o resumo. Clique em **Criar**.

   ![Captura de tela mostrando como criar uma configuração de manutenção](media/virtual-machines-maintenance-control-portal/maintenance-configurations-create.png)

1. Depois que a implantação for concluída, clique em **ir para o recurso**.

   ![Captura de tela mostrando a implantação da configuração de manutenção concluída](media/virtual-machines-maintenance-control-portal/maintenance-configurations-deployment-complete.png)

## <a name="assign-the-configuration"></a>Atribuir a configuração

Na página de detalhes da configuração de manutenção, clique em atribuições e, em seguida, clique em **atribuir recurso**. 

![Captura de tela mostrando como atribuir um recurso](media/virtual-machines-maintenance-control-portal/maintenance-configurations-add-assignment.png)

Selecione os recursos aos quais você deseja que a configuração de manutenção seja atribuída e clique em **OK**. A coluna **tipo** mostra se o recurso é uma VM isolada ou um host dedicado do Azure. A VM precisa estar em execução para atribuir a configuração. Ocorrerá um erro se você tentar atribuir uma configuração a uma VM que é interrompida. 

<!---Shantanu to add details about the error case--->

![Captura de tela mostrando como selecionar um recurso](media/virtual-machines-maintenance-control-portal/maintenance-configurations-select-resource.png)

## <a name="check-configuration"></a>Verificar configuração

Você pode verificar se a configuração foi aplicada corretamente ou verificar se há alguma configuração de manutenção atribuída no momento usando **as configurações de manutenção**. A coluna **tipo** mostra se a configuração é atribuída a uma VM isolada ou ao host dedicado do Azure. 

![Captura de tela mostrando como verificar uma configuração de manutenção](media/virtual-machines-maintenance-control-portal/maintenance-configurations-host-type.png)

Você também pode verificar a configuração de uma máquina virtual específica em sua página de propriedades. Clique em **manutenção** para ver a configuração atribuída a essa máquina virtual.

![Captura de tela mostrando como verificar a manutenção de um host](media/virtual-machines-maintenance-control-portal/maintenance-configurations-check-config.png)

## <a name="check-for-pending-updates"></a>Verificar se há atualizações pendentes

Também há duas maneiras de verificar se há atualizações pendentes para uma configuração de manutenção. Em **configurações de manutenção**, nos detalhes da configuração, clique em **atribuições** e verifique o **status da manutenção**.

![Captura de tela mostrando como verificar atualizações pendentes](media/virtual-machines-maintenance-control-portal/maintenance-configurations-pending.png)

Você também pode verificar um host específico usando **máquinas virtuais** ou propriedades do host dedicado. 

![Captura de tela que mostra o estado de manutenção realçado.](media/virtual-machines-maintenance-control-portal/maintenance-configurations-pending-vm.png)

## <a name="apply-updates"></a>Aplicar atualizações

Você pode aplicar atualizações pendentes sob demanda usando **máquinas virtuais**. Nos detalhes da VM, clique em **manutenção** e clique em **aplicar manutenção agora**.

![Captura de tela mostrando como aplicar atualizações pendentes](media/virtual-machines-maintenance-control-portal/maintenance-configurations-apply-updates-now.png)

## <a name="check-the-status-of-applying-updates"></a>Verificar o status da aplicação de atualizações 

Você pode verificar o progresso das atualizações para uma configuração em configurações de **manutenção** ou usando **máquinas virtuais**. Nos detalhes da VM, clique em **manutenção**. No exemplo a seguir, o **estado de manutenção** mostra que uma atualização está **pendente**.

![Captura de tela mostrando como verificar o status das atualizações pendentes](media/virtual-machines-maintenance-control-portal/maintenance-configurations-status.png)

## <a name="delete-a-maintenance-configuration"></a>Excluir uma configuração de manutenção

Para excluir uma configuração, abra os detalhes de configuração e clique em **excluir**.

![Captura de tela que mostra como excluir uma configuração.](media/virtual-machines-maintenance-control-portal/maintenance-configurations-delete.png)


## <a name="next-steps"></a>Próximas etapas

Para saber mais, consulte [manutenção e atualizações](maintenance-and-updates.md).
