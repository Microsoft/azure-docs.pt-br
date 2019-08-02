---
title: Usar imagens do cliente do Windows no Azure | Microsoft Docs
description: Como usar os benefícios da assinatura do Visual Studio para implantar o Windows 7, Windows 8 ou Windows 10 no Azure em cenários de desenvolvimento/teste
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
ms.assetid: 91c3880a-cede-44f1-ae25-f8f9f5b6eaa4
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/15/2017
ms.author: cynthn
ms.openlocfilehash: fcd97b07416526d71d5bee9bc198eb0d912e1c55
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67719158"
---
# <a name="use-windows-client-in-azure-for-devtest-scenarios"></a>Usar o cliente do Windows no Azure para cenários de desenvolvimento/teste
Use o Windows 7, Windows 8 ou Windows 10 Enterprise (x64) no Azure para cenários de desenvolvimento/teste, desde que você tenha uma assinatura apropriada do Visual Studio (anteriormente MSDN). Este artigo descreve os requisitos de qualificação para executar o Windows 7, Windows 8.1, Windows 10 Enterprise no Azure e o uso das imagens a seguir da Galeria do Azure.

![Detalhes da imagem no portal do Azure](./media/client-images/windows-client-msdn-images.png) 

> [!NOTE]
> Para obter a imagem do Windows 10 Pro e Windows 10 Pro N na Galeria do Azure, veja [Como implantar o Windows 10 no Azure com direitos de hospedagem de multilocatário](windows-desktop-multitenant-hosting-deployment.md)
>![Detalhes da imagem do Pro no portal do Azure](./media/client-images/windows-client-pro-images.png) 
>

## <a name="subscription-eligibility"></a>Qualificação de assinatura
Os assinantes ativos do Visual Studio (pessoas que tem adquiriram uma licença de assinatura do Visual Studio) podem usar o cliente do Windows para fins de teste e desenvolvimento. O cliente do Windows pode ser usado em seu próprio hardware e nas máquinas virtuais do Azure em execução em qualquer tipo de assinatura do Azure. O cliente do Windows não pode ser implantado nem usado no Azure para uso em produção normal ou usado por pessoas que não são assinantes ativos do Visual Studio.

Para sua conveniência, algumas imagens do Windows 10 estão disponíveis na Galeria do Azure em [ofertas de desenvolvimento/teste qualificadas](#eligible-offers). Os assinantes do Visual Studio em qualquer tipo de oferta também podem [preparar e criar de forma adequada](prepare-for-upload-vhd-image.md) uma imagem de 64 bits do Windows 7, Windows 8 ou Windows 10 e [carregá-la no Azure](upload-generalized-managed.md). O uso permanece limitado ao desenvolvimento/teste por assinantes ativos do Visual Studio.

## <a name="eligible-offers"></a>Ofertas qualificadas
A tabela a seguir fornece detalhes sobre as IDs de oferta que são qualificadas para implantar o Windows 10 por meio da Galeria do Azure. As imagens do Windows 10 são visíveis apenas para as ofertas a seguir. Os assinantes do Visual Studio que precisam executar o cliente do Windows em um tipo diferente de oferta exigem a [preparação e criação adequadas](prepare-for-upload-vhd-image.md) de uma imagem de 64 bits do Windows 7, Windows 8 ou Windows 10 e seu [carregamento no Azure](upload-generalized-managed.md).

| Nome da oferta | Número da oferta | Imagens de cliente disponíveis |
|:--- |:---:|:---:|
| [Desenvolvimento/Teste pré-pago](https://azure.microsoft.com/offers/ms-azr-0023p/) |0023P |Windows 10 |
| [Assinantes do Visual Studio Enterprise (MPN)](https://azure.microsoft.com/offers/ms-azr-0029p/) |0029P |Windows 10 |
| [Assinantes do Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/) |0059P |Windows 10 |
| [Assinantes do Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/) |0060P |Windows 10 |
| [Visual Studio Premium com MSDN (benefício)](https://azure.microsoft.com/offers/ms-azr-0061p/) |0061P |Windows 10 |
| [Assinantes do Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/) |0063P |Windows 10 |
| [Assinantes do Visual Studio Enterprise (BizSpark)](https://azure.microsoft.com/offers/ms-azr-0064p/) |0064P |Windows 10 |
| [Desenvolvimento/Teste Enterprise](https://azure.microsoft.com/offers/ms-azr-0148p/) |0148P |Windows 10 |

## <a name="check-your-azure-subscription"></a>Verificar sua assinatura do Azure
Se você não souber sua ID de oferta, poderá obtê-la por meio do portal do Azure ou de uma destas duas maneiras:  

- Na janela *Assinaturas*:

  ![Detalhes da ID de oferta no portal do Azure](./media/client-images/offer-id-azure-portal.png) 

- Ou, clique em **Cobrança** e, em seguida, clique em sua ID de assinatura. A ID da oferta aparece na janela *Cobrança*.

Você também pode exibir a ID de oferta na [guia “Assinaturas”](https://account.windowsazure.com/Subscriptions) do portal de Contas do Azure:

![Detalhes da ID de oferta no portal de Contas do Azure](./media/client-images/offer-id-azure-account-portal.png) 

## <a name="next-steps"></a>Próximas etapas
Agora você pode implantar suas VMs usando o [PowerShell](quick-create-powershell.md), os [modelos do Resource Manager](ps-template.md) ou o [Visual Studio](../../vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

