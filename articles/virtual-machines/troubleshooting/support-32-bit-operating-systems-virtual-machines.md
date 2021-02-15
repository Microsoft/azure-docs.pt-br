---
title: Suporte para sistemas operacionais de 32 bits em máquinas virtuais do Azure | Microsoft Docs
description: Informações sobre sistemas operacionais com suporte em máquinas virtuais do Azure
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 09/18/2019
ms.author: v-miegge
ms.openlocfilehash: 81b7efdd6bca0471719c11d130be95405f4d54e1
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/14/2021
ms.locfileid: "98210181"
---
# <a name="support-for-32-bit-operating-systems-in-azure-virtual-machines"></a>Suporte para sistemas operacionais de 32 bits em máquinas virtuais do Azure

O Microsoft Azure agora permite que os usuários tragam seus sistemas operacionais Windows de 32 bits para o Azure. Somente VHDs especializados têm suporte e imagens generalizadas não funcionarão no Azure. Como alguns desses sistemas operacionais já atingiram seu contrato de suporte de fim de vida útil, a Microsoft pode não oferecer suporte adicional para eles. O suporte também não é oferecido para sistemas operacionais baseados em Linux ou Berkeley de distribuição de software (BSD) que são executados em uma VM (máquina virtual) Microsoft Azure.

> [!NOTE]
> A plataforma Azure tem uma limitação de espaço de endereço de memória imposta em VMs que executam sistemas operacionais de 32 bits em que apenas 1GB de memória pode ser disponibilizada para a VM (*especialmente em SKUs de cliente, como Win7 ou Win10*), e o restante da memória para a VM será exibido como reservado na VM convidada. Esse é um problema conhecido e, no momento, não temos um ETA para uma correção. Recomendamos migrar para versões de sistema operacional de 64 bits.
> 

## <a name="more-information"></a>Mais informações

Para obter mais informações sobre sistemas operacionais com suporte em máquinas virtuais do Azure, acesse os seguintes artigos da base de dados de conhecimento Microsoft:

* [Suporte de software de servidor da Microsoft para máquinas virtuais do Microsoft Azure](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)
* [Suporte para Linux e tecnologia de código aberto no Azure](https://support.microsoft.com/help/2941892/support-for-linux-and-open-source-technology-in-azure)

## <a name="references"></a>Referências

* [Saiba mais sobre as atualizações de segurança estendidas gratuitas para o Windows Server 2008/R2 no Azure](https://www.microsoft.com/cloud-platform/windows-server-2008)
* [Saiba mais sobre o suporte para imagens especializadas do Windows Server 2008 SP2 32 bits no Azure](/windows-server/get-started/uploading-specialized-ws08-image-to-azure)
* [Saiba mais sobre o suporte para a migração de imagens do Windows Server 2008 para o Azure usando Azure Site Recovery](../../site-recovery/migrate-tutorial-windows-server-2008.md)
* [Saiba mais sobre os sistemas operacionais com suporte da extensão do Azure](https://support.microsoft.com/help/4078134/azure-extension-supported-operating-systems)
* [Saiba mais sobre como executar o Windows Server 2003 no Microsoft Azure](https://support.microsoft.com/help/3206074/running-windows-server-2003-on-microsoft-azure)

## <a name="next-steps"></a>Próximas etapas

Se precisar de mais ajuda a qualquer momento neste artigo, entre em contato com os especialistas do Azure nos [fóruns do Azure e do Stack Overflow do MSDN](https://azure.microsoft.com/support/forums/).

Como alternativa, arquivo de um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione **Obter Suporte**.
