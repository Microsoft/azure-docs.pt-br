---
title: Implantar a imagem de contêiner do registro de contêiner do Azure
description: Saiba como implantar contêineres em instâncias de contêiner do Azure puxando imagens de contêiner de um registro de contêiner do Azure.
services: container-instances
ms.topic: article
ms.date: 07/02/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: cca1001f0f84f4e4fc87df233f872fc1efdb3267
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92736732"
---
# <a name="deploy-to-azure-container-instances-from-azure-container-registry"></a>Implantar nas Instâncias de Contêiner do Azure por meio do Registro de Contêiner do Azure

O [Registro de Contêiner do Azure](../container-registry/container-registry-intro.md) é um serviço de registro de contêiner gerenciado, baseado no Azure, usado para armazenar imagens de contêiner de Docker particulares. Este artigo descreve como efetuar pull de imagens de contêiner armazenadas em um registro de contêiner do Azure durante a implantação em instâncias de contêiner do Azure. Uma maneira recomendada para configurar o acesso ao registro é criar um Azure Active Directory entidade de serviço e uma senha e armazenar as credenciais de logon em um cofre de chaves do Azure.

## <a name="prerequisites"></a>Pré-requisitos

**Registro de contêiner do Azure** : você precisa de um registro de contêiner do Azure – e pelo menos uma imagem de contêiner no registro – para concluir as etapas neste artigo. Se você precisar de um registro, consulte [Criar um registro de contêiner usando a CLI do Azure](../container-registry/container-registry-get-started-azure-cli.md).

**CLI do Azure** : os exemplos de linha de comando neste artigo usam a [CLI do Azure](/cli/azure/) e são formatados para shell de Bash. Você pode [instalar a CLI do Azure](/cli/azure/install-azure-cli) localmente ou usar o [Azure Cloud Shell][cloud-shell-bash].

## <a name="limitations"></a>Limitações

* Não é possível autenticar no registro de contêiner do Azure para extrair imagens durante a implantação do grupo de contêineres usando uma [identidade gerenciada](container-instances-managed-identity.md) configurada no mesmo grupo de contêineres.
* Você não pode efetuar pull de imagens do [registro de contêiner do Azure](../container-registry/container-registry-vnet.md) implantado em uma rede virtual do Azure neste momento.

## <a name="configure-registry-authentication"></a>Configurar a autenticação do registro

Em um cenário de produção em que você fornece acesso a aplicativos e serviços "sem periféricos", é recomendável configurar o acesso ao registro usando uma [entidade de serviço](../container-registry/container-registry-auth-service-principal.md). Uma entidade de serviço permite que você forneça o [controle de acesso baseado em função do Azure (RBAC do Azure)](../container-registry/container-registry-roles.md) às suas imagens de contêiner. Por exemplo, é possível configurar uma entidade de serviço com acesso somente pull para um registro.

O registro de contêiner do Azure fornece [Opções de autenticação](../container-registry/container-registry-authentication.md)adicionais.

Na seção a seguir, você cria um cofre de chaves do Azure e uma entidade de serviço e armazena as credenciais da entidade de serviço no cofre.

### <a name="create-key-vault"></a>Criar cofre chaves

Se você ainda não tiver um cofre no [Azure Key Vault](../key-vault/general/overview.md), crie um com a CLI do Azure usando os comandos a seguir.

Atualize a variável `RES_GROUP` com o nome do grupo de recursos existente no qual criar o cofre de chaves e `ACR_NAME` com o nome do registro de contêiner. Para fins de brevidade, os comandos neste artigo pressupõem que o registro, o cofre de chaves e as instâncias de contêiner sejam todos criados no mesmo grupo de recursos.

 Especifique um nome para o novo cofre de chaves em `AKV_NAME`. O nome do cofre deve ser exclusivo no Azure e deve ter de 3 a 24 caracteres alfanuméricos, começar com uma letra, terminar com uma letra ou um dígito e não pode conter hífens consecutivos.

