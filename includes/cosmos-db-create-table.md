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
ms.openlocfilehash: 5743d785afb87aef6b3a89af6dc8eb18f66b164d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "68854635"
---
Agora, você pode usar a ferramenta Data Explorer no Portal do Azure para criar um banco de dados e uma tabela. 

1. Selecione **Data Explorer** > **Nova Tabela**. 
    
    A área **Adicionar Tabela** é exibida à direita, talvez seja necessário rolar para a direita para vê-la.

    ![Data Explorer no Portal do Azure](./media/cosmos-db-create-table/azure-cosmosdb-data-explorer.png)

2. Na página **Adicionar Tabela**, insira as configurações da nova tabela.

    Configuração|Valor sugerido|Descrição
    ---|---|---
    Id da Tabela|tabela de exemplo|A ID da nova tabela. Os nomes de tabela possuem os mesmos requisitos de caractere que os ids de banco de dados. Os nomes de banco de dados devem ter entre um e 255 caracteres e não podem conter `/ \ # ?` nem espaços à direita.
    Produtividade|400 RUs|Altere a taxa de transferência para 400 unidades de solicitação por segundo (RU/s). Se quiser reduzir a latência, você poderá escalar verticalmente a taxa de transferência mais tarde.

3. Selecione **OK**.

4. O Data Explorer exibe o novo banco de dados e a tabela.

   ![O Data Explorer do Portal do Azure, mostrando o novo banco de dados e a coleção](./media/cosmos-db-create-table/azure-cosmos-db-new-table.png)