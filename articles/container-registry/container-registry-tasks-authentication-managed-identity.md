---
title: Usar uma identidade gerenciada com tarefas do registro de contêiner do Azure
description: Forneça um acesso à tarefa de registro de contêiner do Azure para recursos do Azure, incluindo outros registros de contêiner privado atribuindo uma identidade gerenciada para recursos do Azure.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 06/12/2019
ms.author: danlep
ms.openlocfilehash: 46351af375ab4c6e59a3ddfba3c05c1e517fab0d
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68311526"
---
# <a name="use-an-azure-managed-identity-in-acr-tasks"></a>Usar uma identidade gerenciada do Azure em tarefas ACR 

Use uma [identidade gerenciada para recursos do Azure](../active-directory/managed-identities-azure-resources/overview.md) para autenticar de tarefas ACR para um registro de contêiner do Azure ou outros recursos do Azure, sem a necessidade de fornecer ou gerenciar credenciais no código. Por exemplo, use uma identidade gerenciada para efetuar pull ou enviar por push imagens de contêiner para outro registro como uma etapa em uma tarefa.

Neste artigo, você aprenderá mais sobre identidades gerenciadas e como:

> [!div class="checklist"]
> * Habilitar uma identidade atribuída pelo sistema ou uma identidade atribuída pelo usuário em uma tarefa ACR
> * Conceder a identidade acesso aos recursos do Azure, como outros registros de contêiner do Azure
> * Usar a identidade gerenciada para acessar os recursos de uma tarefa 

Para criar os recursos do Azure, este artigo requer que você execute o CLI do Azure versão 2.0.66 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure][azure-cli].

## <a name="why-use-a-managed-identity"></a>Por que usar uma identidade gerenciada?

Uma identidade gerenciada dos recursos do Azure fornece serviços do Azure com uma identidade gerenciada automaticamente no Azure Active Directory (Azure AD). Você pode configurar [determinados recursos do Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md), incluindo tarefas ACR, com uma identidade gerenciada. Em seguida, use a identidade para acessar outros recursos do Azure, sem passar credenciais no código ou scripts.

Identidades gerenciadas são de dois tipos:

* *Identidades atribuídas pelo usuário*, que você pode atribuir a vários recursos e persistir durante o tempo que desejar. As identidades atribuídas pelo usuário estão atualmente em pré-visualização.

* Uma *identidade gerenciada pelo sistema*, que é exclusiva para um recurso específico, como uma tarefa ACR, e dura o tempo de vida desse recurso.

Depois de configurar um recurso do Azure com uma identidade gerenciada, conceda o acesso de identidade a outro recurso, assim como qualquer entidade de segurança. Por exemplo, atribua uma identidade gerenciada a uma função com pull, push e pull ou outras permissões a um registro de contêiner privado no Azure. (Para obter uma lista completa de funções de registro, confira [Funções e permissões do Registro de Contêiner do Azure](container-registry-roles.md).) Você pode conceder à identidade acesso a um ou mais recursos.

## <a name="create-container-registries"></a>Criar registros de contêiner

Para este tutorial, você precisa de três registros de contêiner:

* Você usa o primeiro registro para criar e executar tarefas ACR. Neste artigo, esse registro de origem é denominado *myregistry*. 
* O segundo e o terceiro registros são registros de destino para a primeira tarefa de exemplo enviar por push uma imagem que ele cria. Neste artigo, os registros de destino são nomeados *customregistry1* e *customregistry2*.

Substitua pelos seus próprios nomes de registro em etapas posteriores.

Se você ainda não tiver os registros de contêiner do Azure necessários, [consulte início rápido: Criar um registro de contêiner privado usando a CLI do Azure](container-registry-get-started-azure-cli.md). Você ainda não precisa enviar imagens por push ao registro.

## <a name="example-task-with-a-system-assigned-identity"></a>Exemplo: Tarefa com uma identidade atribuída pelo sistema

Este exemplo mostra como criar uma tarefa de [várias etapas](container-registry-tasks-multi-step.md) com uma identidade atribuída pelo sistema. A tarefa cria uma imagem e, em seguida, usa a identidade para autenticar com dois registros de destino para enviar a imagem por push.

