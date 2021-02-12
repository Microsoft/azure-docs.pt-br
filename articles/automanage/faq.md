---
title: Perguntas frequentes sobre o autogerenciamento do Azure para máquinas virtuais
description: Respostas para perguntas frequentes sobre o Azure automanage para máquinas virtuais.
author: deanwe
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: troubleshooting
ms.date: 09/04/2020
ms.author: deanwe
ms.openlocfilehash: b8f2b3e1ffde49c8df3c3a6d7c0b9b3751fda5fa
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98696169"
---
# <a name="frequently-asked-questions-for-azure-automanage-for-vms"></a>Perguntas frequentes sobre o autogerenciamento do Azure para VMs

Este artigo fornece respostas para algumas das perguntas mais comuns sobre o [autogerenciamento do Azure para máquinas virtuais](automanage-virtual-machines.md).

Se o problema do Azure não for resolvido neste artigo, visite os fóruns do Azure no [MSDN e Stack Overflow](https://azure.microsoft.com/support/forums/). Você pode postar seu problema nesses fóruns ou postar para [@AzureSupport no Twitter](https://twitter.com/AzureSupport). Você também pode enviar uma solicitação de suporte do Azure. Para enviar uma solicitação de suporte, na [página de suporte do Azure](https://azure.microsoft.com/support/options/), selecione **obter suporte**.


## <a name="azure-automanage-for-virtual-machines"></a>Autogerenciamento do Azure para máquinas virtuais

**Quais são os pré-requisitos necessários para habilitar o autogerenciamento do Azure?**

Veja a seguir os pré-requisitos para habilitar o autogerenciamento do Azure:
- Somente VMs do Windows Server
- As VMs devem estar em uma região com suporte
- O usuário deve ter as permissões corretas
- Somente VMs de conjunto sem escala
- O autogerenci não dá suporte a assinaturas de área restrita no momento

**Qual permissão RBAC do Azure é necessária para habilitar o autogerenci?**

Se você estiver habilitando o autogerenciamento em uma VM com uma conta de autogerenciamento existente, precisará da função colaborador para o grupo de recursos onde a VM reside.

Se você estiver usando uma nova conta de autogerenciamento ao habilitar o, deverá ter a função de proprietário ou ter a função de administrador de acesso de usuário ou colaborador para a assinatura.


**Quais regiões têm suporte?**

As VMs nas seguintes regiões têm suporte: Europa Ocidental, leste dos EUA, oeste dos EUA 2, Canadá central, Oeste EUA Central, leste do Japão.


**Quais recursos o Azure autogerenci automatiza?**

O autogerencie registra, configura e monitora durante todo o ciclo de vida da VM os serviços listados [aqui](virtual-machines-best-practices.md).

**O Azure autogerenci funciona com VMs habilitadas para Arc do Azure?**

O autogerenci atualmente não dá suporte a VMs habilitadas para Arc.

**Posso personalizar as configurações no autogerenciamento do Azure?**

Os clientes podem personalizar as configurações para serviços específicos, como retenção de backup do Azure, por meio de preferências de configuração. Para obter a lista completa de configurações que podem ser alteradas, consulte nossa documentação [aqui](virtual-machines-best-practices.md).


**O Azure autogerenci funciona com VMs Linux e Windows?**

Atualmente, o autogerenci oferece suporte a VMs do Azure do Windows Server.


**Posso aplicar a autogerenciamento de forma seletiva somente em um conjunto de VMs?**

O autogerenci pode ser habilitado com simplicidade de clique e ponto em VMs novas e existentes selecionadas. O autogerenci também pode ser desabilitado a qualquer momento.


**O Azure autogerenci dá suporte a VMs em um conjunto de dimensionamento de máquinas virtuais?**

Não, o autogerenciamento do Azure atualmente não dá suporte a VMs em um conjunto de dimensionamento de máquinas virtuais.


**Quanto custa o Azure autogerenci?**

O autogerenciamento do Azure está disponível sem custo adicional na visualização pública. Os recursos do Azure anexados, como o backup do Azure, incorrerão em custos.


**Posso aplicar o autogerenciamento por meio da política do Azure?**

Sim, temos uma política interna que aplicará automaticamente a autogerenciamento a todas as VMs em seu escopo definido. Você também especificará o perfil de configuração (DevTest ou produção) junto com sua conta de autogerenciamento. Saiba mais sobre como habilitar o autogerenciamento por meio da política do Azure [aqui](virtual-machines-policy-enable.md).


**O que é uma conta de autogerenciamento?**

A conta de autogerenciamento é uma MSI (Identidade de Serviço Gerenciada) que fornece o contexto de segurança ou a identidade sob a qual ocorrem as operações automatizadas.


**Ao habilitar o autogerenci, ele afeta quaisquer VMs adicionais além das VMs que selecionei?**

Se sua VM estiver vinculada a um espaço de trabalho Log Analytics existente, usaremos esse espaço de trabalho para aplicar essas soluções: Controle de Alterações, inventário e Gerenciamento de Atualizações. Todas as VMs conectadas a esse espaço de trabalho terão essas soluções habilitadas.


**Posso alterar o perfil de configuração da minha VM?**

Neste momento, você precisará desabilitar o autogerenci para essa VM e, em seguida, reabilitar o autogerenci com o perfil de configuração desejado e as preferências.


**Se minha VM já estiver configurada para um serviço, como Gerenciamento de Atualizações, o autoconfigurável a reconfigurará?**
Não, o autogerenci não irá reconfigurá-lo. Começaremos a monitorar os recursos associados a esse serviço para descompasso.


**Por que minha VM tem um status de falha no portal de autogerenciamento?**

Se você vir o status como *com falha*, poderá solucionar problemas de implantação por meio do grupo de recursos em que sua VM está localizada. Vá para **grupos de recursos**, selecione o grupo de recursos, clique em **implantações** e veja o status com *falha* , junto com os detalhes do erro.

**Como posso obter suporte para a solução de problemas para autogerenciamento?**

Você pode arquivar um [tíquete de caso de suporte técnico](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Para a opção de **serviço** , procure e selecione *autogerenciar* na seção *monitoramento e gerenciamento* .


## <a name="next-steps"></a>Próximas etapas

Tente habilitar o autogerenci para máquinas virtuais no portal do Azure.

> [!div class="nextstepaction"]
> [Habilitar o autogerenci para máquinas virtuais no portal do Azure](quick-create-virtual-machines-portal.md)