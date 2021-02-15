---
title: Habilitar identidade gerenciada no grupo de contêineres
description: Saiba como habilitar uma identidade gerenciada em instâncias de contêiner do Azure que podem ser autenticadas com outros serviços do Azure
ms.topic: article
ms.date: 07/02/2020
ms.openlocfilehash: 67ef17b77a9db92e539dd860a3083760fe1160db
ms.sourcegitcommit: 65a4f2a297639811426a4f27c918ac8b10750d81
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/03/2020
ms.locfileid: "96558939"
---
# <a name="how-to-use-managed-identities-with-azure-container-instances"></a>Como usar identidades gerenciadas com Instâncias de Contêiner do Azure

Use [identidades gerenciadas para recursos do Azure](../active-directory/managed-identities-azure-resources/overview.md) para executar o código em Instâncias de Contêiner do Azure que interagem com outros serviços do Azure - sem manter segredos ou credenciais no código. O recurso fornece uma implantação de Instâncias de Contêiner do Azure com uma identidade gerenciada automaticamente no Azure Active Directory.

Neste artigo, você aprenderá mais sobre identidades gerenciadas nas Instâncias de Contêiner do Azure e:

> [!div class="checklist"]
> * Habilitar uma identidade atribuída pelo usuário ou atribuída pelo sistema em um grupo de contêineres
> * Conceder acesso de identidade a um cofre de chaves do Azure
> * Usar a identidade gerenciada para acessar um cofre de chaves de um contêiner em execução

Adapte os exemplos para habilitar e usar identidades em Instâncias de Contêiner do Azure para acessar outros serviços do Azure. Esses exemplos são interativos. No entanto, na prática as imagens de contêiner executariam o código para acessar os serviços do Azure.
 
