---
title: 'Tutorial: Criar uma definição de política personalizada'
description: Neste tutorial, você cria uma definição de política personalizada do Azure Policy para impor regras de negócios personalizadas nos seus recursos do Azure.
ms.date: 03/31/2021
ms.topic: tutorial
ms.openlocfilehash: c6c4fbe05b6df8a6f8521a0551fe276c893bb6be
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106092782"
---
# <a name="tutorial-create-a-custom-policy-definition"></a>Tutorial: Criar uma definição de política personalizada

Uma definição de política personalizada permite que os clientes definam suas próprias regras para uso do Azure. Geralmente, essas regras impõem:

- Práticas de segurança
- Gerenciamento de custo
- Regras específicas da organização (como nomeação ou localizações)

Seja qual for o fator empresarial para a criação de uma política personalizada, as etapas serão as mesmas para definição da nova política personalizada.

Antes de criar uma política personalizada, verifique as [amostras de política](../samples/index.md) para ver se uma política que atende às suas necessidades já existe.

A abordagem para criação de uma política personalizada segue estas etapas:

> [!div class="checklist"]
> - Identificar seus requisitos de negócios
> - Mapear cada requisito para uma propriedade de recurso do Azure
> - Mapear a propriedade para um alias
> - Determinar qual efeito será usado
> - Redigir a definição de política

## <a name="prerequisites"></a>Pré-requisitos

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="identify-requirements"></a>Identificar os requisitos

Antes de criar a definição de política, é importante entender a intenção dela. Para este tutorial, usaremos um requisito comum de segurança empresarial com o objetivo de ilustrar as etapas envolvidas:

- Cada conta de armazenamento precisa ser habilitada para HTTPS
- Cada conta de armazenamento precisa ser desabilitada para HTTP

Seus requisitos devem identificar claramente os estados de recursos como eles "devem ser" e "não devem ser".

