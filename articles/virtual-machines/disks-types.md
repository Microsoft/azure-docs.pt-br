---
title: Escolha um tipo de disco para VMs IaaS do Azure – discos gerenciados
description: Saiba mais sobre os tipos de disco do Azure disponíveis para máquinas virtuais, incluindo discos ultra, SSDs Premium, SSDs Standard e HDDs Standard.
author: roygara
ms.author: rogarana
ms.date: 09/30/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 1a7e8e71e26af241d16095a5fa1e6a02a7e3d4c2
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96500760"
---
# <a name="what-disk-types-are-available-in-azure"></a>Quais tipos de disco estão disponíveis no Azure?

Atualmente, o Azure Managed disks oferece quatro tipos de disco, cada tipo destina-se a cenários de clientes específicos.

## <a name="disk-comparison"></a>Comparação de discos

A tabela a seguir fornece uma comparação de ultra discos, unidades de estado sólido Premium (SSD), SSD padrão e unidades de disco rígido padrão (HDD) para discos gerenciados para ajudá-lo a decidir o que usar.

| Detalhe | Disco Ultra | SSD Premium | SSD Standard | HDD Standard |
| ------ | ---------- | ----------- | ------------ | ------------ |
|Tipo de disco   |SSD   |SSD   |SSD   |HDD   |
|Cenário   |Cargas de trabalho com uso intensivo de e/s, como [SAP Hana](workloads/sap/hana-vm-operations-storage.md), bancos de dados de camada superior (por exemplo, SQL, Oracle) e outras cargas de trabalho de transações pesadas.   |Cargas de trabalho sensíveis à produção e ao desempenho   |Servidores Web, aplicativos empresariais pouco usados e desenvolvimento/teste   |Backup, não crítico, acesso não frequente   |
|Tamanho máximo do disco   |65.536 GiB (GibiByte)    |32.767 GiB    |32.767 GiB   |32.767 GiB   |
|Taxa de transferência máxima   |2\.000 MB/s    |900 MB/s   |750 MB/s   |500 MB/s   |
|IOPS Máxima   |160.000    |20.000   |6.000   |2.000   |

## <a name="ultra-disk"></a>Disco Ultra

Os discos Ultra do Azure proporcionam uma alta taxa de transferência, alta IOPS e armazenamento em disco de baixa latência e consistente para as VMs de IaaS do Azure. Alguns benefícios adicionais de ultra discos incluem a capacidade de alterar dinamicamente o desempenho do disco, juntamente com suas cargas de trabalho, sem a necessidade de reiniciar suas máquinas virtuais (VM). Os discos Ultra são adequados para cargas de trabalho de grande volume de dados, como SAP HANA, bancos de dados de camada superior e cargas de trabalho de transações pesadas. Os discos Ultra só podem ser usados como discos de dados. É recomendável usar SSDs Premium como discos de sistema operacional.

### <a name="performance"></a>Desempenho

Ao provisionar um disco Ultra, você pode configurar a capacidade e o desempenho do disco de forma independente. Ultra discos vêm em vários tamanhos fixos, variando de 4 GiB até 64 TiB e apresentam um modelo de configuração de desempenho flexível que permite configurar de forma independente a IOPS e a taxa de transferência.

Alguns dos principais recursos dos ultra discos são:

- Capacidade do disco: ultra disks Capacity varia de 4 GiB até 64 TiB.
- IOPS de disco: ultra disks dá suporte a limites de IOPS de 300 IOPS/GiB, até um máximo de 160 K IOPS por disco. Para obter o IOPS que você provisionou, verifique se os IOPS de disco selecionados são menores do que o limite de IOPS de VM. O mínimo de IOPS garantido por disco é 2 IOPS/GiB, com uma linha de base mínima de 100 IOPS. Por exemplo, se você tivesse um ultra GiB de 4 discos, terá um mínimo de 100 IOPS, em vez de oito IOPS.
- Taxa de transferência do disco: com ultra disks, o limite de taxa de transferência de um único disco é 256 KiB/s para cada IOPS provisionado, até um máximo de 2000 MBps por disco (em que MBps = 10 ^ 6 bytes por segundo). A taxa de transferência mínima garantida por disco é 4KiB/s para cada IOPS provisionado, com uma linha de base total mínima de 1 MBps.
- Ultra discos dão suporte ao ajuste dos atributos de desempenho de disco (IOPS e taxa de transferência) em tempo de execução sem desanexar o disco da máquina virtual. Depois que uma operação de redimensionamento de desempenho do disco tiver sido emitida em um disco, poderá levar até uma hora para que a alteração entre em vigor efetivamente. Há um limite de quatro operações de redimensionamento de desempenho durante uma janela de 24 horas. É possível que uma operação de redimensionamento de desempenho falhe devido à falta de capacidade de largura de banda de desempenho.

