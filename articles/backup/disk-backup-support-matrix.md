---
title: Matriz de suporte de Backup de Disco do Azure
description: Fornece um resumo das configurações de suporte e limitações do backup em disco do Azure.
ms.topic: conceptual
ms.date: 01/07/2021
ms.custom: references_regions
ms.openlocfilehash: 88ec26837cc8f69c1e84c77ea6b57ce16e462e0a
ms.sourcegitcommit: c94e282a08fcaa36c4e498771b6004f0bfe8fb70
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105612850"
---
# <a name="azure-disk-backup-support-matrix"></a>Matriz de suporte de Backup de Disco do Azure

Você pode usar o [backup do Azure](./backup-overview.md) para proteger os discos do Azure. Este artigo resume a disponibilidade da região, os cenários com suporte e as limitações.

## <a name="supported-regions"></a>Regiões com suporte

O backup em disco do Azure está disponível nas seguintes regiões: oeste dos EUA, oeste dos EUA 2, Oeste EUA Central, leste dos EUA, leste dos EUA 2, EUA Central, Sul EUA Central, norte EUA Central, Canadá central, sul do Brasil, norte da África do Sul, Sul do Reino Unido, Oeste do Reino Unido, Europa Ocidental, Europa Setentrional, Norte da Suíça, Oeste da Suíça, Centro-oeste da Alemanha, França central, Noruega Oriental, Norte dos EAU, Austrália Central, Austrália Central , Leste da Austrália, Coreia central, sul da Coreia, leste do Japão, oeste do Japão, Ásia Oriental, Sudeste Asiático, Índia central. 

Mais regiões serão anunciadas quando forem disponibilizadas.

## <a name="limitations"></a>Limitações

- O backup em disco do Azure tem suporte para Managed Disks do Azure, incluindo discos compartilhados (SSDs Premium compartilhado). Não há suporte para discos não gerenciados. Atualmente, essa solução não dá suporte a discos ultra, incluindo discos ultra compartilhados, devido à falta de recursos de instantâneo.

- O backup em disco do Azure dá suporte ao backup de Acelerador de Gravação disco. No entanto, durante a restauração, o disco seria restaurado como um disco normal. O cache acelerado de gravação pode ser habilitado no disco após montá-lo em uma VM.

- O backup do Azure fornece backup de camada operacional (instantâneo) de discos gerenciados do Azure com suporte para vários backups por dia. Os backups não são copiados para o cofre de backup.

- Atualmente, a opção de recuperação de Original-Location (OLR) para restaurar substituindo os discos de origem existentes de onde os backups foram feitos não tem suporte. Você pode restaurar do ponto de recuperação para criar um novo disco no mesmo grupo de recursos do disco de origem do qual os backups foram feitos ou em qualquer outro grupo de recursos. Isso é conhecido como ALR (recuperação de Alternate-Location).

- O backup do Azure para Managed Disks usa instantâneos incrementais, que são limitados a 200 instantâneos por disco. Para permitir que você faça backup sob demanda com exceção de backups agendados, a política de backup limita o total de backups a 180. Saiba mais sobre o [instantâneo incremental](../virtual-machines/disks-incremental-snapshots.md#restrictions) para discos gerenciados.

- [Os limites de serviço e assinatura](../azure-resource-manager/management/azure-subscription-service-limits.md#virtual-machine-disk-limits) do Azure se aplicam ao número total de instantâneos de disco por região por assinatura.

- Não há suporte para instantâneos de ponto no tempo de vários discos anexados a uma máquina virtual.

- O cofre de backup e os discos de backup podem estar na mesma assinatura ou em assinaturas diferentes. No entanto, o cofre de backup e o disco para backup devem estar na mesma região.

- Os discos para backup e o grupo de recursos de instantâneo em que os instantâneos são armazenados localmente devem estar na mesma assinatura.

- Há suporte para a restauração de um disco do backup para o mesmo ou para uma assinatura diferente. No entanto, o disco restaurado será criado na mesma região do instantâneo.

- Os discos criptografados em ADE não podem ser protegidos.

- Há suporte para discos criptografados com chaves gerenciadas por plataforma ou chaves gerenciadas pelo cliente. No entanto, a restauração falhará para os pontos de restauração de um disco que é criptografado usando chaves gerenciadas pelo cliente (CMK) se a chave do Key Vault do conjunto de criptografia de disco estiver desabilitada ou excluída.

- Atualmente, a política de backup não pode ser modificada e o grupo de recursos de instantâneo atribuído a uma instância de backup quando você configura o backup de um disco não pode ser alterado.

- Atualmente, a experiência de portal do Azure para configurar o backup de discos é limitada a um máximo de 20 discos da mesma assinatura.

- O backup em disco do Azure dá suporte ao PowerShell. No momento, não há suporte para CLI do Azure.

- Ao configurar o backup, o disco selecionado para backup e o grupo de recursos de instantâneo em que os instantâneos devem ser armazenados deve fazer parte da mesma assinatura. Você não pode criar um instantâneo incremental para um disco específico fora da assinatura desse disco. Saiba mais sobre [instantâneos incrementais](../virtual-machines/disks-incremental-snapshots.md#restrictions) do disco gerenciado. Para obter mais informações sobre como escolher um grupo de recursos de instantâneo, consulte  [Configurar backup](backup-managed-disks.md#configure-backup).

- Para operações de backup e restauração bem-sucedidas, as atribuições de função são exigidas pela identidade gerenciada do cofre de backup. Use apenas as definições de função fornecidas na documentação do. O uso de outras funções, como Owner, Contributor e assim por diante, não tem suporte. Você pode enfrentar problemas de permissão se começar a configurar operações de backup ou restauração logo após atribuir funções. Isso ocorre porque as atribuições de função levam alguns minutos para entrar em vigor.

- Os discos gerenciados permitem alterar o nível de desempenho na implantação ou posteriormente sem alterar o tamanho do disco. A solução de backup em disco do Azure dá suporte às alterações de nível de desempenho no disco de origem que está sendo submetido a backup. Durante a restauração, o nível de desempenho do disco restaurado será o mesmo do disco de origem no momento do backup. Siga a documentação [aqui](../virtual-machines/disks-performance-tiers-portal.md) para alterar o nível de desempenho do disco após a operação de restauração.

- O suporte a [links privados](../virtual-machines/disks-enable-private-links-for-import-export-portal.md) para discos gerenciados permite que você restrinja a exportação e a importação de discos gerenciados para que ele ocorra apenas dentro de sua rede virtual do Azure. O backup em disco do Azure dá suporte ao backup de discos que têm pontos de extremidade privados habilitados. Isso não inclui os dados de backup ou instantâneos para serem acessíveis por meio do ponto de extremidade privado.

- Você pode excluir uma instância de backup, que irá parar o backup e também excluir todos os dados de backup. No momento, não é possível desabilitar um backup, pois a opção **parar o backup e reter os dados de backup** não é suportada.

## <a name="next-steps"></a>Próximas etapas

- [Fazer backup do Azure Managed Disks](backup-managed-disks.md)