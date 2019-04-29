---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: virtual-machines
author: zr-msft
ms.service: virtual-machines
ms.topic: include
ms.date: 03/27/2018
ms.author: zarhoads
ms.custom: include file
ms.openlocfilehash: fb296236fb73823690175b12f4e07c05b60cdbcf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60542557"
---
# <a name="regions-and-availability-for-virtual-machines-in-azure"></a>Regiões e disponibilidade para máquinas virtuais do Azure
O Azure opera em vários datacenters no mundo inteiro. Esses datacenters estão agrupados em regiões geográficas, oferecendo a você a flexibilidade de escolher onde compilar seus aplicativos. É importante entender como e onde as VMs (máquinas virtuais) operam no Azure, juntamente com suas opções para maximizar o desempenho, a disponibilidade e a redundância. Este artigo fornece uma visão geral dos recursos de redundância e disponibilidade do Azure.

## <a name="what-are-azure-regions"></a>O que são as regiões do Azure?
Você cria recursos do Azure em regiões definidas, como “Oeste dos EUA”, “Europa Setentrional” ou “Sudeste Asiático”. Você pode ver a [lista de regiões e suas localizações](https://azure.microsoft.com/regions/). Dentro de cada região, há vários datacenters para fornecer redundância e disponibilidade. Essa abordagem oferece flexibilidade para projetar aplicativos para criar VMs mais próximas de seus usuários, bem como para atender quaisquer requisitos legais, de conformidade ou relacionados a impostos.

## <a name="special-azure-regions"></a>Regiões especiais do Azure
O Azure tem algumas regiões especiais que podem ser úteis durante a criação de seus aplicativos para fins de conformidade ou jurídicos. Essas regiões especiais incluem:

* **US Gov Virginia** e **US Gov Iowa**
  * Uma instância lógica e física do Azure isolada da rede, destinada a parceiros e órgãos do governo dos EUA, operada por cidadãos americanos selecionados. Inclui certificações de conformidade adicionais, como [FedRAMP](https://www.microsoft.com/en-us/TrustCenter/Compliance/FedRAMP) e [DISA](https://www.microsoft.com/en-us/TrustCenter/Compliance/DISA). Leia mais sobre o [Azure Governamental](https://azure.microsoft.com/features/gov/).
* **Norte da China** e **Leste da China**
  * Essas regiões estão disponíveis por meio de uma parceria exclusiva entre a Microsoft e a 21Vianet, segundo a qual a Microsoft não mantém diretamente os data centers. Saiba mais sobre o [Microsoft Azure na China](http://www.windowsazure.cn/).
* **Centro da Alemanha** e **Nordeste Alemanha**
  * Essas regiões estão disponíveis por meio de um modelo de objeto de confiança de dados, por meio do qual os dados do cliente continuam na Alemanha sob o controle da T-Systems, uma empresa da Deutsche Telekom, que atua como o objeto de confiança dos dados da Alemanha.

## <a name="region-pairs"></a>Pares de regiões
Cada região do Azure é emparelhada com outra região na mesma área geográfica (como EUA, Europa ou Ásia). Essa abordagem permite a replicação de recursos, como o armazenamento de VM, em uma região geográfica, o que deve reduzir a probabilidade de desastres naturais, conflitos civis, quedas de energia ou interrupções de rede física afetarem as duas regiões ao mesmo tempo. Vantagens adicionais dos pares de regiões incluem:

* No caso de uma interrupção mais ampla do Azure, é priorizada uma região de cada par para ajudar a reduzir o tempo de restauração dos aplicativos. 
* As atualizações planejadas do Azure são distribuídas para regiões emparelhadas uma por vez, de modo a minimizar o tempo de inatividade e o risco de interrupção dos aplicativos.
* Os dados continuam residindo na mesma região geográfica que seu par (com exceção do Sul do Brasil) para fins de jurisdição fiscal e aplicação da lei.

Exemplos de pares de regiões incluem:

| Primário | Secundário |
|:--- |:--- |
| Oeste dos EUA |Leste dos EUA |
| Norte da Europa |Europa Ocidental |
| Sudeste Asiático |Ásia Oriental |

Você pode ver a [lista completa de pares de regiões aqui](../articles/best-practices-availability-paired-regions.md#what-are-paired-regions).

## <a name="feature-availability"></a>Disponibilidade de recursos
Alguns serviços ou recursos de VM estão disponíveis somente em determinadas regiões, como tamanhos específicos de VMs ou tipos de armazenamento. Também há alguns serviços globais do Azure que não exigem que você selecione uma região específica, como [Azure Active Directory](../articles/active-directory/fundamentals/active-directory-whatis.md), [Gerenciador de Tráfego](../articles/traffic-manager/traffic-manager-overview.md) ou [DNS do Azure](../articles/dns/dns-overview.md). Para ajudá-lo na criação de seu ambiente de aplicativos, você pode verificar a [disponibilidade de serviços do Azure em cada região](https://azure.microsoft.com/regions/#services). Você também pode [consultar programaticamente os tamanhos de VM com suporte e restrições em cada região](../articles/azure-resource-manager/resource-manager-sku-not-available-errors.md).

## <a name="storage-availability"></a>Disponibilidade de armazenamento
Compreender as áreas e regiões do Azure torna-se importante quando você considera as opções de replicação de armazenamento disponíveis. Dependendo do tipo de armazenamento, você tem opções de replicação diferentes.

**Azure Managed Disks**
* Armazenamento com redundância local (LRS)
  * Replica seus dados três vezes dentro da região em que você criou sua conta de armazenamento.

**Discos baseados em contas de armazenamento**
* Armazenamento com redundância local (LRS)
  * Replica seus dados três vezes dentro da região em que você criou sua conta de armazenamento.
* ZRS (Armazenamento com redundância de zona)
  * Replica seus dados três vezes para duas ou três instalações, em uma única região ou em duas regiões.
* Armazenamento com redundância geográfica (GRS)
  * Replica seus dados para uma região secundária a centenas de quilômetros da região primária.
* Armazenamento com redundância geográfica com acesso de leitura (RA-GRS)
  * Replica os dados para uma região secundária, assim como acontece com o GRS, mas também fornece acesso somente leitura aos dados na localização secundária.

A tabela a seguir oferece uma visão geral das diferenças entre os tipos de replicação de armazenamento:

| Estratégia de replicação | LRS | ZRS | GRS | RA-GRS |
|:--- |:--- |:--- |:--- |:--- |
| Os dados são replicados entre várias instalações. |Não  |sim |sim |Sim |
| Os dados podem ser lidos do local secundário e do local primário. |Não  |Não |Não  |Sim |
| Número de cópias de dados mantidas em nós separados. |3 |3 |6 |6 |

Você pode ler mais sobre as [Opções de replicação de armazenamento do Azure aqui](../articles/storage/common/storage-redundancy.md). Para saber mais sobre discos gerenciados, veja [Visão geral dos Azure Managed Disks](../articles/virtual-machines/windows/managed-disks-overview.md).

### <a name="storage-costs"></a>Custos de armazenamento
Os preços variam dependendo do tipo de armazenamento e da disponibilidade que você selecionar.

**Azure Managed Disks**
* Os Managed Disks Premium são compostos por unidades de estado sólido (SSDs) e os Managed Disks Standard são compostos por discos de rotação regular. Premium e padrão de discos gerenciados são cobrados com base na capacidade provisionada para o disco.

**Discos não gerenciados**
* O armazenamento Premium é composto por unidades de estado sólido (SSDs) e é cobrado com base na capacidade do disco.
* O armazenamento padrão é feito em discos giratórios comuns e é cobrado com base na capacidade utilizada e na disponibilidade de armazenamento desejada.
  * Para o RA-GRS, há uma cobrança adicional de Transferência de Dados com Replicação Geográfica pela largura de banda para a replicação desses dados em outra região do Azure.

Consulte [Preços do Armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/) para obter informações sobre preços para diferentes opções de tipos de armazenamento e de disponibilidade.

## <a name="availability-sets"></a>Conjuntos de disponibilidade
Um conjunto de disponibilidade é um agrupamento lógico de VMs em um datacenter que permite que o Azure entenda como o seu aplicativo foi criado para fornecer redundância e disponibilidade. Recomenda-se que duas ou mais VMs sejam criadas dentro de um conjunto de disponibilidade para fornecer um aplicativo altamente disponível e para atender o [SLA de 99,95% do Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/). Não há nenhum custo para o conjunto de disponibilidade em si, você paga apenas por cada instância de VM que criar. Quando uma única VM está usando [SSDs premium do Azure](../articles/virtual-machines/windows/disks-types.md#premium-ssd), o SLA do Azure se aplica a eventos de manutenção não planejada.

Um conjunto de disponibilidade é composto por dois agrupamentos adicionais que protegem contra falhas de hardware e permitem que atualizações sejam aplicadas com segurança – FDs (domínios de falha) e UDs (domínios de atualização). Você pode ler mais sobre como gerenciar a disponibilidade de [VMs Linux](../articles/virtual-machines/linux/manage-availability.md) ou [VMs Windows](../articles/virtual-machines/windows/manage-availability.md).

### <a name="fault-domains"></a>Domínios de falha
Um domínio de falha é um grupo lógico de hardwares subjacentes que compartilham a mesma fonte de alimentação e o mesmo comutador de rede, de forma semelhante a um rack em um datacenter local. À medida que você cria máquinas virtuais em um conjunto de disponibilidade, a plataforma Windows Azure distribui automaticamente suas VMs entre esses domínios de falha. Essa abordagem limita o impacto de possíveis falhas de hardware físico, interrupções de rede ou interrupções de energia.

### <a name="update-domains"></a>Atualizar domínios
Um domínio de atualização é um grupo lógico de hardwares subjacentes que podem passar por manutenção ou ser reinicializados ao mesmo tempo. À medida que você cria máquinas virtuais em um conjunto de disponibilidade, a plataforma Windows Azure distribui automaticamente suas VMs entre esses domínios de atualização. Essa abordagem garante que pelo menos uma instância do aplicativo sempre permaneça em execução enquanto a plataforma Windows Azure passar por manutenção periódica. A ordem de reinicialização dos domínios de atualização pode não ser sequencial durante a manutenção planejada, mas apenas um domínio de atualização é reinicializado por vez.

![Conjuntos de disponibilidade](./media/virtual-machines-common-manage-availability/ud-fd-configuration.png)

### <a name="managed-disk-fault-domains"></a>Domínios de falha de Disco Gerenciado
Para VMs que usam [Azure Managed Disks](../articles/virtual-machines/windows/faq-for-disks.md), as VMs são alinhadas aos domínios de falha de disco gerenciado quando usam um conjunto de disponibilidade gerenciada. Esse alinhamento garante que todos os discos gerenciados anexados a uma VM fiquem no mesmo domínio de falha de disco gerenciado. Somente as VMs com discos gerenciados podem ser criadas em um conjunto de disponibilidade gerenciado. O número de domínios de falha de disco gerenciado varia por região - dois ou três domínios de falha de disco gerenciados por região. Você pode ler mais sobre esses gerenciados domínios de falha de disco para [VMs do Linux](../articles/virtual-machines/linux/manage-availability.md?#use-managed-disks-for-vms-in-an-availability-set) ou [VMs do Windows](../articles/virtual-machines/windows/manage-availability.md?#use-managed-disks-for-vms-in-an-availability-set).

![Conjunto de disponibilidade gerenciado](./media/virtual-machines-common-manage-availability/md-fd-updated.png)

## <a name="availability-zones"></a>Zonas de disponibilidade

[Zonas de disponibilidade](../articles/availability-zones/az-overview.md), uma alternativa para conjuntos de disponibilidade, expandem o nível de controle de que você precisa para manter a disponibilidade dos aplicativos e dos dados em suas VMs. Uma Zona de Disponibilidade é uma zona fisicamente separada em uma região do Azure. Há três Zonas de Disponibilidade por região do Azure com suporte. Cada zona de disponibilidade tem uma rede, resfriamento e fonte de energia distintos. Ao arquitetar suas soluções para usar VMs replicadas em zonas, você pode proteger seus aplicativos e seus dados contra a perda de um data center. Se uma zona for comprometida, os aplicativos e os dados replicados ficarão instantaneamente disponíveis em outra zona. 

![Zonas de disponibilidade](./media/virtual-machines-common-regions-and-availability/three-zones-per-region.png)

Saiba mais sobre como implantar uma VM do [Windows](../articles/virtual-machines/windows/create-powershell-availability-zone.md) ou do [Linux](../articles/virtual-machines/linux/create-cli-availability-zone.md) em uma Zona de Disponibilidade.

## <a name="next-steps"></a>Próximas etapas
Agora você pode começar a usar esses recursos de redundância e disponibilidade para criar seu ambiente do Azure. Para obter informações de práticas recomendadas, confira [Práticas recomendadas de disponibilidade do Azure](../articles/best-practices-availability-checklist.md).

