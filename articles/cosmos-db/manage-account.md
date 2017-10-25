---
title: Gerenciar uma conta do Azure Cosmos DB pelo Portal do Azure | Microsoft Docs
description: Saiba como gerenciar sua conta do Azure Cosmos DB pelo Portal do Azure. Encontre um guia sobre como usar o Portal do Azure para exibir, copiar, excluir e acessar contas.
keywords: Portal do Azure, banco de dados de documentos, azure, Microsoft azure
services: cosmos-db
documentationcenter: 
author: kirillg
manager: jhubbard
editor: cgronlun
ms.assetid: 00fc172f-f86c-44ca-8336-11998dcab45c
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/23/2017
ms.author: kirillg
ms.openlocfilehash: a0c6ec8d490e1adacc96758971ab91d8eaeab45c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-manage-an-azure-cosmos-db-account"></a>Como gerenciar uma conta do Azure Cosmos DB
Saiba como definir a consistência global, trabalhar com chaves e excluir uma conta do Azure Cosmos DB no portal do Azure.

## <a id="consistency"></a>Gerenciar as configurações de consistência do Azure Cosmos DB
A seleção do nível certo de consistência depende da semântica do aplicativo. Familiarize-se com os níveis de consistência disponíveis no Azure Cosmos DB lendo [Usando níveis de consistência para maximizar a disponibilidade e o desempenho no Azure Cosmos DB][consistency]. O Azure Cosmos DB fornece garantias de consistência, disponibilidade e desempenho, em cada nível de consistência disponível para sua conta de banco de dados. A configuração da conta do banco de dados com um nível de consistência Strong exige que seus dados sejam confinados em uma única região do Azure, e não globalmente disponíveis. Por outro lado, os níveis de consistência flexíveis — bounded staleness, session ou eventual — permitem associar qualquer número de regiões do Azure à sua conta de banco de dados. As etapas simples a seguir mostram como selecionar o nível de consistência padrão para sua conta de banco de dados. 

