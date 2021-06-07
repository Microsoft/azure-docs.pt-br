---
title: Identidade gerenciada na tarefa ACR
description: Habilite uma identidade gerenciada para recursos do Azure em uma tarefa de registro de contêiner do Azure para permitir que a tarefa acesse outros recursos do Azure, incluindo outros registros de contêiner privado.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 01/14/2020
ms.author: danlep
ms.openlocfilehash: 8f2749a18a5ac6aed0822553d59beaacc9060228
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98915940"
---
# <a name="use-an-azure-managed-identity-in-acr-tasks"></a>Usar uma identidade gerenciada pelo Azure em tarefas ACR 

Habilite uma [identidade gerenciada para recursos do Azure](../active-directory/managed-identities-azure-resources/overview.md) em uma [tarefa ACR](container-registry-tasks-overview.md), para que a tarefa possa acessar outros recursos do Azure, sem a necessidade de fornecer ou gerenciar credenciais. Por exemplo, use uma identidade gerenciada para habilitar uma etapa de tarefa para efetuar pull ou enviar por push imagens de contêiner para outro registro.

Neste artigo, você aprenderá a usar o CLI do Azure para habilitar uma identidade gerenciada atribuída pelo usuário ou pelo sistema em uma tarefa ACR. Você pode usar o Azure Cloud Shell ou uma instalação local do CLI do Azure. Se você quiser usá-lo localmente, a versão 2.0.68 ou posterior será necessária. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure][azure-cli-install].

Para fins de ilustração, os comandos de exemplo neste artigo usam a [tarefa AZ ACR Create][az-acr-task-create] para criar uma tarefa de compilação de imagem básica que habilita uma identidade gerenciada. Para cenários de exemplo para acessar recursos protegidos de uma tarefa ACR usando uma identidade gerenciada, consulte:

* [Autenticação entre registros](container-registry-tasks-cross-registry-authentication.md)
* [Acesse recursos externos com segredos armazenados no Azure Key Vault](container-registry-tasks-authentication-key-vault.md)

## <a name="why-use-a-managed-identity"></a>Por que usar uma identidade gerenciada?

Uma identidade gerenciada para recursos do Azure fornece serviços do Azure selecionados com uma identidade gerenciada automaticamente no Azure Active Directory. Você pode configurar uma tarefa ACR com uma identidade gerenciada para que a tarefa possa acessar outros recursos protegidos do Azure, sem passar credenciais nas etapas da tarefa.

Identidades gerenciadas são de dois tipos:

* *Identidades atribuídas pelo usuário*, que você pode atribuir a vários recursos e manter o tempo que desejar. As identidades atribuídas pelo usuário estão atualmente em pré-visualização.

* Uma *identidade atribuída pelo sistema*, que é exclusiva para um recurso específico, como uma tarefa ACR, e dura o tempo de vida desse recurso.

Você pode habilitar um ou ambos os tipos de identidade em uma tarefa ACR. Conceda acesso de identidade a outro recurso, assim como qualquer entidade de segurança. Quando a tarefa é executada, ela usa a identidade para acessar o recurso em qualquer etapa de tarefa que exija acesso.

## <a name="steps-to-use-a-managed-identity"></a>Etapas para usar uma identidade gerenciada

Siga estas etapas de alto nível para usar uma identidade gerenciada com uma tarefa ACR.

### <a name="1-optional-create-a-user-assigned-identity"></a>1. (opcional) criar uma identidade atribuída pelo usuário

Se você planeja usar uma identidade atribuída pelo usuário, use uma identidade existente ou crie a identidade usando o CLI do Azure ou outras ferramentas do Azure. Por exemplo, use o comando [AZ Identity Create][az-identity-create] . 

Se você planeja usar apenas uma identidade atribuída pelo sistema, ignore esta etapa. Você cria uma identidade atribuída pelo sistema ao criar a tarefa ACR.

### <a name="2-enable-identity-on-an-acr-task"></a>2. habilitar a identidade em uma tarefa ACR

Ao criar uma tarefa ACR, opcionalmente, habilite uma identidade atribuída pelo usuário, uma identidade atribuída pelo sistema ou ambas. Por exemplo, passe o `--assign-identity` parâmetro ao executar o comando [AZ ACR Task create][az-acr-task-create] no CLI do Azure.

Para habilitar uma identidade atribuída pelo sistema, passe `--assign-identity` sem valor ou `assign-identity [system]` . O comando de exemplo a seguir cria uma tarefa do Linux de um repositório GitHub público que cria a `hello-world` imagem e habilita uma identidade gerenciada atribuída pelo sistema:

