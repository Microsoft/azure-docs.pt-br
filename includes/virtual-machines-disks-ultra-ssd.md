---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 09/24/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 4b5d2de2e9ccd44517e083a435e127bd5678f002
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60564335"
---
# <a name="ultra-disks-preview-managed-disks-for-azure-virtual-machine-workloads"></a>Discos Ultra (visualização) discos gerenciados para cargas de trabalho de máquina virtual do Azure

Discos ultra do Azure (visualização) fornece alta taxa de transferência, IOPS alta e armazenamento de disco de baixa latência consistente para VMs IaaS do Azure. Essa nova oferta fornece o melhor em desempenho de linha, nos mesmos níveis de disponibilidade que nossas ofertas de discos atuais. Benefícios adicionais de discos ultra incluem a capacidade de alterar dinamicamente o desempenho do disco, juntamente com suas cargas de trabalho sem a necessidade de reiniciar as máquinas virtuais. Ultra discos são adequados para cargas de trabalho de grande volume de dados, como SAP HANA, bancos de dados de camada superior e cargas de trabalho pesadas de transação.

## <a name="ultra-disk-features"></a>Recursos de disco Ultra

**Discos gerenciados**: Discos Ultra só estão disponíveis como discos gerenciados. Discos Ultra não podem ser implantados como um disco não gerenciado ou um blob de páginas. Durante a criação de um disco gerenciado, você pode especificar o sku de disco digite como UltraSSD_LRS e indicar o tamanho do disco, o IOPS e taxa de transferência que você precisa e o Azure cria e gerencia o disco para você.  

**Máquinas Virtuais**: Ultra discos são projetados para funcionar com todos os Premium SSD SKUs de máquina Virtual do Azure habilitado; No entanto, como ele está atualmente em visualização, as VMs são dimensionadas como v3 de ES/DS.

**Configuração de desempenho dinâmico**: Discos Ultra permitem que você altere dinamicamente o desempenho (IOPS e taxa de transferência) do disco, juntamente com suas necessidades de carga de trabalho sem precisar reiniciar as máquinas virtuais.

## <a name="scalability-and-performance-targets"></a>Escalabilidade e metas de desempenho

Quando você provisiona um ultra discos, você terá a opção de forma independente, configurar a capacidade e o desempenho do disco. Discos Ultra vêm em vários tamanhos fixos de 4 GiB até 64 TiB e um modelo de configuração de desempenho flexível que permite que você configure, de modo independente, IOPS e taxa de transferência de recursos. Ultra discos somente podem ser usados como discos de dados. É recomendável usar SSDs Premium como discos de sistema operacional.

Alguns recursos principais de discos ultra são:

- Capacidade do disco: Discos Ultra oferece uma variedade de tamanhos de disco diferente de 4 GiB até 64 TiB.
- IOPS do disco: Discos Ultra dão suporte a limites de IOPS de 300 IOPS/GiB, até um máximo de 160 mil IOPS por disco. Para alcançar o IOPS que você provisionou, certifique-se de que o IOPS do disco selecionado seja menor que o IOPS da VM. O IOPS de disco mínimo é de 100 IOPS.
- Taxa de transferência do disco: Com discos ultra, o limite de taxa de transferência de um único disco é 256 KiB/s para cada um provisionado IOPS, até um máximo de 2000 MBps por disco (em que MBps = 10 ^ 6 Bytes por segundo). A taxa de transferência mínima de disco é de 1 MiB.

A tabela a seguir resume as diferentes configurações compatíveis com os diferentes tamanhos de disco:  

### <a name="ultra-disks-managed-disk-offerings"></a>Ultra discos gerenciados ofertas de disco

|Tamanho do Disco (GiB)  |Limites de IOPS  |Limite de taxa de transferência (MBps)  |
|---------|---------|---------|
|4     |1.200         |300         |
|8     |2.400         |600         |
|16     |4.800         |1.200         |
|32     |9.600         |2.000         |
|64     |19.200         |2.000         |
|128     |38.400         |2.000         |
|256     |76.800         |2.000         |
|512     |80.000         |2.000         |
|1.024 a 65.536 (os tamanhos nesse intervalo aumentam em incrementos de 1 TiB)     |160.000         |2.000         |

## <a name="pricing-and-billing"></a>Preços e cobrança

Ao usar discos ultra, serão aplicadas as seguintes considerações de cobrança:

- Tamanho do Disco Gerenciado
- IOPS do Disco Gerenciado provisionado
- Taxa de transferência do Disco Gerenciado Provisionado
- Taxa de reserva de VM de disco Ultra

### <a name="managed-disk-size"></a>Tamanho do disco gerenciado

