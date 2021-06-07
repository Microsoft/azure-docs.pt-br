---
title: Conectar ao Azure Cosmos DB usando ferramentas de análise de BI
description: Saiba como usar o driver ODBC do Azure Cosmos DB para criar tabelas e exibições, de forma que os dados normalizados possam ser exibidos no software de BI e análise de dados.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/02/2019
ms.author: sngun
ms.openlocfilehash: e7d6a67f5322c5bb640430f66ccb0917f6faada1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96003490"
---
# <a name="connect-to-azure-cosmos-db-using-bi-analytics-tools-with-the-odbc-driver"></a>Conectar ao Azure Cosmos DB usando ferramentas de análise de BI com o driver ODBC
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

O driver ODBC do Azure Cosmos DB permite que você se conecte ao Azure Cosmos DB usando ferramentas de análise de BI, como SQL Server Integration Services, Power BI Desktop e Tableau, de forma que você possa analisar e criar visualizações dos dados do Cosmos DB nessas soluções.

O driver ODBC do Azure Cosmos DB está em conformidade com o ODBC 3.8 e dá suporte à sintaxe ANSI SQL-92. O driver oferece recursos avançados para ajudá-lo a renormalizar dados no Azure Cosmos DB. Usando o driver, você pode representar dados no Azure Cosmos DB como tabelas e exibições. O driver permite que você execute operações de SQL em tabelas e exibições incluindo o agrupamento por consultas, inserções, atualizações e exclusões.

> [!NOTE]
> Conectar-se ao Azure Cosmos DB com o driver ODBC no momento, há suporte para apenas para contas de API de SQL do Azure Cosmos DB.

## <a name="why-do-i-need-to-normalize-my-data"></a>Por que preciso normalizar meus dados?
O Azure Cosmos DB é um banco de dados sem esquema, que permite usar o método RAD e a capacidade de iterar em modelos de dados sem estar confinado a um esquema rígido. Um único banco de dados Cosmos do Azure pode conter documentos JSON de várias estruturas. Isso é ótimo para o método RAD, mas quando você deseja analisar e criar relatórios de dados usando ferramentas de BI e análise de dados, os dados geralmente precisam ser nivelados e aderir a um esquema específico.

Esse é o ponto em que o driver ODBC entra. Ao usar o driver ODBC, você pode agora renormalizar dados no Azure Cosmos DB em tabelas e visualizações ajustadas às suas necessidades de relatórios e análise de dados. Os esquemas renormalizados não têm impacto sobre os dados subjacentes e não confinam os desenvolvedores para aderir a eles. Em vez disso, eles permitem utilizar ferramentas em conformidade com ODBC para acessar os dados. Portanto, agora seu banco de dados Cosmos do Azure não será apenas um favorito para sua equipe de desenvolvimento, mas os analistas de data também vão adorar isso.

Vamos começar com o driver ODBC.

## <a name="step-1-install-the-azure-cosmos-db-odbc-driver"></a><a id="install"></a>Etapa 1: Instalar o driver ODBC do Azure Cosmos DB

