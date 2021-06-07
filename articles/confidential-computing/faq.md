---
title: Perguntas frequentes de computação confidencial do Azure
description: Resposta a perguntas frequentes sobre a computação confidencial do Azure.
author: JBCook
ms.topic: troubleshooting
ms.workload: infrastructure
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.date: 4/17/2020
ms.author: jencook
ms.openlocfilehash: a5ecd3827bbdc12b098684f1feda2df652f11940
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102551905"
---
# <a name="frequently-asked-questions-for-azure-confidential-computing"></a>Perguntas frequentes para a computação confidencial do Azure

Este artigo fornece respostas para algumas das perguntas mais comuns sobre a execução [de cargas de trabalho de computação confidencial em máquinas virtuais do Azure](overview.md).

Se o problema do Azure não for resolvido neste artigo, visite os fóruns do Azure no [MSDN e Stack Overflow](https://azure.microsoft.com/support/forums/). Você pode postar seu problema nesses fóruns ou postar para [@AzureSupport no Twitter](https://twitter.com/AzureSupport). Você também pode enviar uma solicitação de suporte do Azure. Para enviar uma solicitação de suporte na página [Suporte do Azure](https://azure.microsoft.com/support/options/), selecione Obter suporte.

## <a name="confidential-computing-virtual-machines"></a>Máquinas Virtuais de computação confidencial <a id="vm-faq"></a>

**Como posso implantar VMs da série DCsv2 no Azure?**

Aqui estão algumas maneiras que de implantar uma VM DCsv2:
   - Usando um [modelo do Azure Resource Manager](../virtual-machines/windows/template-description.md)
   - No [portal do Azure](https://portal.azure.com/#create/hub)
   - No modelo de solução [Computação confidencial do Azure (Máquina Virtual)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-compute.acc-virtual-machine-v2?tab=overview) na seção Marketplace. O modelo de solução do Marketplace ajudará a restringir um cliente aos cenários com suporte (regiões, imagens, disponibilidade, criptografia de disco). 

**Todas as imagens do sistema operacional funcionarão com a computação confidencial do Azure?**

Não. As máquinas virtuais só podem ser implantadas em máquinas operacionais de geração 2 com o Ubuntu Server 18.04, o Ubuntu Server 16.04, o Windows Server 2019 datacenter e o Windows Server 2016 Datacenter. Leia mais sobre VMs de Gen 2 em [Linux](../virtual-machines/generation-2.md) e [Windows](../virtual-machines/generation-2.md)

**As máquinas virtuais DCsv2 estão esmaecidas no portal e não consigo selecionar uma**

Com base na bolha de informações ao lado da VM, há ações diferentes a serem tomadas:
   -    **UnsupportedGeneration**: Altere a geração da imagem da máquina virtual para “Gen2”.
   -    **NotAvailableForSubscription**: A região ainda não está disponível para sua assinatura. Selecione uma região disponível.
   -    **InsufficientQuota**: [Crie uma solicitação de suporte para aumentar a cota](../azure-portal/supportability/per-vm-quota-requests.md). As assinaturas de avaliação gratuita não têm cota para VMs de computação confidencial. 

**As máquinas virtuais DCsv2 não aparecem quando tento procurá-las no seletor de tamanho do portal**

Verifique se você selecionou uma [região disponível](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines). Além disso, certifique-se de selecionar "limpar todos os filtros" no seletor de tamanho. 

**Posso habilitar a rede acelerada com a computação confidencial do Azure?**

 Não. A rede acelerada não tem suporte em máquinas virtuais DC-Series ou DCsv2-Series. A rede acelerada não pode ser habilitada para nenhuma implantação de máquina virtual de computação confidencial ou implantação de cluster do serviço kubernetes do Azure em execução na computação confidencial.

**Posso usar o host dedicado do Azure com esses computadores?**

Sim. O host dedicado do Azure dá suporte a máquinas virtuais da série DCsv2. O host dedicado do Azure fornece um servidor físico de único locatário para executar suas máquinas virtuais. Os usuários geralmente usam o host dedicado do Azure para atender aos requisitos de conformidade relacionados à segurança física, à integridade dos dados e ao monitoramento. 

**Obtenho um erro de falha de implantação de modelo do Azure Resource Manager: "Não foi possível concluir a operação, pois ela resulta em excedendo a cota de núcleos de família de DcsV2 padrão aprovada"**

[Crie uma solicitação de suporte para aumentar a cota](../azure-portal/supportability/per-vm-quota-requests.md). As assinaturas de avaliação gratuita não têm cota para VMs de computação confidencial. 

**Qual é a diferença entre as VMs das séries DCsv2 e DC?**

As VMs da série DC são executadas em processadores Intel de 6 núcleos mais antigos com o Intel SGX e têm menos memória total, memória de Enclave Page Cache (EPC) e estão disponíveis em apenas duas regiões (leste dos EUA e oeste da Europa nos tamanhos Standard_DC2s e Standard_DC4s). Não há planos para tornar essas VMs geralmente disponíveis e elas não são recomendadas para uso em produção. Para implantar essas VMs, use a instância de [Computação confidencial de VM da série DC [versão prévia]](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-compute.confidentialcompute?tab=Overview)  do Marketplace.

**As máquinas virtuais DCsv2 estão disponíveis globalmente?**

Não. Neste momento, essas máquinas virtuais só estão disponíveis em regiões selecionadas. Marque a página [produtos por regiões](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines) para as regiões disponíveis mais recentes. 

**O hyperthreading está desativado nessas máquinas?**

O hyperthreading está desabilitado para todos os clusters de computação confidencial do Azure.

**Como instalo o SDK do Open enclave nas máquinas virtuais do DCsv2?**
   
Para obter instruções sobre como instalar o SDK do OE em uma máquina local ou do Azure, siga as instruções em [Abrir SDK de Enclave do GitHub](https://github.com/openenclave/openenclave).
     
Você também pode examinar o GitHub do SDK do Enclave aberto para obter instruções de instalação específicas do sistema operacional:
   - [Instalar o SDK do OE no Windows](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Windows.md)
   - [Instalar o SDK do OE no Ubuntu 18.04](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Ubuntu_18.04.md)
   - [Instalar o SDK do OE no Ubuntu 16.04](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Ubuntu_16.04.md)