Embora tenhamos definido o estado esperado do recurso, ainda não definimos o que queremos fazer com os recursos fora de conformidade. O Azure Policy dá suporte a diversos [efeitos](../concepts/effects.md). Para este tutorial, definiremos o requisito de negócios como o bloqueio da criação de recursos caso eles não estejam em conformidade com as regras de negócio. Para cumprir essa meta, usaremos o efeito [Negar](../concepts/effects.md#deny). Também queremos ter a opção de suspender a política para atribuições específicas. Assim, usaremos o efeito [Desabilitado](../concepts/effects.md#disabled) e transformaremos o efeito em um [parâmetro](../concepts/definition-structure.md#parameters) na definição de política.

## <a name="determine-resource-properties"></a>Determinar as propriedades de recurso

Com base nos requisitos de negócios, o recurso do Azure para auditoria com o Azure Policy é uma conta de armazenamento. No entanto, não sabemos quais propriedades devemos usar na definição de política. O Azure Policy é avaliado em relação à representação JSON do recurso e, portanto, precisaremos entender as propriedades disponíveis nesse recurso.

Há várias maneiras de determinar as propriedades de um recurso do Azure. Examinaremos cada uma delas neste tutorial:

- Extensão do Azure Policy para o VS Code
- Modelos do ARM (modelos do Azure Resource Manager)
  - Exportar um recurso existente
  - Experiência de criação
  - Modelos de Início Rápido (GitHub)
  - Documentação de referência de modelo
- Azure Resource Explorer

### <a name="view-resources-in-vs-code-extension"></a>Exibir recursos na extensão do VS Code

A [extensão do VS Code](../how-to/extension-for-vscode.md#search-for-and-view-resources) pode ser usada para procurar recursos em seu ambiente e ver as propriedades do Resource Manager em cada recurso.

### <a name="arm-templates"></a>Modelos de ARM

Há várias maneiras de examinar um [ARM](../../../azure-resource-manager/templates/template-tutorial-use-template-reference.md) que inclui a propriedade que você deseja gerenciar.

#### <a name="existing-resource-in-the-portal"></a>Recurso existente no portal

A maneira mais simples de encontrar propriedades é examinar um recurso existente do mesmo tipo. Os recursos já definidos com a configuração que você deseja impor também fornecem o valor para comparação.
Examine a página **Exportar modelo** (em **Configurações**) no portal do Azure desse recurso específico.

> [!WARNING]
> O modelo ARM exportado pelo portal do Azure não pode ser conectado diretamente à propriedade `deployment` para um modelo do ARM em uma definição de política de [deployIfNotExists](../concepts/effects.md#deployifnotexists).

:::image type="content" source="../media/create-custom-policy-definition/export-template.png" alt-text="Captura de tela da página Exportar modelo em um recurso existente do portal do Azure." border="false":::

Fazer isso para uma conta de armazenamento revela um modelo semelhante a este exemplo:

```json
...
"resources": [{
    "comments": "Generalized from resource: '/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount'.",
    "type": "Microsoft.Storage/storageAccounts",
    "sku": {
        "name": "Standard_LRS",
        "tier": "Standard"
    },
    "kind": "Storage",
    "name": "[parameters('storageAccounts_mystorageaccount_name')]",
    "apiVersion": "2018-07-01",
    "location": "westus",
    "tags": {
        "ms-resource-usage": "azure-cloud-shell"
    },
    "scale": null,
    "properties": {
        "networkAcls": {
            "bypass": "AzureServices",
            "virtualNetworkRules": [],
            "ipRules": [],
            "defaultAction": "Allow"
        },
        "supportsHttpsTrafficOnly": false,
        "encryption": {
            "services": {
                "file": {
                    "enabled": true
                },
                "blob": {
                    "enabled": true
                }
            },
            "keySource": "Microsoft.Storage"
        }
    },
    "dependsOn": []
}]
...
```

Em **Propriedades**, há um valor chamado **supportsHttpsTrafficOnly** definido como **false**. Essa propriedade parece ser a propriedade que estamos procurando. Além disso, o **tipo** do recurso é **Microsoft.Storage/storageAccounts**. O tipo permite limitar a política somente aos recursos desse tipo.

#### <a name="create-a-resource-in-the-portal"></a>Criar um recurso no portal

Outra maneira por meio do portal é a experiência de criação de recursos. Durante a criação de uma conta de armazenamento por meio do portal, uma opção na guia **Avançado** é a **Transferência de segurança necessária**. Essa propriedade tem as opções _Desabilitado_ e _Habilitado_. O ícone de informações tem um texto adicional que confirma que essa opção provavelmente é a propriedade desejada. No entanto, o portal não informa o nome da propriedade nessa tela.

Na guia **Examinar + criar**, há um link na parte inferior da página para **Baixar um modelo para automação**. A seleção do link abre o modelo que cria o recurso que configuramos. Nesse caso, vemos duas informações essenciais:

```json
...
"supportsHttpsTrafficOnly": {
    "type": "bool"
}
...
"properties": {
    "accessTier": "[parameters('accessTier')]",
    "supportsHttpsTrafficOnly": "[parameters('supportsHttpsTrafficOnly')]"
}
...
```

Essas informações nos informam o tipo de propriedade e também confirmam que **supportsHttpsTrafficOnly** é a propriedade que estamos procurando.

#### <a name="quickstart-templates-on-github"></a>Modelos de Início Rápido no GitHub

Os [modelos de início rápido do Azure](https://github.com/Azure/azure-quickstart-templates) no GitHub contêm centenas de modelos do ARM criados para diferentes recursos. Esses modelos podem ser uma ótima maneira de encontrar a propriedade de recurso que você está procurando. Algumas propriedades podem parecer ser o que você está procurando, mas controlam algo diferente.

#### <a name="resource-reference-docs"></a>Documentação de referência de recurso

Para validar se **supportsHttpsTrafficOnly** é a propriedade correta, verifique a referência do modelo do ARM para o [recurso de conta de armazenamento](/azure/templates/microsoft.storage/2018-07-01/storageaccounts) no provedor de armazenamento. O objeto de propriedades tem uma lista de parâmetros válidos. A seleção do link [StorageAccountPropertiesCreateParameters-object](/azure/templates/microsoft.storage/2018-07-01/storageaccounts#storageaccountpropertiescreateparameters-object) mostra uma tabela de propriedades aceitáveis. **supportsHttpsTrafficOnly** está presente e a descrição corresponde ao que estamos procurando para atender aos requisitos de negócios.

### <a name="azure-resource-explorer"></a>Azure Resource Explorer

Outra maneira de explorar os recursos do Azure é por meio do [Azure Resource Explorer](https://resources.azure.com) (versão prévia). Essa ferramenta usa o contexto de sua assinatura e, portanto, você precisa se autenticar no site com suas credenciais do Azure. Depois de autenticado, você poderá procurar por provedores, assinaturas, grupos de recursos e recursos.

Localize um recurso de conta de armazenamento e examine as propriedades. Vemos a propriedade **supportsHttpsTrafficOnly** aqui também. Selecionando a guia **Documentação**, vemos que a descrição da propriedade corresponde ao que encontramos na documentação de referência anteriormente.

## <a name="find-the-property-alias"></a>Encontrar o alias de propriedade

Identificamos a propriedade de recurso, mas precisamos mapear essa propriedade para um [alias](../concepts/definition-structure.md#aliases).

Há algumas maneiras de determinar os aliases para um recurso do Azure. Examinaremos cada uma delas neste tutorial:

- Extensão do Azure Policy para o VS Code
- CLI do Azure
- Azure PowerShell

### <a name="get-aliases-in-vs-code-extension"></a>Obter aliases na extensão do VS Code

A extensão do Azure Policy para a extensão do VS Code facilita a navegação de seus recursos e a [descoberta de aliases](../how-to/extension-for-vscode.md#discover-aliases-for-resource-properties).

> [!NOTE]
> A extensão VS Code apenas expõe as propriedades do modo Resource Manager e não exibe nenhuma propriedade do [modo de Provedor de Recursos](../concepts/definition-structure.md#mode).

### <a name="azure-cli"></a>CLI do Azure

Na CLI do Azure, o grupo de comandos `az provider` é usado para pesquisar aliases de recurso. Filtraremos para o namespace **Microsoft.Storage** com base nos detalhes obtidos anteriormente sobre o recurso do Azure.

```azurecli-interactive
# Login first with az login if not using Cloud Shell

# Get Azure Policy aliases for type Microsoft.Storage
az provider show --namespace Microsoft.Storage --expand "resourceTypes/aliases" --query "resourceTypes[].aliases[].name"
```

Nos resultados, vemos um alias compatível com as contas de armazenamento chamado **supportsHttpsTrafficOnly**. A existência desse alias significa que podemos escrever a política para impor nossos requisitos de negócios.

### <a name="azure-powershell"></a>Azure PowerShell

No Azure PowerShell, o cmdlet `Get-AzPolicyAlias` é usado para pesquisar aliases de recurso. Filtraremos para o namespace **Microsoft.Storage** com base nos detalhes obtidos anteriormente sobre o recurso do Azure.

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Use Get-AzPolicyAlias to list aliases for Microsoft.Storage
(Get-AzPolicyAlias -NamespaceMatch 'Microsoft.Storage').Aliases
```

Como a CLI do Azure, os resultados mostram um alias compatível com as contas de armazenamento chamadas **supportsHttpsTrafficOnly**.

## <a name="determine-the-effect-to-use"></a>Determinar o efeito a ser usado

Decidir o que fazer com os recursos fora de conformidade é tão importante quanto decidir o que deve ser avaliado em primeiro lugar. Cada resposta possível para um recurso fora de conformidade é chamada de [efeito](../concepts/effects.md). O efeito controla se o recurso fora de conformidade está conectado, bloqueado, tem dados acrescentados ou tem uma implantação associada a ele para colocar o recurso novamente em um estado de conformidade.

Em nosso exemplo, Negar é o efeito que desejamos, pois não queremos que recursos fora de conformidade sejam criados em nosso ambiente do Azure. A auditoria é uma boa primeira escolha para um efeito de política, a fim de determinar o impacto de uma política antes de defini-la como Negar. Uma forma de facilitar a alteração do efeito por atribuição é parametrizar o efeito. Confira [Parâmetros](#parameters) abaixo para obter detalhes sobre como fazer isso.

## <a name="compose-the-definition"></a>Redigir a definição

Agora, temos os detalhes da propriedade e um alias para o que planejamos gerenciar. Em seguida, redigiremos a regra de política propriamente dita. Se você ainda não estiver familiarizado com a linguagem da política, referencie [Estrutura da definição de política](../concepts/definition-structure.md) para saber como estruturar a definição de política. Este é um modelo vazio da aparência de uma definição de política:

```json
{
    "properties": {
        "displayName": "<displayName>",
        "description": "<description>",
        "mode": "<mode>",
        "parameters": {
                <parameters>
        },
        "policyRule": {
            "if": {
                <rule>
            },
            "then": {
                "effect": "<effect>"
            }
        }
    }
}
```

### <a name="metadata"></a>Metadados

Os três primeiros componentes são os metadados da política. É fácil fornecer valores para esses componentes, pois sabemos para o que estamos criando a regra. O [modo](../concepts/definition-structure.md#mode) trata principalmente das marcas e da localização dos recursos. Como não precisamos limitar a avaliação aos recursos que dão suporte às tags, usaremos o valor _all_ para **mode**.

```json
"displayName": "Deny storage accounts not using only HTTPS",
"description": "Deny storage accounts not using only HTTPS. Checks the supportsHttpsTrafficOnly property on StorageAccounts.",
"mode": "all",
```

### <a name="parameters"></a>Parâmetros

Embora não tenhamos usado um parâmetro para alterar a avaliação, desejamos usar um parâmetro para permitir a alteração do **efeito** para solução de problemas. Definiremos um parâmetro **effectType** e o limitaremos apenas a **Negar** e **Desabilitado**. Essas duas opções correspondem aos nossos requisitos de negócios. O bloco de parâmetros concluído é parecido com este exemplo:

```json
"parameters": {
    "effectType": {
        "type": "string",
        "defaultValue": "Deny",
        "allowedValues": [
            "Deny",
            "Disabled"
        ],
        "metadata": {
            "displayName": "Effect",
            "description": "Enable or disable the execution of the policy"
        }
    }
},
```

### <a name="policy-rule"></a>Regra de política

A redação da [regra de política](../concepts/definition-structure.md#policy-rule) é a etapa final na criação de nossa definição de política personalizada. Identificamos duas instruções para teste:

- Que o **tipo** da conta de armazenamento é **Microsoft.Storage/storageAccounts**
- Que a conta de armazenamento **supportsHttpsTrafficOnly** não é **true**

Como precisamos que as duas instruções sejam verdadeiras, usaremos o [operador lógico](../concepts/definition-structure.md#logical-operators) **allOf**. Passaremos o parâmetro **effectType** para o efeito, em vez de fazer uma declaração estática. Nossa regra concluída é parecida com este exemplo:

```json
"if": {
    "allOf": [
        {
            "field": "type",
            "equals": "Microsoft.Storage/storageAccounts"
        },
        {
            "field": "Microsoft.Storage/storageAccounts/supportsHttpsTrafficOnly",
            "notEquals": "true"
        }
    ]
},
"then": {
    "effect": "[parameters('effectType')]"
}
```

### <a name="completed-definition"></a>Definição concluída

Com todas as três partes da política definida, esta é a nossa definição concluída:

```json
{
    "properties": {
        "displayName": "Deny storage accounts not using only HTTPS",
        "description": "Deny storage accounts not using only HTTPS. Checks the supportsHttpsTrafficOnly property on StorageAccounts.",
        "mode": "all",
        "parameters": {
            "effectType": {
                "type": "string",
                "defaultValue": "Deny",
                "allowedValues": [
                    "Deny",
                    "Disabled"
                ],
                "metadata": {
                    "displayName": "Effect",
                    "description": "Enable or disable the execution of the policy"
                }
            }
        },
        "policyRule": {
            "if": {
                "allOf": [
                    {
                        "field": "type",
                        "equals": "Microsoft.Storage/storageAccounts"
                    },
                    {
                        "field": "Microsoft.Storage/storageAccounts/supportsHttpsTrafficOnly",
                        "notEquals": "true"
                    }
                ]
            },
            "then": {
                "effect": "[parameters('effectType')]"
            }
        }
    }
}
```

A definição concluída pode ser usada para criar uma política. O portal e cada SDK (CLI do Azure, Azure PowerShell e API REST) aceitam a definição de diferentes maneiras. Portanto, examine os comandos de cada um para validar o uso correto. Em seguida, atribua-o, usando o efeito parametrizado, aos recursos adequados para gerenciar a segurança de suas contas de armazenamento.

## <a name="clean-up-resources"></a>Limpar os recursos

Se você estiver trabalhando com os recursos deste tutorial, use as etapas a seguir para excluir qualquer uma das atribuições ou definições criadas acima:

1. Selecione **Definições** (ou **Atribuições** se você estiver tentando excluir uma atribuição) em **Criação** no lado esquerdo da página Azure Policy.

1. Pesquisar pela nova iniciativa ou definição de política (ou atribuição) que você quer remover.

1. Clique com o botão direito na linha e selecione as reticências no final da definição ou da atribuição e selecione **Excluir Definição** (ou **Excluir Atribuição**).

## <a name="review"></a>Revisão

Neste tutorial, você realizou as seguintes tarefas com sucesso:

> [!div class="checklist"]
> - Identificou seus requisitos de negócios
> - Mapeou cada requisito para uma propriedade de recurso do Azure
> - Mapeou a propriedade para um alias
> - Determinou o efeito a ser usado
> - Redigiu a definição de política

## <a name="next-steps"></a>Próximas etapas

Em seguida, use sua definição de política personalizada para criar e atribuir uma política:

> [!div class="nextstepaction"]
> [Criar e atribuir uma definição de política](../how-to/programmatically-create.md#create-and-assign-a-policy-definition)
