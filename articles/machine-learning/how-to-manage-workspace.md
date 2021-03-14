---
title: Criar espaços de trabalho no portal
titleSuffix: Azure Machine Learning
description: Saiba como criar, exibir e excluir espaços de trabalho do Azure Machine Learning no portal do Azure ou com o SDK para Python.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sgilley
author: sdgilley
ms.date: 09/30/2020
ms.topic: conceptual
ms.custom: how-to, fasttrack-edit
ms.openlocfilehash: 472bc66c75881d622e8ecfe23031f58db773a919
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102518918"
---
# <a name="create-and-manage-azure-machine-learning-workspaces"></a>Criar e gerenciar espaços de trabalho do Azure Machine Learning 

Neste artigo, você criará, exibirá e excluirá [**espaços de trabalho Azure Machine Learning**](concept-workspace.md) para [Azure Machine Learning](overview-what-is-azure-ml.md), usando o portal do Azure ou o [SDK para python](/python/api/overview/azure/ml/)

Conforme suas necessidades mudam ou os requisitos para aumentar a automação, você também pode criar e excluir espaços de trabalho [usando a CLI](reference-azure-machine-learning-cli.md)ou [por meio da extensão vs Code](tutorial-setup-vscode-extension.md).

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Caso não tenha uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente hoje mesmo a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree).
* Se estiver usando o SDK do Python, [Instale o SDK](/python/api/overview/azure/ml/install).

## <a name="limitations"></a>Limitações

[!INCLUDE [register-namespace](../../includes/machine-learning-register-namespace.md)]

Por padrão, a criação de um espaço de trabalho também cria um ACR (registro de contêiner do Azure).  Como o ACR atualmente não dá suporte a caracteres Unicode em nomes de grupos de recursos, use um grupo de recursos que não contenha esses caracteres.

## <a name="create-a-workspace"></a>Criar um workspace

# <a name="python"></a>[Python](#tab/python)

* **Especificação padrão.** Por padrão, os recursos dependentes, bem como o grupo de recursos, serão criados automaticamente. Esse código cria um espaço de trabalho chamado `myworkspace` e um grupo de recursos chamado `myresourcegroup` em `eastus2` .
    
    ```python
    from azureml.core import Workspace
    
    ws = Workspace.create(name='myworkspace',
                   subscription_id='<azure-subscription-id>',
                   resource_group='myresourcegroup',
                   create_resource_group=True,
                   location='eastus2'
                   )
    ```
    Defina `create_resource_group` como false se você tiver um grupo de recursos do Azure existente que deseja usar para o espaço de trabalho.

