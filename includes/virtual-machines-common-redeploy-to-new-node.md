---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 1c3996c3f40da496af0cd795d0873864667a1f19
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67171927"
---
## <a name="use-the-azure-portal"></a>Use o Portal do Azure
1. Selecione a VM que você deseja reimplantar e clique no botão *Reimplantar* na folha *Configurações*. Talvez seja necessário rolar para baixo a fim de ver a seção **Suporte e solução de problemas** que contém o botão 'Reimplantar' como no exemplo abaixo:
   
    ![Folha VM do Azure](./media/virtual-machines-common-redeploy-to-new-node/vmoverview.png)
2. Para confirmar a operação, clique no botão *Reimplantar*:
   
    ![Folha Reimplantar uma VM](./media/virtual-machines-common-redeploy-to-new-node/redeployvm.png)
3. O **Status** da VM muda para *Atualizando* à medida que a VM é preparada para a reimplantação, como mostrado no exemplo abaixo:
   
    ![VM atualizando](./media/virtual-machines-common-redeploy-to-new-node/vmupdating.png)
4. O **Status** mudará para *Iniciando* enquanto a VM é inicializada em um novo host do Azure, como mostrado no exemplo abaixo:
   
    ![VM iniciando](./media/virtual-machines-common-redeploy-to-new-node/vmstarting.png)
5. Após a conclusão do processo de inicialização da VM, o **Status** retornará a *Em execução*, indicando que a VM foi reimplantada com êxito:
   
    ![VM em execução](./media/virtual-machines-common-redeploy-to-new-node/vmrunning.png)

