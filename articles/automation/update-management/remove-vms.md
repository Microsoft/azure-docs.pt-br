---
title: Remover VMs do Gerenciamento de Atualizações da Automação do Azure
description: Este artigo informa como remover computadores gerenciados com o Gerenciamento de Atualizações.
services: automation
ms.topic: conceptual
ms.date: 01/05/2021
ms.custom: mvc
ms.openlocfilehash: d0399aed9be8d81abb2aa55190225570ddcc1a4e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97913185"
---
# <a name="remove-vms-from-update-management"></a>Remover VMs do Gerenciamento de Atualizações

Ao concluir o gerenciamento de atualizações em suas VMs em seu ambiente, você pode parar de gerenciar VMs com o recurso [Gerenciamento de atualizações](overview.md) . Para parar de gerenciá-los, você editará a consulta de pesquisa salva `MicrosoftDefaultComputerGroup` em seu espaço de trabalho do log Analytics que está vinculado à sua conta de automação.

## <a name="sign-into-the-azure-portal"></a>Entrar no portal do Azure

Entre no [portal do Azure](https://portal.azure.com).

## <a name="to-remove-your-vms"></a>Para remover suas VMs

1. Na portal do Azure, inicie o **Cloud Shell** na navegação superior da portal do Azure. Se você não estiver familiarizado com Azure Cloud Shell, consulte [visão geral do Azure cloud Shell](../../cloud-shell/overview.md).

2. Use o comando a seguir para identificar o UUID de um computador que você deseja remover do gerenciamento do.

    ```azurecli
    az vm show -g MyResourceGroup -n MyVm -d
    ```

3. Na portal do Azure, navegue até **espaços de trabalho do log Analytics**. Selecione o workspace da lista.

4. No espaço de trabalho Log Analytics, selecione **Configurações avançadas** e, em seguida, escolha **grupos de computadores** no menu à esquerda.

5. Em **grupos de computadores** no painel direito, selecione **grupos salvos**.

6. Na tabela, para as atualizações de consulta de pesquisa salvas **: MicrosoftDefaultComputerGroup**, clique no ícone **Exibir Membros** para executar e exibir seus membros.

7. No editor de consultas, examine a consulta e localize o UUID para a VM. Remova o UUID para a VM e repita as etapas para todas as outras VMs que você deseja remover.

8. Salve a pesquisa salva quando tiver terminado de editá-la selecionando **salvar** na barra superior. Quando solicitado, especifique o seguinte:

    * **Nome**: MicrosoftDefaultComputerGroup
    * **Salvar como**: função
    * **Alias**: Updates__MicrosoftDefaultComputerGroup
    * **Categoria**: atualizações

>[!NOTE]
>Os computadores ainda serão mostrados depois que você tiver cancelado o registro porque relatamos em todos os computadores avaliados nas últimas 24 horas. Depois de remover a máquina, você precisa aguardar 24 horas antes que elas não sejam mais listadas.

## <a name="next-steps"></a>Próximas etapas

Para reabilitar o gerenciamento de sua máquina virtual, consulte [habilitar gerenciamento de atualizações navegando na portal do Azure](enable-from-portal.md) ou [habilitando gerenciamento de atualizações de uma VM do Azure](enable-from-vm.md).