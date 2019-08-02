---
title: Tamanhos de máquinas virtuais para serviços de Nuvem do Azure | Microsoft Docs
description: Lista os tamanhos diferentes da máquina virtual (e IDs) para funções Web e de trabalho do serviço de nuvem do Azure.
services: cloud-services
documentationcenter: ''
author: georgewallace
ms.service: cloud-services
ms.topic: article
ms.date: 07/18/2017
ms.author: gwallace
ms.openlocfilehash: 0df9ee859ae8e341537f5e832d9ff90e9736cb0c
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68443009"
---
# <a name="sizes-for-cloud-services"></a>Tamanhos dos serviços de nuvem
Este tópico descreve os tamanhos disponíveis e as opções para instâncias de função de Serviço de Nuvem (funções web e funções de trabalho). Ele também fornece considerações de implantação a serem observadas ao planejar o uso desses recursos. Cada tamanho tem uma ID que você coloca em seu [arquivo de definição de serviço](cloud-services-model-and-package.md#csdef). Os preços para cada tamanho estão disponíveis na página [Preços de Serviços de Nuvem](https://azure.microsoft.com/pricing/details/cloud-services/).

> [!NOTE]
> Para ver os limites relacionados do Azure, confira [Assinatura do Azure e limites de serviços, cotas e restrições](../azure-subscription-service-limits.md)
>
>

## <a name="sizes-for-web-and-worker-role-instances"></a>Tamanhos de instâncias de função web e de trabalho
Há vários tamanhos padrão para você escolher no Azure. Entre as considerações sobre algumas dessas dimensões estão:

* As VMs da série D são projetadas para executar aplicativos que exigem maior capacidade de computação e de desempenho de disco temporário. As VMs da série D fornecem processadores mais rápidos, uma maior taxa de memória por núcleo e uma unidade de estado sólido (SSD) para o disco temporário. Para obter detalhes, confira o anúncio no blog do Azure, [Novos tamanhos de máquina virtual da série D](https://azure.microsoft.com/blog/2014/09/22/new-d-series-virtual-machine-sizes/).
* Série Dv2, série Dv3, uma continuação da série D original, apresenta uma CPU mais potente. A CPU da série Dv2 é aproximadamente 35% mais rápida do que a CPU da série D. Ela se baseia na última geração do processador Intel Xeon® E5-2673 v3 (Haswell) de 2.4 GHz e, com a Intel Turbo Boost Technology 2.0, pode chegar a até 3.1 GHz. A série Dv2 tem as mesmas configurações de memória e disco que a série D.
* As VMs da série G oferecem a maior memória e são executadas em hosts com processadores da família Intel Xeon E5 V3.
* As VMs da série A podem ser implantadas em vários de tipos de hardware e processadores. O tamanho é restrito, com base no hardware, para oferecer desempenho de processador consistente para a instância em execução, independentemente do hardware em que está implantado. Para determinar o hardware físico no qual esse tamanho é implantado, consulte o hardware virtual de dentro da Máquina Virtual.
* O tamanho A0 está assinado em excesso no hardware físico. Para este tamanho específico somente, outras implantações de clientes podem afetar o desempenho da carga de trabalho em execução. O desempenho relativo é descrito a seguir como a linha de base esperada, sujeito a uma variação aproximada de 15%.

O tamanho da máquina virtual afeta os preços. O tamanho também afeta a capacidade de processamento, memória e armazenamento da máquina virtual. Os custos de armazenamento são calculados separadamente com base nas páginas usadas na conta de armazenamento. Para obter detalhes, confira [Detalhes de preços dos Serviços de Nuvem](https://azure.microsoft.com/pricing/details/cloud-services/) e [Preços do Armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/).

As considerações a seguir podem ajudá-lo a escolher um tamanho:

* Os tamanhos A8-A11 e série H também são conhecidos como *instâncias de computação intensiva*. O hardware de datacenter do Azure que executa esses tamanhos é projetado e otimizado para aplicativos de uso intensivo de computação e rede, incluindo aplicativos, modelagem e simulações de cluster HPC (computação de alto desempenho). A série de A8-A11 usa Intel Xeon E5-2670 a 2,6 GHz e a série H usa Intel Xeon E5-2667 v3 a 3,2 GHz. Para obter informações detalhadas e considerações sobre o uso desses tamanhos, veja [Tamanhos de VM de computação de alto desempenho](../virtual-machines/windows/sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* As séries Dv3, Dv2, D e G são ideais para aplicativos que exigem CPUs mais rápidas, melhor desempenho de disco local ou que têm uma maior demanda de memória. Elas oferecem uma combinação poderosa para vários aplicativos de nível empresarial.
* Alguns dos hosts físicos em data centers do Azure podem não aceitar tamanhos de máquina virtual maiores, como A5 – A11. Como resultado, você poderá ver a mensagem de erro **Falha ao configurar a máquina virtual {nome da máquina}** ou **Falha ao criar a máquina virtual {nome da máquina}** ao redimensionar uma máquina virtual existente para um novo tamanho, criar uma nova máquina virtual em uma rede virtual criada antes de 16 de abril de 2013 ou adicionar uma nova máquina virtual a um serviço de nuvem existente. Confira [Erro: “Falha ao configurar a máquina virtual”](https://social.msdn.microsoft.com/Forums/9693f56c-fcd3-4d42-850e-5e3b56c7d6be/error-failed-to-configure-virtual-machine-with-a5-a6-or-a7-vm-size?forum=WAVirtualMachinesforWindows) no fórum de suporte de soluções alternativas para cada cenário de implantação.
* Sua assinatura também pode limitar o número de núcleos que você pode implantar em determinadas famílias de tamanho. Para aumentar a cota, entre em contato com o Suporte do Azure.

## <a name="performance-considerations"></a>Considerações sobre o desempenho
Criamos o conceito da ACU (Unidade de Computação do Azure) para fornecer uma maneira de comparar o desempenho de computação (CPU) entre SKUs do Azure e para identificar qual SKU poderá melhor satisfazer suas necessidades de desempenho.  A ACU atualmente é padronizada como uma VM pequena (Standard_A1) sendo 100 e todas as SKUs representam, aproximadamente, o quanto a SKU pode executar um parâmetro de comparação padrão mais rapidamente.

> [!IMPORTANT]
> A ACU é apenas uma diretriz. Os resultados para sua carga de trabalho podem variar.
>
>

<br>

| Família de SKU | ACU/núcleo |
| --- | --- |
| [ExtraSmall](#a-series) |50 |
| [Small-ExtraLarge](#a-series) |100 |
| [A5-7](#a-series) |100 |
| [A8-A11](#a-series) |225* |
| [A v2](#av2-series) |100 |
| [D](#d-series) |160 |
| [D v2](#dv2-series) |160 - 190* |
| [D v3](#dv3-series) |160 - 190* |
| [E v3](#ev3-series) |160 - 190* |
| [G](#g-series) |180 - 240* |
| [H](#h-series) |290 – 300* |

ACUs marcados com um * usam tecnologia Intel® Turbo para aumentar a frequência da CPU e fornecer um aumento de desempenho. A quantidade do aumento pode variar com base no tamanho da VM, na carga de trabalho e em outras cargas de trabalho em execução no mesmo host.

## <a name="size-tables"></a>Tabelas de tamanho
As tabelas a seguir mostram os tamanhos e as capacidades oferecidas.

* A capacidade de armazenamento é mostrada em unidades de GiB ou de 1024^3 bytes. Ao comparar os discos medidos em GB (1000^3 bytes) com os discos medidos em GiB (1024^3), lembre-se de que os números de capacidade fornecidos em GiB poderão parecer menores. Por exemplo, 1023 GiB = 1098,4 GB
* A taxa de transferência do disco é medida em IOPS (operações de entrada/saída por segundo) e em MBps, em que MBps = 10^6 bytes/s.
* Os discos de dados podem operar nos modos em cache ou não armazenado em cache. Para a operação do disco de dados armazenados em cache, o modo de cache do host é definido como **ReadOnly** ou **ReadWrite**. Para as operação do disco de dados não armazenados em cache, o modo de cache do host é definido como **Nenhum**.
* A largura de banda de rede máxima é a largura de banda agregada máxima alocada e atribuída por tipo de VM. A largura de banda máxima fornece diretrizes para selecionar o tipo correto de VM, a fim de garantir que uma capacidade adequada de rede está disponível. Ao mover-se entre Baixa, Moderada, Alta e Muito Alta, a taxa de transferência aumenta de acordo. O desempenho real da rede dependerá de vários fatores, incluindo cargas de rede e aplicativos, bem como configurações de rede do aplicativo.

## <a name="a-series"></a>Séria A
| Tamanho            | Núcleos de CPU | Memória: GiB  | Armazenamento temporário: GiB       | NICs máximas / largura de banda da rede |
|---------------- | --------- | ------------ | -------------------- | ---------------------------- |
| ExtraSmall      | 1         | 0,768        | 20                   | 1 / baixa |
| Pequeno           | 1         | 1,75         | 225                  | 1 / moderada |
| Médio          | 2         | 3.5          | 490                  | 1 / moderada |
| Grande           | 4         | 7            | 1000                 | 2 / alta |
| ExtraLarge      | 8         | 14           | 2040                 | 4 / alta |
| A5              | 2         | 14           | 490                  | 1 / moderada |
| A6              | 4         | 28           | 1000                 | 2 / alta |
| A7              | 8         | 56           | 2040                 | 4 / alta |

## <a name="a-series---compute-intensive-instances"></a>Série A – Instâncias de computação intensiva
Para obter informações e considerações sobre o uso desses tamanhos, veja [Tamanhos de VM de computação de alto desempenho](../virtual-machines/windows/sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

| Tamanho            | Núcleos de CPU | Memória: GiB  | Armazenamento temporário: GiB       | NICs máximas / largura de banda da rede |
|---------------- | --------- | ------------ | -------------------- | ---------------------------- |
| A8*             |8          | 56           | 1817                 | 2 / alta |
| A9*             |16         | 112          | 1817                 | 4 / muito alta |
| A10             |8          | 56           | 1817                 | 2 / alta |
| A11             |16         | 112          | 1817                 | 4 / muito alta |

\*Compatível com RDMA

## <a name="av2-series"></a>Série Av2

| Tamanho            | Núcleos de CPU | Memória: GiB  | Armazenamento temporário (SSD): GiB       | NICs máximas / largura de banda da rede |
|---------------- | --------- | ------------ | -------------------- | ---------------------------- |
| Standard_A1_v2  | 1         | 2            | 10                   | 1 / moderada                 |
| Standard_A2_v2  | 2         | 4            | 20                   | 2 / moderada                 |
| Standard_A4_v2  | 4         | 8            | 40                   | 4 / alta                     |
| Standard_A8_v2  | 8         | 16           | 80                   | 8 / alta                     |
| Standard_A2m_v2 | 2         | 16           | 20                   | 2 / moderada                 |
| Standard_A4m_v2 | 4         | 32           | 40                   | 4 / alta                     |
| Standard_A8m_v2 | 8         | 64           | 80                   | 8 / alta                     |


## <a name="d-series"></a>Série D
| Tamanho            | Núcleos de CPU | Memória: GiB  | Armazenamento temporário (SSD): GiB       | NICs máximas / largura de banda da rede |
|---------------- | --------- | ------------ | -------------------- | ---------------------------- |
| Standard_D1     | 1         | 3.5          | 50                   | 1 / moderada |
| Standard_D2     | 2         | 7            | 100                  | 2 / alta |
| Standard_D3     | 4         | 14           | 200                  | 4 / alta |
| Standard_D4     | 8         | 28           | 400                  | 8 / alta |
| Standard_D11    | 2         | 14           | 100                  | 2 / alta |
| Standard_D12    | 4         | 28           | 200                  | 4 / alta |
| Standard_D13    | 8         | 56           | 400                  | 8 / alta |
| Standard_D14    | 16        | 112          | 800                  | 8 / muito alta |

## <a name="dv2-series"></a>Série Dv2
| Tamanho            | Núcleos de CPU | Memória: GiB  | Armazenamento temporário (SSD): GiB       | NICs máximas / largura de banda da rede |
|---------------- | --------- | ------------ | -------------------- | ---------------------------- |
| Standard_D1_v2  | 1         | 3.5          | 50                   | 1 / moderada |
| Standard_D2_v2  | 2         | 7            | 100                  | 2 / alta |
| Standard_D3_v2  | 4         | 14           | 200                  | 4 / alta |
| Standard_D4_v2  | 8         | 28           | 400                  | 8 / alta |
| Standard_D5_v2  | 16        | 56           | 800                  | 8 / extremamente alta |
| Standard_D11_v2 | 2         | 14           | 100                  | 2 / alta |
| Standard_D12_v2 | 4         | 28           | 200                  | 4 / alta |
| Standard_D13_v2 | 8         | 56           | 400                  | 8 / alta |
| Standard_D14_v2 | 16        | 112          | 800                  | 8 / extremamente alta |
| Standard_D15_v2 | 20        | 140          | 1\.000                | 8 / extremamente alta |

## <a name="dv3-series"></a>Dv3-series

| Tamanho            | Núcleos de CPU | Memória: GiB   | Armazenamento temporário (SSD): GiB       | NICs máximas / largura de banda da rede |
|---------------- | --------- | ------------- | -------------------- | ---------------------------- |
| Standard_D2_v3  | 2         | 8             | 50                   | 2 / moderada |
| Standard_D4_v3  | 4         | 16            | 100                  | 2 / alta |
| Standard_D8_v3  | 8         | 32            | 200                  | 4 / alta |
| Standard_D16_v3 | 16        | 64            | 400                  | 8 / extremamente alta |
| Standard_D32_v3 | 32        | 128           | 800                  | 8 / extremamente alta |
| Standard_D64_v3 | 64        | 256           | 1600                 | 8 / extremamente alta |

## <a name="ev3-series"></a>Ev3-series

| Tamanho            | Núcleos de CPU | Memória: GiB   | Armazenamento temporário (SSD): GiB       | NICs máximas / largura de banda da rede |
|---------------- | --------- | ------------- | -------------------- | ---------------------------- |
| Standard_E2_v3  | 2         | 16            | 50                   | 2 / moderada |
| Standard_E4_v3  | 4         | 32            | 100                  | 2 / alta |
| Standard_E8_v3  | 8         | 64            | 200                  | 4 / alta |
| Standard_E16_v3 | 16        | 128           | 400                  | 8 / extremamente alta |
| Standard_E32_v3 | 32        | 256           | 800                  | 8 / extremamente alta |
| Standard_E64_v3 | 64        | 432           | 1600                 | 8 / extremamente alta |


## <a name="g-series"></a>Série G
| Tamanho            | Núcleos de CPU | Memória: GiB  | Armazenamento temporário (SSD): GiB       | NICs máximas / largura de banda da rede |
|---------------- | --------- | ------------ | -------------------- | ---------------------------- |
| Standard_G1     | 2         | 28           | 384                  |1 / alta |
| Standard_G2     | 4         | 56           | 768                  |2 / alta |
| Standard_G3     | 8         | 112          | 1\.536                |4 / muito alta |
| Standard_G4     | 16        | 224          | 3,072                |8 / extremamente alta |
| Standard_G5     | 32        | 448          | 6\.144                |8 / extremamente alta |

## <a name="h-series"></a>Série H
As máquinas virtuais da série H do Azure são a próxima geração de VMs de computação de alto desempenho voltadas para as necessidades computacionais de alto nível, como modelagem molecular e dinâmica de fluido computacional. Essas VMs de 8 e 16 núcleos baseiam-se na tecnologia do processador Intel Haswell E5-2667 V3 apresentando memória DDR4 e armazenamento local baseado em SSD.

Além de potência de CPU considerável, a série H oferece diversas opções para RDMA e rede de baixa latência usando FDR InfiniBand e várias configurações de memória para dar suporte a requisitos computacionais com uso intensivo de memória.

| Tamanho            | Núcleos de CPU | Memória: GiB  | Armazenamento temporário (SSD): GiB       | NICs máximas / largura de banda da rede |
|---------------- | --------- | ------------ | -------------------- | ---------------------------- |
| Standard_H8     | 8         | 56           | 1000                 | 8 / alta |
| Standard_H16    | 16        | 112          | 2000                 | 8 / muito alta |
| Standard_H8m    | 8         | 112          | 1000                 | 8 / alta |
| Standard_H16m   | 16        | 224          | 2000                 | 8 / muito alta |
| Standard_H16r*  | 16        | 112          | 2000                 | 8 / muito alta |
| Standard_H16mr* | 16        | 224          | 2000                 | 8 / muito alta |

\*Compatível com RDMA

## <a name="configure-sizes-for-cloud-services"></a>Configurar tamanhos para os Serviços de Nuvem
Você pode especificar o tamanho da Máquina Virtual de uma instância de função como parte do modelo de serviço descrito pelo [arquivo de definição de serviço](cloud-services-model-and-package.md#csdef). O tamanho da função determina o número de núcleos de CPU, a capacidade de memória e o tamanho do sistema de arquivos local alocados para uma instância em execução. Escolha o tamanho da função com base em requisitos de recurso do aplicativo.

Veja um exemplo de definição do tamanho da função como Standard_D2 para uma instância de Função Web:

```xml
<WorkerRole name="Worker1" vmsize="Standard_D2">
...
</WorkerRole>
```

## <a name="changing-the-size-of-an-existing-role"></a>Alterando o tamanho de uma função existente

Conforme a natureza de sua carga de trabalho se altera ou novos tamanhos de VM se tornam disponíveis, você pode desejar alterar o tamanho de sua função. Para fazer isso, é necessário alterar o tamanho da VM no arquivo de definição de serviço (como mostrado acima), reempacotar seu Serviço de Nuvem e implantá-lo.

>[!TIP]
> Você pode desejar usar tamanhos de VM diferentes para sua função em ambientes diferentes (por exemplo teste versus produção). Uma maneira de fazer isso é criar vários arquivos de definição de serviço (.csdef) em seu projeto e, em seguida, criar diferentes pacotes de serviço de nuvem para cada ambiente durante a compilação automatizada, usando a ferramenta CSPack. Para saber mais sobre os elementos de um pacote de serviços de nuvem e como criá-los, consulte [O que é o modelo de serviços de nuvem e como empacotá-lo?](cloud-services-model-and-package.md)
>
>

## <a name="get-a-list-of-sizes"></a>Obter uma lista de tamanhos
Você pode usar o PowerShell ou a API REST para obter uma lista de tamanhos. A API REST está documentada [aqui](/previous-versions/azure/reference/dn469422(v=azure.100)). O código a seguir é um comando do PowerShell que listará todos os tamanhos disponíveis para o Cloud Services. 

```powershell
Get-AzureRoleSize | where SupportedByWebWorkerRoles -eq $true | select InstanceSize, RoleSizeLabel
```

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre a [assinatura do Azure e limites de serviços, cotas e restrições](../azure-subscription-service-limits.md).
* Saiba mais [sobre tamanhos de VM de computação de alto desempenho](../virtual-machines/windows/sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para cargas de trabalho do HPC.
