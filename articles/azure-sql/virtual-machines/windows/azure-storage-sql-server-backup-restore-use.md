---
title: Como usar o armazenamento do Azure para SQL Server Backup e restauração | Microsoft Docs
description: Saiba como fazer backup do SQL Server no Armazenamento do Azure. Explica os benefícios de fazer backup de bancos de dados SQL no Armazenamento do Azure.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
tags: azure-service-management
ms.assetid: 0db7667d-ef63-4e2b-bd4d-574802090f8b
ms.service: virtual-machines-sql
ms.subservice: backup
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/31/2017
ms.author: mathoma
ms.openlocfilehash: 35fff49a53f5a0a9532fd0dff841356c5deaf3ea
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/22/2020
ms.locfileid: "97724775"
---
# <a name="use-azure-storage-for-sql-server-backup-and-restore"></a>Usar o armazenamento do Azure para SQL Server Backup e restauração
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

A partir do SQL Server 2012 SP1 CU2, agora você pode gravar backup de bancos de dados SQL Server diretamente no armazenamento de BLOBs do Azure. Use essa funcionalidade para fazer backup e restauração do armazenamento de BLOBs do Azure. O backup na nuvem oferece benefícios de disponibilidade, armazenamento fora do local de replicação geográfica ilimitada e facilidade de migração de dados de e para a nuvem. Você pode emitir `BACKUP` `RESTORE` instruções ou usando TRANSACT-SQL ou Smo.

## <a name="overview"></a>Visão geral
O SQL Server 2016 apresenta novos recursos: você pode usar o [backup de instantâneo de arquivo](/sql/relational-databases/backup-restore/file-snapshot-backups-for-database-files-in-azure) para executar backups quase imediatos e restaurações incrivelmente rápidas.

Este tópico explica por que você pode optar por usar o armazenamento do Azure para SQL Server backups e, em seguida, descreve os componentes envolvidos. Você pode usar os recursos fornecidos no final do artigo para acessar orientações e informações adicionais para começar a usar esse serviço com os backups do SQL Server.

## <a name="benefits-of-using-azure-blob-storage-for-sql-server-backups"></a>Benefícios do uso do armazenamento de BLOBs do Azure para backups SQL Server
Há vários desafios que enfrentamos ao fazer o backup do SQL Server. Eles incluem gerenciamento de armazenamento, risco de falha de armazenamento, acesso a armazenamento externo e configuração de hardware. Muitos desses desafios são abordados usando o armazenamento de BLOBs do Azure para backups SQL Server. Considere as vantagens a seguir:

