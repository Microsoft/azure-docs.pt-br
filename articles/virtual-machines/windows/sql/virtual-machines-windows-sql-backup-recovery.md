---
title: "Backup e restauração do SQL Server | Microsoft Docs"
description: "Descreve as considerações de backup e restauração para bancos de dados do SQL Server em execução em Máquinas Virtuais do Azure."
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: jhubbard
editor: 
tags: azure-resource-management
ms.assetid: 95a89072-0edf-49b5-88ed-584891c0e066
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/15/2016
ms.author: mikeray
ms.openlocfilehash: 65557938673c5442758396a47873be1016e0f71b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="backup-and-restore-for-sql-server-in-azure-virtual-machines"></a>Backup e restauração para o SQL Server em Máquinas Virtuais do Azure
## <a name="overview"></a>Visão geral
O Armazenamento do Azure mantém 3 cópias de cada disco de VM do Azure para assegurar a proteção contra perda de dados ou dados físicos corrompidos. Portanto, ao contrário do local, você não precisa se preocupar com isso. No entanto, você ainda deve fazer backup dos bancos de dados SQL Server para se proteger contra erros do aplicativo ou do usuário (por exemplo, inserção de dados incorretos ou exclusão de uma tabela) e poder fazer uma restauração para um ponto no tempo.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

