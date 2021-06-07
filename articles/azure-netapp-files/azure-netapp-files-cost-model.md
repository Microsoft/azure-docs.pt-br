---
title: Modelo de custo para Azure NetApp Files | Microsoft Docs
description: Descreve o modelo de custo para Azure NetApp Files para gerenciar as despesas do serviço.
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
ms.date: 09/22/2020
ms.author: b-juche
ms.openlocfilehash: 9c4eebae6909c9ef0969bc85bcb9a985db2a7c02
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91325599"
---
# <a name="cost-model-for-azure-netapp-files"></a>Modelo de custo para o Azure NetApp Files 

Entender o modelo de custo do Azure NetApp Files ajuda você a gerenciar suas despesas do serviço. 

Para o modelo de custo específico para replicação entre regiões, consulte [modelo de custo para replicação entre regiões](cross-region-replication-introduction.md#cost-model-for-cross-region-replication).

## <a name="calculation-of-capacity-consumption"></a>Cálculo do consumo de capacidade

Azure NetApp Files é cobrado na capacidade de armazenamento provisionada.  A capacidade provisionada é alocada pela criação de pools de capacidade.  Os pools de capacidade são cobrados com base em $/provisioned-GiB/month em incrementos por hora. O tamanho mínimo para um único pool de capacidade é 4 TiB, e os pools de capacidade podem ser subsequentemente expandidos em incrementos de 1 TiB. Os volumes são criados nos pools de capacidade.  Cada volume recebe uma cota que diminui da capacidade provisionada por pools. A cota que pode ser atribuída a volumes varia de um mínimo de 100 GiB a um máximo de 100 TiB.  

Para um volume ativo, o consumo de capacidade em relação à cota é baseado na capacidade lógica (efetiva).

Se o consumo de capacidade real de um volume exceder sua cota de armazenamento, o volume poderá continuar crescendo. As gravações ainda serão permitidas desde que o tamanho real do volume seja menor que o limite do sistema (100 TiB).  

A capacidade total usada em um pool de capacidade em relação ao valor provisionado é a soma do maior da cota atribuída ou do consumo real de todos os volumes no pool: 

   ![Total de cálculo da capacidade usada](../media/azure-netapp-files/azure-netapp-files-total-used-capacity.png)

O diagrama a seguir ilustra esses conceitos.  
* Temos um pool de capacidade com 4 TiB de capacidade provisionada.  O pool contém três volumes.  
    * O volume 1 recebe uma cota de 2 TiB e tem 800 GiB de consumo.  
    * O volume 2 recebe uma cota de 1 TiB e tem 100 GiB de consumo.  
    * O volume 3 recebe uma cota de 500 GiB, mas tem 800 GiB de consumo (excedente).  
* O pool de capacidade é medido por 4 TiB de capacidade (o valor provisionado).  
    3,8 TiB de capacidade é consumido (2 TiB e 1 TiB de cota dos volumes 1 e 2, e 800 GiB de consumo real para o volume 3). E 200 GiB de capacidade estão restantes.

   ![Pool de capacidade com três volumes](../media/azure-netapp-files/azure-netapp-files-capacity-pool-with-three-vols.png)

## <a name="overage-in-capacity-consumption"></a>Excedente no consumo de capacidade  

Quando a capacidade total usada de um pool excede sua capacidade provisionada, as gravações de dados ainda são permitidas.  Após o período de carência (uma hora), se a capacidade usada do pool ainda exceder sua capacidade provisionada, o tamanho do pool será aumentado automaticamente em incrementos de 1 TiB até que a capacidade provisionada seja maior que a capacidade total usada.  Por exemplo, na ilustração acima, se o volume 3 continuar crescendo e o consumo real atingir 1,2 TiB, depois do período de carência, o pool será automaticamente redimensionado para 5 TiB.  O resultado é que a capacidade do pool provisionado (5 TiB) excede a capacidade usada (4,2 TiB).  

Embora o tamanho do pool de capacidade cresça automaticamente para atender à demanda do volume, ele não é automaticamente reduzido quando o tamanho do volume diminui. Se você quiser diminuir o tamanho do pool de capacidade após a diminuição do tamanho do volume (por exemplo, após a limpeza de dados de um volume), será necessário reduzir _manualmente_ o tamanho do pool de capacidade.

## <a name="manual-changes-of-the-pool-size"></a>Alterações manuais do tamanho do pool  

Você pode aumentar ou diminuir o tamanho do pool manualmente. No entanto, as seguintes restrições se aplicam:
* Limites mínimo e máximo de serviço  
    Consulte o artigo sobre [limites de recursos](azure-netapp-files-resource-limits.md).
* Um incremento de 1 TiB após a compra mínima de 4-TiB inicial
* Um incremento de cobrança mínimo de uma hora
* O tamanho do pool provisionado não pode ser reduzido para menos que a capacidade total usada no pool.
* Para pools de capacidade com QoS manual, o tamanho do pool só poderá ser diminuído se o tamanho e o nível de serviço fornecerem mais taxa de transferência do que a taxa de transferência real atribuída de todos os volumes.

## <a name="behavior-of-maximum-size-pool-overage"></a>Comportamento do excedente do pool de tamanho máximo   

O tamanho máximo de um pool de capacidade que pode ser criado ou redimensionado é 500 TiB.  Quando a capacidade total usada em um pool de capacidade exceder 500 TiB, ocorrerão as seguintes situações:
* As gravações de dados ainda serão permitidas (se o volume estiver abaixo do máximo do sistema de 100 TiB).
* Após o período de carência de uma hora, o pool será redimensionado automaticamente em incrementos de 1 TiB, até que a capacidade provisionada do pool exceda a capacidade total utilizada.
* A capacidade de pool provisionada e cobrada adicional que excede 500 TiB não pode ser usada para atribuir cota de volume. Ele também não pode ser usado para expandir os limites de QoS de desempenho.  
    Consulte [níveis de serviço](azure-netapp-files-service-levels.md) sobre limites de desempenho e dimensionamento de QoS.

O diagrama a seguir ilustra esses conceitos:
* Temos um pool de capacidade com uma camada de armazenamento Premium e uma capacidade de 500-TiB. O pool contém nove volumes.
    * Os volumes de 1 a 8 recebem uma cota de 60 TiB cada.  A capacidade total usada é de 480 TiB.  
        Cada volume tem um limite de QoS de 3,75 GiB/s de taxa de transferência (60 TiB * 64 MiB/s).  
    * O volume 9 recebe uma cota de 20 TiB.  
        O volume 9 tem um limite de QoS de 1,25 GiB/s de taxa de transferência (20 TiB * 64 MiB/s).
* O volume 9 é um cenário excedente. Ele tem 25 TiB de consumo real.  
    * Após o período de carência de uma hora, o pool de capacidade será redimensionado para 505 TiB.  
        Ou seja, a capacidade total usada = 8 * 60-cota de TiB para os volumes de 1 a 8 e 25 TiB de consumo real para o volume 9.
    * A capacidade cobrada é 505 TiB.
    * A cota de volume do volume 9 não pode ser aumentada (porque a cota total atribuída para o pool não pode exceder 500 TiB).
    * A taxa de transferência de QoS adicional pode não ser atribuída (porque a QoS total para o pool ainda se baseia em 500 TiB).

   ![Pool de capacidade com nove volumes](../media/azure-netapp-files/azure-netapp-files-capacity-pool-with-nine-vols.png)

## <a name="capacity-consumption-of-snapshots"></a>Consumo de capacidade de instantâneos 

O consumo de capacidade de instantâneos no Azure NetApp Files é cobrado em relação à cota do volume pai.  Como resultado, ele compartilha a mesma taxa de cobrança que o pool de capacidade ao qual o volume pertence.  No entanto, ao contrário do volume ativo, o consumo de instantâneo é medido com base na capacidade incremental consumida.  Os instantâneos de Azure NetApp Files são diferenciais por natureza. Dependendo da taxa de alteração dos dados, os instantâneos geralmente consomem muito menos capacidade do que a capacidade lógica do volume ativo. Por exemplo, suponha que você tenha um instantâneo de um volume 500-GiB que contém apenas 10 GiB de dados diferenciais. A capacidade cobrada em relação à cota de volume para esse instantâneo seria 10 GiB, e não 500 GiB. 

## <a name="next-steps"></a>Próximas etapas

* [Página de preços do Azure NetApp Files](https://azure.microsoft.com/pricing/details/storage/netapp/)
* [Níveis de serviço do Azure NetApp Files](azure-netapp-files-service-levels.md)
* [Limites de recursos do Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Modelo de custo para replicação entre regiões](cross-region-replication-introduction.md#cost-model-for-cross-region-replication)
