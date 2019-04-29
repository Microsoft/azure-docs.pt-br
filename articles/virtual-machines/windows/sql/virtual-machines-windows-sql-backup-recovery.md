---
title: Backup e restauração no SQL Server em VMs do Azure | Microsoft Docs
description: Descreve as considerações de backup e restauração para bancos de dados do SQL Server em execução em Máquinas Virtuais do Azure.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-resource-management
ms.assetid: 95a89072-0edf-49b5-88ed-584891c0e066
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/04/2018
ms.author: mikeray
ms.openlocfilehash: ab239d0546508d74874c6b6be03f6afc06b08fa7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60563403"
---
# <a name="backup-and-restore-for-sql-server-in-azure-virtual-machines"></a>Backup e restauração para o SQL Server em Máquinas Virtuais do Azure

Este artigo fornece diretrizes sobre as opções de backup e restauração disponíveis para o SQL Server em execução em uma máquina virtual do Windows no Azure. O Armazenamento do Azure mantém três cópias de cada disco de VM do Azure para assegurar a proteção contra perda de dados ou dados físicos corrompidos. Assim, ao contrário do local, você não precisa se concentrar em falhas de hardware. No entanto, você ainda deve fazer backup de seus bancos de dados do SQL Server para se proteger contra erros de aplicativo ou usuário, como inserções ou exclusões de dados acidentais. Nessa situação, é importante poder restaurar para um ponto específico no tempo.

A primeira parte deste artigo fornece uma visão geral das opções de restauração e backup disponíveis. Isso é seguido por seções que fornecem mais informações sobre cada estratégia.

## <a name="backup-and-restore-options"></a>Opções de backup e restauração

A tabela a seguir fornece informações sobre várias opções de backup e restauração do SQL Server em execução em máquinas virtuais do Azure:

