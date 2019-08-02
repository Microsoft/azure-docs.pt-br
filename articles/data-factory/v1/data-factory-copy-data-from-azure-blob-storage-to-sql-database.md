---
title: Copiar dados do Armazenamento de Blobs para o Banco de Dados SQL - Azure | Microsoft Docs
description: Este tutorial mostra como usar a Atividade de Cópia em um pipeline do Azure Data Factory para copiar dados do Armazenamento de Blobs para um banco de dados SQL.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: ''
editor: ''
ms.assetid: e4035060-93bf-4e8d-bf35-35e2d15c51e0
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: ad114ce3a40e11048d01c6768811089c43cdf1db
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67839383"
---
# <a name="tutorial-copy-data-from-blob-storage-to-sql-database-using-data-factory"></a>Tutorial: Copiar dados do Armazenamento de Blobs para o Banco de Dados SQL usando o Data Factory
> [!div class="op_single_selector"]
> * [Visão geral e pré-requisitos](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Assistente de Cópia](data-factory-copy-data-wizard-tutorial.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Modelo do Azure Resource Manager](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [API REST](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [API do .NET](data-factory-copy-activity-tutorial-using-dotnet-api.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se você estiver usando a versão atual do serviço Data Factory, consulte o [tutorial de atividade de cópia](../quickstart-create-data-factory-dot-net.md). 

Neste tutorial, você cria um data factory com um pipeline para copiar dados do Armazenamento de Blobs para o banco de dados SQL.

A atividade de cópia realiza a movimentação de dados no Azure Data Factory. Ela é habilitada por um serviço disponível globalmente que pode copiar dados entre vários repositórios de dados de forma segura, confiável e escalonável. Veja o artigo [Atividades de movimentação de dados](data-factory-data-movement-activities.md) para obter detalhes sobre a Atividade de Cópia.  

> [!NOTE]
> Para obter uma visão geral detalhada do serviço Data Factory, veja o artigo [Introdução à Azure Data Factory](data-factory-introduction.md) .
>
>

## <a name="prerequisites-for-the-tutorial"></a>Pré-requisitos para o tutorial
Antes de iniciar este tutorial, você deverá ter os seguintes pré-requisitos:

* **Assinatura do Azure**.  Se você não tiver uma assinatura, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Consulte o artigo [Avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/) para obter detalhes.
* **Conta de Armazenamento do Azure**. Você usa o armazenamento de blobs como um armazenamento de dados de **origem** dados neste tutorial. Se você não tiver uma conta de armazenamento do Azure, veja o artigo [Criar uma conta de armazenamento](../../storage/common/storage-quickstart-create-account.md) para conhecer as etapas para criar um.
* **Banco de dados SQL do Azure**. Você usa um banco de dados SQL do Azure como um armazenamento de dados de **destino** neste tutorial. Se você não tiver um banco de dados do SQL Azure que você possa usar no tutorial, consulte [Como criar e configurar um banco de dados SQL do Azure](../../sql-database/sql-database-get-started.md) para criar um.
* **SQL Server 2012/2014 ou Visual Studio 2013**. Você usa SQL Server Management Studio ou Visual Studio para criar um banco de dados de exemplo e exibir os dados de resultado no banco de dados.  

## <a name="collect-blob-storage-account-name-and-key"></a>Coletar o nome e a chave da conta de armazenamento de blobs
Você precisa do nome da conta e chave de conta da sua conta de armazenamento do Azure para concluir este tutorial. Anote o **nome da conta** e a **chave de conta** da sua conta de armazenamento do Azure.

1. Faça logon no [Portal do Azure](https://portal.azure.com/).
2. Clique em **Todos os serviços** no menu esquerdo e selecione **Contas de Armazenamento**.

    ![Procurar - Contas de armazenamento](media/data-factory-copy-data-from-azure-blob-storage-to-sql-database/browse-storage-accounts.png)
3. Na folha **Contas de armazenamento**, selecione a **Conta de armazenamento do Azure** que você deseja usar neste tutorial.
4. Selecione o link **Chaves de acesso** em **CONFIGURAÇÕES**.
5. Clique no botão **copiar** (imagem) ao lado da caixa de texto **Nome da conta de armazenamento** e salve-a/cole-a em algum lugar (por exemplo, em um arquivo de texto).
6. Repita a etapa anterior para copiar ou anote a **chave1**.

    ![Chave de acesso de armazenamento](media/data-factory-copy-data-from-azure-blob-storage-to-sql-database/storage-access-key.png)
7. Feche todas as folhas, clicando em **X**.

## <a name="collect-sql-server-database-user-names"></a>Coletar o servidor SQL, o banco de dados, os nomes de usuário
Você precisa dos nomes do servidor, banco de dados e usuário SQL do Azure para concluir este tutorial. Anote os nomes do **servidor**, **banco de dados** e **usuário** para seu banco de dados SQL do Azure.

1. No **Portal do Azure**, clique em **Todos os serviços** à esquerda e selecione **Bancos de Dados SQL**.
2. Na **folha Bancos de dados SQL**, clique no **banco de dados** que você deseja utilizar neste tutorial. Anote o **nome do banco de dados**.  
3. Em seguida, na folha **Banco de dados SQL**, clique em **Propriedades** em **CONFIGURAÇÕES**.
4. Anote os valores para **NOME DO SERVIDOR** e **LOGON DE ADMINISTRADOR DO SERVIDOR**.
5. Feche todas as folhas, clicando em **X**.

## <a name="allow-azure-services-to-access-sql-server"></a>Permitir que os serviços do Azure acessem o servidor
Certifique-se de a configuração **Permitir acesso aos serviços do Azure** esteja definida como **ATIVADA** para o servidor do SQL Azure, para que o serviço Data Factory possa acessar seu SQL Server do Azure. Para verificar e ativar essa configuração, faça as seguintes etapas:

1. Clique no hub **Todos os serviços** à esquerda e clique em **Servidores SQL Server**.
2. Selecione seu servidor e clique em **Firewall** em **CONFIGURAÇÕES**.
3. Na folha **Configurações de Firewall**, clique em **ATIVADO** para **Permitir acesso aos serviços do Azure**.
4. Feche todas as folhas, clicando em **X**.

## <a name="prepare-blob-storage-and-sql-database"></a>Preparar o Armazenamento de Blobs e o banco de dados SQL
Agora, prepare seu armazenamento de blob do Azure e o banco de dados SQL do Azure para o tutorial, executando as seguintes etapas:  

1. Inicie o Bloco de notas. Copie o texto a seguir e salve-o como **emp.txt** na pasta **C:\ADFGetStarted** em seu disco rígido.

    ```
    John, Doe
    Jane, Doe
    ```
2. Use ferramentas como o [Gerenciador de Armazenamento do Azure](https://storageexplorer.com/) para criar o contêiner **adftutorial** e carregar o arquivo **emp.txt** no contêiner.

3. Use o script SQL a seguir para criar a tabela **emp** no seu Banco de Dados SQL do Azure.  

    ```SQL
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50),
    )
    GO

    CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);
    ```

    **Se você tiver o SQL Server 2012/2014 instalado no computador:** siga as instruções em [Gerenciando o Banco de Dados SQL do Azure usando o SQL Server Management Studio](../../sql-database/sql-database-manage-azure-ssms.md) para se conectar ao servidor SQL do Azure e executar o script SQL. 

    Se o cliente não tiver permissão para acessar o servidor SQL do Azure, você precisará configurar o firewall para o servidor SQL do Azure permitir o acesso no seu computador (endereço IP). Veja [este artigo](../../sql-database/sql-database-configure-firewall-settings.md) para obter as etapas para configurar o firewall para o SQL Server do Azure.

## <a name="create-a-data-factory"></a>Criar uma data factory
Você concluiu os pré-requisitos. Você pode criar um data factory usando um dos caminhos a seguir. Clique em uma das opções na lista suspensa na parte superior ou nos links a seguir para executar o tutorial.     

* [Assistente de Cópia](data-factory-copy-data-wizard-tutorial.md)
* [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
* [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
* [Modelo do Azure Resource Manager](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
* [API REST](data-factory-copy-activity-tutorial-using-rest-api.md)
* [API do .NET](data-factory-copy-activity-tutorial-using-dotnet-api.md)

> [!NOTE]
> O pipeline de dados neste tutorial copia os dados de um armazenamento de dados de origem para um armazenamento de dados de destino. Ele não transforma dados de entrada para gerar dados de saída. Para ver um tutorial sobre como transformar dados usando o Azure Data Factory, consulte [Tutorial: criar seu primeiro pipeline para transformar dados usando um cluster Hadoop](data-factory-build-your-first-pipeline.md).
> 
> É possível encadear duas atividades (executar uma atividade após a outra) definindo o conjunto de dados de saída de uma atividade como o conjunto de dados de entrada da outra atividade. Confira [Agendamento e execução no Data Factory](data-factory-scheduling-and-execution.md) para obter informações detalhadas. 
