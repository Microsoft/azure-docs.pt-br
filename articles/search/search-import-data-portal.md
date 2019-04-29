---
title: Importar dados para o índice de pesquisa usando o portal do Azure – Azure Search
description: Saiba como usar o Assistente de Importação de Dados no portal do Azure para rastrear dados do Azure do Cosmos DB, do armazenamento de blobs, do armazenamento de tabela, do Banco de Dados SQL e do SQL Server em VMs do Azure.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 02/26/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: fcb1e4f32608a1c83b653984dfa066da38e7c451
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60871078"
---
# <a name="import-data-wizard-for-azure-search"></a>Assistente de Importação de Dados do Azure Search

O portal do Azure fornece um assistente de **Importação de dados** no painel do Azure Search para carregar os dados em um índice. Nos bastidores, o assistente configura e invoca uma *fonte de dados*, um *índice* e um *indexador*, automatizando várias etapas do processo de indexação: 

* Conecta-se a uma fonte de dados externa na mesma assinatura do Azure.
* De forma opcional, integra o processamento de linguagem natural ou reconhecimento óptico de caracteres para extrair o texto de dados não estruturados.
* Gera um índice com base na amostragem de dados e metadados da fonte de dados externa.
* Rastreia a fonte de dados pelo conteúdo pesquisável, serializando e carregando documentos JSON em um índice.

O assistente não pode se conectar a um índice predefinido ou executar um indexador existente, mas dentro do assistente, você pode configurar um novo índice ou o indexador para oferecer suporte à estrutura e aos comportamentos necessários.

Ainda não conhece o Azure Search? Explore o [Início Rápido: importe, indexe e consulte usando as ferramentas do portal](search-get-started-portal.md) para fazer um test drive importando e indexando com a **Importação de dados** e o conjunto de dados de exemplo realestate integrado.

## <a name="start-importing-data"></a>Iniciar a importação de dados

Esta seção explica como iniciar o assistente e fornece uma visão geral de cada etapa.

