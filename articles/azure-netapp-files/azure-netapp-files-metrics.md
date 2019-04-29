---
title: Métricas do Azure NetApp Files | Microsoft Docs
description: Descreve as métricas do Azure NetApp Files.
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
ms.openlocfilehash: 1563b60ef26ac5e4d40f45095d0109dd9dd71570
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61084932"
---
# <a name="metrics-for-azure-netapp-files"></a>Métricas do Azure NetApp Files

O Azure NetApp Files fornece métricas sobre o armazenamento alocado, a utilização de armazenamento real, taxa de transferência de volume, IOPS e latência. Analisando essas métricas, você pode obter um melhor entendimento sobre o padrão de uso e o desempenho do volume de suas contas do NetApp.  

## <a name="capacity_pools"></a>Métricas de uso de pools de capacidade

- *Tamanho alocado do pool de volume*  
    Esse é o tamanho (GiB) do pool de capacidade provisionado.  
- *Pool de volume alocado usado*  
    Esse é o total de cota do volume (GiB) em um pool de capacidade específico (ou seja, o total dos tamanhos provisionados dos volumes no pool de capacidade). Esse é o tamanho selecionado durante a criação do volume.  
- *Tamanho lógico total do pool de volume*  
    Esse é o total de espaço lógico (GiB) usado nos volumes em um pool de capacidade.  
- *Tamanho total de instantâneos do pool de volume*  
    Esse é o total de espaço lógico incremental usado pelos instantâneos.  

## <a name="volumes"></a>Métricas de uso de volumes

- *Tamanho alocado do volume*   
    Esse é o tamanho do volume (cota) provisionado em GiB.  
- *Tamanho do volume lógico*   
    Esse é o total de espaço lógico usado em um volume (GiB). Esse tamanho inclui espaço lógico usado por sistemas de arquivos ativos e instantâneos.  
- *Tamanho do instantâneo de volume*   
    Esse é o espaço lógico incremental usado pelos instantâneos em um volume.  

## <a name="next-steps"></a>Próximas etapas

* [Compreender a hierarquia de armazenamento do Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
* [Configurar um pool de capacidade](azure-netapp-files-set-up-capacity-pool.md)
* [Criar um volume do Azure NetApp Files](azure-netapp-files-create-volumes.md)
