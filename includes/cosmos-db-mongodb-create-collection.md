---
title: incluir arquivo
description: incluir arquivo
services: cosmos-db
author: LuisBosquez
ms.service: cosmos-db
ms.topic: include
ms.date: 04/15/2020
ms.author: lbosq
ms.custom: include file
ms.openlocfilehash: 37cdb6b466417add8dae69464304ce2f32247c8d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95557918"
---
Agora, você pode usar a ferramenta Data Explorer no portal do Azure para criar um banco de dados e um contêiner da API do Azure Cosmos DB para o MongoDB. 

1. Selecione **Data Explorer** > **Novo Contêiner**. 
    
    A área **Adicionar Contêiner** é exibida à extrema direita; talvez seja necessário rolar a página para a direita para vê-la.

    :::image type="content" source="./media/cosmos-db-create-collection/azure-cosmosdb-mongodb-data-explorer.png" alt-text="O Data Explorer do portal do Azure, painel Adicionar Contêiner":::

2. Na página **Adicionar contêiner**, insira as configurações do novo contêiner.

    |Configuração|Valor sugerido|Descrição
    |---|---|---|
    |**ID do banco de dados**|db|Digite *db* como o nome do novo banco de dados. Os nomes dos banco de dados devem conter de 1 a 255 caracteres e não podem conter `/, \\, #, ?` nem um espaço à direita. Marque a opção **Provisionar a produtividade do banco de dados**; ela permite que você compartilhe a produtividade provisionada para o banco de dados em todos os contêineres no banco de dados. Essa opção também ajuda na economia de custo. |
    |**Taxa de transferência**|400|Deixe a taxa de transferência em 400 unidades de solicitação por segundo (RU/s). Se quiser reduzir a latência, você poderá escalar verticalmente a taxa de transferência mais tarde. Você também pode escolher o [Modo de dimensionamento automático](../articles/cosmos-db/provision-throughput-autoscale.md), que fornecerá uma gama de RU/s que aumentarão e diminuirão dinamicamente conforme necessário.| 
    |**ID da coleção**|coll|Insira `coll` como o nome do novo contêiner. As IDs do contêiner têm os mesmos requisitos de caractere dos nomes de bancos de dados.|
    |**Capacidade de armazenamento**|Fixo (10 GB)|Insira *Fixo (10 GB)* para este aplicativo. Se selecionar *Ilimitado*, você precisará criar uma `Shard Key`, que todos os itens inseridos exigirão.|
    |**Chave de fragmentação**| /_id| O exemplo descrito neste artigo não usa uma Chave de fragmentação, portanto, defini-la como */_id* usará o campo de ID gerado automaticamente como chave de fragmentação. Saiba mais sobre a fragmentação, também chamada de particionamento, em [Particionamento no Azure Cosmos DB](../articles/cosmos-db/partitioning-overview.md)|
        
    Selecione **OK**. O Data Explorer exibe o novo banco de dados e o contêiner.