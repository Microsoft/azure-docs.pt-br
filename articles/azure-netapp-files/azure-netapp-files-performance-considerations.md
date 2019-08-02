---
title: Considerações sobre desempenho para arquivos do Azure NetApp | Microsoft Docs
description: Descreve considerações sobre desempenho para arquivos do Azure NetApp.
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
ms.date: 06/25/2019
ms.author: b-juche
ms.openlocfilehash: 97e3c6212edd2ade4eabb96db3543e9b3b68e2ae
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67454133"
---
# <a name="performance-considerations-for-azure-netapp-files"></a>Considerações de desempenho para o Azure NetApp Files

O [limite de taxa de transferência](azure-netapp-files-service-levels.md) para um volume é determinado por uma combinação da cota atribuída ao volume e o serviço de nível selecionado. Quando você faz planos de desempenho sobre os arquivos do Azure NetApp, você precisa entender as várias considerações. 

## <a name="quota-and-throughput"></a>Cota e taxa de transferência  

O limite de taxa de transferência é apenas um determinante do desempenho real que será obtido.  

Considerações de desempenho de armazenamento comuns, incluindo ler e gravar combinação, o tamanho da transferência, padrões sequenciais ou aleatórias e muitos outros fatores contribuirá para o desempenho total fornecido.  

A taxa de transferência máxima empírica que foi observada em teste é 4.500 MiB/s.  Na camada de armazenamento Premium, uma cota do volume de TiB 70.31 provisionará um limite de taxa de transferência é alto o suficiente para alcançar esse nível de desempenho.  

Se você estiver considerando atribuindo valores de cota além do 70.31 TiB de volume, a cota adicional pode ser atribuída a um volume para armazenar dados adicionais. No entanto, a cota adicional não resultará em um aumento adicional na taxa de transferência real.  

Ver [benchmarks de desempenho para arquivos do Azure NetApp](azure-netapp-files-performance-benchmarks.md) para obter informações adicionais.

## <a name="overprovisioning-the-volume-quota"></a>A cota do volume de excesso de provisionamento

Se o desempenho de uma carga de trabalho for associado de limite de taxa de transferência, é possível provisionar excessivamente a cota de volume para definir um nível mais alto de taxa de transferência e atingir um melhor desempenho.  

Por exemplo, se um volume na camada de armazenamento Premium tem apenas 500 GiB de dados, mas requer 128 MiB/s de taxa de transferência, você pode definir a cota para 2 TiB para que o nível de taxa de transferência é definido adequadamente (MiB/s 64 por TB * 2 TiB = 128 MiB/s).  

Se você provisionar excessivamente consistentemente um volume para atingir uma taxa de transferência maior, considere usar um nível mais alto de serviço.  No exemplo acima, você pode obter o mesmo limite de taxa de transferência com metade a cota de volume usando a camada de armazenamento Ultra em vez disso (128 MiB/s por TiB * 1 TiB = 128 MiB/s).

## <a name="dynamically-increasing-or-decreasing-volume-quota"></a>Dinamicamente aumentando ou diminuindo a cota do volume

Se seus requisitos de desempenho são temporários por natureza, ou se você tiver aumentado necessidades de desempenho para um período fixo de tempo, você pode aumentar ou diminuir a cota do volume para instantaneamente ajustar o limite de taxa de transferência dinamicamente.  Observe as seguintes considerações: 

* Cota do volume pode seja aumentada ou diminuída sem qualquer necessidade de pausar e/s e acessar o volume não é interrompida ou afetado.  

    Você pode ajustar a cota durante uma transação ativa de e/s em relação a um volume.  Observe que essa cota do volume nunca pode ser reduzida abaixo da quantidade de dados lógicos que são armazenados no volume.

* Quando a cota do volume for alterada, a alteração correspondente no limite de taxa de transferência é quase instantânea. 

    A alteração interrompa nem afetar o acesso ao volume ou e/s.  

* Cota do volume de ajustar exige uma alteração no tamanho do pool de capacidade.  

    O tamanho do pool de capacidade pode ser ajustado dinamicamente e sem afetar a disponibilidade de volume ou e/s.

## <a name="next-steps"></a>Próximas etapas

- [Níveis de serviço do Azure NetApp Files](azure-netapp-files-service-levels.md)
- [Avaliações de desempenho do arquivos do Azure NetApp](azure-netapp-files-performance-benchmarks.md)