### <a name="disk-size"></a>Tamanho do disco

|Tamanho do Disco (GiB)  |Limite de IOPS  |Limite de taxa de transferência (MBps)  |
|---------|---------|---------|
|4     |1.200         |300         |
|8     |2.400         |600         |
|16     |4.800         |1.200         |
|32     |9.600         |2.000         |
|64     |19.200         |2\.000         |
|128     |38.400         |2.000         |
|256     |76.800         |2.000         |
|512     |153.600         |2\.000         |
|1.024 a 65.536 (os tamanhos nesse intervalo aumentam em incrementos de 1 TiB)     |160.000         |2.000         |

Ultra discos são projetados para fornecer latências de submilissegundos e IOPS de destino e taxa de transferência descritas na tabela anterior 99,99% do tempo.

### <a name="ga-scope-and-limitations"></a>Limitações e escopo de GA

[!INCLUDE [managed-disks-ultra-disks-GA-scope-and-limitations](../../includes/managed-disks-ultra-disks-GA-scope-and-limitations.md)]


Se você quiser começar a usar o ultra disks, consulte nosso artigo sobre o assunto: [usando os ultra discos do Azure](disks-enable-ultra-ssd.md).

## <a name="premium-ssd"></a>SSD Premium

Os SSDs Premium do Azure oferecem compatibilidade de disco de alto desempenho e baixa latência para VMs (máquinas virtuais) com cargas de trabalho com uso intensivo de E/S (entrada/saída). Para aproveitar a velocidade e o desempenho dos discos de armazenamento Premium, você pode migrar os discos de VM existentes para os SSDs Premium. Os SSDs Premium são adequados para aplicativos de produção críticos. O SSDs Premium só pode ser usado com a série de VMs que são compatíveis com o armazenamento Premium.

Para saber mais sobre os tipos e tamanhos de VM individuais no Azure para Windows ou Linux, incluindo quais tamanhos são compatíveis com o armazenamento Premium, consulte [tamanhos de máquinas virtuais no Azure](sizes.md). Neste artigo, você precisa verificar cada artigo de tamanho de VM individual para determinar se ele é compatível com o armazenamento Premium.

### <a name="disk-size"></a>Tamanho do disco
[!INCLUDE [disk-storage-premium-ssd-sizes](../../includes/disk-storage-premium-ssd-sizes.md)]

Quando você provisiona um disco de armazenamento premium, ao contrário do armazenamento padrão, a capacidade, IOPS e taxa de transferência de disco são garantidos. Por exemplo, se você criar um disco P50, o Azure provisionará uma capacidade de armazenamento de 4.095 GB, 7.500 IOPS e uma taxa de transferência de 250 MB/s para o disco. O aplicativo pode usar a capacidade e o desempenho no todo ou em parte. Os discos SSD Premium são projetados para fornecer as latências baixas de milissegundos de dígito único e a IOPS de destino e a taxa de transferência descritas na tabela anterior 99,9% do tempo.

## <a name="bursting"></a>Bursting

SSD Premium tamanhos menores que p30 agora oferecem intermitência de disco e podem estourar seus IOPS por disco até 3.500 e sua largura de banda de até 170 MB/s. A intermitência é automatizada e opera com base em um sistema de crédito. Os créditos são acumulados automaticamente em um Bucket de intermitência quando o tráfego de disco está abaixo do destino de desempenho provisionado e os créditos são consumidos automaticamente quando o tráfego ultrapassa o destino, até o limite de intermitência máximo. O limite máximo de intermitência define o teto de IOPS de disco & largura de banda, mesmo se você tiver créditos de intermitência a serem consumidos. A intermitência de disco fornece melhor tolerância a alterações imprevisíveis de padrões de e/s. Você pode aproveitá-lo melhor para inicialização de disco do so e aplicativos com tráfego com picos.    

