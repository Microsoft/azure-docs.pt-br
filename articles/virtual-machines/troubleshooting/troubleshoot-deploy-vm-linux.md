---
title: Solução de problemas de implantação de máquina virtual do Linux no Azure | Microsoft Docs
description: Solucione problemas de máquina virtual implantação do Linux no modelo de implantação do Azure Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 4e383427-4aff-4bf3-a0f4-dbff5c6f0c81
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 11/01/2018
ms.author: genli
ms.openlocfilehash: 38d53af06312c993a749fa86619e25e60d39fff4
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67710469"
---
# <a name="troubleshoot-deploying-linux-virtual-machine-issues-in-azure"></a>Solução de problemas de implantação de máquina virtual do Linux no Azure

Para solucionar problemas de implantação de VM (máquina virtual) no Azure, examine os [principais problemas](#top-issues) para falhas e resoluções comuns.

Caso precise de mais ajuda a qualquer momento neste artigo, entre em contato com os especialistas do Azure nos [fóruns do Azure e do Stack Overflow no MSDN](https://azure.microsoft.com/support/forums/). Como alternativa, você pode registrar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione **Obter Suporte**.

## <a name="top-issues"></a>Principais problemas
[!INCLUDE [virtual-machines-linux-troubleshoot-deploy-vm-top](../../../includes/virtual-machines-linux-troubleshoot-deploy-vm-top.md)]

## <a name="the-cluster-cannot-support-the-requested-vm-size"></a>O cluster não dá suporte ao tamanho de VM solicitado
\<properties supportTopicIds="123456789" resourceTags="windows" productPesIds="1234, 5678" />
- Repita a solicitação com um tamanho de VM menor.
- Se o tamanho da VM solicitada não puder ser alterado:
    - Pare todas as VMs no conjunto de disponibilidade. Clique em **Grupos de recursos** > seu grupo de recursos > **Recursos** > seu conjunto de disponibilidade > **Máquinas Virtuais** > sua máquina virtual > **Parar**.
    - Depois de parar todas as VMs, crie a VM no tamanho desejado.
    - Inicie a nova VM primeiro e, em seguida, selecione cada uma das VMs paradas e clique em Iniciar.


## <a name="the-cluster-does-not-have-free-resources"></a>O cluster não tem recursos livres
\<properties supportTopicIds="123456789" resourceTags="windows" productPesIds="1234, 5678" />
- Tente fazer novamente a solicitação.
- Se a nova VM puder ser parte de um conjunto de disponibilidade diferente
    - Crie uma nova VM em um conjunto de disponibilidade diferente (na mesma região).
    - Adicione a nova VM à mesma rede virtual.

## <a name="how-do-i-activate-my-monthly-credit-for-visual-studio-enterprise-bizspark"></a>Como ativar o crédito mensal para o Visual Studio Enterprise (BizSpark)

Para ativar o seu crédito mensal, consulte este [artigo](https://azure.microsoft.com/offers/ms-azr-0064p/).

## <a name="why-can-i-not-install-the-gpu-driver-for-an-ubuntu-nv-vm"></a>Por que posso não instalar o driver GPU em uma VM Ubuntu NV?

No momento, o suporte ao GPU do Linux está disponível apenas em VMs NC do Azure que executam o Ubuntu Server 16.04 LTS. Para saber mais, confira [Configurar drivers GPU para VMs série N executando o Linux](../linux/n-series-driver-setup.md).

## <a name="my-drivers-are-missing-for-my-linux-n-series-vm"></a>Meus drivers estão ausentes para minha VM série N do Linux

Drivers para VMs baseadas em Linux estão localizados [aqui](../linux/n-series-driver-setup.md). 

## <a name="i-cant-find-a-gpu-instance-within-my-n-series-vm"></a>Não é possível localizar uma instância GPU em minha VM série N

Para aproveitar as funcionalidades de GPU das VMs da série N do Azure que executam o Windows Server 2016 ou o Windows Server 2012 R2, é necessário instalar os drivers gráficos NVIDIA em cada VM após a implantação. Também há informações de instalação de driver disponíveis para [VMs do Windows](../windows/n-series-driver-setup.md) e [VMs do Linux](../linux/n-series-driver-setup.md).

## <a name="is-n-series-vms-available-in-my-region"></a>As VMs da série N estão disponíveis na minha região?

Você pode verificar a disponibilidade em [Produtos disponíveis pela tabela de região](https://azure.microsoft.com/regions/services) e preços [aqui](https://azure.microsoft.com/pricing/details/virtual-machines/series/#n-series).

## <a name="i-am-not-able-to-see-vm-size-family-that-i-want-when-resizing-my-vm"></a>Não consigo ver a família de tamanho da VM que desejo ao redimensionar a minha VM.

Quando uma máquina virtual está em execução, ela é implantada em um servidor físico. Os servidores físicos nas regiões do Azure são agrupados em clusters de hardware físico comum. Redimensionar uma VM que requer que a VM seja movida para clusters de hardware diferentes é diferente dependendo de qual modelo de implantação foi usado para implantar a VM.

- As VMs implantadas no modelo de implantação Clássico, a implantação do serviço de nuvem deve ser removida e reimplantada para alterar as VMs para um tamanho em outra família de tamanho.

- Para VMs implantadas no modelo de implantação do Gerenciador de Recursos, você deve interromper todas as VMs no conjunto antes de alterar o tamanho de uma VM no conjunto de disponibilidade.

## <a name="the-listed-vm-size-is-not-supported-while-deploying-in-availability-set"></a>Não há suporte para o tamanho da VM listado durante a implantação no Conjunto de Disponibilidade.

Escolha um tamanho que seja compatível com o cluster do conjunto de disponibilidade. É recomendável escolher o maior tamanho de VM que você achar necessário ao criar um conjunto de disponibilidade e faça com que ela seja a primeira implantação para o conjunto de disponibilidade.

## <a name="what-linux-distributionsversions-are-supported-on-azure"></a>Quais versões/distribuições do Linux têm suporte no Azure?

Você pode encontrar a lista no Linux em [Distribuições endossadas pelo Azure](../linux/endorsed-distros.md).

## <a name="can-i-add-an-existing-classic-vm-to-an-availability-set"></a>Posso adicionar uma VM Clássica existente a um conjunto de disponibilidade?

Sim. Você pode adicionar uma VM clássica existente para um Conjunto de Disponibilidade novo ou existente. Para obter mais informações, consulte [Adicionar uma máquina virtual existente ao conjunto de disponibilidade](/previous-versions/azure/virtual-machines/windows/classic/configure-availability-classic#addmachine).


## <a name="next-steps"></a>Próximas etapas
Caso precise de mais ajuda a qualquer momento neste artigo, entre em contato com os especialistas do Azure nos [fóruns do Azure e do Stack Overflow no MSDN](https://azure.microsoft.com/support/forums/).

Como alternativa, você pode registrar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione **Obter Suporte**.
