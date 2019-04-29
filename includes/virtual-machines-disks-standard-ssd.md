---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 08/14/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: da46687517dbfe189571286087d4ef29d50d1246
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60564269"
---
# <a name="standard-ssd-managed-disks-for-azure-virtual-machine-workloads"></a>Discos Gerenciados SSD Padrão para carga de trabalho da Máquina virtual

Os Discos Gerenciados SSD (Unidade de Estado Sólido) Padrão do Azure são uma opção de armazenamento otimizado para cargas de trabalho que necessitam de desempenho consistente em níveis de IOPS mais baixos. SSD padrão oferece uma experiência de bom nível de entrada para aqueles que desejam migrar para a nuvem, especialmente se passar por problemas com a variação das cargas de trabalho em execução nas soluções de HD no local. SSDs padrão fornecem melhor disponibilidade, consistência, confiabilidade e latência em comparação com as Unidades de HD e são ideais para servidores Web, servidores de aplicativos de IOPS baixa, aplicativos corporativos pouco usados e cargas de trabalho de Desenvolvimento/Teste.

## <a name="standard-ssd-features"></a>Recursos do SSD Padrão

**Managed Disks**: SSDs Standard só estão disponível como discos gerenciados. Não há suporte para Discos Não Gerenciados e Blobs de Páginas em SSD Padrão. Ao criar o Disco Gerenciado, somente é necessário especificar o tipo como Standard HDD e o tamanho do disco requerido; e o Azure cria e gerencia o disco para você.
Os SSDs padrão suportam todas as operações clássicas do modelo de implantação oferecidas pelos discos gerenciados. Por exemplo, você pode criar, copiar ou tirar instantâneo dos Discos Gerenciados SSD Padrão da mesma maneira que faria com o Managed Disks.

**Máquinas Virtuais**: SSDs Standard podem ser usados com todas as VMs do Azure, incluindo tipos de VM que não dão suporte a Discos Premium. Por exemplo, se você estiver usando uma VM da série A, ou VM da série N ou série DS, ou quaisquer outras séries de VM do Azure, é possível usar SSDs Padrão com essa VM. Com a introdução do SSD Padrão, habilitamos uma ampla gama de cargas de trabalho que antes usavam discos com base em HD para fazer a transição para discos baseados em SSD e experimentamos um desempenho consistente, maior disponibilidade, melhor latência e uma melhor experiência geral que veio com os SSDs.

**Altamente duráveis e disponíveis**: SSDs Standard são construídos na mesma plataforma de Discos do Azure, que proporciona consistentemente alta disponibilidade e durabilidade para discos. Os Discos do Azure foram projetados para oferecer uma disponibilidade de 99,999%. Como todos os Discos Gerenciados, os SSDs Padrão também oferecem LRS (armazenamento com redundância local). Com LRS, a plataforma mantém múltiplas réplicas dos dados para cada disco e proporciona durabilidade de nível empresarial de modo consistente para discos de IaaS, com uma Taxa de Falha Anualizada de 0%, líder do setor.

**Instantâneos**: Como todos os discos gerenciados, o SSDs padrão também dá suporte a criação de instantâneos. O tipo de instantâneo pode ser Standard (HD) ou Premium (SSD). Para economia de custos, recomendamos o tipo de Instantâneo Standard (HD) para todos os tipos de disco do Azure. Isso porque ao criar um disco gerenciado a partir de um instantâneo, você sempre poderá escolher uma camada superior, como SSD Standard ou SSD Premium.

## <a name="scalability-and-performance-targets"></a>Escalabilidade e metas de desempenho

A tabela a seguir contém os tamanhos de disco atualmente oferecidos atualmente para SSD Padrão. Tamanhos demarcados com um asterisco estão atualmente em visualização.

|Tipo de Disco SSD Padrão  |Tamanho do disco  |IOPS por Disco  |Taxa de transferência por disco  |
|---------|---------|---------|---------|
|E4     |32 GiB         |Até 120         |Até 25 MiB por segundo         |
|E6     |64 GiB         |Até 240         |Até 50 MiB por segundo         |
|E10     |128 GiB         |Até 500         |Até 60 MiB por segundo         |
|E15     |256 GiB         |Até 500         |Até 60 MiB por segundo         |
|E20     |512 GiB         |Até 500         |Até 60 MiB por segundo         |
|E30     |1.024 GiB       |Até 500         |Até 60 MiB por segundo         |
|E40     |2.048 GiB       |Até 500         |Até 60 MiB por segundo         |
|E50     |4.095 GiB       |Até 500         |Até 60 MiB por segundo         |
|E60 *     |8.192 GiB       |Até 1.300       |Até 300 MiB por segundo        |
|E70 *    |16.384 GiB      |Até 2.000       |Até 500 MiB por segundo        |
|E80 *    |32,767 GiB      |Até 2.000       |Até 500 MiB por segundo        |