* **Facilidade de uso**: Armazenar seus backups em blobs do Azure pode ser uma opção conveniente, flexível e de fácil acesso externo. Criar armazenamento externo para os backups do SQL Server pode ser tão fácil quanto modificar seus scripts/trabalhos existentes para usar a sintaxe **BACKUP TO URL** . O armazenamento externo deve estar, normalmente, distante o suficiente do local do banco de dados de produção para evitar um único desastre que possa afetar os locais dos bancos de dados externo e de produção. Ao optar por fazer a [replicação geográfica de seus blobs do Azure](../../../storage/common/storage-redundancy.md), você tem uma camada extra de proteção em caso de um desastre que poderia afetar a região inteira.
* **Arquivo de backup**: o armazenamento de BLOBs do Azure oferece uma alternativa melhor à opção de fita usada com frequência para arquivar backups. O armazenamento em fita pode exigir o transporte físico para uma instalação externa e medidas para proteger a mídia. Armazenar os backups no armazenamento de BLOBs do Azure fornece uma opção de arquivamento instantânea, altamente disponível e durável.
* **Hardware gerenciado**: Não há nenhuma sobrecarga de gerenciamento de hardware com os serviços do Azure. Serviços Azure gerenciar o hardware e fornecem replicação geográfica para redundância e proteção contra falhas de hardware.
* **Armazenamento ilimitado**: Ao habilitar o backup direto para blobs do Azure, você terá acesso a um armazenamento virtualmente ilimitado. Como alternativa, o backup em um disco de máquina virtual do Azure tem limites com base no tamanho da máquina. Há um limite para o número de discos que você pode anexar a uma máquina virtual do Azure para backups. O limite é 16 discos para uma instância grande adicional, e menos que isso para instâncias menores.
* **Disponibilidade de backup**: Os backups armazenados em blobs do Azure estão disponíveis em qualquer lugar e a qualquer momento e podem ser acessados com facilidade para restaurações em uma instância do SQL Server, sem a necessidade de anexação/desanexação do banco de dados nem de download e anexação do VHD.
* **Custo**: pague apenas pelo serviço usado. Pode ser tão econômico quanto uma opção de arquivamento externo e de backup. Confira a [Calculadora de Preços do Azure](https://go.microsoft.com/fwlink/?LinkId=277060 "Calculadora de Preço") e o [artigo Preços do Azure](https://go.microsoft.com/fwlink/?LinkId=277059 "Artigo sobre preços") para obter mais informações.
* **Instantâneos de armazenamento**: Quando arquivos de banco de dados são armazenados em um blob do Azure e você está usando o SQL Server 2016, é possível usar o [backup de instantâneo de arquivo](/sql/relational-databases/backup-restore/file-snapshot-backups-for-database-files-in-azure) para fazer backups quase instantâneos e restaurações incrivelmente rápidas.

Para obter mais detalhes, consulte [SQL Server Backup e restauração com o armazenamento de BLOBs do Azure](/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service).

As duas seções a seguir introduzem o armazenamento de BLOBs do Azure, incluindo os componentes de SQL Server necessários. É importante entender os componentes e sua interação para usar com êxito o backup e a restauração do armazenamento de BLOBs do Azure.

## <a name="azure-blob-storage-components"></a>Componentes do armazenamento de BLOBs do Azure
Os componentes do Azure a seguir são usados ao fazer backup no armazenamento de BLOBs do Azure.

| Componente | Descrição |
| --- | --- |
| **Conta de armazenamento** |A conta de armazenamento é o ponto de partida de todos os serviços de armazenamento. Para acessar o armazenamento de BLOBs do Azure, primeiro crie uma conta de armazenamento do Azure. Para obter mais informações sobre o armazenamento de BLOBs do Azure, consulte [como usar o armazenamento de BLOBs do Azure](https://azure.microsoft.com/develop/net/how-to-guides/blob-storage/). |
| **Contêiner** |Um contêiner fornece um agrupamento de um conjunto de blobs e pode armazenar um número ilimitado de blobs. Para gravar um backup de SQL Server no armazenamento de BLOBs do Azure, você deve ter pelo menos o contêiner raiz criado. |
| **Blob** |Um arquivo de qualquer tipo e tamanho. Os BLOBs são endereçáveis usando o seguinte formato de URL: `https://<storageaccount>.blob.core.windows.net/<container>/<blob>` . Para obter mais informações sobre blobs de páginas, consulte [Noções gerais sobre blobs de blocos e blobs de páginas](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs) |

## <a name="sql-server-components"></a>Componentes do SQL Server
Os componentes SQL Server a seguir são usados ao fazer backup no armazenamento de BLOBs do Azure.

| Componente | Descrição |
| --- | --- |
| **URL** |Uma URL especifica um URI (Uniform Resource Identifier) para um arquivo de backup exclusivo. A URL fornece o local e o nome do arquivo de backup SQL Server. A URL deve apontar para um blob real, e não apenas para um contêiner. Se o BLOB não existir, o Azure o criará. Se um blob existente for especificado, o comando backup falhará, a menos que a `WITH FORMAT` opção seja especificada. Veja a seguir um exemplo da URL que você especificaria no comando de BACKUP: `https://<storageaccount>.blob.core.windows.net/<container>/<FILENAME.bak>` .<br><br> HTTPS é recomendável, mas não obrigatório. |
| **Credencial** |As informações necessárias para se conectar e autenticar no armazenamento de BLOBs do Azure são armazenadas como uma credencial. Para o SQL Server gravar backups para uma restauração ou BLOBs do Azure dela, uma credencial do SQL Server deve ser criada. Para obter mais informações, veja [Credencial do SQL Server](/sql/t-sql/statements/create-credential-transact-sql). |

> [!NOTE]
> SQL Server 2016 foi atualizado para dar suporte a blobs de blocos. Consulte o [tutorial: usando Microsoft Azure armazenamento de BLOBs com bancos de dados SQL Server 2016](/sql/relational-databases/tutorial-use-azure-blob-storage-service-with-sql-server-2016) para obter mais detalhes.
> 

## <a name="next-steps"></a>Próximas etapas

1. Crie uma conta do Azure caso você ainda não tenha uma. Se você estiver avaliando o Azure, considere usar a [avaliação gratuita](https://azure.microsoft.com/free/).
2. Em seguida, percorra um dos seguintes tutoriais que mostram como criar uma conta de armazenamento e realizar uma restauração.
   
   * **SQL Server 2014**: [tutorial: Backup de SQL Server 2014 e restauração para Microsoft Azure armazenamento de BLOBs](/previous-versions/sql/2014/relational-databases/backup-restore/sql-server-backup-to-url).
   * **SQL Server 2016**: [tutorial: usando o armazenamento de BLOBs Microsoft Azure com bancos de dados SQL Server 2016](/sql/relational-databases/tutorial-use-azure-blob-storage-service-with-sql-server-2016)
3. Examine a documentação adicional que começa com [SQL Server Backup e restauração com Microsoft Azure armazenamento de BLOBs](/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service).

Se você tiver problemas, examine o tópico [Práticas recomendadas e solução de problemas de backup do SQL Server para URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url-best-practices-and-troubleshooting).

Para outras SQL Server opções de backup e restauração, consulte [backup e restauração para SQL Server em máquinas virtuais do Azure](backup-restore.md).