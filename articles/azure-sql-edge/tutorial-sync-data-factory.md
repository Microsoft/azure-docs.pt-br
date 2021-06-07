---
title: Sincronizar dados do SQL do Azure no Edge usando o Azure Data Factory
description: Saiba mais sobre a sincronização de dados entre o SQL do Azure no Edge e o armazenamento de blobs do Azure
keywords: SQL do Azure no Edge, sincronizar dados do SQL do Azure no Edge, data factory do SQL do Azure no Edge
services: sql-edge
ms.service: sql-edge
ms.topic: tutorial
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: b83201ae864d1f1eb9124af5268360bb1748f6c8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97507601"
---
# <a name="tutorial-sync-data-from-sql-edge-to-azure-blob-storage-by-using-azure-data-factory"></a>Tutorial: Sincronizar dados do SQL do Azure no Edge para o Armazenamento de Blobs do Azure usando o Azure Data Factory

Neste tutorial, você usa o Azure Data Factory para sincronizar dados de maneira incremental de uma tabela em uma instância do SQL do Azure no Edge para um Armazenamento de Blobs do Azure.

## <a name="before-you-begin"></a>Antes de começar

Se você ainda não criou um banco de dados ou uma tabela em sua implantação do SQL do Azure no Edge, use um destes métodos para criar um:

* Use o [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms/) ou o [Azure Data Studio](/sql/azure-data-studio/download/) para conectar-se a um SQL do Azure no Edge. Execute um script SQL para criar o banco de dados e a tabela.
* Crie um banco de dados e uma tabela usando [SQLCMD](/sql/tools/sqlcmd-utility/), conectando-se diretamente ao módulo do SQL no Edge. Para obter mais informações, confira [Conectar-se ao Mecanismo de Banco de Dados usando SQLCMD](/sql/ssms/scripting/sqlcmd-connect-to-the-database-engine/).
* Use SQLPackage.exe para implantar um arquivo do pacote de DAC no contêiner do SQL do Azure no Edge. Você pode automatizar esse processo especificando o URI do arquivo SqlPackage como parte da configuração das propriedades desejadas do módulo. Você também pode usar diretamente a ferramenta de cliente SqlPackage.exe para implantar um pacote de DAC no SQL do Azure no Edge.

    Para obter informações sobre como baixar o SqlPackage.exe, confira [Baixar e instalar o sqlpackage](/sql/tools/sqlpackage-download/). A seguir estão alguns comandos de exemplo para o SqlPackage.exe. Para obter mais informações, confira a documentação do SqlPackage.exe.

    **Criar um pacote de DAC**

    ```cmd
    sqlpackage /Action:Extract /SourceConnectionString:"Data Source=<Server_Name>,<port>;Initial Catalog=<DB_name>;User ID=<user>;Password=<password>" /TargetFile:<dacpac_file_name>
    ```

    **Aplicar um pacote de DAC**

    ```cmd
    sqlpackage /Action:Publish /Sourcefile:<dacpac_file_name> /TargetServerName:<Server_Name>,<port> /TargetDatabaseName:<DB_Name> /TargetUser:<user> /TargetPassword:<password>
    ```

## <a name="create-a-sql-table-and-procedure-to-store-and-update-the-watermark-levels"></a>Criar uma tabela SQL e um procedimento para armazenar e atualizar os níveis de marca-d'água

Uma tabela de marca-d'água é usada para armazenar o último carimbo de data/hora até o qual os dados foram sincronizados com o Armazenamento do Azure. Um procedimento armazenado T-SQL (Transact-SQL) é usado para atualizar a tabela de marca-d'água após cada sincronização.

Execute estes comandos na instância do SQL do Azure no Edge:

```sql
    Create table [dbo].[watermarktable]
    (
    TableName varchar(255),
    WatermarkValue datetime,
    )
    GO

    CREATE PROCEDURE usp_write_watermark @timestamp datetime, @TableName varchar(50)  
    AS  
    BEGIN
    
    UPDATE [dbo].[watermarktable]
    SET [WatermarkValue] = @timestamp
    WHERE [TableName] = @TableName

    END
    Go
```

