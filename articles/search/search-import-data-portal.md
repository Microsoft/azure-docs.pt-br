---
title: Importar dados para o Azure Search no portal | Microsoft Docs
description: "Use o Assistente de Dados de Importação do Azure Search no Portal do Azure para rastrear dados do Azure desde o Azure Cosmos DB NoSQL, o armazenamento de Blobs, o armazenamento de tabelas, o Banco de Dados SQL e o SQL Server em VMs do Azure."
services: search
documentationcenter: 
author: HeidiSteen
manager: jhubbard
editor: 
tags: Azure Portal
ms.assetid: f40fe07a-0536-485d-8dfa-8226eb72e2cd
ms.service: search
ms.devlang: na
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 05/01/2017
ms.author: heidist
ms.openlocfilehash: a3e6dd66197a17bfdc80c04130e198b787692a58
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="import-data-to-azure-search-using-the-portal"></a>Importar dados para a Pesquisa do Azure usando o portal
O portal do Azure fornece um assistente de **Importação de dados** no painel do Azure Search para carregar os dados em um índice. 

  ![Importar Dados na barra de comandos][1]

Internamente, o assistente configura e invoca um *indexador*, automatizando várias etapas do processo de indexação: 

* Conexão a uma fonte de dados externa na mesma assinatura do Azure
* Gerar um esquema de índice modificável com base na estrutura de dados de origem
* Carregar documentos JSON em um índice usando um conjunto de registros recuperado da fonte de dados

Você pode experimentar este fluxo de trabalho usando dados de exemplo no Azure Cosmos DB. Visite [Introdução ao Azure Search no Portal do Azure](search-get-started-portal.md) para obter instruções.

> [!NOTE]
> Você pode iniciar o assistente **Importar dados** do painel do Azure Cosmos DB para simplificar a indexação dessa fonte de dados. Em navegação à esquerda, vá para **Coleções** > **Adicionar Azure Search** para começar.

## <a name="data-sources-supported-by-the-import-data-wizard"></a>Fontes de dados com suporte do Assistente para Importação de Dados
O Assistente de importação de dados oferece suporte às seguintes fontes de dados: 

* Banco de Dados SQL do Azure
* Dados relacionais do SQL Server em uma VM do Azure
* Azure Cosmos DB
* Armazenamento do Blob do Azure
* Armazenamento da tabela do Azure

Um conjunto de dados bidimensional é uma entrada exigida. Você só pode importar de uma única tabela, exibição do banco de dados ou estrutura de dados equivalente. Você deve criar essa estrutura de dados antes de executar o assistente.