```azurecli
RES_GROUP=myresourcegroup # Resource Group name
ACR_NAME=myregistry       # Azure Container Registry registry name
AKV_NAME=mykeyvault       # Azure Key Vault vault name

az keyvault create -g $RES_GROUP -n $AKV_NAME
```

### <a name="create-service-principal-and-store-credentials"></a>Criar entidades de serviço e armazenar credenciais

Agora, crie uma entidade de serviço e armazene suas credenciais no cofre de chaves.

O comando a seguir usa [az ad sp create-for-rbac][az-ad-sp-create-for-rbac] para criar a entidade de serviço e [az keyvault secret set][az-keyvault-secret-set] para armazenar a **senha** da entidade de serviço no cofre.

```azurecli
# Create service principal, store its password in vault (the registry *password*)
az keyvault secret set \
  --vault-name $AKV_NAME \
  --name $ACR_NAME-pull-pwd \
  --value $(az ad sp create-for-rbac \
                --name http://$ACR_NAME-pull \
                --scopes $(az acr show --name $ACR_NAME --query id --output tsv) \
                --role acrpull \
                --query password \
                --output tsv)
```

O argumento `--role` no comando anterior configura a entidade de serviço com a função *acrpull* , que permite a ela acesso somente de pull ao Registro. Para permitir acesso de push e pull, altere o argumento `--role` para *acrpush* .

Em seguida, armazene o *AppID* da entidade de serviço no cofre, que é o **nome de usuário** que você passa para o registro de contêiner do Azure para autenticação.

```azurecli
# Store service principal ID in vault (the registry *username*)
az keyvault secret set \
    --vault-name $AKV_NAME \
    --name $ACR_NAME-pull-usr \
    --value $(az ad sp show --id http://$ACR_NAME-pull --query appId --output tsv)
```

Você criou um cofre de chaves do Azure e armazenou dois segredos nele:

* `$ACR_NAME-pull-usr`: a ID da entidade de serviço, para uso como o **nome de usuário** do registro de contêiner.
* `$ACR_NAME-pull-pwd`: a senha da entidade de serviço, para uso como a **senha** do registro de contêiner.

Agora, é possível referenciar esses segredos pelo nome quando você ou os aplicativos e serviços efetuam pull de imagens do registro.

## <a name="deploy-container-with-azure-cli"></a>Implantar contêiner com CLI do Azure

Agora que as credenciais da entidade de serviço estão armazenadas nos segredos do Azure Key Vault, os aplicativos e serviços podem utilizá-las para acessar o registro privado.

Primeiro, obtenha o nome do servidor de logon do registro usando o comando [az acr show][az-acr-show]. O nome do servidor de logon é composto por letras minúsculas e é semelhante a `myregistry.azurecr.io`.

```azurecli
ACR_LOGIN_SERVER=$(az acr show --name $ACR_NAME --resource-group $RES_GROUP --query "loginServer" --output tsv)
```

Execute o seguinte comando [az container create][az-container-create] para implantar uma instância de contêiner. O comando usa as credenciais da entidade de serviço armazenadas no Azure Key Vault para autenticar no registro de contêiner e assume que você efetuou push da imagem [aci-helloworld](container-instances-quickstart.md) anteriormente para o registro. Atualize o valor `--image` se você quiser usar uma imagem diferente do registro.

```azurecli
az container create \
    --name aci-demo \
    --resource-group $RES_GROUP \
    --image $ACR_LOGIN_SERVER/aci-helloworld:v1 \
    --registry-login-server $ACR_LOGIN_SERVER \
    --registry-username $(az keyvault secret show --vault-name $AKV_NAME -n $ACR_NAME-pull-usr --query value -o tsv) \
    --registry-password $(az keyvault secret show --vault-name $AKV_NAME -n $ACR_NAME-pull-pwd --query value -o tsv) \
    --dns-name-label aci-demo-$RANDOM \
    --query ipAddress.fqdn
```