```azurecli
az acr task create \
    --image hello-world:{{.Run.ID}} \
    --name hello-world --registry MyRegistry \
    --context https://github.com/Azure-Samples/acr-build-helloworld-node.git#main \
    --file Dockerfile \
    --commit-trigger-enabled false \
    --assign-identity
```

Para habilitar uma identidade atribuída pelo usuário, passe `--assign-identity` com um valor da *ID de recurso* da identidade. O comando de exemplo a seguir cria uma tarefa do Linux de um repositório GitHub público que cria a `hello-world` imagem e habilita uma identidade gerenciada atribuída pelo usuário:

```azurecli
az acr task create \
    --image hello-world:{{.Run.ID}} \
    --name hello-world --registry MyRegistry \
    --context https://github.com/Azure-Samples/acr-build-helloworld-node.git#main \
    --file Dockerfile \
    --commit-trigger-enabled false
    --assign-identity <resourceID>
```

Você pode obter a ID de recurso da identidade executando o comando [AZ Identity show][az-identity-show] . A ID de recurso para a ID *myUserAssignedIdentity* no grupo de recursos *MyResource* Group está no formato: 

```
"/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myUserAssignedIdentity"
```

### <a name="3-grant-the-identity-permissions-to-access-other-azure-resources"></a>3. conceder as permissões de identidade para acessar outros recursos do Azure

Dependendo dos requisitos da sua tarefa, conceda permissões de identidade para acessar outros recursos do Azure. Os exemplos incluem:

* Atribua a identidade gerenciada uma função com pull, push e pull ou outras permissões a um registro de contêiner de destino no Azure. Para obter uma lista completa de funções de registro, consulte [funções e permissões do registro de contêiner do Azure](container-registry-roles.md). 
* Atribua à identidade gerenciada uma função para ler segredos em um cofre de chaves do Azure.

Use o [CLI do Azure](../role-based-access-control/role-assignments-cli.md) ou outras ferramentas do Azure para gerenciar o acesso baseado em função aos recursos. Por exemplo, execute o comando [AZ role Assignment Create][az-role-assignment-create] para atribuir a identidade uma função ao recurso. 

O exemplo a seguir atribui uma identidade gerenciada às permissões para efetuar pull de um registro de contêiner. O comando especifica a *ID da entidade de segurança* da tarefa e a *ID de recurso* do registro de destino.


```azurecli
az role assignment create \
  --assignee <principalID> \
  --scope <registryID> \
  --role acrpull
```

### <a name="4-optional-add-credentials-to-the-task"></a>4. (opcional) adicionar credenciais à tarefa

Se sua tarefa precisar de credenciais para efetuar pull ou enviar imagens por push para outro registro personalizado, ou para acessar outros recursos, adicione as credenciais à tarefa. Execute o comando [AZ ACR Task Credential Add][az-acr-task-credential-add] para adicionar credenciais e passe o `--use-identity` parâmetro para indicar que a identidade pode acessar as credenciais. 

Por exemplo, para adicionar credenciais para uma identidade atribuída pelo sistema para autenticar com o registro de contêiner do Azure *targetregistry*, passe `use-identity [system]` :

```azurecli
az acr task credential add \
    --name helloworld \
    --registry myregistry \
    --login-server targetregistry.azurecr.io \
    --use-identity [system]
```

Para adicionar credenciais para uma identidade atribuída pelo usuário para autenticar com o *targetregistry* do registro, passe `use-identity` com um valor da *ID do cliente* da identidade. Por exemplo:

```azurecli
az acr task credential add \
    --name helloworld \
    --registry myregistry \
    --login-server targetregistry.azurecr.io \
    --use-identity <clientID>
```

Você pode obter a ID do cliente da identidade executando o comando [AZ Identity show][az-identity-show] . A ID do cliente é um GUID do formulário `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` .

### <a name="5-run-the-task"></a>5. executar a tarefa

Depois de configurar uma tarefa com uma identidade gerenciada, execute a tarefa. Por exemplo, para testar uma das tarefas criadas neste artigo, acione-a manualmente usando o comando [AZ ACR Task execute][az-acr-task-run] . Se você tiver configurado gatilhos de tarefa automatizados adicionais, a tarefa será executada quando disparado automaticamente.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu como habilitar e usar uma identidade gerenciada atribuída pelo usuário ou pelo sistema em uma tarefa ACR. Para cenários para acessar recursos protegidos de uma tarefa ACR usando uma identidade gerenciada, consulte:

* [Autenticação entre registros](container-registry-tasks-cross-registry-authentication.md)
* [Acesse recursos externos com segredos armazenados no Azure Key Vault](container-registry-tasks-authentication-key-vault.md)


<!-- LINKS - Internal -->
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-identity-show]: /cli/azure/identity#az-identity-show
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az-acr-task-credential-add
[azure-cli-install]: /cli/azure/install-azure-cli