SSDs Standard são projetados para fornecer latências de milissegundo de dígito único para a maioria das operações de E/S e entregar IOPS e taxa de transferência até os limites descritos na tabela acima 99% do tempo. IOPS e Taxa de Transferência reais podem variar às vezes, dependendo dos padrões de tráfego. SSDs Padrão fornecerão um desempenho mais consistente do que os discos HD com a menor latência.

SSDs Premium, por outro lado, executar melhor SSDs Padrão, com baixas latências, alta IOPS/taxa de transferência e consistência ainda melhor com o desempenho do disco provisionado. O SSD Premium é o tipo de disco recomendado para cargas de trabalho de produção críticas. Se a sua carga de trabalho requer suporte de disco de alto desempenho e baixa latência, considere usar o armazenamento Premium.

Como os SSDs Premium, os SSDs padrão também usam tamanho da Unidade de E/S de 256 KiB. Se os dados transferidos forem menores que 256 KiB, serão considerados 1 unidade de E/S. Tamanhos maiores de E/S são contados como várias E/Ss com tamanho de 256 KB. Por exemplo, uma E/S de 1.100 KiB é contada como cinco unidades de E/S.

## <a name="pricing-and-billing"></a>Preços e cobrança

Ao usar o SSDs Padrão, as seguintes considerações de cobrança se aplicam:

- Tamanho do Disco Gerenciado
- Instantâneos
- Transferências de dados de saída
- Transações

**Tamanho do Disco Gerenciado**: Discos gerenciados são cobrados no tamanho provisionado. O Azure mapeia o tamanho provisionado (arredondado) para a opção de tamanho de disco mais próxima. Para obter detalhes sobre os tamanhos de disco oferecidos, consulte a tabela na seção de Escalabilidade e Metas de Desempenho acima. Cada disco é mapeado para um tamanho de disco provisionado com suporte e é cobrado de acordo. Por exemplo, se você provisionou uma SSD Padrão de 200 GiB, ela será mapeada para a oferta de tamanho de disco de E15 (256 GiB). A cobrança por qualquer disco provisionado é rateada por hora usando o preço mensal para a oferta de Armazenamento Premium. Por exemplo, se você provisionou um disco E10 e o excluiu após 20 horas, será cobrado pela a oferta E10 rateada em 20 horas. Isso é independente da quantidade de dados reais gravados no disco.

**Instantâneos**: Instantâneos de discos gerenciados são cobrados pela capacidade usada pelos instantâneos, o destino e na origem, se houver. Para obter mais informações sobre instantâneos, consulte [Instantâneos de Disco Gerenciado](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview#managed-disk-snapshots).

**Transferências de dados de saída**: [Transferências de dados de saída](https://azure.microsoft.com/pricing/details/bandwidth/) (dados saindo dos data centers do Azure) acarretam a cobrança por uso de largura de banda.

**Transações**: semelhantes ao HDD Standard, as transações em SSDs Standard incorrem cobrança. As transações incluem operações de leitura e gravação no disco. O tamanho da unidade de E/S usada para contabilizar as transações em SSD Padrão é de 256 KiB. Tamanhos maiores de E/S são contados como várias E/Ss com tamanho de 256 KB.

Para obter mais informações sobre os preços de armazenamento para Máquinas Virtuais e Discos Gerenciados, consulte:

- [Preços de Máquinas Virtuais](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)
- [Preços do Managed Disks](https://azure.microsoft.com/pricing/details/managed-disks/)

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a criação de SSDs Padrão, consulte o exemplo que mostra como criar uma VM com várias SSDs padrão.

> [!div class="nextstepaction"]
> [Criar uma VM com várias SSDs padrão](https://github.com/azure/azure-quickstart-templates/tree/master/101-vm-with-standardssd-disk/)
