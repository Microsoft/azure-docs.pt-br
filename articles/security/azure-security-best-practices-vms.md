---
title: "Práticas recomendadas de segurança de máquina virtual do Azure | Microsoft Docs"
description: "Este artigo fornece uma variedade de práticas recomendadas de segurança a serem usadas em máquinas virtuais localizadas no Azure."
services: security
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 5e757abe-16f6-41d5-b1be-e647100036d8
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/19/2017
ms.author: yurid
ms.openlocfilehash: c8a920a0523cb4737e6bbca7e49d0b9e2c942565
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="best-practices-for-azure-vm-security"></a>Práticas recomendadas para a segurança de VM do Azure

Na maioria dos cenários de IaaS (Infraestrutura como Serviço), as [máquinas virtuais (VMs) do Azure](https://docs.microsoft.com/en-us/azure/virtual-machines/) são a carga de trabalho principal para as organizações que usam a computação em nuvem. Esse fato é especialmente evidente em [cenários híbridos](https://social.technet.microsoft.com/wiki/contents/articles/18120.hybrid-cloud-infrastructure-design-considerations.aspx), nos quais as organizações desejam migrar cargas de trabalho para a nuvem de forma lenta. Nesses cenários, siga o [considerações gerais de segurança para IaaS](https://social.technet.microsoft.com/wiki/contents/articles/3808.security-considerations-for-infrastructure-as-a-service-iaas.aspx)e aplicar práticas recomendadas de segurança para todas as suas VMs.

Este artigo aborda várias práticas recomendadas de segurança VM, cada derivada de nossos clientes e nossas próprias experiências diretas com VMs.

As recomendações baseiam-se um consenso de opinião, e trabalhar com recursos da plataforma Windows Azure atuais e conjuntos de recursos. Como opiniões e tecnologias podem mudar ao longo do tempo, pretendemos atualizar este artigo regularmente para refletir essas alterações.

Cada prática recomendada, o artigo explica:

* O que é a prática recomendada.
* Por que é uma boa ideia para habilitá-lo.
* Como você pode aprender a habilitá-la.
* O que poderia acontecer se você não ativá-lo.
* Possíveis alternativas à prática recomendada.

O artigo examina as seguintes práticas recomendadas de segurança da VM:

* Autenticação e controle de acesso de VM
* Acesso de rede e a disponibilidade de VM
* Proteger dados em repouso em VMs por meio da imposição de criptografia
* Gerenciar as atualizações de VM
* Gerenciar sua postura de segurança de VM
* Monitorar o desempenho de VM

## <a name="vm-authentication-and-access-control"></a>Autenticação e controle de acesso de VM

A primeira etapa para proteger sua VM é garantir que somente usuários autorizados podem configurar novas VMs. É possível usar [políticas do Azure Resource Manager](../azure-resource-manager/resource-manager-policy.md) para estabelecer convenções para recursos na organização, criar políticas personalizadas e aplicá-las a recursos, como [grupo de recursos](../azure-resource-manager/resource-group-overview.md).

Máquinas virtuais que pertencem a um grupo de recursos naturalmente herdam suas políticas. Apesar de recomendarmos essa abordagem para gerenciar VMs, você também pode controlar o acesso às políticas VM individuais usando [o controle de acesso baseado em função (RBAC)](../active-directory/role-based-access-control-configure.md).

Quando você habilita as políticas do Gerenciador de recursos e o RBAC para controlar o acesso VM, você ajuda a melhorar a segurança geral de VM. É recomendável que você consolide VMs com o mesmo ciclo de vida no mesmo grupo de recursos. Usando grupos de recursos, você pode implantar, monitorar e acumular custos para os seus recursos de cobrança. Para habilitar os usuários acessar e configurar máquinas virtuais, use uma [abordagem de privilégios mínimos](https://technet.microsoft.com/en-us/windows-server-docs/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models). E quando você atribuir privilégios a usuários, planeje usar as seguintes funções internas do Azure:

- [Colaborador de Máquina Virtual](../active-directory/role-based-access-built-in-roles.md#virtual-machine-contributor): pode gerenciar VMs, mas não a rede virtual nem a conta de armazenamento à qual ele está conectado.
- [Colaborador de Máquina Virtual Clássica](../active-directory/role-based-access-built-in-roles.md#classic-virtual-machine-contributor): pode gerenciar VMs criadas usando o modelo de implantação clássica, mas não a rede ou armazenamento de conta virtual para o qual as VMs estão conectadas.
- [Gerente de Segurança](../active-directory/role-based-access-built-in-roles.md#security-manager): pode gerenciar componentes de segurança, políticas de segurança e VMs.
- [Usuário do DevTest Labs](../active-directory/role-based-access-built-in-roles.md#devtest-labs-user): pode exibir tudo e se conectar a VMs, iniciá-las, reiniciá-las e desligá-las.

Não compartilhe contas e senhas entre administradores nem reutilize senhas em várias contas de usuário ou serviços, particularmente, as senhas destinadas à mídia social ou outras atividades não administrativas. O ideal é que você use modelos do [Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) para configurar suas VMs com segurança. Ao usar essa abordagem, é possível fortalecer as opções de implantação e impor configurações de segurança em toda a implantação.

As organizações que não impõem o controle de acesso a dados, tirando proveito dos recursos, como o RBAC podem estar concedendo aos usuários mais privilégios do que o necessário. Acesso de usuário inadequado para determinados dados diretamente pode comprometer dados.

## <a name="vm-availability-and-network-access"></a>Acesso de rede e a disponibilidade de VM

Se a VM executar aplicativos críticos que precisam ter alta disponibilidade, será altamente recomendável usar várias VMs. Para uma melhor disponibilidade, crie, no mínimo, duas VMs no [conjunto de disponibilidade](../virtual-machines/windows/tutorial-availability-sets.md).

O [Azure Load Balancer](../load-balancer/load-balancer-overview.md) também exige que as VMs com balanceamento de carga pertençam ao mesmo conjunto de disponibilidade. Se essas VMs devem ser acessadas da Internet, você deve configurar um [balanceador de carga para Internet](../load-balancer/load-balancer-internet-overview.md).

Quando as VMs são expostas à Internet, é importante que você [controle o fluxo de tráfego de rede com grupos de segurança de rede (NSGs)](../virtual-network/virtual-networks-nsg.md). Como os NSGs podem ser aplicados às sub-redes, você pode minimizar o número de NSGs agrupando recursos por sub-rede e aplicando NSGs às sub-redes. O objetivo é criar uma camada de isolamento de rede, o que você pode fazer por meio da configuração correta das funcionalidades de [segurança de rede](../best-practices-network-security.md) no Azure.

Você também pode usar o recurso de acesso à VM just-in-time (JIT) na Central de Segurança do Azure para controlar quem tem acesso remoto a uma VM específica.

As organizações que não impõem restrições de acesso à rede para máquinas virtuais para a Internet estão expostas a riscos de segurança, como um ataque de força bruta do protocolo de área de trabalho remota (RDP).

## <a name="protect-data-at-rest-in-your-vms-by-enforcing-encryption"></a>Proteger dados em repouso em VMs por meio da imposição de criptografia

A [criptografia de dados em repouso](https://blogs.microsoft.com/cybertrust/2015/09/10/cloud-security-controls-series-encrypting-data-at-rest/) é uma etapa obrigatória para garantir a privacidade de dados, conformidade e soberania. O [Azure Disk Encryption](../security/azure-security-disk-encryption.md) permite que os administradores de TI criptografem discos de VM IaaS Windows e Linux. O Disk Encryption combina o recurso BitLocker do Windows padrão do setor e o recurso dm-crypt do Linux para fornecer uma criptografia de volume para o sistema operacional e os discos de dados.

Você pode aplicar o Disk Encryption para ajudar a proteger seus dados e atender a requisitos de conformidade e segurança da organização. Sua organização deve cogitar usar a criptografia para ajudar a reduzir riscos relativos ao acesso a dados não autorizado. Também recomendamos que você criptografe as unidades antes de gravar dados confidenciais-los.

Certifique-se de criptografar os volumes de dados VM para protegê-los em repouso em sua conta de armazenamento do Azure. Proteja as chaves de criptografia e o segredo usando o [Azure Key Vault](https://azure.microsoft.com/en-us/documentation/articles/key-vault-whatis/).

As organizações que não impõem criptografia de dados estão mais expostas a problemas de integridade de dados. Por exemplo, usuários não autorizados ou não autorizados podem roubar dados nas contas comprometidas ou acesso não autorizado a dados codificados em ClearFormat. Além de assumir a tais riscos, de acordo com as normas do setor, as empresas devem comprovar que estão tendo o cuidado e usando controles de segurança corretas para aprimorar a segurança de dados.

Para saber mais sobre criptografia de disco, veja [Azure Disk Encryption para VMs Windows e Linux IaaS](azure-security-disk-encryption.md).


## <a name="manage-your-vm-updates"></a>Gerenciar as atualizações de VM

Porque as VMs do Azure, como todas as máquinas virtuais no local, devem ser gerenciadas pelo usuário, o Azure não push atualizações do Windows-los. No entanto, é recomendável que os clientes deixem a configuração automática do Windows Update habilitada. Outra opção é implantar o [WSUS (Windows Server Update Services)](https://technet.microsoft.com/windowsserver/bb332157.aspx) ou outro produto de gerenciamento de atualizações adequado em outra VM ou local. O WSUS e o Windows Update mantêm as VMs atualizadas. Também recomendamos que você use um produto de verificação para verificar se todas as suas VMs de IaaS são atualizados.

As imagens de estoque fornecidas pelo Azure são atualizadas regularmente para incluir a série mais recente das atualizações do Windows. No entanto, não há nenhuma garantia de que as imagens serão atuais no momento da implantação. Um pequeno intervalo (de não mais do que algumas semanas) versões públicas a seguir pode ser possível. Verificar e instalar todas as atualizações do Windows deve ser a primeira etapa de cada implantação. Essa medida é especialmente importante para aplicar ao implantar imagens que vêm de você ou sua própria biblioteca. Imagens que são fornecidas como parte do Azure Marketplace são atualizadas automaticamente por padrão.

As organizações que não impõem diretivas de atualização de software estão mais expostas a ameaças que exploram vulnerabilidades conhecidas anteriormente fixas. Além de risco essas ameaças, de acordo com as normas do setor, empresas devem provar que estão tendo o cuidado e usando controles de segurança corretas para ajudar a garantir a segurança de sua carga de trabalho localizada na nuvem.

É importante enfatizar que a atualização de software práticas recomendadas para data centers tradicionais e IaaS do Azure têm muitas semelhanças. Portanto, recomendamos que você avalie suas políticas de atualização de software atual para incluir as VMs.

## <a name="manage-your-vm-security-posture"></a>Gerenciar sua postura de segurança de VM

Ameaças estão evoluindo e proteger suas VMs exige uma avançada funcionalidade de monitoramento que pode rapidamente detectar ameaças, impedir acesso não autorizado aos recursos, disparar alertas e reduzir os falsos positivos. A postura de segurança para uma carga de trabalho consiste em todos os aspectos de segurança da VM, de gerenciamento de atualizações para proteger o acesso à rede.

Para monitorar a postura de segurança das suas [VMs Windows ](../security-center/security-center-virtual-machine.md) e [Linux](../security-center/security-center-linux-virtual-machine.md), use a [Central de Segurança do Azure](../security-center/security-center-intro.md). Na Central de segurança do Azure, proteger suas VMs, tirando proveito dos recursos a seguir:

* Aplicar configurações de segurança do sistema operacional com as regras de configuração recomendada
* Identificar e fazer o download de segurança do sistema e atualizações críticas que podem estar ausentes
* Implantar as recomendações de proteção do ponto de extremidade antimalware
* Validar a criptografia de disco
* Avaliar e corrigir vulnerabilidades
* Detectar ameaças

A Central de Segurança pode monitorar as ameaças de forma ativa e essas potenciais ameaças são expostas em **Alertas de Segurança**. As ameaças correlacionadas são agregadas em uma única exibição chamada **Incidente de Segurança**.

Para entender como a Central de Segurança pode ajudá-lo a identificar possíveis ameaças em suas VMs localizadas no Azure, assista a este vídeo:

<iframe src="https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Security-Center-in-Incident-Response/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

As organizações que não impõem uma postura de segurança forte para suas VMs permaneçam sem conhecimento dos possíveis tentativas por usuários não autorizados para driblar controles de segurança estabelecidas.

## <a name="monitor-vm-performance"></a>Monitorar o desempenho de VM

Abuso de recursos pode ser um problema quando os processos VM consomem mais recursos do que deveriam. Problemas de desempenho com uma máquina virtual podem levar a interrupção do serviço, o que viola o princípio de segurança de disponibilidade. Por esse motivo, é fundamental monitorar o acesso à VM não apenas de forma reativa, enquanto um problema está ocorrendo, mas também de forma proativa, em relação ao desempenho de linha de base, conforme medido durante a operação normal.

Analisando [arquivos de log de diagnóstico do Azure](https://azure.microsoft.com/en-us/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/), você pode monitorar os recursos VM e identificar problemas potenciais que podem comprometer o desempenho e disponibilidade. A Extensão de Diagnóstico do Azure fornece funcionalidades de monitoramento e diagnóstico em VMs baseadas no Windows. É possível habilitar essas funcionalidades ao incluir a extensão como parte do [modelo do Azure Resource Manager](../virtual-machines/windows/extensions-diagnostics-template.md).

Também é possível usar o [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview-metrics.md) para obter visibilidade da integridade do recurso.

As organizações que não monitoram o desempenho da máquina virtual não conseguem determinar se são determinadas alterações nos padrões de desempenho normais ou anormais. Se a VM está consumindo mais recursos do que o normal, tal uma anomalia pode indicar um ataque potencial de um recurso externo ou um processo comprometido em execução na máquina virtual.
