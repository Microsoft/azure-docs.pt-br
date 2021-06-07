---
title: Considerações sobre desempenho para Azure NetApp Files | Microsoft Docs
description: Saiba mais sobre o desempenho de Azure NetApp Files, incluindo a relação de cota e o limite de taxa de transferência e como aumentar/diminuir dinamicamente a cota de volume.
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
ms.date: 02/19/2021
ms.author: b-juche
ms.openlocfilehash: f963c87148c08a4855befc5afb79d9c5ea0f4481
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101713381"
---
# <a name="performance-considerations-for-azure-netapp-files"></a>Considerações de desempenho para o Azure NetApp Files

O [limite de taxa de transferência](azure-netapp-files-service-levels.md) para um volume com QoS automático é determinado por uma combinação da cota atribuída ao volume e ao nível de serviço selecionado. Para volumes com QoS manual, o limite de taxa de transferência pode ser definido individualmente. Ao fazer planos de desempenho sobre Azure NetApp Files, você precisa entender várias considerações. 

## <a name="quota-and-throughput"></a>Cota e taxa de transferência  

O limite de taxa de transferência é apenas um determinante do desempenho real que será realizado.  

Considerações de desempenho de armazenamento típicas, incluindo a combinação de leitura e gravação, o tamanho da transferência, os padrões aleatórios ou sequenciais, e muitos outros fatores contribuirão para o desempenho total entregue.  

A taxa de transferência máxima de empírica observada no teste é 4.500 MiB/s.  Na camada de armazenamento Premium, uma cota de volume de QoS automática de 70,31 TiB provisionará um limite de taxa de transferência que é alto o suficiente para atingir esse nível de desempenho.  

No caso de volumes de QoS automáticos, se você estiver considerando a atribuição de valores de cota de volume além de 70,31 TiB, a cota adicional poderá ser atribuída a um volume para armazenar dados adicionais. No entanto, a cota adicionada não resultará em um aumento adicional na taxa de transferência real.  

O mesmo teto de taxa de transferência empírica se aplica a volumes com QoS manual. A taxa de transferência máxima pode ser atribuída a um volume é 4.500 MiB/s.

## <a name="automatic-qos-volume-quota-and-throughput"></a>Cota e taxa de transferência de volume de QoS automática

Esta seção descreve o gerenciamento de cotas e a taxa de transferência para volumes com o tipo de QoS automático.

### <a name="overprovisioning-the-volume-quota"></a>Provisionando a cota de volume

Se o desempenho de uma carga de trabalho for limite de taxa de transferência, é possível provisionar a cota automática de volume de QoS para definir um nível de taxa de transferência mais alto e obter um melhor desempenho.  

Por exemplo, se um volume de QoS automático na camada de armazenamento Premium tiver apenas 500 GiB de dados, mas exigir a 128 MiB/s de taxa de transferência, você poderá definir a cota como 2 TiB para que o nível de taxa de transferência seja definido adequadamente (64 MiB/s por TB * 2 TiB = 128 MiB/s).  

Se você provisionar consistentemente um volume para obter uma taxa de transferência mais alta, considere usar os volumes de QoS manuais ou usar um nível de serviço mais alto em vez disso.  No exemplo acima, você pode obter o mesmo limite de taxa de transferência com metade da cota automática de volume de QoS usando a camada de armazenamento em vez disso (128 MiB/s por TiB * 1 TiB = 128 MiB/s).

### <a name="dynamically-increasing-or-decreasing-volume-quota"></a>Aumentando ou diminuindo a cota de volume dinamicamente

Se seus requisitos de desempenho forem temporários por natureza ou se você tiver maior necessidade de desempenho por um período de tempo fixo, você poderá aumentar ou diminuir dinamicamente a cota de volume para ajustar instantaneamente o limite de taxa de transferência.  Observe as seguintes considerações: 

* A cota de volume pode ser aumentada ou diminuída sem a necessidade de pausar e/s e o acesso ao volume não é interrompido ou afetado.  

    Você pode ajustar a cota durante uma transação de e/s ativa em um volume.  Observe que a cota de volume nunca pode ser reduzida abaixo da quantidade de dados lógicos armazenados no volume.

* Quando a cota de volume é alterada, a alteração correspondente no limite de taxa de transferência é quase instantânea. 

    A alteração não interrompe nem afeta o acesso ao volume ou e/s.  

* Ajustar a cota de volume pode exigir uma alteração no tamanho do pool de capacidade.  

    O tamanho do pool de capacidade pode ser ajustado dinamicamente e sem afetar a disponibilidade ou a e/s do volume.

## <a name="manual-qos-volume-quota-and-throughput"></a>Cota de volume de QoS manual e taxa de transferência 

Se você usar volumes de QoS manuais, não precisará provisionar a cota de volume para obter uma taxa de transferência maior, pois a taxa de transferência pode ser atribuída a cada volume de forma independente. No entanto, você ainda precisa garantir que o pool de capacidade seja previamente provisionado com taxa de transferência suficiente para suas necessidades de desempenho. A taxa de transferência de um pool de capacidade é provisionada de acordo com seu nível de tamanho e de serviço. Consulte [níveis de serviço para Azure NetApp files](azure-netapp-files-service-levels.md) para obter mais detalhes.


## <a name="next-steps"></a>Próximas etapas

- [Calculadora de desempenho de Azure NetApp Files](https://cloud.netapp.com/azure-netapp-files/tco?hs_preview=tIKQbfoF-41214739590)
- [Níveis de serviço do Azure NetApp Files](azure-netapp-files-service-levels.md)
- [Parâmetros de comparação de desempenho para o Linux](performance-benchmarks-linux.md)
