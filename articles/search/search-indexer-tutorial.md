---
title: 'Tutorial: Indexar dados de bancos de dados SQL do Azure em um código de exemplo C# – Azure Search'
description: Um exemplo de código C# que mostra como se conectar ao banco de dados SQL do Azure, extrair dados pesquisáveis e carregá-los em um índice do Azure Search.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.devlang: na
ms.topic: tutorial
ms.date: 05/02/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: bb082fb83f8c2521b0deabced6f851e62b785e8f
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67485381"
---
# <a name="c-tutorial-crawl-an-azure-sql-database-using-azure-search-indexers"></a>Tutorial do C#: Rastrear um Banco de Dados SQL do Azure usando indexadores do Azure Search

Saiba como configurar um indexador para extrair dados pesquisáveis de um Banco de dados SQL do Azure de exemplo. [Indexadores](search-indexer-overview.md) são componentes do Azure Search que rastreiam as fontes de dados externas, preenchendo um [índice de pesquisa](search-what-is-an-index.md) com conteúdo. De todos os indexadores, o indexador do Banco de Dados SQL do Azure é o mais amplamente utilizado. 

Ser proficiente na configuração do indexador é útil porque ele simplifica a quantidade de código que você precisa escrever e manter. Em vez de preparar e enviar por push um conjunto de dados JSON compatíveis com esquema, você pode anexar um indexador para uma fonte de dados, fazer o indexador extrair dados e inseri-lo em um índice e, opcionalmente, executar o indexador em um agendamento recorrente para acompanhar as alterações na fonte subjacente.

