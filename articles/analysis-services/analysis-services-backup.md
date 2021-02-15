---
title: Backup e restauração do banco de dados do Analysis Services do Azure | Microsoft Docs
description: Este artigo descreve como fazer backup e restaurar dados e metadados do modelo de um banco de Azure Analysis Services.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: owend
ms.reviewer: minewiskan
ms.custom: references_regions
ms.openlocfilehash: af1850f77c1d13c761bfc2a143074b5067b349b4
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96012996"
---
# <a name="analysis-services-database-backup-and-restore"></a>Backup e restauração de banco de dados Analysis Services

O backup de bancos de dados de modelos tabulares no Azure Analysis Services é muito semelhante ao do Analysis Services local. A principal diferença é onde você armazena seus arquivos de backup. Os arquivos de backup devem ser salvos em um contêiner em uma [conta de armazenamento do Azure](../storage/common/storage-account-create.md). Você pode usar uma conta de armazenamento e um contêiner que já existam ou eles podem ser criados ao definir as configurações de armazenamento para o seu servidor.

> [!NOTE]
> A criação de uma conta de armazenamento pode resultar em um novo serviço faturável. Para saber mais, consulte [Preços de Armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/blobs/).
> 
> 

> [!NOTE]
> Se a conta de armazenamento estiver em uma região diferente, defina as configurações de firewall da conta de armazenamento para permitir o acesso de **redes selecionadas**. Em **intervalo de endereços** do firewall, especifique o intervalo de endereços IP para a região em que o servidor Analysis Services está. A definição de configurações de firewall da conta de armazenamento para permitir o acesso de todas as redes tem suporte, no entanto, escolher redes selecionadas e especificar um intervalo de endereços IP é preferencial. Para saber mais, consulte [perguntas frequentes sobre conectividade de rede](analysis-services-network-faq.md#backup-and-restore).

Os backups são salvos com uma extensão. ABF. Para modelos tabular na memória, ambos os dados de modelo e metadados são armazenados. Para modelos tabulares do DirectQuery, somente os metadados do modelo são armazenados. Os backups podem ser compactados e criptografados, dependendo das opções escolhidas.


## <a name="configure-storage-settings"></a>Definir as configurações de armazenamento
Antes de fazer backup, é necessário definir as configurações de armazenamento para o servidor.


### <a name="to-configure-storage-settings"></a>Para definir as configurações de armazenamento
1.  No portal do n Azure > **Configurações**, clique em **Backup**.

    ![Backups em Configurações](./media/analysis-services-backup/aas-backup-backups.png)

2.  Clique em **Habilitado** e, em seguida, clique em **Configurações de armazenamento**.

    ![Habilitar](./media/analysis-services-backup/aas-backup-enable.png)

3. Selecione uma conta de armazenamento ou crie uma nova.

4. Selecione um contêiner ou crie um novo.

    ![Selecionar contêiner](./media/analysis-services-backup/aas-backup-container.png)

5. Salve as configurações de backup.

    ![Salvar as configurações de backup](./media/analysis-services-backup/aas-backup-save.png)

## <a name="backup"></a>Backup

### <a name="to-backup-by-using-ssms"></a>Para backup usando SSMS

1. No SSMS, clique com o botão direito do mouse em um banco de dados > **Backup**.

2. Em arquivo de backup do **banco de dados de backup**  >  **Backup file**, clique em **procurar**.

3. Na caixa de diálogo **Salvar arquivo como** verifique o caminho da pasta e digite um nome para o arquivo de backup. 

4. Na caixa de diálogo **Banco de Dados de Backup**, selecione as opções.

    **Permitir substituição de arquivos** -Selecione essa opção para substituir os arquivos de backup do mesmo nome. Se essa opção não estiver selecionada, o arquivo que você está salvando não poderá ter o mesmo nome de um arquivo que já existente no mesmo local.

    **Aplicar compactação** - Selecione essa opção para compactar o arquivo de backup. Arquivos de backup compactados economizam espaço em disco mas exigem uma utilização de CPU ligeiramente maior. 

    **Criptografar arquivo de backup** - Selecione essa opção para criptografar o arquivo de backup. Essa opção requer uma senha fornecida pelo usuário para proteger o arquivo de backup. A senha impede a leitura dos dados de backup por qualquer outro meio exceto uma operação de restauração. Se você optar por criptografar backups, armazene a senha em um local seguro.

5. Clique em **OK** para criar e salvar o arquivo de backup.


### <a name="powershell"></a>PowerShell
Use o cmdlet [Backup-ASDatabase](/powershell/module/sqlserver/backup-asdatabase).

## <a name="restore"></a>Restaurar
Durante a restauração, o arquivo de backup deve ser na conta de armazenamento que você configurou para o servidor. Se você precisar mover um arquivo de backup de um local para sua conta de armazenamento, use [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) ou [AzCopy](../storage/common/storage-use-azcopy-v10.md) utilitário de linha de comando. 



> [!NOTE]
> Se você estiver restaurando de um servidor local, é necessário remover todos os usuários de domínio das funções do modelo e adicioná-los de volta às funções como usuários do Azure Active Directory.
> 
> 

### <a name="to-restore-by-using-ssms"></a>Para restaurar usando o SSMS

1. No SSMS, clique com o botão direito do mouse em um banco de dados > **Restaurar**.

2. Na caixa de diálogo **Banco de Dados de Backup**, em **Arquivo de Backup**, clique em **Navegar**.

3. Na caixa de diálogo **Localizar Arquivos de Banco de Dados**, selecione o arquivo que deseja restaurar.

4. Em **Restaurar banco de dados**, selecione o banco de dados.

5. Especifique as opções. Opções de segurança devem corresponder com as opções de backup usadas durante o backup.


### <a name="powershell"></a>PowerShell

Use o cmdlet [Restore-ASDatabase](/powershell/module/sqlserver/restore-asdatabase).


## <a name="related-information"></a>Informações relacionadas

[Contas de Armazenamento do Azure](../storage/common/storage-account-create.md)  
[Alta disponibilidade](analysis-services-bcdr.md)      
[Perguntas frequentes sobre conectividade de rede Analysis Services](analysis-services-network-faq.md)