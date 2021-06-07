---
title: Controle de acesso baseado em função do Azure no Azure Cosmos DB
description: Saiba como Azure Cosmos DB fornece proteção de banco de dados com a integração do Active Directory (RBAC do Azure).
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/23/2020
ms.author: mjbrown
ms.openlocfilehash: 3a0f0f617e45834f1f205bcb4c8bcbc884b20d22
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102048090"
---
# <a name="azure-role-based-access-control-in-azure-cosmos-db"></a>Controle de acesso baseado em função do Azure no Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

> [!NOTE]
> Este artigo é sobre o controle de acesso baseado em função para operações do plano de gerenciamento no Azure Cosmos DB. Se você estiver usando operações de plano de dados, consulte [Azure Cosmos DB RBAC](how-to-setup-rbac.md) para controle de acesso baseado em função aplicado às operações do plano de dados.

O Azure Cosmos DB fornece o Azure RBAC (controle de acesso baseado em função) interno do Azure para cenários comuns de gerenciamento no Azure Cosmos DB. Um indivíduo que tem um perfil no Azure Active Directory pode atribuir essas funções do Azure a usuários, grupos, entidades de serviço ou identidades gerenciadas para conceder ou negar acesso a recursos e operações em Azure Cosmos DB recursos. As atribuições de função têm como escopo o acesso ao plano de controle, que inclui acesso a contas, bancos de dados, contêineres e ofertas (taxa de transferência) do Azure Cosmos.

## <a name="built-in-roles"></a>Funções internas

A seguir estão as funções internas com suporte pelo Azure Cosmos DB:

