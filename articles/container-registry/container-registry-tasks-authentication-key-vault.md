---
title: Autenticação externa da tarefa ACR
description: Configurar uma tarefa de registro de contêiner do Azure (tarefa ACR) para ler as credenciais do Hub do Docker armazenadas em um cofre de chaves do Azure, usando uma identidade gerenciada para recursos do Azure.
ms.topic: article
ms.date: 07/06/2020
ms.openlocfilehash: 0bc43f958a14016146160a06372af0b36a9fff75
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "86058122"
---
# <a name="external-authentication-in-an-acr-task-using-an-azure-managed-identity"></a>Autenticação externa em uma tarefa ACR usando uma identidade gerenciada pelo Azure 

Em uma [tarefa do ACR](container-registry-tasks-overview.md), você pode [habilitar uma identidade gerenciada para os recursos do Azure](container-registry-tasks-authentication-managed-identity.md). A tarefa pode usar a identidade para acessar outros recursos Azure, sem a necessidade de fornecer ou gerenciar credenciais. 

Neste artigo, você aprenderá a habilitar uma identidade gerenciada em uma tarefa que acessa os segredos armazenados em um cofre de chaves do Azure. 

Para criar os recursos do Azure, este artigo requer que você execute a versão 2.0.68 ou posterior da CLI do Azure. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure][azure-cli].

## <a name="scenario-overview"></a>Visão geral do cenário

A tarefa de exemplo lê as credenciais do Hub do Docker armazenadas em um cofre de chaves do Azure. As credenciais são para uma conta de Hub do Docker com permissões de gravação (push) para um repositório privado do Hub do Docker. Para ler as credenciais, configure a tarefa com uma identidade gerenciada e atribua as permissões apropriadas a ela. A tarefa associada à identidade cria uma imagem e entra no Hub do Docker para enviar a imagem por push para o repositório privado. 

Este exemplo mostra as etapas usando uma identidade gerenciada atribuída pelo usuário ou pelo sistema. A sua escolha de identidade depende das necessidades da sua organização.

Em um cenário do mundo real, uma empresa pode publicar imagens em um repositório privado no Hub do Docker como parte de um processo de compilação. 

## <a name="prerequisites"></a>Pré-requisitos

Você precisa de um registro de contêiner do Azure no qual executa a tarefa. Neste artigo, esse registro é nomeado *myregistry*. Substitua pelo seu próprio nome de registro em etapas posteriores.

Se você ainda não tiver um registro de contêiner do Azure, consulte [início rápido: criar um registro de contêiner privado usando o CLI do Azure](container-registry-get-started-azure-cli.md). Você ainda não precisa enviar imagens por push para o registro.

Você também precisa de um repositório privado no Hub do Docker e uma conta do Hub do Docker com permissões para gravar no repositório. Neste exemplo, esse repositório é denominado *hubuser/hubrepo*. 

## <a name="create-a-key-vault-and-store-secrets"></a>Criar um cofre de chaves e armazenar segredos

Primeiro, se você precisar, crie um grupo de recursos chamado *MyResource* Group na localização *lesteus* com o seguinte comando [AZ Group Create][az-group-create] :

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Use o comando [AZ keyvault Create][az-keyvault-create] para criar um cofre de chaves. Certifique-se de especificar um nome exclusivo do cofre de chaves. 

```azurecli-interactive
az keyvault create --name mykeyvault --resource-group myResourceGroup --location eastus
```

Armazene as credenciais do Hub do Docker necessárias no cofre de chaves usando o comando [AZ keyvault segredo Set][az-keyvault-secret-set] . Nesses comandos, os valores são passados em variáveis de ambiente:

```azurecli
# Store Docker Hub user name
az keyvault secret set \
  --name UserName \
  --value $USERNAME \
  --vault-name mykeyvault

# Store Docker Hub password
az keyvault secret set \
  --name Password \
  --value $PASSWORD \
  --vault-name mykeyvault
```

Em um cenário do mundo real, os segredos provavelmente seriam definidos e mantidos em um processo separado.

## <a name="define-task-steps-in-yaml-file"></a>Definir as etapas de tarefa no arquivo YAML

