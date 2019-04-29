---
title: Solucionar problemas de implantação da VM Windows no Azure| Microsoft Docs
description: Solucionar problemas de implantação do Resource Manager ao criar uma nova máquina virtual Windows no Azure
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: JiangChen79
manager: jeconnoc
editor: ''
tags: top-support-issue, azure-resource-manager
ms.assetid: afc6c1a4-2769-41f6-bbf9-76f9f23bcdf4
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: cjiang
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8f18fbabe52c9170cde70900933ce96a3a6400c7
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62123820"
---
# <a name="troubleshoot-deployment-issues-when-creating-a-new-windows-vm-in-azure"></a>Solucionar problemas de implantação ao criar uma nova VM Windows no Azure
[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-opening](../../../includes/virtual-machines-troubleshoot-deployment-new-vm-opening-include.md)]

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]

## <a name="top-issues"></a>Principais problemas
[!INCLUDE [support-disclaimer](../../../includes/virtual-machines-windows-troubleshoot-deploy-vm-top.md)]

Para outros problemas de implantação de VM e perguntas, confira [Solucionar problemas de implantação de máquina virtual Windows no Azure](troubleshoot-deploy-vm-windows.md).

## <a name="collect-activity-logs"></a>Coletar logs de atividades
Para iniciar a solução de problemas, colete os logs de atividades para identificar o erro associado ao problema. Os links a seguir contêm informações detalhadas sobre o processo a ser seguido.

[Exibir operações de implantação](../../azure-resource-manager/resource-manager-deployment-operations.md)

[Exibir logs de atividades para gerenciar recursos do Azure](../../resource-group-audit.md)

[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-issue1](../../../includes/virtual-machines-troubleshoot-deployment-new-vm-issue1-include.md)]

[!INCLUDE [virtual-machines-windows-troubleshoot-deployment-new-vm-table](../../../includes/virtual-machines-windows-troubleshoot-deployment-new-vm-table.md)]

**Y:** se o sistema operacional for Windows generalizado e ele for carregado e/ou capturado com a configuração generalizada, não haverá erros. Da mesma forma, se o sistema operacional for Windows especializado e ele for carregado e/ou capturado com a configuração especializada, não haverá erros.

**Erros de upload:**

**N<sup>1</sup>:** se o sistema operacional for Windows generalizado e ele for carregado como especializado, você receberá um erro de tempo limite de provisionamento, com a VM paralisada na tela do OOBE.

**N<sup>2</sup>:** se o sistema operacional for Windows especializado e ele for carregado como generalizado, você receberá um erro de falha no provisionamento com a VM paralisada na tela do OOBE, pois a nova VM estará em execução com o nome do computador, nome de usuário e senha originais.

**Resolução**

Para resolver ambos os erros, use o [Add-AzVhd para carregar o VHD original](https://docs.microsoft.com/powershell/module/az.compute/add-azvhd), disponível localmente, com a mesma configuração usada para o SO (generalizado/especializado). Para carregar como generalizado, lembre-se de executar sysprep primeiro.

**Erros de captura:**

**N<sup>3</sup>:** se o sistema operacional for Windows generalizado e ele for capturado como especializado, você receberá um erro de tempo limite de provisionamento, pois a VM original não será utilizável, já que estará marcada como generalizada.

**N<sup>4</sup>:** se o sistema operacional for Windows especializado e ele for capturado como generalizado, você receberá um erro de falha no provisionamento, pois a nova VM estará em execução com o nome do computador, nome de usuário e senha originais. Além disso, a VM original não será utilizável, já que estará marcada como especializada.

**Resolução**

Para resolver ambos os erros, exclua a imagem atual do portal e [recapture-a dos VHDs atuais](../windows/create-vm-specialized.md) com a mesma configuração usada para o sistema operacional (generalizado/especializado).

## <a name="issue-customgallerymarketplace-image-allocation-failure"></a>Problema: imagem personalizada/da galeria/do Marketplace; falha de alocação
Esse erro ocorre em situações nas quais a nova solicitação de VM é fixada em um cluster que não tem suporte para o tamanho da VM sendo solicitado ou não tem espaço livre disponível para acomodar a solicitação.

**Causa 1:** o cluster não dá suporte ao tamanho de VM solicitado.

**Resolução 1:**

* Repita a solicitação com um tamanho de VM menor.
* Se o tamanho da VM solicitada não puder ser alterado:
  * Pare todas as VMs no conjunto de disponibilidade.
    Clique em **Grupos de recursos** > *seu grupo de recursos* > **Recursos** > *seu conjunto de disponibilidade* > **Máquinas Virtuais** > *sua máquina virtual* > **Parar**.
  * Depois de parar todas as máquinas virtuais, crie a nova VM no tamanho desejado.
  * Inicie a nova VM primeiro e, em seguida, selecione cada uma das VMs paradas e clique em **Iniciar**.

**Causa 2:** o cluster não tem recursos livres.

**Resolução 2:**

* Repita a solicitação mais tarde.
* Se a nova VM puder ser parte de um conjunto de disponibilidade diferente
  * Crie uma nova VM em um conjunto de disponibilidade diferente (na mesma região).
  * Adicione a nova VM à mesma rede virtual.

## <a name="next-steps"></a>Próximas etapas
Se você encontrar problemas ao iniciar uma VM do Windows parada ou redimensionar uma VM do Windows existente no Azure, consulte [Solucionar problemas de implantação do Resource Manager com a reinicialização ou o redimensionamento de uma máquina virtual Windows no Azure](restart-resize-error-troubleshooting.md).


