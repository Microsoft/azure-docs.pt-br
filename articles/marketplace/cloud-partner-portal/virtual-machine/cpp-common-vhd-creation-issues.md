---
title: Problemas comuns durante a criação do VHD (Perguntas Frequentes) para o Azure Marketplace | Microsoft Docs
description: Perguntas frequentes sobre a criação do VHD e problemas associados.
services: Azure Marketplace
documentationcenter: ''
author: HannibalSII
manager: Patrick.Butler
editor: ''
ms.assetid: e39563d8-8646-4cb7-b078-8b10ac35b494
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 10/02/2018
ms.author: hascipio; v-divte; v-miclar
ms.openlocfilehash: 381f88c4641417bceca0f988d4b1a187aedaa642
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60744186"
---
# <a name="common-issues-during-vhd-creation-faq"></a>Problemas comuns durante a criação do VHD (Perguntas Frequentes)

As perguntas frequentes cobrem problemas comuns encontrados durante a criação do disco rígido virtual (VHD) máquina virtual (VM) para as ofertas da máquina virtual. 

## <a name="how-do-you-create-a-vm-from-the-azure-portal-using-the-vhd-that-is-uploaded-to-premium-storage"></a>Como criar uma VM no portal do Microsoft Azure usando o VHD carregado no armazenamento premium?

O Azure Marketplace atualmente não oferece suporte para criar ofertas VM de imagens que residem no armazenamento gerenciado ou de Armazenamento Premium do Azure.  Para saber mais sobre as opções de armazenamento, confira a [Visão geral do Azure Managed Disks](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview).


## <a name="can-you-use-generation-2-vms-for-offers"></a>É possível usar VMs de geração 2 para as ofertas?

Não, somente VHDs da geração 1 são compatíveis.  No entanto, estamos atualmente trabalhando com a Equipe da Plataforma do Microsoft Azure para investigar o suporte para VMs de geração 2.  Para obter mais informações sobre as diferenças, consulte [Devo criar uma máquina virtual de geração 1 ou 2 no Hyper-V?](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)


## <a name="how-do-you-change-the-name-of-the-host"></a>Como posso alterar o nome do host?

Não é possível.  Após a criação da VM, os usuários (incluindo os proprietários) não poderão atualizar o nome do host.


## <a name="how-do-you-reset-the-remote-desktop-service-or-its-sign-in-password"></a>Como redefinir o serviço de Área de Trabalho Remota ou sua senha de logon?

Os artigos a seguir explicam como executar redefinições RDS para VMs baseadas em Windows e Linux:   

- [Como redefinir o serviço Área de Trabalho Remota ou sua senha de logon em uma VM do Windows](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-reset-rdp/)
- [Como redefinir uma senha de VM do Linux ou chave SSH, corrigir a configuração de SSH e verificar a consistência de disco usando a extensão VMAccess](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-classic-reset-access/)


## <a name="how-do-you-generate-new-ssh-certificates"></a>Como posso gerar novos certificados SSH?

A geração de certificados é explicada no artigo [Obter URI da assinatura de acesso compartilhada pra a sua imagem VM](./cpp-get-sas-uri.md) na seção subsequente [Criar ativos técnicos para uma oferta de VM](./cpp-create-technical-assets.md).


## <a name="how-do-you-configure-a-virtual-private-network-vpn-to-work-with-my-vms"></a>Como configurar uma rede virtual privada (VPN) para trabalhar com minhas VMs?

Se você estiver usando o Modelo de Implantação do Azure Resource Manager, você tem três opções comuns de configuração de uma VPN:
- [Criar um gateway de VPN baseado em rotas usando o portal do Azure](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-portal)
- [Criar um gateway VPN baseado em rotas usando PowerShell](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-powershell)
- [Criar um Gateway de VPN baseado em rota usando CLI](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-cli)


## <a name="what-are-microsoft-support-policies-for-running-microsoft-server-software-on-azure-based-vms"></a>Quais são as políticas de suporte da Microsoft para a execução de software de servidor Microsoft em VMs do Azure?

Essas políticas são detalhadas no artigo [Suporte de software de servidor da Microsoft para máquinas virtuais do Microsoft Azure](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines).


## <a name="do-virtual-machines-have-unique-identifiers-associated-with-them"></a>As máquinas virtuais têm identificadores exclusivos associados a eles?

Sim, se hospedados no Azure.  O Azure atribui um identificador exclusivo, chamado de ID exclusiva da máquina Virtual do Azure para cada novo recurso VM que é criado.  Para obter mais informações, leia a postagem no blog [ID exclusiva da máquina Virtual do Azure](https://blogs.msdn.microsoft.com/wasimbloch/2016/10/20/azure-virtual-machine-unique-id/).  Você também pode obter esse identificador programaticamente por meio de [API de Lista](https://docs.microsoft.com/rest/api/compute/virtualmachines/list).


## <a name="in-a-vm-how-do-you-manage-the-custom-script-extension-in-the-startup-task"></a>Em uma VM, como posso gerenciar a extensão de script personalizado na tarefa de inicialização?

O artigo a seguir detalha como usar a Extensão de Script Personalizado usando o módulo do Azure PowerShell e modelos do Azure Resource Manager, além de detalhar as etapas da solução de problemas em sistemas Windows: [Extensão de script personalizado para o Windows](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-extensions-customscript/)


## <a name="are-32-bit-applications-or-services-supported-in-the-azure-marketplace"></a>São aplicativos de 32 bits ou serviços com suporte no Azure Marketplace?

Em geral, não.  Os serviços padrão para VMs do Azure e sistemas operacionais com suporte são 64-bit.  No entanto, do ponto de vista técnico, a maioria dos sistemas operacionais de 64 bits suporta as versões de 32 bits de execução de aplicativos para compatibilidade com versões anteriores.  No entanto, use aplicativos de 32 bits, uma vez que parte de sua solução VM não tem suporte e, portanto, é *altamente desencorajada*.  Em vez disso, recompile seu aplicativo como um projeto de 64 bits.

Para obter mais informações, consulte os seguintes artigos:
- [Aplicativos de 32 bits em execução](https://docs.microsoft.com/windows/desktop/WinProg64/running-32-bit-applications)
- [Suporte para sistemas operacionais de 32 bits em máquinas virtuais do Azure](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines)
- [Suporte de software de servidor da Microsoft para máquinas virtuais do Microsoft Azure](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)


## <a name="every-time-i-try-to-create-an-image-from-my-vhds-i-get-the-error-vhd-is-already-registered-with-image-repository-as-the-resource-in-powershell-i-did-not-create-any-image-before-nor-did-i-find-any-image-with-this-name-in-azure-how-do-i-resolve-this-issue"></a>Sempre que tento criar uma imagem de minhas VHDs, recebo o erro `.VHD is already registered with image repository as the resource` no PowerShell. Eu não criei qualquer imagem antes, nem encontrei qualquer imagem com esse nome no Azure. Como resolver isso?

Esse problema geralmente ocorre se o usuário provisionado uma VM a partir de um VHD que tem um bloqueio.  Verifique se há uma VM alocada a partir desse VHD e, em seguida, repita a operação.  Se o problema persistir, abra um tíquete de suporte, conforme explicado em [Suporte para o Portal do Cloud Partner](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-support-for-cloud-partner-portal). 