As etapas para esta tarefa de exemplo são definidas em um [arquivo YAML](container-registry-tasks-reference-yaml.md). Crie um arquivo chamado `dockerhubtask.yaml` em um diretório de trabalho local e cole o conteúdo a seguir. Certifique-se de substituir o nome do cofre de chaves no arquivo pelo nome do cofre de chaves.

```yml
version: v1.1.0
# Replace mykeyvault with the name of your key vault
secrets:
  - id: username
    keyvault: https://mykeyvault.vault.azure.net/secrets/UserName
  - id: password
    keyvault: https://mykeyvault.vault.azure.net/secrets/Password
steps:
# Log in to Docker Hub
  - cmd: bash echo '{{.Secrets.password}}' | docker login --username '{{.Secrets.username}}' --password-stdin 
# Build image
  - build: -t {{.Values.PrivateRepo}}:$ID https://github.com/Azure-Samples/acr-tasks.git -f hello-world.dockerfile
# Push image to private repo in Docker Hub
  - push:
    - {{.Values.PrivateRepo}}:$ID
```

As etapas da tarefa fazem o seguinte:

* Gerencie credenciais de segredo para autenticar com o Hub do Docker.
* Autentique com o Hub do Docker passando os segredos para o `docker login` comando.
* Crie uma imagem usando um exemplo de Dockerfile no repositório [Azure-Samples/ACR-Tasks](https://github.com/Azure-Samples/acr-tasks.git) .
* Envie a imagem por push para o repositório privado do Hub do Docker.


## <a name="option-1-create-task-with-user-assigned-identity"></a>Opção 1: Criar tarefa com identidade atribuída pelo usuário

As etapas nesta seção criam uma tarefa e habilitam uma identidade atribuída pelo usuário. Se você quiser habilitar uma identidade atribuída pelo sistema, consulte [Opção 2: Criar tarefa com identidade atribuída pelo usuário](#option-2-create-task-with-system-assigned-identity). 

[!INCLUDE [container-registry-tasks-user-assigned-id](../../includes/container-registry-tasks-user-assigned-id.md)]

### <a name="create-task"></a>Criar tarefa

Crie a tarefa *dockerhubtask* executando o comando [AZ ACR Task Create][az-acr-task-create] a seguir. A tarefa é executada sem um contexto de código-fonte, e o comando faz referência ao arquivo `dockerhubtask.yaml` no diretório de trabalho. O parâmetro `--assign-identity` passa a ID do recurso da identidade atribuída pelo usuário. 

```azurecli
az acr task create \
  --name dockerhubtask \
  --registry myregistry \
  --context /dev/null \
  --file dockerhubtask.yaml \
  --assign-identity $resourceID
```

[!INCLUDE [container-registry-tasks-user-id-properties](../../includes/container-registry-tasks-user-id-properties.md)]


### <a name="grant-identity-access-to-key-vault"></a>Conceder acesso de identidade ao cofre de chaves

Execute o seguinte comando [AZ keyvault Set-Policy][az-keyvault-set-policy] para definir uma política de acesso no cofre de chaves. O exemplo a seguir permite que a identidade Leia segredos do cofre de chaves. 

```azurecli
az keyvault set-policy --name mykeyvault \
  --resource-group myResourceGroup \
  --object-id $principalID \
  --secret-permissions get
```

Continue para [executar a tarefa manualmente](#manually-run-the-task).

## <a name="option-2-create-task-with-system-assigned-identity"></a>Opção 2: Criar tarefa com identidade atribuída pelo sistema

As etapas nesta seção criam uma tarefa e habilitam uma identidade atribuída pelo sistema. Se você quiser habilitar uma identidade atribuída pelo usuário, consulte [Opção 1: Criar tarefa com identidade atribuída pelo usuário](#option-1-create-task-with-user-assigned-identity). 

### <a name="create-task"></a>Criar tarefa

Crie a tarefa *dockerhubtask* executando o comando [AZ ACR Task Create][az-acr-task-create] a seguir. A tarefa é executada sem um contexto de código-fonte, e o comando faz referência ao arquivo `dockerhubtask.yaml` no diretório de trabalho. O parâmetro `--assign-identity` sem valor habilita a identidade atribuída pelo sistema na tarefa.  

```azurecli
az acr task create \
  --name dockerhubtask \
  --registry myregistry \
  --context /dev/null \
  --file dockerhubtask.yaml \
  --assign-identity 
```

[!INCLUDE [container-registry-tasks-system-id-properties](../../includes/container-registry-tasks-system-id-properties.md)]

### <a name="grant-identity-access-to-key-vault"></a>Conceder acesso de identidade ao cofre de chaves

Execute o seguinte comando [AZ keyvault Set-Policy][az-keyvault-set-policy] para definir uma política de acesso no cofre de chaves. O exemplo a seguir permite que a identidade Leia segredos do cofre de chaves. 

```azurecli
az keyvault set-policy --name mykeyvault \
  --resource-group myResourceGroup \
  --object-id $principalID \
  --secret-permissions get
```

## <a name="manually-run-the-task"></a>Executar a tarefa manualmente

Para verificar se a tarefa na qual você habilitou uma identidade gerenciada está sendo executada com sucesso, acione manualmente a tarefa com o comando [az acr task run][az-acr-task-run]. O `--set` parâmetro é usado para passar o nome do repositório privado para a tarefa. Neste exemplo, o nome do repositório de espaço reservado é *hubuser/hubrepo*.

```azurecli
az acr task run --name dockerhubtask --registry myregistry --set PrivateRepo=hubuser/hubrepo
```

Quando a tarefa é executada com êxito, a saída mostra a autenticação bem-sucedida para o Hub do Docker e a imagem é compilada e enviada com êxito para o repositório privado:

```console
Queued a run with ID: cf24
Waiting for an agent...
2019/06/20 18:05:55 Using acb_vol_b1edae11-30de-4f2b-a9c7-7d743e811101 as the home volume
2019/06/20 18:05:58 Creating Docker network: acb_default_network, driver: 'bridge'
2019/06/20 18:05:58 Successfully set up Docker network: acb_default_network
2019/06/20 18:05:58 Setting up Docker configuration...
2019/06/20 18:05:59 Successfully set up Docker configuration
2019/06/20 18:05:59 Logging in to registry: myregistry.azurecr.io
2019/06/20 18:06:00 Successfully logged into myregistry.azurecr.io
2019/06/20 18:06:00 Executing step ID: acb_step_0. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/20 18:06:00 Launching container with name: acb_step_0
[...]
Login Succeeded
2019/06/20 18:06:02 Successfully executed container: acb_step_0
2019/06/20 18:06:02 Executing step ID: acb_step_1. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/20 18:06:02 Scanning for dependencies...
2019/06/20 18:06:04 Successfully scanned dependencies
2019/06/20 18:06:04 Launching container with name: acb_step_1
Sending build context to Docker daemon    129kB
[...]
2019/06/20 18:06:07 Successfully pushed image: hubuser/hubrepo:cf24
2019/06/20 18:06:07 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 2.064353)
2019/06/20 18:06:07 Step ID: acb_step_1 marked as successful (elapsed time in seconds: 2.594061)
2019/06/20 18:06:07 Populating digests for step ID: acb_step_1...
2019/06/20 18:06:09 Successfully populated digests for step ID: acb_step_1
2019/06/20 18:06:09 Step ID: acb_step_2 marked as successful (elapsed time in seconds: 2.743923)
2019/06/20 18:06:09 The following dependencies were found:
2019/06/20 18:06:09
- image:
    registry: registry.hub.docker.com
    repository: hubuser/hubrepo
    tag: cf24
    digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/hello-world
    tag: latest
    digest: sha256:0e11c388b664df8a27a901dce21eb89f11d8292f7fca1b3e3c4321bf7897bffe
  git:
    git-head-revision: b0ffa6043dd893a4c75644c5fed384c82ebb5f9e

Run ID: cf24 was successful after 15s
```

Para confirmar se a imagem foi enviada por push, verifique a marca ( `cf24` neste exemplo) no repositório privado do Hub do Docker.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [habilitar uma identidade gerenciada em uma tarefa do ACR](container-registry-tasks-authentication-managed-identity.md).
* Consulte [a referência YAML das Tarefas do ACR](container-registry-tasks-reference-yaml.md)


<!-- LINKS - Internal -->
[az-login]: /cli/azure/reference-index#az-login
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-repository-show-tags]: /cli/azure/acr/repository#az-acr-repository-show-tags
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-identity-create]: /cli/azure/identity#az-identity-create
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
