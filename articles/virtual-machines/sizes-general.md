---
title: Tamanhos de VM do Azure – Uso geral | Microsoft Docs
description: Lista os tamanhos diferentes para uso geral disponíveis para máquinas virtuais no Azure. Lista informações sobre o número de vCPUs, discos de dados e NICs, bem como a taxa de transferência de armazenamento e a largura de banda de rede para cada tamanho nessa série.
author: mimckitt
ms.service: virtual-machines
ms.subservice: sizes
ms.devlang: na
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/20/2020
ms.author: mimckitt
ms.openlocfilehash: 6f6f98b39f693d2d15b77cdf6de6251e1c264c8a
ms.sourcegitcommit: 4d48a54d0a3f772c01171719a9b80ee9c41c0c5d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2021
ms.locfileid: "98746080"
---
# <a name="general-purpose-virtual-machine-sizes"></a>Tamanhos das Máquinas Virtuais de uso geral

Os tamanhos de VM para uso geral fornecem uma relação de CPU para memória equilibrada. Ideal para teste e desenvolvimento, bancos de dados pequenos a médios e servidores Web de tráfego baixo a médio. Este artigo fornece informações sobre as ofertas para computação de uso geral.

- As VMs da [série Av2](av2-series.md) podem ser implantadas em uma variedade de tipos de hardware e processadores. As VMs da série A possuem configurações de memória e de desempenho de CPU mais adequadas para cargas de trabalho de entrada, como desenvolvimento e teste. O tamanho é limitado, com base no hardware, para oferecer desempenho de processador consistente para a instância em execução, independentemente do hardware em que é implantado. Para determinar o hardware físico no qual esse tamanho é implantado, consulte o hardware virtual de dentro da Máquina Virtual. Os exemplos de casos de uso incluem servidores de desenvolvimento e teste, servidores Web de tráfego baixo, bancos de dados pequenos a médios, servidores para prova de conceito e repositórios de código.

  > [!NOTE]
  > As VMs A8, A9, A10 a11 estão planejadas para aposentadoria em 3/2021. Para obter mais informações, confira o [Guia de migração de HPC](https://azure.microsoft.com/resources/hpc-migration-guide/). Esses tamanhos de VM estão na série "A_v1" original, não em "v2".

- As VMs de [intermitência da série B](sizes-b-series-burstable.md) são ideais para cargas de trabalho que não precisam do desempenho total da CPU continuamente, como servidores Web, bancos de dados pequenos e ambientes de desenvolvimento e teste. Normalmente, essas cargas de trabalho têm requisitos de desempenho expansíveis. A série B fornece esses clientes a possibilidade de comprar um tamanho VM com um preço consciência da linha de base de desempenho que permite que a instância VM criar créditos quando a VM é menor que o desempenho de base. Quando a VM tiver acumulado crédito, poderá disparar acima da linha de base da VM usando até 100% da CPU quando seu aplicativo requer o maior desempenho de CPU.

- As [séries Dav4 e Dasv4](dav4-dasv4-series.md) são novos tamanhos que utilizam o processador 2,35 GHz EPYC<sup>TM</sup> 7452 da AMD em uma configuração multi-thread com até 256 MB de cache L3, que dedica 8 MB desse cache L3 a cada 8 núcleos, aumentando as opções do cliente para executar as cargas de trabalho de uso geral. As séries Dav4 e Dasv4 têm as mesmas configurações de memória e disco que as séries Dsv3 e D.

- [Dv4 e Dsv4-Series](dv4-dsv4-series.md) A dv4 e a série Dsv4 são executadas nos processadores Intel® Xeon® Platinum 8272CL (Cascadey Lake) em uma configuração de hiperthread, fornecendo uma proposta de valor melhor para as cargas de trabalho de uso geral. Ele apresenta uma velocidade de clock de Turbo principal de 3,4 GHz.

- [Ddv4 e Ddsv4-Series](ddv4-ddsv4-series.md) A Ddv4 e a série Ddsv4 são executadas nos &reg; processadores Intel Xeon &reg; Platinum 8272CL (cascadey Lake) em uma configuração de hiperthread, fornecendo uma proposta de valor melhor para as cargas de trabalho de uso geral. Ele apresenta uma velocidade de clock de Turbo principal de 3,4 GHz [, &reg; tecnologia Intel Turbo Boost 2,0](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html), [ &reg; tecnologia Intel Hyper-Threading](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html) e [extensões de &reg; vetor avançadas Intel 512 (Intel &reg; AVX-512)](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html). Eles também dão suporte ao [ &reg; aumento de aprendizado profundo da Intel](https://software.intel.com/content/www/us/en/develop/topics/ai/deep-learning-boost.html). Esses novos tamanhos de VM terão um armazenamento local 50% maior, bem como um melhor IOPS de disco local para leitura e gravação em comparação com os tamanhos [Dv3/Dsv3](./dv3-dsv3-series.md) com [VMs Gen2](./generation-2.md).

- [Dv3 e Dsv3-Series](dv3-dsv3-series.md) As VMs são executadas em 2ª geração Intel® Xeon® Platinum 8272CL (cascade), Intel® Xeon® 8171M 2.1 GHz (Skylake), Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) ou os processadores Intel® Xeon® E5-2673 v3 2,4 GHz (Haswell) em uma configuração de hiperthread, fornecendo uma melhor proposta de valor para as cargas de trabalho de uso geral. A memória foi expandida (de ~3.5 GiB/vCPU para 4 GiB/vCPU) enquanto os limites de rede e disco em uma base por núcleo foram ajustados para alinhar com a mudança para o hyperthreading. A série Dv3 não tem mais os tamanhos de VM de memória alta da série D/Dv2, que foram migrados para as [séries Ev3 e Esv3](ev3-esv3-series.md) otimizadas para memória.

- As VMs das [séries Dv2 e Dsv2](dv2-dsv2-series.md), continuação da série D original, apresentam uma CPU mais potente e a configuração ideal de CPU/memória, tornando-as adequadas para a maioria das cargas de trabalho de produção. A série Dv2 é aproximadamente 35% mais rápida do que a série D. A série Dv2 é executada em 2ª geração Intel® Xeon® Platinum 8272CL (Cascadey Lake), Intel® Xeon® 8171M 2.1 GHz (Skylake), Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) ou os processadores Intel® Xeon® E5-2673 v3 2,4 GHz (Haswell) com a tecnologia Intel Turbo Boost 2,0. A série Dv2 tem as mesmas configurações de memória e disco que a série D.

- A [série DCv2](dcv2-series.md) pode ajudar a proteger a confidencialidade e a integridade dos dados e do código enquanto eles são processados na nuvem pública. Esses computadores têm o suporte da última geração do processador Intel XEON E-2288G com a tecnologia SGX. Com a Tecnologia Intel Turbo Boost, esses computadores podem chegar a 5,0 GHz. As instâncias da série DCv2 permitem que os clientes criem aplicativos seguros baseados em enclave para proteger os códigos e os dados enquanto estiverem em uso.

## <a name="other-sizes"></a>Outros tamanhos

- [Computação otimizada](sizes-compute.md)
- [Memória otimizada](sizes-memory.md)
- [Armazenamento otimizado](sizes-storage.md)
- [GPU otimizada](sizes-gpu.md)
- [Computação de alto desempenho](sizes-hpc.md)
- [Gerações anteriores](sizes-previous-gen.md)

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como as [ACUs (unidade de computação do Azure)](acu.md) podem ajudar você a comparar o desempenho de computação entre SKUs do Azure.

Para obter mais informações sobre como o Azure nomeia suas VMs, consulte [convenções de nomenclatura de tamanhos de máquina virtual do Azure](./vm-naming-conventions.md).
