---
title: Agendar trabalhos do U-SQL do Azure Data Lake Analytics usando SSIS
description: Saiba como usar SQL Server Integration Services para agendar trabalhos U-SQL com script embutido ou arquivos de consulta do U-SQL.
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 07/17/2018
ms.openlocfilehash: b080b433f5af49e970faba02003fb68e21a08365
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92221444"
---
# <a name="schedule-u-sql-jobs-using-sql-server-integration-services-ssis"></a>Agendar trabalhos do U-SQL usando SSIS (SQL Server Integration Services)

Neste documento, você aprende como orquestrar e criar trabalhos do U-SQL usando SSIS (SQL Server Integration Service). 

## <a name="prerequisites"></a>Pré-requisitos

O [Feature Pack do Azure para Serviços de Integração](/sql/integration-services/azure-feature-pack-for-integration-services-ssis#scenario-managing-data-in-the-cloud) fornece a [tarefa do Azure Data Lake Analytics](/sql/integration-services/control-flow/azure-data-lake-analytics-task) e o [Gerenciador de Conexões do Azure Data Lake Analytics](/sql/integration-services/connection-manager/azure-data-lake-analytics-connection-manager) que ajuda a conectar o serviço do Azure Data Lake Analytics. Para usar essa tarefa, certifique-se de instalar:

- [Baixar e instalar o SSDT (SQL Server Data Tools) para o Visual Studio](/sql/ssdt/download-sql-server-data-tools-ssdt)
- [Instalar o Feature Pack do Azure para SSIS (Integration Services)](/sql/integration-services/azure-feature-pack-for-integration-services-ssis)

## <a name="azure-data-lake-analytics-task"></a>Tarefa do Azure Data Lake Analytics

A tarefa do Azure Data Lake Analytics permite que os usuários enviem trabalhos do U-SQL para a conta do Azure Data Lake Analytics. 

[Saiba como configurar a tarefa do Azure Data Lake Analytics](/sql/integration-services/control-flow/azure-data-lake-analytics-task).

![Tarefa do Azure Data Lake Analytics no SSIS](./media/data-lake-analytics-schedule-jobs-ssis/data-lake-analytics-azure-data-lake-analytics-task-in-ssis.png)

É possível obter o script do U-SQL de diferentes locais usando as funções e tarefas internas do SSIS; os cenários abaixo mostram como você pode configurar os scripts do U-SQL para diferentes casos de usuários.

## <a name="scenario-1-use-inline-script-call-tvfs-and-stored-procs"></a>Cenário 1 - Use o script em linha chamado tvfs e procedimentos armazenados

No Editor de Tarefas do Azure Data Lake Analytics, configure **SourceType** como **DirectInput** e coloque as instruções do U-SQL em **USQLStatement**.

Para facilitar a manutenção e o gerenciamento de código, coloque apenas scripts curtos de U-SQL como scripts embutidos, por exemplo, você pode chamar funções de valor de tabela existentes e procedimentos armazenados nos bancos de dados do U-SQL. 

![Editar script do U-SQL em linha na tarefa do SSIS](./media/data-lake-analytics-schedule-jobs-ssis/edit-inline-usql-script-in-ssis.png)

Artigo relacionado: [Como passar parâmetros para procedimentos armazenados](#scenario-6-pass-parameters-to-u-sql-script)

## <a name="scenario-2-use-u-sql-files-in-azure-data-lake-store"></a>Cenário 2 - Usar arquivos do U-SQL no Azure Data Lake Store

Também é possível usar arquivos do U-SQL no Azure Data Lake Store usando a **Tarefa do Sistema de Arquivos do Azure Data Lake Store** no Feature Pack do Azure. Essa abordagem permite usar os scripts armazenados na nuvem.

Siga as etapas abaixo para configurar a conexão entre a Tarefa do Sistema de Arquivos do Azure Data Lake Store e a Tarefa do Azure Data Lake Analytics.

### <a name="set-task-control-flow"></a>Definir fluxo de controle de tarefa

No modo de exibição de design do pacote SSIS, adicione uma **Tarefa do Sistema de Arquivos do Azure Data Lake Store**, um **Contêiner do Loop Foreach** e uma **Tarefa do Azure Data Lake Analytics** no contêiner do Loop Foreach. A Tarefa do Sistema de Arquivos do Azure Data Lake Store ajuda a baixar arquivos do U-SQL da conta do ADLS para uma pasta temporária. O Contêiner do Loop Foreach e a Tarefa do Azure Data Lake Analytics ajudam a enviar cada arquivo do U-SQL na pasta temporária para a conta do Azure Data Lake Analytics como uma tarefa do U-SQL.

![Diagrama que mostra uma Azure Data Lake Store tarefa do sistema de arquivos que está sendo adicionada a um contêiner Loop Foreach.](./media/data-lake-analytics-schedule-jobs-ssis/use-u-sql-files-in-azure-data-lake-store.png)

### <a name="configure-azure-data-lake-store-file-system-task"></a>Configurar Tarefa do Sistema de Arquivos do Azure Data Lake Store

1. Defina a **Operação** para **CopyFromADLS**.
2. Configure **AzureDataLakeConnection**, saiba mais sobre o [Gerenciador de conexões do Azure Data Lake Store](/sql/integration-services/connection-manager/azure-data-lake-store-connection-manager).
3. Defina **AzureDataLakeDirectory**. Aponte para a pasta que armazena os scripts do U-SQL. Use o caminho relativo que é relativo à pasta raiz da conta do Azure Data Lake Store.
4. Defina o **Destino** para uma pasta que armazene em cache os scripts do U-SQL baixados. Este caminho de pasta será usado no Contêiner do Loop Foreach para envio de trabalhos do U-SQL. 

![Configurar Tarefa do Sistema de Arquivos do Azure Data Lake Store](./media/data-lake-analytics-schedule-jobs-ssis/configure-azure-data-lake-store-file-system-task.png)

[Saiba mais sobre a Tarefa do Sistema de Arquivos do Azure Data Lake Store](/sql/integration-services/control-flow/azure-data-lake-store-file-system-task).

### <a name="configure-foreach-loop-container"></a>Configurar o Contêiner do Loop Foreach

1. Na página **Coleção**, defina **Enumerador** para **Enumerador de Arquivo Foreach**.

2. Defina **Pasta** no grupo **Configuração do enumerador** para a pasta temporária que inclui os scripts do U-SQL baixados.

3. Defina **Arquivos** em **Configuração do enumerador** para `*.usql`, de modo que o contêiner de loop capture apenas os arquivos que terminam com `.usql`.

    ![Captura de tela que mostra o editor de loop foreach com "coleção" selecionada e as seções de configuração de enumerador e enumerador realçadas.](./media/data-lake-analytics-schedule-jobs-ssis/configure-foreach-loop-container-collection.png)

4. Na página **Mapeamentos de Variáveis** adicione uma variável definida pelo usuário para obter o nome do arquivo para cada arquivo do U-SQL. Defina o **Índice** para 0 para obter o nome do arquivo. Neste exemplo, defina uma variável chamada `User::FileName`. Essa variável será usada para obter dinamicamente a conexão do arquivo de script do U-SQL e definir o nome da tarefa do U-SQL na tarefa do Azure Data Lake Analytics.

    ![Configurar o contêiner do Loop Foreach para obter o nome do arquivo](./media/data-lake-analytics-schedule-jobs-ssis/configure-foreach-loop-container-variable-mapping.png)

### <a name="configure-azure-data-lake-analytics-task"></a>Configurar tarefa do Azure Data Lake Analytics 

1. Defina **SourceType** para **FileConnection**.

2. Defina **FileConnection** como a conexão de arquivo que aponta para os objetos de arquivo retornados do Contêiner do Loop Foreach.
    
    Para criar essa conexão de arquivo:

   1. Escolha a **\<New Connection...>** configuração de FileConnection.
   2. Defina **Tipo de uso** para **Arquivo existente** e defina o **Arquivo** para o caminho de arquivo de qualquer arquivo existente.

       ![Captura de tela que mostra o editor do Gerenciador de conexões de arquivo com "arquivo existente" selecionado para "tipo de uso".](./media/data-lake-analytics-schedule-jobs-ssis/configure-file-connection-for-foreach-loop-container.png)

   3. Na exibição **Gerenciadores de Conexões**, clique com o botão direito do mouse na conexão de arquivo criada agora e escolha **Propriedades**.

   4. Na janela **Propriedades**, expanda **Expressões** e defina **ConnectionString** para a variável definida no Contêiner do Loop Foreach, por exemplo, `@[User::FileName]`.

       ![Configurar o Contêiner do Loop Foreach](./media/data-lake-analytics-schedule-jobs-ssis/configure-file-connection-property-for-foreach-loop-container.png)

3. Defina **AzureDataLakeAnalyticsConnection** na conta do Azure Data Lake Analytics para a qual deseja enviar tarefas. Saiba mais sobre o [Gerenciador de Conexões do Azure Data Lake Analytics](/sql/integration-services/connection-manager/azure-data-lake-analytics-connection-manager).

4. Defina outras configurações de trabalho. [Saiba mais](/sql/integration-services/control-flow/azure-data-lake-analytics-task).

5. Use **Expressões** para definir dinamicamente o nome do trabalho do U-SQL:

    1. Na página **Expressões**, adicione um novo par chave-valor de expressão para **JobName**.
    2. Configure o valor de JobName para a variável definida no Contêiner do Loop Foreach, por exemplo, `@[User::FileName]`.
    
        ![Configurar a Expressão do SSIS para o nome do trabalho do U-SQL](./media/data-lake-analytics-schedule-jobs-ssis/configure-expression-for-u-sql-job-name.png)

## <a name="scenario-3-use-u-sql-files-in-azure-blob-storage"></a>Cenário 3 - Usar arquivos do U-SQL no Armazenamento de Blobs do Azure

É possível usar arquivos do U-SQL no Armazenamento de Blobs do Azure, usando a **Tarefa de Download de Blob do Azure** no Feature Pack do Azure. Essa abordagem permite usar os scripts na nuvem.

As etapas são semelhantes ao [cenário 2: usar arquivos U-SQL no Azure data Lake Store](#scenario-2-use-u-sql-files-in-azure-data-lake-store). Altere a Tarefa do Sistema de Arquivos do Azure Data Lake Store para Tarefa de Download de Blob do Azure. [Saiba mais sobre a Tarefa de Download de Blob do Azure](/sql/integration-services/control-flow/azure-blob-download-task).

O fluxo de controle é conforme abaixo.

![Usar arquivos do U-SQL no Azure Data Lake Store](./media/data-lake-analytics-schedule-jobs-ssis/use-u-sql-files-in-azure-blob-storage.png)

## <a name="scenario-4-use-u-sql-files-on-the-local-machine"></a>Cenário 4 - Usar arquivos do U-SQL no computador local

Além de usar arquivos do U-SQL armazenados na nuvem, você também poderá usar arquivos no computador local ou arquivos implantados com os pacotes SSIS.

1. Clique com o botão direito do mouse em **Gerenciadores de Conexões** no projeto SSIS e escolha **Novo Gerenciador de Conexões**.

2. Selecione o tipo de **Arquivo** e clique em **Adicionar...**.

3. Defina **Tipo de uso** para **Arquivo existente** e defina o **Arquivo** para o arquivo no computador local.

    ![Adicionar conexão de arquivo ao arquivo local](./media/data-lake-analytics-schedule-jobs-ssis/configure-file-connection-for-foreach-loop-container.png)

4. Adicionar **Azure data Lake Analytics** tarefa e:
    1. Defina **SourceType** para **FileConnection**.
    2. Defina **FileConnection** para a Conexão do Arquivo criada agora.

5. Conclua outras configurações para Tarefa do Azure Data Lake Analytics.

## <a name="scenario-5-use-u-sql-statement-in-ssis-variable"></a>Cenário 5 - Usar instrução do U-SQL na variável do SSIS

Em alguns casos, talvez seja necessário gerar dinamicamente as instruções do U-SQL. É possível usar a **Variável do SSIS** com a **Expressão do SSIS** e outras tarefas do SSIS, como Tarefa Script, para ajudá-lo a gerar a instrução do U-SQL dinamicamente.

1. Abra a janela de ferramentas Variáveis no menu de nível superior **SSIS > Variáveis**.

2. Adicione uma variável do SSIS e defina o valor diretamente ou use a **Expressão** para gerar o valor.

3. Adicione a **Tarefa do Azure Data Lake Analytics** e:
    1. Defina **SourceType** para **Variável**.
    2. Configure **SourceVariable** para a variável do SSIS criada agora.

4. Conclua outras configurações para Tarefa do Azure Data Lake Analytics.

## <a name="scenario-6-pass-parameters-to-u-sql-script"></a>Cenário 6 - Passar parâmetros para script do U-SQL

Em alguns casos, convém definir dinamicamente o valor da variável do U-SQL no script do U-SQL. O recurso **Mapeamento de Parâmetros** na Tarefa do Azure Data Lake Analytics ajuda com esse cenário. Geralmente, há dois casos típicos de usuários:

- Defina as variáveis de caminho do arquivo de entrada e saída dinamicamente com base na data e hora atuais.
- Defina o parâmetro para procedimentos armazenados.

[Saiba mais sobre como definir parâmetros para o script do U-SQL](/sql/integration-services/control-flow/azure-data-lake-analytics-task#parameter-mapping-page-configuration).

## <a name="next-steps"></a>Próximas etapas

- [Executar pacotes SSIS no Azure](../data-factory/how-to-invoke-ssis-package-ssis-activity.md)
- [Feature Pack do Azure para o Integration Services (SSIS)](/sql/integration-services/azure-feature-pack-for-integration-services-ssis#scenario-managing-data-in-the-cloud)
- [Agendar trabalhos do U-SQL usando o Azure Data Factory](../data-factory/transform-data-using-data-lake-analytics.md)