O valor `--dns-name-label` deve ser exclusivo no Azure, portanto, o comando anterior acrescenta um número aleatório ao rótulo do nome DNS do contêiner. A saída do comando exibe o FQDN (nome de domínio totalmente qualificado) do contêiner, por exemplo:

```output
"aci-demo-25007.eastus.azurecontainer.io"
```

Depois que o contêiner for iniciado com êxito, será possível navegar para o FQDN no navegador para verificar se o aplicativo está sendo executado com êxito.

## <a name="deploy-with-azure-resource-manager-template"></a>Implantar com o modelo do Azure Resource Manager

Você pode especificar as propriedades do seu registro de contêiner do Azure em um modelo de Azure Resource Manager, incluindo a `imageRegistryCredentials` Propriedade na definição do grupo de contêineres. Por exemplo, você pode especificar as credenciais de registro diretamente:

```JSON
[...]
"imageRegistryCredentials": [
  {
    "server": "imageRegistryLoginServer",
    "username": "imageRegistryUsername",
    "password": "imageRegistryPassword"
  }
]
[...]
```

Para obter configurações completas do grupo de contêineres, consulte a [referência de modelo do Resource Manager](/azure/templates/Microsoft.ContainerInstance/2019-12-01/containerGroups).    

Para obter detalhes sobre como referenciar segredos do Azure Key Vault em um modelo do Resource Manager, consulte [Usar o Azure Key Vault para passar o valor de parâmetro seguro durante a implantação](../azure-resource-manager/templates/key-vault-parameter.md).

## <a name="deploy-with-azure-portal"></a>Implantar com o portal do Azure

Se você mantiver as imagens de contêiner no Registro de Contêiner do Azure, você poderá facilmente criar um contêiner em Instâncias de Contêiner do Azure usando o portal do Azure. Ao usar o portal para implantar uma instância de contêiner de um registro de contêiner, você deve habilitar a [conta de administrador](../container-registry/container-registry-authentication.md#admin-account) do registro. A conta do administrador destina-se para um único usuário acessar o registro, principalmente para fins de teste. 

1. No Portal do Azure, navegue até o registro de contêiner.

1. Para confirmar que a conta de administrador é habilitada, selecione **Chaves de acesso** e, em **Usuário administrador** selecione **Habilitar** .

1. Selecione **Repositórios** e, em seguida, selecione o repositório do qual deseja implantar, clique com o botão direito do mouse na marca da imagem de contêiner que deseja implantar e selecione **Executar instância** .

    !["Executar instância" no Registro de Contêiner do Azure no portal do Azure][acr-runinstance-contextmenu]

1. Insira um nome para o contêiner e um nome para o grupo de recursos. Você também poderá alterar os valores padrão se desejar.

    ![Criar menu para Instâncias de Contêiner do Azure][acr-create-deeplink]

1. Quando a implantação for concluída, você poderá navegar para o grupo de contêineres do painel de notificações para localizar o endereço IP e outras propriedades do contêiner.

    ![Exibição de detalhes de grupo de contêineres das Instâncias de Contêiner do Azure][aci-detailsview]

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre a autenticação do Registro de Contêiner do Azure, consulte [Autenticar com um registro de contêiner do Azure](../container-registry/container-registry-authentication.md).

<!-- IMAGES -->
[acr-create-deeplink]: ./media/container-instances-using-azure-container-registry/acr-create-deeplink.png
[aci-detailsview]: ./media/container-instances-using-azure-container-registry/aci-detailsview.png
[acr-runinstance-contextmenu]: ./media/container-instances-using-azure-container-registry/acr-runinstance-contextmenu.png

<!-- LINKS - External -->
[cloud-shell-bash]: https://shell.azure.com/bash
[cloud-shell-try-it]: https://shell.azure.com/powershell

<!-- LINKS - Internal -->
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-container-create]: /cli/azure/container#az-container-create
[az-keyvault-secret-set]: /cli/azure/keyvault/secret#az-keyvault-secret-set
