---
title: Atualizar a versão Mongo da sua API do Azure Cosmos DB para a conta do MongoDB
description: Como atualizar a versão do protocolo de fio do MongoDB para as contas da API do Azure Cosmos DB para MongoDB existentes de forma direta
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 03/19/2021
ms.author: chrande
ms.openlocfilehash: 8865a16c2840b65f432de679c6dd63b285b1f760
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104771781"
---
# <a name="upgrade-the-api-version-of-your-azure-cosmos-db-api-for-mongodb-account"></a>Atualizar a versão de API da sua API do Azure Cosmos DB para a conta do MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Este artigo descreve como atualizar a versão de API da sua API do Azure Cosmos DB para a conta do MongoDB. Após a atualização, você pode usar a funcionalidade mais recente na API do Azure Cosmos DB para MongoDB. O processo de atualização não interrompe a disponibilidade da sua conta e não consome RU/s ou diminui a capacidade do banco de dados em qualquer ponto. Nenhum dado ou índice existente será afetado por esse processo. 

Ao atualizar para uma nova versão de API, comece com cargas de trabalho de desenvolvimento/teste antes de atualizar as cargas de trabalho de produção. É importante atualizar seus clientes para uma versão compatível com a versão de API para a qual você está atualizando antes de atualizar sua conta de API do Azure Cosmos DB para MongoDB.

>[!Note]
> Neste momento, somente contas qualificadas que usam a versão 3,2 do servidor podem ser atualizadas para a versão 3,6 ou 4,0. Se sua conta não mostrar a opção de atualização, registre [um tíquete de suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="upgrading-to-40-or-36"></a>Atualização para 4.0 ou 3.6

### <a name="benefits-of-upgrading-to-version-40"></a>Benefícios da atualização para a versão 4.0

Veja a seguir os novos recursos incluídos na versão 4,0:
- Suporte para transações de vários documentos em coleções não fragmentadas.
- Novos operadores de agregação
- Desempenho de verificação aprimorado
- Armazenamento mais rápido e eficiente

### <a name="benefits-of-upgrading-to-version-36"></a>Benefícios da atualização para a versão 3.6

Veja a seguir os novos recursos incluídos na versão 3,6:
- Desempenho e estabilidade aprimorados
- Suporte a novos comandos de banco de dados
- Suporte a um pipeline de agregação por padrão e novos estágios de agregação
- Suporte para alterar fluxos
- Suporte para índices compostos
- Suporte entre partições para as seguintes operações: atualizar, excluir, contar e classificar
- Desempenho aprimorado para as seguintes operações agregadas: $count, $skip, $limit e $group
- A indexação de caractere curinga agora tem suporte

### <a name="changes-from-version-32"></a>Alterações da versão 3.2

- Por padrão, o recurso [Repetição do lado do servidor (SSR)](prevent-rate-limiting-errors.md) está habilitado, de modo que as solicitações do aplicativo cliente não retornarão erros 16500. Em vez disso, as solicitações serão retomadas até serem concluídas ou atenderem ao tempo limite de 60 segundos.
- O tempo limite por solicitação é definido para 60 segundos.
- Novas coleções do MongoDB criadas por você após a migração só terão a propriedade `_id` indexada por padrão.

### <a name="action-required-when-upgrading-from-32"></a>Ação necessária ao atualizar do 3.2

Ao atualizar do 3.2, o sufixo do ponto de extremidade da conta do banco de dados será atualizado para o seguinte formato:

```
<your_database_account_name>.mongo.cosmos.azure.com
```

Se você estiver atualizando da versão 3,2, será necessário substituir o ponto de extremidade existente em seus aplicativos e drivers que se conectam a essa conta de banco de dados. **Somente as conexões que estão usando o novo ponto de extremidade terão acesso aos recursos na nova versão da API**. O ponto de extremidade 3.2 anterior deve ter o sufixo `.documents.azure.com`.

>[!Note]
> Esse ponto de extremidade pode ter pequenas diferenças se sua conta foi criada em uma nuvem do Azure soberanas, governamental ou restrita.

## <a name="how-to-upgrade"></a>Como atualizar

1. Entre no [portal do Azure.](https://portal.azure.com/)

1. Navegue até sua conta da API do Azure Cosmos DB para MongoDB. Abra o painel **visão geral** e verifique se a **versão** atual do servidor é 3,2 ou 3,6.

    :::image type="content" source="./media/mongodb-version-upgrade/check-current-version.png" alt-text="Verifique a versão atual da sua conta do MongoDB na portal do Azure." border="true":::

1. No menu à esquerda, abra o `Features` painel. Esse painel mostra os recursos de nível de conta que estão disponíveis para sua conta de banco de dados.

1. Selecione a `Upgrade MongoDB server version` linha. Se você não vir essa opção, sua conta poderá não estar qualificada para essa atualização. Registre [um tíquete de suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) se esse for o caso.

    :::image type="content" source="./media/mongodb-version-upgrade/upgrade-server-version.png" alt-text="Abra a folha recursos e atualize sua conta." border="true":::

1. Examine as informações exibidas sobre a atualização. Selecione `Set server version to 4.0` (ou 3,6, dependendo da versão atual).

    :::image type="content" source="./media/mongodb-version-upgrade/select-upgrade.png" alt-text="Examine as diretrizes de atualização e selecione Atualizar." border="true":::

1. Depois de iniciar a atualização, o menu do **recurso** ficará esmaecido e o status será definido como *pendente*. A atualização leva cerca de 15 minutos para ser concluída. Esse processo não afetará a funcionalidade ou as operações existentes da sua conta de banco de dados. Após a conclusão, o status da **versão do servidor do MongoDB de atualização** mostrará a versão atualizada. [Entre em contato com o suporte](https://azure.microsoft.com/en-us/support/create-ticket/) se houvesse um problema ao processar sua solicitação.

1. A seguir, algumas considerações depois de atualizar sua conta:

    1. Se você atualizou de 3,2, volte para o painel **visão geral** e copie a nova cadeia de conexão para usar em seu aplicativo. A cadeia de conexão antiga executando a versão 3.2 não será interrompida. Para garantir uma experiência consistente, todos os seus aplicativos devem usar o novo ponto de extremidade.

    1. Se você atualizou da versão 3.6, sua cadeia de conexão existente será atualizada para a versão especificada e deverá continuar a ser usada.

## <a name="how-to-downgrade"></a>Como fazer downgrade

Você também pode fazer downgrade de sua conta de 4,0 para 3,6 por meio das mesmas etapas na seção ' como atualizar '.

Se você atualizou de 3,2 para (4,0 ou 3,6) e deseja fazer o downgrade de volta para a 3,2, você pode simplesmente voltar a usar sua cadeia de conexão anterior (3,2) com o host `accountname.documents.azure.com` que permanece ativo após a atualização executando a versão 3,2.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre os recursos compatíveis e incompatíveis [do MongoDB versão 4.0](mongodb-feature-support-40.md).
- Saiba mais sobre os recursos compatíveis e incompatíveis [do MongoDB versão 3.6](mongodb-feature-support-36.md).
- Para obter mais informações, confira [Recursos de versão Mongo 3.6](https://devblogs.microsoft.com/cosmosdb/azure-cosmos-dbs-api-for-mongodb-now-supports-server-version-3-6/)
