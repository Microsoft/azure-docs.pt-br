---
title: Tutorial – Adicionar recurso ao modelo
description: Descreve as etapas para criar seu primeiro modelo do ARM (Azure Resource Manager). Você aprende sobre a sintaxe do arquivo de modelo e como implantar uma conta de armazenamento.
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: 49cee5c98c4099e214a732371269e935db353152
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97106964"
---
# <a name="tutorial-add-a-resource-to-your-arm-template"></a>Tutorial: Adicionar um recurso ao modelo do ARM

No [tutorial anterior](template-tutorial-create-first-template.md), você aprendeu a criar um modelo do ARM (Azure Resource Manager) em branco e implantá-lo. Agora você está pronto para implantar um recurso real. Neste tutorial, você adiciona uma conta de armazenamento. Este tutorial demora cerca de **9 minutos** para ser concluído.

## <a name="prerequisites"></a>Pré-requisitos

Recomendamos que você conclua o [tutorial introdutório sobre modelos](template-tutorial-create-first-template.md), mas isso não é obrigatório.

É necessário ter o Visual Studio Code com a extensão Ferramentas do Resource Manager e o Azure PowerShell ou a CLI do Azure. Para obter mais informações, confira [Ferramentas de modelo](template-tutorial-create-first-template.md#get-tools).

## <a name="add-resource"></a>Adicionar recurso

Para adicionar uma definição de conta de armazenamento ao modelo existente, examine o JSON realçado no exemplo a seguir. Em vez de tentar copiar as seções do modelo, copie o arquivo inteiro e substitua o seu modelo pelo conteúdo desse arquivo.

Substitua `{provide-unique-name}` e as chaves `{}` por um nome de conta de armazenamento exclusivo.

> [!IMPORTANT]
> O nome da conta de armazenamento deve ser exclusivo no Azure. O nome deve ter apenas letras minúsculas ou números. Ele não pode ter mais de 24 caracteres. Você pode tentar um padrão de nomenclatura, tal como usar **store1** como um prefixo e, em seguida, adicionar suas iniciais e a data de hoje. Por exemplo, o nome que você usa pode ser semelhante a **store1abc09092019**.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-storage/azuredeploy.json" range="1-19" highlight="5-17":::

Adivinhar um nome exclusivo para uma conta de armazenamento não é fácil e não funciona bem na automatização de grandes implantações. Posteriormente nesta série de tutoriais, você usará recursos de modelo que facilitam a criação de um nome exclusivo.

## <a name="resource-properties"></a>Propriedades de recurso

Você pode estar se perguntando como encontrar as propriedades a serem usadas para cada tipo de recurso. Você pode usar a [referência de modelo do ARM](/azure/templates/) para localizar os tipos de recursos que você deseja implantar.

Cada recurso que você implanta tem pelo menos as três propriedades a seguir:

- `type`: Tipo do recurso. Esse valor é uma combinação do namespace do provedor de recursos e do tipo de recurso como `Microsoft.Storage/storageAccounts`.
- `apiVersion`: Versão da API REST a ser usada para criar o recurso. Cada provedor de recursos publica as próprias versões de API, portanto, esse valor é específico para o tipo.
- `name`: Nome do recurso.

A maioria dos recursos também tem uma propriedade `location`, que define a região em que o recurso é implantado.

As outras propriedades variam por tipo de recurso e versão de API. É importante entender a conexão entre a versão da API e as propriedades disponíveis, então vamos ver isso em mais detalhes.

Neste tutorial, você adicionou uma conta de armazenamento ao modelo. Você pode ver essa versão da API em [storageAccounts 2019-04-01](/azure/templates/microsoft.storage/2019-04-01/storageaccounts). Observe que você não adicionou todas as propriedades ao seu modelo. Muitas das propriedades são opcionais. O provedor de recursos `Microsoft.Storage` pode liberar uma nova versão de API, mas a versão que você está implantando não precisa ser alterada. Você poderá continuar usando essa versão e saber que os resultados da implantação serão consistentes.

Se você exibir uma versão de API mais antiga, assim como [storageAccounts 2016-05-01](/azure/templates/microsoft.storage/2016-05-01/storageaccounts), verá que um conjunto menor de propriedades está disponível.

Se você decidir alterar a versão da API para um recurso, será preciso avaliar as propriedades dessa versão e ajustar o modelo adequadamente.

## <a name="deploy-template"></a>Implantar modelo

Você pode implantar o modelo para criar a conta de armazenamento. Dê um nome diferente à sua implantação para que você possa encontrá-la com facilidade no histórico.

Caso você não tenha criado o grupo de recursos, confira [Criar grupo de recursos](template-tutorial-create-first-template.md#create-resource-group). O exemplo pressupõe que você tenha definido a variável `templateFile` como o caminho para o arquivo de modelo, conforme mostrado no [primeiro tutorial](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addstorage `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para executar esse comando de implantação, você precisa ter a [versão mais recente](/cli/azure/install-azure-cli) da CLI do Azure.

```azurecli
az deployment group create \
  --name addstorage \
  --resource-group myResourceGroup \
  --template-file $templateFile
```

---

> [!NOTE]
> Se a implantação falhar, use a opção `verbose` para obter informações sobre os recursos que estão sendo criados. Use a opção `debug` para obter mais informações de depuração.

Duas possíveis falhas de implantação que você pode encontrar:

- `Error: Code=AccountNameInvalid; Message={provide-unique-name}` não é um nome de conta de armazenamento válido. O nome da conta de armazenamento deve ter entre 3 e 24 caracteres, usar números e apenas letras minúsculas.

    No modelo, substitua `{provide-unique-name}` por um nome exclusivo da conta de armazenamento. Confira [Adicionar recurso](#add-resource).

- `Error: Code=StorageAccountAlreadyTaken; Message=The storage account named store1abc09092019` já está em uso.

    No modelo, experimente um nome de conta de armazenamento diferente.

Essa implantação leva mais tempo do que a implantação de modelo em branco porque a conta de armazenamento é criada. Isso pode levar cerca de um minuto, mas geralmente é mais rápido.

## <a name="verify-deployment"></a>Verificar implantação

Você pode verificar a implantação explorando o grupo de recursos no portal do Azure.

1. Entre no [portal do Azure](https://portal.azure.com).
1. No menu à esquerda, selecione **Grupos de recursos**.
1. Selecione o grupo de recursos no qual você fez a implantação.
1. Você verá que uma conta de armazenamento foi implantada.
1. Observe que o rótulo de implantação agora mostra: **Implantações: duas bem-sucedidas**.

## <a name="clean-up-resources"></a>Limpar os recursos

Se você estiver passando para o próximo tutorial, não será necessário excluir o grupo de recursos.

Se estiver parando agora, o ideal será limpar os recursos implantados excluindo o grupo de recursos.

1. No portal do Azure, escolha **Grupos de recursos** do menu à esquerda.
2. No campo **Filtrar por nome**, insira o nome do grupo de recursos.
3. Selecione o nome do grupo de recursos.
4. Escolha **Excluir grupo de recursos** no menu superior.

## <a name="next-steps"></a>Próximas etapas

Você criou um modelo simples para implantar uma conta do Armazenamento do Azure. Nos tutoriais posteriores, você aprenderá a adicionar parâmetros, variáveis, recursos e saídas a um modelo. Esses recursos são os blocos de construção para modelos muito mais complexos.

> [!div class="nextstepaction"]
> [Adicionar parâmetros](template-tutorial-add-parameters.md)
