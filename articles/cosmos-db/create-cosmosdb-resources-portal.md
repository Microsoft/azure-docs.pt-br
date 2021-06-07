---
title: Início rápido – Criar recursos do Azure Cosmos DB usando o portal do Azure
description: Este início rápido mostra como criar um banco de dados, um contêiner e itens do Azure Cosmos usando o portal do Azure.
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 11/10/2020
ms.openlocfilehash: 041b930a0c2fa82727216b4ca0c8367387d0d801
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100367241"
---
# <a name="quickstart-create-an-azure-cosmos-account-database-container-and-items-from-the-azure-portal"></a>Início Rápido: Criar uma conta, um banco de dados, um contêiner e itens do Azure Cosmos usando o portal do Azure
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [Azure portal](create-cosmosdb-resources-portal.md)
> * [.NET](create-sql-api-dotnet.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)
>  

O Azure Cosmos DB é o serviço de banco de dados multimodelo distribuído globalmente da Microsoft. É possível usar o Azure Cosmos DB para criar e consultar rapidamente bancos de dados de chave/valor, bancos de dados de documentos e bancos de dados de grafo. Todos se beneficiam de recursos de escala horizontal e distribuição global no núcleo do Azure Cosmos DB. 

Este início rápido demonstra como usar o portal do Azure para criar uma conta de [API de SQL](./introduction.md) do Azure Cosmos DB, criar um banco de dados de documentos e um contêiner e adicionar dados ao contêiner. 

## <a name="prerequisites"></a>Pré-requisitos

Uma assinatura do Azure ou uma conta de avaliação gratuita do Azure Cosmos DB
- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 

- [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]  

<a id="create-account"></a>
## <a name="create-an-azure-cosmos-db-account"></a>Criar uma conta do Azure Cosmos DB

