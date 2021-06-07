---
title: Conectar um aplicativo do MongoDB ao Azure Cosmos DB
description: Saiba como conectar um aplicativo do MongoDB a Azure Cosmos DB obtendo a cadeia de conexão do portal do Azure
author: gahl-levy
ms.author: gahllevy
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 02/08/2021
ms.reviewer: sngun
robots: noindex
ms.openlocfilehash: bd244b0bb0aa5c5b2377b5a5675466645da3256c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99982080"
---
# <a name="connect-a-mongodb-application-to-azure-cosmos-db"></a>Conectar um aplicativo do MongoDB ao Azure Cosmos DB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Saiba como conectar seu aplicativo do MongoDB a um Azure Cosmos DB usando uma cadeia de conexão do MongoDB. É possível usar um banco de dados do Azure Cosmos como o armazenamento de dados para seu aplicativo MongoDB. Além do tutorial a seguir, você pode explorar os [exemplos](mongodb-samples.md) do MongoDB com a API do Azure Cosmos DB para MongoDB.

Este tutorial fornece duas maneiras de recuperar informações da cadeia de conexão:

- [O método de início rápido](#get-the-mongodb-connection-string-by-using-the-quick-start), para uso com drivers do .NET, Node.js, Shell do MongoDB, Java e Python
- [O método de cadeia de conexão personalizada](#get-the-mongodb-connection-string-to-customize), para uso com outros drivers

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure. Se você ainda não tiver uma, crie agora mesmo uma [conta gratuita do Azure](https://azure.microsoft.com/free/).
- Uma conta do Cosmos. Para obter instruções, veja [Compilar um aplicativo Web usando a API do Azure Cosmos DB para MongoDB e o SDK do .NET](create-mongodb-dotnet.md).

## <a name="get-the-mongodb-connection-string-by-using-the-quick-start"></a>Obter a cadeia de conexão do MongoDB usando o início rápido

1. Em um navegador da Internet, entre no [portal do Azure](https://portal.azure.com).
2. Na folha **Azure Cosmos DB**, selecione a API.
3. No painel esquerdo da folha da conta, clique em **Início rápido**.
4. Escolha sua plataforma (**.NET**, **Node.js**, **MongoDB Shell**, **Java**, **Python**). Caso não veja seu driver ou ferramenta na lista, não se preocupe, pois documentamos continuamente mais snippets de código de conexão. Comente abaixo sobre o que você gostaria de ver. Para saber como gostaria de ver sua conexão e leia [Obter informações da cadeia de conexão da conta](#get-the-mongodb-connection-string-to-customize).
5. Copie e cole o snippet de código no seu aplicativo MongoDB.

    :::image type="content" source="./media/connect-mongodb-account/QuickStartBlade.png" alt-text="Folha início rápido":::

## <a name="get-the-mongodb-connection-string-to-customize"></a> Obter a cadeia de conexão do MongoDB para personalização

1. Em um navegador da Internet, entre no [portal do Azure](https://portal.azure.com).
2. Na folha **Azure Cosmos DB**, selecione a API.
3. No painel esquerdo do folha de conta, clique em **Cadeia de Conexão**.
4. A folha de **Cadeia de Conexão** é aberta. Ela tem todas as informações necessárias para se conectar à conta usando um driver para MongoDB, incluindo uma cadeia de conexão pré-construída.

   :::image type="content" source="./media/connect-mongodb-account/ConnectionStringBlade.png" alt-text="Folha Cadeia de conexão" lightbox= "./media/connect-mongodb-account/ConnectionStringBlade.png" :::

## <a name="connection-string-requirements"></a>Requisitos da cadeia de conexão

> [!Important]
> O Azure Cosmos DB tem padrões e requisitos de segurança rígidos. Azure Cosmos DB contas exigem autenticação e comunicação segura via *TLS*.

O Azure Cosmos DB dá suporte ao formato de URI de cadeia de conexão padrão do MongoDB, com alguns requisitos específicos: Azure Cosmos DB contas exigem autenticação e comunicação segura via TLS. Sendo assim, o formato da cadeia de conexão é:

`mongodb://username:password@host:port/[database]?ssl=true`

Os valores dessa cadeia de caracteres estão disponíveis na folha **Cadeia de conexão** mostrada acima:

* Nome de usuário (obrigatório): Cosmos nome da conta.
* Senha (obrigatório): senha da conta do cosmos.
* Host (obrigatório): FQDN da conta Cosmos.
* Porta (obrigatória): 10255.
* Banco de dados (opcional): o banco de dados que a conexão usa. Se nenhum banco de dados for fornecido, o banco de dados padrão é "teste".
* ssl=true (obrigatório)

Por exemplo, considere a conta mostrada na folha **Cadeia de conexão**. Uma cadeia de conexão válida é:

`mongodb://contoso123:0Fc3IolnL12312asdfawejunASDF@asdfYXX2t8a97kghVcUzcDv98hawelufhawefafnoQRGwNj2nMPL1Y9qsIr9Srdw==@contoso123.documents.azure.com:10255/mydatabase?ssl=true`

## <a name="next-steps"></a>Próximas etapas

- Saiba como [usar o Studio 3T](mongodb-mongochef.md) com a API do Azure Cosmos DB para MongoDB.
- Saiba como [usar o Robo 3T](mongodb-robomongo.md) com a API do Azure Cosmos DB para MongoDB.
- Explore [exemplos](mongodb-samples.md) do MongoDB com a API do Azure Cosmos DB para MongoDB.