1. No [portal do Azure](https://portal.azure.com), abra a página de serviço de pesquisa no painel ou [localize seu serviço](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) na lista de serviços.

2. Na página de visão geral do serviço, na parte superior, clique em **Importação de dados**.

   ![Comando Importação de dados no portal](./media/search-import-data-portal/import-data-cmd2.png "Iniciar o Assistente de Importação de Dados")

   > [!NOTE]
   > Você pode iniciar a **Importação de dados** de outros serviços do Azure, incluindo o Azure Cosmos DB, o Banco de Dados SQL do Azure e o Armazenamento de Blobs do Azure. Procure por **Adicionar Azure Search** no painel de navegação à esquerda da página de visão geral do serviço.

3. O assistente abre em **Conectar a seus dados**, onde você pode escolher uma fonte de dados externa que será usada nessa importação. Há várias coisas para saber sobre esta etapa, portanto, certifique-se de ler a seção [Entradas da fonte de dados](#data-source-inputs) para obter mais detalhes.

   ![Assistente de Importação de Dados no portal](./media/search-import-data-portal/import-data-wizard-startup.png "Assistente de Importação de Dados para o Azure Search")

4. Em seguida, vem **Adicionar pesquisa cognitiva**, caso você queira incluir o OCR (reconhecimento óptico de caracteres) do texto em arquivos de imagem ou análise de texto em dados não estruturados. Algoritmos de inteligência artificial dos Serviços Cognitivos são obtidos para essa tarefa. Há duas partes nesta etapa:
  
   Primeiro, [anexe um recurso dos Serviços Cognitivos](cognitive-search-attach-cognitive-services.md) em um conjunto de habilidades do Azure Search.
  
   Segundo, escolha quais aprimoramentos de inteligência artificial serão incluídos no conjunto de habilidades. Para ver uma demonstração passo a passo, confira esse [Início Rápido](cognitive-search-quickstart-blob.md).

   Se você quiser importar dados, ignore esta etapa e siga diretamente para a definição de índice.

5. Em seguida, vem **Personalizar índice de destino**, onde você pode aceitar ou modificar o esquema de índice apresentado no assistente. O assistente infere os campos e os tipos de dados realizando a amostragem de dados e lendo os metadados da fonte de dados externa.

   Para cada campo, [verifique os atributos de índice](#index-definition) para habilitar comportamentos específicos. Se você não marcar todos os atributos, o índice não será utilizável. 

6. Em seguida, vem **Criar um indexador**, que é um produto desse assistente. Um indexador é um rastreador que extrai dados pesquisáveis e metadados de uma fonte de dados externa do Azure. Ao escolher a fonte de dados e anexar os conjuntos de habilidades (opcionais) e um índice, você terá configurado um indexador conforme percorre cada etapa do assistente.

   Nomeie o indexador e clique em **Enviar** para iniciar o processo de importação. 

Você pode monitorar a indexação no portal clicando no indexador na lista **Indexadores**. Como documentos são carregados, a contagem de documentos se expandirão para o índice que você definiu. Às vezes, demora alguns minutos para que a página do portal obtenham as atualizações mais recentes.

O índice estará pronto para consulta assim que o primeiro documento for carregado. Você pode usar o [Gerenciador de pesquisa](search-explorer.md) para essa tarefa.

<a name="data-source-inputs"></a>

## <a name="data-source-inputs"></a>Entradas de fonte de dados

O assistente de **Importação de Dados** cria um objeto de fonte de dados persistentes especificando as informações de conexão com uma fonte de dados externa. O objeto da fonte de dados é usado exclusivamente com [indexadores](search-indexer-overview.md) e pode ser criado para as seguintes fontes de dados: 

* [SQL Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Azure Cosmos DB](search-howto-index-cosmosdb.md)
* [Armazenamento de Blobs do Azure](search-howto-indexing-azure-blob-storage.md)
* [Armazenamento de Tabelas do Azure](search-howto-indexing-azure-tables.md) (não tem suporte para pipelines de [pesquisa cognitiva](cognitive-search-concept-intro.md))

Um conjunto de dados bidimensional é uma entrada exigida. Você só pode importar de uma única tabela, exibição do banco de dados ou estrutura de dados equivalente. 

Você deve criar essa estrutura de dados antes de executar o assistente, e ele deve conter o conteúdo. Não execute o **importar dados** assistente em uma fonte de dados vazio.

|  Seleção | DESCRIÇÃO |
| ---------- | ----------- |
| **Fonte de dados existente** |Se já houver indexadores definidos em seu serviço de pesquisa, selecione uma definição de fonte de dados existente para outra importação. No Azure Search, os objetos das fonte de dados são usados somente por indexadores. Você pode criar um objeto da fonte de dados programaticamente ou por meio do assistente de **Importação de Dados**.|
| **Exemplos**| O Azure Search hospeda um banco de dados SQL do Azure público e gratuito que você pode usar para aprender sobre importação e solicitações de consulta no Azure Search. Confira [Início Rápido: Importar, indexar e consultar usando as ferramentas do portal](search-get-started-portal.md) para obter uma demonstração passo a passo. |
| **Banco de Dados SQL do Azure** |É possível especificar o nome do serviço, credenciais para um usuário de banco de dados com permissão de leitura e um nome de banco de dados na página ou por meio de uma cadeia de conexão do ADO.NET. Escolha a opção de cadeia de conexão para exibir ou personalizar as propriedades. <br/><br/>A tabela ou exibição que fornece o conjunto de linhas deve ser especificada na página. Essa opção aparece após o êxito da conexão, fornecendo uma lista suspensa para que você possa fazer uma seleção. |
| **SQL Server em VM do Azure** |Especifique um nome de serviço totalmente qualificado, a ID e a senha de usuário e um banco de dados como uma cadeia de conexão. Para usar esta fonte de dados, você deve ter instalado um certificado no repositório local que criptografa a conexão. Para obter instruções, veja [Conexão de VM do SQL ao Azure Search](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md). <br/><br/>A tabela ou exibição que fornece o conjunto de linhas deve ser especificada na página. Essa opção aparece após o êxito da conexão, fornecendo uma lista suspensa para que você possa fazer uma seleção. |
| **Cosmos DB** |Os requisitos incluem a conta, o banco de dados e a coleção. Todos os documentos na coleção serão incluídos no índice. Você pode definir uma consulta para nivelar ou filtrar o conjunto de linhas, ou deixe a consulta em branco. Uma consulta não é necessário neste assistente.|
| **Armazenamento de Blobs do Azure** |Os requisitos incluem a conta de armazenamento e um contêiner. Como opção, se os nomes de blob seguirem uma convenção de nomenclatura virtual para fins de agrupamento, você poderá especificar a parte do diretório virtual do nome como uma pasta no contêiner. Confira [Indexação do Armazenamento de Blobs](search-howto-indexing-azure-blob-storage.md) para saber mais. |
| **Armazenamento de Tabelas do Azure** |Os requisitos incluem a conta de armazenamento e um nome de tabela. Como opção, você pode especificar uma consulta para recuperar um subconjunto das tabelas. Confira [Indexação do Armazenamento de Tabelas](search-howto-indexing-azure-tables.md) para saber mais. |


<a name="index-definition"></a>

## <a name="index-attributes"></a>Atributos de índice

O assistente de **Importação de Dados** gera um índice, que será preenchido com documentos obtidos da fonte de dados de entrada. 

Para um índice funcional, verifique se você tem os seguintes elementos definidos.

1. Um campo deve estar marcado como uma **Chave** e será usado para identificar exclusivamente cada documento. A **Chave** deve ser *Edm.string*. 

   Se os valores de campo incluírem espaços ou traços, defina a opção **Chave de Codificação de Base 64** na etapa **Criar um indexador**, em **Opções avançadas**, para suprimir a verificação de validação para esses caracteres.

1. Defina os atributos de índice para cada campo. Se você não escolher atributos, o índice estará basicamente vazio, exceto pelo campo de chave necessário. Escolha, no mínimo, um ou mais destes atributos para cada campo.
   
   + **Recuperável** retorna o campo nos resultados da pesquisa. Todos os campos que fornecem conteúdo para os resultados da pesquisa devem ter esse atributo. Definir este campo não afetará significativamente o tamanho do índice.
   + **Filtrável** permite que o campo seja referenciado em expressões de filtro. Todos os campos usados em uma expressão **$filter** devem ter esse atributo. As expressões de filtro servem para correspondências exatas. Como as cadeias de caracteres de texto permanecem intactas, é necessário armazenamento adicional para acomodar o conteúdo textual.
   + **Pesquisável** habilita a pesquisa de texto completo. Todos os campos usados em consultas de forma livre ou em expressões de consulta devem ter esse atributo. Índices invertidos são criados para cada campo que você marcar como **Pesquisável**.

1. Como opção, defina esses atributos conforme necessário:

   + **Classificável** permite que o campo seja usado em uma classificação. Todos os campos usados em uma expressão **$Orderby** devem ter esse atributo.
   + **Com faceta** habilita o campo para a navegação facetada. Somente os campos que também estão marcados como **Filtrável** podem ser marcados como **Com faceta**.

1. Defina um **Analisador** se você quiser uma indexação e consulta avançada de idioma. O padrão é *Lucene Standard*, mas você pode escolher *English Microsoft* se quiser usar o analisador da Microsoft para processamento léxico avançado, como resolver formas verbais e substantivos irregulares.

   + Marque **Pesquisável** para habilitar a lista do **Analisador**.
   + Escolha um analisador fornecido na lista. 
   
   Somente os analisadores de idioma podem ser especificados no momento. O uso de um analisador personalizado ou de um analisador que não é de linguagem, como o Keyword, o Pattern etc., exige código. Para obter mais informações sobre os analisadores, confira [Criar um índice para documentos em vários idiomas](search-language-support.md).

1. Marque a caixa de seleção do **Sugestor** para habilitar o preenchimento automático de sugestões de consulta nos campos escolhidos.


## <a name="next-steps"></a>Próximas etapas
Consulte estes links para saber mais sobre os indexadores:

* [Indexação do Banco de Dados SQL do Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Indexação do Azure Cosmos DB](search-howto-index-cosmosdb.md)
* [Indexação do Armazenamento de Blobs](search-howto-indexing-azure-blob-storage.md)
* [Indexação do Armazenamento de Tabelas](search-howto-indexing-azure-tables.md)