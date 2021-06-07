---
title: incluir arquivo
description: incluir arquivo
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 04/13/2018
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: 3693f46a602929f9d6e2f4e29083b071ae6a9e9e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95972399"
---
Agora, você pode usar a ferramenta Data Explorer no portal do Azure para criar um banco de dados de grafo. 

1. Selecione **Data Explorer** > **Novo Grafo**.

    A área **Adicionar Grafo** é exibida à direita, talvez seja necessário rolar para a direita para vê-la.

    ![O Data Explorer do Portal do Azure, página Adicionar Grafo](./media/cosmos-db-create-graph/azure-cosmosdb-data-explorer-graph.png)

2. Na página **Adicionar Grafo**, insira as configurações do novo grafo.

    Configuração|Valor sugerido|Descrição
    ---|---|---
    ID do banco de dados|banco de dados de exemplo|Digite *banco de dados de exemplo* como o nome do novo banco de dados. Os nomes de banco de dados devem ter entre um e 255 caracteres e não podem conter `/ \ # ?` nem espaços à direita.
    Produtividade|400 RUs|Altere a taxa de transferência para 400 unidades de solicitação por segundo (RU/s). Se quiser reduzir a latência, você poderá escalar verticalmente a taxa de transferência mais tarde.
    ID do Grafo|grafo de exemplo|Digite *grafo de exemplo* como o nome da nova coleção. Os nomes de grafo têm os mesmos requisitos de caractere do que as IDs de banco de dados.
    Chave de Partição| /pk |Todas as contas do Cosmos DB precisam de uma chave de partição para dimensionar horizontalmente. Saiba como selecionar uma chave de partição apropriada no [artigo de Particionamento de Dados de Grafo](../articles/cosmos-db/graph-partitioning.md).

3. Depois que o formulário for preenchido, selecione **OK**.