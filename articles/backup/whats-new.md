---
title: Novidades no Backup do Azure
description: Saiba mais sobre os novos recursos no backup do Azure.
ms.topic: conceptual
ms.date: 11/11/2020
ms.openlocfilehash: c5e6734c6a962fa43d79fc90fdfaa85923b6339f
ms.sourcegitcommit: c94e282a08fcaa36c4e498771b6004f0bfe8fb70
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105612476"
---
# <a name="whats-new-in-azure-backup"></a>Novidades no Backup do Azure

O backup do Azure está constantemente aprimorando e liberando novos recursos que melhoram a proteção de seus dados no Azure. Esses novos recursos expandem sua proteção de dados para novos tipos de carga de trabalho, aumentam a segurança e melhoram a disponibilidade dos seus dados de backup. Eles também adicionam novos recursos de gerenciamento, monitoramento e automação.

Você pode saber mais sobre as novas versões ao marcar esta página ou [inscrevendo-se nas atualizações aqui](https://azure.microsoft.com/updates/?query=backup).

## <a name="updates-summary"></a>Resumo de atualizações

- Março de 2021
  - [O backup em disco do Azure já está disponível para o público geral](#azure-disk-backup-is-now-generally-available)
  - [O centro de backup já está disponível para o público geral](#backup-center-is-now-generally-available)
  - [Suporte de camada de arquivo para backup do Azure (em versão prévia)](#archive-tier-support-for-azure-backup-in-preview)
- Fevereiro de 2021
  - [Backup para BLOBs do Azure (em versão prévia)](#backup-for-azure-blobs-in-preview)
- Janeiro de 2021
  - [Backup em disco do Azure (em versão prévia)](#azure-disk-backup-in-preview)
  - [Criptografia em repouso usando chaves gerenciadas pelo cliente (disponibilidade geral)](#encryption-at-rest-using-customer-managed-keys)
- Novembro de 2020
  - [Modelo de Azure Resource Manager para backup de compartilhamento de arquivos do Azure (AFS)](#azure-resource-manager-template-for-afs-backup)
  - [Backups incrementais para bancos de dados SAP HANA em VMs do Azure (em versão prévia)](#incremental-backups-for-sap-hana-databases-in-preview)
- Setembro de 2020
  - [Centro de backup (em versão prévia)](#backup-center-in-preview)
  - [Fazer backup do banco de dados do Azure para PostgreSQL (em versão prévia)](#backup-azure-database-for-postgresql-in-preview)
  - [Backup e restauração de disco seletivo](#selective-disk-backup-and-restore)
  - [Restauração entre regiões para bancos de dados SQL Server e SAP HANA em VMs do Azure (em versão prévia)](#cross-region-restore-for-sql-server-and-sap-hana-in-preview)
  - [Suporte para backup de VMs com até 32 discos (disponibilidade geral)](#support-for-backup-of-vms-with-up-to-32-disks)
  - [Experiência de configuração de backup simplificada para SQL em VMs do Azure](#simpler-backup-configuration-for-sql-in-azure-vms)
  - [SAP HANA de backup em máquinas virtuais RHEL do Azure (em versão prévia)](#backup-sap-hana-in-rhel-azure-virtual-machines-in-preview)
  - [ZRS (armazenamento com redundância de zona) para dados de backup (em versão prévia)](#zone-redundant-storage-zrs-for-backup-data-in-preview)
  - [Exclusão reversível para cargas de trabalho de SQL Server e SAP HANA em VMs do Azure](#soft-delete-for-sql-server-and-sap-hana-workloads)

## <a name="azure-disk-backup-is-now-generally-available"></a>O backup em disco do Azure já está disponível para o público geral

O backup do Azure oferece gerenciamento de ciclo de vida de instantâneo para o Azure Managed Disks automatizando a criação periódica de instantâneos e retendo-os para durações configuradas usando a política de backup.

Para obter mais informações, consulte [visão geral do backup de disco do Azure](disk-backup-overview.md).

## <a name="backup-center-is-now-generally-available"></a>O centro de backup já está disponível para o público geral

O centro de backup simplifica o gerenciamento de proteção de dados em escala, permitindo que você descubra, controle, monitore, opere e otimize o gerenciamento de backup de um único console central.

Para obter mais informações, consulte [visão geral do centro de backup](backup-center-overview.md).

## <a name="archive-tier-support-for-azure-backup-in-preview"></a>Suporte de camada de arquivo para backup do Azure (em versão prévia)

O backup do Azure agora permite que você reduza o custo de backups de retenção de longo prazo com a disponibilidade da camada de arquivamento para máquinas virtuais do Azure e SQL Server em máquinas virtuais do Azure.

Para obter mais informações, consulte [suporte à camada de arquivo morto (versão prévia)](archive-tier-support.md).

## <a name="backup-for-azure-blobs-in-preview"></a>Backup para BLOBs do Azure (em versão prévia)

O backup operacional para BLOBs é uma solução de proteção de dados local gerenciada que permite proteger seus blobs de blocos de vários cenários de perda de dados, como corrupções, exclusões de BLOB e exclusão acidental da conta de armazenamento. Os dados são armazenados localmente na própria conta de armazenamento de origem e podem ser recuperados para um momento determinado, sempre que necessário. Portanto, ele fornece um meio simples, seguro e econômico para proteger seus BLOBs.

O backup operacional para BLOBs integra-se ao centro de backup, entre outros recursos de gerenciamento de backup, para fornecer um único painel de vidro que pode ajudá-lo a controlar, monitorar, operar e analisar backups em escala.

Para obter mais informações, consulte [visão geral do backup operacional para BLOBs do Azure (em versão prévia)](blob-backup-overview.md).

## <a name="azure-disk-backup-in-preview"></a>Backup em disco do Azure (em versão prévia)

O backup em disco do Azure oferece uma solução completa que fornece gerenciamento de ciclo de vida de instantâneo para o [Azure Managed disks](../virtual-machines/managed-disks-overview.md) automatizando a criação periódica de instantâneos e retendo-o por uma duração configurada usando a política de backup. Você pode gerenciar os instantâneos de disco sem custo de infraestrutura zero e sem a necessidade de script personalizado ou qualquer sobrecarga de gerenciamento. Esta é uma solução de backup consistente com falhas que faz backup pontual de um disco gerenciado usando [instantâneos incrementais](../virtual-machines/disks-incremental-snapshots.md) com suporte para vários backups por dia. Ela também é uma solução sem agente e não afeta o desempenho do aplicativo de produção. Ele dá suporte a backup e restauração de sistemas operacionais e de discos de dados (incluindo discos compartilhados), independentemente de estarem ou não conectados a uma máquina virtual do Azure em execução.

Para obter mais informações, consulte [backup em disco do Azure (em versão prévia)](disk-backup-overview.md).

## <a name="encryption-at-rest-using-customer-managed-keys"></a>Criptografia em repouso usando chaves gerenciadas pelo cliente

O suporte para criptografia em repouso usando chaves gerenciadas pelo cliente agora está disponível para o público geral. Isso lhe dá a capacidade de criptografar os dados de backup em seus cofres dos serviços de recuperação usando suas próprias chaves armazenadas em cofres de chaves do Azure. A chave de criptografia usada para criptografar backups no cofre dos serviços de recuperação pode ser diferente daquelas usadas para criptografar a origem. Os dados são protegidos usando uma DEK (chave de criptografia de dados) baseada em AES 256, que é, por sua vez, protegida usando as chaves armazenadas no Key Vault. Em comparação com a criptografia usando chaves gerenciadas pela plataforma (que está disponível por padrão), isso lhe dá mais controle sobre suas chaves e pode ajudá-lo a atender melhor às suas necessidades de conformidade.

Para obter mais informações, consulte [criptografia de dados de backup usando chaves gerenciadas pelo cliente](encryption-at-rest-with-cmk.md).

## <a name="azure-resource-manager-template-for-afs-backup"></a>Modelo de Azure Resource Manager para o backup AFS

O backup do Azure agora dá suporte à configuração de backup para compartilhamentos de arquivos do Azure existentes usando um modelo de Azure Resource Manager (ARM). O modelo configura a proteção para um compartilhamento de arquivos existente do Azure especificando os detalhes apropriados para o cofre dos serviços de recuperação e a política de backup. Opcionalmente, ele cria um cofre dos Serviços de Recuperação e uma política de backup e registra a conta de armazenamento que contém o compartilhamento de arquivo no cofre dos Serviços de Recuperação.

Para obter mais informações, consulte [modelos de Azure Resource Manager para o backup do Azure](backup-rm-template-samples.md).

## <a name="incremental-backups-for-sap-hana-databases-in-preview"></a>Backups incrementais para bancos de dados SAP HANA (em versão prévia)

O backup do Azure agora dá suporte a backups incrementais para SAP HANA bancos de dados hospedados em VMs do Azure. Isso permite backups mais rápidos e econômicos de seus dados de SAP HANA.

Para obter mais informações, consulte [várias opções disponíveis durante a criação de uma política de backup](sap-hana-faq-backup-azure-vm.md#policy) e [como criar uma política de backup para bancos de dados do SAP Hana](tutorial-backup-sap-hana-db.md#creating-a-backup-policy).

## <a name="backup-center-in-preview"></a>Centro de backup (em versão prévia)

O backup do Azure habilitou uma nova funcionalidade de gerenciamento nativo para gerenciar todo o seu espaço de backup a partir de um console central. O centro de backup fornece a capacidade de monitorar, operar, controlar e otimizar a proteção de dados em escala de maneira unificada consistente com as experiências de gerenciamento nativo do Azure.

Com o centro de backup, você obtém uma exibição agregada do seu inventário entre assinaturas, locais, grupos de recursos, cofres e até mesmo locatários usando o Azure Lighthouse. O centro de backup também é uma central de ações da qual você pode disparar suas atividades relacionadas a backup, como configurar backup, restauração, criação de políticas ou cofres, tudo a partir de um único lugar. Além disso, com a integração direta com o Azure Policy, agora você pode controlar seu ambiente e acompanhar a conformidade de uma perspectiva de backup. Políticas internas do Azure específicas ao backup do Azure também permitem que você configure backups em escala.

Para obter mais informações, consulte [visão geral do centro de backup](backup-center-overview.md).

## <a name="backup-azure-database-for-postgresql-in-preview"></a>Fazer backup do banco de dados do Azure para PostgreSQL (em versão prévia)

O backup do Azure e os serviços de banco de dados do Azure vêm juntos para criar uma solução de backup de classe empresarial para o Azure PostgreSQL (agora em versão prévia). Agora você pode atender às suas necessidades de proteção e conformidade de dados com uma política de backup controlada pelo cliente que permite a retenção de backups por até 10 anos. Com isso, você tem controle granular para gerenciar as operações de backup e restauração no nível de banco de dados individual. Da mesma forma, você pode restaurar em versões do PostgreSQL ou no armazenamento de BLOBs com facilidade.

Para obter mais informações, consulte [banco de dados do Azure para o backup do PostgreSQL](backup-azure-database-postgresql.md).

## <a name="selective-disk-backup-and-restore"></a>Backup e restauração de disco seletivo

O backup do Azure dá suporte ao backup de todos os discos (sistema operacional e dados) em uma VM juntas usando a solução de backup de máquina virtual. Agora, usando a funcionalidade de backup e restauração dos discos seletivos, você pode fazer backup de um subconjunto dos discos de dados em uma VM. Isso fornece uma solução eficiente e econômica para suas necessidades de backup e restauração. Cada ponto de recuperação contém apenas os discos que são incluídos na operação de backup.

Para obter mais informações, consulte [backup e restauração de disco seletivo para máquinas virtuais do Azure](selective-disk-backup-restore.md).

## <a name="cross-region-restore-for-sql-server-and-sap-hana-in-preview"></a>Restauração entre regiões para SQL Server e SAP HANA (em versão prévia)

Com a introdução da restauração entre regiões, agora você pode iniciar restaurações em uma região secundária para reduzir problemas reais de tempo de inatividade em uma região primária para seu ambiente. Isso faz com que a região secundária seja restaurada completamente controlada pelo cliente. O backup do Azure usa os dados de backup replicados para a região secundária para essas restaurações.

Agora, além do suporte para a restauração entre regiões para máquinas virtuais do Azure, o recurso foi estendido para restaurar bancos de dados SQL e SAP HANA em máquinas virtuais do Azure também.

Para obter mais informações, consulte [restauração entre regiões para bancos de dados SQL](restore-sql-database-azure-vm.md#cross-region-restore) e [restauração entre regiões para bancos de dados SAP Hana](sap-hana-db-restore.md#cross-region-restore).

## <a name="support-for-backup-of-vms-with-up-to-32-disks"></a>Suporte para backup de VMs com até 32 discos

Até agora, o backup do Azure tem suporte de 16 discos gerenciados por VM. Agora, o backup do Azure dá suporte ao backup de até 32 discos gerenciados por VM.

Para obter mais informações, consulte a [matriz de suporte de armazenamento de VM](backup-support-matrix-iaas.md#vm-storage-support).

## <a name="simpler-backup-configuration-for-sql-in-azure-vms"></a>Configuração de backup mais simples para SQL em VMs do Azure

A configuração de backups para seu SQL Server em VMs do Azure agora é ainda mais fácil com a configuração de backup embutida integrada no painel de VM do portal do Azure. Em apenas algumas etapas, você pode habilitar o backup de seus SQL Server para proteger todos os bancos de dados existentes, bem como aqueles que são adicionados no futuro.

Para obter mais informações, consulte [fazer backup de um SQL Server no painel VM](backup-sql-server-vm-from-vm-pane.md).

## <a name="backup-sap-hana-in-rhel-azure-virtual-machines-in-preview"></a>SAP HANA de backup em máquinas virtuais RHEL do Azure (em versão prévia)

O backup do Azure é a solução de backup nativo para o Azure e é BackInt certificado pelo SAP. O backup do Azure agora adicionou suporte para o Red Hat Enterprise Linux (RHEL), um dos sistemas operacionais Linux mais amplamente usados em execução SAP HANA.

Para obter mais informações, consulte a [matriz de suporte do cenário de backup do banco de dados SAP Hana](sap-hana-backup-support-matrix.md#scenario-support).

## <a name="zone-redundant-storage-zrs-for-backup-data-in-preview"></a>ZRS (armazenamento com redundância de zona) para dados de backup (em versão prévia)

O armazenamento do Azure fornece um excelente equilíbrio entre alto desempenho, alta disponibilidade e alta resiliência de dados com suas variadas opções de redundância. O backup do Azure permite que você estenda esses benefícios para os dados de backup também, com opções para armazenar seus backups em LRS (armazenamento com redundância local) e GRS (armazenamento com redundância geográfica). Agora, há opções de durabilidade adicionais com suporte adicionado para ZRS (armazenamento com redundância de zona).

Para obter mais informações, consulte [definir a redundância de armazenamento para o cofre dos serviços de recuperação](backup-create-rs-vault.md#set-storage-redundancy).

## <a name="soft-delete-for-sql-server-and-sap-hana-workloads"></a>Exclusão reversível para cargas de trabalho de SQL Server e SAP HANA

Preocupações sobre problemas de segurança, como malware, ransomware e invasão, estão aumentando. Esses problemas de segurança podem ser dispendiosos em termos de dinheiro e dados. Para se proteger contra tais ataques, o backup do Azure fornece recursos de segurança para ajudar a proteger os dados de backup mesmo após a exclusão.

Um desses recursos é exclusão reversível. Com a exclusão reversível, mesmo que um ator mal-intencionado exclua um backup (ou os dados de backup sejam excluídos acidentalmente), os dados de backup são mantidos por 14 dias adicionais, permitindo a recuperação desse item de backup sem perda de dados. Os 14 dias adicionais de retenção para dados de backup no estado de "exclusão reversível" não incorrem em nenhum custo para você.

Agora, além do suporte de exclusão reversível para VMs do Azure, as cargas de trabalho SQL Server e SAP HANA em VMs do Azure também são protegidas com exclusão reversível.

Para obter mais informações, consulte [exclusão reversível para SQL Server na VM do Azure e SAP Hana em cargas de trabalho de VM do Azure](soft-delete-sql-saphana-in-azure-vm.md).

## <a name="next-steps"></a>Próximas etapas

- [Diretrizes e práticas recomendadas do backup do Azure](guidance-best-practices.md)