Neste tutorial, use as [bibliotecas de cliente do .NET do Azure Search](https://aka.ms/search-sdk) e um aplicativo de console do .NET Core para executar as seguintes tarefas:

> [!div class="checklist"]
> * Adiciona informações de serviço de pesquisa às configurações do aplicativo
> * Prepara um conjunto de dados externo no banco de dados SQL do Azure 
> * Analisa as definições de índice e indexador no código de exemplo
> * Executa o código do indexador para importar dados
> * Pesquisar o índice
> * Exibe configuração do indexador no portal

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Os serviços, as ferramentas e os dados a seguir são usados neste início rápido. 

[Crie um serviço Azure Search](search-create-service-portal.md) ou [localize um serviço existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) na assinatura atual. Você pode usar um serviço gratuito para este tutorial.

O [Banco de Dados SQL do Azure](https://azure.microsoft.com/services/sql-database/) armazena a fonte de dados externa usada por um indexador. A solução de exemplo fornece um arquivo de dados do SQL para criar a tabela. As etapas para criação do serviço e do banco de dados são fornecidas neste tutorial.

O [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/), qualquer edição, pode ser usado para executar a solução de amostra. O código de exemplo e as instruções foram testados na edição gratuita da Comunidade.

[Azure-Samples/search-dotnet-getting-started](https://github.com/Azure-Samples/search-dotnet-getting-started) fornece a solução de amostra, localizada no repositório GitHub de amostras do Azure. Baixe e extraia a solução. Por padrão, as soluções são somente leitura. Clique com o botão direito do mouse na solução e desmarque o atributo somente leitura para que você possa modificar arquivos.

> [!Note]
> Se você estiver usando o serviço do Azure Search gratuito, você está limitado a três índices, três indexadores e três fontes de dados. Este tutorial cria um de cada. Não se esqueça de deixar um espaço em seu serviço para aceitar os novos recursos.

## <a name="get-a-key-and-url"></a>Obter uma chave e uma URL

As chamadas REST exigem a URL do serviço e uma chave de acesso em cada solicitação. Um serviço de pesquisa é criado com ambos, portanto, se você adicionou o Azure Search à sua assinatura, siga estas etapas para obter as informações necessárias:

1. [Entre no portal do Azure](https://portal.azure.com/) e, na página **Visão Geral** do serviço de pesquisa, obtenha a URL. Um ponto de extremidade de exemplo pode parecer com `https://mydemo.search.windows.net`.

1. Em **Configurações** > **Chaves**, obtenha uma chave de administração para adquirir todos os direitos sobre o serviço. Há duas chaves de administração intercambiáveis, fornecidas para a continuidade dos negócios, caso seja necessário sobrepor uma. É possível usar a chave primária ou secundária em solicitações para adicionar, modificar e excluir objetos.

![Obter um ponto de extremidade HTTP e uma chave de acesso](media/search-get-started-postman/get-url-key.png "Obter um ponto de extremidade HTTP e uma chave de acesso")

Todas as solicitações requerem uma chave de api em cada pedido enviado ao serviço. Ter uma chave válida estabelece a relação de confiança, para cada solicitação, entre o aplicativo que envia a solicitação e o serviço que lida com ela.

## <a name="set-up-connections"></a>Configurar as conexões
As informações de conexão para os serviços necessários são especificadas no arquivo **appsettings.json** da solução. 

1. No Visual Studio, abra o arquivo **DotNetHowToIndexers.sln**.

1. No Gerenciador de Soluções, abra **appsettings.json** para popular cada configuração.  

As duas primeiras entradas podem ser preenchidas agora com a URL e as chaves de administrador para o serviço Azure Search. Dado um ponto de extremidade de `https://mydemo.search.windows.net`, o nome do serviço a ser fornecido é `mydemo`.

```json
{
  "SearchServiceName": "Put your search service name here",
  "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
  "AzureSqlConnectionString": "Put your Azure SQL database connection string here",
}
```

A última entrada exige um banco de dados existente. Você o criará na próxima etapa.

## <a name="prepare-sample-data"></a>Preparar os dados de exemplo

Nesta etapa, crie uma fonte de dados externa que pode ser rastreada por um indexador. Você pode usar o portal do Azure e o arquivo *hotels.sql* de exemplo para criar o conjunto de dados no Banco de Dados SQL do Azure. O Azure Search consome conjuntos de linhas bidimensionais, como aqueles gerados em uma exibição ou consulta. O arquivo SQL na solução de exemplo cria e preenche uma única tabela.

O exercício a seguir pressupõe que não há nenhum servidor ou banco de dados existentes e instrui você a criar os dois na etapa 2. Opcionalmente, se você tiver um recurso existente, pode adicionar a tabela hotéis, começando na etapa 4.

1. [Entre no Portal do Azure](https://portal.azure.com/). 

2. Localize ou crie um **Banco de Dados SQL do Azure** para criar um banco de dados, um servidor e um grupo de recursos. Você pode usar os padrões e o tipo de preço com o valor mais baixo. Uma vantagem de criar um servidor é que você pode especificar um nome de usuário e senha do administrador, necessários para criar e carregar tabelas em uma etapa posterior.

   ![Nova página de banco de dados](./media/search-indexer-tutorial/indexer-new-sqldb.png)

3. Clique em **Criar** para implantar o novo servidor e o banco de dados. Aguarde a implantação do servidor e do banco de dados.

4. Abra a página do Banco de Dados SQL para o seu novo banco de dados, se ela já não estiver aberta. O nome do recurso deve dizer *Banco de Dados SQL* e não *SQL Server*.

   ![Página do banco de dados SQL](./media/search-indexer-tutorial/hotels-db.png)

4. No painel de navegação, clique em **Editor de consultas (visualização)** .

5. Clique em **Logon** e insira o nome de usuário e senha do administrador do servidor.

6. Clique em **Abrir consulta** e navegue até o local de *hotels.sql*. 

7. Selecione o arquivo e clique em **Abrir**. O script deve ser semelhante à captura de tela a seguir:

   ![Script SQL](./media/search-indexer-tutorial/sql-script.png)

8. Clique em **Executar** para executar a consulta. No painel Resultados, você verá uma mensagem de consulta bem-sucedida, para 3 linhas.

9. Para retornar um conjunto de linhas desta tabela, você pode executar a consulta a seguir como uma etapa de verificação:

    ```sql
    SELECT HotelId, HotelName, Tags FROM Hotels
    ```
    A consulta de modelo, `SELECT * FROM Hotels`, não funciona no Editor de Consultas. Os dados de exemplo incluem coordenadas geográficas no campo Localização, que atualmente não é tratado no editor. Para obter uma lista das outras colunas para consultar, você pode executar essa instrução: `SELECT * FROM sys.columns WHERE object_id = OBJECT_ID('dbo.Hotels')`

10. Agora que você tem um conjunto de dados externo, copie a cadeia de conexão ADO.NET para o banco de dados. Na página de Banco de Dados SQL do seu banco de dados, vá para **Configurações** > **Cadeias de Conexão** e copie a cadeia de conexão ADO.NET.
 
    Uma cadeia de conexão ADO.NET se parece com o exemplo a seguir, modificada para usar um nome de banco de dados, nome de usuário e senha válidos.

    ```sql
    Server=tcp:hotels-db.database.windows.net,1433;Initial Catalog=hotels-db;Persist Security Info=False;User ID={your_username};Password={your_password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
    ```
11. Cole a cadeia de conexão "AzureSqlConnectionString" como a terceira entrada no arquivo **appsettings.json** no Visual Studio.

    ```json
    {
      "SearchServiceName": "<placeholder-Azure-Search-service-name>",
      "SearchServiceAdminApiKey": "<placeholder-admin-key-for-Azure-Search>",
      "AzureSqlConnectionString": "Server=tcp:hotels-db.database.windows.net,1433;Initial Catalog=hotels-db;Persist Security  Info=False;User ID={your_username};Password={your_password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;",
    }
    ```

## <a name="understand-the-code"></a>Compreender o código

Depois que os dados e as configurações estiverem em vigor, o programa de exemplo em **DotNetHowToIndexers.sln** estará pronto para ser criado e executado. Antes de fazer isso, reserve um minuto para estudar as definições de índice e indexador para este exemplo. O código relevante encontra-se em dois arquivos:

  + **hotel.cs**, que contém o esquema que define o índice
  + **Program.cs**, que contém as funções para criar e gerenciar estruturas em seu serviço

### <a name="in-hotelcs"></a>Em hotel.cs

O esquema de índice define a coleção de campos, incluindo atributos especificando as operações permitidas, por exemplo, se um campo é de texto completo que pode ser pesquisado, filtrado ou classificado, conforme mostrado na definição de campo a seguir para HotelName. 

```csharp
. . . 
[IsSearchable, IsFilterable, IsSortable]
public string HotelName { get; set; }
. . .
```

Um esquema também pode incluir outros elementos, incluindo perfis de pontuação para acelerar uma pontuação de pesquisa, analisadores personalizados e outros constructos. No entanto, neste caso, o esquema é definido de forma esparsa, consistindo somente em campos encontrados nos conjuntos de dados de exemplo.

Neste tutorial, o indexador extrai dados de uma fonte de dados. Na prática, você pode anexar vários indexadores ao mesmo índice, criando um índice pesquisável consolidado de várias fontes de dados. Você pode usar o mesmo par de índice e indexador, variando apenas as fontes de dados ou um índice com várias combinações de indexador e fonte de dados, dependendo de onde você precisa de flexibilidade.

### <a name="in-programcs"></a>Em Program.cs

O programa principal inclui lógica para a criação de um cliente, um índice, uma fonte de dados e um indexador. O código verifica e exclui os recursos existentes do mesmo nome, sob a suposição de que você pode executar este programa várias vezes.

O objeto de fonte de dados é configurado com configurações específicas de recursos do banco de dados SQL do Azure, incluindo a [indexação incremental](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#capture-new-changed-and-deleted-rows) para aproveitar os [recursos internos de detecção de alterações](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server) do Azure SQL. O banco de dados de hotéis de demonstração no SQL Azure tem uma coluna de "exclusão reversível" chamada **IsDeleted**. Quando essa coluna está definida como true no banco de dados, o indexador remove o documento correspondente do índice do Azure Search.

  ```csharp
  Console.WriteLine("Creating data source...");

  DataSource dataSource = DataSource.AzureSql(
      name: "azure-sql",
      sqlConnectionString: configuration["AzureSQLConnectionString"],
      tableOrViewName: "hotels",
      deletionDetectionPolicy: new SoftDeleteColumnDeletionDetectionPolicy(
          softDeleteColumnName: "IsDeleted",
          softDeleteMarkerValue: "true"));
  dataSource.DataChangeDetectionPolicy = new SqlIntegratedChangeTrackingPolicy();

  searchService.DataSources.CreateOrUpdateAsync(dataSource).Wait();
  ```

Um objeto de indexador é independente da plataforma, com invocação, a programação e a configuração sendo as mesmas independentemente da origem. Esse indexador de exemplo inclui uma agenda e uma opção de redefinir que limpa o histórico do indexador e chama um método para criar e executar o indexador imediatamente.

  ```csharp
  Console.WriteLine("Creating Azure SQL indexer...");
  Indexer indexer = new Indexer(
      name: "azure-sql-indexer",
      dataSourceName: dataSource.Name,
      targetIndexName: index.Name,
      schedule: new IndexingSchedule(TimeSpan.FromDays(1)));
  // Indexers contain metadata about how much they have already indexed
  // If we already ran the sample, the indexer will remember that it already
  // indexed the sample data and not run again
  // To avoid this, reset the indexer if it exists
  exists = await searchService.Indexers.ExistsAsync(indexer.Name);
  if (exists)
  {
      await searchService.Indexers.ResetAsync(indexer.Name);
  }

  await searchService.Indexers.CreateOrUpdateAsync(indexer);

  // We created the indexer with a schedule, but we also
  // want to run it immediately
  Console.WriteLine("Running Azure SQL indexer...");

  try
  {
      await searchService.Indexers.RunAsync(indexer.Name);
  }
  catch (CloudException e) when (e.Response.StatusCode == (HttpStatusCode)429)
  {
      Console.WriteLine("Failed to run indexer: {0}", e.Response.Content);
  }
  ```



## <a name="run-the-indexer"></a>Executar o indexador

Nesta etapa, vamos compilar e executar o programa. 

1. No Gerenciador de Soluções, clique com o botão direito do mouse em **DotNetHowToIndexers** e selecione **Compilar**.
2. Novamente, clique com o botão direito do mouse em **DotNetHowToIndexers**, depois em **Depurar** > **Iniciar nova instância**.

O programa é executado no modo de depuração. Uma janela de console relata o status de cada operação.

  ![Script SQL](./media/search-indexer-tutorial/console-output.png)

Seu código é executado localmente no Visual Studio, conectando-se ao seu serviço de pesquisa no Azure, que por sua vez usa a cadeia de conexão para se conectar ao Banco de Dados SQL do Azure e recuperar o conjunto de dados. Com este número de operações, há vários potenciais pontos de falha, mas se você receber um erro, verifique primeiro as condições a seguir:

+ As informações de conexão do serviço de pesquisa que você fornece são limitadas ao nome do serviço neste tutorial. Se você digitou a URL completa, as operações são interrompidas durante a criação do índice, com um erro de falha de conexão.

+ Informações de conexão do banco de dados em **appsettings.json**. Elas devem ser limitadas à cadeia de conexão ADO.NET obtida no portal, modificadas para incluir um nome de usuário e senha válidos para o seu banco de dados. A conta de usuário deve ter permissão para recuperar dados.

+ Limites de recursos. Lembre-se de que o serviço (gratuito) compartilhado tem um limite de 3 índices, indexadores e fontes de dados. Um serviço no limite máximo não pode criar novos objetos.

## <a name="search-the-index"></a>Pesquisar o índice 

No portal do Azure, na página de Visão geral do serviço de pesquisa, clique em **Gerenciador de pesquisa** na parte superior para enviar algumas consultas no novo índice.

1. Clique em **Alterar índice** na parte superior para selecionar o índice *hotéis*.

2. Clique no botão **Pesquisar** para emitir uma pesquisa vazia. 

   As três entradas no índice são retornadas como documentos JSON. O Gerenciador de pesquisa retorna documentos em JSON para que você possa exibir toda a estrutura.

3. Em seguida, insira uma cadeia de caracteres de pesquisa: `search=river&$count=true`. 

   Essa consulta invoca a pesquisa de texto completo no termo `river`, e o resultado inclui uma contagem dos documentos correspondentes. Retornar a contagem de correspondência de documentos é útil em cenários de teste, quando você tiver um índice grande com milhares ou milhões de documentos. Nesse caso, apenas um documento corresponde à consulta.

4. Por fim, insira uma cadeia de caracteres de pesquisa que limita a saída JSON para campos de interesse: `search=river&$count=true&$select=hotelId, baseRate, description`. 

   A resposta da consulta é reduzida para os campos selecionados, resultando em uma saída mais concisa.

## <a name="view-indexer-configuration"></a>Exibir configuração do indexador

Todos os indexadores, incluindo o que você acabou de criar de forma programática, são listados no portal. Você pode abrir uma definição do indexador e exibir sua fonte de dados ou configurar um agendamento de atualização para obter linhas novas e alteradas.

1. [Entre no portal do Azure](https://portal.azure.com/) e, na página **Visão geral** de seu serviço de pesquisa, clique nos links para **Índices**, **Indexadores** e **Fontes de dados**.
3. Selecione objetos individuais para exibir ou modificar as definições de configuração.

   ![Blocos de indexador e de fonte de dados](./media/search-indexer-tutorial/tiles-portal.png)

## <a name="clean-up-resources"></a>Limpar recursos

A maneira mais rápida de fazer a limpeza depois de um tutorial é excluindo o grupo de recursos que contém o serviço Azure Search. Você pode excluir o grupo de recursos agora para excluir permanentemente todo o conteúdo. No portal, o nome do grupo de recursos está na página Visão geral do serviço Azure Search.

## <a name="next-steps"></a>Próximas etapas

Você pode anexar os algoritmos de aprimoramento por IA a um pipeline do indexador. Como uma próxima etapa, prossiga para o tutorial a seguir.

> [!div class="nextstepaction"]
> [Indexação de documentos no Armazenamento de Blobs do Azure](search-howto-indexing-azure-blob-storage.md)