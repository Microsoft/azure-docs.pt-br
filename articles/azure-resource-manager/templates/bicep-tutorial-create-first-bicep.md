---
title: Tutorial – Criar e implantar os arquivos Bicep do Azure Resource Manager
description: Crie seu primeiro arquivo Bicep para implantar recursos do Azure. No tutorial, você aprenderá sobre a sintaxe do arquivo Bicep e como implantar uma conta de armazenamento.
author: mumian
ms.date: 03/17/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: 8979585d7ec0fa6eac1866375fe1e80214f2d2e2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104594267"
---
# <a name="tutorial-create-and-deploy-first-azure-resource-manager-bicep-file"></a>Tutorial: criar e implantar seu primeiro arquivo Bicep do Azure Resource Manager

Este tutorial apresenta o [Bicep](./bicep-overview.md). Ele mostra como criar um arquivo Bicep inicial e implantá-lo no Azure. Você aprenderá sobre a estrutura do arquivo Bicep e as ferramentas necessárias para trabalhar com esse arquivo. Leva cerca de **12 minutos** para concluir este tutorial, mas o tempo real varia com base em quantas ferramentas você precisa instalar.

Este tutorial é o primeiro de uma série. À medida que avança pela série, você modifica o arquivo Bicep inicial passo a passo até explorar todas as partes principais desse arquivo. Esses elementos são os blocos de construção para arquivos Bicep muito mais complexos. Esperamos que ao final da série você tenha confiança para criar seus próprios arquivos Bicep e esteja pronto para automatizar suas implantações com arquivos desse tipo.

Se você quiser saber mais sobre os benefícios de usar o Bicep e por que deve automatizar a implantação com esse arquivo, confira [Bicep](./bicep-overview.md).

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="get-tools"></a>Obter ferramentas

Vamos começar verificando se você tem as ferramentas necessárias para criar e implantar arquivos Bicep. Instale estas ferramentas no seu computador local.

### <a name="editor"></a>Editor