## <a name="connect-to-your-data"></a>Conectar aos seus dados
1. Entre no [portal do Azure](https://portal.azure.com) e abra o painel de serviço. Você pode clicar em **Mais serviços** na barra de salto para procurar os “serviços de pesquisa” existentes na assinatura atual. 
2. Clique em **Importar Dados** na barra de comandos para deslizar e abrir a folha Importar Dados.  
3. Clique em **Conectar a seus dados** para especificar uma definição de fonte de dados usada por um indexador. Para fontes de dados dentro da assinatura, normalmente o assistente pode detectar e ler as informações de conexão, reduzindo os requisitos gerais de configuração.

|  |  |
| --- | --- |
| **Fonte de dados existente** |Se já houver indexadores definidos em seu serviço de pesquisa, selecione uma definição de fonte de dados existente para outra importação. |
| **Banco de Dados SQL do Azure** |É possível especificar o nome do serviço, credenciais para um usuário de banco de dados com permissão de leitura e um nome de banco de dados na página ou por meio de uma cadeia de conexão do ADO.NET. Escolha a opção de cadeia de conexão para exibir ou personalizar as propriedades. <br/><br/>A tabela ou exibição que fornece o conjunto de linhas deve ser especificada na página. Essa opção aparece após o êxito da conexão, fornecendo uma lista suspensa para que você possa fazer uma seleção. |
| **SQL Server em VM do Azure** |Especifique um nome de serviço totalmente qualificado, a ID e a senha de usuário e um banco de dados como uma cadeia de conexão. Para usar esta fonte de dados, você deve ter instalado um certificado no repositório local que criptografa a conexão. Para obter instruções, veja [Conexão de VM do SQL ao Azure Search](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md). <br/><br/>A tabela ou exibição que fornece o conjunto de linhas deve ser especificada na página. Essa opção aparece após o êxito da conexão, fornecendo uma lista suspensa para que você possa fazer uma seleção. |
| **Azure Cosmos DB** |Os requisitos incluem a conta, o banco de dados e a coleção. Todos os documentos na coleção serão incluídos no índice. Você pode definir uma consulta para nivelar ou filtrar o conjunto de linhas, ou para detectar documentos alterados para operações de atualização de dados subsequentes. |
| **Armazenamento de Blobs do Azure** |Os requisitos incluem a conta de armazenamento e um contêiner. Como opção, se os nomes de blob seguirem uma convenção de nomenclatura virtual para fins de agrupamento, você poderá especificar a parte do diretório virtual do nome como uma pasta no contêiner. Confira [Indexação do Armazenamento de Blobs](search-howto-indexing-azure-blob-storage.md) para saber mais. |
| **Armazenamento de Tabelas do Azure** |Os requisitos incluem a conta de armazenamento e um nome de tabela. Como opção, você pode especificar uma consulta para recuperar um subconjunto das tabelas. Confira [Indexação do Armazenamento de Tabelas](search-howto-indexing-azure-tables.md) para saber mais. |

## <a name="customize-target-index"></a>Personalizar o índice de destino
Normalmente, um índice preliminar é inferido do conjunto de dados. Adicionar, editar ou excluir campos para concluir o esquema. Além disso, defina os atributos no nível do campo para determinar seus comportamentos de pesquisa subsequentes.

1. Em **Personalizar índice de destino**, especifique o nome e uma **Chave** usados para identificar exclusivamente cada documento. A Chave deve ser uma cadeia de caracteres. Se os valores de campo incluem espaços ou traços, defina as opções avançadas em **Importar seus dados** para suprimir a verificação de validação para esses caracteres.
2. Analise e revise os campos restantes. O nome e o tipo do campo geralmente são preenchidos para você. Você pode alterar o tipo de dados até que o índice é criado. Alterá-la posteriormente exigirá uma recompilação.
3. Defina os atributos de índice de cada campo:
   
   * Recuperável retorna o campo nos resultados da pesquisa.
   * Filtrável permite que o campo seja referenciado em expressões de filtro.
   * Classificável permite que o campo seja usado em uma classificação.
   * Com faceta habilita o campo para a navegação com faceta.
   * Pesquisável habilita a pesquisa de texto completo.
4. Clique na guia **Analisador** se desejar especificar um analisador de idiomas no nível do campo. Somente os analisadores de idioma podem ser especificados no momento. O uso de um analisador personalizado ou de um analisador que não é de linguagem, como o Keyword, o Pattern etc., exige código.
   
   * Clique em **Pesquisável** para designar a pesquisa de texto completo no campo e habilitar a lista suspensa Analisador.
   * Escolha o analisador que quiser. Confira [Criar um índice para documentos em vários idiomas](search-language-support.md) para obter detalhes.
5. Clique em **Sugestor** para habilitar o preenchimento automático de sugestões de consulta nos campos selecionados.

## <a name="import-your-data"></a>Importar seus dados
1. Em **Importar seus dados**, forneça um nome para o indexador. Lembre-se de que o produto do assistente para Importação de Dados é um indexador. Posteriormente, se você quiser exibir ou editá-lo, selecione-o no portal em vez de executar novamente o assistente. 
2. Especifique o cronograma, que tem base no fuso horário regional no qual o serviço é provisionado.
3. Defina opções avançadas para especificar os limites de continuação da indexação no caso de um documento ser descartado. Além disso, você pode especificar se os campos **Chave** podem conter espaços e barras.  
4. Clique em **OK** para criar o índice e importar os dados.

Você pode monitorar a indexação no portal. Como documentos são carregados, a contagem de documentos se expandirão para o índice que você definiu. Às vezes, demora alguns minutos para que a página do portal obtenham as atualizações mais recentes.

O índice estará pronto para consulta assim que todos os documentos forem carregados.

## <a name="query-an-index-using-search-explorer"></a>Consultar um índice usando o Search Explorer

O portal inclui **Search Explorer** para que você possa consultar um índice sem precisar escrever nenhum código. Você pode usar o [Search Explorer](search-explorer.md) em qualquer índice.

A experiência de pesquisa se baseia nas configurações padrão, como a [sintaxe simples](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) e padrão[searchMode query parameter(https://docs.microsoft.com/rest/api/searchservice/search-documents). 

Os resultados são retornados em JSON, em um formato detalhado, para que você possa inspecionar o documento inteiro.

## <a name="edit-an-existing-indexer"></a>Editar um indexador existente
Conforme observado, o Assistente de importação de dados cria um **indexador**, que você pode modificar como uma construção autônomo no portal.

No painel do serviço, clique duas vezes no bloco Indexador para deslizar uma lista de todos os indexadores criados para sua assinatura. Clique duas vezes em um dos indexadores para executá-lo, editá-lo ou excluí-lo. Você pode substituir o índice por outro existente, alterar a fonte de dados e definir opções para os limites de erro durante a indexação.

## <a name="edit-an-existing-index"></a>Editar um indexador existente
O assistente também criado um **índice**. No Azure Search, estruturais atualizações em um índice exigirá uma recriação de índice. Uma recriação exige a exclusão do índice, recriando o índice usando um esquema revisado que tem as alterações desejadas e recarregar os dados. As atualizações estruturais incluem alterar um tipo de dados e renomear ou excluir um campo.

As edições que não exigem a recriação incluem adicionar um novo campo, alterar os perfis de pontuação, mudar as sugestões ou alterar os analistas de linguagem. Consulte [Atualizar Índice](https://msdn.microsoft.com/library/azure/dn800964.aspx) para obter mais informações.


## <a name="next-steps"></a>Próximas etapas
Consulte estes links para saber mais sobre os indexadores:

* [Indexação do Banco de Dados SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Indexação do Azure Cosmos DB](search-howto-index-documentdb.md)
* [Indexação do Armazenamento de Blobs](search-howto-indexing-azure-blob-storage.md)
* [Indexação do Armazenamento de Tabelas](search-howto-indexing-azure-tables.md)

<!--Image references-->
[1]: ./media/search-import-data-portal/search-import-data-command.png

