---
title: Configurar a política de exportação para um volume do NFS usando arquivos do Azure NetApp | Microsoft Docs
description: Descreve como configurar a política de exportação para controlar o acesso a um volume do NFS usando arquivos do Azure NetApp
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
ms.date: 03/20/2019
ms.author: b-juche
ms.openlocfilehash: 8cda5921a1aec86d28beabbd9cea5b07a203a0e8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61086136"
---
# <a name="configure-export-policy-for-an-nfs-volume"></a>Configurar a política de exportação para um volume do NFS

Opcionalmente, você pode configurar a política de exportação para controlar o acesso a um volume de Azure NetApp Files. Política de exportação é o suporte somente para volumes do NFS. 

## <a name="steps"></a>Etapas 

1.  Clique na folha **Criar Política de Exportação** na folha Gerenciar Volume. 

2.  Especifique informações para os campos a seguir para criar uma regra de política de exportação:   
    *  **Index**   
        Especifique o número de índice para a regra.  
        Uma política de exportação pode consistir em até cinco regras. As regras são avaliadas de acordo com sua ordem na lista de números de índice. As regras com números de índice mais baixos são avaliadas primeiro. Por exemplo, a regra com o número de índice 1 é avaliada antes da regra com o número de índice 2. 

    * **Clientes permitidos**   
        Especifique o valor em um dos seguintes formatos:  
        * Endereço IPv4, por exemplo, `10.1.12.24` 
        * Endereço IPv4 com uma máscara de sub-rede expressada como um número de bits, por exemplo, `10.1.12.10/4`

    * **Access**  
        Selecione um dos seguintes tipos de acesso:  
        * Sem Acesso 
        * Leitura e Gravação
        * Somente leitura

    * **Protocolos**   
        Especifique o protocolo a ser usado para a política de exportação.   
        Atualmente, a política de exportação de arquivos do Azure NetApp suporta apenas NFS V3.

    ![Exportar política](../media/azure-netapp-files/azure-netapp-files-export-policy.png) 


## <a name="next-steps"></a>Próximas etapas 
* [Gerenciar volumes](azure-netapp-files-manage-volumes.md)
* [Montar ou desmontar um volume para máquinas virtuais](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Gerenciar instantâneos](azure-netapp-files-manage-snapshots.md)
