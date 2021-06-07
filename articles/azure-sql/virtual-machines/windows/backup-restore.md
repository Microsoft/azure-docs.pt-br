---
title: Backup e restauração no SQL Server em VMs do Azure | Microsoft Docs
description: Descreve as considerações de backup e restauração para bancos de dados do SQL Server em execução em Máquinas Virtuais do Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-resource-management
ms.assetid: 95a89072-0edf-49b5-88ed-584891c0e066
ms.service: virtual-machines-sql
ms.subservice: backup
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/04/2018
ms.author: mikeray
ms.openlocfilehash: 2fcba81bcd20db321d791fcda589f40fb0699702
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97733066"
---
# <a name="backup-and-restore-for-sql-server-on-azure-vms"></a>Backup e restauração para o SQL Server em VMs do Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Este artigo fornece diretrizes sobre as opções de backup e restauração disponíveis para o SQL Server em execução em uma VM (máquina virtual) do Windows no Azure. O Armazenamento do Azure mantém três cópias de cada disco de VM do Azure para assegurar a proteção contra perda de dados ou dados físicos corrompidos. Assim, ao contrário do SQL Server local, você não precisa se concentrar em falhas de hardware. No entanto, você ainda deve fazer backup de seus bancos de dados do SQL Server para se proteger contra erros de aplicativo ou usuário, como inserções ou exclusões de dados acidentais. Nessa situação, é importante poder restaurar para um ponto específico no tempo.

A primeira parte deste artigo fornece uma visão geral das opções de restauração e backup disponíveis. Isso é seguido por seções que fornecem mais informações sobre cada estratégia.

## <a name="backup-and-restore-options"></a>Opções de backup e restauração

A seguinte tabela fornece informações sobre várias opções de backup e restauração do SQL Server em execução em Máquinas Virtuais do Azure:

