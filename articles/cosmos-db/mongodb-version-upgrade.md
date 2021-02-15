---
title: Atualizar a versão Mongo da sua API do Azure Cosmos DB para a conta do MongoDB
description: Como atualizar a versão do protocolo de fio do MongoDB para as contas da API do Azure Cosmos DB para MongoDB existentes de forma direta
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: guide
ms.date: 09/22/2020
ms.author: chrande
ms.openlocfilehash: 9ce444e41d19ece984071d0f62e705a09d5f23c9
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93356443"
---
# <a name="upgrade-the-mongodb-wire-protocol-version-of-your-azure-cosmos-dbs-api-for-mongodb-account"></a>Atualizar a versão do protocolo de transmissão do MongoDB da conta da API para MongoDB do Azure Cosmos DB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Este artigo descreve como atualizar a versão do protocolo de conexão de sua conta da API para MongoDB do Azure Cosmos DB. Depois de atualizar a versão do protocolo de conexão, você pode usar a funcionalidade mais recente na API do Azure Cosmos DB para MongoDB. O processo de atualização não interrompe a disponibilidade da sua conta e não consome RU/s ou diminui a capacidade do banco de dados em qualquer ponto. Nenhum dado ou índice existente será afetado por esse processo.

>[!Note]
> Neste momento, somente contas qualificadas usando a versão 3,2 do servidor podem ser atualizadas para a versão 3,6. Se sua conta não mostrar a opção de atualização, registre [um tíquete de suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="upgrading-from-version-32-to-36"></a>Atualizando da versão 3,2 para 3,6

### <a name="benefits-of-upgrading-to-version-36"></a>Benefícios da atualização para a versão 3.6

Veja a seguir os novos recursos incluídos na versão 3,6:
- Desempenho e estabilidade aprimorados
- Suporte a novos comandos de banco de dados
- Suporte a um pipeline de agregação por padrão e novos estágios de agregação
- Suporte para fluxos de alteração
- Suporte para índices compostos
- Suporte entre partições para as seguintes operações: atualizar, excluir, contar e classificar
- Desempenho aprimorado para as seguintes operações de agregação: $count, $skip, $limit e $group
- A indexação de curinga agora tem suporte

### <a name="changes-from-version-32"></a>Alterações da versão 3,2

- **Erros de RequestRateIsLarge foram removidos**. As solicitações do aplicativo cliente não retornarão mais 16500 erros. Em vez disso, solicitações serão retomadas até que concluam ou atendam ao tempo limite.
- Por tempo limite de solicitação é definido como 60 segundos.
- As coleções do MongoDB criadas na nova versão do protocolo de conexão só terão a `_id` propriedade indexada por padrão.

### <a name="action-required"></a>Ação requerida

Para a atualização para a versão 3,6, o sufixo do ponto de extremidade da conta do banco de dados será atualizado para o seguinte formato:

```
<your_database_account_name>.mongo.cosmos.azure.com
```

Você precisa substituir o ponto de extremidade existente em seus aplicativos e drivers que se conectam a essa conta de banco de dados. **Somente as conexões que estão usando o novo ponto de extremidade terão acesso aos recursos do MongoDB versão 3,6**. O ponto de extremidade anterior deve ter o sufixo `.documents.azure.com` .

>[!Note]
> Esse ponto de extremidade pode ter pequenas diferenças se sua conta foi criada em uma nuvem do Azure soberanas, governamental ou restrita.

### <a name="how-to-upgrade"></a>Como atualizar

1. Primeiro, vá para a portal do Azure e navegue até a folha de visão geral da sua API do Azure Cosmos DB para MongoDB. Verifique se a versão do servidor é `3.2` . 

    :::image type="content" source="./media/mongodb-version-upgrade/1.png" alt-text="Visão geral da conta do portal do Azure com MongoDB" border="false":::

2. Nas opções à esquerda, selecione a `Features` folha. Isso revelará os recursos de nível de conta que estão disponíveis para sua conta de banco de dados.

    :::image type="content" source="./media/mongodb-version-upgrade/2.png" alt-text="portal do Azure visão geral da conta do MongoDB com a folha recursos realçada" border="false":::

3. Clique na `Upgrade to Mongo server version 3.6` linha. Se você não vir essa opção, sua conta poderá não estar qualificada para esta atualização. Registre [um tíquete de suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) se esse for o caso.

    :::image type="content" source="./media/mongodb-version-upgrade/3.png" alt-text="Recursos folha com opções." border="false":::

4. Examine as informações exibidas sobre essa atualização específica. Observe que a atualização só será concluída até que seus aplicativos usem o ponto de extremidade atualizado, conforme destacado nesta seção. Clique em assim que `Enable` estiver pronto para iniciar o processo.

    :::image type="content" source="./media/mongodb-version-upgrade/4.png" alt-text="Diretrizes de atualização expandidas." border="false":::

5. Depois de iniciar o processo, o `Features` menu mostrará o status da atualização. O status vai de `Pending` , para `In Progress` , para `Upgraded` . Esse processo não afetará a funcionalidade ou as operações existentes da conta do banco de dados.

    :::image type="content" source="./media/mongodb-version-upgrade/5.png" alt-text="Status da atualização após o início." border="false":::

6. Quando a atualização for concluída, o status será exibido como `Upgraded` . Clique nele para saber mais sobre as próximas etapas e ações que você precisa executar para finalizar o processo. [Entre em contato com o suporte](https://azure.microsoft.com/en-us/support/create-ticket/) se houvesse um problema ao processar sua solicitação.

    :::image type="content" source="./media/mongodb-version-upgrade/6.png" alt-text="Status da conta atualizado." border="false":::

7. **Para começar a usar a versão atualizada da sua conta de banco de dados** , volte para a `Overview` folha e copie a nova cadeia de conexão a ser usada em seu aplicativo. Os aplicativos começarão a usar a versão atualizada assim que se conectarem ao novo ponto de extremidade. As conexões existentes não serão interrompidas e poderão ser atualizadas à sua conveniência. Para garantir uma experiência consistente, todos os seus aplicativos devem usar o novo ponto de extremidade.

    :::image type="content" source="./media/mongodb-version-upgrade/7.png" alt-text="Nova folha de visão geral." border="false":::

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre os recursos com e sem suporte [do MongoDB versão 3,6](mongodb-feature-support-36.md).
- Para obter mais informações, confira [Recursos de versão Mongo 3.6](https://devblogs.microsoft.com/cosmosdb/azure-cosmos-dbs-api-for-mongodb-now-supports-server-version-3-6/)