* <a name="create-multi-tenant"></a>**Vários locatários.**  Se você tiver várias contas, adicione a ID de locatário do Azure Active Directory que deseja usar.  Localize sua ID de locatário do [portal do Azure](https://portal.azure.com) em **Azure Active Directory, identidades externas**.

    ```python
    from azureml.core.authentication import InteractiveLoginAuthentication
    from azureml.core import Workspace
    
    interactive_auth = InteractiveLoginAuthentication(tenant_id="my-tenant-id")
    ws = Workspace.create(name='myworkspace',
                subscription_id='<azure-subscription-id>',
                resource_group='myresourcegroup',
                create_resource_group=True,
                location='eastus2',
                auth=interactive_auth
                )
    ```

* **[Soberanas Cloud](reference-machine-learning-cloud-parity.md)**. Você precisará de um código extra para se autenticar no Azure se estiver trabalhando em uma nuvem do soberanas.

    ```python
    from azureml.core.authentication import InteractiveLoginAuthentication
    from azureml.core import Workspace
    
    interactive_auth = InteractiveLoginAuthentication(cloud="<cloud name>") # for example, cloud="AzureUSGovernment"
    ws = Workspace.create(name='myworkspace',
                subscription_id='<azure-subscription-id>',
                resource_group='myresourcegroup',
                create_resource_group=True,
                location='eastus2',
                auth=interactive_auth
                )
    ```

* **Use os recursos existentes do Azure**.  Você também pode criar um espaço de trabalho que usa recursos existentes do Azure com o formato de ID de recurso do Azure. Localize as IDs de recurso do Azure específicas no portal do Azure ou com o SDK. Este exemplo pressupõe que o grupo de recursos, a conta de armazenamento, o cofre de chaves, o app insights e o registro de contêiner já existam.

   ```python
   import os
   from azureml.core import Workspace
   from azureml.core.authentication import ServicePrincipalAuthentication

   service_principal_password = os.environ.get("AZUREML_PASSWORD")

   service_principal_auth = ServicePrincipalAuthentication(
      tenant_id="<tenant-id>",
      username="<application-id>",
      password=service_principal_password)

                        auth=service_principal_auth,
                             subscription_id='<azure-subscription-id>',
                             resource_group='myresourcegroup',
                             create_resource_group=False,
                             location='eastus2',
                             friendly_name='My workspace',
                             storage_account='subscriptions/<azure-subscription-id>/resourcegroups/myresourcegroup/providers/microsoft.storage/storageaccounts/mystorageaccount',
                             key_vault='subscriptions/<azure-subscription-id>/resourcegroups/myresourcegroup/providers/microsoft.keyvault/vaults/mykeyvault',
                             app_insights='subscriptions/<azure-subscription-id>/resourcegroups/myresourcegroup/providers/microsoft.insights/components/myappinsights',
                             container_registry='subscriptions/<azure-subscription-id>/resourcegroups/myresourcegroup/providers/microsoft.containerregistry/registries/mycontainerregistry',
                             exist_ok=False)
   ```

Para obter mais informações, consulte [referência do SDK do espaço de trabalho](/python/api/azureml-core/azureml.core.workspace.workspace).

Se você tiver problemas ao acessar sua assinatura, consulte [Configurar a autenticação para Azure Machine Learning recursos e fluxos de trabalho](how-to-setup-authentication.md), bem como a [autenticação no Azure Machine Learning](https://aka.ms/aml-notebook-auth) notebook.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Entre no [portal do Microsoft Azure](https://portal.azure.com/) usando as credenciais de assinatura do Azure. 

1. No canto superior esquerdo do portal do Azure, selecione **+ Criar um recurso**.

      ![Criar um novo recurso](./media/how-to-manage-workspace/create-workspace.gif)

1. Use a barra de pesquisa para localizar **Machine Learning**.

1. Selecione **Machine Learning**.

1. No painel **Machine Learning**, selecione **Criar** para começar.

1. Forneça as informações a seguir para configurar o novo workspace:

   Campo|Descrição 
   ---|---
   Nome do workspace |Insira um nome único que identifique seu workspace. Para este exemplo, usamos **docs-ws**. Os nomes devem ser únicos em todo o grupo de recursos. Use um nome que seja fácil de lembrar e diferenciar de workspaces criados por outras pessoas. O nome do workspace não diferencia maiúsculas de minúsculas.
   Assinatura |Selecione a assinatura do Azure que você deseja usar.
   Resource group | Use um grupo de recursos existente na sua assinatura ou insira um nome para criar um grupo de recursos. Um grupo de recursos mantém os recursos relacionados a uma solução do Azure. Para este exemplo, usamos **docs-aml**. Você precisa de um *colaborador* ou função de *proprietário* para usar um grupo de recursos existente.  Para obter mais informações sobre o acesso, consulte [gerenciar o acesso a um espaço de trabalho do Azure Machine Learning](how-to-assign-roles.md).
   Região | Selecione a região do Azure mais próxima aos usuários e aos recursos de dados para criar seu espaço de trabalho.
   | Conta de armazenamento | A conta de armazenamento padrão para o espaço de trabalho. Por padrão, um novo é criado. |
   | Key Vault | O Azure Key Vault usado pelo espaço de trabalho. Por padrão, um novo é criado. |
   | Application Insights | A instância do Application insights para o espaço de trabalho. Por padrão, um novo é criado. |
   | Registro de Contêiner | O registro de contêiner do Azure para o espaço de trabalho. Por padrão, um novo _não_ é criado inicialmente para o espaço de trabalho. Em vez disso, ele é criado quando você precisar dele ao criar uma imagem do Docker durante o treinamento ou a implantação. |

   :::image type="content" source="media/how-to-manage-workspace/create-workspace-form.png" alt-text="Configure seu espaço de trabalho.":::

1. Quando tiver terminado de configurar o espaço de trabalho, selecione **examinar + criar**. Opcionalmente, use as seções [rede](#networking) e [avançado](#advanced) para definir mais configurações para o espaço de trabalho.

1. Examine as configurações e faça quaisquer alterações ou correções adicionais. Quando estiver satisfeito com as configurações, selecione **criar**.

   > [!Warning] 
   > Pode levar vários minutos para criar seu workspace na nuvem.

   Quando o processo é finalizado, será exibida uma mensagem de êxito da implantação. 
 
 1. Para exibir o novo workspace, selecione **Ir para o recurso**.
 
---



### <a name="networking"></a>Rede  

> [!IMPORTANT]  
> Para obter mais informações sobre como usar um ponto de extremidade privado e uma rede virtual com seu espaço de trabalho, consulte [isolamento de rede e privacidade](how-to-network-security-overview.md).


# <a name="python"></a>[Python](#tab/python)

O SDK do Azure Machine Learning Python fornece a classe [PrivateEndpointConfig](/python/api/azureml-core/azureml.core.privateendpointconfig) , que pode ser usada com [Workspace. Create ()](/python/api/azureml-core/azureml.core.workspace.workspace#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---tags-none--friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--adb-workspace-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--private-endpoint-config-none--private-endpoint-auto-approval-true--exist-ok-false--show-output-true-) para criar um espaço de trabalho com um ponto de extremidade privado. Essa classe requer uma rede virtual existente.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. A configuração de rede padrão é usar um __ponto de extremidade público__, que pode ser acessado na Internet pública. Para limitar o acesso ao seu espaço de trabalho a uma rede virtual do Azure que você criou, você pode selecionar __ponto de extremidade privado__ (versão prévia) como o __método de conectividade__ e, em seguida, usar __+ Adicionar__ para configurar o ponto de extremidade.   

   :::image type="content" source="media/how-to-manage-workspace/select-private-endpoint.png" alt-text="Seleção de ponto de extremidade particular":::  

1. No formulário __criar ponto de extremidade privado__ , defina o local, o nome e a rede virtual a ser usada. Se você quiser usar o ponto de extremidade com uma zona DNS privado, selecione __integrar com a zona DNS privada__ e selecione a zona usando o campo __zona de DNS privado__ . Selecione __OK__ para criar o ponto de extremidade.   

   :::image type="content" source="media/how-to-manage-workspace/create-private-endpoint.png" alt-text="Criação de ponto de extremidade particular":::   

1. Quando terminar de configurar a rede, você poderá selecionar __revisar + criar__ ou avançar para a configuração __avançada__ opcional.

---

> [!IMPORTANT]  
> O uso de um ponto de extremidade privado com Azure Machine Learning espaço de trabalho está atualmente em visualização pública. Essa versão prévia é fornecida sem um Contrato de Nível de Serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.     
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

### <a name="multiple-workspaces-with-private-endpoint"></a>Vários espaços de trabalho com ponto de extremidade privado

Quando você cria um ponto de extremidade privado, uma nova zona de DNS privado chamada __privatelink.API.azureml.ms__ é criada. Ele contém um link para a rede virtual. Se você criar vários espaços de trabalho com pontos de extremidade privados no mesmo grupo de recursos, somente a rede virtual para o primeiro ponto final privado poderá ser adicionada à zona DNS. Para adicionar entradas para as redes virtuais usadas pelos pontos de extremidade de espaços de trabalho/particulares adicionais, use as seguintes etapas:

1. No [portal do Azure](https://portal.azure.com), selecione o grupo de recursos que contém o espaço de trabalho. Em seguida, selecione o DNS privado recurso de zona chamado __privatelink.API.azureml.ms__
2. Nas __configurações__, selecione __links de rede virtual__.
3. Selecione __Adicionar__. Na página __Adicionar link de rede virtual__ , forneça um __nome de link__ exclusivo e selecione a __rede virtual__ a ser adicionada. Selecione __OK__ para adicionar o link de rede.

Para obter mais informações, consulte [configuração de DNS do ponto de extremidade privado do Azure](../private-link/private-endpoint-dns.md).

### <a name="vulnerability-scanning"></a>Verificação de vulnerabilidade

A Central de Segurança do Azure fornece um gerenciamento de segurança unificado e proteção avançada contra ameaças nas cargas de trabalho de nuvem híbrida. Você deve permitir que a central de segurança do Azure verifique seus recursos e siga suas recomendações. Para obter mais informações, consulte  [verificação de imagem do registro de contêiner do Azure por central de segurança](../security-center/defender-for-container-registries-introduction.md) e [integração dos serviços do Azure kubernetes com a central de segurança](../security-center/defender-for-kubernetes-introduction.md).

### <a name="advanced"></a>Avançado

Por padrão, os metadados para o espaço de trabalho são armazenados em uma instância Azure Cosmos DB que a Microsoft mantém. Esses dados são criptografados usando chaves gerenciadas pela Microsoft.

Para limitar os dados que a Microsoft coleta em seu espaço de trabalho, selecione __espaço de trabalho de alto impacto nos negócios__ no portal ou defina `hbi_workspace=true ` em Python. Para obter mais informações sobre essa configuração, consulte [criptografia em repouso](concept-data-encryption.md#encryption-at-rest).

> [!IMPORTANT]  
> A seleção de alto impacto nos negócios só pode ser feita durante a criação de um espaço de trabalho. Você não pode alterar essa configuração após a criação do espaço de trabalho.   

#### <a name="use-your-own-key"></a>Use sua própria chave

Você pode fornecer sua própria chave para criptografia de dados. Isso cria a instância de Azure Cosmos DB que armazena metadados em sua assinatura do Azure.

[!INCLUDE [machine-learning-customer-managed-keys.md](../../includes/machine-learning-customer-managed-keys.md)]

Use as etapas a seguir para fornecer sua própria chave:

> [!IMPORTANT]  
> Antes de seguir essas etapas, você deve primeiro executar as seguintes ações:   
>
> 1. Autorize o __aplicativo Machine Learning__ (no gerenciamento de identidade e acesso) com permissões de colaborador em sua assinatura.  
> 1. Siga as etapas em [Configurar chaves gerenciadas pelo cliente](../cosmos-db/how-to-setup-cmk.md) para:
>     * Registrar o provedor de Azure Cosmos DB
>     * Criar e configurar um Azure Key Vault
>     * Gerar uma chave
>   
>     Você não precisa criar manualmente a instância de Azure Cosmos DB, uma será criada para você durante a criação do espaço de trabalho. Esta instância de Azure Cosmos DB será criada em um grupo de recursos separado usando um nome com base neste padrão: `<your-workspace-resource-name>_<GUID>` .   
>   
> Você não pode alterar essa configuração após a criação do espaço de trabalho. Se você excluir o Azure Cosmos DB usado pelo seu espaço de trabalho, também deverá excluir o espaço de trabalho que o está usando.

# <a name="python"></a>[Python](#tab/python)

Use `cmk_keyvault` e `resource_cmk_uri` para especificar a chave gerenciada pelo cliente.

```python
from azureml.core import Workspace
   ws = Workspace.create(name='myworkspace',
               subscription_id='<azure-subscription-id>',
               resource_group='myresourcegroup',
               create_resource_group=True,
               location='eastus2'
               cmk_keyvault='subscriptions/<azure-subscription-id>/resourcegroups/myresourcegroup/providers/microsoft.keyvault/vaults/<keyvault-name>', 
               resource_cmk_uri='<key-identifier>'
               )

```

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Selecione __chaves gerenciadas pelo cliente__ e, em seguida, selecione __clique para selecionar chave__.

    :::image type="content" source="media/how-to-manage-workspace/advanced-workspace.png" alt-text="Chaves gerenciadas pelo cliente":::

1. No formulário __selecionar chave de Azure Key Vault__ , selecione um Azure Key Vault existente, uma chave que ele contém e a versão da chave. Essa chave é usada para criptografar os dados armazenados no Azure Cosmos DB. Por fim, use o botão __selecionar__ para usar essa chave.

   :::image type="content" source="media/how-to-manage-workspace/select-key-vault.png" alt-text="Selecione a chave":::

---

### <a name="download-a-configuration-file"></a>Baixar um arquivo de configuração

Se você for criar uma [instância de computação](tutorial-1st-experiment-sdk-setup.md#azure), ignore esta etapa.  A instância de computação já criou uma cópia desse arquivo para você.

# <a name="python"></a>[Python](#tab/python)

Se você planeja usar código em seu ambiente local que faz referência a esse espaço de trabalho ( `ws` ), grave o arquivo de configuração:

```python
ws.write_config()
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

Se você planeja usar código em seu ambiente local que referencia esse workspace, selecione **Baixar config.json** da seção **Visão geral** do workspace.  

   ![Baixe o config.json](./media/how-to-manage-workspace/configure.png)

---

Coloque o arquivo na estrutura de diretório com seus scripts de Python ou Jupyter Notebooks. Ele pode estar no mesmo diretório, um subdiretório denominado *.azureml* ou em um diretório pai. Quando você cria uma instância de computação, esse arquivo é adicionado ao diretório correto na VM para você.

## <a name="connect-to-a-workspace"></a>Conectar-se a um workspace

No seu código Python, você cria um objeto de espaço de trabalho para se conectar ao seu espaço de trabalho.  Esse código lerá o conteúdo do arquivo de configuração para localizar seu espaço de trabalho.  Você receberá uma solicitação para entrar se ainda não estiver autenticado.

```python
from azureml.core import Workspace

ws = Workspace.from_config()
```

* <a name="connect-multi-tenant"></a>**Vários locatários.**  Se você tiver várias contas, adicione a ID de locatário do Azure Active Directory que deseja usar.  Localize sua ID de locatário do [portal do Azure](https://portal.azure.com) em **Azure Active Directory, identidades externas**.

    ```python
    from azureml.core.authentication import InteractiveLoginAuthentication
    from azureml.core import Workspace
    
    interactive_auth = InteractiveLoginAuthentication(tenant_id="my-tenant-id")
    ws = Workspace.from_config(auth=interactive_auth)
    ```

* **[Soberanas Cloud](reference-machine-learning-cloud-parity.md)**. Você precisará de um código extra para se autenticar no Azure se estiver trabalhando em uma nuvem do soberanas.

    ```python
    from azureml.core.authentication import InteractiveLoginAuthentication
    from azureml.core import Workspace
    
    interactive_auth = InteractiveLoginAuthentication(cloud="<cloud name>") # for example, cloud="AzureUSGovernment"
    ws = Workspace.from_config(auth=interactive_auth)
    ```
    
Se você tiver problemas ao acessar sua assinatura, consulte [Configurar a autenticação para Azure Machine Learning recursos e fluxos de trabalho](how-to-setup-authentication.md), bem como a [autenticação no Azure Machine Learning](https://aka.ms/aml-notebook-auth) notebook.

## <a name="find-a-workspace"></a><a name="view"></a>Localizar um espaço de trabalho

Veja uma lista de todos os espaços de trabalho que você pode usar.

# <a name="python"></a>[Python](#tab/python)

Localize suas assinaturas na [página de assinaturas no portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).  Copie a ID e use-a no código abaixo para ver todos os espaços de trabalho disponíveis para essa assinatura.

```python
from azureml.core import Workspace

Workspace.list('<subscription-id>')
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Entre no [portal do Azure](https://portal.azure.com/).

1. No campo de pesquisa superior, digite **Machine Learning**.  

1. Selecione **Machine Learning**.

   ![Procurar Azure Machine Learning espaço de trabalho](./media/how-to-manage-workspace/find-workspaces.png)

1. Examine a lista de workspaces encontrada. É possível filtrar com base na assinatura, grupos de recursos e locais.  

1. Selecione um espaço de trabalho para exibir suas propriedades.

---


## <a name="delete-a-workspace"></a>Excluir um workspace

Quando você não precisar mais de um espaço de trabalho, exclua-o.  

# <a name="python"></a>[Python](#tab/python)

Exclua o espaço de trabalho `ws` :

```python
ws.delete(delete_dependent_resources=False, no_wait=False)
```

A ação padrão não é excluir os recursos associados ao espaço de trabalho, ou seja, registro de contêiner, conta de armazenamento, cofre de chaves e Application insights.  Defina `delete_dependent_resources` como true para excluir esses recursos também.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Na [portal do Azure](https://portal.azure.com/), selecione **excluir**  na parte superior do espaço de trabalho que você deseja excluir.

:::image type="content" source="./media/how-to-manage-workspace/delete-workspace.png" alt-text="Excluir workspace":::

---

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="troubleshooting"></a>Solução de problemas

* **Navegadores com suporte no Azure Machine Learning Studio**: Recomendamos que você use o navegador mais atualizado que é compatível com seu sistema operacional. Há suporte para os seguintes navegadores:
  * Microsoft Edge (a versão mais recente, não o Microsoft Edge herdado)
  * Safari (última versão, apenas Mac)
  * Chrome (última versão)
  * Firefox (última versão)

* **Portal do Azure**: 
  * Se você ir diretamente para seu espaço de trabalho de um link de compartilhamento do SDK ou do portal do Azure, não será possível exibir a página de **visão geral** padrão que tem informações de assinatura na extensão. Nesse cenário, você também não pode alternar para outro espaço de trabalho. Para exibir outro espaço de trabalho, vá diretamente para [Azure Machine Learning Studio](https://ml.azure.com) e pesquise o nome do espaço de trabalho.
  * Todos os ativos (conjuntos de valores, testes, computações e assim por diante) estão disponíveis apenas no [Azure Machine Learning Studio](https://ml.azure.com). Eles *não* estão disponíveis na portal do Azure.

### <a name="resource-provider-errors"></a>Erros do provedor de recursos

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="moving-the-workspace"></a>Como mover o workspace

> [!WARNING]
> Não há suporte para a movimentação do workspace do Azure Machine Learning para outra assinatura nem para a movimentação da assinatura proprietária para um novo locatário. Se você fizer isso, poderá causar erros.

### <a name="deleting-the-azure-container-registry"></a>Como excluir o Registro de Contêiner do Azure

O workspace do Azure Machine Learning usa o ACR (Registro de Contêiner do Azure) para algumas operações. Ele criará automaticamente uma instância do ACR quando precisar de uma pela primeira vez.

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

## <a name="examples"></a>Exemplos

Exemplos de criação de um espaço de trabalho:
* Usar portal do Azure para [criar um espaço de trabalho e uma instância de computação](tutorial-1st-experiment-sdk-setup.md)
* Use o SDK do Python para [criar um espaço de trabalho em seu próprio ambiente](tutorial-1st-experiment-sdk-setup-local.md)

## <a name="next-steps"></a>Próximas etapas

Quando você tiver um espaço de trabalho, saiba como [treinar e implantar um modelo](tutorial-train-models-with-aml.md).