---
title: Benchmarking de seu aplicativo no Armazenamento em Disco do Azure
description: Saiba mais sobre o processo de aplicação de parâmetros de comparação em seu aplicativo no Azure.
author: roygara
ms.author: rogarana
ms.date: 01/11/2019
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 0d1fb4d51aa08ce1c4889e82d7284da05a68aa17
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/17/2021
ms.locfileid: "98540508"
---
# <a name="benchmarking-a-disk"></a>Aplicação de parâmetros de comparação em um disco

Os parâmetros de comparação são o processo de simular diferentes cargas de trabalho no aplicativo e avaliar o desempenho do aplicativo para cada carga de trabalho. Usando as etapas descritas no artigo [Projeto para alto desempenho](premium-storage-performance.md), você reuniu os requisitos de desempenho do aplicativo. Ao executar ferramentas de parâmetros de comparação nas VMs que hospedam o aplicativo, você poderá determinar os níveis de desempenho que o aplicativo pode atingir com o Armazenamento Premium. Neste artigo, forneceremos exemplos de parâmetros de comparação de uma VM DS14 Standard provisionada com discos do Armazenamento Premium do Azure.

Usamos ferramentas comuns de parâmetros de comparação, Iometer e FIO, para Windows e Linux, respectivamente. Essas ferramentas geram vários threads que simulam uma carga de trabalho parecida com uma produção e avaliam o desempenho do sistema. Usando as ferramentas, você pode configurar parâmetros como tamanho do bloco e profundidade da fila, que normalmente você não pode mudar para um aplicativo. Isso proporciona mais flexibilidade para impulsionar o desempenho máximo em uma VM de alta escala provisionada com discos premium para diferentes tipos de carga de trabalho de aplicativo. Para saber mais sobre cada ferramenta de parâmetro de comparação, acesse [Iometer](http://www.iometer.org/) e [FIO](http://freecode.com/projects/fio).

Para seguir os exemplos abaixo, crie uma VM DS14 padrão e anexe 11 discos do Armazenamento Premium à VM. Dos 11 discos, configure dez discos com cache de host como "None" e distribua-os em um volume chamado NoCacheWrites. Configure o cache de host como "ReadOnly" no disco restante e crie um volume chamado CacheReads com esse disco. Usando essa configuração, você pode ver o desempenho máximo de Leitura e Gravação de uma VM DS14 Standard. Para obter etapas detalhadas sobre como criar uma VM DS14 com SSDs premium, acesse [Projeto para alto desempenho](premium-storage-performance.md).

[!INCLUDE [virtual-machines-disks-benchmarking](../../includes/virtual-machines-managed-disks-benchmarking.md)]

## <a name="next-steps"></a>Próximas etapas

Vá para nosso artigo sobre como [projetar para alto desempenho](premium-storage-performance.md).

Neste artigo, você cria uma lista de verificação semelhante ao aplicativo existente para o protótipo. Usando ferramentas de Benchmark, você pode simular as cargas de trabalho e avaliar o desempenho no aplicativo do protótipo. Com isso, será possível determinar qual oferta de disco pode corresponder ou superar os requisitos de desempenho do aplicativo. Em seguida, você pode implementar as mesmas diretrizes para o aplicativo de produção.