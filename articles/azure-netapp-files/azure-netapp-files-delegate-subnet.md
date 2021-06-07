---
title: Delegar uma sub-rede para Azure NetApp Files | Microsoft Docs
description: Saiba como delegar uma sub-rede para Azure NetApp Files. Especifique a sub-rede delegada ao criar um volume.
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
ms.topic: how-to
ms.date: 09/28/2020
ms.author: b-juche
ms.openlocfilehash: bb3d1fd49c2623ff6dcbe8a19ae8c8ca3b46425a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96006569"
---
# <a name="delegate-a-subnet-to-azure-netapp-files"></a>Delegar uma sub-rede ao Azure NetApp Files 

É necessário delegar uma sub-rede para Azure NetApp Files.   Quando você cria um volume, é necessário especificar a sub-rede delegada.

## <a name="considerations"></a>Considerações

* O assistente para criar uma nova sub-rede usa como padrão uma máscara de rede /24, que possibilita 251 endereços IP disponíveis. Usar uma máscara de rede/28, que fornece por 11 endereços IP utilizáveis, é suficiente para o serviço.
* Em cada VNet (Rede virtual do Azure), apenas uma sub-rede pode ser delegada para o Azure NetApp Files.   
   O Azure permite que você crie várias sub-redes delegadas em uma VNet.  No entanto, qualquer tentativa de criar um novo volume falhará se você usar mais de uma sub-rede delegada.  
   Você pode ter apenas uma única sub-rede delegada em uma VNet. Uma conta do NetApp pode implantar volumes em vários VNets, cada um com sua própria sub-rede delegada.  
* Não é possível designar um grupo de segurança de rede nem um ponto de extremidade de serviço na sub-rede delegada. Fazer isso causa a falha da delegação da sub-rede.
* Atualmente, não há suporte para o acesso a um volume de uma rede virtual emparelhada globalmente.
* Não há suporte para UDRs ( [rotas definidas pelo usuário](../virtual-network/virtual-networks-udr-overview.md#custom-routes) ) e NSGs (grupos de segurança de rede) em sub-redes delegadas para Azure NetApp files. No entanto, você pode aplicar UDRs e NSGs a outras sub-redes, mesmo na mesma VNet que a sub-rede delegada para Azure NetApp Files.  
   Azure NetApp Files cria uma rota do sistema para a sub-rede delegada. A rota será mostrada em **rotas efetivas** na tabela de rotas se você precisar dela para solução de problemas.

## <a name="steps"></a>Etapas

1.  Vá para a folha **redes virtuais** no portal do Azure e selecione a rede virtual que você deseja usar para Azure NetApp files.    

1. Selecione **Sub-redes** na folha da Rede virtual e clique no botão **+ Sub-rede**. 

1. Crie uma nova sub-rede a ser usada para o Azure NetApp Files preenchendo os campos obrigatórios a seguir na página Adicionar sub-rede:
    * **Nome**: especifique o nome da sub-rede.
    * **Intervalo de endereços**: especifique o intervalo de endereços IP.
    * **Delegação de sub-rede**: selecione **Microsoft. NetApp/volumes**. 

      ![Delegação de sub-rede](../media/azure-netapp-files/azure-netapp-files-subnet-delegation.png)
    
Também é possível criar e delegar uma sub-rede quando você [cria um volume do Azure NetApp Files](azure-netapp-files-create-volumes.md). 

## <a name="next-steps"></a>Próximas etapas

* [Criar um volume para Azure NetApp Files](azure-netapp-files-create-volumes.md)
* [Saiba mais sobre a integração de rede virtual para os serviços do Azure](../virtual-network/virtual-network-for-azure-services.md)