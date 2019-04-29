---
title: Conectar um aplicativo do MongoDB ao Azure Cosmos DB
description: Saiba como conectar seu aplicativo MongoDB ao Azure Cosmos DB.
author: rimman
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 12/26/2018
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: 737e179c2c16937d00bc9b6601f12ebe392c1906
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60892499"
---
# <a name="connect-a-mongodb-application-to-azure-cosmos-db"></a>Conectar um aplicativo do MongoDB ao Azure Cosmos DB
Saiba como conectar seu aplicativo do MongoDB a um Azure Cosmos DB usando uma cadeia de conexão do MongoDB. Você pode usar um banco de dados do Azure Cosmos DB como o armazenamento de dados para seu aplicativo MongoDB. 

Este tutorial fornece duas maneiras de recuperar informações da cadeia de conexão:

- [O método de início rápido](#QuickstartConnection), para uso com drivers do .NET, Node.js, Shell do MongoDB, Java e Python
- [O método de cadeia de conexão personalizada](#GetCustomConnection), para uso com outros drivers

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure. Se você ainda não tiver uma, crie agora mesmo uma [conta gratuita do Azure](https://azure.microsoft.com/free/). 
- Uma conta do Cosmos. Para obter instruções, veja [Compilar um aplicativo Web usando a API do Azure Cosmos DB para MongoDB e o SDK do .NET](create-mongodb-dotnet.md).

## <a id="QuickstartConnection"></a>Obter a cadeia de conexão do MongoDB usando o início rápido
1. Em um navegador da Internet, entre no [Portal do Azure](https://portal.azure.com).
2. Na folha **Azure Cosmos DB**, selecione a API. 
3. No painel esquerdo da folha da conta, clique em **Início rápido**. 
4. Escolha sua plataforma (**.NET**, **Node.js**, **MongoDB Shell**, **Java**, **Python**). Caso não veja seu driver ou ferramenta na lista, não se preocupe, pois documentamos continuamente mais snippets de código de conexão. Comente abaixo sobre o que você gostaria de ver. Para saber como gostaria de ver sua conexão e leia [Obter informações da cadeia de conexão da conta](#GetCustomConnection).
5. Copie e cole o snippet de código no seu aplicativo MongoDB.

    ![Folha início rápido](./media/connect-mongodb-account/QuickStartBlade.png)

## <a id="GetCustomConnection"></a> Obter a cadeia de conexão do MongoDB para personalização
1. Em um navegador da Internet, entre no [Portal do Azure](https://portal.azure.com).
2. Na folha **Azure Cosmos DB**, selecione a API. 
3. No painel esquerdo do folha de conta, clique em **Cadeia de Conexão**. 
4. A folha de **Cadeia de Conexão** é aberta. Ela tem todas as informações necessárias para se conectar à conta usando um driver para MongoDB, incluindo uma cadeia de conexão pré-construída.

    ![Folha Cadeia de conexão](./media/connect-mongodb-account/ConnectionStringBlade.png)

## <a name="connection-string-requirements"></a>Requisitos da cadeia de conexão
> [!Important]
> O Azure Cosmos DB tem padrões e requisitos de segurança rígidos. As contas do Azure Cosmos DB exigem autenticação e comunicação segura por *SSL*. 
>
>

O Azure Cosmos DB oferece suporte ao formato de URI de cadeia de conexão padrão do MongoDB, com alguns requisitos específicos: As contas do Azure Cosmos DB exigem autenticação e comunicação segura por SSL. Sendo assim, o formato da cadeia de conexão é:

    mongodb://username:password@host:port/[database]?ssl=true

Os valores dessa cadeia de caracteres estão disponíveis na folha **Cadeia de conexão** mostrada acima:

* Nome de usuário (obrigatório): Nome da conta do Cosmos.
* Senha (obrigatória): Senha da conta do Cosmos.
* Host (obrigatório): FQDN da conta do Cosmos.
* Porta (obrigatória): 10255.
* Banco de dados (opcional): o banco de dados que a conexão usa. Se nenhum banco de dados for fornecido, o banco de dados padrão é "teste".
* ssl=true (obrigatório)

Por exemplo, considere a conta mostrada na folha **Cadeia de conexão**. Uma cadeia de conexão válida é:

    mongodb://contoso123:0Fc3IolnL12312asdfawejunASDF@asdfYXX2t8a97kghVcUzcDv98hawelufhawefafnoQRGwNj2nMPL1Y9qsIr9Srdw==@contoso123.documents.azure.com:10255/mydatabase?ssl=true

## <a name="next-steps"></a>Próximas etapas

- Saiba como [usar o Studio 3T](mongodb-mongochef.md) com a API do Azure Cosmos DB para MongoDB.
- Saiba como [usar o Robo 3T](mongodb-robomongo.md) com a API do Azure Cosmos DB para MongoDB.
- Explore [exemplos](mongodb-samples.md) do MongoDB com a API do Azure Cosmos DB para MongoDB.
