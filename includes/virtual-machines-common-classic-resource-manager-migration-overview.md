---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: virtual-machines
author: singhkays
ms.service: virtual-machines
ms.topic: include
ms.date: 04/25/2019
ms.author: kasing
ms.custom: include file
ms.openlocfilehash: de2e33ceb182383d9529bfe41afffbbf28e1e493
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67671312"
---
# <a name="platform-supported-migration-of-iaas-resources-from-classic-to-azure-resource-manager"></a>Migração de recursos de IaaS com suporte da plataforma do clássico para o Azure Resource Manager
Este artigo descreve como migrar recursos de infra-estrutura como serviço (IaaS) dos modelos de implantação do Classic para o Resource Manager e detalha como conectar recursos dos dois modelos de implantação que coexistem na sua assinatura usando gateways de site a site de rede virtual. Você pode ler mais sobre os [recursos e benefícios do Azure Resource Manager](../articles/azure-resource-manager/resource-group-overview.md). 

## <a name="goal-for-migration"></a>Meta de migração
O Gerenciador de Recursos possibilita implantar aplicativos complexos por meio de modelos, configurar máquinas virtuais usando extensões de VM e incorporar o gerenciamento de acesso e a marcação. O Azure Resource Manager inclui implantação paralela e escalonável para máquinas virtuais em conjuntos de disponibilidade. O novo modelo também oferece gerenciamento de ciclo de vida de computação, rede e armazenamento de maneira independente. Por fim, há um enfoque para habilitar a segurança por padrão com a imposição de máquinas virtuais em uma rede virtual.

Há suporte para quase todos os recursos do modelo de implantação clássica referentes a computação, rede e armazenamento no Azure Resource Manager. Para aproveitar os novos recursos no Azure Resource Manager, você pode migrar as implantações existentes do modelo de implantação clássico.

## <a name="supported-resources-for-migration"></a>Recursos com suporte para migração
Esses recursos de IaaS clássicos têm suporte durante a migração

* Máquinas Virtuais
* Conjuntos de Disponibilidade
* Serviços de Nuvem com Máquinas Virtuais
* Contas de armazenamento
* Redes Virtuais
* Gateways VPN
* Express Route Gateways _(na mesma assinatura como Rede Virtual somente)_
* Grupos de segurança de rede
* Tabelas de Rotas
* IPs Reservados

## <a name="supported-scopes-of-migration"></a>Escopos de migração com suporte
Há quatro maneiras diferentes para concluir a migração de recursos de computação, rede e armazenamento.