|**Função interna**  |**Descrição**  |
|---------|---------|
|[Colaborador de Conta do DocumentDB](../role-based-access-control/built-in-roles.md#documentdb-account-contributor)|Pode gerenciar contas do Azure Cosmos DB.|
|[Cosmos DB leitor de conta](../role-based-access-control/built-in-roles.md#cosmos-db-account-reader-role)|Pode ler dados de contas do Azure Cosmos DB.|
|[Operador de backup Cosmos](../role-based-access-control/built-in-roles.md#cosmosbackupoperator)| Pode enviar uma solicitação de restauração para portal do Azure para um banco de dados ou contêiner habilitado para backup periódico. Pode modificar o intervalo de backup e a retenção no portal do Azure. Não é possível acessar nenhum dado ou usar Data Explorer.  |
| [CosmosRestoreOperator](../role-based-access-control/built-in-roles.md) | Pode executar a ação de restauração para Azure Cosmos DB conta com o modo de backup contínuo.|
|[Operador de Cosmos DB](../role-based-access-control/built-in-roles.md#cosmos-db-operator)|Pode provisionar contas, bancos de dados e contêineres do Azure Cosmos. Não é possível acessar nenhum dado ou usar Data Explorer.|

> [!IMPORTANT]
> O suporte do RBAC do Azure no Azure Cosmos DB se aplica somente às operações do plano de controle. As operações do plano de dados são protegidas usando chaves primárias ou tokens de recurso. Para saber mais, confira [proteger o acesso aos dados no Azure Cosmos DB](secure-access-to-data.md)

## <a name="identity-and-access-management-iam"></a>IAM (gerenciamento de identidade e acesso)

O painel de **controle de acesso (iam)** no portal do Azure é usado para configurar o controle de acesso baseado em função do Azure nos recursos de Cosmos do Azure. As funções são aplicadas a usuários, grupos, entidades de serviço e identidades gerenciadas no Active Directory. Você pode usar funções internas ou funções personalizadas para indivíduos e grupos. A captura de tela a seguir mostra a integração de Active Directory (RBAC do Azure) usando o controle de acesso (IAM) no portal do Azure:

:::image type="content" source="./media/role-based-access-control/database-security-identity-access-management-rbac.png" alt-text="IAM (controle de acesso) no portal do Azure-demonstrando a segurança do banco de dados.":::

## <a name="custom-roles"></a>Funções personalizadas

Além das funções internas, os usuários também podem criar [funções personalizadas](../role-based-access-control/custom-roles.md) no Azure e aplicar essas funções a entidades de serviço em todas as assinaturas em seu locatário Active Directory. As funções personalizadas fornecem aos usuários uma maneira de criar definições de função do Azure com um conjunto personalizado de operações de provedor de recursos. Para saber quais operações estão disponíveis para a criação de funções personalizadas para Azure Cosmos DB consulte, [Azure Cosmos DB operações do provedor de recursos](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)

> [!TIP]
> As funções personalizadas que precisam acessar dados armazenados no Cosmos DB ou usar Data Explorer no portal do Azure devem ter uma `Microsoft.DocumentDB/databaseAccounts/listKeys/*` ação.

## <a name="preventing-changes-from-the-azure-cosmos-db-sdks"></a><a id="prevent-sdk-changes"></a>Impedindo alterações dos SDKs de Azure Cosmos DB

O provedor de recursos de Azure Cosmos DB pode ser bloqueado para evitar qualquer alteração nos recursos de um cliente que se conecta usando as chaves de conta (ou seja, aplicativos que se conectam por meio do SDK do cosmos do Azure). Isso também inclui as alterações feitas no portal do Azure. Esse recurso pode ser desejável para usuários que desejam maiores graus de controle e governança para ambientes de produção. Impedir alterações do SDK também habilita recursos como bloqueios de recursos e logs de diagnóstico para operações de plano de controle. Os clientes que se conectam do SDK do Azure Cosmos DB serão impedidos de alterar qualquer propriedade para as contas, os bancos de dados, os contêineres e a taxa de transferência do Azure Cosmos. As operações que envolvem a leitura e gravação de dados em contêineres Cosmos em si não são afetadas.

Quando esse recurso é habilitado, as alterações em qualquer recurso só podem ser feitas de um usuário com a função correta do Azure e credenciais de Azure Active Directory, incluindo identidades de serviço gerenciadas.

> [!WARNING]
> Habilitar esse recurso pode ter impacto em seu aplicativo. Certifique-se de entender o impacto antes de habilitá-lo.

### <a name="check-list-before-enabling"></a>Lista de verificação antes de habilitar

Essa configuração impedirá qualquer alteração em qualquer recurso Cosmos de qualquer cliente que se conecte usando chaves de conta, incluindo qualquer Cosmos DB SDK, quaisquer ferramentas que se conectam por meio de chaves de conta ou da portal do Azure. Para evitar problemas ou erros de aplicativos depois de habilitar esse recurso, verifique se os aplicativos ou portal do Azure usuários executam qualquer uma das seguintes ações antes de habilitar esse recurso, incluindo:

- Qualquer alteração na conta Cosmos, incluindo qualquer propriedade ou adição ou remoção de regiões.

- Criação, exclusão de recursos filho, como bancos de dados e contêineres. Isso inclui recursos para outras APIs, como Cassandra, MongoDB, Gremlin e recursos de tabela.

- Atualizando a produtividade em recursos de nível de contêiner ou banco de dados.

- Modificando Propriedades de contêiner, incluindo política de índice, TTL e chaves exclusivas.

- Modificando procedimentos armazenados, gatilhos ou funções definidas pelo usuário.

Se seus aplicativos (ou usuários via portal do Azure) executarem qualquer uma dessas ações, eles precisarão ser migrados para execução por meio de [modelos ARM](./manage-with-templates.md), [PowerShell](manage-with-powershell.md), [CLI do Azure](manage-with-cli.md), REST ou [biblioteca de gerenciamento do Azure](https://github.com/Azure-Samples/cosmos-management-net). Observe que o gerenciamento do Azure está disponível em [vários idiomas](/azure/?product=developer-tools#languages-and-tools).

### <a name="set-via-arm-template"></a>Definir por meio do modelo ARM

Para definir essa propriedade usando um modelo do ARM, atualize seu modelo existente ou exporte um novo modelo para a implantação atual e, em seguida, inclua o `"disableKeyBasedMetadataWriteAccess": true` para as propriedades dos `databaseAccounts` recursos. Veja abaixo um exemplo básico de um modelo de Azure Resource Manager com essa configuração de propriedade.

```json
{
    {
      "type": "Microsoft.DocumentDB/databaseAccounts",
      "name": "[variables('accountName')]",
      "apiVersion": "2020-04-01",
      "location": "[parameters('location')]",
      "kind": "GlobalDocumentDB",
      "properties": {
        "consistencyPolicy": "[variables('consistencyPolicy')[parameters('defaultConsistencyLevel')]]",
        "locations": "[variables('locations')]",
        "databaseAccountOfferType": "Standard",
        "disableKeyBasedMetadataWriteAccess": true
        }
    }
}
```

> [!IMPORTANT]
> Certifique-se de incluir as outras propriedades para sua conta e recursos filho ao redploying com essa propriedade. Não implante este modelo como está ou ele redefinirá todas as suas propriedades de conta.

### <a name="set-via-azure-cli"></a>Definir via CLI do Azure

Para habilitar o uso de CLI do Azure, use o comando a seguir:

```azurecli-interactive
az cosmosdb update  --name [CosmosDBAccountName] --resource-group [ResourceGroupName]  --disable-key-based-metadata-write-access true

```

### <a name="set-via-powershell"></a>Definir por meio do PowerShell

Para habilitar o uso de Azure PowerShell, use o comando a seguir:

```azurepowershell-interactive
Update-AzCosmosDBAccount -ResourceGroupName [ResourceGroupName] -Name [CosmosDBAccountName] -DisableKeyBasedMetadataWriteAccess true
```

## <a name="next-steps"></a>Próximas etapas

- [O que é o controle de acesso baseado em função do Azure (RBAC do Azure)](../role-based-access-control/overview.md)
- [Funções personalizadas do Azure](../role-based-access-control/custom-roles.md)
- [Operações do provedor de recursos Azure Cosmos DB](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)
