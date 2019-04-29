---
title: Configurar um pool de capacidade para Azure NetApp Files | Microsoft Docs
description: Descreve como configurar um pool de capacidade para que você possa criar volumes nele.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/15/2019
ms.author: b-juche
ms.openlocfilehash: 8f50b2ad34c705c8d3831d8243f136c41d750dc0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60691065"
---
# <a name="set-up-a-capacity-pool"></a>Configurar um pool de capacidade

Como configurar um pool de capacidade permite que você crie volumes dentro dele.  

## <a name="before-you-begin"></a>Antes de começar 

Você já deve ter criado uma conta do NetApp.   

[Criar uma conta do NetApp](azure-netapp-files-create-netapp-account.md)

## <a name="steps"></a>Etapas 

1. Acesse a folha de gerenciamento de sua conta do NetApp e, em seguida, no painel de navegação, clique em **Pools de capacidade**.  
    
    ![Navegar para o pool de capacidade](../media/azure-netapp-files/azure-netapp-files-navigate-to-capacity-pool.png)

2. Clique em **+ Adicionar pools** para criar um novo pool de capacidade.   
    A janela Novo Pool de Capacidade é exibida.

3. Forneça as seguintes informações para o novo pool de capacidade:  
   * **Nome**  
     Especifique o nome para o pool de capacidade.  
     O nome do pool de capacidade deve ser exclusivo para cada conta do NetApp.

   * **Nível de serviço**   
     Esse campo mostra o desempenho de destino para o pool de capacidade.  
     Especifique o nível de serviço para o pool de capacidade: [**Premium**](azure-netapp-files-service-levels.md#Premium) ou [**Standard**](azure-netapp-files-service-levels.md#Standard).

   * **Tamanho**     
     Especifique o tamanho do pool de capacidade que você está adquirindo.        
     O tamanho do pool de capacidade mínima é de 4 TiB. Você pode criar um pool com um tamanho de múltiplos de 4 TiB.   
      
     ![Novo pool de capacidade](../media/azure-netapp-files/azure-netapp-files-new-capacity-pool.png)

4. Clique em **OK**.

## <a name="next-steps"></a>Próximas etapas 

- [Níveis de serviço do Azure NetApp Files](azure-netapp-files-service-levels.md)
- Confira a [página de preços do Azure NetApp Files](https://azure.microsoft.com/pricing/details/storage/netapp/) para obter o preço de diferentes níveis de serviço
- [Delegar uma sub-rede ao Azure NetApp Files](azure-netapp-files-delegate-subnet.md)
