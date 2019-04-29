---
title: Delegar uma sub-rede para Azure NetApp Files | Microsoft Docs
description: Descreve como delegar uma sub-rede para o Azure NetApp Files.
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
ms.date: 03/25/2019
ms.author: b-juche
ms.openlocfilehash: fd8e380ad68b86b9ffd0f1e40efde8bdadfb19c5
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63763300"
---
# <a name="delegate-a-subnet-to-azure-netapp-files"></a>Delegar uma sub-rede ao Azure NetApp Files 

É necessário delegar uma sub-rede para Azure NetApp Files.   Quando você cria um volume, é necessário especificar a sub-rede delegada.

## <a name="considerations"></a>Considerações
* O assistente para criar uma nova sub-rede usa como padrão uma máscara de rede /24, que possibilita 251 endereços IP disponíveis. Usar uma máscara de rede /28, que possibilita 16 endereços IP utilizáveis, é suficiente para o serviço.
* Em cada Vnet (Rede virtual) do Azure, apenas uma sub-rede pode ser delegada para o Azure NetApp Files.
* Não é possível designar um grupo de segurança de rede nem um ponto de extremidade de serviço na sub-rede delegada. Fazer isso causa a falha da delegação da sub-rede.
* Atualmente, não há suporte para acesso a um volume de uma rede virtual emparelhada globalmente.
* Criando [rotas personalizadas definidas pelo usuário](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#custom-routes) em sub-redes VM com endereço de prefixo (destino) a uma sub-rede delegada aos arquivos do Azure NetApp é sem suporte. Isso afetará a conectividade da VM.

## <a name="steps"></a>Etapas 
1.  Acesse a folha **Redes virtuais** no portal do Azure e selecione a rede virtual que você deseja usar para o Azure NetApp Files.    

1. Selecione **Sub-redes** na folha da Rede virtual e clique no botão **+ Sub-rede**. 

1. Crie uma nova sub-rede a ser usada para o Azure NetApp Files preenchendo os campos obrigatórios a seguir na página Adicionar sub-rede:
    * **Nome**: especifique o nome da sub-rede.
    * **Intervalo de endereços**: especifique o intervalo de endereços IP.
    * **Delegação de sub-rede**: Selecione **Microsoft.NetApp/volumes**. 

      ![Delegação de sub-rede](../media/azure-netapp-files/azure-netapp-files-subnet-delegation.png)
    
Também é possível criar e delegar uma sub-rede quando você [cria um volume do Azure NetApp Files](azure-netapp-files-create-volumes.md). 

## <a name="next-steps"></a>Próximos passos  
* [Criar um volume do Azure NetApp Files](azure-netapp-files-create-volumes.md)
* [Saiba mais sobre a integração de rede virtual para os serviços do Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)