Vá para o [portal do Azure](https://portal.azure.com/) para criar uma conta do Azure Cosmos DB. Pesquise pelo **Azure Cosmos DB** e selecione-o.

   :::image type="content" source="./media/create-cosmosdb-resources-portal/find-nosql-cosmosdb-marketplace.png" alt-text="O painel Bancos de Dados do portal do Azure":::

1. Selecione **Adicionar**.
1. Na página **Criar Conta do Azure Cosmos DB**, insira as configurações básicas da nova conta do Azure Cosmos. 

    |Configuração|Valor|Descrição |
    |---|---|---|
    |Subscription|Nome da assinatura|Selecione a assinatura do Azure que você deseja usar para essa conta do Azure Cosmos. |
    |Grupo de recursos|Nome do grupo de recursos|Selecione um grupo de recursos ou selecione **Criar novo**, então insira um nome exclusivo para o novo grupo de recursos. |
    |Nome da Conta|Um nome exclusivo|Insira um nome para identificar a conta do Azure Cosmos. Já que *documents.Azure.com* é acrescentado ao nome que você fornece para criar o URI, use um nome exclusivo.<br><br>O nome pode conter apenas letras minúsculas, números e o caractere de hífen (-). Ela deve ter entre 3 e 31 caracteres.|
    |API|O tipo de conta a ser criada|Selecione **Núcleo (SQL)** para criar uma consulta e um banco de dados de documento usando a sintaxe SQL. <br><br>A API determina o tipo de conta a ser criada. O Azure Cosmos DB fornece cinco APIs: Núcleo (SQL) e MongoDB para dados de documento, Gremlin para dados de grafo, Tabela do Azure e Cassandra. No momento, você deve criar uma conta separada para cada API. <br><br>[Saiba mais sobre a API do SQL](introduction.md).|
    |Modo de capacidade|Taxa de transferência provisionada ou sem servidor|Selecione **Taxa de transferência provisionada** para criar uma conta no modo [taxa de transferência provisionada](set-throughput.md). Selecione **Sem servidor** para criar uma conta no modo [sem servidor](serverless.md).|
    |Aplicar o desconto por nível gratuito do Azure Cosmos DB|Aplicar ou não aplicar|Com o nível gratuito do Azure Cosmos DB, você receberá os primeiros 400 RU/s e 5 GB de armazenamento gratuitamente em uma conta. Saiba mais sobre o [nível gratuito](https://azure.microsoft.com/pricing/details/cosmos-db/).|
    |Location|A região mais próxima dos usuários|Selecione uma localização geográfica para hospedar a sua conta do Azure Cosmos DB. Use a localização mais próxima dos usuários para fornecer a eles acesso mais rápido aos dados.|
    |Tipo de Conta|Produção ou não produção|Selecione **Produção** se a conta for usada para uma carga de trabalho de produção. Selecione **Não produção** se a conta for usada para não produção, por exemplo, desenvolvimento, teste, garantia de qualidade ou de preparo. Essa é uma configuração de marca de recurso do Azure que ajusta a experiência do Portal, mas não afeta a conta do Azure Cosmos DB subjacente. Você pode alterar esse valor a qualquer momento.|
    |Redundância geográfica|Habilitar ou Desabilitar|Habilite ou desabilite a distribuição global em sua conta emparelhando sua região com uma região de par. Você poderá adicionar mais regiões à sua conta posteriormente.|
    |Gravações de várias regiões|Habilitar ou Desabilitar|A capacidade de gravação de várias regiões permite que você aproveite a taxa de transferência provisionada para seus bancos de dados e contêineres em todo o mundo.|
    |Zonas de Disponibilidades|Habilitar ou Desabilitar|As Zonas de Disponibilidade ajudam a aprimorar a disponibilidade e a resiliência do seu aplicativo.|

> [!NOTE]
> Você pode ter no máximo uma conta do nível gratuito do Azure Cosmos DB por assinatura do Azure e deve aceitar ao criar a conta. Se você não vir a opção de aplicar o desconto por nível gratuito, significa que outra conta da assinatura já foi habilitada com o nível gratuito.

> [!NOTE]
> As seguintes opções não estarão disponíveis se você selecionar **Sem servidor** como **Modo de capacidade**:
> - Aplicar desconto por nível gratuito
> - Redundância geográfica
> - Gravações de várias regiões
   
   :::image type="content" source="./media/create-cosmosdb-resources-portal/azure-cosmos-db-create-new-account-detail-2.png" alt-text="A página da nova conta do Azure Cosmos DB":::

1. Selecione **Examinar + criar**. Você pode ignorar as seções **Rede** e **Marcas**.

1. Examine as configurações da conta e selecione **Criar**. São necessários alguns minutos para criar a conta. Aguarde até que a página do portal exiba **Sua implantação está concluída**. 

    :::image type="content" source="./media/create-cosmosdb-resources-portal/azure-cosmos-db-account-deployment-successful.png" alt-text="O painel Notificações do portal do Azure":::

1. Selecione **Ir para recurso** para ir para a página da conta do Azure Cosmos DB. 

    :::image type="content" source="./media/create-cosmosdb-resources-portal/azure-cosmos-db-account-quickstart-pane.png" alt-text="A página da conta do Azure Cosmos DB":::

<a id="create-container-database"></a>
## <a name="add-a-database-and-a-container"></a>Adicionar um banco de dados e um contêiner 

Você pode usar o Data Explorer no portal do Azure para criar um banco de dados e um contêiner. 

1.  Selecione **Data Explorer** no painel de navegação à esquerda na página de sua conta do Azure Cosmos DB e, em seguida, selecione **Novo Contêiner**. 
    
    Talvez seja necessário rolar a página para a direita para ver a janela **Adicionar Contêiner**.
    
    :::image type="content" source="./media/create-sql-api-dotnet/azure-cosmosdb-data-explorer-dotnet.png" alt-text="O Data Explorer do portal do Azure, painel Adicionar Contêiner":::
    
1.  No painel **Adicionar contêiner**, insira as configurações do novo contêiner.
    
    |Configuração|Valor sugerido|Descrição
    |---|---|---|
    |**ID do banco de dados**|ToDoList|Insira *ToDoList* como o nome para o novo banco de dados. Os nomes dos banco de dados devem conter de 1 a 255 caracteres e não podem conter `/, \\, #, ?` nem um espaço à direita. Marque a opção **Provisionar a produtividade do banco de dados**; ela permite que você compartilhe a produtividade provisionada para o banco de dados em todos os contêineres no banco de dados. Essa opção também ajuda na economia de custo. |
    |**Taxa de transferência**|400|Deixe a taxa de transferência em 400 unidades de solicitação por segundo (RU/s). Se quiser reduzir a latência, você poderá escalar verticalmente a taxa de transferência mais tarde.<br><br>**Observação**: esta configuração não está disponível ao criar um contêiner em uma conta sem servidor.| 
    |**ID do contêiner**|Itens|Insira *Itens* como o nome do novo contêiner. As IDs do contêiner têm os mesmos requisitos de caractere dos nomes de bancos de dados.|
    |**Chave de partição**| /category| O exemplo descrito neste artigo usa */category* como a chave de partição.|

    
    Não adicione **Chaves exclusivas** para este exemplo. Chaves exclusivas permitem que você adicione uma camada de integridade de dados ao banco de dados garantindo a exclusividade de um ou mais valores por chave de partição. Para obter mais informações, veja [Chaves exclusivas no Azure Cosmos DB](unique-keys.md).
    
1.  Selecione **OK**. O Data Explorer exibe o novo banco de dados e o contêiner que você criou.

## <a name="add-data-to-your-database"></a>Adicionar dados a seu banco de dados

Adicione dados a seu novo banco de dados usando o Data Explorer.

1. No **Data Explorer**, expanda o banco de dados **ToDoList** e, em seguida, expanda o contêiner **Itens**. Em seguida, selecione **Itens** e, em seguida, selecione **Novo Item**. 
   
   :::image type="content" source="./media/create-sql-api-dotnet/azure-cosmosdb-new-document.png" alt-text="Criar documentos no Data Explorer no portal do Azure":::
   
1. Adicione a seguinte estrutura ao documento no lado direito do painel **Documentos**:

     ```json
     {
         "id": "1",
         "category": "personal",
         "name": "groceries",
         "description": "Pick up apples and strawberries.",
         "isComplete": false
     }
     ```

1. Clique em **Salvar**.
   
   :::image type="content" source="./media/create-sql-api-dotnet/azure-cosmosdb-save-document.png" alt-text="Copie nos dados json e selecione em Salvar no Data Explorer no portal do Azure":::
   
1. Selecione **Novo Documento** novamente e crie e salve um outro documento com uma única `id` e quaisquer outras propriedades e valores desejados. Os documentos podem ter qualquer estrutura, pois o Azure Cosmos DB não impõe nenhum esquema a seus dados.

## <a name="query-your-data"></a>Consultar seus dados

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)] 

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

Se você quiser excluir apenas o banco de dados e usar a conta do Azure Cosmos no futuro, poderá excluir o banco de dados com as seguintes etapas:

* Navegue até a conta do Azure Cosmos.
* Abra **Data Explorer**, clique com o botão direito do mouse no banco de dados que você deseja excluir e selecione **Excluir Banco de Dados**.
* Insira a ID/nome do banco de dados para confirmar a operação de exclusão. 

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você aprendeu a criar uma conta do Azure Cosmos DB, como criar um banco de dados e contêiner usando o Data Explorer. Agora, é possível importar outros dados para sua conta do Azure Cosmos DB. 

> [!div class="nextstepaction"]
> [Importar dados no Azure Cosmos DB](import-data.md)