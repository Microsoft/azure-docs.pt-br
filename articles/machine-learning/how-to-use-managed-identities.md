---
title: Usar identidades gerenciadas para controle de acesso (versão prévia)
titleSuffix: Azure Machine Learning
description: Saiba como usar identidades gerenciadas para controlar o acesso aos recursos do Azure de Azure Machine Learning espaço de trabalho.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.topic: conceptual
ms.date: 10/22/2020
ms.openlocfilehash: 3246f6668a653c05599001a2f8d8fc8d376936ce
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2021
ms.locfileid: "105642422"
---
# <a name="use-managed-identities-with-azure-machine-learning-preview"></a>Usar identidades gerenciadas com Azure Machine Learning (versão prévia)

[Identidades gerenciadas](../active-directory/managed-identities-azure-resources/overview.md) permitem que você configure seu espaço de trabalho com as *permissões mínimas necessárias para acessar recursos*. 

Ao configurar Azure Machine Learning espaço de trabalho de maneira confiável, é importante garantir que diferentes serviços associados ao espaço de trabalho tenham o nível correto de acesso. Por exemplo, durante o fluxo de trabalho de aprendizado de máquina, o Workspace precisa de acesso ao registro de contêiner do Azure (ACR) para imagens do Docker e contas de armazenamento para dados de treinamento. 

Além disso, as identidades gerenciadas permitem um controle refinado sobre as permissões, por exemplo, você pode conceder ou revogar o acesso de recursos de computação específicos a um ACR específico.

Neste artigo, você aprenderá a usar identidades gerenciadas para:

 * Configure e use o ACR para seu espaço de trabalho Azure Machine Learning sem precisar habilitar o acesso de usuário administrador ao ACR.
 * Acesse um ACR privado externo ao seu espaço de trabalho para efetuar pull de imagens base para treinamento ou inferência.
 * Crie um espaço de trabalho com identidade gerenciada atribuída pelo usuário para acessar os recursos associados.

