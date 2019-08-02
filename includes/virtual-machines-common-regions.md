---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 05/28/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 6cd721fdd7841df279bbe24cab1057ea5e16e0e2
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2019
ms.locfileid: "67850464"
---
# <a name="regions-for-virtual-machines-in-azure"></a>Regiões para máquinas virtuais no Azure
É importante entender como e onde as VMs (máquinas virtuais) operam no Azure, juntamente com suas opções para maximizar o desempenho, a disponibilidade e a redundância. Este artigo fornece uma visão geral dos recursos de redundância e disponibilidade do Azure.


## <a name="what-are-azure-regions"></a>O que são as regiões do Azure?
O Azure opera em vários datacenters no mundo inteiro. Esses datacenters estão agrupados em regiões geográficas, oferecendo a você a flexibilidade de escolher onde compilar seus aplicativos. 

Você cria recursos do Azure em regiões definidas, como “Oeste dos EUA”, “Europa Setentrional” ou “Sudeste Asiático”. Você pode ver a [lista de regiões e suas localizações](https://azure.microsoft.com/regions/). Dentro de cada região, há vários datacenters para fornecer redundância e disponibilidade. Essa abordagem oferece flexibilidade para projetar aplicativos para criar VMs mais próximas de seus usuários, bem como para atender quaisquer requisitos legais, de conformidade ou relacionados a impostos.

## <a name="special-azure-regions"></a>Regiões especiais do Azure
O Azure tem algumas regiões especiais que podem ser úteis durante a criação de seus aplicativos para fins de conformidade ou jurídicos. Essas regiões especiais incluem:

* **US Gov Virginia** e **US Gov Iowa**
  * Uma instância lógica e física do Azure isolada da rede, destinada a parceiros e órgãos do governo dos EUA, operada por cidadãos americanos selecionados. Inclui certificações de conformidade adicionais, como [FedRAMP](https://www.microsoft.com/en-us/TrustCenter/Compliance/FedRAMP) e [DISA](https://www.microsoft.com/en-us/TrustCenter/Compliance/DISA). Leia mais sobre o [Azure Governamental](https://azure.microsoft.com/features/gov/).
* **Norte da China** e **Leste da China**
  * Essas regiões estão disponíveis por meio de uma parceria exclusiva entre a Microsoft e a 21Vianet, segundo a qual a Microsoft não mantém diretamente os data centers. Veja mais sobre o [Azure China 21vianet](http://www.windowsazure.cn/).
* **Centro da Alemanha** e **Nordeste Alemanha**
  * Essas regiões estão disponíveis por meio de um modelo de objeto de confiança de dados, por meio do qual os dados do cliente continuam na Alemanha sob o controle da T-Systems, uma empresa da Deutsche Telekom, que atua como o objeto de confiança dos dados da Alemanha.

## <a name="region-pairs"></a>Pares de regiões
Cada região do Azure é emparelhada com outra região na mesma área geográfica (como EUA, Europa ou Ásia). Essa abordagem permite a replicação de recursos, como o armazenamento de VM, em uma região geográfica, o que deve reduzir a probabilidade de desastres naturais, conflitos civis, quedas de energia ou interrupções de rede física afetarem as duas regiões ao mesmo tempo. Vantagens adicionais dos pares de regiões incluem:

* No caso de uma interrupção mais ampla do Azure, é priorizada uma região de cada par para ajudar a reduzir o tempo de restauração dos aplicativos. 
* As atualizações planejadas do Azure são distribuídas para regiões emparelhadas uma por vez, de modo a minimizar o tempo de inatividade e o risco de interrupção dos aplicativos.
* Os dados continuam residindo na mesma região geográfica que seu par (com exceção do Sul do Brasil) para fins de jurisdição fiscal e aplicação da lei.

Exemplos de pares de regiões incluem:

| Primária | Secundário |
|:--- |:--- |
| Oeste dos EUA |East US |
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
| Os dados são replicados entre várias instalações. |Não |Sim |Sim |Sim |
| Os dados podem ser lidos do local secundário e do local primário. |Não |Não |Não |Sim |
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

