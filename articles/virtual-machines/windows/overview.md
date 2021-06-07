---
title: Visão geral das VMs do Windows no Azure
description: Visão geral das máquinas virtuais do Windows no Azure.
author: cynthn
ms.service: virtual-machines
ms.collection: windows
ms.workload: infrastructure-services
ms.topic: overview
ms.date: 11/14/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 07562641ef866074dcf14cb01afd4d7ce071e467
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107310998"
---
# <a name="windows-virtual-machines-in-azure"></a>Máquinas virtuais do Windows no Azure

VM (Máquinas Virtuais) do Azure é um dos vários tipos de [recursos de computação sob demanda escalonáveis](/azure/architecture/guide/technology-choices/compute-decision-tree) oferecidos pelo Azure. Normalmente, você escolhe uma VM quando precisar de mais controle sobre o ambiente de computação do que as outras opções oferecem. Este artigo fornece informações sobre o que você deve considerar antes de criar uma VM, como criá-la e como gerenciá-la.

Uma VM do Azure oferece a flexibilidade da virtualização sem a necessidade de comprar e manter o hardware físico que a executa. No entanto, você ainda precisa manter a VM executando tarefas, como configurar, corrigir e instalar o software que será executado nela.

Máquinas virtuais do Azure podem ser usadas de várias maneiras. Alguns exemplos são:

* **Desenvolvimento e teste** – as VMs do Azure oferecem uma rápida e maneira fácil de criar um computador com configurações específicas, necessárias para codificar e testar um aplicativo.
* **Aplicativos na nuvem** – como a demanda por seu aplicativo pode flutuar, pode fazer sentido, em termos econômicos, executá-lo em uma VM no Azure. Você paga por VMs extras quando precisa delas e as desliga quando não são necessárias.
* **Datacenter estendido** – máquinas virtuais em uma rede virtual do Azure podem ser facilmente conectadas à rede de sua organização.

O número de VMs que o aplicativo usa pode ser escalado verticalmente e horizontalmente para atender às suas necessidades.

## <a name="what-do-i-need-to-think-about-before-creating-a-vm"></a>O que é necessário pensar antes de criar uma VM?
Sempre há uma infinidade de [considerações de design](/azure/architecture/reference-architectures/n-tier/windows-vm) quando você cria uma infraestrutura de aplicativo no Azure. Estes aspectos de uma VM são importantes a considerar antes de começar:

* Os nomes dos recursos do aplicativo
* O local onde os recursos são armazenados
* O tamanho da VM
* O número máximo de VMs que podem ser criadas
* O sistema operacional que a VM executa
* A configuração da VM após ela ser iniciada
* Os recursos relacionados dos quais a VM precisa

