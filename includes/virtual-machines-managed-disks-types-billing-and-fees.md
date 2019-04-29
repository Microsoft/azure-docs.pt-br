---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/22/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 3e303ce2b6f28500406bacf5b66d26f9c78ba46d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60618007"
---
**Transferências de dados de saída**: [Transferências de dados de saída](https://azure.microsoft.com/pricing/details/bandwidth/) (dados saindo dos data centers do Azure) acarretam a cobrança por uso de largura de banda.

**Transações**: Você será cobrado pelo número de transações executadas em um disco gerenciado padrão.

Para obter informações detalhadas sobre os preços para Managed Disks, incluindo os custos de transação, consulte [preços do Managed Disks](https://azure.microsoft.com/pricing/details/managed-disks).

### <a name="ultra-ssd-vm-reservation-fee"></a>Taxa de reserva de VM de SSD Ultra

As VMs do Azure tem um recurso que indica se são compatíveis com o SSD ultra. Uma VM compatível com um disco ultra aloca a capacidade de largura de banda dedicada entre a instância de VM de computação e a unidade de escala de armazenamento de bloco para otimizar o desempenho e reduzir a latência. A inclusão desse recurso na VM resulta em uma cobrança de reserva que será imposta somente se você habilitar o recurso de disco ultra na VM sem anexar um disco ultra a ela. Quando um disco ultra é anexado à VM compatível com discos ultra, essa cobrança não é aplicada. Essa cobrança é por vCPU provisionada na VM.

Consulte a [página de preços do Azure Disks](https://azure.microsoft.com/pricing/details/managed-disks/) para ver detalhes de preços do Disks para discos ultra.