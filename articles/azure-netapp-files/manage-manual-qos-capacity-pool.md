---
title: Gerenciar um pool de capacidade de QoS manual para Azure NetApp Files | Microsoft Docs
description: Descreve como gerenciar um pool de capacidade que usa o tipo de QoS manual, incluindo a configuração de um pool de capacidade de QoS manual e a alteração de um pool de capacidade para usar a QoS manual.
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
ms.date: 02/04/2021
ms.author: b-juche
ms.openlocfilehash: 1c20190ba1a997ef85f4023a54ecea3c2a77ae53
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102183821"
---
# <a name="manage-a-manual-qos-capacity-pool"></a>Gerenciar um pool de capacidade de QoS manual

Este artigo descreve como gerenciar um pool de capacidade que usa o tipo de QoS manual.  

Consulte [hierarquia de armazenamento de considerações de Azure NetApp files](azure-netapp-files-understand-storage-hierarchy.md) e [desempenho para Azure NetApp files](azure-netapp-files-performance-considerations.md) para entender as considerações sobre tipos de QoS.  

## <a name="register-the-feature"></a>Registrar o recurso
O recurso de tipo de QoS manual está atualmente em visualização. Se você estiver usando esse recurso pela primeira vez, é necessário registrar o recurso primeiro.
  
1.  Registre o recurso:

    ```azurepowershell-interactive
    Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFFlexPool
    ```

2. Verifique o status do registro do recurso: 

    > [!NOTE]
    > O **RegistrationState** pode estar no `Registering` estado de até 60 minutos antes da alteração para `Registered` . Aguarde até que o status seja **registrado** antes de continuar.

    ```azurepowershell-interactive
    Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFFlexPool
    ```
Você também pode usar [comandos de CLI do Azure](/cli/azure/feature) `az feature register` e `az feature show` para registrar o recurso e exibir o status do registro. 

## <a name="set-up-a-new-manual-qos-capacity-pool"></a>Configurar um novo pool de capacidade de QoS manual 

Para criar um novo pool de capacidade usando o tipo de QoS manual:

1. Siga as etapas em [configurar um pool de capacidade](azure-netapp-files-set-up-capacity-pool.md).  

2. Na janela novo pool de capacidade, selecione o tipo de **QoS manual** .  

## <a name="change-a-capacity-pool-to-use-manual-qos"></a><a name="change-to-qos"></a>Alterar um pool de capacidade para usar a QoS manual

Você pode alterar um pool de capacidade que atualmente usa o tipo de QoS automático para usar o tipo de QoS manual.  

> [!IMPORTANT]
> Definir o tipo de capacidade como QoS manual é uma alteração permanente. Não é possível converter uma ferramenta de capacidade de tipo de QoS manual em um pool de capacidade de QoS automático.  
> No momento da conversão, os níveis de taxa de transferência podem ser limitados para estar em conformidade com os limites de taxa de transferência para volumes do tipo de QoS manual. Consulte [limites de recursos para Azure NetApp files](azure-netapp-files-resource-limits.md#resource-limits).

1. Na folha de gerenciamento da sua conta do NetApp, clique em **pools de capacidade** para exibir os pools de capacidade existentes.   
 
2.  Clique no pool de capacidade que você deseja alterar para usar a QoS manual.

3.  Clique em **alterar tipo de QoS**. Em seguida, defina **novo tipo de QoS** como **manual**. Clique em **OK**. 

![Alterar tipo de QoS](../media/azure-netapp-files/change-qos-type.png)


## <a name="monitor-the-throughput-of-a-manual-qos-capacity-pool"></a>Monitorar a taxa de transferência de um pool de capacidade de QoS manual  

As métricas estão disponíveis para ajudá-lo a monitorar a taxa de transferência de leitura e gravação de um volume.  Consulte [métricas para Azure NetApp files](azure-netapp-files-metrics.md).  

## <a name="modify-the-allotted-throughput-of-a-manual-qos-volume"></a>Modificar a taxa de transferência alocada de um volume de QoS manual 

Se um volume estiver contido em um pool de capacidade de QoS manual, você poderá modificar a taxa de transferência do volume alocado conforme necessário.

1. Na página **volumes** , selecione o volume cuja taxa de transferência você deseja modificar.   

2. Clique em **alterar taxa de transferência**. Especifique a **taxa de transferência (MIB/S)** que você deseja. Clique em **OK**. 

    ![Alterar taxa de transferência de QoS](../media/azure-netapp-files/change-qos-throughput.png)

## <a name="next-steps"></a>Próximas etapas  

* [Configurar um pool de capacidade](azure-netapp-files-set-up-capacity-pool.md)
* [Métricas do Azure NetApp Files](azure-netapp-files-metrics.md)
* [Considerações de desempenho para o Azure NetApp Files](azure-netapp-files-performance-considerations.md)
* [Solucionar problemas de pools de capacidade](troubleshoot-capacity-pools.md)
* [Hierarquia de armazenamento do Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
* [Níveis de serviço do Azure NetApp Files](azure-netapp-files-service-levels.md)
* [Modelo de custo para o Azure NetApp Files](azure-netapp-files-cost-model.md)
* [Limites de recursos do Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Criar um volume NFS](azure-netapp-files-create-volumes.md)
* [Criar um volume SMB](azure-netapp-files-create-volumes-smb.md)
* [Criar um volume de protocolo duplo](create-volumes-dual-protocol.md)