O suporte a intermitência de discos será habilitado em novas implantações de tamanhos de disco aplicáveis por padrão, sem a necessidade de ação do usuário. Para discos existentes dos tamanhos aplicáveis, você pode habilitar a intermitência com uma das duas opções: desanexar e anexar novamente o disco ou parar e reiniciar a VM conectada. Todos os tamanhos de disco aplicáveis de intermitência começarão com um Bucket de crédito de intermitência completa quando o disco for anexado a uma máquina virtual que dá suporte a uma duração máxima no limite de pico de intermitência de 30 minutos. Para saber mais sobre como a intermitência funciona nos discos do Azure, confira [SSD Premium intermitênciaing](./disk-bursting.md). 

### <a name="transactions"></a>Transactions

Para o SSDs Premium, cada operação de e/s menor ou igual a 256 KiB de taxa de transferência é considerada uma única operação de e/s. Operações de e/s maiores que 256 KiB de taxa de transferência são consideradas várias e/SS de tamanho de 256 KiB.

## <a name="standard-ssd"></a>SSD Standard

Os SSDs Standard do Azure são uma opção econômica de armazenamento otimizado para cargas de trabalho que necessitam de desempenho consistente em níveis de IOPS mais baixos. SSD padrão oferece uma experiência de bom nível de entrada para aqueles que desejam migrar para a nuvem, especialmente se passar por problemas com a variação das cargas de trabalho em execução nas soluções de HD no local. Em comparação com HDDs padrão, o SSDs padrão oferece melhor disponibilidade, consistência, confiabilidade e latência. Os SSDs Standard são adequado para servidores Web, servidores de aplicativos de IOPS baixo, aplicativos empresariais pouco usados e cargas de trabalho de Desenvolvimento/Teste. Como HDDs padrão, o SSDs padrão está disponível em todas as VMs do Azure.

### <a name="disk-size"></a>Tamanho do disco
[!INCLUDE [disk-storage-standard-ssd-sizes](../../includes/disk-storage-standard-ssd-sizes.md)]

O SSDs padrão é projetado para fornecer latências de milissegundos de dígito único e a IOPS e a taxa de transferência até os limites descritos na tabela anterior 99% do tempo. O IOPS e a taxa de transferência reais podem variar às vezes, dependendo dos padrões de tráfego. SSDs Padrão fornecerão um desempenho mais consistente do que os discos HD com a menor latência.

### <a name="transactions"></a>Transactions

Para SSDs padrão, cada operação de e/s menor ou igual a 256 KiB de taxa de transferência é considerada uma única operação de e/s. Operações de e/s maiores que 256 KiB de taxa de transferência são consideradas várias e/SS de tamanho de 256 KiB. Essas transações têm um impacto de cobrança.

## <a name="standard-hdd"></a>HDD Standard

Os HDs Standard do Azure oferecem compatibilidade de disco confiável e de baixo custo para VMs que executam cargas de trabalho insensíveis a latência. Com o Armazenamento Standard, os dados são armazenados em HDs (unidades de disco rígido). A latência, o IOPS e a taxa de transferência de discos de HDD Standard podem variar mais amplamente em comparação com discos baseados em SSD. HDD Standard discos são projetados para fornecer latências de gravação em 10 ms e latências de leitura em 20 ms para a maioria das operações de e/s, no entanto, o desempenho real pode variar dependendo do padrão de tamanho de e/s Ao trabalhar com VMs, você pode usar discos HDD padrão para cenários de desenvolvimento/teste e cargas de trabalho menos críticas. Os HDDs padrão estão disponíveis em todas as regiões do Azure e podem ser usados com todas as VMs do Azure.

### <a name="disk-size"></a>Tamanho do disco
[!INCLUDE [disk-storage-standard-hdd-sizes](../../includes/disk-storage-standard-hdd-sizes.md)]

### <a name="transactions"></a>Transactions

Para HDDs padrão, cada operação de e/s é considerada como uma única transação, independentemente do tamanho de e/s. Essas transações têm um impacto de cobrança.

## <a name="billing"></a>Cobrança

Ao usar discos gerenciados, as seguintes considerações de cobrança se aplicam:

- Tipo de disco
- Tamanho do disco gerenciado
- Instantâneos
- Transferências de dados de saída
- Número de transações