As etapas para esta tarefa de exemplo são definidas em um [arquivo YAML](container-registry-tasks-reference-yaml.md) chamado `testtask.yaml`. O arquivo está localizado no diretório multipleRegistries do repositório de exemplos do [ACR-Tasks](https://github.com/Azure-Samples/acr-tasks) . O arquivo é reproduzido aqui:

```yml
version: v1.0.0
steps:
  - build: -t {{.Values.REGISTRY1}}/hello-world:{{.Run.ID}} . -f hello-world.dockerfile
  - push: ["{{.Values.REGISTRY1}}/hello-world:{{.Run.ID}}"]
  - build: -t {{.Values.REGISTRY2}}/hello-world:{{.Run.ID}} . -f hello-world.dockerfile
  - push: ["{{.Values.REGISTRY2}}/hello-world:{{.Run.ID}}"]
```

### <a name="create-task-with-system-assigned-identity"></a>Criar tarefa com identidade atribuída pelo sistema

Crie a tarefa *Multiple-reg* executando o comando [AZ ACR Task Create][az-acr-task-create] a seguir. O contexto da tarefa é a pasta multipleRegistries do repositório de amostras e o comando faz referência ao `testtask.yaml` arquivo no repositório. O `--assign-identity` parâmetro sem nenhum valor adicional cria uma identidade atribuída pelo sistema para a tarefa. Essa tarefa é configurada para que você tenha que dispará-la manualmente, mas você pode configurá-la para ser executada quando as confirmações forem enviadas para o repositório ou se uma solicitação de pull for feita. 

```azurecli
az acr task create \
  --registry myregistry \
  --name multiple-reg \
  --context https://github.com/Azure-Samples/acr-tasks.git#:multipleRegistries \
  --file testtask.yaml \
  --commit-trigger-enabled false \
  --pull-request-trigger-enabled false \
  --assign-identity
```

Na saída do comando, a `identity` seção mostra que a identidade do `SystemAssigned` tipo está definida na tarefa. O `principalId` é a ID da entidade de serviço da identidade:

```console
[...]
  "identity": {
    "principalId": "xxxxxxxx-2703-42f9-97d0-xxxxxxxxxxxx",
    "tenantId": "xxxxxxxx-86f1-41af-91ab-xxxxxxxxxxxx",
    "type": "SystemAssigned",
    "userAssignedIdentities": null
  },
  "location": "eastus",
[...]
``` 

Use o comando [AZ ACR Task show][az-acr-task-show] para armazenar o `principalId` em uma variável, para usar em comandos posteriores:

```azurecli
principalID=$(az acr task show --name multiple-reg --registry myregistry --query identity.principalId --output tsv)
```

### <a name="give-identity-push-permissions-to-two-target-container-registries"></a>Conceder permissões de push de identidade para dois registros de contêiner de destino

Nesta seção, conceda permissões de identidade atribuídas pelo sistema para enviar por push para os dois registros de destino, chamados *customregistry1* e *customregistry2*.

Primeiro, use o comando [AZ ACR show][az-acr-show] para obter a ID de recurso de cada registro e armazenar as IDs em variáveis:

```azurecli
reg1_id=$(az acr show --name customregistry1 --query id --output tsv)
reg2_id=$(az acr show --name customregistry2 --query id --output tsv)
```

Use o comando [AZ role Assignment Create][az-role-assignment-create] para atribuir a identidade `acrpush` à função para cada registro. Essa função tem permissões para efetuar pull e enviar imagens por push a um registro de contêiner.

```azurecli
az role assignment create --assignee $principalID --scope $reg1_id --role acrpush
az role assignment create --assignee $principalID --scope $reg2_id --role acrpush
```

### <a name="add-target-registry-credentials-to-task"></a>Adicionar credenciais do registro de destino à tarefa

Agora, use o comando [AZ ACR Task Credential Add][az-acr-task-credential-add] para adicionar as credenciais da identidade à tarefa para que ela possa ser autenticada com os registros de destino.

```azurecli
az acr task credential add \
  --name multiple-reg \
  --registry myregistry \
  --login-server customregistry1.azurecr.io \
  --use-identity [system]

az acr task credential add \
  --name multiple-reg \
  --registry myregistry \
  --login-server customregistry2.azurecr.io \
  --use-identity [system]
```

### <a name="manually-run-the-task"></a>Executar a tarefa manualmente

Use o comando [AZ ACR Task execute][az-acr-task-run] para disparar a tarefa manualmente. O `--set` parâmetro é usado para passar os nomes do servidor de logon dos dois registros de destino como valores para as variáveis `REGISTRY1` de tarefa `REGISTRY2`e.

```azurecli
az acr task run \
  --name multiple-reg \
  --registry myregistry \
  --set REGISTRY1=customregistry1.azurecr.io \
  --set REGISTRY2=customregistry2.azurecr.io
```

A saída é semelhante a:

```console
Sending build context to Docker daemon  4.096kB
Step 1/1 : FROM hello-world
 ---> fce289e99eb9
Successfully built fce289e99eb9
Successfully tagged customregistry2.azurecr.io/hello-world:cf31
2019/05/31 22:16:02 Successfully executed container: acb_step_0
2019/05/31 22:16:02 Executing step ID: acb_step_1. Timeout(sec): 600, Working directory: 'multipleRegistries', Network: 'acb_default_network'
2019/05/31 22:16:02 Pushing image: customregistry2.azurecr.io/hello-world:cf31, attempt 1
The push refers to repository [customregistry2.azurecr.io/hello-world]
af0b15c8625b: Preparing
af0b15c8625b: Pushed
cf31: digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a size: 524
2019/05/31 22:16:08 Successfully pushed image: customregistry2.azurecr.io/hello-world:cf31
2019/05/31 22:16:08 Executing step ID: acb_step_2. Timeout(sec): 600, Working directory: 'multipleRegistries', Network: 'acb_default_network'
2019/05/31 22:16:08 Scanning for dependencies...
2019/05/31 22:16:08 Successfully scanned dependencies
2019/05/31 22:16:08 Launching container with name: acb_step_2
Sending build context to Docker daemon  4.096kB
Step 1/1 : FROM hello-world
 ---> fce289e99eb9
Successfully built fce289e99eb9
Successfully tagged customregistry1.azurecr.io/hello-world:cf31
2019/05/31 22:16:09 Successfully executed container: acb_step_2
2019/05/31 22:16:09 Executing step ID: acb_step_3. Timeout(sec): 600, Working directory: 'multipleRegistries', Network: 'acb_default_network'
2019/05/31 22:16:09 Pushing image: customregistry1.azurecr.io/hello-world:cf31, attempt 1
The push refers to repository [customregistry1.azurecr.io/hello-world]
af0b15c8625b: Preparing
af0b15c8625b: Pushed
cf31: digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a size: 524
2019/05/31 22:16:21 Successfully pushed image: customregistry1.azurecr.io/hello-world:cf31
2019/05/31 22:16:21 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 1.985291)
2019/05/31 22:16:21 Populating digests for step ID: acb_step_0...
2019/05/31 22:16:22 Successfully populated digests for step ID: acb_step_0
2019/05/31 22:16:22 Step ID: acb_step_1 marked as successful (elapsed time in seconds: 5.743225)
2019/05/31 22:16:22 Step ID: acb_step_2 marked as successful (elapsed time in seconds: 1.925959)
2019/05/31 22:16:22 Populating digests for step ID: acb_step_2...
2019/05/31 22:16:24 Successfully populated digests for step ID: acb_step_2
2019/05/31 22:16:24 Step ID: acb_step_3 marked as successful (elapsed time in seconds: 11.061057)
2019/05/31 22:16:24 The following dependencies were found:
2019/05/31 22:16:24
- image:
    registry: customregistry2.azurecr.io
    repository: hello-world
    tag: cf31
    digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/hello-world
    tag: latest
    digest: sha256:6f744a2005b12a704d2608d8070a494ad1145636eeb74a570c56b94d94ccdbfc
  git:
    git-head-revision: 05275dca2bc61f584085ca913c39d509236f576b
- image:
    registry: customregistry1.azurecr.io
    repository: hello-world
    tag: cf31
    digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/hello-world
    tag: latest
    digest: sha256:6f744a2005b12a704d2608d8070a494ad1145636eeb74a570c56b94d94ccdbfc
  git:
    git-head-revision: 05275dca2bc61f584085ca913c39d509236f576b

Run ID: cf31 was successful after 35s
```

## <a name="example-task-with-a-user-assigned-identity"></a>Exemplo: Tarefa com uma identidade atribuída pelo usuário

Neste exemplo, você cria uma identidade atribuída pelo usuário com permissões para ler segredos de um cofre de chaves do Azure. Você atribui essa identidade a uma tarefa de várias etapas que lê o segredo, cria uma imagem e entra na CLI do Azure para ler a marca de imagem.

### <a name="create-a-key-vault-and-store-a-secret"></a>Criar um cofre de chaves e armazenar um segredo

Primeiro, se você precisar, crie um grupo de recursos chamado *MyResource* Group na localização *lesteus* com o seguinte comando [AZ Group Create][az-group-create] :

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Use o comando [AZ keyvault Create][az-keyvault-create] para criar um cofre de chaves. Certifique-se de especificar um nome exclusivo do cofre de chaves. 

```azurecli-interactive
az keyvault create --name mykeyvault --resource-group myResourceGroup --location eastus
```

Armazene um segredo de exemplo no cofre de chaves usando o comando [AZ keyvault segredo Set][az-keyvault-secret-set] :

```azurecli
az keyvault secret set \
  --name SampleSecret \
  --value "Hello ACR Tasks!" \
  --description ACRTasksecret  \
  --vault-name mykeyvault
```

Por exemplo, talvez você queira armazenar credenciais para autenticar com um registro do Docker privado para poder efetuar pull de uma imagem privada.

### <a name="create-an-identity"></a>Criar uma identidade

Crie uma identidade denominada *myACRTasksId* em sua assinatura usando o comando [AZ Identity Create][az-identity-create] . Você pode usar o mesmo grupo de recursos que usou anteriormente para criar um registro de contêiner ou um cofre de chaves, ou um diferente.

```azurecli-interactive
az identity create --resource-group myResourceGroup --name myACRTasksId
```

Para configurar a identidade nas etapas a seguir, use o comando [AZ Identity show][az-identity-show] para armazenar a ID de recurso da identidade e a ID da entidade de serviço em variáveis.

```azurecli
# Get resource ID of the user-assigned identity
resourceID=$(az identity show --resource-group myResourceGroup --name myACRTasksId --query id --output tsv)

# Get service principal ID of the user-assigned identity
principalID=$(az identity show --resource-group myResourceGroup --name myACRTasksId --query principalId --output tsv)
```

### <a name="grant-identity-access-to-keyvault-to-read-secret"></a>Conceder acesso de identidade ao keyvault para ler o segredo

Execute o seguinte comando [AZ keyvault Set-Policy][az-keyvault-set-policy] para definir uma política de acesso no cofre de chaves. O exemplo a seguir permite que a identidade atribuída pelo usuário obtenha segredos do cofre de chaves. Esse acesso é necessário posteriormente para executar uma tarefa de várias etapas com êxito.

```azurecli-interactive
 az keyvault set-policy --name mykeyvault --resource-group myResourceGroup --object-id $principalID --secret-permissions get
```

### <a name="grant-identity-reader-access-to-the-resource-group-for-registry"></a>Conceder acesso ao leitor de identidade para o grupo de recursos para o registro

Execute o comando [AZ role Assignment Create][az-role-assignment-create] a seguir para atribuir a identidade uma função de leitor, nesse caso, ao grupo de recursos que contém o registro de origem. Essa função é necessária posteriormente para executar uma tarefa de várias etapas com êxito.

```azurecli
az role assignment create --role reader --resource-group myResourceGroup --assignee $principalID
```

### <a name="create-task-with-user-assigned-identity"></a>Criar tarefa com identidade atribuída pelo usuário

Agora, crie uma [tarefa de várias etapas](container-registry-tasks-multi-step.md) e atribua a ela a identidade atribuída pelo usuário. Para esta tarefa de exemplo, crie um [arquivo YAML](container-registry-tasks-reference-yaml.md) chamado `managed-identities.yaml` em um diretório de trabalho local e cole o conteúdo a seguir. Certifique-se de substituir o nome do cofre de chaves no arquivo pelo nome do cofre de chaves

```yml
version: v1.0.0
# Replace mykeyvault with the name of your key vault
secrets:
  - id: name
    keyvault: https://mykeyvault.vault.azure.net/secrets/SampleSecret
steps:
  # Verify that the task can access the secret in the key vault
  - cmd: bash -c 'if [ -z "$MY_SECRET" ]; then echo "Secret not resolved"; else echo "Secret resolved"; fi'
    env: 
      - MY_SECRET='{{.Secrets.name}}' 

  # Build/push the website image to source registry, using dockerfile in the Azure-Samples repo
  - cmd: docker build -t {{.Run.Registry}}/my-website:{{.Run.ID}} https://github.com/Azure-Samples/aci-helloworld.git
  - push: 
    - "{{.Run.Registry}}/my-website:{{.Run.ID}}"
  
  # Login to Azure CLI with identity and list the tags to verify that the image is in the registry
  - cmd: microsoft/azure-cli az login --identity
  - cmd: microsoft/azure-cli az acr repository show-tags -n {{.Values.registryName}} --repository my-website
```

Essa tarefa faz o seguinte:

* Verifica se ele pode acessar o segredo em seu cofre de chaves. Esta etapa é para fins de demonstração. Em um cenário do mundo real, você pode precisar de uma etapa de tarefa para obter credenciais para acessar um repositório privado do Hub do Docker.
* Compila e envia a `mywebsite` imagem para o registro de origem.
* Faz logon no CLI do Azure para listar `my-website` as marcas de imagem no registro de origem.

Crie uma tarefa chamada *msitask* e passe a ID de recurso da identidade atribuída pelo usuário que você criou anteriormente. Esta tarefa de exemplo é criada a `managed-identities.yaml` partir do arquivo que você salvou em seu diretório de trabalho local, portanto, você precisa dispará-lo manualmente.

```azurecli
az acr task create \
  --name msitask \
  --registry myregistry \
  --context /dev/null \
  --file managed-identities.yaml \
  --pull-request-trigger-enabled false \
  --commit-trigger-enabled false \
  --assign-identity $resourceID
```

Na saída do comando, a `identity` seção mostra que a identidade do `UserAssigned` tipo está definida na tarefa. O `principalId` é a ID da entidade de serviço da identidade:

```console
[...]
"identity": {
    "principalId": null,
    "tenantId": null,
    "type": "UserAssigned",
    "userAssignedIdentities": {
      "/subscriptions/xxxxxxxx-d12e-4760-9ab6-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myACRTasksId": {
        "clientId": "xxxxxxxx-f17e-4768-bb4e-xxxxxxxxxxxx",
        "principalId": "xxxxxxxx-1335-433d-bb6c-xxxxxxxxxxxx"
      }
[...]
```

### <a name="manually-run-the-task"></a>Executar a tarefa manualmente

Use o comando [AZ ACR Task execute][az-acr-task-run] para disparar a tarefa manualmente. O `--set` parâmetro é usado para passar o nome do registro de origem para a tarefa:

```azurecli
az acr task run --name msitask --registry myregistry --set registryName=myregistry  
```

A saída mostra que o segredo é resolvido, a imagem é compilada e enviada com êxito e a tarefa faz logon no CLI do Azure com a identidade para ler a marca de imagem do registro de origem:

```console
Queued a run with ID: cf32
Waiting for an agent...
2019/06/10 23:25:37 Downloading source code...
2019/06/10 23:25:38 Finished downloading source code
2019/06/10 23:25:39 Using acb_vol_4e4a0a7c-b6ef-4ec5-b40f-3436fc5eb0f5 as the home volume
2019/06/10 23:25:41 Creating Docker network: acb_default_network, driver: 'bridge'
2019/06/10 23:25:41 Successfully set up Docker network: acb_default_network
2019/06/10 23:25:41 Setting up Docker configuration...
2019/06/10 23:25:42 Successfully set up Docker configuration
2019/06/10 23:25:42 Logging in to registry: myregistry.azurecr.io
2019/06/10 23:25:43 Successfully logged into myregistry.azurecr.io
2019/06/10 23:25:43 Executing step ID: acb_step_0. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/10 23:25:43 Launching container with name: acb_step_0
Secret resolved
2019/06/10 23:25:44 Successfully executed container: acb_step_0
2019/06/10 23:25:44 Executing step ID: acb_step_1. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/10 23:25:44 Launching container with name: acb_step_1
Sending build context to Docker daemon  74.75kB

[...]

cf32: digest: sha256:cbb4aa83b33f6959d83e84bfd43ca901084966a9f91c42f111766473dc977f36 size: 1577
2019/06/10 23:26:05 Successfully pushed image: myregistry.azurecr.io/my-website:cf32
2019/06/10 23:26:05 Executing step ID: acb_step_3. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/10 23:26:05 Launching container with name: acb_step_3
[
  {
    "environmentName": "AzureCloud",
    "id": "xxxxxxxx-d12e-4760-9ab6-xxxxxxxxxxxx",
    "isDefault": true,
    "name": "DanLep Internal Consumption",
    "state": "Enabled",
    "tenantId": "xxxxxxxx-86f1-41af-91ab-xxxxxxxxxxxx",
      "user": {
      "assignedIdentityInfo": "MSI",
      "name": "systemAssignedIdentity",
      "type": "servicePrincipal"
    }
  }
]
2019/06/10 23:26:09 Successfully executed container: acb_step_3
2019/06/10 23:26:09 Executing step ID: acb_step_4. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/10 23:26:09 Launching container with name: acb_step_4
[
  "cf32"
]
2019/06/10 23:26:14 Successfully executed container: acb_step_4
2019/06/10 23:26:14 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 1.025312)
2019/06/10 23:26:14 Step ID: acb_step_1 marked as successful (elapsed time in seconds: 13.703823)
2019/06/10 23:26:14 Step ID: acb_step_2 marked as successful (elapsed time in seconds: 6.791506)
2019/06/10 23:26:14 Step ID: acb_step_3 marked as successful (elapsed time in seconds: 3.852972)
2019/06/10 23:26:14 Step ID: acb_step_4 marked as successful (elapsed time in seconds: 5.079079)
```


## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu sobre o uso de identidades gerenciadas com tarefas de registro de contêiner do Azure e como:

> [!div class="checklist"]
> * Habilitar uma identidade atribuída pelo sistema ou atribuída pelo usuário em uma tarefa ACR
> * Conceder a identidade acesso aos recursos do Azure, como outros registros de contêiner do Azure
> * Usar a identidade gerenciada para acessar os recursos de uma tarefa  

* Aprenda sobre [identidades gerenciadas dos recursos do Azure](/azure/active-directory/managed-identities-azure-resources/).

<!-- LINKS - Internal -->
[az-login]: /cli/azure/reference-index#az-login
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-identity-create]: /cli/azure/identity?view=azure-cli-latest#az-identity-create
[az-identity-show]: /cli/azure/identity#az-identity-show
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-show]: /cli/azure/acr/task#az-acr-task-show
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-list-runs]: /cli/azure/acr/task#az-acr-task-list-runs
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az-acr-task-credential-add
[az-group-create]: /cli/azure/group?#az-group-create
[az-keyvault-create]: /cli/azure/keyvault?#az-keyvault-create
[az-keyvault-secret-set]: /cli/azure/keyvault/secret#az-keyvault-secret-set
[az-keyvault-set-policy]: /cli/azure/keyvault#az-keyvault-set-policy
