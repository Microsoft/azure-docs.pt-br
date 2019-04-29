---
title: Backup e restauração do banco de dados do Analysis Services do Azure | Microsoft Docs
description: Descreve como fazer backup e restaurar um banco de dados do Analysis Services do Azure.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 31e8e65b382a3a6bcad2998a0babdf9605dc4968
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61023814"
---
# <a name="backup-and-restore"></a>Backup e restauração

O backup de bancos de dados de modelos tabulares no Azure Analysis Services é muito semelhante ao do Analysis Services local. A principal diferença é onde você armazena seus arquivos de backup. Os arquivos de backup devem ser salvos em um contêiner em uma [conta de armazenamento do Azure](../storage/common/storage-create-storage-account.md). Você pode usar uma conta de armazenamento e um contêiner que já existam ou eles podem ser criados ao definir as configurações de armazenamento para o seu servidor.

> [!NOTE]
> A criação de uma conta de armazenamento pode resultar em um novo serviço faturável. Para saber mais, consulte [Preços de Armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/blobs/).
> 
> 

Os backups são salvos com uma extensão. abf. Para modelos tabular na memória, ambos os dados de modelo e metadados são armazenados. Para modelos tabulares do DirectQuery, somente os metadados do modelo são armazenados. Os backups podem ser compactados e criptografados, dependendo das opções escolhidas.


## <a name="configure-storage-settings"></a>Definir as configurações de armazenamento
Antes de fazer backup, é necessário definir as configurações de armazenamento para o servidor.


### <a name="to-configure-storage-settings"></a>Para definir as configurações de armazenamento
1.  No portal do n Azure > **Configurações**, clique em **Backup**.

    ![Backups em Configurações](./media/analysis-services-backup/aas-backup-backups.png)

2.  Clique em **Habilitado** e, em seguida, clique em **Configurações de armazenamento**.

    ![Habilitar](./media/analysis-services-backup/aas-backup-enable.png)

3. Selecione uma conta de armazenamento ou crie uma nova.

4. Selecione um contêiner ou crie um novo.

    ![Selecione o contêiner](./media/analysis-services-backup/aas-backup-container.png)

5. Salve as configurações de backup.

    ![Salvar as configurações de backup](./media/analysis-services-backup/aas-backup-save.png)

## <a name="backup"></a>Backup

### <a name="to-backup-by-using-ssms"></a>Para backup usando SSMS

1. No SSMS, clique com o botão direito do mouse em um banco de dados > **Backup**.

2. Em **Banco de Dados de Backup** > **Arquivo de Backup**, clique em **Navegar**.

3. Na caixa de diálogo **Salvar arquivo como** verifique o caminho da pasta e digite um nome para o arquivo de backup. 

4. Na caixa de diálogo **Banco de Dados de Backup**, selecione as opções.

    **Permitir substituição de arquivos** -Selecione essa opção para substituir os arquivos de backup do mesmo nome. Se essa opção não estiver selecionada, o arquivo que você está salvando não poderá ter o mesmo nome de um arquivo que já existente no mesmo local.

    **Aplicar compactação** - Selecione essa opção para compactar o arquivo de backup. Arquivos de backup compactados economizam espaço em disco mas exigem uma utilização de CPU ligeiramente maior. 

    **Criptografar arquivo de backup** - Selecione essa opção para criptografar o arquivo de backup. Essa opção requer uma senha fornecida pelo usuário para proteger o arquivo de backup. A senha impede a leitura dos dados de backup por qualquer outro meio exceto uma operação de restauração. Se você optar por criptografar backups, armazene a senha em um local seguro.

5. Clique em **OK** para criar e salvar o arquivo de backup.


### <a name="powershell"></a>PowerShell
Use o cmdlet [Backup-ASDatabase](https://docs.microsoft.com/sql/analysis-services/powershell/backup-asdatabase-cmdlet).

## <a name="restore"></a>Restaurar
Durante a restauração, o arquivo de backup deve ser na conta de armazenamento que você configurou para o servidor. Se você precisar mover um arquivo de backup de um local para sua conta de armazenamento, use [Microsoft Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer) ou [AzCopy](../storage/common/storage-use-azcopy.md) utilitário de linha de comando. 



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

Use o cmdlet [Restore-ASDatabase](https://docs.microsoft.com/sql/analysis-services/powershell/restore-asdatabase-cmdlet).


## <a name="related-information"></a>Informações relacionadas

[Contas de Armazenamento do Azure](../storage/common/storage-create-storage-account.md)  
[Alta disponibilidade](analysis-services-bcdr.md)     
[Gerenciar Azure Analysis Services](analysis-services-manage.md)