Os discos gerenciados são cobrados conforme os tamanhos de VM que você escolhe ao provisionar uma nova VM do Azure. O Azure mapeia o tamanho provisionado (arredondado) para a opção de tamanho de disco mais próxima. Para obter detalhes sobre os tamanhos de disco oferecidos, consulte a tabela na seção de Escalabilidade e Metas de Desempenho acima. Cada disco é mapeado para um tamanho de disco provisionado com suporte e será cobrado de acordo com uma hora por hora. Por exemplo, se você provisionou um disco de ultra GiB 200 e ele foi excluído após 20 horas, ele será mapeado para a oferta de tamanho de disco de 256 GiB e você será cobrado por 256 GiB para 20 horas. Essa cobrança foi baseada no consumo de horas de computação, independentemente do volume de dados realmente gravado no disco.

### <a name="managed-disk-provisioned-iops"></a>IOPS provisionado do disco gerenciado

IOPS é o número de solicitações que o aplicativo está enviando aos discos por segundo. Uma operação de entrada/saída pode ser leitura ou gravação sequencial ou leitura ou gravação aleatória. Com base no tamanho do disco ou no número de discos conectados à VM, o número médio de IOPS será cobrado por hora. Para obter detalhes sobre o IOPS de disco oferecido, consulte a tabela na seção de Escalabilidade e Metas de Desempenho acima.

### <a name="managed-disk-provisioned-throughput"></a>Taxa de transferência provisionada do disco de gerenciado

A Taxa de Transferência é o volume de dados que o aplicativo está enviando aos discos em um intervalo especificado, medida em bytes/segundo. Se o aplicativo estiver executando grandes operações de entrada/saída, ele exigirá taxa de transferência alta.  

Há uma relação entre a Taxa de Transferência e IOPS, como mostrado na fórmula a seguir:  IOPS x Tamanho E/S = Taxa de Transferência

Portanto, é importante determinar os valores ideais de Taxa de Transferência e IOPS que o aplicativo exige. Ao tentar otimizar um deles, o outro também é afetado. É recomendável iniciar com uma taxa de transferência correspondente ao tamanho de E/S de 16 KiB, ajustando se houver necessidade de mais taxa de transferência.

Para obter detalhes sobre a taxa de transferência de disco com suporte nos discos ultra, consulte a tabela na seção de escalabilidade e metas de desempenho acima. Como o tamanho de disco e IOPS, a taxa de transferência provisionada é cobrada por hora por MBps provisionado.

### <a name="ultra-disk-vm-reservation-fee"></a>Taxa de reserva de VM de disco Ultra

Estamos introduzindo uma funcionalidade na VM que indica que sua VM é ultra disco compatível. Uma VM compatível com um disco ultra aloca a capacidade de largura de banda dedicada entre a instância de VM de computação e a unidade de escala de armazenamento de bloco para otimizar o desempenho e reduzir a latência. A inclusão desse recurso na VM resulta em uma cobrança de reserva que será imposta somente se você habilitar o recurso de disco ultra na VM sem anexar um disco ultra a ela. Quando um disco ultra é anexado à VM ultra compatível, a cobrança não poderá ser aplicada. Essa cobrança é por vCPU provisionada na VM.

Consulte a [página de preços do Azure Disks](https://azure.microsoft.com/pricing/details/managed-disks/) para os novo ultra discos detalhes de preço disponíveis em versão prévia limitada.

### <a name="ultra-disk-preview-scope-and-limitations"></a>Limitações e o escopo de visualização de disco ultra

Na visualização, ultra discos:

- Terão suporte inicialmente no Leste dos EUA 2 em uma única zona de disponibilidade  
- Só pode ser usado com zonas de disponibilidade (conjuntos de disponibilidade e implantações de VM única fora será zonas que não têm a capacidade de anexar um disco ultra)
- Serão compatíveis somente com VMs ES/DS v3
- Só estarão disponíveis como discos de dados e serão compatíveis somente com tamanho de setor físico de 4k  
- Só poderão ser criados como discos vazios  
- Atualmente, só podem ser implantados usando o SDK do Python, a CLI e os modelos do Resource Manager.
- Não serão compatíveis (ainda) com instantâneos de disco, Imagens de VM, Conjuntos de disponibilidade, Conjuntos de Dimensionamento de Máquinas Virtuais nem com o Azure Disk Encryption.
- Não serão compatíveis (ainda) com a integração com o Backup do Azure nem com o Azure Site Recovery.
- Assim como acontece com a  [maioria das versões prévias](https://azure.microsoft.com/support/legal/preview-supplemental-terms/), esse recurso não deve ser usado para cargas de trabalho de produção até a GA (disponibilidade geral).
