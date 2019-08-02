---
title: Usar uma identidade gerenciada com Instâncias de Contêiner do Azure
description: Saiba como usar uma identidade gerenciada para autenticar com outros serviços do Azure em Instâncias de Contêiner do Azure.
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 10/22/2018
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: 773650e5e5e85d4a5fca0b3755f3730921cc5f2e
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68325935"
---
# <a name="how-to-use-managed-identities-with-azure-container-instances"></a>Como usar identidades gerenciadas com Instâncias de Contêiner do Azure

Use [identidades gerenciadas para recursos do Azure](../active-directory/managed-identities-azure-resources/overview.md) para executar o código em Instâncias de Contêiner do Azure que interagem com outros serviços do Azure - sem manter segredos ou credenciais no código. O recurso fornece uma implantação de Instâncias de Contêiner do Azure com uma identidade gerenciada automaticamente no Azure Active Directory.

Neste artigo, você aprenderá mais sobre identidades gerenciadas nas Instâncias de Contêiner do Azure e:

> [!div class="checklist"]
> * Habilitar uma identidade atribuída pelo usuário ou atribuída pelo sistema em um grupo de contêineres
> * Conceder à identidade o acesso ao Azure Key Vault
> * Usar a identidade gerenciada para acessar um Key Vault de um contêiner em execução

Adapte os exemplos para habilitar e usar identidades em Instâncias de Contêiner do Azure para acessar outros serviços do Azure. Esses exemplos são interativos. No entanto, na prática as imagens de contêiner executariam o código para acessar os serviços do Azure.

> [!NOTE]
> Atualmente, não é possível usar uma identidade gerenciada em um grupo de contêineres implantados em uma rede virtual.

## <a name="why-use-a-managed-identity"></a>Por que usar uma identidade gerenciada?

Use uma identidade gerenciada em um contêiner gerenciado para autenticar em qualquer serviço [ que dá suporte à autenticação do Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication), sem ter as credenciais no seu código de contêiner. Para serviços que não dão suporte à autenticação do AD, você pode armazenar segredos no Azure Key Vault e usar a identidade gerenciada para acessar o Key Vault para recuperar as credenciais. Para saber mais sobre como usar uma identidade gerenciada, consulte [O que são identidades gerenciadas para recursos do Azure?](../active-directory/managed-identities-azure-resources/overview.md)