Para criar arquivos Bicep, você precisará de um bom editor. Recomendamos o Visual Studio Code com a extensão Bicep. Se você precisar instalar essas ferramentas, confira [Configurar ambiente de desenvolvimento do Bicep](./bicep-install.md#development-environment).

### <a name="command-line-deployment"></a>Implantação de linha de comando

Você pode implantar arquivos Bicep usando a CLI do Azure ou o Azure PowerShell. Para a CLI do Azure, você precisa da versão 2.20.0 ou posterior; para o Azure PowerShell, você precisa da versão 5.6.0 ou posterior. Para obter as instruções de instalação, confira:

- [Instale o Azure PowerShell](/powershell/azure/install-az-ps)
- [Instalar a CLI do Azure no Windows](/cli/azure/install-azure-cli-windows)
- [Instalar a CLI do Azure no Linux](/cli/azure/install-azure-cli-linux)
- [Instalar a CLI do Azure no macOS](/cli/azure/install-azure-cli-macos)

Depois de instalar o Azure PowerShell ou a CLI do Azure, é preciso que você entre pela primeira vez. Para obter ajuda, confira [Entrar – PowerShell](/powershell/azure/install-az-ps#sign-in) ou [Entrar – CLI do Azure](/cli/azure/get-started-with-azure-cli#sign-in).

Ok, você está pronto para começar a aprender sobre o Bicep.

## <a name="create-your-first-bicep-file"></a>Criar seu primeiro arquivo Bicep

1. Abra o Visual Studio Code com a extensão Bicep instalada.
1. Do menu **Arquivo**, selecione **Novo Arquivo** para criar um novo arquivo.
1. No menu **Arquivo**, selecione **Salvar como**.
1. Nomeie o arquivo como _azuredeploy_ e selecione a extensão de arquivo _bicep_. O nome completo do arquivo é _azuredeploy.bicep_.
1. Salve o arquivo na estação de trabalho. Selecione um caminho que seja fácil de lembrar, pois você fornecerá esse caminho mais tarde ao implantar o arquivo Bicep.
1. Copie e cole o seguinte conteúdo no arquivo:

    ```bicep
    resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' = {
      name: '{provide-unique-name}'
      location: 'eastus'
      sku: {
        name: 'Standard_LRS'
      }
      kind: 'StorageV2'
      properties: {
        supportsHttpsTrafficOnly: true
      }
    }
    ```

    Esta é a aparência do seu ambiente do Visual Studio Code:

    ![Primeiro arquivo Bicep do Visual Studio Code](./media/Bicep-tutorial-create-first-bicep/resource-manager-visual-studio-code-first-bicep.png)

    A declaração de recurso tem quatro componentes:

    - **recurso**: palavra-chave.
    - **nome simbólico** (stg): um nome simbólico é um identificador para referenciar o recurso em todo o arquivo Bicep. Não se trata de qual será o nome do recurso quando for implantado. O nome do recurso é definido pela propriedade **Name**.  Confira o quarto componente desta lista. Para facilitar o acompanhamento dos tutoriais, o **stg** é usado como o nome simbólico para o recurso de conta de armazenamento nesta série de tutoriais. Para ver como usar o nome simbólico para obter uma lista completa das propriedades do objeto, confira [Adicionar saídas](./bicep-tutorial-add-outputs.md).
    - **tipo de recurso** (Microsoft.Storage/storageAccounts@2019-06-01): é composto pelo provedor de recursos (Microsoft.Storage), tipo de recurso (StorageAccounts) e apiVersion (2019-06-01). Cada provedor de recursos publica as próprias versões de API, portanto, esse valor é específico para o tipo. Você pode encontrar mais tipos e apiVersions para vários recursos do Azure da [referência de modelo do ARM](/azure/templates/).
    - **propriedades** (tudo dentro de = {...}): são as propriedades específicas que você gostaria de especificar para o tipo de recurso fornecido. São exatamente as mesmas propriedades disponíveis em um modelo do ARM. Cada recurso tem uma propriedade `name`. A maioria dos recursos também tem uma propriedade `location`, que define a região em que o recurso é implantado. As outras propriedades variam por tipo de recurso e versão de API. É importante entender a conexão entre a versão da API e as propriedades disponíveis, então vamos ver isso em mais detalhes.

        Para esta conta de armazenamento, você pode ver a versão de API em [storageAccounts 2019-06-01](/azure/templates/microsoft.storage/2019-06-01/storageaccounts). Observe que você não adicionou todas as propriedades ao seu arquivo Bicep. Muitas das propriedades são opcionais. O provedor de recursos `Microsoft.Storage` pode liberar uma nova versão de API, mas a versão que você está implantando não precisa ser alterada. Você poderá continuar usando essa versão e saber que os resultados da implantação serão consistentes.

        Se você exibir uma versão de API mais antiga, como [storageAccounts 2016-05-01](/azure/templates/microsoft.storage/2016-05-01/storageaccounts), verá que um conjunto menor de propriedades está disponível.

        Se você decidir alterar a versão da API para um recurso, será preciso avaliar as propriedades dessa versão e ajustar o arquivo Bicep adequadamente.

1. Substitua `{provide-unique-name}` incluindo as chaves `{}` com um nome de conta de armazenamento exclusivo.

    > [!IMPORTANT]
    > O nome da conta de armazenamento deve ser exclusivo no Azure. O nome deve ter apenas letras minúsculas ou números. Ele não pode ter mais de 24 caracteres. Você pode tentar um padrão de nomenclatura, tal como usar **store1** como um prefixo e, em seguida, adicionar suas iniciais e a data de hoje. Por exemplo, o nome que você usa pode ser semelhante a **store1abc09092019**.

    Adivinhar um nome exclusivo para uma conta de armazenamento não é fácil e não funciona bem na automatização de grandes implantações. Posteriormente nesta série de tutoriais, você usará recursos do Bicep que facilitam a criação de um nome exclusivo.

1. Salve o arquivo.

Parabéns, você criou seu primeiro arquivo Bicep.

## <a name="sign-in-to-azure"></a>Entrar no Azure

Para começar a trabalhar com o Azure PowerShell/CLI do Azure, entre com suas credenciais do Azure.

Selecione as guias nas seções de código a seguir para escolher entre o Azure PowerShell e a CLI do Azure. Os exemplos de CLI neste artigo são escritos para o shell do Bash.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Connect-AzAccount
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
az login
```

---

Se tiver várias assinaturas do Azure, selecione a que deseja usar. Substitua `[SubscriptionID/SubscriptionName]` e os colchetes `[]` pelas informações da sua assinatura:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Set-AzContext [SubscriptionID/SubscriptionName]
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
az account set --subscription [SubscriptionID/SubscriptionName]
```

---

## <a name="create-resource-group"></a>Criar grupo de recursos

Ao implantar um arquivo Bicep, você especificará um grupo de recursos que conterá os recursos. Antes de executar o comando de implantação, crie o grupo de recursos com a CLI do Azure ou o Azure PowerShell.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup `
  -Name myResourceGroup `
  -Location "Central US"
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
az group create \
  --name myResourceGroup \
  --location "Central US"
```

---

## <a name="deploy-bicep-file"></a>Implantar o arquivo Bicep

Bicep é uma abstração transparente sobre os modelos do ARM (modelos do Azure Resource Manager). Cada arquivo Bicep é compilado em um modelo do ARM padrão antes de ser implantado. Você pode compilar o arquivo Bicep em um modelo do ARM antes de implantá-lo ou implantar o arquivo diretamente. Para implantar o arquivo Bicep, use a CLI do Azure ou o Azure PowerShell. Use o grupo de recursos que você criou anteriormente. Dê um nome para a implantação para que você possa identificá-la facilmente no histórico de implantação. Para sua conveniência, crie também uma variável que armazene o caminho para o arquivo Bicep. Essa variável facilita a execução dos comandos de implantação porque você não precisa digitar o caminho toda vez que implantar. Substitua `{provide-the-path-to-the-bicep-file}` incluindo as chaves `{}` com o caminho para o arquivo Bicep com o nome da extensão de arquivo _.bicep_.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para executar esse cmdlet de implantação, você precisa ter a [versão mais recente](/powershell/azure/install-az-ps) do Azure PowerShell.

```azurepowershell
$bicepFile = "{provide-the-path-to-the-bicep-file}"
New-AzResourceGroupDeployment `
  -Name firstbicep `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para executar esse comando de implantação, você precisa ter a [versão mais recente](/cli/azure/install-azure-cli) da CLI do Azure.

```azurecli
bicepFile="{provide-the-path-to-the-bicep-file}"
az deployment group create \
  --name firstbicep \
  --resource-group myResourceGroup \
  --template-file $bicepFile
```

---

O comando de implantação retorna os resultados. Procure `ProvisioningState` para ver se a implantação foi bem-sucedida.

> [!NOTE]
> Se a implantação falhar, use a opção `verbose` para obter informações sobre os recursos que estão sendo criados. Use a opção `debug` para obter mais informações de depuração.

## <a name="verify-deployment"></a>Verificar implantação

Você pode verificar a implantação explorando o grupo de recursos no portal do Azure.

1. Entre no [portal do Azure](https://portal.azure.com).

1. No menu à esquerda, selecione **Grupos de recursos**.

1. Selecione o grupo de recursos implantado no último procedimento. O nome padrão é **myResourceGroup**. Você não deverá ver nenhum recurso implantado dentro do grupo de recursos.

1. Observe que, no canto superior direito da visão geral, o status da implantação é exibido. Selecione **1 Bem-sucedida**.

   ![Exibir status da implantação](./media/bicep-tutorial-create-first-bicep/deployment-status.png)

1. Você vê um histórico das implantações do grupo de recursos. Selecione **firstbicep**.

   ![Selecionar a implantação](./media/bicep-tutorial-create-first-bicep/select-from-deployment-history.png)

1. Você verá um resumo da implantação. Há uma conta de armazenamento implantada. Observe que à esquerda você pode exibir entradas, saídas e o modelo usado durante a implantação.

   ![Exibir resumo da implantação](./media/bicep-tutorial-create-first-bicep/view-deployment-summary.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Se você estiver passando para o próximo tutorial, não será necessário excluir o grupo de recursos.

Se estiver parando agora, talvez você queira excluir o grupo de recursos.

1. No portal do Azure, escolha **Grupos de recursos** do menu à esquerda.
2. No campo **Filtrar por nome**, insira o nome do grupo de recursos.
3. Selecione o nome do grupo de recursos.
4. Escolha **Excluir grupo de recursos** no menu superior.

## <a name="next-steps"></a>Próximas etapas

Você criou um arquivo Bicep simples para implantar no Azure.  Nos tutoriais posteriores, você aprenderá a adicionar parâmetros, variáveis, saídas e módulos a um arquivo Bicep. Esses recursos são os blocos de construção para arquivos Bicep muito mais complexos.

> [!div class="nextstepaction"]
> [Adicionar parâmetros](bicep-tutorial-add-parameters.md)