## <a name="create-a-data-factory-pipeline"></a>Criar um pipeline do Data Factory

Nesta seção, você cria um pipeline do Azure Data Factory para sincronizar os dados de uma tabela em um SQL do Azure no Edge para um Armazenamento de Blobs do Azure.

### <a name="create-a-data-factory-by-using-the-data-factory-ui"></a>Criar um data factory usando a interface do usuário do Data Factory

Crie um Data Factory seguindo as instruções fornecidas [neste tutorial](../data-factory/quickstart-create-data-factory-portal.md#create-a-data-factory).

### <a name="create-a-data-factory-pipeline"></a>Criar um pipeline do Data Factory

1. Na página **Introdução** da interface do usuário do Data Factory, selecione **Criar pipeline**.

    ![Criar um pipeline do Data Factory](media/tutorial-sync-data-factory/data-factory-get-started.png)

2. Na página **Geral** da janela **Propriedades** para o pipeline, para o nome, insira **PeriodicSync**.

3. Adicione a atividade Pesquisa para obter o valor antigo da marca-d'água. No painel **Atividades**, expanda **Geral** e arraste a atividade de **Pesquisa** para a superfície do designer de pipeline. Altere o nome da atividade para **OldWatermark**.

    ![Adicionar a pesquisa de marca-d'água antiga](media/tutorial-sync-data-factory/create-old-watermark-lookup.png)

4. Alterne para a guia **Configurações** e, para o **Conjunto de Dados de Origem**, selecione **Novo**. Você criará agora um conjunto de dados para representar os dados na tabela de marca-d'água. Esta tabela contém a marca d'água antiga que foi usada na operação de cópia anterior.

5. Na janela **Novo Conjunto de Dados**, selecione **SQL Server do Azure** e selecione **Continuar**.  

6. Na janela **Definir Propriedades** para o conjunto de dados, em **Nome**, insira **WatermarkDataset**.

7. Para **Serviço Vinculado**, selecione **Novo** e conclua estas etapas:

    1. Em **Nome**, insira **SQLDBEdgeLinkedService**.

    2. Em **Nome do Servidor**, insira os detalhes do servidor do SQL do Azure no Edge.

    3. Selecione o **Nome do banco de dados** na lista.

    4. Insira o **Nome de Usuário** e a **Senha**.

    5. Para testar a conexão à instância do SQL do Azure no Edge, clique em **Testar conexão**.

    6. Selecione **Criar**.

    ![Criar um serviço vinculado](media/tutorial-sync-data-factory/create-linked-service.png)

    7. Selecione **OK**.

8. Na guia **Configurações**, selecione **Editar**.

9. Na guia **Conexão**, selecione **[dbo].[watermarktable]** para **Tabela**. Se você quiser visualizar os dados na tabela, selecione **Visualizar Dados**.

10. Alterne para o editor de pipeline selecionando a guia Pipeline na parte superior ou selecionando o nome do pipeline no modo de exibição de árvore à esquerda. Na janela Propriedades para a atividade de Pesquisa, confirme se **WatermarkDataset** está selecionado para o campo **Conjunto de dados de origem**.

11. No painel **Atividades**, expanda **Geral** e arraste outra atividade de **Pesquisa** para a superfície do designer de pipeline. Defina o nome para **NewWatermark** na guia **geral** da janela Propriedades. Esta atividade de Pesquisa obtém o novo valor de marca-d'água da tabela que contém os dados de origem para que esse valor possa ser copiado para o destino.

12. Na janela Propriedades da segunda atividade de Pesquisa, alterne para a guia **Configurações** e selecione **Novo** para criar um conjunto de dados que aponte para a tabela de origem que contém o novo valor de marca-d'água.

13. Na janela **Novo Conjunto de Dados**, selecione a **instância do SQL do Azure no Edge** e selecione **Continuar**.

    1. Na janela **Definir propriedades**, em **Nome**, insira **SourceDataset**. Em **Serviço vinculado**, selecione **SQLDBEdgeLinkedService**.

    2. Em **Tabela**, selecione a tabela que você deseja sincronizar. Você também pode especificar uma consulta para esse conjunto de dados, conforme mencionado mais adiante neste tutorial. A consulta terá precedência sobre a tabela que você especificar nesta etapa.

    3. Selecione **OK**.

14. Alterne para o editor de pipeline selecionando a guia Pipeline na parte superior ou selecionando o nome do pipeline no modo de exibição de árvore à esquerda. Na janela Propriedades para a atividade de Pesquisa, confirme se **SourceDataset** está selecionado na lista **Conjunto de dados de origem**.

15. Selecione **Consulta** em **Usar consulta**. Atualize o nome da tabela na consulta a seguir e, em seguida, insira a consulta. Você está selecionando apenas o valor máximo de `timestamp` da tabela. Verifique se você selecionou **Somente primeira linha**.

    ```sql
    select MAX(timestamp) as NewWatermarkvalue from [TableName]
    ```

    ![selecionar consulta](media/tutorial-sync-data-factory/select-query-data-factory.png)

16. No painel **Atividades**, expanda **Mover e Transformar** e arraste a atividade **Copiar** do painel **Atividades** para a superfície do designer. Defina o nome da atividade como **IncrementalCopy**.

17. Conecte ambas as atividades de Pesquisa à atividade de Cópia arrastando o botão verde anexado às atividades de Pesquisa para a atividade de Cópia. Solte o botão do mouse quando a cor da borda da atividade de Cópia ficar azul.

18. Selecione a atividade de Cópia e confirme se você vê as propriedades da atividade na janela **Propriedades**.

19. Alterne para a guia **Fonte** na janela **Propriedades** e conclua estas etapas:

    1. Na caixa **Conjunto de dados de origem**, selecione **SourceDataset**.

    2. Em **Usar consulta**, selecione **Consulta**.

    3. Insira a consulta SQL na caixa **Consulta**. Aqui está um exemplo de consulta:

    ```sql
    select * from TemperatureSensor where timestamp > '@{activity('OldWaterMark').output.firstRow.WatermarkValue}' and timestamp <= '@{activity('NewWaterMark').output.firstRow.NewWatermarkvalue}'
    ```

20. Na guia **Coletor**, selecione **Novo** em **Conjunto de Dados do Coletor**.

21. Neste tutorial, o armazenamento de dados do coletor é um armazenamento de dados do Armazenamento de Blobs do Azure. Selecione **Armazenamento de Blobs do Azure** depois selecione **Continuar** na janela **Novo Conjunto de Dados**.

22. Na janela **Selecionar Formato**, selecione o formato dos seus dados e depois selecione **Continuar**.

23. Na janela **Definir Propriedades**, em **Nome**, insira **SinkDataset**. Em **Serviço vinculado**, selecione **Novo**. Você agora criará uma conexão (um serviço vinculado) ao Armazenamento de Blobs do Azure.

24. Na janela **Novo Serviço Vinculado (Armazenamento de Blobs do Azure)** , conclua estas etapas:

    1. Na caixa **Nome**, insira **AzureStorageLinkedService**.

    2. Em **Nome da Conta de Armazenamento**, selecione a conta do Armazenamento do Microsoft Azure para a sua assinatura do Azure.

    3. Teste a conexão e selecione **Concluir**.

25. Na janela **Definir Propriedades**, confirme se **AzureStorageLinkedService** está selecionado em **Serviço vinculado**. Selecione **Criar** e **OK**.

26. Na guia **Coletor**, selecione **Editar**.

27. Vá para a guia **Conexão** de SinkDataset e conclua estas etapas:

    1. Em **Caminho do arquivo**, insira *asdedatasync/incrementalcopy*, em que *asdedatasync* é o nome do contêiner de blob e *incrementalcopy* é o nome da pasta. Crie o contêiner caso ele não exista ou use o nome de um contêiner existente. O Azure Data Factory criará automaticamente a pasta de saída *incrementalcopy* se ela não existir. Você também pode usar o botão **Procurar** para o **Caminho de arquivo** para navegar até uma pasta em um contêiner de blob.

    2. Para a parte referente ao **Arquivo** do **Caminho do arquivo**, selecione **Adicionar conteúdo dinâmico [Alt+P]** e, em seguida, insira **@CONCAT('Incremental-', pipeline().RunId, '.txt')** na janela que se abre. Selecione **Concluir**. Neste tutorial, o nome do arquivo é gerado dinamicamente pela expressão. Cada execução de pipeline possui uma ID exclusiva. A atividade de Cópia usa a ID de execução para gerar o nome do arquivo.

28. Alterne para o editor de pipeline selecionando a guia Pipeline na parte superior ou selecionando o nome do pipeline no modo de exibição de árvore à esquerda.

29. No painel **Atividades**, expanda **Geral** e arraste a atividade de **Procedimento armazenado** do painel **Atividades** para a superfície de designer do pipeline. Conecte a saída verde (bem-sucedida) da atividade de Cópia à atividade de Procedimento Armazenado.

30. Selecione **Atividade de Procedimento Armazenado** no designer de pipeline e altere o nome dela para **SPtoUpdateWatermarkActivity**.

31. Alterne para a guia **Conta SQL** e selecione **_QLDBEdgeLinkedService_* em **Serviço vinculado**.

32. Alterne para a guia **Procedimento Armazenado** e conclua estas etapas:

    1. No **Nome do procedimento armazenado**, selecione **[dbo].[usp_write_watermark]** .

    2. Para especificar valores para os parâmetros de procedimento armazenado, selecione **Importar parâmetro** e insira estes valores para os parâmetros:

    |Nome|Tipo|Valor|
    |-----|----|-----|
    |LastModifiedtime|Datetime|@{activity('NewWaterMark').output.firstRow.NewWatermarkvalue}|
    |TableName|String|@{activity('OldWaterMark').output.firstRow.TableName}|

33. Para validar as configurações de pipeline, selecione **Validar** na barra de ferramentas. Confirme se não houver nenhum erro de validação. Para fechar a janela **Relatório de Validação do Pipeline** selecione **>>** .

34. Publique as entidades (serviços vinculados, conjuntos de dados e pipelines) para o serviço de Azure Data Factory selecionando o botão **Publicar Tudo**. Aguarde até que você veja uma mensagem confirmando que a operação de publicação foi bem-sucedida.

## <a name="trigger-a-pipeline-based-on-a-schedule"></a>Disparar um pipeline com base em uma agenda

1. Na barra de ferramentas do pipeline, clique em **Adicionar Gatilho**, selecione **Novo/Editar** e **Novo**.

2. Nomeie seu gatilho **HourlySync**. Em **Tipo**, selecione **Agendamento**. Defina a **Recorrência** para a cada uma hora.

3. Selecione **OK**.

4. Selecione **Publicar Tudo**.

5. Selecione **Disparar Agora**.

6. Alterne para a guia **Monitorar** à esquerda. Você pode ver o status da execução do pipeline disparado pelo gatilho manual. Selecione **Atualizar** para atualizar a lista.

## <a name="next-steps"></a>Próximas etapas

O pipeline do Azure Data Factory neste tutorial copia os dados de uma tabela em uma instância do SQL do Azure no Edge para uma localização no Armazenamento de Blobs do Azure uma vez a cada hora. Para saber mais sobre como usar Data Factory em outros cenários, confira estes [tutoriais](../data-factory/tutorial-copy-data-portal.md).
