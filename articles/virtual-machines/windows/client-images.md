---
title: Usar imagens de cliente do Windows no Azure
description: Como usar os benefícios da assinatura do Visual Studio para implantar o Windows 7, Windows 8 ou Windows 10 no Azure em cenários de desenvolvimento/teste
author: cynthn
ms.subservice: imaging
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 12/15/2017
ms.author: cynthn
ms.openlocfilehash: 0f8a17ca69da15d650ba88642d7a81e477a6c537
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/06/2021
ms.locfileid: "99627139"
---
# <a name="use-windows-client-in-azure-for-devtest-scenarios"></a>Usar o cliente do Windows no Azure para cenários de desenvolvimento/teste
Use o Windows 7, Windows 8 ou Windows 10 Enterprise (x64) no Azure para cenários de desenvolvimento/teste, desde que você tenha uma assinatura apropriada do Visual Studio (anteriormente MSDN). 

Para executar o Windows 10 em um ambiente de produção, consulte [como implantar o Windows 10 no Azure com direitos de hospedagem multilocatário](windows-desktop-multitenant-hosting-deployment.md).


## <a name="subscription-eligibility"></a>Qualificação de assinatura
Assinantes ativos do Visual Studio (pessoas que adquiriram uma licença de assinatura do Visual Studio) podem usar imagens de cliente do Windows para fins de desenvolvimento e teste. As imagens de cliente do Windows podem ser usadas em seu próprio hardware ou em máquinas virtuais do Azure.

Determinadas imagens de cliente do Windows estão disponíveis no Azure Marketplace. Os assinantes do Visual Studio em qualquer tipo de oferta também podem [preparar e criar](prepare-for-upload-vhd-image.md) imagens de 64 bits do Windows 7, do Windows 8 ou do Windows 10 e, em seguida, [carregar para o Azure](upload-generalized-managed.md).

## <a name="eligible-offers-and-client-images"></a>Ofertas qualificadas e imagens de cliente
A tabela a seguir detalha as IDs de oferta qualificadas para implantar imagens de cliente do Windows por meio do Azure Marketplace. As imagens de cliente do Windows são visíveis apenas para as ofertas a seguir. 

| Nome da oferta | Número da oferta | Imagens de cliente disponíveis | 
|:--- |:---:|:---:|
| [Desenvolvimento/Teste pré-pago](https://azure.microsoft.com/offers/ms-azr-0023p/) |0023P | Windows 10 Enterprise N (x64) <br> Windows 8.1 Enterprise N (x64) <br> Windows 7 Enterprise N com SP1 (x64) |
| [Assinantes do Visual Studio Enterprise (MPN)](https://azure.microsoft.com/offers/ms-azr-0029p/) |0029P | Windows 10 Enterprise N (x64) <br> Windows 8.1 Enterprise N (x64) <br> Windows 7 Enterprise N com SP1 (x64) |
| [Assinantes do Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/) |0059P | Windows 10 Enterprise N (x64) <br> Windows 8.1 Enterprise N (x64) <br> Windows 7 Enterprise N com SP1 (x64) |
| [Assinantes do Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/) |0060P | Windows 10 Enterprise N (x64) <br> Windows 8.1 Enterprise N (x64) <br> Windows 7 Enterprise N com SP1 (x64) |
| [Visual Studio Premium com MSDN (benefício)](https://azure.microsoft.com/offers/ms-azr-0061p/) |0061P | Windows 10 Enterprise N (x64) <br> Windows 8.1 Enterprise N (x64) <br> Windows 7 Enterprise N com SP1 (x64) |
| [Assinantes do Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/) |0063P | Windows 10 Enterprise N (x64) <br> Windows 8.1 Enterprise N (x64) <br> Windows 7 Enterprise N com SP1 (x64) |
| [Assinantes do Visual Studio Enterprise (BizSpark)](https://azure.microsoft.com/offers/ms-azr-0064p/) |0064P | Windows 10 Enterprise N (x64) <br> Windows 8.1 Enterprise N (x64) <br> Windows 7 Enterprise N com SP1 (x64) |
| [Desenvolvimento/Teste Enterprise](https://azure.microsoft.com/offers/ms-azr-0148p/) |0148P | Windows 10 Enterprise N (x64) <br> Windows 8.1 Enterprise N (x64) <br> Windows 7 Enterprise N com SP1 (x64) |

## <a name="check-your-azure-subscription"></a>Verificar sua assinatura do Azure
Se você não souber sua ID de oferta, poderá obtê-la por meio do portal do Azure ou de uma destas duas maneiras:  
- Na janela *assinaturas* : ![ detalhes da ID da oferta do portal do Azure](./media/client-images/offer-id-azure-portal.png) 
- Ou, clique em **Cobrança** e, em seguida, clique em sua ID de assinatura. A ID da oferta aparece na janela *Cobrança*.
Você também pode exibir a ID da oferta na [guia ' assinaturas '](https://account.windowsazure.com/Subscriptions) do portal da conta do Azure: ![ detalhes da ID da oferta no portal da conta do Azure](./media/client-images/offer-id-azure-account-portal.png) 

## <a name="next-steps"></a>Próximas etapas
Agora você pode implantar suas VMs usando o [PowerShell](quick-create-powershell.md), os [modelos do Resource Manager](ps-template.md) ou o [Visual Studio](../../azure-resource-manager/templates/create-visual-studio-deployment-project.md).