---
title: Visão geral do Backup de Disco do Azure
description: Saiba mais sobre a solução de backup em disco do Azure.
ms.topic: conceptual
ms.date: 01/07/2021
ms.openlocfilehash: 9449fdc57909cb143d381ae074913c79d24c8893
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/25/2021
ms.locfileid: "105107288"
---
# <a name="overview-of-azure-disk-backup"></a>Visão geral do Backup de Disco do Azure

O backup em disco do Azure é uma solução de backup nativa baseada em nuvem que protege seus dados em discos gerenciados. É uma solução simples, segura e econômica que permite configurar a proteção de discos gerenciados em algumas etapas. Garante que você possa recuperar seus dados em um cenário de desastre.

O backup em disco do Azure oferece uma solução completa que fornece gerenciamento de ciclo de vida de instantâneos para discos gerenciados automatizando a criação periódica de instantâneos e retendo-o para duração configurada usando a política de backup. Você pode gerenciar os instantâneos de disco sem custo de infraestrutura zero e sem a necessidade de script personalizado ou qualquer sobrecarga de gerenciamento. Esta é uma solução de backup consistente com falhas que faz backup pontual de um disco gerenciado usando [instantâneos incrementais](../virtual-machines/disks-incremental-snapshots.md) com suporte para vários backups por dia. Ela também é uma solução sem agente e não afeta o desempenho do aplicativo de produção. Ele dá suporte a backup e restauração de sistemas operacionais e de discos de dados (incluindo discos compartilhados), independentemente de estarem ou não conectados a uma máquina virtual do Azure em execução.

Se você precisar de backup consistente com o aplicativo de máquina virtual, incluindo os discos de dados, ou uma opção para restaurar uma máquina virtual inteira do backup, restaurar um arquivo ou uma pasta ou restaurar para uma região secundária, use a solução de [backup da VM do Azure](backup-azure-vms-introduction.md) . O backup do Azure oferece suporte lado a lado para backup de discos gerenciados usando o backup em disco além das soluções de [backup de VM do Azure](./backup-azure-vms-introduction.md) . Isso é útil quando você precisa de backups consistentes do aplicativo uma vez por dia de máquinas virtuais e também de backups mais frequentes de discos do sistema operacional ou de um disco de dados específico que são consistentes com falha e não afetam o desempenho do aplicativo de produção.

O backup em disco do Azure é integrado ao centro de backup, que fornece uma **única experiência de gerenciamento unificada** no Azure para empresas para controlar, monitorar, operar e analisar backups em escala.

## <a name="key-benefits-of-disk-backup"></a>Principais benefícios do backup em disco

O backup em disco do Azure é uma solução consistente sem agente e com falha que usa [instantâneos incrementais](../virtual-machines/disks-incremental-snapshots.md) e oferece as seguintes vantagens:

- Backups mais frequentes e rápidos sem interromper a máquina virtual.
- Não afeta o desempenho do aplicativo de produção.
- Nenhuma preocupação de segurança, pois não requer a execução de scripts personalizados nem a instalação de agentes.
- Uma solução econômica para fazer backup de discos específicos em comparação com o backup de uma máquina virtual inteira.

A solução de backup em disco do Azure é útil nos seguintes cenários:

- Necessidade de backups frequentes por dia sem que o aplicativo esteja sendo inativo
- Aplicativos em execução em um cenário de cluster: o cluster de failover do Windows Server e os clusters do Linux estão gravando em discos compartilhados
- Necessidade específica de backup sem agente devido a questões de segurança ou desempenho no aplicativo
- O backup consistente com o aplicativo da VM não é viável, pois os aplicativos de linha de negócios não dão suporte a Serviço de Cópias de Sombra de Volume (VSS).

Considere o backup em disco do Azure em cenários em que:

- um aplicativo de missão crítica está em execução em uma máquina virtual do Azure que exige vários backups por dia para atender ao objetivo do ponto de recuperação, mas sem afetar o desempenho do ambiente de produção ou do aplicativo
- sua organização ou regulamento do setor restringe a instalação de agentes devido a questões de segurança
- executar scripts anteriores ou posteriores personalizados e invocar congelar e descongelar em máquinas virtuais Linux para obter o backup consistente com o aplicativo coloca uma sobrecarga indevida na disponibilidade da carga de trabalho de produção
- os aplicativos em contêineres executados no serviço kubernetes do Azure (cluster AKS) estão usando discos gerenciados como armazenamento persistente. Hoje, você precisa fazer backup do disco gerenciado por meio de scripts de automação que são difíceis de gerenciar.
- um disco gerenciado está mantendo dados críticos para os negócios, usados como um compartilhamento de arquivos ou contém arquivos de backup de banco e você deseja otimizar o custo de backup não investindo no backup de VM do Azure
- Você tem muitas máquinas virtuais de disco único do Linux e do Windows (ou seja, uma máquina virtual com apenas um disco do sistema operacional e sem discos de dados anexados) que hospedam o servidor Webou computadores sem estado ou serve como um ambiente de preparo com definições de configuração de aplicativo e você precisa de uma solução de backup econômica para proteger o disco do sistema operacional. Por exemplo, para disparar um backup rápido sob demanda antes de atualizar ou aplicar patch na máquina virtual
- uma máquina virtual está executando uma configuração de sistema operacional que não é suportada pela solução de backup de VM do Azure (por exemplo, servidor do Windows 2008 32 bits)