> [!IMPORTANT]
> O uso de identidades gerenciadas para controlar o acesso a recursos com Azure Machine Learning está atualmente em versão prévia. A funcionalidade de visualização é fornecida "no estado em que se encontra", sem garantia de suporte ou contrato de nível de serviço. Para obter mais informações, consulte os [termos de uso complementares para visualizações de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
 
## <a name="prerequisites"></a>Pré-requisitos

- Um Workspace do Azure Machine Learning. Para obter mais informações, consulte [criar um Azure Machine Learning espaço de trabalho](how-to-manage-workspace.md).
- A [extensão de CLI do Azure para o serviço Machine Learning](reference-azure-machine-learning-cli.md)
- O [SDK do Azure Machine Learning Python](/python/api/overview/azure/ml/intro).
- Para atribuir funções, o logon da sua assinatura do Azure deve ter a função de [operador de identidade gerenciada](../role-based-access-control/built-in-roles.md#managed-identity-operator) ou outra função que conceda as ações necessárias (como o __proprietário__).
- Você deve estar familiarizado com a criação e o trabalho com [identidades gerenciadas](../active-directory/managed-identities-azure-resources/overview.md).

## <a name="configure-managed-identities"></a>Configurar identidades gerenciadas

Em algumas situações, é necessário impedir que o usuário administrador acesse o registro de contêiner do Azure. Por exemplo, o ACR pode ser compartilhado e você precisa desautorizar o acesso de administrador por outros usuários. Ou, criar ACR com o usuário administrador habilitado não é permitido por uma política de nível de assinatura.

> [!IMPORTANT]
> Ao usar Azure Machine Learning para inferência na ACI (instância de contêiner do Azure), o acesso de usuário de administrador no ACR é __necessário__. Não o desabilite se você planeja implantar modelos em ACI para inferência.

Quando você cria ACR sem habilitar o acesso de usuário administrador, as identidades gerenciadas são usadas para acessar o ACR para criar e extrair imagens do Docker.

Você pode colocar seu próprio ACR com o usuário administrador desabilitado ao criar o espaço de trabalho. Como alternativa, permita Azure Machine Learning criar ACR de espaço de trabalho e desabilitar o usuário administrador posteriormente.

### <a name="bring-your-own-acr"></a>Traga seu próprio ACR

Se o usuário administrador do ACR não for permitido pela política de assinatura, primeiro crie ACR sem usuário administrador e, em seguida, associe-o ao espaço de trabalho. Além disso, se você tiver um ACR existente com o usuário administrador desabilitado, você poderá anexá-lo ao espaço de trabalho.

[Crie um ACR de CLI do Azure](../container-registry/container-registry-get-started-azure-cli.md) sem definir o ```--admin-enabled``` argumento ou de portal do Azure sem habilitar o usuário administrador. Em seguida, ao criar Azure Machine Learning espaço de trabalho, especifique a ID de recurso do Azure do ACR. O exemplo a seguir demonstra como criar um novo espaço de trabalho do Azure ML que usa um ACR existente:

> [!TIP]
> Para obter o valor do `--container-registry` parâmetro, use o comando [AZ ACR show](/cli/azure/acr#az_acr_show) para mostrar informações de seu ACR. O `id` campo contém a ID de recurso para o ACR.

```azurecli-interactive
az ml workspace create -w <workspace name> \
-g <workspace resource group> \
-l <region> \
--container-registry /subscriptions/<subscription id>/resourceGroups/<acr resource group>/providers/Microsoft.ContainerRegistry/registries/<acr name>
```

### <a name="let-azure-machine-learning-service-create-workspace-acr"></a>Permitir que Azure Machine Learning serviço crie ACR de espaço de trabalho

Se você não trazer seu próprio ACR, Azure Machine Learning serviço criará um para você quando você executar uma operação que precisa de um. Por exemplo, envie uma execução de treinamento para Computação do Machine Learning, crie um ambiente ou implante um ponto de extremidade de serviço Web. O ACR criado pelo espaço de trabalho terá o usuário administrador habilitado e você precisará desabilitar o usuário administrador manualmente.

1. Criar um novo workspace

    ```azurecli-interactive
    az ml workspace show -n <my workspace> -g <my resource group>
    ```

1. Execute uma ação que exija ACR. Por exemplo, o [tutorial sobre como treinar um modelo](tutorial-train-models-with-aml.md).

1. Obtenha o nome do ACR criado pelo cluster:

    ```azurecli-interactive
    az ml workspace show -w <my workspace> \
    -g <my resource group>
    --query containerRegistry
    ```

    Esse comando retorna um valor semelhante ao texto a seguir. Você quer apenas a última parte do texto, que é o nome da instância do ACR:

    ```output
    /subscriptions/<subscription id>/resourceGroups/<my resource group>/providers/MicrosoftContainerReggistry/registries/<ACR instance name>
    ```

1. Atualize o ACR para desabilitar o usuário administrador:

    ```azurecli-interactive
    az acr update --name <ACR instance name> --admin-enabled false
    ```

### <a name="create-compute-with-managed-identity-to-access-docker-images-for-training"></a>Criar computação com identidade gerenciada para acessar imagens do Docker para treinamento

Para acessar o ACR do espaço de trabalho, crie o cluster de computação do Machine Learning com a identidade gerenciada atribuída pelo sistema habilitada. Você pode habilitar a identidade de portal do Azure ou Studio ao criar a computação ou de CLI do Azure usando o abaixo. Para obter mais informações, consulte [usando a identidade gerenciada com clusters de computação](how-to-create-attach-compute-cluster.md#managed-identity).

# <a name="python"></a>[Python](#tab/python)

Ao criar um cluster de computação com o [AmlComputeProvisioningConfiguration](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcomputeprovisioningconfiguration), use o `identity_type` parâmetro para definir o tipo de identidade gerenciada.

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interaction
az ml computetarget create amlcompute --name cpucluster -w <workspace> -g <resource group> --vm-size <vm sku> --assign-identity '[system]'
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

Para obter informações sobre como configurar a identidade gerenciada ao criar um cluster de computação no estúdio, consulte [Configurar a identidade gerenciada](how-to-create-attach-compute-studio.md#managed-identity).

---

Uma identidade gerenciada recebe automaticamente a função ACRPull no ACR do espaço de trabalho para habilitar a extração de imagens do Docker para treinamento.

> [!NOTE]
> Se você criar a computação primeiro, antes de o ACR do espaço de trabalho ter sido criado, você precisará atribuir a função ACRPull manualmente.

## <a name="access-base-images-from-private-acr"></a>Acessar imagens base do ACR privado

Por padrão, o Azure Machine Learning usa imagens base do Docker que vêm de um repositório público gerenciado pela Microsoft. Em seguida, ele cria seu ambiente de treinamento ou inferência nessas imagens. Para obter mais informações, consulte [o que são ambientes ml?](concept-environments.md).

Para usar uma imagem de base personalizada interna à sua empresa, você pode usar identidades gerenciadas para acessar seu ACR privado. Há dois casos de uso:

 * Use a imagem base para treinamento como está.
 * Crie Azure Machine Learning imagem gerenciada com imagem personalizada como base.

### <a name="pull-docker-base-image-to-machine-learning-compute-cluster-for-training-as-is"></a>Extrair a imagem base do Docker para o cluster de computação do Machine Learning para treinamento como está

Crie o cluster de computação do Machine Learning com a identidade gerenciada atribuída pelo sistema habilitada conforme descrito anteriormente. Em seguida, determine a ID de entidade de segurança da identidade gerenciada.

```azurecli-interactive
az ml computetarget amlcompute identity show --name <cluster name> -w <workspace> -g <resource group>
```

Opcionalmente, você pode atualizar o cluster de computação para atribuir uma identidade gerenciada atribuída pelo usuário:

```azurecli-interactive
az ml computetarget amlcompute identity assign --name cpucluster \
-w $mlws -g $mlrg --identities <my-identity-id>
```

Para permitir que o cluster de computação Extraia as imagens base, conceda a função de ACRPull de identidade de serviço gerenciada no ACR privado

```azurecli-interactive
az role assignment create --assignee <principal ID> \
--role acrpull \
--scope "/subscriptions/<subscription ID>/resourceGroups/<private ACR resource group>/providers/Microsoft.ContainerRegistry/registries/<private ACR name>"
```

Por fim, ao enviar uma execução de treinamento, especifique o local da imagem base na [definição do ambiente](how-to-use-environments.md#use-existing-environments).

```python
from azureml.core import Environment
env = Environment(name="private-acr")
env.docker.base_image = "<ACR name>.azurecr.io/<base image repository>/<base image version>"
env.python.user_managed_dependencies = True
```

> [!IMPORTANT]
> Para garantir que a imagem base seja puxada diretamente para o recurso de computação, defina `user_managed_dependencies = True` e não especifique um Dockerfile. Caso contrário, Azure Machine Learning serviço tentará criar uma nova imagem do Docker e falhar, porque apenas o cluster de computação tem acesso para efetuar pull da imagem base do ACR.

### <a name="build-azure-machine-learning-managed-environment-into-base-image-from-private-acr-for-training-or-inference"></a>Crie Azure Machine Learning ambiente gerenciado na imagem base do ACR privado para treinamento ou inferência

Nesse cenário, Azure Machine Learning serviço cria o ambiente de treinamento ou inferência sobre uma imagem base que você fornece de um ACR privado. Como a tarefa de criação de imagem ocorre no ACR do espaço de trabalho usando tarefas ACR, você deve executar mais etapas para permitir o acesso.

1. Crie uma __identidade gerenciada atribuída pelo usuário__ e conceda a identidade ACRPull acesso ao __ACR privado__.  
1. Conceda à identidade __gerenciada atribuída pelo sistema__ do espaço de trabalho uma função de operador de identidade gerenciada na __identidade gerenciada atribuída pelo usuário__ da etapa anterior. Essa função permite que o espaço de trabalho atribua a identidade gerenciada atribuída pelo usuário à tarefa ACR para criar o ambiente gerenciado. 

    1. Obtenha a ID da entidade de segurança da identidade gerenciada atribuída pelo sistema do espaço de trabalho:

        ```azurecli-interactive
        az ml workspace show -w <workspace name> -g <resource group> --query identityPrincipalId
        ```

    1. Conceda a função de operador de identidade gerenciada:

        ```azurecli-interactive
        az role assignment create --assignee <principal ID> --role managedidentityoperator --scope <UAI resource ID>
        ```

        A ID de recurso UAI é a ID de recurso do Azure da identidade atribuída pelo usuário, no formato `/subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<UAI name>` .

1. Especifique o ACR externo e a ID do cliente da __identidade gerenciada atribuída pelo usuário__ em conexões de espaço de trabalho usando o [método Workspace.set_connection](/python/api/azureml-core/azureml.core.workspace.workspace#set-connection-name--category--target--authtype--value-):

    ```python
    workspace.set_connection(
        name="privateAcr", 
        category="ACR", 
        target = "<acr url>", 
        authType = "RegistryConnection", 
        value={"ResourceId": "<UAI resource id>", "ClientId": "<UAI client ID>"})
    ```

Quando a configuração for concluída, você poderá usar as imagens base do ACR privado ao criar ambientes para treinamento ou inferência. O trecho de código a seguir demonstra como especificar o ACR da imagem base e o nome da imagem em uma definição de ambiente:

```python
from azureml.core import Environment

env = Environment(name="my-env")
env.docker.base_image = "<acr url>/my-repo/my-image:latest"
```

Opcionalmente, você pode especificar a URL do recurso de identidade gerenciada e a ID do cliente na própria definição de ambiente usando [RegistryIdentity](/python/api/azureml-core/azureml.core.container_registry.registryidentity). Se você usar explicitamente a identidade do registro, ela substituirá todas as conexões de espaço de trabalho especificadas anteriormente:

```python
from azureml.core.container_registry import RegistryIdentity

identity = RegistryIdentity()
identity.resource_id= "<UAI resource ID>"
identity.client_id="<UAI client ID>”
env.docker.base_image_registry.registry_identity=identity
env.docker.base_image = "my-acr.azurecr.io/my-repo/my-image:latest"
```

## <a name="use-docker-images-for-inference"></a>Usar imagens do Docker para inferência

Depois de configurar o ACR sem o usuário administrador, conforme descrito anteriormente, você pode acessar as imagens do Docker para inferência sem chaves de administração do seu AKS (serviço kubernetes do Azure). Quando você cria ou anexa AKS ao espaço de trabalho, a entidade de serviço do cluster recebe automaticamente o acesso de ACRPull ao ACR do espaço de trabalho.

> [!NOTE]
> Se você colocar seu próprio cluster AKS, o cluster deverá ter a entidade de serviço habilitada em vez da identidade gerenciada.

## <a name="create-workspace-with-user-assigned-managed-identity"></a>Criar espaço de trabalho com identidade gerenciada atribuída pelo usuário

Ao criar o espaço de trabalho, você pode especificar uma identidade gerenciada atribuída pelo usuário que será usada para acessar os recursos associados: ACR, keyvault, armazenamento e insights do aplicativo.

Primeiro, [crie uma identidade gerenciada atribuída pelo usuário](/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli)e anote a ID de recurso do ARM da identidade gerenciada.

Em seguida, use CLI do Azure ou o SDK do Python para criar o espaço de trabalho. Ao usar a CLI, especifique a ID usando o `--primary-user-assigned-identity` parâmetro. Ao usar o SDK, use `primary_user_assigned_identity` . Veja a seguir exemplos de como usar o CLI do Azure e o Python para criar um novo espaço de trabalho usando estes parâmetros:

__CLI do Azure__

```azurecli-interactive
az ml workspace create -w <workspace name> -g <resource group> --primary-user-assigned-identity <managed identity ARM ID>
```

__Python__

```python
from azureml.core import Workspace

ws = Workspace.create(name="workspace name", 
    subscription_id="subscription id", 
    resource_group="resource group name",
    primary_user_assigned_identity="managed identity ARM ID")
```

Você também pode usar [um modelo ARM](https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-advanced) para criar um espaço de trabalho com identidade gerenciada atribuída pelo usuário.

> [!IMPORTANT]
> Se você colocar seus próprios recursos associados, em vez de fazer com que Azure Machine Learning o serviço os crie, você deve conceder as funções de identidade gerenciadas nesses recursos. Use o [modelo de ARM de atribuição de função](https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-dependencies-role-assignment) para fazer as atribuições.

Para um espaço de trabalho com (chaves gerenciadas pelo cliente para criptografia) [ https://docs.microsoft.com/azure/machine-learning/concept-data-encryption ], você pode passar uma identidade gerenciada atribuída pelo usuário para autenticar do armazenamento para Key Vault. Use o argumento __atribuído pelo usuário-identidade-para-CMK-Encryption__ (CLI) ou __user_assigned_identity_for_cmk_encryption__ (SDK) para passar a identidade gerenciada. Essa identidade gerenciada pode ser igual ou diferente da identidade gerenciada atribuída ao usuário primário do espaço de trabalho.

Se você tiver um espaço de trabalho existente, você poderá atualizá-lo de uma identidade gerenciada atribuída pelo usuário para o sistema usando o ```az ml workspace update``` comando da CLI ou o ```Workspace.update``` método do SDK do Python.


## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [segurança corporativa no Azure Machine Learning](concept-enterprise-security.md).
