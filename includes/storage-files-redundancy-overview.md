---
title: incluir arquivo
description: arquivo de inclusão
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/27/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: b7c097306ba46d4f0024aecc55994508e2d8a090
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96011671"
---
Para proteger os dados em seus compartilhamentos de arquivo do Azure contra perda ou corrupção de dados, todos os compartilhamentos de arquivos do Azure armazenam várias cópias de cada arquivo conforme eles são gravados. Será possível selecionar graus adicionais de redundância dependendo dos requisitos de sua carga de trabalho. No momento, os Arquivos do Azure são compatíveis com as seguintes opções de redundância de dados:

- **Redundância local**: o armazenamento com redundância local, geralmente chamado de LRS, significa que cada arquivo é armazenado três vezes em um cluster de armazenamento do Azure. Isso fornece proteção contra a perda de dados devido a falhas de hardware, como uma unidade de disco defeituosa.
- **Redundância de zona**: o armazenamento com redundância de zona, geralmente chamado de ZRS, significa que cada arquivo é armazenado três vezes em três clusters distintos de armazenamento do Azure. Assim como ocorre no armazenamento com redundância local, a redundância de zona fornece três cópias de cada arquivo. No entanto, essas cópias são fisicamente isoladas em três clusters distintos de armazenamento em diferentes *zonas de disponibilidade* do Azure. As zonas de disponibilidade são locais físicos exclusivos em uma região do Azure. Cada zona é composta por um ou mais datacenters equipados com energia, resfriamento e rede independentes. Uma gravação no armazenamento não será aceita até que seja gravada nos clusters de armazenamento das três zonas de disponibilidade. 
- **Redundância geográfica**: o armazenamento com redundância geográfica, geralmente chamado de GRS, funciona como o armazenamento com redundância local, no qual um arquivo é armazenado três vezes em um cluster de armazenamento do Azure na região primária. Todas as gravações depois são replicadas de maneira assíncrona para uma região secundária definida pela Microsoft. O armazenamento com redundância geográfica fornece seis cópias de seus dados difundidos entre duas regiões do Azure. No caso de um grande desastre, como a perda permanente de uma região do Azure devido a um desastre natural ou outro evento semelhante, a Microsoft executará um failover para que a região secundária em vigor se torne a primária, atendendo a todas as operações. Como a replicação entre as regiões primária e secundária é assíncrona, no caso de um grande desastre, os dados que não tiverem sido replicados para a região secundária serão perdidos. Também será possível executar um failover manual de uma conta de armazenamento com redundância geográfica.
- **Redundância de zona geográfica**: o armazenamento com redundância de zona geográfica, geralmente chamado de GZRS, funciona como o armazenamento com redundância de zona, no qual um arquivo é armazenado três vezes em três clusters distintos de armazenamento na região primária. Todas as gravações depois são replicadas de maneira assíncrona para uma região secundária definida pela Microsoft. O processo de failover do armazenamento com redundância de zona geográfica funciona da mesma forma que o armazenamento com redundância geográfica.

Os compartilhamentos de arquivo Standard do Azure são compatíveis com os quatro tipos de redundância, enquanto os compartilhamentos de arquivos Premium do Azure são compatíveis somente com o armazenamento com redundância local e de zona.

As contas GPv2 (armazenamento de uso geral versão 2) fornecem duas opções adicionais de redundância que não são compatíveis com os Arquivos do Azure: o armazenamento com redundância geográfica com acesso de leitura, geralmente chamado de RA-GRS, e o armazenamento com redundância de zona geográfica com acesso de leitura, geralmente chamado de RA-GZRS. Será possível provisionar compartilhamentos de arquivo do Azure em contas de armazenamento com essas opções definidas. No entanto, os Arquivos do Azure não são compatíveis com a leitura da região secundária. Os compartilhamentos de arquivo do Azure implantados em contas de armazenamento com redundância geográfica ou de zona geográfica com acesso de leitura serão cobrados como um armazenamento com redundância geográfica ou um armazenamento com redundância de zona geográfica, respectivamente.