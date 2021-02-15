---
title: Avaliar o aplicativo no Armazenamento em Disco do Azure
description: Saiba mais sobre o processo de aplicação de parâmetros de comparação em seu aplicativo no Azure.
author: roygara
ms.author: rogarana
ms.date: 01/29/2021
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: bfda14acc2e50005e25faafa3037805af871c1df
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2021
ms.locfileid: "99094566"
---
# <a name="benchmark-a-disk"></a>Submeter um disco a benchmark

Os parâmetros de comparação são o processo de simular diferentes cargas de trabalho no aplicativo e avaliar o desempenho do aplicativo para cada carga de trabalho. Usando as etapas descritas no artigo [Projeto para alto desempenho](premium-storage-performance.md), você reuniu os requisitos de desempenho do aplicativo. Ao executar ferramentas de benchmark nas VMs que hospedam o aplicativo, você pode determinar os níveis de desempenho que seu aplicativo pode atingir com o SSDs Premium. Neste artigo, fornecemos exemplos de benchmarking de uma VM Standard_D8ds_v4 provisionada com o SSDs Premium do Azure.

Usamos ferramentas comuns de benchmark DiskSpd e FIO, para Windows e Linux, respectivamente. Essas ferramentas geram vários threads que simulam uma carga de trabalho parecida com uma produção e avaliam o desempenho do sistema. Usando as ferramentas, você pode configurar parâmetros como tamanho do bloco e profundidade da fila, que normalmente você não pode mudar para um aplicativo. Isso proporciona mais flexibilidade para impulsionar o desempenho máximo em uma VM de alta escala provisionada com o SSDs Premium para diferentes tipos de cargas de trabalho de aplicativo. Para saber mais sobre cada ferramenta de benchmark, visite [DiskSpd](https://github.com/Microsoft/diskspd/wiki/) e [fio](http://freecode.com/projects/fio).

Para seguir os exemplos abaixo, crie um Standard_D8ds_v4 e anexe quatro SSDs Premium à VM. Dos quatro discos, configure três com o cache de host como "None" e distribua-os para um volume chamado NoCacheWrites. Configure o cache de host como "ReadOnly" no disco restante e crie um volume chamado CacheReads com esse disco. Usando essa configuração, você pode ver o desempenho máximo de leitura e gravação de uma VM Standard_D8ds_v4. Para obter etapas detalhadas sobre como criar um Standard_D8ds_v4 com o SSDs Premium, consulte [projetando para alto desempenho](premium-storage-performance.md).

[!INCLUDE [virtual-machines-disks-benchmarking](../../includes/virtual-machines-managed-disks-benchmarking.md)]

## <a name="next-steps"></a>Próximas etapas

Vá para nosso artigo sobre como [projetar para alto desempenho](premium-storage-performance.md).

Neste artigo, você cria uma lista de verificação semelhante ao aplicativo existente para o protótipo. Usando ferramentas de Benchmark, você pode simular as cargas de trabalho e avaliar o desempenho no aplicativo do protótipo. Com isso, será possível determinar qual oferta de disco pode corresponder ou superar os requisitos de desempenho do aplicativo. Em seguida, você pode implementar as mesmas diretrizes para o aplicativo de produção.
