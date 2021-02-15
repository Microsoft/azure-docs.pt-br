---
title: Notas de versão do Servidor de Backup do Azure v3
description: Este artigo fornece informações sobre os problemas conhecidos e soluções alternativas para o Backup do Microsoft Azure Server (MABS) v3.
ms.topic: conceptual
ms.date: 06/03/2020
ms.asset: 0c4127f2-d936-48ef-b430-a9198e425d81
ms.openlocfilehash: 87bc415c125a387d98ac88255d77fb1867564acf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91254254"
---
# <a name="release-notes-for-microsoft-azure-backup-server"></a>Notas de versão do Serviço de Backup do Azure

Este artigo fornece os problemas conhecidos e soluções alternativas para o Servidor de Backup do Azure (MABS) V3.

## <a name="backup-and-recovery-fails-for-clustered-workloads"></a>O Backup do Microsoft Azure e a recuperação falham para cargas de trabalho em cluster

**Descrição:** Backup/restauração falha para fontes de dados clusterizadas, como cluster Hyper-V ou cluster SQL (SQL Always On) ou Exchange no grupo de disponibilidade de banco de dados (DAG) após atualizar o MABS V2 para o MABS V3.

**Para contornar:** Para evitar isso, abra o SQL Server Management Studio (SSMS)) e execute o seguinte script SQL no banco de dados do DPM:

```sql
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
```

## <a name="upgrade-to-mabs-v3-fails-in-russian-locale"></a>Atualização para o MABS V3 falha no idioma russo

**Descrição:** A atualização do MABS V2 para o MABS V3 no idioma russo falha com um código de erro **4387**.

**Solução alternativa:** Siga estas etapas para atualizar para o MABS V3 usando o pacote de instalação russo:

1. Faça [Backup do Microsoft Azure](/sql/relational-databases/backup-restore/create-a-full-database-backup-sql-server#SSMSProcedure) no seu banco de dados SQL e desinstale o MABS V2 (escolha manter os dados protegidos durante a desinstalação).
2. Atualize para o SQL 2017 (Enterprise) e desinstale os relatórios como parte da atualização.
3. [Instalar](/sql/reporting-services/install-windows/install-reporting-services#install-your-report-server) o SQL Server Reporting Services (SSRS).
4. [Instale](/sql/ssms/download-sql-server-management-studio-ssms) o SQL Server Management Studio (SSMS).
5. Configure o Relatório usando os parâmetros conforme documentado na [Configuração do SSRS com o SQL 2017](./backup-azure-microsoft-azure-backup.md#upgrade-mabs).
6. [Instalar](backup-azure-microsoft-azure-backup.md) MABS V3.
7. [Restaure](/sql/relational-databases/backup-restore/restore-a-database-backup-using-ssms) SQL usando SSMS e a execução da ferramenta de sincronização de DPM, conforme descrito [aqui](/system-center/dpm/back-up-the-dpm-server#using-dpmsync).
8. Atualize a propriedade "DataBaseVersion" na tabela dbo.tbl_DLS_GlobalSetting usando o seguinte comando:

    ```sql
            UPDATE dbo.tbl_DLS_GlobalSetting
            set PropertyValue = '13.0.415.0'
            where PropertyName = 'DatabaseVersion'
    ```

9. Inicie o serviço MSDPM.

## <a name="after-installing-ur1-the-mabs-reports-arent-updated-with-new-rdl-files"></a>Depois de instalar o UR1, os relatórios do MABS não são atualizados com novos arquivos RDL

**Descrição**: com UR1, o problema de formatação do relatório mAbs é corrigido com arquivos RDL atualizados. Os novos arquivos RDL não são substituídos automaticamente por arquivos existentes.

**Solução alternativa**: para substituir os arquivos RDL, siga as etapas abaixo:

1. No computador MABS, abra o SQL Reporting Services URL do portal da Web.
1. Na URL do portal da Web, a pasta DPMReports está presente no formato de **`DPMReports_<GUID>`**

    >[!NOTE]
    >Sempre há apenas uma pasta com essa Convenção de nomenclatura. Se o MABS for atualizado de uma versão anterior, talvez haja outra pasta mais antiga, mas você não poderá abri-la.

    ![Pasta DPMReports](./media/backup-mabs-release-notes-v3/dpm-reports-folder.png)

1. Selecione e abra a **`DPMReports_<GUID>`** pasta. Os arquivos de relatório individuais serão listados como mostrado abaixo.

    ![Lista de arquivos de relatório individuais](./media/backup-mabs-release-notes-v3/individual-report-files.png)

1. Selecione os arquivos de relatório que não terminam com o **relatório**, clique com o botão direito do mouse na **opção** e selecione **gerenciar**.

    ![Selecionar gerenciar para arquivos de relatório](./media/backup-mabs-release-notes-v3/manage-files.png)

1. Na página novo, selecione a opção **substituir** para substituir os arquivos pelos arquivos de relatório mais recentes.

    Os arquivos de relatório mais recentes podem ser encontrados no caminho `<MABS Installation Directory>\Program Files\Microsoft Azure Backup Server\DPM\DPM\bin\DpmReports`

    Por exemplo: `C:\Program Files\Microsoft Azure Backup Server\DPM\DPM\bin\DpmReports`

    ![Substituir os arquivos pelos arquivos de relatório mais recentes](./media/backup-mabs-release-notes-v3/replace-files.png)

    Depois que os arquivos forem substituídos, verifique se o **Nome** e a **Descrição** estão intactos e não estão vazios.

1. Depois que os arquivos forem substituídos, reinicie os serviços MABS e use os arquivos de relatório.

## <a name="next-steps"></a>Próximas etapas

[O que há de novo no MABS](backup-mabs-whats-new-mabs.md)
