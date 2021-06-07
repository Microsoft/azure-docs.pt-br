---
title: Criar pools de recursos com o mapeamento de assinatura do Azure
titleSuffix: Azure VMware Solution by CloudSimple
description: Descreve como criar pools de recursos para sua nuvem privada por meio do mapeamento de assinatura do Azure
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 7be4a4c601d3f33972c1e52596ef623116dcadd4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97897069"
---
# <a name="create-resource-pools-for-your-private-cloud-with-azure-subscription-mapping"></a>Criar pools de recursos para sua nuvem privada com o mapeamento de assinatura do Azure
O mapeamento de assinatura do Azure permite que você crie pools de recursos para sua nuvem privada dos pools de recursos do vSphere disponíveis. No portal do CloudSimple, você pode exibir e gerenciar a assinatura do Azure para suas nuvens privadas.

> [!NOTE]
> O mapeamento de um pool de recursos também mapeia os pools de recursos filho. Um pool de recursos pai não poderá ser mapeado se algum pool de recursos filho já estiver mapeado.

1. [Acesse o portal do CloudSimple](access-cloudsimple-portal.md).
2. Abra a página **recursos** e selecione **mapeamento de assinaturas do Azure**.  
3. Clique em **Editar mapeamento de assinatura do Azure**.  
4. Para mapear os pools de recursos disponíveis, selecione-os à esquerda e clique na seta para a direita. 
5. Para remover mapeamentos, selecione-os à direita e clique na seta para a esquerda. 

    ![Assinaturas do Azure](media/resources-azure-mapping.png)

6. Clique em **OK**.
