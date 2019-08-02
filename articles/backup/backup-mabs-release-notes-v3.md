---
title: Notas de versão do Servidor de Backup do Azure v3
description: Este artigo fornece as informações sobre os problemas conhecidos e as soluções alternativas para o MABS v3.
author: JYOTHIRMAISURI
manager: vvithal
ms.service: backup
ms.topic: conceptual
ms.date: 11/22/2018
ms.author: v-jysur
ms.asset: 0c4127f2-d936-48ef-b430-a9198e425d81
ms.openlocfilehash: e0bcdeeda2f8517b85059817669c19d66c723902
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68465058"
---
# <a name="release-notes-for-microsoft-azure-backup-server"></a>Notas de versão do Serviço de Backup do Azure
Este artigo fornece os problemas conhecidos e soluções alternativas para o Servidor de Backup do Azure (MABS) V3.

##  <a name="backup-and-recovery-fails-for-clustered-workloads"></a>O Backup do Microsoft Azure e a recuperação falham para cargas de trabalho em cluster

**Descrição:** Backup/restauração falha para fontes de dados clusterizadas, como cluster Hyper-V ou cluster SQL (SQL Always On) ou Exchange no grupo de disponibilidade de banco de dados (DAG) após atualizar o MABS V2 para o MABS V3.

**Solução alternativa:** Para evitar isso, abra o SQL Server Management Studio (SSMS)) e execute o seguinte script SQL no banco de dados do DPM:


    IF EXISTS (SELECT * FROM dbo.sysobjects
        WHERE id = OBJECT_ID(N'[dbo].[tbl_PRM_DatasourceLastActiveServerMap]')
        AND OBJECTPROPERTY(id, N'IsUserTable') = 1)
        DROP TABLE [dbo].[tbl_PRM_DatasourceLastActiveServerMap]
        GO

        CREATE TABLE [dbo].[tbl_PRM_DatasourceLastActiveServerMap] (
            [DatasourceId]          [GUID]          NOT NULL,
            [ActiveNode]            [nvarchar](256) NULL,
            [IsGCed]                [bit]           NOT NULL
            ) ON [PRIMARY]
        GO

        ALTER TABLE [dbo].[tbl_PRM_DatasourceLastActiveServerMap] ADD
    CONSTRAINT [pk__tbl_PRM_DatasourceLastActiveServerMap__DatasourceId] PRIMARY KEY NONCLUSTERED
        (
            [DatasourceId]
        )  ON [PRIMARY],

    CONSTRAINT [DF_tbl_PRM_DatasourceLastActiveServerMap_IsGCed] DEFAULT
        (
            0
        ) FOR [IsGCed]
    GO


##  <a name="upgrade-to-mabs-v3-fails-in-russian-locale"></a>Atualização para o MABS V3 falha no idioma russo

**Descrição:** A atualização do MABS V2 para o MABS V3 no idioma russo falha com um código de erro **4387**.

**Solução alternativa:** Siga estas etapas para atualizar para o MABS V3 usando o pacote de instalação russo:

1.  Faça [Backup do Microsoft Azure](https://docs.microsoft.com/sql/relational-databases/backup-restore/create-a-full-database-backup-sql-server?view=sql-server-2017#SSMSProcedure) no seu banco de dados SQL e desinstale o MABS V2 (escolha manter os dados protegidos durante a desinstalação).
2.  Atualize para o SQL 2017 (Enterprise) e desinstale os relatórios como parte da atualização.
3. [Instale](https://docs.microsoft.com/sql/reporting-services/install-windows/install-reporting-services?view=sql-server-2017#install-your-report-server) o SQL Server Reporting Services (SSRS).
4.  [Instale](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms#download-ssms-181) o SQL Server Management Studio (SSMS).
5.  Configure o Relatório usando os parâmetros conforme documentado na [Configuração do SSRS com o SQL 2017](https://docs.microsoft.com/azure/backup/backup-azure-microsoft-azure-backup#upgrade-mabs).
6.  [Instalar](backup-azure-microsoft-azure-backup.md) MABS V3.
7. [Restaure](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-database-backup-using-ssms?view=sql-server-2017) SQL usando SSMS e a execução da ferramenta de sincronização de DPM, conforme descrito [aqui](https://docs.microsoft.com/previous-versions/system-center/data-protection-manager-2010/ff634215(v=technet.10)).
8.  Atualize a propriedade "DataBaseVersion" na tabela dbo.tbl_DLS_GlobalSetting usando o seguinte comando:

        UPDATE dbo.tbl_DLS_GlobalSetting
        set PropertyValue = '13.0.415.0'
        where PropertyName = 'DatabaseVersion'


9.  Inicie o serviço MSDPM.


## <a name="next-steps"></a>Próximas etapas

[O que há de novo no MABS V3](backup-mabs-whats-new-mabs.md)