### <a name="to-specify-the-default-consistency-for-an-azure-cosmos-db-account"></a>Para especificar a consistência padrão de uma conta do Azure Cosmos DB
1. No [portal do Azure](https://portal.azure.com/), acesse sua conta do Azure Cosmos DB.
2. Na folha da conta, clique em **Consistência padrão**.
3. Na folha **Consistência Padrão**, selecione o novo nível de consistência e clique em **Salvar**.
    ![Sessão de consistência padrão][5]

## <a id="keys"></a>Exibir, copiar e regenerar chaves de acesso
Quando você cria uma conta do Azure Cosmos DB, o serviço gera duas chaves de acesso mestras que podem ser usadas para autenticação quando a conta do Azure Cosmos DB é acessada. Ao fornecer duas chaves de acesso, o Azure Cosmos DB permite regenerar as chaves sem nenhuma interrupção na conta do Azure Cosmos DB. 

No [portal do Azure](https://portal.azure.com/), acesse a folha **Chaves** no menu de recursos da folha **Conta do Azure Cosmos DB** para exibir, copiar e regenerar as chaves de acesso que são usadas para acessar sua conta do Azure Cosmos DB.

![Captura de tela do Portal do Azure, folha Chaves](./media/manage-account/keys.png)

> [!NOTE]
> A folha **Chaves** também inclui cadeias de conexão primárias e secundárias que podem ser usadas para se conectar à sua conta na [Ferramenta de Migração de Dados](import-data.md).
> 
> 

Chaves somente leitura também estão disponíveis nessa folha. Leituras e consultas são operações somente leitura, ao contrário de criações, exclusões e substituições.

### <a name="copy-an-access-key-in-the-azure-portal"></a>Copiar uma chave de acesso no portal do Azure
Na folha **Chaves**, clique no botão **Copiar** à direita da chave que você quer copiar.

![Exibir e copiar uma chave de acesso no Portal do Azure, folha Chaves](./media/manage-account/copykeys.png)

### <a name="regenerate-access-keys"></a>Regenerar chaves de acesso
Você deve alterar as chaves de acesso de sua conta do Azure Cosmos DB periodicamente para ajudar a manter as conexões mais seguras. Duas chaves de acesso são atribuídas para permitir que você mantenha conexões com a conta do Azure Cosmos DB usando uma chave de acesso enquanto regenera a outra.

> [!WARNING]
> A regeneração de suas chaves de acesso afeta quaisquer aplicativos que são dependentes da chave atual. Todos os clientes que usam a chave de acesso para acessar a conta do Azure Cosmos DB devem ser informados para usarem a nova chave.
> 
> 

Se você tiver aplicativos ou serviços de nuvem que usam a conta do Azure Cosmos DB, perderá as conexões se regenerar as chaves, a menos que você as reverta. As etapas a seguir descrevem o processo envolvido ao reverter suas chaves.

1. Atualize a chave de acesso no código do aplicativo para referenciar a chave de acesso secundária da conta do Azure Cosmos DB.
2. Regenere a chave de acesso primária de sua conta do Azure Cosmos DB. No [Portal do Azure](https://portal.azure.com/), acesse sua conta do Azure Cosmos DB.
3. Na folha **Conta do Azure Cosmos DB**, clique em **Chaves**.
4. Na folha **Chaves**, clique no botão Regenerar e clique em **Ok** para confirmar que você quer gerar uma nova chave.
    ![Regenerar chaves de acesso](./media/manage-account/regenerate-keys.png)
5. Uma vez que você verificou que a nova chave está disponível para uso(aproximadamente 5 minutos após a regeneração), atualize a chave de acesso em seu código do aplicativo para fazer referência à nova chave de acesso principal.
6. Regenere a chave de acesso secundária.
   
    ![Regenerar chaves de acesso](./media/manage-account/regenerate-secondary-key.png)

> [!NOTE]
> Poderá levar alguns minutos para que a chave recém-gerada possa ser usada para acessar sua conta do Azure Cosmos DB.
> 
> 

## <a name="get-the--connection-string"></a>Obtenha a cadeia de conexão
Para recuperar sua cadeia de conexão, faça o seguinte: 

1. No [portal do Azure](https://portal.azure.com), acesse sua conta do Azure Cosmos DB.
2. No menu de recursos, clique em **Chaves**.
3. Clique no botão **Copiar** ao lado da caixa **Cadeia de conexão primária** ou **Cadeia de conexão secundária** caixa. 

Se você estiver usando a cadeia de conexão na [Ferramenta de Migração de Banco de Dados do Azure Cosmos DB](import-data.md), acrescente o nome do banco de dados ao final da cadeia de conexão. `AccountEndpoint=< >;AccountKey=< >;Database=< >`.

## <a id="delete"></a> Excluir uma conta do Azure Cosmos DB
Para remover uma conta do Azure Cosmos DB no Portal do Azure que não está mais sendo usada, clique com o botão direito do mouse no nome da conta e, depois, clique em **Excluir conta**.

![Como excluir uma conta do Azure Cosmos DB no Portal do Azure](./media/manage-account/deleteaccount.png)

1. No [portal do Azure](https://portal.azure.com/), acesse a conta do Azure Cosmos DB que você deseja excluir.
2. Na folha **Conta do Azure Cosmos DB**, clique com o botão direito do mouse na conta e, depois, clique em **Excluir Conta**. 
3. Na folha de confirmação resultante, digite o nome da conta do Azure Cosmos DB para confirmar que você deseja excluir a conta.
4. Clique no botão **Excluir** .

![Como excluir uma conta do Azure Cosmos DB no Portal do Azure](./media/manage-account/delete-account-confirm.png)

## <a id="next"></a>Próximas etapas
Saiba como [começar a usar sua conta do Azure Cosmos DB](http://go.microsoft.com/fwlink/p/?LinkId=402364).

<!--Image references-->
[5]: ./media/manage-account/documentdb_change_consistency-1.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[bcdr]: https://azure.microsoft.com/documentation/articles/best-practices-availability-paired-regions/
[consistency]: consistency-levels.md
[azureregions]: https://azure.microsoft.com/regions/#services
[offers]: https://azure.microsoft.com/pricing/details/cosmos-db/
