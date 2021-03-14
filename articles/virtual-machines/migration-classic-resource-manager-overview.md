---
title: Visão geral da migração de recursos de IaaS com suporte da plataforma do clássico para o Azure Resource Manager
description: Percorra a migração de recursos com suporte da plataforma do clássico para o Azure Resource Manager.
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.subservice: classic-to-arm-migration
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: 116e99339ac79e9e6a2de5e7a6222460a71bf4a1
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102615084"
---
# <a name="platform-supported-migration-of-iaas-resources-from-classic-to-azure-resource-manager"></a>Migração de recursos de IaaS com suporte da plataforma do clássico para o Azure Resource Manager

> [!IMPORTANT]
> Hoje, cerca de 90% das VMs de IaaS estão usando [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/). A partir de 28 de fevereiro de 2020, as VMs clássicas foram preteridas e serão totalmente desativadas em 1º de março de 2023. [Saiba mais]( https://aka.ms/classicvmretirement) sobre essa reprovação e [como ela afeta você](classic-vm-deprecation.md#how-does-this-affect-me).



Este artigo fornece uma visão geral sobre a ferramenta de migração com suporte da plataforma, como migrar recursos do ASM (Service Manager do Azure), também conhecido como modelos de implantação clássicos para o Gerenciador de recursos (ARM), e detalhes sobre como conectar recursos de dois modelos de implantação que coexistem em sua assinatura usando gateways site a site da rede virtual. Você pode ler mais sobre [Azure Resource Manager recursos e benefícios](../azure-resource-manager/management/overview.md). 

O ASM dá suporte a dois produtos de computação diferentes, as máquinas virtuais do Azure (clássicas) também conhecidas como VMs IaaS & [serviços de nuvem do Azure (clássico)](../cloud-services/index.yml) , conhecidos como VMs PaaS ou funções Web/de trabalho. Este documento fala apenas sobre a migração de máquinas virtuais do Azure (clássico).

## <a name="goal-for-migration"></a>Meta de migração
O Gerenciador de Recursos possibilita implantar aplicativos complexos por meio de modelos, configurar máquinas virtuais usando extensões de VM e incorporar o gerenciamento de acesso e a marcação. O Azure Resource Manager inclui implantação paralela e escalonável para máquinas virtuais em conjuntos de disponibilidade. O novo modelo também oferece gerenciamento de ciclo de vida de computação, rede e armazenamento de maneira independente. Por fim, há um enfoque para habilitar a segurança por padrão com a imposição de máquinas virtuais em uma rede virtual.

Há suporte para quase todos os recursos do modelo de implantação clássica referentes a computação, rede e armazenamento no Azure Resource Manager. Para aproveitar os novos recursos no Azure Resource Manager, você pode migrar as implantações existentes do modelo de implantação clássico.

## <a name="supported-resources--configurations-for-migration"></a>Recursos com suporte & configurações para migração

### <a name="supported-resources-for-migration"></a>Recursos com suporte para migração
* Máquinas Virtuais
* Conjuntos de Disponibilidade
* Contas de Armazenamento
* Redes Virtuais
* Gateways VPN
* [Gateways de rota expressa](../expressroute/expressroute-howto-move-arm.md) _(na mesma assinatura que apenas na rede virtual)_
* Grupos de segurança de rede
* Tabelas de Rotas
* IPs Reservados

## <a name="supported-configurations-for-migration"></a>Configurações com suporte para migração
Esses recursos de IaaS clássicos têm suporte durante a migração

| Serviço | Configuração |
| --- | --- |
| Azure AD Domain Services | [Redes virtuais que contêm serviços do Azure AD Domain Services](../active-directory-domain-services/migrate-from-classic-vnet.md) |

## <a name="supported-scopes-of-migration"></a>Escopos de migração com suporte
Há quatro maneiras diferentes para concluir a migração de recursos de computação, rede e armazenamento.

* [Migração de máquinas virtuais (NÃO em uma rede virtual)](#migration-of-virtual-machines-not-in-a-virtual-network)
* [Migração de máquinas virtuais (em uma rede virtual)](#migration-of-virtual-machines-in-a-virtual-network)
* [Migração de contas de armazenamento](#migration-of-storage-accounts)
* [Migração de recursos não anexados](#migration-of-unattached-resources)

### <a name="migration-of-virtual-machines-not-in-a-virtual-network"></a>Migração de máquinas virtuais (NÃO em uma rede virtual)
No modelo de implantação do Resource Manager, a segurança de seus aplicativos é imposta por padrão. Todas as VMs precisam estar em uma rede virtual no modelo do Gerenciador de Recursos. As plataforma Azure reinicia (`Stop`, `Deallocate`, e `Start`) as VMs como parte da migração. Você tem duas opções de redes virtuais para as quais as Máquinas Virtuais serão migradas:

* Você pode solicitar que a plataforma crie uma nova rede virtual e migre a máquina virtual para a nova rede virtual.
* Você pode migrar a máquina virtual para uma rede virtual existente no Gerenciador de Recursos.

> [!NOTE]
> Nesse escopo de migração, as operações do “plano de gerenciamento” e do “plano de dados” podem não ser permitidas por determinado período durante a migração.
>

### <a name="migration-of-virtual-machines-in-a-virtual-network"></a>Migração de máquinas virtuais (em uma rede virtual)
Para a maioria das configurações de VM, somente os metadados são migrados entre os modelos Clássico e o Resource Manager. As VMs subjacentes estão em execução no mesmo hardware, na mesma rede e com o mesmo armazenamento. As operações do plano de gerenciamento talvez não tenham permissão por determinado período de tempo durante a migração. No entanto, o plano de dados continua funcionando. Ou seja, os aplicativos em execução nas VMs (clássicas) não incorrem em tempo de inatividade durante a migração.

Atualmente, não há suporte para as seguintes configurações. Se for adicionado suporte a elas no futuro, algumas VMs nessa configuração poderão incorrer em tempo de inatividade (passarão por operações de parar, desalocar e reiniciar a VM).

* Você tem mais de um conjunto de disponibilidade em um único serviço de nuvem.
* Você tem um ou mais conjuntos de disponibilidade e as VMs que não estão em um conjunto de disponibilidade em um único serviço de nuvem.

> [!NOTE]
> Nesse escopo de migração, o plano de gerenciamento pode não ser permitido por determinado período durante a migração. Para algumas configurações, conforme descrito acima, ocorre tempo de inatividade do plano de dados.
>

### <a name="migration-of-storage-accounts"></a>Migração de contas de armazenamento
Para permitir uma migração perfeita, você implantar VMs do Resource Manager em uma conta de armazenamento clássico. Com essa funcionalidade, recursos de computação e rede podem e devem ser migrados independentemente de contas de armazenamento. Depois de migrar suas Máquinas Virtuais e a Rede Virtual, você precisa migrar suas contas de armazenamento para concluir o processo de migração.

Se a sua conta de armazenamento não tiver discos associados ou dados de Máquinas Virtuais e tiver apenas blobs, arquivos, tabelas e filas, a migração para o Azure Resource Manager poderá ser feita como uma migração independente, sem dependências.

> [!NOTE]
> O modelo de implantação do Resource Manager não tem o conceito de discos e imagens clássicas. Quando a conta de armazenamento é migrada, os discos e imagens clássicos não ficarão visíveis na pilha do Resource Manager, mas os VHDs de backup permanecem na conta de armazenamento.

As capturas de tela a seguir mostram como atualizar uma conta de armazenamento clássico para uma conta de armazenamento Azure Resource Manager usando portal do Azure:
1. Entre no [portal do Azure](https://portal.azure.com).
2. Navegue para sua conta de armazenamento.
3. Na seção **configurações** , clique em **migrar para o ARM**.
4. Clique em **validar** para determinar a viabilidade de migração.
5. Se a validação for aprovada, clique em **preparar** para criar uma conta de armazenamento migrada.
6. Digite **Sim** para confirmar a migração e clique em **confirmar** para concluir a migração.

    ![Validar conta de armazenamento](../../includes/media/storage-account-upgrade-classic/storage-migrate-resource-manager-1.png)
    
    ![Preparar conta de armazenamento](../../includes/media/storage-account-upgrade-classic/storage-migrate-resource-manager-2.png)
    
    ![Finalizar migração da conta de armazenamento](../../includes/media/storage-account-upgrade-classic/storage-migrate-resource-manager-3.png)

### <a name="migration-of-unattached-resources"></a>Migração de recursos não anexados
Contas de armazenamento sem discos associados ou dados de máquinas virtuais podem ser migradas independentemente.

Grupos de Segurança de Rede, Tabelas de Rotas e IPs Reservados que não estão associadas a Máquinas Virtuais e Redes Virtuais podem ser também migrados de forma independente.

<br>

## <a name="unsupported-features-and-configurations"></a>Recursos e configurações sem suporte
Alguns recursos e configurações não são suportados atualmente; as seções a seguir descrevem nossas recomendações em torno deles.

### <a name="unsupported-features"></a>Recursos sem suporte
Atualmente, não há suporte para os seguintes recursos. Se preferir, você pode remover essas configurações, migrar as VMs e, em seguida, habilitar as configurações novamente no modelo de implantação do Gerenciador de Recursos.

| Provedor de recursos | Recurso | Recomendação |
| --- | --- | --- |
| Computação | Discos de máquina virtual não associados. | Os blobs VHD por trás desses discos serão migrados quando a Conta de Armazenamento for migrada |
| Computação | Imagens de máquinas virtuais. | Os blobs VHD por trás desses discos serão migrados quando a Conta de Armazenamento for migrada |
| Rede | ACLs de ponto de extremidade. | Remova as ACLs de Ponto de Extremidade e repita a migração. |
| Rede | Gateway de Aplicativo | Remova o Gateway de Aplicativo antes de iniciar a migração e crie novamente o Gateway de Aplicativo após a conclusão da migração. |
| Rede | Redes virtuais usando Emparelhamento VNet. | Migre a Rede Virtual para o Gerenciador de Recursos e depois emparelhe. Saiba mais sobre [Emparelhamento de VNet](../virtual-network/virtual-network-peering-overview.md). |

### <a name="unsupported-configurations"></a>Configurações sem suporte
Atualmente, não há suporte para as seguintes configurações.

| Serviço | Configuração | Recomendação |
| --- | --- | --- |
| Gerenciador de Recursos |Controle de acesso de Role-Based (RBAC) para recursos clássicos |Como o URI dos recursos é modificado após a migração, é recomendável planejar as atualizações da política de RBAC que precisam ocorrer após a migração. |
| Computação |Várias sub-redes associadas a uma VM |Atualize a configuração de sub-rede para referenciar apenas uma sub-rede. Isso poderá exigir que você remova um NIC secundário (que está referenciado a outra sub-rede) da máquina virtual e anexá-lo novamente depois que a migração for concluída. |
| Computação |Máquinas virtuais que pertencem a uma rede virtual, mas que não têm uma sub-rede explícita atribuída |Opcionalmente, você pode excluir a VM. |
| Computação |Máquinas virtuais que têm alertas e políticas de Escala Automática |A migração passa e essas configurações serão descartadas. É altamente recomendável que você avalie seu ambiente antes de fazer a migração. Se preferir, você pode redefinir as configurações de alerta após a conclusão da migração. |
| Computação |Extensões de VM do XML (BGInfo 1.\*, Depurador, Implantação da Web e Depuração Remota do Visual Studio) |Isso não tem suporte. Recomendamos que você remova essas extensões da máquina virtual para continuar a migração, ou elas serão eliminadas automaticamente durante o processo de migração. |
| Computação |Diagnóstico de inicialização com o armazenamento Premium |Desabilite o recurso de Diagnóstico de Inicialização para as VMs antes de continuar com a migração. Você pode habilitar novamente o diagnóstico de inicialização na pilha do Gerenciador de Recursos após a migração ser concluída. Além disso, os blobs que estão sendo usados para captura de tela e logs seriais devem ser excluídos para que você não seja cobrado por eles. |
| Computação | Serviços de nuvem que contêm funções de trabalho/web | Não há suporte para esse recurso no momento. |
| Computação | Serviços de nuvem que contêm mais de um conjunto de disponibilidade ou vários conjuntos de disponibilidade. |Não há suporte para esse recurso no momento. Mova as Máquinas Virtuais para a mesmo conjunto de disponibilidade antes de fazer a migração. |
| Computação | VM com a extensão de Central de Segurança do Azure | A Central de Segurança do Azure instala automaticamente extensões em suas Máquinas Virtuais a fim de monitorar a segurança e emitir alertas. Essas extensões normalmente são instaladas automaticamente se a política da Central de Segurança do Azure for habilitada na assinatura. Para migrar as máquinas virtuais, desabilite a política da central de segurança na assinatura que removerá a extensão de monitoramento da Central de Segurança das máquinas virtuais. |
| Computação | VM com extensão de backup ou instantâneo | Essas extensões são instaladas em uma máquina Virtual configurada com o serviço de Backup do Azure. Enquanto não houver suporte para a migração dessas VMs, siga as diretrizes descritas [aqui](./migration-classic-resource-manager-faq.md#i-backed-up-my-classic-vms-in-a-vault-can-i-migrate-my-vms-from-classic-mode-to-resource-manager-mode-and-protect-them-in-a-recovery-services-vault) para manter os backups feitos antes da migração.  |
| Computação | VM com extensão Azure Site Recovery | Essas extensões são instaladas em uma máquina virtual configurada com o serviço de Azure Site Recovery. Enquanto a migração de armazenamento usada com Site Recovery funcionará, a replicação atual será afetada. Você precisa desabilitar e habilitar a replicação de VM após a migração de armazenamento. |
| Rede |Redes virtuais que contêm máquinas virtuais e funções de trabalho/web |Não há suporte para esse recurso no momento. Mova as funções Web/Trabalho para as suas próprias redes virtuais antes de fazer a migração. Depois que a rede virtual clássica for migrada, a rede virtual do Azure Resource Manager pode ser emparelhada com a rede virtual clássica para obter uma configuração semelhante como antes.|
| Rede | Circuitos do ExpressRoute clássico |Não há suporte para esse recurso no momento. Esses circuitos precisam ser migrados para o Azure Resource Manager antes da migração do IaaS ser iniciada. Para saber mais, consulte [Movimentação dos circuitos do ExpressRoute do modelo de implantação clássico para o Resource Manager](../expressroute/expressroute-move.md).|
| Serviço de aplicativo do Azure |Redes virtuais que contêm ambientes do Serviço de Aplicativo |Não há suporte para esse recurso no momento. |
| Azure HDInsight |Redes virtuais que contêm serviços do HDInsight |Não há suporte para esse recurso no momento. |
| Serviços de Ciclo de Vida do Microsoft Dynamics |Redes virtuais que contêm máquinas virtuais gerenciadas pelos Serviços de Ciclo de Vida do Microsoft Dynamics |Não há suporte para esse recurso no momento. |
| Gerenciamento de API do Azure |Redes virtuais que contêm implantações do Gerenciamento de API do Azure |Não há suporte para esse recurso no momento. Para migrar a VNET IaaS, altere a VNET da implantação do Gerenciamento de API, que é uma operação sem tempo de inatividade. |

## <a name="next-steps"></a>Próximas etapas

* [Análise técnica aprofundada sobre a migração com suporte da plataforma do clássico para o Azure Resource Manager](migration-classic-resource-manager-deep-dive.md)
* [Planejamento para a migração de recursos de IaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-plan.md)
* [Usar o PowerShell para migrar recursos de IaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-ps.md)
* [Usar a CLI para migrar recursos de IaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-cli.md)
* [Ferramentas da comunidade para ajudar com a migração de recursos de IaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-community-tools.md)
* [Examinar os erros de migração mais comuns](migration-classic-resource-manager-errors.md)
* [Confira as perguntas mais frequentes sobre a migração de recursos de IaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-faq.md)
