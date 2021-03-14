---
title: Soluções de computação confidencial do Azure em máquinas virtuais
description: Saiba mais sobre as soluções de computação confidencial do Azure em máquinas virtuais.
author: JBCook
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: JenCook
ms.openlocfilehash: 8621dc8cfc10ab44ecb358a40fdae1a1b2081734
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102566576"
---
# <a name="solutions-on-azure-virtual-machines"></a>Soluções em máquinas virtuais do Azure

Este artigo aborda informações sobre a implantação de VMs (máquinas virtuais) de computação confidencial do Azure que executam processadores apoiados pelo [Intel Software Guard Extension](https://software.intel.com/sgx) (SGX da Intel). 

## <a name="azure-confidential-computing-vm-sizes"></a>Tamanhos de VM de computação confidencial do Azure

As máquinas virtuais de computação confidencial do Azure são projetadas para proteger a confidencialidade e a integridade de seus dados e códigos enquanto são processados na nuvem 

As VMs da [série DCsv2](../virtual-machines/dcv2-series.md) são a família de tamanho de computação confidencial mais recente e atual. Essas VMs dão suporte a uma grande variedade de recursos de implantação, têm duas vezes mais EPC (cache de página enclave) e uma seleção maior de tamanhos quando comparados a nossas VMs da série DC. No momento, as VMs da [série DC](../virtual-machines/sizes-previous-gen.md#preview-dc-series) estão na versão prévia e serão descontinuadas e não disponibilizadas para o público geral.

Inicie a implantação de uma VM da série DCsv2 por meio do marketplace comercial da Microsoft seguindo o [tutorial de início rápido](quick-create-marketplace.md).

### <a name="current-available-sizes-and-regions"></a>Tamanhos e regiões disponíveis no momento

Para obter uma lista de todos os tamanhos de VMs de computação confidencial disponíveis para o público em regiões e zonas disponíveis, execute o seguinte comando na [CLI do Azure](/cli/azure/install-azure-cli-windows):

```azurecli-interactive
az vm list-skus `
    --size dc `
    --query "[?family=='standardDCSv2Family'].{name:name,locations:locationInfo[0].location,AZ_a:locationInfo[0].zones[0],AZ_b:locationInfo[0].zones[1],AZ_c:locationInfo[0].zones[2]}" `
    --all `
    --output table
```

Para ter uma visão mais detalhada dos tamanhos acima, execute o seguinte comando:

```azurecli-interactive
az vm list-skus `
    --size dc `
    --query "[?family=='standardDCSv2Family']"
```
### <a name="dedicated-host-requirements"></a>Requisitos de host dedicado
A implantação de um tamanho de máquina virtual **Standard_DC8_v2** na família de VM DCSv2-Series ocupará o host completo e não será compartilhada com outros locatários ou assinaturas. Essa família de SKU de VM fornece o isolamento que você pode precisar para atender aos requisitos regulatórios de conformidade e segurança que normalmente são atendidos por ter um serviço de host dedicado. Quando você escolher **Standard_DC8_v2** SKU, o servidor host físico alocará todos os recursos de hardware disponíveis, incluindo a memória EPC somente para sua máquina virtual. Observe que essa funcionalidade existe por design de infraestrutura e todos os recursos do **Standard_DC8_v2** terão suporte. Essa implantação não é igual ao serviço de [host dedicado do Azure](../virtual-machines/dedicated-hosts.md) que é fornecido por outras famílias de VM do Azure.


## <a name="deployment-considerations"></a>Considerações de implantação

Siga um tutorial de início rápido para implantar uma máquina virtual da série DCsv2 em menos de dez minutos. 

- **Assinatura do Azure**: para implantar uma instância de VM de computação confidencial, considere aderir a uma assinatura paga conforme o uso ou outra opção de compra. Se você estiver usando uma [conta gratuita do Azure](https://azure.microsoft.com/free/), não terá uma cota com a quantidade apropriada de núcleos de computação do Azure.

- **Preços e disponibilidade regional**: encontre os preços das VMs da série DCsv2 na [Página de preços de máquinas virtuais](https://azure.microsoft.com/pricing/details/virtual-machines/linux/). Confira [Produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines) para ver a disponibilidade nas regiões do Azure.


- **Cota de núcleos** – Talvez seja preciso aumentar a cota de núcleos em sua assinatura do Azure, saindo do valor padrão. Sua assinatura também pode limitar o número de núcleos que você pode implantar em determinadas famílias de tamanho de VM, incluindo a série de DCsv2. Para solicitar um aumento de cota, [abra uma solicitação de atendimento ao cliente online](../azure-portal/supportability/per-vm-quota-requests.md) gratuitamente. Observação: os limites padrão podem variar dependendo de sua categoria de assinatura.

  > [!NOTE]
  > Entre em contato com o Suporte do Azure se precisar de capacidade em larga escala. Cotas do Azure são limites de crédito, não garantias de capacidade. Independentemente de sua cota, você é cobrado apenas pelo núcleos utilizados.
  
- **Redimensionamento**: devido ao hardware especializado, você só pode redimensionar instâncias de computação confidenciais dentro da mesma família de tamanhos. Por exemplo, só é possível dimensionar o tamanho de uma VM da série DCsv2 para outra DCsv2. Não há suporte para o redimensionamento de um tamanho de computação não confidencial para um tamanho de computação confidencial.  

- **Imagem**: para fornecer suporte ao SGX da Intel (Intel Software Guard Extension) em instâncias de computação confidenciais, todas as implantações precisam ser executadas nas imagens da Geração 2. A computação confidencial do Azure dá suporte a cargas de trabalho em execução no Ubuntu 18.04 Gen 2, Ubuntu 16.04 Gen 2, Windows Server 2019 Gen2 e Windows Server 2016 Gen 2. Leia sobre o [suporte a VMs da geração 2 no Azure](../virtual-machines/generation-2.md) para saber mais sobre os cenários com e sem suporte. 

- **Armazenamento**: os discos de dados da máquina virtual de computação confidencial do Azure e nossos discos efêmeros do SO estão em discos NVMe. As instâncias dão suporte apenas para discos SSD Premium e SSD Standard, não SSD Ultra ou HDD Standard. O tamanho da máquina virtual **DC8_v2** não oferece suporte de armazenamento Premium. 

- **Criptografia de disco**: no momento, as instâncias confidenciais de computação não dão suporte para criptografia de disco. 

## <a name="high-availability-and-disaster-recovery-considerations"></a>Considerações sobre alta disponibilidade e recuperação de desastres

Ao usar máquinas virtuais no Azure, você será responsável por implementar uma solução de alta disponibilidade e recuperação de desastre para evitar que haja tempo de inatividade. 

No momento, a computação confidencial do Azure não oferece suporte para redundância de zona por meio de zonas de disponibilidade. Para obter a maior disponibilidade e redundância para computação confidencial, use [Conjuntos de Disponibilidade](../virtual-machines/availability-set-overview.md). Devido a restrições de hardware, os Conjuntos de Disponibilidade para instâncias de computação confidencial podem ter somente um máximo de dez domínios de atualização. 

## <a name="deployment-with-azure-resource-manager-arm-template"></a>Implantação com modelo de Azure Resource Manager (ARM)

O Azure Resource Manager é p serviço de implantação e gerenciamento do Azure. Ele fornece uma camada de gerenciamento que lhe permite criar, atualizar e excluir recursos em sua assinatura do Azure. Você pode usar recursos de gerenciamento, como controle de acesso, bloqueios e marcas, para proteger e organizar seus recursos após a implantação.

Para saber mais sobre os modelos do ARM, confira [implantação de modelo visão geral](../azure-resource-manager/templates/overview.md).

Para implantar uma VM DCsv2-Series em um modelo ARM, você usará o [recurso de máquina virtual](../virtual-machines/windows/template-description.md). Certifique-se de especificar as propriedades corretas em **vmSize** e para **imageReference**.

### <a name="vm-size"></a>Tamanho da VM

Especifique um dos seguintes tamanhos no modelo do ARM no recurso de máquina virtual. Essa cadeia de caracteres está contida em **propriedades** como **vmSize**.

```json
  [
        "Standard_DC1s_v2",
        "Standard_DC2s_v2",
        "Standard_DC4s_v2",
        "Standard_DC8_v2"
      ],
```

### <a name="gen2-os-image"></a>Imagem do so Gen2

Em **Propriedades**, você também deverá fazer referência a uma imagem em **storageProfile**. Use *apenas uma* das imagens a seguir em sua **imageReference**.

```json
      "2019-datacenter-gensecond": {
        "offer": "WindowsServer",
        "publisher": "MicrosoftWindowsServer",
        "sku": "2019-datacenter-gensecond",
        "version": "latest"
      },
      "2016-datacenter-gensecond": {
        "offer": "WindowsServer",
        "publisher": "MicrosoftWindowsServer",
        "sku": "2016-datacenter-gensecond",
        "version": "latest"
      },
        "18_04-lts-gen2": {
        "offer": "UbuntuServer",
        "publisher": "Canonical",
        "sku": "18_04-lts-gen2",
        "version": "latest"
      },
      "16_04-lts-gen2": {
        "offer": "UbuntuServer",
        "publisher": "Canonical",
        "sku": "16_04-lts-gen2",
        "version": "latest"
      }
```

## <a name="next-steps"></a>Próximas etapas 

Neste artigo, você aprendeu sobre as qualificações e configurações necessárias ao criar uma máquina virtual de computação confidencial. Acesse agora o Microsoft Azure Marketplace para implantar uma VM da série DCsv2.

> [!div class="nextstepaction"]
> [Implantar uma máquina virtual da série DCsv2 no Azure Marketplace](quick-create-marketplace.md)