## <a name="how-the-backup-and-restore-process-works"></a>Como funciona o processo de backup e restauração

- A primeira etapa na configuração do backup para o Azure Managed disks é a criação de um [cofre de backup](backup-vault-overview.md). O cofre fornece uma exibição consolidada dos backups configurados em diferentes cargas de trabalho.

- Em seguida, crie uma política de backup que permita configurar a frequência de backup e a duração da retenção.

- Para configurar o backup, vá para o cofre de backup, atribua uma política de backup, selecione o disco gerenciado que precisa ser armazenado em backup e forneça um grupo de recursos no qual os instantâneos devem ser armazenados e gerenciados. O backup do Azure dispara automaticamente os trabalhos de backup agendados que criam um instantâneo incremental do disco de acordo com a frequência de backup. Os instantâneos mais antigos são excluídos de acordo com a duração da retenção especificada pela política de backup.

- O backup do Azure usa [instantâneos incrementais](../virtual-machines/disks-incremental-snapshots.md#restrictions) do disco gerenciado. Instantâneos incrementais são um backup pontual e econômico de discos gerenciados que são cobrados pelas alterações delta no disco desde o último instantâneo. Eles são sempre armazenados no armazenamento mais econômico, armazenamento de HDD padrão, independentemente do tipo de armazenamento dos discos pai. O primeiro instantâneo do disco ocupará o tamanho usado do disco e os instantâneos incrementais consecutivos armazenarão as alterações delta no disco desde o último instantâneo.

- Depois de configurar o backup de um disco gerenciado, uma instância de backup será criada no cofre de backup. Usando a instância de backup, você pode encontrar a integridade das operações de backup, disparar backups sob demanda e executar operações de restauração. Você também pode exibir a integridade de backups em vários cofres e instâncias de backup usando o centro de backup, que fornece um único painel de exibição de vidro.

- Para restaurar, basta selecionar o ponto de recuperação do qual você deseja restaurar o disco. Forneça o grupo de recursos em que o disco restaurado deve ser criado a partir do instantâneo. O backup do Azure fornece uma experiência de restauração instantânea, já que os instantâneos são armazenados localmente em sua assinatura.

- O cofre de backup usa identidade gerenciada para acessar outros recursos do Azure. Para configurar o backup de um disco gerenciado e restaurar do backup anterior, a identidade gerenciada do cofre de backup requer um conjunto de permissões no disco de origem, o grupo de recursos de instantâneo em que os instantâneos são criados e gerenciados e o grupo de recursos de destino no qual você deseja restaurar o backup. Você pode conceder permissões para a identidade gerenciada usando o controle de acesso baseado em função do Azure (RBAC do Azure). A identidade gerenciada é uma entidade de serviço de um tipo especial que pode ser usada apenas com recursos do Azure. Saiba mais sobre [identidades gerenciadas](../active-directory/managed-identities-azure-resources/overview.md).

- Atualmente, o backup em disco do Azure dá suporte ao backup operacional de discos gerenciados e não copia os backups para o armazenamento do cofre de backup. Consulte a [matriz de suporte](disk-backup-support-matrix.md)para obter uma lista detalhada de cenários com e sem suporte e disponibilidade de região.

## <a name="pricing"></a>Preços

O backup do Azure oferece uma solução de gerenciamento de ciclo de vida de instantâneo para proteger discos do Azure. Os instantâneos de disco criados pelo backup do Azure são armazenados no grupo de recursos em sua assinatura do Azure e incorrem em encargos de **armazenamento de instantâneos** . Você pode visitar [preços do disco gerenciado](https://azure.microsoft.com/pricing/details/managed-disks/) para obter mais detalhes sobre os preços do instantâneo. Como os instantâneos não são copiados para o cofre de backup, o backup do Azure não cobra uma taxa de **instância protegida** e o custo de **armazenamento de backup** não se aplica. Além disso, os instantâneos incrementais ocupam alterações delta desde o último instantâneo e sempre são armazenados no armazenamento Standard, independentemente do tipo de armazenamento dos discos gerenciados pelo pai e são cobrados de acordo com o preço do armazenamento padrão. Isso torna o backup em disco do Azure uma solução econômica.

## <a name="next-steps"></a>Próximas etapas

- [Matriz de suporte de Backup de Disco do Azure](disk-backup-support-matrix.md)
