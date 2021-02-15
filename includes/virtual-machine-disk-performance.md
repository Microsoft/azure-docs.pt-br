---
title: incluir arquivo
description: incluir arquivo
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 09/25/2020
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: 4770ac0181c64ef800aa02ba87284c8add357e36
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2020
ms.locfileid: "92518034"
---
Este artigo ajuda a esclarecer o desempenho do disco e como ele funciona quando você combina máquinas virtuais do Azure e discos do Azure. Ele também descreve como você pode diagnosticar afunilamentos para a e/s de disco e as alterações que você pode fazer para otimizar o desempenho.

## <a name="how-does-disk-performance-work"></a>Como funciona o desempenho do disco?
As máquinas virtuais do Azure têm o IOPS (operações de entrada/saída por segundo) e os limites de desempenho de taxa de transferência com base no tipo e no tamanho da máquina virtual. Discos do sistema operacional e discos de dados podem ser anexados a máquinas virtuais. Os discos têm seus próprios limites de IOPS e taxa de transferência.

O desempenho do aplicativo é limitado quando solicita mais IOPS ou taxa de transferência do que o que é alocado para as máquinas virtuais ou discos anexados. Quando estiver limitado, o aplicativo apresentará um desempenho de qualidade inferior. Isso pode levar a consequências negativas, como maior latência. Vamos executar alguns exemplos para esclarecer esse conceito. Para facilitar o acompanhamento desses exemplos, veremos apenas IOPS. Porém, a mesma lógica se aplica à taxa de transferência.

## <a name="disk-io-capping"></a>Limitação de e/s de disco

**Instalação**

- Standard_D8s_v3
  - IOPS não armazenado em cache: 12.800
- Disco do sistema operacional E30
  - IOPS: 500
- Dois discos de dados E30 × 2
  - IOPS: 500

![Diagrama mostrando o limitação de nível de disco.](media/vm-disk-performance/disk-level-throttling.jpg)

O aplicativo em execução na máquina virtual faz uma solicitação que exige 10.000 IOPS para a máquina virtual. Todos os quais são permitidos pela VM porque a máquina virtual Standard_D8s_v3 pode executar até 12.800 IOPS.

As solicitações de IOPS de 10.000 são divididas em três solicitações diferentes para os diferentes discos:

- 1.000 IOPS são solicitados para o disco do sistema operacional.
- 4.500 IOPS são solicitadas para cada disco de dados.

Todos os discos anexados são discos E30 e só podem manipular 500 IOPS. Portanto, eles respondem com 500 IOPS cada. O desempenho do aplicativo é limitado pelos discos anexados e só pode processar 1.500 IOPS. O aplicativo pode funcionar em desempenho máximo em 10.000 IOPS se discos de melhor desempenho forem usados, como SSD Premium discos p30.

## <a name="virtual-machine-io-capping"></a>E/s de máquina virtual com limitação

**Instalação**

- Standard_D8s_v3
  - IOPS não armazenado em cache: 12.800
- Disco do sistema operacional p30
  - IOPS: 5.000
- Dois discos de dados p30 × 2
  - IOPS: 5.000

![Diagrama mostrando o nível de limitação da máquina virtual.](media/vm-disk-performance/vm-level-throttling.jpg)

O aplicativo em execução na máquina virtual faz uma solicitação que exige 15.000 IOPS. Infelizmente, a máquina virtual Standard_D8s_v3 é provisionada apenas para lidar com 12.800 IOPS. O aplicativo é limitado pelos limites da máquina virtual e deve alocar o IOPS alocado de 12.800.

Esses 12.800 IOPS solicitados são divididos em três solicitações diferentes para os diferentes discos:

- 4.267 IOPS são solicitados para o disco do sistema operacional.
- 4.266 IOPS são solicitadas para cada disco de dados.

Todos os discos anexados são discos p30 que podem lidar com 5.000 IOPS. Portanto, eles respondem de volta com seus valores solicitados.
