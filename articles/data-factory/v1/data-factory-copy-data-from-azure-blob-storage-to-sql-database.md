---
title: Copiar dados do armazenamento de BLOBs para o banco de dados SQL – Azure
description: Este tutorial mostra como usar a Atividade de Cópia em um pipeline do Azure Data Factory para copiar dados do Armazenamento de Blobs para um banco de dados SQL.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 24cedc6a1e0be66e9a924a50e25257f18b7f96a6
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100376880"
---
# <a name="tutorial-copy-data-from-blob-storage-to-sql-database-using-data-factory"></a>Tutorial: copiar dados do Armazenamento de Blobs para o Banco de Dados SQL usando o Data Factory
> [!div class="op_single_selector"]
> * [Visão geral e pré-requisitos](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Assistente de Cópia](data-factory-copy-data-wizard-tutorial.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Modelo do Azure Resource Manager](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [REST API](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [API do .NET](data-factory-copy-activity-tutorial-using-dotnet-api.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se você estiver usando a versão atual do serviço Data Factory, consulte o [tutorial de atividade de cópia](../quickstart-create-data-factory-dot-net.md).

Neste tutorial, você cria um data factory com um pipeline para copiar dados do armazenamento de BLOBs para o banco de dados SQL.

A atividade de cópia realiza a movimentação de dados no Azure Data Factory. Ela é habilitada por um serviço disponível globalmente que pode copiar dados entre vários repositórios de dados de forma segura, confiável e escalonável. Veja o artigo [Atividades de movimentação de dados](data-factory-data-movement-activities.md) para obter detalhes sobre a Atividade de Cópia.  

> [!NOTE]
> Para obter uma visão geral detalhada do serviço Data Factory, veja o artigo [Introdução à Azure Data Factory](data-factory-introduction.md) .
>
>

## <a name="prerequisites-for-the-tutorial"></a>Pré-requisitos para o tutorial
Antes de iniciar este tutorial, você deverá ter os seguintes pré-requisitos:

* **Assinatura do Azure**.  Se você não tiver uma assinatura, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Consulte o artigo [Avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/) para obter detalhes.
* **Conta de armazenamento do Azure**. Você usa o armazenamento de blobs como um armazenamento de dados de **origem** dados neste tutorial. Se você não tiver uma conta de armazenamento do Azure, consulte o artigo [criar uma conta de armazenamento](../../storage/common/storage-account-create.md) para obter as etapas para criar uma.
* **Banco de dados SQL do Azure**. Você usa o banco de dados SQL do Azure como um armazenamento de dado de **destino** neste tutorial. Se você não tiver um banco de dados no banco de dados SQL do Azure que possa ser usado no tutorial, consulte [como criar e configurar um banco de dados no banco de dados SQL do Azure](../../azure-sql/database/single-database-create-quickstart.md) para criar um.
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
Você precisa dos nomes do SQL Server, do banco de dados e do usuário lógicos para fazer este tutorial. Anote os nomes do **servidor, do** **banco de dados** e do **usuário** para o banco de dados SQL do Azure.

1. No **Portal do Azure**, clique em **Todos os serviços** à esquerda e selecione **Bancos de Dados SQL**.
2. Na **folha Bancos de dados SQL**, clique no **banco de dados** que você deseja utilizar neste tutorial. Anote o **nome do banco de dados**.  
3. Em seguida, na folha **Banco de dados SQL**, clique em **Propriedades** em **CONFIGURAÇÕES**.
4. Anote os valores para **NOME DO SERVIDOR** e **LOGON DE ADMINISTRADOR DO SERVIDOR**.
5. Feche todas as folhas, clicando em **X**.

## <a name="allow-azure-services-to-access-sql-server"></a>Permitir que os serviços do Azure acessem o servidor
Verifique se a configuração **permitir acesso aos serviços do Azure** está ativada para o **servidor para que** o serviço de data Factory possa acessar o servidor. Para verificar e ativar essa configuração, faça as seguintes etapas:

1. Clique no hub **Todos os serviços** à esquerda e clique em **Servidores SQL Server**.
2. Selecione seu servidor e clique em **Firewall** em **CONFIGURAÇÕES**.
3. Na folha **Configurações de Firewall**, clique em **ATIVADO** para **Permitir acesso aos serviços do Azure**.
4. Feche todas as folhas, clicando em **X**.

## <a name="prepare-blob-storage-and-sql-database"></a>Preparar o Armazenamento de Blobs e o banco de dados SQL
Agora, prepare o armazenamento de BLOBs do Azure e o banco de dados SQL do Azure para o tutorial executando as seguintes etapas:  

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

    **Se você tiver o SQL Server 2012/2014 instalado no computador:** siga as instruções em [Gerenciando o banco de dados SQL do Azure usando SQL Server Management Studio](../../azure-sql/database/single-database-manage.md) para se conectar ao servidor e executar o script SQL.

    Se o cliente não tiver permissão para acessar o SQL Server lógico, você precisará configurar o firewall para o servidor para permitir o acesso do seu computador (endereço IP). Consulte [Este artigo](../../azure-sql/database/firewall-configure.md) para obter as etapas para configurar o firewall para seu servidor.

## <a name="create-a-data-factory"></a>Criar um data factory
Você concluiu os pré-requisitos. Você pode criar um data factory usando um dos caminhos a seguir. Clique em uma das opções na lista suspensa na parte superior ou nos links a seguir para executar o tutorial.     

* [Assistente de Cópia](data-factory-copy-data-wizard-tutorial.md)
* [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
* [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
* [Modelo do Azure Resource Manager](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
* [REST API](data-factory-copy-activity-tutorial-using-rest-api.md)
* [API do .NET](data-factory-copy-activity-tutorial-using-dotnet-api.md)

> [!NOTE]
> O pipeline de dados neste tutorial copia os dados de um armazenamento de dados de origem para um armazenamento de dados de destino. Ele não transforma dados de entrada para gerar dados de saída. Para obter um tutorial sobre como transformar dados usando o Azure Data Factory, confira [Tutorial: criar seu primeiro pipeline para transformar dados usando o cluster Hadoop](data-factory-build-your-first-pipeline.md).
>
> É possível encadear duas atividades (executar uma atividade após a outra) definindo o conjunto de dados de saída de uma atividade como o conjunto de dados de entrada da outra atividade. Confira [Agendamento e execução no Data Factory](data-factory-scheduling-and-execution.md) para obter informações detalhadas.