**Tamanho do disco gerenciado**: discos gerenciados são cobrados no tamanho provisionado. O Azure mapeia o tamanho provisionado (arredondado) para o tamanho de disco oferecido mais próximo. Para obter detalhes sobre os tamanhos de disco oferecidos, confira as tabelas anteriores. Cada disco é mapeado para um tamanho de disco provisionado compatível e é cobrado de acordo. Por exemplo, se você provisionar uma SSD Standard de 200 GiB, ela será mapeada para a oferta de tamanho de disco E15 (256 GiB). A cobrança por qualquer disco provisionado é rateada por hora usando o preço mensal da oferta de armazenamento. Por exemplo, se você provisionou um disco E10 e o excluiu após 20 horas, será cobrado pela a oferta E10 rateada em 20 horas. Isso é independente da quantidade de dados reais gravados no disco.

**Instantâneos**: os instantâneos são cobrados com base no tamanho usado. Por exemplo, se você criar um instantâneo de um disco gerenciado com capacidade provisionada de 64 GiB e tamanho real de dados usados de 10 GiB, o instantâneo será cobrado apenas pelo tamanho de dados usados de 10 GiB.

Para obter mais informações sobre instantâneos, confira a seção sobre instantâneos [Visão geral de discos gerenciados](managed-disks-overview.md).

**Transferências de dados de saída**: as [transferências de dados de saída](https://azure.microsoft.com/pricing/details/bandwidth/) (dados saindo dos datacenters do Azure) incorrem em cobrança por uso de largura de banda.

**Transações**: você será cobrado pelo número de transações executadas em um disco gerenciado padrão. Para SSDs padrão, cada operação de e/s menor ou igual a 256 KiB de taxa de transferência é considerada uma única operação de e/s. Operações de e/s maiores que 256 KiB de taxa de transferência são consideradas várias e/SS de tamanho de 256 KiB. Para HDDs padrão, cada operação de e/s é considerada como uma única transação, independentemente do tamanho de e/s.

Para obter informações detalhadas sobre os preços de Managed Disks, incluindo os custos de transação, consulte [Managed disks preços](https://azure.microsoft.com/pricing/details/managed-disks).

### <a name="ultra-disk-vm-reservation-fee"></a>Taxa de reserva de VM de ultra Disk

As VMs do Azure têm a capacidade de indicar se são compatíveis com ultra discos. Uma VM compatível com um disco ultra aloca a capacidade de largura de banda dedicada entre a instância de VM de computação e a unidade de escala de armazenamento de bloco para otimizar o desempenho e reduzir a latência. A inclusão desse recurso na VM resulta em uma cobrança de reserva que será imposta somente se você habilitar o recurso de disco ultra na VM sem anexar um disco ultra a ela. Quando um disco ultra é anexado à VM compatível com discos ultra, essa cobrança não é aplicada. Essa cobrança é por vCPU provisionada na VM. 

> [!Note]
> Para [tamanhos de VM de núcleo restritos](constrained-vcpu.md), a taxa de reserva é baseada no número real de vCPUs e não nos núcleos restritos. Para Standard_E32-8s_v3, a taxa de reserva será baseada em núcleos de 32. 

Consulte a [página de preços dos discos do Azure](https://azure.microsoft.com/pricing/details/managed-disks/) para obter detalhes de preços do ultra Disk.

### <a name="azure-disk-reservation"></a>Reserva de disco do Azure

A reserva de disco é a opção de comprar um ano de armazenamento em disco com antecedência a um desconto, reduzindo o custo total. Ao comprar uma reserva de disco, você seleciona um SKU de disco específico em uma região de destino, por exemplo, 10 p30 (1TiB) do SSDs Premium na região leste dos EUA 2 para um termo de um ano. A experiência de reserva é semelhante às instâncias de VM (máquina virtual) reservadas. Você pode agrupar as reservas de VM e disco para maximizar sua economia. Por enquanto, a reserva de discos do Azure oferece um plano de compromisso de um ano para SKUs de SSD Premium de p30 (1TiB) a P80 (32 TiB) em todas as regiões de produção. Para obter mais detalhes sobre os preços dos discos reservados, consulte a [página de preços dos discos do Azure](https://azure.microsoft.com/pricing/details/managed-disks/).

## <a name="next-steps"></a>Próximas etapas

Consulte [Managed disks preços](https://azure.microsoft.com/pricing/details/managed-disks/) para começar.