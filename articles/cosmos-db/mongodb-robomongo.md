---
title: Usar o Robo 3T para se conectar ao Azure Cosmos DB
description: Saiba como se conectar ao Azure Cosmos DB usando o Robo 3T e a API do Azure Cosmos DB para MongoDB
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 03/23/2020
author: timsander1
ms.author: tisande
ms.openlocfilehash: e096de5eea9bf5bbd47b976976192e62eb6aa33e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93096403"
---
# <a name="use-robo-3t-with-azure-cosmos-dbs-api-for-mongodb"></a>Usar o Robo 3T com a API do Azure Cosmos DB para MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Para se conectar à conta do Cosmos usando o Robo 3T, faça o seguinte:

* Baixe e instale o [Robo 3T](https://robomongo.org/)
* Obtenha informações da [cadeia de conexão](connect-mongodb-account.md) do Cosmos DB

> [!NOTE]
> Atualmente, o Robo 3T v 1.2 e versões inferiores têm suporte com a API do Cosmos DB para MongoDB.

## <a name="connect-using-robo-3t"></a>Conecte usando o Robo 3T

Para adicionar sua conta do Cosmos ao gerenciador de conexões do Robo 3T, execute as seguintes etapas:

1. Recupere as informações de conexão da sua conta do Cosmos configurada com a API do Azure Cosmos DB para MongoDB usando as instruções que estão [aqui](connect-mongodb-account.md).

    :::image type="content" source="./media/mongodb-robomongo/connectionstringblade.png" alt-text="Captura de tela da folha de cadeia de conexão":::
2. Execute o aplicativo *Robomongo*.

3. Clique no botão de conexão em **Arquivo** para gerenciar suas conexões. Em seguida, clique em **Criar** na janela **Conexões do MongoDB**, o que abrirá a janela **Configurações de Conexão**.

4. No janela **Configurações de Conexão**, escolha um nome. Em seguida, localize o **Host** e **Porta** de suas informações de conexão na Etapa 1 e insira-os em **Endereço** e **Porta**, respectivamente.

    :::image type="content" source="./media/mongodb-robomongo/manageconnections.png" alt-text="Captura de tela do Robomongo Manage Connections":::
5. Na guia **Autenticação**, clique em **Executar autenticação**. Em seguida, insira seu Banco de dados (o padrão é *Admin*), **Nome de Usuário** e **Senha**.
Tanto o **Nome de Usuário** quanto a **Senha** podem ser encontrados em suas informações de conexão na Etapa 1.

    :::image type="content" source="./media/mongodb-robomongo/authentication.png" alt-text="Captura de tela da guia Autenticação do Robomongo":::
6. Na guia **SSL**, marque **Usar protocolo SSL** e altere o **Método de Autenticação** para **Certificado Autoassinado**.

    :::image type="content" source="./media/mongodb-robomongo/SSL.png" alt-text="Captura de tela da guia SSL do Robomongo":::
7. Por fim, clique em **Teste** para verificar se você é capaz de se conectar e, em seguida, **Salve**.

## <a name="next-steps"></a>Próximas etapas

- Saiba como [usar o Studio 3T](mongodb-mongochef.md) com a API do Azure Cosmos DB para MongoDB.
- Explore [exemplos](mongodb-samples.md) do MongoDB com a API do Azure Cosmos DB para MongoDB.
