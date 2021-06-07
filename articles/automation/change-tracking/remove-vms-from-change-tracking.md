---
title: Remoção de VMs dos recursos Controle de Alterações e Inventário da Automação do Azure
description: Este artigo descreve como remover as VMs dos Controle de Alterações e Inventário.
services: automation
ms.subservice: change-inventory-management
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: 0b79fa22d3203504e63161aba03b32830d74d016
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93131268"
---
# <a name="remove-vms-from-change-tracking-and-inventory"></a>Remover VMs do Controle de Alterações e do Inventário

Quando terminar de controlar as alterações nas VMs em seu ambiente, você poderá parar de gerenciá-las com o recurso de [controle de alterações e inventário](overview.md) . Para parar de gerenciá-los, você editará a consulta de pesquisa salva `MicrosoftDefaultComputerGroup` em seu espaço de trabalho do log Analytics que está vinculado à sua conta de automação.

## <a name="sign-into-the-azure-portal"></a>Entrar no portal do Azure

Entre no [portal do Azure](https://portal.azure.com).

## <a name="to-remove-your-vms"></a>Para remover suas VMs

1. Na portal do Azure, inicie o **Cloud Shell** na navegação superior da portal do Azure. Se você não estiver familiarizado com Azure Cloud Shell, consulte [visão geral do Azure cloud Shell](../../cloud-shell/overview.md).

2. Use o comando a seguir para identificar o UUID de um computador que você deseja remover do gerenciamento do.

    ```azurecli
    az vm show -g MyResourceGroup -n MyVm -d
    ```

3. Na portal do Azure, navegue até **espaços de trabalho do log Analytics**. Selecione o workspace da lista.

4. No espaço de trabalho Log Analytics, selecione **logs** e, em seguida, escolha **Gerenciador de consultas** no menu de ações superior.

5. No **Gerenciador de consultas** no painel direito, expanda **Queries\Updates salvas** e selecione a consulta de pesquisa salva `MicrosoftDefaultComputerGroup` para editá-lo.

6. No editor de consultas, examine a consulta e localize o UUID para a VM. Remova o UUID para a VM e repita as etapas para todas as outras VMs que você deseja remover.

7. Salve a pesquisa salva quando tiver terminado de editá-la selecionando **salvar** na barra superior.

>[!NOTE]
>Os computadores ainda serão mostrados depois que você tiver cancelado o registro porque relatamos em todos os computadores avaliados nas últimas 24 horas. Depois de remover a máquina, você precisa aguardar 24 horas antes que elas não sejam mais listadas.

## <a name="next-steps"></a>Próximas etapas

Para reabilitar esse recurso, consulte [habilitar o controle de alterações e o inventário de uma conta de automação](enable-from-automation-account.md), [habilitar controle de alterações e inventário navegando na portal do Azure](enable-from-portal.md), [habilitar controle de alterações e inventário de um runbook](enable-from-runbook.md)ou [habilitar o controle de alterações e o inventário de uma VM do Azure](enable-from-vm.md).