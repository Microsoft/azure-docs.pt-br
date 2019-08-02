---
title: Recursos e extensões de máquina virtual do Microsoft Azure| Microsoft Docs
description: Saiba o que são as extensões Azure VM e como usá-las com as máquinas virtuais do Azure
services: virtual-machines-linux
documentationcenter: ''
author: roiyz-msft
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/30/2018
ms.author: roiyz
ms.openlocfilehash: 3a8c571d3c6bb55fdd8b2d097b71b83afab5ca00
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67705913"
---
# <a name="azure-virtual-machine-extensions-and-features"></a>Extensões e recursos da máquina virtual do Azure
Extensões de máquina virtual do Azure (VM) são pequenos aplicativos que fornecem as tarefas de configuração e automação de pós-implantação em VMs do Azure, você pode usar imagens existentes e personalizá-las como parte de suas implantações, você saindo da empresa de criação de imagem personalizada.

A plataforma do Azure hospeda várias extensões que variam de configuração de VM, monitoramento, segurança e aplicativos de utilidade. Os publicadores tomam um aplicativo, e o envolvem em uma extensão e simplificam a instalação, assim tudo que você precisa fazer é fornecer parâmetros obrigatórios. 

 Há uma opção grande de primeiras e extensões de terceiros, se o aplicativo no repositório de extensão não existir, você pode usar a extensão de Script personalizado e configurar sua VM com seus próprios comandos e scripts.

Exemplos de cenários-chave cujas extensões são usadas para:
* Configuração da VM, você pode usar a DSC (Configuração de Estado Desejado) do Powershell, Extensões Chef, Puppet e Custom Script para instalar os agentes de configuração da VM e configurar a sua VM. 
* Produtos AV, como Symantec, ESET.
* Ferramenta de vulnerabilidade VM, como Qualys, Rapid7, HPE.
* Ferramentas de VM e aplicativo, como DynaTrace, Observador de Rede do Azure, Site24x7, e Stackify.

As extensões podem ser incluídas com uma nova implantação de VM. Por exemplo, podem fazer parte de uma implantação maior, configuração de aplicativos no provisionamento de VM, ou executar em qualquer pós-implantação de sistemas operado por  extensão com suporte.

## <a name="how-can-i-find-what-extensions-are-available"></a>Como localizar quais extensões estão disponíveis?
Você pode exibir as extensões disponíveis na folha da VM no Portal, em extensões, isso representa apenas uma pequena quantidade para a lista completa, você pode usar as ferramentas CLI, consulte [Descobrindo as Extensões VM para Linux](features-linux.md) e [ Descobrindo as Extensões VM para Windows](features-windows.md).

## <a name="how-can-i-install-an-extension"></a>Como instalar uma extensão?
As extensões de VM do Azure podem ser gerenciadas usando a CLI do Azure, o Azure PowerShell, os modelos do Azure Resource Manager e o portal do Azure. Para testar uma extensão, você pode ir para o portal do Azure, selecionar a extensão de Script personalizado, em seguida, passar um comando / script e executar as extensões.

Caso deseje a mesma extensão que você adicionou no portal pela CLI ou pelo modelo do Resource Manager, confira a documentação de uma extensão diferente, como a [Extensão de Script Personalizado do Windows](custom-script-windows.md) e a [Extensão de Script Personalizado do Linux](custom-script-linux.md).

## <a name="how-do-i-manage-extension-application-lifecycle"></a>Como gerenciar o ciclo de vida do aplicativo de extensão?
Você não precisa se conectar a uma VM diretamente para instalar ou excluir a extensão. Como o ciclo de vida do aplicativo de extensão do Azure é gerenciado fora da VM e integrado à plataforma do Azure, também é possível obter status integrado da extensão.

## <a name="anything-else-i-should-be-thinking-about-for-extensions"></a>Qualquer outra coisa que devo estar pensando sobre extensões?
As extensões instalam aplicativos, como os aplicativos, há alguns requisitos, para extensões existe uma lista de Windows e Linux OSes compatíveis e você  precisa ter os agentes de VM do Azure instalados. Alguns aplicativos de extensão VM individuais podem ter seus próprios pré-requisitos de ambiente, como acesso a um ponto de extremidade.

## <a name="next-steps"></a>Próximas etapas
* Para obter mais informações sobre como as extensões e agente do Linux funcionam, consulte [extensões de VM do Azure e recursos para Linux](features-linux.md).
* Para obter mais informações sobre como as extensões e agente do Windows funcionam, consulte [extensões de VM do Azure e recursos para Windows](features-windows.md).  
* Para instalar o agente convidado do Windows, consulte [visão geral do agente de máquina Virtual do Azure Windows](agent-windows.md).  
* Para instalar o agente do Linux, consulte [visão geral do agente de máquina Virtual Linux do Azure](agent-linux.md).  