> [!IMPORTANT]
> Esse recurso está atualmente na visualização. As versões prévias são disponibilizadas com a condição de que você concorde com os [termos de uso complementares](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Alguns aspectos desse recurso podem alterar antes da GA (disponibilidade geral). Atualmente, as identidades gerenciadas nas instâncias de contêiner do Azure só têm suporte com contêineres do Linux e ainda não com contêineres do Windows.

## <a name="why-use-a-managed-identity"></a>Por que usar uma identidade gerenciada?

Use uma identidade gerenciada em um contêiner gerenciado para autenticar em qualquer serviço [ que dá suporte à autenticação do Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication), sem ter as credenciais no seu código de contêiner. Para serviços que não dão suporte à autenticação do AD, você pode armazenar segredos em um cofre de chaves do Azure e usar a identidade gerenciada para acessar o cofre de chaves para recuperar credenciais. Para saber mais sobre como usar uma identidade gerenciada, consulte [O que são identidades gerenciadas para recursos do Azure?](../active-directory/managed-identities-azure-resources/overview.md)

### <a name="enable-a-managed-identity"></a>Habilitar uma identidade gerenciada

 Quando você cria um grupo de contêineres, habilite uma ou mais identidades gerenciadas definindo uma propriedade [ContainerGroupIdentity](/rest/api/container-instances/containergroups/createorupdate#containergroupidentity). Você também pode habilitar ou atualizar identidades gerenciadas depois que um grupo de contêineres está em execução-qualquer ação faz com que o grupo de contêiners seja reiniciado. Para definir as identidades em um grupo de contêiner novo ou existente, use o CLI do Azure, um modelo do Resource Manager, um arquivo YAML ou outra ferramenta do Azure. 

As instâncias de contêiner do Azure dão suporte a ambos os tipos de identidades gerenciada do Azure: atribuída pelo usuário e atribuída pelo sistema. Em um grupo de contêineres, você pode habilitar uma identidade atribuída pelo sistema, uma ou mais identidades atribuídas pelo usuário ou ambos os tipos de identidades. Se você não estiver familiarizado com identidades gerenciadas para recursos do Azure, consulte a [visão geral](../active-directory/managed-identities-azure-resources/overview.md).

### <a name="use-a-managed-identity"></a>Usar uma identidade gerenciada

Para usar uma identidade gerenciada, a identidade deve receber acesso a um ou mais recursos de serviço do Azure (como um aplicativo Web, um cofre de chaves ou uma conta de armazenamento) na assinatura. O uso de uma identidade gerenciada em um contêiner em execução é semelhante ao uso de uma identidade em uma VM do Azure. Consulte as diretrizes de VM para usar um [token](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md), [Azure PowerShell ou CLI do Azure](../active-directory/managed-identities-azure-resources/how-to-use-vm-sign-in.md) ou os [SDKs do Azure](../active-directory/managed-identities-azure-resources/how-to-use-vm-sdk.md).

### <a name="limitations"></a>Limitações

* No momento, você não pode usar uma identidade gerenciada em um grupo de contêineres implantado em uma rede virtual.
* Você não pode usar uma identidade gerenciada para efetuar pull de uma imagem do registro de contêiner do Azure ao criar um grupo de contêineres. A identidade está disponível somente dentro de um contêiner em execução.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Este artigo requer a versão 2.0.49 ou posterior do CLI do Azure. Se você está usando o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="create-an-azure-key-vault"></a>Criar um cofre de chaves do Azure

Os exemplos neste artigo usam uma identidade gerenciada em instâncias de contêiner do Azure para acessar um segredo do Azure Key Vault. 

Primeiramente, crie um grupo de recursos denominado *myResourceGroup* no local *eastus* com o seguinte comando [az group create](/cli/azure/group?view=azure-cli-latest#az-group-create):

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Use o comando [AZ keyvault Create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) para criar um cofre de chaves. Certifique-se de especificar um nome exclusivo do cofre de chaves. 

```azurecli-interactive
az keyvault create \
  --name mykeyvault \
  --resource-group myResourceGroup \ 
  --location eastus
```

Armazene um segredo de exemplo no cofre de chaves usando o comando [AZ keyvault segredo Set](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-set) :

```azurecli-interactive
az keyvault secret set \
  --name SampleSecret \
  --value "Hello Container Instances" \
  --description ACIsecret --vault-name mykeyvault
```

Continue com os exemplos a seguir para acessar o cofre de chaves usando uma identidade gerenciada atribuída pelo usuário ou pelo sistema em instâncias de contêiner do Azure.

## <a name="example-1-use-a-user-assigned-identity-to-access-azure-key-vault"></a>Exemplo 1: usar uma identidade atribuída pelo usuário para acessar o Azure Key Vault

### <a name="create-an-identity"></a>Criar uma identidade

Primeiro, crie uma identidade em sua assinatura usando o comando [az identity create](/cli/azure/identity?view=azure-cli-latest#az-identity-create). Você pode usar o mesmo grupo de recursos usado para criar o cofre de chaves ou usar um diferente.

```azurecli-interactive
az identity create \
  --resource-group myResourceGroup \
  --name myACIId
```

Para usar a identidade nas etapas a seguir, use o comando [az identity show](/cli/azure/identity?view=azure-cli-latest#az-identity-show) para armazenar a ID da entidade de serviço da identidade e a ID de recurso em variáveis.

```azurecli-interactive
# Get service principal ID of the user-assigned identity
spID=$(az identity show \
  --resource-group myResourceGroup \
  --name myACIId \
  --query principalId --output tsv)

# Get resource ID of the user-assigned identity
resourceID=$(az identity show \
  --resource-group myResourceGroup \
  --name myACIId \
  --query id --output tsv)
```

### <a name="grant-user-assigned-identity-access-to-the-key-vault"></a>Conceder acesso de identidade atribuído pelo usuário ao cofre de chaves

Execute o seguinte comando [AZ keyvault Set-Policy](/cli/azure/keyvault?view=azure-cli-latest) para definir uma política de acesso no cofre de chaves. O exemplo a seguir permite que a identidade atribuída pelo usuário obtenha segredos do cofre de chaves:

```azurecli-interactive
 az keyvault set-policy \
    --name mykeyvault \
    --resource-group myResourceGroup \
    --object-id $spID \
    --secret-permissions get
```

### <a name="enable-user-assigned-identity-on-a-container-group"></a>Habilitar identidade atribuída pelo usuário em um grupo de contêineres

Execute o comando [AZ container Create](/cli/azure/container?view=azure-cli-latest#az-container-create) a seguir para criar uma instância de contêiner com base na imagem da Microsoft `azure-cli` . Este exemplo fornece um grupo de contêiner único que você pode usar interativamente para executar o CLI do Azure para acessar outros serviços do Azure. Nesta seção, apenas o sistema operacional base é usado. Para obter um exemplo de como usar o CLI do Azure no contêiner, consulte [habilitar a identidade atribuída pelo sistema em um grupo de contêineres](#enable-system-assigned-identity-on-a-container-group). 

O parâmetro `--assign-identity` passa sua identidade atribuída pelo usuário gerenciado para o grupo. O comando de execução demorada mantém o contêiner em execução. Este exemplo usa o mesmo grupo de recursos usado para criar o cofre de chaves, mas você pode especificar um diferente.

```azurecli-interactive
az container create \
  --resource-group myResourceGroup \
  --name mycontainer \
  --image mcr.microsoft.com/azure-cli \
  --assign-identity $resourceID \
  --command-line "tail -f /dev/null"
```

Em poucos segundos, você obterá uma resposta da CLI do Azure indicando que a implantação foi concluída. Verifique seu status com o comando [AZ container show](/cli/azure/container?view=azure-cli-latest#az-container-show) .

```azurecli-interactive
az container show \
  --resource-group myResourceGroup \
  --name mycontainer
```

A seção `identity` na saída é semelhante ao seguinte, mostrando que a identidade está definida no grupo de contêineres. O `principalID` em `userAssignedIdentities` é a entidade de serviço da identidade que você criou no Azure Active Directory:

```console
[...]
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
[...]
```

### <a name="use-user-assigned-identity-to-get-secret-from-key-vault"></a>Usar identidade atribuída pelo usuário para obter o segredo do Key Vault

Agora você pode usar a identidade gerenciada dentro da instância de contêiner em execução para acessar o cofre de chaves. Primeiro, inicie um shell bash no contêiner:

```azurecli-interactive
az container exec \
  --resource-group myResourceGroup \
  --name mycontainer \
  --exec-command "/bin/bash"
```

Execute os seguintes comandos no shell bash no contêiner. Para obter um token de acesso para usar Azure Active Directory para autenticar no Key Vault, execute o seguinte comando:

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

Agora, use o token de acesso para autenticar no cofre de chaves e ler um segredo. Certifique-se de substituir o nome do cofre de chaves na URL (*https: \/ /mykeyvault.Vault.Azure.net/...*):

```bash
curl https://mykeyvault.vault.azure.net/secrets/SampleSecret/?api-version=2016-10-01 -H "Authorization: Bearer $token"
```

A resposta se assemelha à seguinte, mostrando o segredo. No código, analise essa saída para obter o segredo. Em seguida, use o segredo em uma operação subsequente para acessar outro recurso do Azure.

```bash
{"value":"Hello Container Instances","contentType":"ACIsecret","id":"https://mykeyvault.vault.azure.net/secrets/SampleSecret/xxxxxxxxxxxxxxxxxxxx","attributes":{"enabled":true,"created":1539965967,"updated":1539965967,"recoveryLevel":"Purgeable"},"tags":{"file-encoding":"utf-8"}}
```

## <a name="example-2-use-a-system-assigned-identity-to-access-azure-key-vault"></a>Exemplo 2: usar uma identidade atribuída pelo sistema para acessar o Azure Key Vault

### <a name="enable-system-assigned-identity-on-a-container-group"></a>Habilitar a identidade atribuída pelo sistema em um grupo de contêineres

Execute o comando [AZ container Create](/cli/azure/container?view=azure-cli-latest#az-container-create) a seguir para criar uma instância de contêiner com base na imagem da Microsoft `azure-cli` . Este exemplo fornece um grupo de contêiner único que você pode usar interativamente para executar o CLI do Azure para acessar outros serviços do Azure. 

O parâmetro `--assign-identity` sem valor adicional permite uma identidade gerenciada atribuída pelo sistema no grupo. A identidade tem como escopo o grupo de recursos do grupo de contêineres. O comando de execução demorada mantém o contêiner em execução. Este exemplo usa o mesmo grupo de recursos usado para criar o cofre de chaves, que está no escopo da identidade.

```azurecli-interactive
# Get the resource ID of the resource group
rgID=$(az group show --name myResourceGroup --query id --output tsv)

# Create container group with system-managed identity
az container create \
  --resource-group myResourceGroup \
  --name mycontainer \
  --image mcr.microsoft.com/azure-cli \
  --assign-identity --scope $rgID \
  --command-line "tail -f /dev/null"
```

Em poucos segundos, você obterá uma resposta da CLI do Azure indicando que a implantação foi concluída. Verifique seu status com o comando [AZ container show](/cli/azure/container#az-container-show) .

```azurecli-interactive
az container show \
  --resource-group myResourceGroup \
  --name mycontainer
```

A seção `identity` na saída é semelhante ao seguinte, mostrando que uma identidade atribuída pelo sistema é criada no Azure Active Directory:

```console
[...]
"identity": {
    "principalId": "xxxxxxxx-528d-7083-b74c-xxxxxxxxxxxx",
    "tenantId": "xxxxxxxx-f292-4e60-9122-xxxxxxxxxxxx",
    "type": "SystemAssigned",
    "userAssignedIdentities": null
},
[...]
```

Definir uma variável como o valor de `principalId` (a ID de entidade de serviço) da identidade, para ser usada em etapas posteriores.

```azurecli-interactive
spID=$(az container show \
  --resource-group myResourceGroup \
  --name mycontainer \
  --query identity.principalId --out tsv)
```

### <a name="grant-container-group-access-to-the-key-vault"></a>Conceder acesso ao grupo de contêineres para o cofre de chaves

Execute o seguinte comando [AZ keyvault Set-Policy](/cli/azure/keyvault?view=azure-cli-latest) para definir uma política de acesso no cofre de chaves. O exemplo a seguir permite que a identidade gerenciada pelo sistema obtenha segredos do cofre de chaves:

```azurecli-interactive
 az keyvault set-policy \
   --name mykeyvault \
   --resource-group myResourceGroup \
   --object-id $spID \
   --secret-permissions get
```

### <a name="use-container-group-identity-to-get-secret-from-key-vault"></a>Use a identidade do grupo de contêineres para obter o segredo do Key Vault

Agora você pode usar a identidade gerenciada para acessar o cofre de chaves na instância de contêiner em execução. Primeiro, inicie um shell bash no contêiner:

```azurecli-interactive
az container exec \
  --resource-group myResourceGroup \
  --name mycontainer \
  --exec-command "/bin/bash"
```

Execute os seguintes comandos no shell bash no contêiner. Primeiro faça logon no CLI do Azure usando a identidade gerenciada:

```bash
az login --identity
```

No contêiner em execução, recupere o segredo do cofre de chaves:

```bash
az keyvault secret show \
  --name SampleSecret \
  --vault-name mykeyvault --query value
```

O valor do segredo é recuperado:

```bash
"Hello Container Instances"
```

## <a name="enable-managed-identity-using-resource-manager-template"></a>Habilitar a identidade gerenciada usando o modelo do Resource Manager

Para habilitar uma identidade gerenciada em um grupo de contêiner usando um [modelo do Resource Manager](container-instances-multi-container-group.md), defina a propriedade `identity` do objeto `Microsoft.ContainerInstance/containerGroups` com um objeto `ContainerGroupIdentity`. Os snippets a seguir mostram a propriedade `identity` configurada para cenários diferentes. Defina a [referência de modelo do Resource Manager](/azure/templates/microsoft.containerinstance/containergroups). Especifique um mínimo `apiVersion` de `2018-10-01` .

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
Especifique um mínimo `apiVersion` de `2018-10-01` .

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
> * Conceder acesso de identidade a um cofre de chaves do Azure
> * Usar a identidade gerenciada para acessar um cofre de chaves de um contêiner em execução

* Saiba mais sobre [identidades gerenciadas para recursos do Azure](../active-directory/managed-identities-azure-resources/index.yml).

* Consulte um [exemplo do SDK do Azure go](https://medium.com/@samkreter/c98911206328) do uso de uma identidade gerenciada para acessar um cofre de chaves de instâncias de contêiner do Azure.