1. Baixe os drivers para seu ambiente:

    | Instalador | Sistemas operacionais com suporte| 
    |---|---| 
    |[Microsoft Azure Cosmos DB ODBC 64-bit.msi](https://aka.ms/cosmos-odbc-64x64) para Windows de 64 bits| Versões de 64 bits do Windows 8.1 ou posterior, Windows 8, Windows 7, Windows Server 2012 R2, Windows Server 2012 e Windows Server 2008 R2.| 
    |[Microsoft Azure Cosmos DB ODBC 32x64-bit.msi](https://aka.ms/cosmos-odbc-32x64) para 32 bits no Windows de 64 bits| Versões de 64 bits do Windows 8.1 ou posterior, Windows 8, Windows 7, Windows XP, Windows Vista, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 e Windows Server 2003.| 
    |[Microsoft Azure Cosmos DB ODBC 32-bit.msi](https://aka.ms/cosmos-odbc-32x32) para Windows de 32 bits|Versões de 32 bits do Windows 8.1 ou posterior, Windows 8, Windows 7, Windows XP e Windows Vista.|

    Execute o arquivo msi localmente, que inicia o **Assistente de Instalação do Driver ODBC do Microsoft Azure Cosmos DB**. 

1. Conclua o assistente de instalação usando a entrada padrão para instalar o driver ODBC.

1. Abra o aplicativo **Administrador de Fonte de Dados de ODBC** no seu computador. Você pode fazer isso digitando **fontes de dados ODBC** na caixa de pesquisa do Windows. 
    Confirme se o driver foi instalado clicando na guia **Drivers** e garantindo que o **Driver ODBC do Microsoft Azure Cosmos DB** está listado.

    :::image type="content" source="./media/odbc-driver/odbc-driver.png" alt-text="Administrador de fonte de dados ODBC do Azure Cosmos DB":::

## <a name="step-2-connect-to-your-azure-cosmos-database"></a><a id="connect"></a>Etapa 2: conectar-se ao banco de dados Cosmos do Azure

1. Depois de [Instalar o driver ODBC do Azure Cosmos DB](#install), na janela **Administrador de Fonte de Dados ODBC**, clique em **Adicionar**. Você pode criar um DSN de usuário ou de sistema. Neste exemplo, estamos criando um DSN de usuário.

1. Na janela **Criar Nova Fonte de Dados**, selecione **Driver ODBC do Microsoft Azure Cosmos DB** e clique em **Concluir**.

1. Na janela **Instalação de SDN do Driver ODBC do Azure Cosmos DB**, preencha as seguintes informações: 

    :::image type="content" source="./media/odbc-driver/odbc-driver-dsn-setup.png" alt-text="Janela Configuração de DSN do Driver ODBC do Azure Cosmos DB":::
    - **Nome da Fonte de Dados**: seu próprio nome amigável para o DSN do ODBC. Esse nome é exclusivo à sua conta do Azure Cosmos DB; portanto, nomeie-a corretamente se você tiver várias contas.
    - **Descrição**: uma breve descrição da fonte de dados.
    - **Host**: URI de sua conta do Azure Cosmos DB. Você pode recuperar isso na página Chaves do Azure Cosmos DB no portal do Azure, conforme mostra a captura de tela a seguir. 
    - **Chave de Acesso**: a chave primária ou secundária, somente leitura ou gravação da página Chaves do Azure Cosmos DB no portal do Azure, conforme mostra a captura de tela a seguir. É recomendável que você use a chave somente leitura se o DSN for usado para relatórios e processamento de dados somente leitura.
    :::image type="content" source="./media/odbc-driver/odbc-cosmos-account-keys.png" alt-text="Página Chaves do Azure Cosmos DB":::
    - **Criptografar a Tecla de Acesso para**: selecione a melhor opção com base nos usuários deste computador. 
    
1. Clique no botão **Testar** para ter certeza de que você pode se conectar à sua conta do Azure Cosmos DB. 

1.  Clique em **Opções Avançadas de** e defina os seguintes valores:
    *  **Versão da API REST**: selecione a [versão da API REST](/rest/api/cosmos-db/) para suas operações. O padrão 2015-12-16. Se você tiver contêineres com [chaves de partição grandes](large-partition-keys.md) e exigir a versão de API REST 2018-12-31:
        - Digite **2018-12-31** para a versão da API REST
        - No menu **Iniciar** , digite "regedit" para localizar e abrir o aplicativo **Editor do registro** .
        - No editor do registro, navegue até o caminho: **Computer\HKEY_LOCAL_MACHINE\SOFTWARE\ODBC\ODBC.INI**
        - Crie uma nova subchave com o mesmo nome que o seu DSN, por exemplo, "DSN ODBC da conta contoso".
        - Navegue até a subchave "DSN de ODBC da conta contoso".
        - Clique com o botão direito do mouse para adicionar um novo valor de **cadeia de caracteres** :
            - Nome do valor: **IgnoreSessionToken**
            - Dados do valor: **1** 
             :::image type="content" source="./media/odbc-driver/cosmos-odbc-edit-registry.png" alt-text="configurações do editor do registro":::
    - **Consistência de Consulta**: selecione o [nível de consistência](consistency-levels.md) para as operações. O padrão é Sessão.
    - **Número de tentativas**: insira o número de vezes para tentar novamente uma operação se a solicitação inicial não for concluída devido à limitação de taxa de serviço.
    - **Arquivo de Esquema**: você tem várias opções aqui.
        - Por padrão, deixar essa entrada como está (em branco), o driver examina a primeira página de dados de todos os contêineres para determinar o esquema de cada contêiner. Isso é conhecido como mapeamento de contêiner. Sem um arquivo de esquema definido, o driver deve executar o exame para cada sessão de driver e pode resultar em um tempo de inicialização mais alto de um aplicativo usando o DSN. É recomendável sempre associar um arquivo de esquema para um DSN.
        - Se você já tiver um arquivo de esquema (possivelmente um criado usando o editor de esquema), poderá clicar em **procurar**, navegar até o arquivo, clicar em **salvar** e, em seguida, clicar em **OK**.
        - Se você desejar criar um novo esquema, clique em **OK** e clique em **Editor de Esquema** na janela principal. Em seguida, vá para as informações do Editor de Esquema. Ao criar o novo arquivo de esquema, não se esqueça de voltar para a janela **Opções Avançadas** para incluir o arquivo de esquema recém-criado.

1. Depois de concluir e fechar a janela **Instalação de DSN do Driver ODBC do Azure Cosmos DB**, o novo DSN de Usuário é adicionado à guia DSN de Usuário.

    :::image type="content" source="./media/odbc-driver/odbc-driver-user-dsn.png" alt-text="Novo DSN ODBC do Azure Cosmos DB na guia DSN de Usuário":::

## <a name="step-3-create-a-schema-definition-using-the-container-mapping-method"></a><a id="#container-mapping"></a>Etapa 3: criar uma definição de esquema usando o método de mapeamento de contêiner

Há dois tipos de métodos de amostragem que você pode usar: **mapeamento de contêiner** ou **delimitadores de tabela**. Uma sessão de amostragem pode utilizar os dois métodos de amostragem, mas cada contêiner só pode usar um método de amostragem específico. As etapas a seguir criam um esquema para os dados em um ou mais contêineres usando o método de mapeamento de contêiner. Esse método de amostragem recupera os dados na página de um contêiner para determinar a estrutura dos dados. Ele transpõe um contêiner para uma tabela no lado do ODBC. Esse método de amostragem é eficiente e rápido quando os dados em um contêiner são homogêneos. Se um contêiner contiver um tipo de dados heterogêneo, recomendamos que você use o [método de mapeamento de delimitadores de tabela](#table-mapping) , pois ele fornece um método de amostragem mais robusto para determinar as estruturas de dados no contêiner. 

1. Depois de concluir as etapas 1-4 em [conectar-se ao banco de dados Cosmos do Azure](#connect), clique em **Editor de esquema** na janela **instalação de DSN do driver ODBC do Azure Cosmos DB** .

    :::image type="content" source="./media/odbc-driver/odbc-driver-schema-editor.png" alt-text="Botão do editor de esquema na janela Instalação de DSN do Driver ODBC do Azure Cosmos DB":::
1. Na janela **Editor de Esquema**, clique em **Criar Novo**.
    A janela **gerar esquema** exibe todos os contêineres na conta Azure Cosmos DB. 

1. Selecione um ou mais contêineres para amostra e, em seguida, clique em **exemplo**. 

1. Na guia **Modo de Exibição de Design**, o banco de dados, o esquema e a tabela são representados. Na exibição de tabela, o exame mostra o conjunto de propriedades associado aos nomes de coluna (Nome SQL, Nome da Fonte etc.).
    Para cada coluna, você pode modificar o nome SQL, o tipo SQL, o comprimento SQL (se aplicável), a escala (se aplicável), a precisão (se aplicável) e o que permite valor nulo da coluna.
    - Você pode definir **Ocultar Coluna** como **true** se deseja excluir a coluna dos resultados da consulta. As colunas marcadas com Ocultar Coluna = true não são retornadas para seleção e projeção, embora ainda façam parte do esquema. Por exemplo, você pode ocultar todas as propriedades obrigatórias do sistema do Azure Cosmos DB que começam com “_”.
    - A coluna **id** é o único campo que não pode ser oculto porque ele é usado como a chave primária no esquema normalizado. 

1. Depois de concluir a definição do esquema, clique em **arquivo**  |  **salvar**, navegue até o diretório para salvar o esquema e, em seguida, clique em **salvar**.

1. Para usar esse esquema com um DSN, abra a **janela instalação de DSN do driver ODBC do Azure Cosmos DB** (por meio do administrador de fonte de dados ODBC), clique em **Opções avançadas** e, em seguida, na caixa **arquivo de esquema** , navegue até o esquema salvo. Salvar um arquivo de esquema em um DSN existente modifica a conexão DSN de escopo para os dados e a estrutura definidos pelo esquema.

## <a name="step-4-create-a-schema-definition-using-the-table-delimiters-mapping-method"></a><a id="table-mapping"></a>Etapa 4: Criar uma definição de esquema usando o método de mapeamento de delimitadores de tabela

Há dois tipos de métodos de amostragem que você pode usar: **mapeamento de contêiner** ou **delimitadores de tabela**. Uma sessão de amostragem pode utilizar os dois métodos de amostragem, mas cada contêiner só pode usar um método de amostragem específico. 

As etapas a seguir criam um esquema para os dados em um ou mais contêineres usando o método de mapeamento de **delimitadores de tabela** . É recomendável que você use esse método de amostragem quando seus contêineres contiverem tipos de dados heterogêneos. Você pode usar esse método para definir o escopo de amostragem para um conjunto de atributos e seus valores correspondentes. Por exemplo, se um documento contém uma propriedade "Type", você pode definir o escopo de amostragem para os valores dessa propriedade. O resultado final da amostragem seria um conjunto de tabelas para cada um dos valores de Type especificados. Por exemplo, Type = Carro gerará uma tabela Carro, enquanto Type = Avião gerará uma tabela Avião.

1. Depois de concluir as etapas 1-4 em [conectar-se ao banco de dados Cosmos do Azure](#connect), clique em **Editor de esquema** na janela instalação de DSN do driver ODBC do Azure Cosmos DB.

1. Na janela **Editor de Esquema**, clique em **Criar Novo**.
    A janela **gerar esquema** exibe todos os contêineres na conta Azure Cosmos DB. 

1. Selecione um contêiner na guia **exibição de exemplo** , na coluna **definição de mapeamento** do contêiner, clique em **Editar**. Em seguida, na janela **Definição do Mapeamento**, selecione o método **Delimitadores de Tabela**. Faremos o seguinte:

    a. Na caixa **Atributos**, digite o nome de uma propriedade de delimitador. Esta é uma propriedade do documento para a qual você deseja definir o escopo de amostragem, por exemplo, Cidade, e pressione Enter. 

    b. Se você quiser definir o escopo de amostragem para determinados valores para o atributo inserido acima, selecione o atributo na caixa de seleção, insira um valor na caixa **Valor** ( por exemplo, Seattle) e pressione Enter. Você pode continuar a adicionar vários valores para atributos. Apenas certifique-se de que o atributo correto esteja selecionado quando estiver inserindo valores.

    Por exemplo, se você incluir um valor de **Atributos** de Cidade e desejar limitar sua tabela para incluir apenas linhas com um valor de cidade de Nova York e Dubai, insira Cidade na caixa Atributos e Nova York e Dubai na caixa **Valores**.

1. Clique em **OK**. 

1. Depois de concluir as definições de mapeamento para os contêineres que você deseja obter como amostra, na janela **Editor de esquema** , clique em **exemplo**.
     Para cada coluna, você pode modificar o nome SQL, o tipo SQL, o comprimento SQL (se aplicável), a escala (se aplicável), a precisão (se aplicável) e o que permite valor nulo da coluna.
    - Você pode definir **Ocultar Coluna** como **true** se deseja excluir a coluna dos resultados da consulta. As colunas marcadas com Ocultar Coluna = true não são retornadas para seleção e projeção, embora ainda façam parte do esquema. Por exemplo, você pode ocultar todas as propriedades obrigatórias do sistema do Azure Cosmos DB que começam com `_`.
    - A coluna **id** é o único campo que não pode ser oculto porque ele é usado como a chave primária no esquema normalizado. 

1. Depois de concluir a definição do esquema, clique em **arquivo**  |  **salvar**, navegue até o diretório para salvar o esquema e, em seguida, clique em **salvar**.

1. Na janela **Instalação de DSN do Driver ODBC do Azure Cosmos DB**, clique em **Opções Avançadas**. Em seguida, na caixa **Arquivo de Esquema**, navegue até o arquivo de esquema salvo e clique em **OK**. Clique em **OK** novamente para salvar o DSN. Isso salva o esquema que você criou no DSN. 

## <a name="optional-set-up-linked-server-connection"></a>(Opcional) Configurar a conexão de servidor vinculado

Você pode consultar o Azure Cosmos DB do SQL Server Management Studio (SSMS), configurando uma conexão de servidor vinculado.

1. Crie uma fonte de dados do sistema, conforme descrito em [etapa 2](#connect), por exemplo, denominado `SDS Name`.

1. [Instalar o SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) e conecte-se ao servidor. 

1. No editor de consulta do SSMS, crie um objeto de servidor vinculado `DEMOCOSMOS` da fonte de dados com os seguintes comandos. Substitua `DEMOCOSMOS` com o nome do seu servidor vinculado e `SDS Name` com o nome da sua fonte de dados do sistema.

    ```sql
    USE [master]
    GO
    
    EXEC master.dbo.sp_addlinkedserver @server = N'DEMOCOSMOS', @srvproduct=N'', @provider=N'MSDASQL', @datasrc=N'SDS Name'
    
    EXEC master.dbo.sp_addlinkedsrvlogin @rmtsrvname=N'DEMOCOSMOS', @useself=N'False', @locallogin=NULL, @rmtuser=NULL, @rmtpassword=NULL
    
    GO
    ```
    
Para ver o novo nome de servidor vinculado, atualize a lista de servidores vinculados.

:::image type="content" source="./media/odbc-driver/odbc-driver-linked-server-ssms.png" alt-text="Servidor Vinculado em SSMS":::

### <a name="query-linked-database"></a>Consulta de banco de dados vinculado

Para consultar o banco de dados vinculado, insira uma consulta do SSMS. Neste exemplo, a consulta seleciona a tabela no contêiner chamado `customers` :

```sql
SELECT * FROM OPENQUERY(DEMOCOSMOS, 'SELECT *  FROM [customers].[customers]')
```

Executar a consulta. O resultado deve ter esta aparência:

```
attachments/  1507476156    521 Bassett Avenue, Wikieup, Missouri, 5422   "2602bc56-0000-0000-0000-59da42bc0000"   2015-02-06T05:32:32 +05:00 f1ca3044f17149f3bc61f7b9c78a26df
attachments/  1507476156    167 Nassau Street, Tuskahoma, Illinois, 5998   "2602bd56-0000-0000-0000-59da42bc0000"   2015-06-16T08:54:17 +04:00 f75f949ea8de466a9ef2bdb7ce065ac8
attachments/  1507476156    885 Strong Place, Cassel, Montana, 2069       "2602be56-0000-0000-0000-59da42bc0000"   2015-03-20T07:21:47 +04:00 ef0365fb40c04bb6a3ffc4bc77c905fd
attachments/  1507476156    515 Barwell Terrace, Defiance, Tennessee, 6439     "2602c056-0000-0000-0000-59da42bc0000"   2014-10-16T06:49:04 +04:00      e913fe543490432f871bc42019663518
attachments/  1507476156    570 Ruby Street, Spokane, Idaho, 9025       "2602c156-0000-0000-0000-59da42bc0000"   2014-10-30T05:49:33 +04:00 e53072057d314bc9b36c89a8350048f3
```

> [!NOTE]
> O servidor do Cosmos DB vinculado não oferece suporte a nomes de quatro partes. Será retornado um erro semelhante à seguinte mensagem:

```
Msg 7312, Level 16, State 1, Line 44

Invalid use of schema or catalog for OLE DB provider "MSDASQL" for linked server "DEMOCOSMOS". A four-part name was supplied, but the provider does not expose the necessary interfaces to use a catalog or schema.
``` 

## <a name="optional-creating-views"></a>(Opcional) Criando exibições
Você pode definir e criar exibições como parte do processo de amostragem. Essas exibições são equivalentes às exibições do SQL. Elas são somente leitura e são um escopo definido pelas seleções e projeções da consulta SQL do Azure Cosmos DB. 

Para criar um modo de exibição para seus dados, na janela **Editor de esquema** , na coluna **Exibir definições** , clique em **Adicionar** na linha do contêiner para amostrar. 

:::image type="content" source="./media/odbc-driver/odbc-driver-create-view.png" alt-text="Criar um modo de exibição":::


Em seguida, na janela **Definições de Exibição**, faça o seguinte:

1. Clique em **Novo**, insira um nome para exibição, por exemplo, EmployeesfromSeattleView e, em seguida, clique em **OK**.

1. Na janela **Editar exibição**, insira uma consulta do Azure Cosmos DB. Ela deve ser uma [consulta SQL do Azure Cosmos DB](./sql-query-getting-started.md), por exemplo, `SELECT c.City, c.EmployeeName, c.Level, c.Age, c.Manager FROM c WHERE c.City = "Seattle"`. Depois, clique em **OK**.

    :::image type="content" source="./media/odbc-driver/odbc-driver-create-view-2.png" alt-text="Adicionar consulta ao criar um modo de exibição":::


Você pode criar quantas exibições desejar. Depois de definir as exibições, você pode realizar a amostragem dos dados. 

## <a name="step-5-view-your-data-in-bi-tools-such-as-power-bi-desktop"></a>Etapa 5: Exibir os dados em ferramentas de BI como Power BI Desktop

Você pode usar o novo DSN para conectar o Azure Cosmos DB com qualquer ferramenta em conformidade com ODBC, essa etapa simplesmente mostra como se conectar ao Power BI Desktop e criar uma visualização do Power BI.

1. Abra o Power BI Desktop.

1. Clique em **Obter Dados**.

    :::image type="content" source="./media/odbc-driver/odbc-driver-power-bi-get-data.png" alt-text="Obter dados em Power BI Desktop":::

1. Na janela **Obter Dados**, clique em **Outro** | **ODBC** | **Conectar**.

    :::image type="content" source="./media/odbc-driver/odbc-driver-power-bi-get-data-2.png" alt-text="Escolher fonte de dados ODBC em Obter dados do Power BI":::

1. Na janela **Do ODBC**, selecione o nome da fonte de dados que você criou e clique em **OK**. Você pode deixar as entradas de **Opções Avançadas** em branco.

   :::image type="content" source="./media/odbc-driver/odbc-driver-power-bi-get-data-3.png" alt-text="Escolher o nome da fonte de dados (DSN) em Obter dados do Power BI":::

1. Na janela **Acesse uma fonte de dados usando um driver ODBC**, selecione **Padrão ou Personalizada** e clique em **Conectar**. Você não precisa incluir as **Propriedades de cadeia de conexão de credencial**.

1. Na janela **Navegador**, no painel esquerdo, expanda o banco de dados, o esquema e selecione a tabela. O painel de resultados inclui os dados usando o esquema que você criou.

    :::image type="content" source="./media/odbc-driver/odbc-driver-power-bi-get-data-4.png" alt-text="Selecionar a Tabela em Obter dados do Power BI":::

1. Para visualizar os dados no Power BI Desktop, marque a caixa na frente do nome da tabela e clique em **Carregar**.

1. No Power BI Desktop, na extrema esquerda, selecione a guia dados :::image type="icon" source="./media/odbc-driver/odbc-driver-data-tab.png"::: para confirmar que os dados foram importados. 

1. Agora você pode criar visuais usando Power BI clicando na guia relatório :::image type="icon" source="./media/odbc-driver/odbc-driver-report-tab.png"::: , clicando em **novo visual** e, em seguida, personalizando o bloco. Para obter mais informações sobre como criar visualizações no Power BI Desktop, consulte [Tipos de visualização no Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-visualization-types-for-reports-and-q-and-a/). 

## <a name="troubleshooting"></a>Solução de problemas

Se você receber o seguinte erro, verifique se os valores de **Host** e **Tecla de Acesso** copiados do Portal do Azure na [Etapa 2](#connect) estão corretos e tente novamente. Use os botões de cópia à direita dos valores de **Host** e **Tecla de Acesso** no Portal do Azure para copiar os valores sem erros.

```output
[HY000]: [Microsoft][Azure Cosmos DB] (401) HTTP 401 Authentication Error: {"code":"Unauthorized","message":"The input authorization token can't serve the request. Please check that the expected payload is built as per the protocol, and check the key being used. Server used the following payload to sign: 'get\ndbs\n\nfri, 20 jan 2017 03:43:55 gmt\n\n'\r\nActivityId: 9acb3c0d-cb31-4b78-ac0a-413c8d33e373"}
```


## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o Azure Cosmos DB, confira [Bem-vindo ao Azure Cosmos DB](introduction.md).