| Estratégia | Versões do SQL | DESCRIÇÃO |
|---|---|---|
| [Backup Automatizado](#automated) | 2014<br/> 2016<br/> 2017 | O Backup Automatizado permite que você agende backups regulares de todos os bancos de dados em uma VM do SQL Server. Os backups são armazenados no armazenamento do Azure por até 30 dias. A partir do SQL Server 2016, o Backup Automatizado v2 oferece opções adicionais, como a configuração de agendamento manual e a frequência de backups de log e completos. |
| [Backup do Azure para VMs do SQL](#azbackup) | 2012<br/> 2014<br/> 2016<br/> 2017 | O Backup do Azure fornece um recurso de backup de classe empresarial para SQL Server em execução em VMs do Azure. Com esse serviço, você pode gerenciar centralmente os backups para vários servidores e milhares de bancos de dados. Os bancos de dados podem ser restaurados para um ponto específico no tempo no portal. Ele oferece uma política de retenção personalizável que pode manter backups por anos. Esse recurso está atualmente em visualização pública. |
| [Backup manual](#manual) | Todos | Dependendo de sua versão do SQL Server, há várias técnicas para fazer backup e restauração manuais do SQL Server em execução em uma VM do Azure. Neste cenário, você é responsável pela forma como o backup dos bancos de dados é feito e o local de armazenamento e gerenciamento desses backups. |

As seções a seguir descrevem cada abordagem mais detalhadamente. A seção final deste artigo fornece um resumo na forma de uma matriz de recurso.

## <a id="automated"></a> Backup Automatizado

O Backup Automatizado fornece um serviço de backup automático para edições do SQL Server Standard e Enterprise em execução em uma VM do Windows no Azure. Esse serviço é fornecido pela [Extensão do SQL Server IaaS Agent](virtual-machines-windows-sql-server-agent-extension.md), que é instalado automaticamente em imagens de máquinas virtuais do Windows do SQL Server no Portal do Azure.

Todos os backups de bancos de dados são feitos em uma conta de armazenamento do Azure configurada por você. Os backups podem ser criptografados e mantidos por até 30 dias.

O SQL Server 2016 e as VMs superiores oferecem mais opções de personalização com o Backup Automatizado v2. Entre as melhorias estão:

- Backups de banco de dados do sistema
- Agendamento de backup manual e janela de tempo
- Frequência de backup completa e de arquivo de log

Para restaurar um banco de dados, você deve localizar os arquivos de backup necessários na conta de armazenamento e executar uma restauração na sua VM do SQL usando o SQL Server Management Studio (SSMS) ou comandos do Transact-SQL.

Para saber mais sobre como configurar o Backup Automatizado para VMs do SQL, veja um dos seguintes artigos:

- **SQL Server 2016/2017**: [Backup automatizado v2 para máquinas virtuais do Azure](virtual-machines-windows-sql-automated-backup-v2.md)
- **SQL Server 2014**: [Backup Automatizado para Máquinas Virtuais do SQL Server 2014](virtual-machines-windows-sql-automated-backup.md)

## <a id="azbackup"></a> Backup do Azure para VMs do SQL

O [Backup do Azure](/azure/backup/) fornece um recurso de backup de classe empresarial para SQL Server em execução em VMs do Azure. Todos os backups são armazenados e gerenciados em um cofre de Serviços de Recuperação. Esta solução fornece diversas vantagens, especialmente para empresas:

- **Backup com infraestrutura zero**: você não precisa gerenciar servidores de backup ou locais de armazenamento.
- **Escala**: proteja muitas máquinas virtuais do SQL e milhares de bancos de dados.
- **Pagamento Conforme o Uso**: esse recurso é um serviço separado fornecido pelo Backup do Azure, mas assim como acontece com todos os serviços do Azure, você só paga pelo que usa.
- **Gerenciamento e monitoramento central**: gerencie centralmente todos os seus backups, inclusive outras cargas de trabalho para as quais o Backup do Azure dá suporte, de um único painel de controle no Azure.
- **Backup e retenção controlados por política**: crie políticas de backup padrão para backups regulares. Estabeleça políticas de retenção para manter os backups por anos.
- **Suporte para SQL Always On**: detecte e proteja uma configuração do SQL Server Always On e respeite a preferência de backup do Grupo de Disponibilidade.
- **RPO (Objetivo de Ponto de Recuperação) de 15 minutos**: configure backups de log de transações do SQL para a cada 15 minutos.
- **Recuperação Pontual**: use o portal para recuperar bancos de dados para um ponto específico no tempo sem a necessidade de restaurar manualmente vários backups completos, diferenciais e de log.
- **Alertas de email consolidados para falhas**: configure notificações de email consolidadas para todas as falhas.
- **Controle de acesso baseado em função**: determine quem pode gerenciar as operações de backup e restauração por meio do portal.

Para obter uma visão geral rápida de como isso funciona junto com uma demonstração, assista ao vídeo a seguir:

> [!VIDEO https://www.youtube.com/embed/wmbANpHos_E]

Essa solução de Backup do Azure para VMs do SQL está disponível. Para saber mais, veja [Fazer backup de banco de dados do SQL Server para o Azure](../../../backup/backup-azure-sql-database.md).

## <a id="manual"></a> Backup manual

Se quiser gerenciar o backup e restaurar operações em suas VMs do SQL manualmente, há várias opções, dependendo da versão do SQL Server que você está usando. Para obter uma visão geral de backup e restauração, veja um dos seguintes artigos com base em sua versão do SQL Server:

- [Backup e restauração no SQL Server 2016 e posterior](https://docs.microsoft.com/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases)
- [Backup e restauração no SQL Server 2014](https://msdn.microsoft.com/library/ms187048%28v=sql.120%29.aspx)
- [Backup e restauração no SQL Server 2012](https://msdn.microsoft.com/library/ms187048%28v=sql.110%29.aspx)
- [Backup e restauração no SQL Server 2008 R2](https://msdn.microsoft.com/library/ms187048%28v=sql.105%29.aspx)
- [Backup e restauração no SQL Server 2008](https://msdn.microsoft.com/library/ms187048%28v=sql.100%29.aspx)

As seções a seguir descrevem várias opções de backup e de restauração em mais detalhes.

### <a name="backup-to-attached-disks"></a>Backup em discos anexados

Para o SQL Server em execução em VMs do Azure, você pode usar o backup nativo e técnicas de restauração usando discos anexados na VM para o destino dos arquivos de backup. No entanto, há um limite para o número de discos que você pode anexar a uma máquina virtual do Azure, com base no [tamanho da máquina virtual](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Também é necessário considerar a sobrecarga de gerenciamento de disco.

Para obter um exemplo de como criar manualmente um backup de banco de dados completo usando o Transact-SQL ou o SQL Server Management Studio (SSMS), veja [Criar um backup completo de banco de dados](https://docs.microsoft.com/sql/relational-databases/backup-restore/create-a-full-database-backup-sql-server).

### <a name="backup-to-url"></a>Backup para URL

A partir do SQL Server 2012 SP1 CU2, você pode fazer backup e restaurar diretamente no armazenamento de blobs do Microsoft Azure, que também é conhecido como backup para URL. O SQL Server 2016 também introduziu os seguintes aprimoramentos para esse recurso:

| Aprimoramento do 2016 | Detalhes |
| --- | --- |
| **Distribuição** |Ao fazer backup para o armazenamento de blobs do Microsoft Azure, o SQL Server 2016 dá suporte ao backup para vários blobs a fim de habilitar o backup de grandes bancos de dados, até o máximo de 12,8 TB. |
| **Backup de instantâneo** |Com o uso de instantâneos do Azure, o Backup de Instantâneo de Arquivo do SQL Server fornece backups quase imediatos e restaurações rápidas para os arquivos de banco de dados armazenados por meio do serviço de armazenamento de blobs do Azure. Essa funcionalidade permite simplificar suas políticas de backup e restauração. O backup de instantâneo de arquivo também dá suporte à recuperação pontual. Para obter mais informações, veja [Backups de instantâneo para arquivos de banco de dados no Azure](https://docs.microsoft.com/sql/relational-databases/backup-restore/file-snapshot-backups-for-database-files-in-azure). |

Para saber mais, veja um dos seguintes artigos com base em sua versão do SQL Server:

- **SQL Server 2016/2017**: [Backup do SQL Server para URL](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service)
- **SQL Server 2014**: [Backup do SQL Server 2014 para URL](https://msdn.microsoft.com/library/jj919148%28v=sql.120%29.aspx)
- **SQL Server 2012**: [Backup do SQL Server 2012 para URL](https://msdn.microsoft.com/library/jj919148%28v=sql.110%29.aspx)

### <a name="managed-backup"></a>Backup Gerenciado

A partir do SQL Server 2014, o Backup Gerenciado automatiza a criação de backups no armazenamento do Azure. Nos bastidores, o Backup Gerenciado faz uso do Backup para o recurso de URL descrito na seção anterior deste artigo. O Backup Gerenciado também é o recurso subjacente que dá suporte ao serviço de Backup Automatizado da VM do SQL Server.

A partir do SQL Server 2016, o backup gerenciado tem opções adicionais para o agendamento, o banco de dados de sistema completo e backup e a frequência de backup de log.

Para saber mais, veja um dos seguintes artigos com base em sua versão do SQL Server:

- [Backup gerenciado do Microsoft Azure para SQL Server 2016 e posterior](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-managed-backup-to-microsoft-azure)
- [Backup gerenciado do Microsoft Azure no SQL Server 2014](https://msdn.microsoft.com/library/dn449496%28v=sql.120%29.aspx)

## <a name="decision-matrix"></a>Matriz de decisão

A tabela a seguir resume os recursos de cada opção de backup e restauração de máquinas virtuais de SQL Server no Azure.

|| **Backup Automatizado** | **Backup do Azure para SQL** | **Backup manual** |
|---|---|---|---|
| Requer serviço adicional do Azure |   | ![Sim](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Configurar a política de backup no Portal do Azure | ![Sim](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Sim](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Restaurar bancos de dados no Portal do Azure |   | ![Sim](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Gerenciar vários servidores em um painel |   | ![Sim](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Restauração pontual | ![Sim](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![sim](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Sim](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |
| RPO (Objetivo de Ponto de Recuperação) de 15 minutos | ![Sim](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![sim](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Sim](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |
| Política de retenção de backup de curto prazo (dias) | ![Sim](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Sim](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Política de retenção de backup de longo prazo (meses, anos) |   | ![Sim](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Suporte interno para o SQL Server Always On |   | ![Sim](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Backup das contas do Armazenamento do Azure | ![Sim](./media/virtual-machines-windows-sql-backup-recovery/yes.png)(automático) | ![Sim](./media/virtual-machines-windows-sql-backup-recovery/yes.png)(automático) | ![Sim](./media/virtual-machines-windows-sql-backup-recovery/yes.png)(gerenciado pelo cliente) |
| Gerenciamento de arquivos de backup e armazenamento | | ![Sim](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |  |
| Backup em discos anexados na VM |   |   | ![Sim](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |
| Relatórios de backup personalizáveis centrais |   | ![Sim](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Alertas de email consolidado para falhas |   | ![Sim](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Personalizar o monitoramento com base nos logs do Azure Monitor |   | ![Sim](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Monitorar trabalhos de backup com scripts SSMS ou Transact-SQL | ![Sim](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![sim](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Sim](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |
| Restaurar bancos de dados com scripts SSMS ou Transact-SQL | ![Sim](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   | ![Sim](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |

## <a name="next-steps"></a>Próximas etapas

Se estiver planejando a implantação do SQL Server em uma VM do Azure, você encontrará diretrizes sobre o provisionamento no seguinte tutorial: [Como provisionar uma máquina virtual do SQL Server do Windows no Portal do Azure](virtual-machines-windows-portal-sql-server-provision.md).

Embora o backup e a restauração possam ser usados para migrar seus dados, há caminhos de migração de dados potencialmente mais fácil para o SQL Server em uma VM do Azure. Para ver uma discussão completa sobre as opções de migração e suas recomendações, consulte [Migração de um banco de dados para o SQL Server em uma VM do Azure](virtual-machines-windows-migrate-sql.md).