* [Migração de máquinas virtuais (NÃO em uma rede virtual)](#migration-of-virtual-machines-not-in-a-virtual-network)
* [Migração de máquinas virtuais (em uma rede virtual)](#migration-of-virtual-machines-in-a-virtual-network)
* [Migração de contas de armazenamento](#migration-of-storage-accounts)
* [Migração de recursos desanexados](#migration-of-unattached-resources)

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

As capturas de tela a seguir mostram como atualizar uma conta de armazenamento clássico para uma conta de armazenamento do Azure Resource Manager usando o portal do Azure:
1. Entre no [Portal do Azure](https://portal.azure.com).
2. Navegue até sua conta de armazenamento.
3. No **as configurações** seção, clique em **migrar para ARM**.
4. Clique em **validar** para determinar a viabilidade de migração.
5. Se a validação é bem-sucedida, clique em **preparar** para criar uma conta de armazenamento migrada.
6. Tipo de **yes** para confirmar a migração e clique em **confirmar** para concluir a migração.

    ![Validar a conta de armazenamento](../includes/media/storage-account-upgrade-classic/storage-migrate-resource-manager-1.png)
    
    ![Preparar a conta de armazenamento](../includes/media/storage-account-upgrade-classic/storage-migrate-resource-manager-2.png)
    
    ![Finalizar a migração da conta de armazenamento](../includes/media/storage-account-upgrade-classic/storage-migrate-resource-manager-3.png)

### <a name="migration-of-unattached-resources"></a>Migração de recursos não anexados
Contas de armazenamento sem discos associados ou dados de máquinas virtuais podem ser migradas independentemente.

Grupos de Segurança de Rede, Tabelas de Rotas e IPs Reservados que não estão associadas a Máquinas Virtuais e Redes Virtuais podem ser também migrados de forma independente.

<br>

## <a name="unsupported-features-and-configurations"></a>Recursos e configurações sem suporte
Alguns recursos e configurações não são suportados atualmente; as seções a seguir descrevem nossas recomendações em torno deles.

### <a name="unsupported-features"></a>Recursos sem suporte
Atualmente, não há suporte para os seguintes recursos. Se preferir, você pode remover essas configurações, migrar as VMs e, em seguida, habilitar as configurações novamente no modelo de implantação do Gerenciador de Recursos.

| Provedor de recursos | Recurso | Recomendações |
| --- | --- | --- |
| Computação | Discos de máquina virtual não associados. | Os blobs VHD por trás desses discos serão migrados quando a Conta de Armazenamento for migrada |
| Computação | Imagens de máquinas virtuais. | Os blobs VHD por trás desses discos serão migrados quando a Conta de Armazenamento for migrada |
| Rede | ACLs de ponto de extremidade. | Remova as ACLs de Ponto de Extremidade e repita a migração. |
| Rede | Gateway de Aplicativo | Remova o Gateway de Aplicativo antes de iniciar a migração e crie novamente o Gateway de Aplicativo após a conclusão da migração. |
| Rede | Redes virtuais usando Emparelhamento VNet. | Migre a Rede Virtual para o Gerenciador de Recursos e depois emparelhe. Saiba mais sobre [Emparelhamento de VNet](../articles/virtual-network/virtual-network-peering-overview.md). |

### <a name="unsupported-configurations"></a>Configurações sem suporte
Atualmente, não há suporte para as seguintes configurações.

| Serviço | Configuração | Recomendações |
| --- | --- | --- |
| Gerenciador de Recursos |Controle de acesso baseado em função (RBAC) para recursos clássicos |Como o URI dos recursos é modificado após a migração, é recomendável planejar as atualizações da política de RBAC que precisam ocorrer após a migração. |
| Computação |Várias sub-redes associadas a uma VM |Atualize a configuração de sub-rede para referenciar apenas uma sub-rede. Isso poderá exigir que você remova um NIC secundário (que está referenciado a outra sub-rede) da máquina virtual e anexá-lo novamente depois que a migração for concluída. |
| Computação |Máquinas virtuais que pertencem a uma rede virtual, mas que não têm uma sub-rede explícita atribuída |Opcionalmente, você pode excluir a VM. |
| Computação |Máquinas virtuais que têm alertas e políticas de Escala Automática |A migração passa e essas configurações serão descartadas. É altamente recomendável que você avalie seu ambiente antes de fazer a migração. Se preferir, você pode redefinir as configurações de alerta após a conclusão da migração. |
| Computação |Extensões de VM do XML (BGInfo 1.*, Depurador, Implantação da Web e Depuração Remota do Visual Studio) |Não há suporte para isso. Recomendamos que você remova essas extensões da máquina virtual para continuar a migração, ou elas serão eliminadas automaticamente durante o processo de migração. |
| Computação |Diagnóstico de inicialização com o armazenamento Premium |Desabilite o recurso de Diagnóstico de Inicialização para as VMs antes de continuar com a migração. Você pode habilitar novamente o diagnóstico de inicialização na pilha do Gerenciador de Recursos após a migração ser concluída. Além disso, os blobs que estão sendo usados para captura de tela e logs seriais devem ser excluídos para que você não seja cobrado por eles. |
| Computação | Serviços de nuvem que contêm funções de trabalho/web | Não há suporte para esse recurso no momento. |
| Computação | Serviços de nuvem que contêm mais de um conjunto de disponibilidade ou vários conjuntos de disponibilidade. |Não há suporte para esse recurso no momento. Mova as Máquinas Virtuais para a mesmo conjunto de disponibilidade antes de fazer a migração. |
| Computação | VM com a extensão de Central de Segurança do Azure | A Central de Segurança do Azure instala automaticamente extensões em suas Máquinas Virtuais a fim de monitorar a segurança e emitir alertas. Essas extensões normalmente são instaladas automaticamente se a política da Central de Segurança do Azure for habilitada na assinatura. Para migrar as máquinas virtuais, desabilite a política da central de segurança na assinatura que removerá a extensão de monitoramento da Central de Segurança das máquinas virtuais. |
| Computação | VM com extensão de backup ou instantâneo | Essas extensões são instaladas em uma máquina Virtual configurada com o serviço de Backup do Azure. Enquanto não houver suporte para a migração dessas VMs, siga as diretrizes descritas [aqui](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-faq#vault) para manter os backups feitos antes da migração.  |
| Rede |Redes virtuais que contêm máquinas virtuais e funções de trabalho/web |Não há suporte para esse recurso no momento. Mova as funções Web/Trabalho para as suas próprias redes virtuais antes de fazer a migração. Depois que a rede virtual clássica for migrada, a rede virtual do Azure Resource Manager pode ser emparelhada com a rede virtual clássica para obter uma configuração semelhante como antes.|
| Rede | Circuitos do ExpressRoute clássico |Não há suporte para esse recurso no momento. Esses circuitos precisam ser migrados para o Azure Resource Manager antes da migração do IaaS ser iniciada. Para saber mais, consulte [Movimentação dos circuitos do ExpressRoute do modelo de implantação clássico para o Resource Manager](../articles/expressroute/expressroute-move.md).|
| Serviço de aplicativo do Azure |Redes virtuais que contêm ambientes do Serviço de Aplicativo |Não há suporte para esse recurso no momento. |
| Azure HDInsight |Redes virtuais que contêm serviços do HDInsight |Não há suporte para esse recurso no momento. |
| Serviços de Ciclo de Vida do Microsoft Dynamics |Redes virtuais que contêm máquinas virtuais gerenciadas pelos Serviços de Ciclo de Vida do Microsoft Dynamics |Não há suporte para esse recurso no momento. |
| Azure AD Domain Services |Redes virtuais que contêm serviços do Azure AD Domain Services |Não há suporte para esse recurso no momento. |
| Gerenciamento de API do Azure |Redes virtuais que contêm implantações do Gerenciamento de API do Azure |Não há suporte para esse recurso no momento. Para migrar a VNET IaaS, altere a VNET da implantação do Gerenciamento de API, que é uma operação sem tempo de inatividade. |