| Estratégia | Versões do SQL | Descrição |
|---|---|---|
| [Backup Automatizado](#automated) | 2014<br/> 2016<br/> 2017 | O Backup Automatizado permite que você agende backups regulares de todos os bancos de dados em uma VM do SQL Server. Os backups são armazenados no armazenamento do Azure por até 30 dias. A partir do SQL Server 2016, o Backup Automatizado v2 oferece opções adicionais, como a configuração de agendamento manual e a frequência de backups de log e completos. |
| [Backup do Azure para VMs do SQL](#azbackup) | 2008<br/> 2012<br/> 2014<br/> 2016<br/> 2017<br/> 2019 | O Backup do Azure fornece uma funcionalidade de backup de classe empresarial para SQL Server em VMs do Azure. Com esse serviço, você pode gerenciar centralmente os backups para vários servidores e milhares de bancos de dados. Os bancos de dados podem ser restaurados para um ponto específico no tempo no portal. Ele oferece uma política de retenção personalizável que pode manter backups por anos. |
| [Backup manual](#manual) | Todos | Dependendo de sua versão do SQL Server, há várias técnicas para fazer backup e restauração manuais do SQL Server em uma VM do Azure. Neste cenário, você é responsável pela forma como o backup dos bancos de dados é feito e o local de armazenamento e gerenciamento desses backups. |

As seções a seguir descrevem cada abordagem mais detalhadamente. A seção final deste artigo fornece um resumo na forma de uma matriz de recurso.

## <a name="automated-backup"></a><a id="automated"></a> Backup Automatizado

O Backup Automatizado fornece um serviço de backup automático para edições do SQL Server Standard e Enterprise em execução em uma VM do Windows no Azure. Esse serviço é fornecido pela [Extensão do SQL Server IaaS Agent](sql-server-iaas-agent-extension-automate-management.md), que é instalado automaticamente em imagens de máquinas virtuais do Windows do SQL Server no Portal do Azure.

Todos os backups de bancos de dados são feitos em uma conta de armazenamento do Azure configurada por você. Os backups podem ser criptografados e mantidos por até 30 dias.

O SQL Server 2016 e as VMs superiores oferecem mais opções de personalização com o Backup Automatizado v2. Entre as melhorias estão:

- Backups de banco de dados do sistema
- Agendamento de backup manual e janela de tempo
- Frequência de backup completa e de arquivo de log

Para restaurar um banco de dados, você deve localizar os arquivos de backup necessários na conta de armazenamento e executar uma restauração na sua VM do SQL usando o SQL Server Management Studio (SSMS) ou comandos do Transact-SQL.

Para saber mais sobre como configurar o Backup Automatizado para VMs do SQL, veja um dos seguintes artigos:

- **SQL Server 2016/2017**: [Backup Automatizado v2 para Máquinas Virtuais do Azure](automated-backup.md)
- **SQL Server 2014**: [Backup Automatizado para Máquinas Virtuais do SQL Server 2014](automated-backup-sql-2014.md)

## <a name="azure-backup-for-sql-vms"></a><a id="azbackup"></a> Backup do Azure para VMs do SQL

O [Backup do Azure](../../../backup/index.yml) fornece uma funcionalidade de backup de classe empresarial para SQL Server em VMs do Azure. Todos os backups são armazenados e gerenciados em um cofre de Serviços de Recuperação. Esta solução fornece diversas vantagens, especialmente para empresas:

- **Backup com infraestrutura zero**: você não precisa gerenciar servidores de backup ou locais de armazenamento.
- **Escala**: proteja muitas máquinas virtuais do SQL e milhares de bancos de dados.
- **Pagamento Conforme o Uso**: esse recurso é um serviço separado fornecido pelo Backup do Azure, mas assim como acontece com todos os serviços do Azure, você só paga pelo que usa.
- **Gerenciamento e monitoramento central**: gerencie centralmente todos os seus backups, inclusive outras cargas de trabalho para as quais o Backup do Azure dá suporte, de um único painel de controle no Azure.
- **Backup e retenção controlados por política**: crie políticas de backup padrão para backups regulares. Estabeleça políticas de retenção para manter os backups por anos.
- **Suporte para SQL Always On**: detecte e proteja uma configuração do SQL Server Always On e respeite a preferência de backup do Grupo de Disponibilidade.
- **RPO (Objetivo de Ponto de Recuperação) de 15 minutos**: configure backups de log de transações do SQL para a cada 15 minutos.
- **Recuperação Pontual**: use o portal para recuperar bancos de dados para um ponto específico no tempo sem a necessidade de restaurar manualmente vários backups completos, diferenciais e de log.
- **Alertas de email consolidados para falhas**: configure notificações de email consolidadas para todas as falhas.
- **Controle de acesso baseado em função do Azure**: Determine quem pode gerenciar operações de backup e restauração por meio do Portal.

Para obter uma visão geral rápida de como isso funciona junto com uma demonstração, assista ao vídeo a seguir:

> [!VIDEO https://www.youtube.com/embed/wmbANpHos_E]

Essa solução de Backup do Azure para VMs do SQL está atualmente em disponibilidade geral. Para saber mais, veja [Fazer backup de banco de dados do SQL Server para o Azure](../../../backup/backup-azure-sql-database.md).

## <a name="manual-backup"></a><a id="manual"></a> Backup manual

Se quiser gerenciar o backup e restaurar operações em suas VMs do SQL manualmente, há várias opções, dependendo da versão do SQL Server que você está usando. Para obter uma visão geral de backup e restauração, veja um dos seguintes artigos com base em sua versão do SQL Server:

- [Backup e restauração no SQL Server 2016 e posterior](/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases)
- [Backup e restauração no SQL Server 2014](/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases?viewFallbackFrom=sql-server-2014)
- [Backup e restauração no SQL Server 2012](/previous-versions/sql/sql-server-2012/ms187048(v=sql.110))
- [Backup e restauração no SQL Server 2008 R2](/previous-versions/sql/sql-server-2008-r2/ms187048(v=sql.105))
- [Backup e restauração no SQL Server 2008](/previous-versions/sql/sql-server-2008/ms187048(v=sql.100))

As seções a seguir descrevem várias opções de backup e de restauração em mais detalhes.

### <a name="backup-to-attached-disks"></a>Backup em discos anexados

Para o SQL Server em VMs do Azure, você pode usar o backup nativo e técnicas de restauração usando discos anexados na VM para o destino dos arquivos de backup. No entanto, há um limite para o número de discos que você pode anexar a uma máquina virtual do Azure, com base no [tamanho da máquina virtual](../../../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Também é necessário considerar a sobrecarga de gerenciamento de disco.

Para obter um exemplo de como criar manualmente um backup de banco de dados completo usando o Transact-SQL ou o SQL Server Management Studio (SSMS), veja [Criar um backup completo de banco de dados](/sql/relational-databases/backup-restore/create-a-full-database-backup-sql-server).

### <a name="backup-to-url"></a>Backup para URL

A partir do SQL Server 2012 SP1 CU2, você pode fazer backup e restaurar diretamente no armazenamento de blobs do Microsoft Azure, que também é conhecido como backup para URL. O SQL Server 2016 também introduziu os seguintes aprimoramentos para esse recurso:

| Melhoria do 2016 | Detalhes |
| --- | --- |
| **Distribuição** |Ao fazer backup para o armazenamento de blobs do Microsoft Azure, o SQL Server 2016 dá suporte ao backup para vários blobs a fim de habilitar o backup de grandes bancos de dados, até o máximo de 12,8 TB. |
| **Backup de instantâneo** |Com o uso de instantâneos do Azure, o Backup de Instantâneo de Arquivo do SQL Server fornece backups quase imediatos e restaurações rápidas para os arquivos de banco de dados armazenados por meio do serviço de armazenamento de blobs do Azure. Essa funcionalidade permite simplificar suas políticas de backup e restauração. O backup de instantâneo de arquivo também dá suporte à recuperação pontual. Para obter mais informações, veja [Backups de instantâneo para arquivos de banco de dados no Azure](/sql/relational-databases/backup-restore/file-snapshot-backups-for-database-files-in-azure). |

Para saber mais, veja um dos seguintes artigos com base em sua versão do SQL Server:

- **SQL Server 2016/2017**: [Backup do SQL Server para URL](/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service)
- **SQL Server 2014**: [Backup do SQL Server 2014 para URL](/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service?viewFallbackFrom=sql-server-2014)
- **SQL Server 2012**: [Backup do SQL Server 2012 para URL](/previous-versions/sql/sql-server-2012/jj919148(v=sql.110))

### <a name="managed-backup"></a>Backup Gerenciado

A partir do SQL Server 2014, o Backup Gerenciado automatiza a criação de backups no armazenamento do Azure. Nos bastidores, o Backup Gerenciado faz uso do Backup para o recurso de URL descrito na seção anterior deste artigo. O Backup Gerenciado também é o recurso subjacente que dá suporte ao serviço de Backup Automatizado da VM do SQL Server.

No SQL Server 2016 e nas versões posteriores, o Backup Gerenciado tem opções adicionais para agendamento, backup de banco de dados do sistema e frequência de backup completo e de log.

Para saber mais, veja um dos seguintes artigos com base em sua versão do SQL Server:

- [Backup gerenciado do Microsoft Azure para SQL Server 2016 e posterior](/sql/relational-databases/backup-restore/sql-server-managed-backup-to-microsoft-azure)
- [Backup gerenciado do Microsoft Azure no SQL Server 2014](/sql/relational-databases/backup-restore/sql-server-managed-backup-to-microsoft-azure?viewFallbackFrom=sql-server-2014)

## <a name="decision-matrix"></a>Matriz de decisão

A tabela a seguir resume os recursos de cada opção de backup e restauração de máquinas virtuais de SQL Server no Azure.

| Opção | Backup Automatizado | Backup do Azure para SQL | Backup manual |
|---|---|---|---|
| Requer serviço adicional do Azure |   | ![Marca de seleção verde.](./media/backup-restore/yes.png) |   |
| Configurar a política de backup no Portal do Azure | ![Marca de seleção verde.](./media/backup-restore/yes.png) | ![Marca de seleção verde.](./media/backup-restore/yes.png) |   |
| Restaurar bancos de dados no Portal do Azure |   | ![Marca de seleção verde.](./media/backup-restore/yes.png) |   |
| Gerenciar vários servidores em um painel |   | ![Marca de seleção verde.](./media/backup-restore/yes.png) |   |
| Restauração em um momento determinado | ![Marca de seleção verde.](./media/backup-restore/yes.png) | ![Marca de seleção verde.](./media/backup-restore/yes.png) | ![Marca de seleção verde.](./media/backup-restore/yes.png) |
| RPO (Objetivo de Ponto de Recuperação) de 15 minutos | ![Marca de seleção verde.](./media/backup-restore/yes.png) | ![Marca de seleção verde.](./media/backup-restore/yes.png) | ![Marca de seleção verde.](./media/backup-restore/yes.png) |
| Política de retenção de backup de curto prazo (dias) | ![Marca de seleção verde.](./media/backup-restore/yes.png) | ![Marca de seleção verde.](./media/backup-restore/yes.png) |   |
| Política de retenção de backup de longo prazo (meses, anos) |   | ![Marca de seleção verde.](./media/backup-restore/yes.png) |   |
| Suporte interno para o SQL Server Always On |   | ![Marca de seleção verde.](./media/backup-restore/yes.png) |   |
| Backup das contas do Armazenamento do Azure | ![Marca de seleção verde.](./media/backup-restore/yes.png)(automático) | ![Marca de seleção verde.](./media/backup-restore/yes.png)(automático) | ![Marca de seleção verde.](./media/backup-restore/yes.png)(gerenciado pelo cliente) |
| Gerenciamento de arquivos de backup e armazenamento | | ![Marca de seleção verde.](./media/backup-restore/yes.png) |  |
| Backup em discos anexados na VM |   |   | ![Marca de seleção verde.](./media/backup-restore/yes.png) |
| Relatórios de backup personalizáveis centrais |   | ![Marca de seleção verde.](./media/backup-restore/yes.png) |   |
| Alertas de email consolidado para falhas |   | ![Marca de seleção verde.](./media/backup-restore/yes.png) |   |
| Personalizar monitoramento com base nos logs do Azure Monitor |   | ![Marca de seleção verde.](./media/backup-restore/yes.png) |   |
| Monitorar trabalhos de backup com scripts SSMS ou Transact-SQL | ![Marca de seleção verde.](./media/backup-restore/yes.png) | ![Marca de seleção verde.](./media/backup-restore/yes.png) | ![Marca de seleção verde.](./media/backup-restore/yes.png) |
| Restaurar bancos de dados com scripts SSMS ou Transact-SQL | ![Marca de seleção verde.](./media/backup-restore/yes.png) |   | ![Marca de seleção verde.](./media/backup-restore/yes.png) |

## <a name="next-steps"></a>Próximas etapas

Se estiver planejando a implantação do SQL Server em uma VM do Azure, você encontrará diretrizes sobre o provisionamento no seguinte guia: [Como provisionar uma máquina virtual do SQL Server do Windows no Portal do Azure](create-sql-vm-portal.md).

Embora o backup e a restauração possam ser usados para migrar seus dados, há caminhos de migração de dados potencialmente mais fácil para o SQL Server em uma VM. Para ver uma discussão completa sobre as opções de migração e suas recomendações, confira [Migração de um banco de dados para o SQL Server em uma VM do Azure](migrate-to-vm-from-sql-server.md).