Para o SQL Server em execução em VMs do Azure, você pode usar o backup nativo e técnicas de restauração usando discos anexados para o destino dos arquivos de backup. No entanto, há um limite para o número de discos que você pode anexar a uma máquina virtual do Azure, com base no [tamanho da máquina virtual](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Também é necessário considerar a sobrecarga de gerenciamento de disco.

A partir do SQL Server 2014, você pode fazer backup e restauração para o Armazenamento de Blobs do Microsoft Azure. O SQL Server 2016 também fornece aprimoramentos para essa opção. Além disso, para os arquivos de banco de dados armazenados no armazenamento de Blobs do Microsoft Azure, o SQL Server 2016 fornece uma opção de backups quase imediatos e restaurações rápidas usando instantâneos do Azure. Este artigo fornece uma visão geral dessas opções. Encontre mais informações em [Backup e restauração do SQL Server com o serviço de Armazenamento de Blobs do Microsoft Azure](https://msdn.microsoft.com/library/jj919148.aspx).

> [!NOTE]
> Para ver uma discussão sobre as opções de backup de bancos de dados muito grandes, veja [Estratégias de backup do banco de dados SQL Server de vários terabytes para Máquinas Virtuais do Azure](http://blogs.msdn.com/b/igorpag/archive/2015/07/28/multi-terabyte-sql-server-database-backup-strategies-for-azure-virtual-machines.aspx).
> 
> 

As seções abaixo incluem informações específicas para as diferentes versões do SQL Server com suporte em uma máquina virtual do Azure.

## <a name="sql-server-virtual-machines"></a>Máquinas virtuais do SQL Server
Quando a instância do SQL Server está em execução em uma máquina virtual do Azure, os arquivos de banco de dados já residem em discos de dados no Azure. Esses discos residem no armazenamento de blobs do Azure. Portanto, os motivos para fazer backup do banco de dados e a abordagem adotada mudam ligeiramente. Considere o seguinte. 

* Você não precisa executar backups de banco de dados para fornecer proteção contra falhas de hardware ou de mídia, pois o Microsoft Azure fornece essa proteção como parte do seu serviço.
* Você ainda precisará fazer backups de banco de dados para proporcionar proteção contra erros de usuário ou para fins de arquivamento, regulamentos ou fins administrativos.
* Você pode armazenar o arquivo de backup diretamente no Azure. Para obter mais informações, consulte as seções a seguir que fornecem orientação para as diferentes versões do SQL Server.

## <a name="sql-server-2016"></a>SQL Server 2016
O Microsoft SQL Server 2016 dá suporte às funcionalidades de [backup e restauração com blobs do Azure](https://msdn.microsoft.com/library/jj919148.aspx) encontradas no SQL Server 2014. Porém ele também inclui os seguintes aprimoramentos:

| Aprimoramento do 2016 | Detalhes |
| --- | --- |
| **Distribuição** |Ao fazer backup para o armazenamento de blobs do Microsoft Azure, o SQL Server 2016 dá suporte ao backup para vários blobs a fim de habilitar o backup de grandes bancos de dados, até o máximo de 12,8 TB. |
| **Backup de instantâneo** |Com o uso de instantâneos do Azure, o Backup de Instantâneo de Arquivo do SQL Server fornece backups quase imediatos e restaurações rápidas para os arquivos de banco de dados armazenados por meio do serviço de armazenamento de blobs do Azure. Essa funcionalidade permite simplificar suas políticas de backup e restauração. O backup de instantâneo de arquivo também dá suporte à recuperação pontual. Para obter mais informações, veja [Backups de instantâneo para arquivos de banco de dados no Azure](https://msdn.microsoft.com/library/mt169363%28v=sql.130%29.aspx). |
| **Agendamento de backup gerenciado** |O backup de gerenciado do SQL Server no Azure agora dá suporte a agendamentos personalizados. Para obter mais informações, veja [Backup Gerenciado do SQL Server para o Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx). |

Para obter um tutorial dos recursos do SQL Server 2016 ao usar o armazenamento de Blobs do Azure, consulte [Tutorial: usando o serviço de armazenamento de Blobs do Microsoft Azure com bancos de dados do SQL Server 2016](https://msdn.microsoft.com/library/dn466438.aspx).

## <a name="sql-server-2014"></a>SQL Server 2014
O SQL Server 2014 inclui os seguintes aprimoramentos:

1. **Backup e restauração para o Azure**:
   
   * *Backup do SQL Server para URL* no SQL Server Management Studio. A opção de backup para o Azure agora está disponível ao usar a tarefa de Backup ou Restauração, ou ainda o Assistente de plano de manutenção no SQL Server Management Studio. Para obter mais informações, veja [Backup do SQL Server para URL](https://msdn.microsoft.com/library/jj919148%28v=sql.120%29.aspx).
   * *Backup Gerenciado do SQL Server para Azure* traz uma nova funcionalidade que permite o gerenciamento de backup automatizado. Isso é especialmente útil para automatizar o gerenciamento de backups para instâncias do SQL Server 2014 em execução em um computador do Azure. Para obter mais informações, veja [Backup Gerenciado do SQL Server para o Microsoft Azure](https://msdn.microsoft.com/library/dn449496%28v=sql.120%29.aspx).
   * O *Backup Automatizado* fornece automação adicional para habilitar automaticamente o *Backup Gerenciado do SQL Server para o Azure* em todos os bancos de dados novos e existentes para uma VM do SQL Server no Azure. Para obter mais informações, veja [Backup Automatizado para o SQL Server em Máquinas Virtuais do Azure](virtual-machines-windows-sql-automated-backup.md).
   * Para obter uma visão geral de todas as opções de Backup do SQL Server 2014 no Azure, veja [Backup e restauração do SQL Server com o serviço de armazenamento de blob do Microsoft Azure](https://msdn.microsoft.com/library/jj919148%28v=sql.120%29.aspx).
2. **Criptografia**: o SQL Server 2014 dá suporte à criptografia de dados durante a criação de um backup. Ele dá suporte a vários algoritmos de criptografia e ao uso de um certificado ou chave assimétrica. Para obter mais informações, veja [Criptografia de backup](https://msdn.microsoft.com/library/dn449489%28v=sql.120%29.aspx).

## <a name="sql-server-2012"></a>SQL Server 2012
Para obter informações detalhadas sobre o Backup e restauração do SQL Server no SQL Server 2012, veja [Backup e restauração de Bancos de Dados do SQL Server (SQL Server 2012)](https://msdn.microsoft.com/library/ms187048%28v=sql.110%29.aspx).

A partir do SQL Server 2012 SP1 Atualização Cumulativa 2, você pode fazer o backup e restauração a partir do Serviço de Armazenamento de Blobs do Azure. Esse aprimoramento pode ser usado para fazer backup de bancos de dados do SQL Server em um SQL Server em execução em uma máquina virtual do Azure ou em uma instância local. Para obter mais informações, veja [Backup e restauração do SQL Server com o Serviço de Armazenamento de Blob do Azure](https://msdn.microsoft.com/library/jj919148%28v=sql.110%29.aspx).

Alguns dos benefícios de usar o serviço de armazenamento de Blobs do Azure incluem a capacidade de ignorar o limite de 16 discos para discos anexados, a facilidade de gerenciamento, a disponibilidade direta do arquivo de backup para outra instância do SQL Server em execução em uma máquina virtual do Azure ou para instâncias locais para fins de migração ou recuperação de desastres. Para obter uma lista completa dos benefícios de usar um serviço de armazenamento de blob do Azure para backups do SQL Server, veja a seção *Benefícios* em [Backup e restauração do SQL Server com o serviço de armazenamento de blob do Azure](https://msdn.microsoft.com/library/jj919148%28v=sql.110%29.aspx).

Para ver as práticas recomendadas e informações de solução de problemas, veja [Práticas recomendadas de Backup e restauração (Serviço de Armazenamento de Blob do Azure)](https://msdn.microsoft.com/library/jj919149%28v=sql.110%29.aspx).

## <a name="sql-server-2008"></a>SQL Server 2008
Para Backup e restauração do SQL Server no SQL Server 2008 R2, veja [Fazer backup e restauração de bancos de dados no SQL Server (SQL Server 2008 R2)](https://msdn.microsoft.com/library/ms187048%28v=sql.105%29.aspx).

Para Backup e restauração do SQL Server no SQL Server 2008, veja [Fazer backup e restauração de bancos de dados no SQL Server (SQL Server 2008)](https://msdn.microsoft.com/library/ms187048%28v=sql.100%29.aspx).

## <a name="next-steps"></a>Próximas etapas
Caso esteja planejando a implantação do SQL Server em uma VM do Azure, você encontrará diretrizes sobre o provisionamento no seguinte tutorial: [Provisionamento de uma máquina virtual do SQL Server no Azure com o Azure Resource Manager](virtual-machines-windows-portal-sql-server-provision.md).

Embora o backup e a restauração possam ser usados para migrar seus dados, há caminhos de migração de dados potencialmente mais fácil para o SQL Server em uma VM do Azure. Para ver uma discussão completa sobre as opções de migração e suas recomendações, consulte [Migração de um banco de dados para o SQL Server em uma VM do Azure](virtual-machines-windows-migrate-sql.md).

Examine outros [recursos para executar o SQL Server em Máquinas Virtuais do Azure](virtual-machines-windows-sql-server-iaas-overview.md).

