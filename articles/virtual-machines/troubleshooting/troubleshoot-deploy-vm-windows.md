---
title: Solução de problemas de implantação de máquina virtual do Windows no Azure | Microsoft Docs
description: Solucione problemas de máquina virtual implantação do Windows no modelo de implantação do Azure Resource Manager.
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
ms.openlocfilehash: 76841484595b0a8809b8ab10db3199fc8cd28b04
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67710445"
---
# <a name="troubleshoot-deploying-windows-virtual-machine-issues-in-azure"></a>Solução de problemas de implantação de máquina virtual do Windows no Azure

Para solucionar problemas de implantação de máquina virtual (VM) no Azure, examine os [principais problemas](#top-issues) para falhas e resoluções comuns.

Caso precise de mais ajuda a qualquer momento neste artigo, entre em contato com os especialistas do Azure nos [fóruns do Azure e do Stack Overflow no MSDN](https://azure.microsoft.com/support/forums/). Como alternativa, você pode registrar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione **Obter Suporte**.

## <a name="top-issues"></a>Principais problemas
[!INCLUDE [virtual-machines-windows-troubleshoot-deploy-vm-top](../../../includes/virtual-machines-windows-troubleshoot-deploy-vm-top.md)]

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

## <a name="how-can-i-use-and-deploy-a-windows-client-image-into-azure"></a>Como usar e implantar uma imagem do cliente Windows no Azure?

Você pode usar o Windows 7, Windows 8 ou Windows 10 no Azure para cenários de desenvolvimento + teste, desde que você tenha uma assinatura apropriada do Visual Studio (anteriormente conhecido como MSDN). Este [artigo](../windows/client-images.md) descreve os requisitos de qualificação para execução do cliente do Windows no Azure e uso das imagens da Galeria do Azure.

## <a name="how-can-i-deploy-a-virtual-machine-using-the-hybrid-use-benefit-hub"></a>Como implantar uma máquina virtual usando o Benefício de Uso Híbrido (HUB)?

Existem algumas maneiras diferentes para implantar máquinas virtuais Windows com o Benefício de Uso Híbrido do Azure.

Para uma assinatura do Enterprise Agreement:

•   Implantar VMs com base em imagens específicas do Marketplace pré-configuradas com o Benefício de Uso Híbrido do Azure.

Para o Enterprise Agreement:

•   Carregar uma VM personalizada e implantar usando um modelo do Resource Manager ou do Azure PowerShell.

Para obter mais informações, consulte os seguintes recursos:

 - [Visão geral do benefício de uso híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/)

 - [Perguntas frequentes para download](https://download.microsoft.com/download/4/2/1/4211AC94-D607-4A45-B472-4B30EDF437DE/Windows_Server_Azure_Hybrid_Use_FAQ_EN_US.pdf)

 - [Benefício do Uso Híbrido do Azure para o Windows Server e o Windows Client](../windows/hybrid-use-benefit-licensing.md).

 - [Como usar o Benefício de Uso Híbrido no Azure](https://blogs.msdn.microsoft.com/azureedu/2016/04/13/how-can-i-use-the-hybrid-use-benefit-in-azure)

## <a name="how-do-i-activate-my-monthly-credit-for-visual-studio-enterprise-bizspark"></a>Como ativar o crédito mensal para o Visual Studio Enterprise (BizSpark)

Para ativar o seu crédito mensal, consulte este [artigo](https://azure.microsoft.com/offers/ms-azr-0064p/).

## <a name="how-to-add-enterprise-devtest-to-my-enterprise-agreement-ea-to-get-access-to-window-client-images"></a>Como adicionar o Desenvolvimento/Teste Enterprise no meu Enterprise Agreement (EA) para obter acesso a imagens do cliente Windows?

A capacidade de criar assinaturas com base na oferta de Desenvolvimento/Teste Enterprise é restrita a Proprietários da Conta que receberam permissão para fazer isso por um Administrador Corporativo. O Proprietário da Conta cria assinaturas no Portal de Contas do Azure e deve adicionar assinantes ativos do Visual Studio como coadministradores. Para que eles podem gerenciar e usar os recursos necessários para desenvolvimento e teste. Para obter mais informações, consulte [Desenvolvimento/Teste Enterprise](https://azure.microsoft.com/offers/ms-azr-0148p/).

## <a name="my-drivers-are-missing-for-my-windows-n-series-vm"></a>Meus drivers estão ausentes para minha VM série N do Windows

Drivers para VMs baseadas em Windows estão localizados [aqui](../windows/n-series-driver-setup.md).

## <a name="i-cant-find-a-gpu-instance-within-my-n-series-vm"></a>Não é possível localizar uma instância GPU em minha VM série N

Para aproveitar as funcionalidades de GPU das VMs da série N do Azure que executam o Windows Server 2016 ou o Windows Server 2012 R2, é necessário instalar os drivers gráficos NVIDIA em cada VM após a implantação. Também há informações de instalação de driver disponíveis para [VMs do Windows](../windows/n-series-driver-setup.md) e [VMs do Linux](../linux/n-series-driver-setup.md).

## <a name="is-n-series-vms-available-in-my-region"></a>As VMs da série N estão disponíveis na minha região?

Você pode verificar a disponibilidade em [Produtos disponíveis pela tabela de região](https://azure.microsoft.com/regions/services) e preços [aqui](https://azure.microsoft.com/pricing/details/virtual-machines/series/#n-series).

## <a name="what-client-images-can-i-use-and-deploy-in-azure-and-how-to-i-get-them"></a>Quais imagens do cliente que posso usar e implantar no Azure e como obtê-las?

Você pode usar o Windows 7, Windows 8 ou Windows 10 no Azure para cenários de desenvolvimento + teste, desde que você tenha uma assinatura apropriada do Visual Studio (anteriormente conhecido como MSDN). 

- Para sua conveniência, disponibilizamos algumas imagens do Windows 10 da Galeria do Azure em [ofertas de desenvolvimento/teste qualificadas](../windows/client-images.md#eligible-offers). 
- Os assinantes do Visual Studio em qualquer tipo de oferta também podem [preparar e criar de forma adequada](../windows/prepare-for-upload-vhd-image.md) uma imagem de 64 bits do Windows 7, Windows 8 ou Windows 10 e [carregá-la no Azure](../windows/upload-generalized-managed.md). O uso permanece limitado ao desenvolvimento/teste por assinantes ativos do Visual Studio.

Este [artigo](../windows/client-images.md) descreve os requisitos de qualificação para execução do cliente do Windows no Azure e uso das imagens da Galeria do Azure.

## <a name="i-am-not-able-to-see-vm-size-family-that-i-want-when-resizing-my-vm"></a>Não consigo ver a família de tamanho da VM que desejo ao redimensionar a minha VM.

Quando uma máquina virtual está em execução, ela é implantada em um servidor físico. Os servidores físicos nas regiões do Azure são agrupados em clusters de hardware físico comum. Redimensionar uma VM que requer que a VM seja movida para clusters de hardware diferentes é diferente dependendo de qual modelo de implantação foi usado para implantar a VM.

- As VMs implantadas no modelo de implantação Clássico, a implantação do serviço de nuvem deve ser removida e reimplantada para alterar as VMs para um tamanho em outra família de tamanho.

- Para VMs implantadas no modelo de implantação do Gerenciador de Recursos, você deve interromper todas as VMs no conjunto antes de alterar o tamanho de uma VM no conjunto de disponibilidade.

## <a name="the-listed-vm-size-is-not-supported-while-deploying-in-availability-set"></a>Não há suporte para o tamanho da VM listado durante a implantação no Conjunto de Disponibilidade.

Escolha um tamanho que seja compatível com o cluster do conjunto de disponibilidade. É recomendável escolher o maior tamanho de VM que você achar necessário ao criar um conjunto de disponibilidade e faça com que ela seja a primeira implantação para o conjunto de disponibilidade.

## <a name="can-i-add-an-existing-classic-vm-to-an-availability-set"></a>Posso adicionar uma VM Clássica existente a um conjunto de disponibilidade?

Sim. Você pode adicionar uma VM clássica existente para um Conjunto de Disponibilidade novo ou existente. Para obter mais informações, consulte [Adicionar uma máquina virtual existente ao conjunto de disponibilidade](/previous-versions/azure/virtual-machines/windows/classic/configure-availability-classic#addmachine).


## <a name="next-steps"></a>Próximas etapas
Caso precise de mais ajuda a qualquer momento neste artigo, entre em contato com os especialistas do Azure nos [fóruns do Azure e do Stack Overflow no MSDN](https://azure.microsoft.com/support/forums/).

Como alternativa, você pode registrar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione **Obter Suporte**.