### <a name="locations"></a>Locais
Todos os recursos criados no Azure são distribuídos entre várias [regiões geográficas](https://azure.microsoft.com/regions/) em todo o mundo. Normalmente, a região é chamada **local** quando você cria uma VM. Para uma VM, a localização especifica onde os discos rígidos virtuais são armazenados.

Esta tabela mostra algumas das maneiras de obter uma lista dos locais disponíveis.

| Método | Descrição |
| --- | --- |
| Portal do Azure |Selecione um local na lista quando você criar uma VM. |
| Azure PowerShell |Use o comando [Get-AzLocation](/powershell/module/az.resources/get-azlocation). |
| API REST |Use a operação [Listar locais](/rest/api/resources/subscriptions/listlocations). |
| CLI do Azure |Use a operação [az account list-locations](/cli/azure/account). |

## <a name="availability"></a>Disponibilidade
O Azure anunciou um Contrato de Nível de Serviço de máquina virtual de única instância de 99,9%, o melhor que há no mercado, desde que você implante a VM com armazenamento premium para todos os discos.  Para sua implantação se qualificar para o Contrato de Nível de Serviço de 99,95% padrão de VM, você ainda precisará implantar duas ou mais VMs que executem sua carga de trabalho dentro de um conjunto de disponibilidade. Um conjunto de disponibilidade garante que suas VMs sejam distribuídas entre vários domínios de falha nos datacenters do Azure, além de serem implantadas em hosts com janelas de manutenção diferentes. O [SLA completo do Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) explica a disponibilidade garantida do Azure como um todo.


## <a name="vm-size"></a>Tamanho da VM
O [tamanho](../sizes.md) da VM que você usa é determinado pela carga de trabalho que deseja executar. O tamanho que você escolhe, em seguida, determina fatores como capacidade de processamento, memória e armazenamento. O Azure oferece uma grande variedade de tamanhos para oferecer suporte a muitos tipos de usos.

O Azure cobra um [preço por hora](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) com base no tamanho da VM e do sistema operacional. Para horas parciais, o Azure cobrará somente os minutos usados. O armazenamento terá o preço e será cobrado separadamente.

## <a name="vm-limits"></a>Limites de VM
Sua assinatura do Azure tem [limites de cota](../../azure-resource-manager/management/azure-subscription-service-limits.md) padrão que podem afetar a implantação de muitas VMs para seu projeto. O limite atual por assinatura é de 20 VMs por região. Os limites podem ser aumentados [pelo preenchimento de um tíquete de suporte para solicitar um aumento](../../azure-portal/supportability/resource-manager-core-quotas-request.md)

### <a name="operating-system-disks-and-images"></a>Imagens e discos de sistema operacional
As máquinas virtuais usam [VHDs (discos rígidos virtuais)](../managed-disks-overview.md) para armazenar seus dados e sistema operacional (SO). Os VHDs também são usados para as imagens que você pode optar por instalar um sistema operacional. 

O Azure fornece muitas [imagens do marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?filters=virtual-machine-images%3Bwindows&page=1) para usar com várias versões e tipos de sistemas operacionais Windows Server. As imagens do Marketplace são identificadas por editor de imagem, oferta, sku e versão (normalmente, a versão é especificada como a versão mais recente). Há suporte somente para sistemas operacionais de 64 bits. Para saber mais informações sobre os sistemas operacionais convidados, funções e recursos com suporte, consulte [Suporte de software para servidores Microsoft para máquinas virtuais do Microsoft Azure](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines).

Esta tabela mostra algumas maneiras de encontrar as informações de uma imagem.

| Método | Descrição |
| --- | --- |
| Portal do Azure |Os valores são especificados automaticamente quando você seleciona uma imagem a ser usada. |
| Azure PowerShell |[Get-AzVMImagePublisher](/powershell/module/az.compute/get-azvmimagepublisher) -Location *location*<BR>[Get-AzVMImageOffer](/powershell/module/az.compute/get-azvmimageoffer) -Location *location* -Publisher *publisherName*<BR>[Get-AzVMImageSku](/powershell/module/az.compute/get-azvmimagesku) -Location *location* -Publisher *publisherName* -Offer *offerName* |
| APIs REST |[Listar editores de imagem](/rest/api/compute/platformimages/platformimages-list-publishers)<BR>[Listar ofertas de imagem](/rest/api/compute/platformimages/platformimages-list-publisher-offers)<BR>[Listar skus de imagem](/rest/api/compute/platformimages/platformimages-list-publisher-offer-skus) |
| CLI do Azure |[az vm image list-publishers](/cli/azure/vm/image) --location *location*<BR>[az vm image list-offers](/cli/azure/vm/image) --location *location* --publisher *publisherName*<BR>[az vm image list-skus](/cli/azure/vm) --location *location* --publisher *publisherName* --offer *offerName*|

Você pode optar por [carregar e usar sua própria imagem](upload-generalized-managed.md) e, quando faz isso, o nome do editor, da oferta e da sku não são usados.

### <a name="extensions"></a>Extensões
As [extensões](../extensions/features-windows.md?toc=/azure/virtual-machines/windows/toc.json) de VM dão à VM recursos adicionais por meio de configuração pós-implantação e tarefas automatizadas.

Estas tarefas comuns podem ser realizadas usando extensões:

* **Executar scripts personalizados** – a [Extensão de Script Personalizado](../extensions/custom-script-windows.md?toc=/azure/virtual-machines/windows/toc.json) o ajuda a configurar as cargas de trabalho na VM executando o script quando a VM é provisionada.
* **Implantar e gerenciar configurações** – a [Extensão de Configuração de Estado Desejado (DSC) do PowerShell](../extensions/dsc-overview.md?toc=/azure/virtual-machines/windows/toc.json) ajuda a configurar o DSC em uma VM para gerenciar configurações e ambientes.
* **Coletar dados de diagnóstico** – a [Extensão de Diagnóstico do Azure](../extensions/diagnostics-template.md?toc=/azure/virtual-machines/windows/toc.json) o ajuda a configurar a VM para coletar dados de diagnóstico que podem ser usados para monitorar a integridade do aplicativo.

### <a name="related-resources"></a>Recursos relacionados
Os recursos nesta tabela são usados por VM e precisam existir ou ser criados quando a VM é criada.

| Recurso | Obrigatório | Descrição |
| --- | --- | --- |
| [Grupo de recursos](../../azure-resource-manager/management/overview.md) |Sim |A VM deve estar contida em um grupo de recursos. |
| [Conta de armazenamento](../../storage/common/storage-account-create.md) |Sim |A VM precisa da conta de armazenamento para armazenar seus discos rígidos virtuais. |
| [Rede virtual](../../virtual-network/virtual-networks-overview.md) |Sim |A VM deve ser membro de uma rede virtual. |
| [Endereço IP público](../../virtual-network/public-ip-addresses.md) |Não |A VM pode ter um endereço IP público atribuído a ela para acessá-la remotamente. |
| [Interface de rede](../../virtual-network/virtual-network-network-interface.md) |Sim |A VM precisa de interface de rede para se comunicar na rede. |
| [Discos de dados](attach-managed-disk-portal.md) |Não |A VM pode incluir discos de dados para expandir os recursos de armazenamento. |


## <a name="data-residency"></a>Residência de dadosResidência de dados

No Azure, o recurso para habilitar o armazenamento de dados do cliente em apenas uma região está disponível atualmente apenas na região do Sudeste da Ásia (Singapura), na área geográfica do Pacífico Asiático, e na região Sul do Brasil (Estado de São Paulo), na área geográfica do Brasil. Para todas as outras regiões, os dados do cliente são armazenados na Área geográfica. Para obter mais informações, confira [Central de Confiabilidade](https://azure.microsoft.com/global-infrastructure/data-residency/).


## <a name="next-steps"></a>Próximas etapas

Crie sua primeira VM!

- [Portal](quick-create-portal.md)
- [PowerShell](quick-create-powershell.md)
- [CLI do Azure](quick-create-cli.md)