> [!IMPORTANT]
> Esse recurso está atualmente na visualização. As versões prévias são disponibilizadas com a condição de que você concorde com os [termos de uso complementares](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Alguns aspectos desse recurso podem alterar antes da GA (disponibilidade geral). Atualmente, as identidades gerenciadas somente têm suporte em instâncias de contêiner do Linux.
>  

### <a name="enable-a-managed-identity"></a>Habilitar uma identidade gerenciada

 Em Instâncias de Contêiner do Azure, as identidades gerenciadas para recursos do Azure que têm suporte a partir da API REST versão 2018-10-01 e ferramentas e SDKs correspondentes. Quando você cria um grupo de contêineres, habilite uma ou mais identidades gerenciadas definindo uma propriedade [ContainerGroupIdentity](/rest/api/container-instances/containergroups/createorupdate#containergroupidentity). Você também pode habilitar ou atualizar identidades gerenciadas depois que um grupo de contêineres estiver em execução; qualquer ação faz o grupo de contêineres reiniciar. Para definir as identidades em um grupo de contêineres novos ou existentes, use a CLI do Azure, um modelo do Resource Manager ou um arquivo YAML. 

As instâncias de contêiner do Azure dão suporte a ambos os tipos de identidades gerenciada do Azure: atribuída pelo usuário e atribuída pelo sistema. Em um grupo de contêineres, você pode habilitar uma identidade atribuída pelo sistema, uma ou mais identidades atribuídas pelo usuário ou ambos os tipos de identidades. 

* Uma identidade gerenciada **atribuída pelo usuário** é criada como um recurso autônomo do Azure no locatário do Azure AD que é confiável pela assinatura em uso. Depois que a identidade é criada, ela poderá ser atribuída a um ou mais recursos do Azure (nas Instâncias de Contêiner do Azure ou outros serviços do Azure). O ciclo de vida de uma identidade atribuída pelo usuário é gerenciado separadamente do ciclo de vida dos grupos de contêiner ou outros recursos de serviço a que ela é atribuída. Esse comportamento é especialmente útil em Instâncias de Contêiner do Azure. Como a identidade estende-se além do tempo de vida de um grupo de contêineres, você poderá reutilizá-la junto com outras configurações padrão para fazer suas implantações do grupo de contêineres altamente repetitivo.

* Uma **identidade gerenciada atribuída pelo sistema** é habilitada diretamente em um grupo de contêineres nas Instâncias de Contêiner do Microsoft Azure. Quando ela está habilitada, o Azure cria uma identidade para o grupo do locatário do Azure AD confiado pela assinatura da instância. Depois que a identidade é criada, as credenciais são provisionadas em cada contêiner no grupo de contêineres. O ciclo de vida de uma identidade atribuída ao sistema está diretamente relacionado ao grupo de contêineres no qual ele está habilitado. Quando o grupo é excluído, o Azure limpa automaticamente as credenciais e a identidade no Azure AD.

### <a name="use-a-managed-identity"></a>Usar uma identidade gerenciada

Para usar uma identidade gerenciada, a identidade deve inicialmente receber acesso a um ou mais recursos de serviço do Azure (por exemplo, um aplicativo Web, um Key Vault ou uma Conta de Armazenamento) na assinatura. Para acessar os recursos do Azure de um contêiner em execução, seu código deverá adquirir um *token de acesso* de um ponto de extremidade do Azure AD. Seu código envia o token de acesso em uma chamada para um serviço que dá suporte à autenticação do Azure AD. 

Usar uma identidade gerenciada em um contêiner em execução é essencialmente o mesmo que usar uma identidade em uma VM do Azure. Consulte as diretrizes de VM para usar um [token](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md), [Azure PowerShell ou CLI do Azure](../active-directory/managed-identities-azure-resources/how-to-use-vm-sign-in.md) ou os [SDKs do Azure](../active-directory/managed-identities-azure-resources/how-to-use-vm-sdk.md).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, este artigo exigirá que esteja em execução a CLI do Azure versão 2.0.49 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

## <a name="create-an-azure-key-vault"></a>Criar um Cofre de chaves do Azure

Os exemplos neste artigo usam uma identidade gerenciada nas Instâncias de Contêiner do Azure para acessar um segredo do Azure Key Vault. 

Primeiramente, crie um grupo de recursos denominado *myResourceGroup* no local *eastus* com o seguinte comando [az group create](/cli/azure/group?view=azure-cli-latest#az-group-create):

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Use o comando [az keyvault create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) para criar um Key Vault. Especifique um nome exclusivo de Key Vault. 

```azurecli-interactive
az keyvault create --name mykeyvault --resource-group myResourceGroup --location eastus
```

Armazene um segredo de exemplo no Key Vault usando o comando [az keyvault secret set](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-set):

```azurecli-interactive
az keyvault secret set --name SampleSecret --value "Hello Container Instances!" --description ACIsecret  --vault-name mykeyvault
```

Continue com os exemplos a seguir para acessar o Key Vault usando uma identidade gerenciada atribuída pelo usuário ou atribuída pelo sistema nas Instâncias de Contêiner do Azure.

## <a name="example-1-use-a-user-assigned-identity-to-access-azure-key-vault"></a>Exemplo 1: Usar uma identidade atribuída pelo usuário para acessar o Azure Key Vault

### <a name="create-an-identity"></a>Criar uma identidade

Primeiro, crie uma identidade em sua assinatura usando o comando [az identity create](/cli/azure/identity?view=azure-cli-latest#az-identity-create). Você pode usar o mesmo grupo de recursos usado para criar o Key Vault ou usar um diferente.

```azurecli-interactive
az identity create --resource-group myResourceGroup --name myACIId
```

Para usar a identidade nas etapas a seguir, use o comando [az identity show](/cli/azure/identity?view=azure-cli-latest#az-identity-show) para armazenar a ID da entidade de serviço da identidade e a ID de recurso em variáveis.

```azurecli-interactive
# Get service principal ID of the user-assigned identity
spID=$(az identity show --resource-group myResourceGroup --name myACIId --query principalId --output tsv)

# Get resource ID of the user-assigned identity
resourceID=$(az identity show --resource-group myResourceGroup --name myACIId --query id --output tsv)
```

### <a name="enable-a-user-assigned-identity-on-a-container-group"></a>Habilitar uma identidade atribuída pelo usuário em um grupo de contêineres

Execute o seguinte comando [az container create](/cli/azure/container?view=azure-cli-latest#az-container-create) para criar uma instância de contêiner com base em um Ubuntu Server. Este exemplo fornece um grupo de contêiner único que você pode usar para acessar interativamente os outros serviços do Azure. O parâmetro `--assign-identity` passa sua identidade atribuída pelo usuário gerenciado para o grupo. O comando de execução demorada mantém o contêiner em execução. Este exemplo usa o mesmo grupo de recursos usado para criar o Key Vault, mas você pode especificar um diferente.

```azurecli-interactive
az container create --resource-group myResourceGroup --name mycontainer --image microsoft/azure-cli --assign-identity $resourceID --command-line "tail -f /dev/null"
```

Em poucos segundos, você obterá uma resposta da CLI do Azure indicando que a implantação foi concluída. Verifique o status dele usando o comando [az container show](/cli/azure/container?view=azure-cli-latest#az-container-show).

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer
```

A seção `identity` na saída é semelhante ao seguinte, mostrando que a identidade está definida no grupo de contêineres. O `principalID` em `userAssignedIdentities` é a entidade de serviço da identidade que você criou no Azure Active Directory:

```console
...
"identity": {
    "principalId": "null",
    "tenantId": "xxxxxxxx-f292-4e60-9122-xxxxxxxxxxxx",
    "type": "UserAssigned",
    "userAssignedIdentities": {
      "/subscriptions/xxxxxxxx-0903-4b79-a55a-xxxxxxxxxxxx/resourcegroups/danlep1018/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myACIId": {
        "clientId": "xxxxxxxx-5523-45fc-9f49-xxxxxxxxxxxx",
        "principalId": "xxxxxxxx-f25b-4895-b828-xxxxxxxxxxxx"
      }
    }
  },
...
```

### <a name="grant-user-assigned-identity-access-to-the-key-vault"></a>Conceder acesso de identidade atribuída pelo usuário para o Key Vault

Execute o seguinte comando [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest) para definir uma política de acesso no Key Vault. O exemplo a seguir permite que a identidade atribuída pelo usuário receba segredos do Key Vault:

```azurecli-interactive
 az keyvault set-policy --name mykeyvault --resource-group myResourceGroup --object-id $spID --secret-permissions get
```

### <a name="use-user-assigned-identity-to-get-secret-from-key-vault"></a>Usar a identidade atribuída pelo usuário para obter segredo do Key Vault

Agora você pode usar a identidade gerenciada para acessar o Key Vault dentro da instância de contêiner em execução. Neste exemplo, primeiro inicie um shell bash no contêiner:

```azurecli-interactive
az container exec --resource-group myResourceGroup --name mycontainer --exec-command "/bin/bash"
```

Execute os seguintes comandos no shell bash no contêiner. Para obter um token de acesso para usar o Azure Active Directory para autenticar para o Key Vault, execute o seguinte comando:

```bash
curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -H Metadata:true -s
```

Saída:

```bash
{"access_token":"xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9......xxxxxxxxxxxxxxxxx","refresh_token":"","expires_in":"28799","expires_on":"1539927532","not_before":"1539898432","resource":"https://vault.azure.net/","token_type":"Bearer"}
```

Para armazenar o token de acesso em uma variável para usar em comandos subsequentes para autenticar, execute o seguinte comando:

```bash
token=$(curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -H Metadata:true | jq -r '.access_token')

```

Agora, use o token de acesso para autenticar no Key Vault e ler um segredo. Substitua o nome do seu Key Vault na URL ( *https://mykeyvault.vault.azure.net/...* ):

```bash
curl https://mykeyvault.vault.azure.net/secrets/SampleSecret/?api-version=2016-10-01 -H "Authorization: Bearer $token"
```

A resposta se assemelha à seguinte, mostrando o segredo. No código, analise essa saída para obter o segredo. Em seguida, use o segredo em uma operação subsequente para acessar outro recurso do Azure.

```bash
{"value":"Hello Container Instances!","contentType":"ACIsecret","id":"https://mykeyvault.vault.azure.net/secrets/SampleSecret/xxxxxxxxxxxxxxxxxxxx","attributes":{"enabled":true,"created":1539965967,"updated":1539965967,"recoveryLevel":"Purgeable"},"tags":{"file-encoding":"utf-8"}}
```

## <a name="example-2-use-a-system-assigned-identity-to-access-azure-key-vault"></a>Exemplo 2: Usar uma identidade atribuída pelo sistema para acessar o Azure Key Vault

### <a name="enable-a-system-assigned-identity-on-a-container-group"></a>Habilitar uma identidade atribuída pelo sistema em um grupo de contêineres

Execute o seguinte comando [az container create](/cli/azure/container?view=azure-cli-latest#az-container-create) para criar uma instância de contêiner com base em um Ubuntu Server. Este exemplo fornece um grupo de contêiner único que você pode usar para acessar interativamente os outros serviços do Azure. O parâmetro `--assign-identity` sem valor adicional permite uma identidade gerenciada atribuída pelo sistema no grupo. O comando de execução demorada mantém o contêiner em execução. Este exemplo usa o mesmo grupo de recursos usado para criar o Key Vault, mas você pode especificar um diferente.

```azurecli-interactive
az container create --resource-group myResourceGroup --name mycontainer --image microsoft/azure-cli --assign-identity --command-line "tail -f /dev/null"
```

Em poucos segundos, você obterá uma resposta da CLI do Azure indicando que a implantação foi concluída. Verifique o status dele usando o comando [az container show](/cli/azure/container?view=azure-cli-latest#az-container-show).

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer
```

A seção `identity` na saída é semelhante ao seguinte, mostrando que uma identidade atribuída pelo sistema é criada no Azure Active Directory:

```console
...
"identity": {
    "principalId": "xxxxxxxx-528d-7083-b74c-xxxxxxxxxxxx",
    "tenantId": "xxxxxxxx-f292-4e60-9122-xxxxxxxxxxxx",
    "type": "SystemAssigned",
    "userAssignedIdentities": null
},
...
```

Definir uma variável como o valor de `principalId` (a ID de entidade de serviço) da identidade, para ser usada em etapas posteriores.

```azurecli-interactive
spID=$(az container show --resource-group myResourceGroup --name mycontainer --query identity.principalId --out tsv)
```

### <a name="grant-container-group-access-to-the-key-vault"></a>Conceder acesso do grupo de contêineres ao Key Vault

Execute o seguinte comando [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest) para definir uma política de acesso no Key Vault. O exemplo a seguir permite que a identidade gerenciada pelo sistema receba segredos do Key Vault:

```azurecli-interactive
 az keyvault set-policy --name mykeyvault --resource-group myResourceGroup --object-id $spID --secret-permissions get
```

### <a name="use-container-group-identity-to-get-secret-from-key-vault"></a>Usar a identidade de grupo de contêineres para obter segredo do Key Vault

Agora você pode usar a identidade gerenciada para acessar o Key Vault dentro da instância de contêiner em execução. Neste exemplo, primeiro inicie um shell bash no contêiner:

```azurecli-interactive
az container exec --resource-group myResourceGroup --name mycontainer --exec-command "/bin/bash"
```

Execute os seguintes comandos no shell bash no contêiner. Para obter um token de acesso para usar o Azure Active Directory para autenticar para o Key Vault, execute o seguinte comando:

```bash
curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net%2F' -H Metadata:true -s
```

Saída:

```bash
{"access_token":"xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9......xxxxxxxxxxxxxxxxx","refresh_token":"","expires_in":"28799","expires_on":"1539927532","not_before":"1539898432","resource":"https://vault.azure.net/","token_type":"Bearer"}
```

Para armazenar o token de acesso em uma variável para usar em comandos subsequentes para autenticar, execute o seguinte comando:

```bash
token=$(curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -H Metadata:true | jq -r '.access_token')

```

Agora, use o token de acesso para autenticar no Key Vault e ler um segredo. Certifique-se de substituir o nome do cofre de chaves na URL (*https:\//mykeyvault.Vault.Azure.net/...* ):

```bash
curl https://mykeyvault.vault.azure.net/secrets/SampleSecret/?api-version=2016-10-01 -H "Authorization: Bearer $token"
```

A resposta se assemelha à seguinte, mostrando o segredo. No código, analise essa saída para obter o segredo. Em seguida, use o segredo em uma operação subsequente para acessar outro recurso do Azure.

```bash
{"value":"Hello Container Instances!","contentType":"ACIsecret","id":"https://mykeyvault.vault.azure.net/secrets/SampleSecret/xxxxxxxxxxxxxxxxxxxx","attributes":{"enabled":true,"created":1539965967,"updated":1539965967,"recoveryLevel":"Purgeable"},"tags":{"file-encoding":"utf-8"}}
```

## <a name="enable-managed-identity-using-resource-manager-template"></a>Habilitar a identidade gerenciada usando o modelo do Resource Manager

Para habilitar uma identidade gerenciada em um grupo de contêiner usando um [modelo do Resource Manager](container-instances-multi-container-group.md), defina a propriedade `identity` do objeto `Microsoft.ContainerInstance/containerGroups` com um objeto `ContainerGroupIdentity`. Os trechos de código a seguir mostram a propriedade `identity` configurada para cenários diferentes. Defina a [referência de modelo do Resource Manager](/azure/templates/microsoft.containerinstance/containergroups). Especifique um `apiVersion` de `2018-10-01`.

### <a name="user-assigned-identity"></a>Identidade atribuída pelo usuário

Uma identidade atribuída pelo usuário é uma ID de recurso do formulário:

```
"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}"
``` 

Você pode habilitar uma ou mais identidades atribuídas pelo usuário.

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "myResourceID1": {
            }
        }
    }
```

### <a name="system-assigned-identity"></a>Identidade atribuída pelo sistema

```json
"identity": {
    "type": "SystemAssigned"
    }
```

### <a name="system--and-user-assigned-identities"></a>Identidades atribuídas pelo sistema e pelo usuário

Em um grupo de contêineres, você pode habilitar uma identidade atribuída pelo sistema e uma ou mais identidades atribuídas pelo usuário.

```json
"identity": {
    "type": "System Assigned, UserAssigned",
    "userAssignedIdentities": {
        "myResourceID1": {
            }
        }
    }
...
```

## <a name="enable-managed-identity-using-yaml-file"></a>Habilitar a identidade gerenciada usando o arquivo YAML

Para habilitar uma identidade gerenciada em um grupo de contêineres implantados usando um [arquivo YAML](container-instances-multi-container-yaml.md), inclua o YAML a seguir.
Especifique um `apiVersion` de `2018-10-01`.

### <a name="user-assigned-identity"></a>Identidade atribuída pelo usuário

Uma identidade atribuída pelo usuário é uma ID de recurso do formulário 

```
'/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}'
```

Você pode habilitar uma ou mais identidades atribuídas pelo usuário.

```YAML
identity:
  type: UserAssigned
  userAssignedIdentities:
    {'myResourceID1':{}}
```

### <a name="system-assigned-identity"></a>Identidade atribuída pelo sistema

```YAML
identity:
  type: SystemAssigned
```

### <a name="system--and-user-assigned-identities"></a>Identidades atribuídas pelo sistema e pelo usuário

Em um grupo de contêineres, você pode habilitar uma identidade atribuída pelo sistema e uma ou mais identidades atribuídas pelo usuário.

```YAML
identity:
  type: SystemAssigned, UserAssigned
  userAssignedIdentities:
   {'myResourceID1':{}}
```

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu sobre identidades gerenciadas nas Instâncias de Contêiner do Azure e como:

> [!div class="checklist"]
> * Habilitar uma identidade atribuída pelo usuário ou atribuída pelo sistema em um grupo de contêineres
> * Conceder à identidade o acesso ao Azure Key Vault
> * Usar a identidade gerenciada para acessar um Key Vault de um contêiner em execução

* Aprenda sobre [identidades gerenciadas dos recursos do Azure](/azure/active-directory/managed-identities-azure-resources/).

* Veja um [exemplo de SDK do Azure para Go](https://medium.com/@samkreter/c98911206328) de como usar uma identidade gerenciada para acessar um Key Vault de Instâncias de Contêiner do Azure.
