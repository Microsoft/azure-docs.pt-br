---
title: Excluir nós da solução VMware por CloudSimple-Azure
description: Saiba como excluir nós de seu VMWare com a implantação do CloudSimple. Os nós CloudSimple são medidos. Exclua os nós que não são usados no portal do Azure.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/05/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 569bc6350b1bfa01228d49d28a1d12e2ab62f6f0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88142257"
---
# <a name="delete-nodes-from-azure-vmware-solution-by-cloudsimple"></a>Excluir nós da solução do Azure VMware por CloudSimple

Os nós CloudSimple são medidos assim que são criados.  Os nós devem ser excluídos para interromper a medição dos nós.  Você exclui os nós que não são usados no portal do Azure.

## <a name="before-you-begin"></a>Antes de começar

Um nó só pode ser excluído sob as seguintes condições:

* Uma nuvem privada criada com os nós é excluída.  Para excluir uma nuvem privada, consulte [excluir uma solução do Azure VMware por nuvem privada CloudSimple](delete-private-cloud.md).
* O nó foi removido da nuvem privada por meio da redução da nuvem privada.  Para reduzir uma nuvem privada, consulte [reduzir a solução do Azure VMware pela nuvem privada CloudSimple](shrink-private-cloud.md).

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="delete-cloudsimple-node"></a>Excluir nó CloudSimple

1. Selecione **Todos os serviços**.

2. Procure **nós CloudSimple**.

   ![Pesquisar nós do CloudSimple](media/create-cloudsimple-node-search.png)

3. Selecione **nós CloudSimple**.

4. Selecione os nós que não pertencem a uma nuvem privada a ser excluída.  A coluna **nome da nuvem particular** mostra o nome da nuvem privada ao qual um nó pertence.  Se um nó não for usado por uma nuvem privada, o valor estará vazio. 

    ![Selecionar nós CloudSimple](media/select-delete-cloudsimple-node.png)

> [!NOTE]
> Somente os nós que não fazem parte da nuvem privada podem ser excluídos.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre a [nuvem privada](cloudsimple-private-cloud.md)
