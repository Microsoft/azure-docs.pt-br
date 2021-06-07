---
title: Entidades de serviço para o AKS (Serviço de Kubernetes do Azure)
description: Criar e gerenciar uma entidade de serviço do Azure Active Directory para um cluster no AKS (Serviço de Kubernetes do Azure)
services: container-service
ms.topic: conceptual
ms.date: 06/16/2020
ms.openlocfilehash: b4b5b3eedb2e63686e1bb26580ea653e3a50a910
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102507816"
---
# <a name="service-principals-with-azure-kubernetes-service-aks"></a>Entidades de serviço com o AKS (Serviço de Kubernetes do Azure)

Para interagir com as APIs do Azure, um cluster AKS requer uma [entidade de serviço Azure Active Directory (AD)][aad-service-principal] ou uma [identidade gerenciada](use-managed-identity.md). Uma entidade de serviço ou identidade gerenciada é necessária para criar e gerenciar dinamicamente outros recursos do Azure, como um Azure Load Balancer ou um ACR (registro de contêiner).

Este artigo mostra como criar e usar uma entidade de serviço para seus clusters do AKS.

## <a name="before-you-begin"></a>Antes de começar

Para criar uma entidade de serviço do Azure AD, você deve ter permissões para registrar um aplicativo com o locatário do Azure AD e para atribuir o aplicativo a uma função em sua assinatura. Se você não tiver as permissões necessárias, talvez precise solicitar ao administrador do seu Azure AD ou da assinatura que atribua as permissões necessárias ou crie previamente uma entidade de serviço para uso com o cluster do AKS.

Se você estiver usando uma entidade de serviço de um locatário do Azure AD diferente, haverá considerações adicionais sobre as permissões disponíveis quando você implantar o cluster. Talvez você não tenha as permissões apropriadas para ler e gravar informações de diretório. Para obter mais informações, consulte [quais são as permissões de usuário padrão no Azure Active Directory?][azure-ad-permissions]

A CLI do Azure versão 2.0.59 ou posterior também precisa estar instalada e configurada. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure][install-azure-cli].

## <a name="automatically-create-and-use-a-service-principal"></a>Criar e usar uma entidade de serviço automaticamente

Quando você cria um cluster do AKS no portal do Azure ou usando o comando [criar az aks][az-aks-create], o Azure pode gerar uma entidade de serviço automaticamente.

No exemplo a seguir da CLI do Azure, uma entidade de serviço não foi especificada. Neste cenário, a CLI do Azure cria um entidade de serviço para o cluster do AKS. Para concluir a operação com êxito, sua conta do Azure deve ter os direitos necessários para criar uma entidade de serviço.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup
```

## <a name="manually-create-a-service-principal"></a>Criar uma entidade de serviço manualmente

Para criar uma entidade de serviço com a CLI do Azure manualmente, use o comando [az ad sp create-for-rbac][az-ad-sp-create]. No exemplo abaixo, o parâmetro `--skip-assignment` impede que outras atribuições padrão sejam atribuídas:

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment --name myAKSClusterServicePrincipal
```

A saída deverá ser semelhante ao seguinte exemplo: Anote seu próprio `appId` e `password`. Esses valores serão usados quando você criar um cluster do AKS na próxima seção.

```json
{
  "appId": "559513bd-0c19-4c1a-87cd-851a26afd5fc",
  "displayName": "myAKSClusterServicePrincipal",
  "name": "http://myAKSClusterServicePrincipal",
  "password": "e763725a-5eee-40e8-a466-dc88d980f415",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db48"
}
```

## <a name="specify-a-service-principal-for-an-aks-cluster"></a>Especificar uma entidade de serviço para um cluster do AKS

Para usar uma entidade de serviço existente quando você criar um cluster do AKS usando o comando [criar az aks][az-aks-create], use os parâmetros `--service-principal` e `--client-secret` para especificar o `appId` e a `password` da saída do comando [az ad sp create-for-rbac][az-ad-sp-create]:

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --service-principal <appId> \
    --client-secret <password>
```

> [!NOTE]
> Se você estiver usando uma entidade de serviço existente com segredo personalizado, verifique se o segredo não tem mais de 190 bytes.

Se você implantar um cluster do AKS usando o portal do Azure, na página *Autenticação* do diálogo **Criar cluster do Kubernetes**, escolha **Configurar entidade de serviço**. Selecione **Usar existente** e especifique os seguintes valores:

- **ID do cliente da entidade de serviço** é seu *appId*
- **Segredo do cliente da entidade de serviço** é o valor da *senha*

![Imagem de navegação para o Voto do Azure](media/kubernetes-service-principal/portal-configure-service-principal.png)

## <a name="delegate-access-to-other-azure-resources"></a>Delegar acesso a outros recursos do Azure

A entidade de serviço do cluster do AKS pode ser usada para acessar outros recursos. Por exemplo, se você deseja implantar o cluster AKS em uma sub-rede de rede virtual do Azure existente ou conectar-se ao registro de contêiner do Azure (ACR), você precisa delegar acesso a esses recursos para a entidade de serviço.

Para delegar permissões, crie uma atribuição de função usando o comando [AZ role Assignment Create][az-role-assignment-create] . Atribua o `appId` a um escopo específico, como um grupo de recursos ou recurso de rede virtual. Em seguida, uma função define quais permissões a entidade de serviço tem no recurso, conforme mostrado no exemplo a seguir:

```azurecli
az role assignment create --assignee <appId> --scope <resourceScope> --role Contributor
```

O `--scope` para um recurso precisa ser uma ID de recurso completa, como */subscriptions/ \<guid\> /ResourceGroups/myResourceGroup* ou */subscriptions/ \<guid\> /resourceGroups/myResourceGroupVnet/Providers/Microsoft.Network/virtualNetworks/myVnet*

> [!NOTE]
> Se você tiver removido a atribuição de função de colaborador do grupo de recursos de nó, as operações abaixo poderão falhar.  

As seções a seguir detalham as delegações comuns que talvez você precise fazer.

### <a name="azure-container-registry"></a>Registro de Contêiner do Azure

Se você usar o ACR (registro de contêiner do Azure) como seu repositório de imagens de contêiner, precisará conceder permissões à entidade de serviço para o cluster AKS ler e efetuar pull de imagens. Atualmente, a configuração recomendada é usar o comando [AZ AKs Create][az-aks-create] ou [AZ AKs Update][az-aks-update] para integrar com um registro e atribuir a função apropriada para a entidade de serviço. Para obter etapas detalhadas, consulte [autenticar com o registro de contêiner do Azure do serviço kubernetes do Azure][aks-to-acr].

### <a name="networking"></a>Rede

Você pode usar os recursos de rede quando rede e a sub-rede virtuais, ou endereços IP públicos, estiverem em outro grupo de recursos. Atribua a função interna de [colaborador de rede][rbac-network-contributor] na sub-rede dentro da rede virtual. Como alternativa, você pode criar uma [função personalizada][rbac-custom-role] com permissões para acessar os recursos de rede nesse grupo de recursos. Consulte [permissões do serviço AKs][aks-permissions] para obter mais detalhes.

### <a name="storage"></a>Armazenamento

Talvez você precise acessar os recursos de disco existentes em outro grupo de recursos. Atribua um dos seguintes conjuntos de permissões de função:

- Crie uma [função personalizada][rbac-custom-role] e defina as permissões de função a seguir:
  - *Microsoft.Compute/disks/read*
  - *Microsoft.Compute/disks/write*
- Ou, atribua a função interna [Colaborador da Conta de Armazenamento][rbac-storage-contributor] no grupo de recursos

### <a name="azure-container-instances"></a>Instâncias de Contêiner do Azure

Se você usar o Virtual Kubelet para integrar-se ao AKS e optar por executar ACI (Instâncias de Contêiner do Azure) no grupo de recursos separadamente do cluster do AKS, a entidade de serviço do AKS deverá receber permissões de *Colaborador* no grupo de recursos do ACI.

## <a name="additional-considerations"></a>Considerações adicionais

Ao usar o AKS e as entidades de serviço do Azure AD, tenha em mente as considerações a seguir.

- A entidade de serviço para o Kubernetes é parte da configuração do cluster. No entanto, não use a identidade para implantar o cluster.
- Por padrão, as credenciais da entidade de serviço são válidas por um ano. Você pode [atualizar ou girar as credenciais da entidade de serviço][update-credentials] a qualquer momento.
- Cada entidade de serviço é associada a um aplicativo Azure AD. A entidade de serviço para um cluster kubernetes pode ser associada a qualquer nome de aplicativo do Azure AD válido (por exemplo: *https://www.contoso.org/example* ). A URL para o aplicativo não precisa ser um ponto de extremidade real.
- Ao especificar a **ID do cliente** da entidade de serviço, use o valor de `appId`.
- Nas VMs do nó do agente no cluster kubernetes, as credenciais da entidade de serviço são armazenadas no arquivo `/etc/kubernetes/azure.json`
- Se você usar o comando [az aks create][az-aks-create] para gerar a entidade de serviço automaticamente, as credenciais da entidade de serviço serão gravadas no arquivo `~/.azure/aksServicePrincipal.json` no computador usado para executar o comando.
- Se você não passar uma entidade de serviço especificamente em comandos adicionais da CLI do AKS, a entidade de serviço padrão localizada em `~/.azure/aksServicePrincipal.json` será usada.  
- Opcionalmente, você também pode remover o aksServicePrincipal.jsno arquivo e o AKS criará uma nova entidade de serviço.
- Ao excluir um cluster do AKS que foi criado por [az aks create][az-aks-create], a entidade de serviço que foi criada automaticamente não será excluída.
    - Para excluir a entidade de serviço, consulte o cluster *servicePrincipalProfile. clientId* e, em seguida, exclua com [AZ ad SP Delete][az-ad-sp-delete]. Substitua os seguintes nomes de cluster e de grupo de recursos por seus próprios valores:

        ```azurecli
        az ad sp delete --id $(az aks show -g myResourceGroup -n myAKSCluster --query servicePrincipalProfile.clientId -o tsv)
        ```

## <a name="troubleshoot"></a>Solucionar problemas

As credenciais da entidade de serviço para um cluster AKS são armazenadas em cache pelo CLI do Azure. Se essas credenciais tiverem expirado, você encontrará erros ao implantar clusters AKS. A seguinte mensagem de erro ao executar [AZ AKs Create][az-aks-create] pode indicar um problema com as credenciais da entidade de serviço armazenada em cache:

```console
Operation failed with status: 'Bad Request'.
Details: The credentials in ServicePrincipalProfile were invalid. Please see https://aka.ms/aks-sp-help for more details.
(Details: adal: Refresh request failed. Status Code = '401'.
```

Verifique a idade do arquivo de credenciais usando o seguinte comando:

```console
ls -la $HOME/.azure/aksServicePrincipal.json
```

O tempo de expiração padrão para as credenciais da entidade de serviço é de um ano. Se seu *aksServicePrincipal.jsno* arquivo tiver mais de um ano, exclua o arquivo e tente implantar um cluster AKs novamente.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre Azure Active Directory entidades de serviço, consulte [objetos de aplicativo e entidade de serviço][service-principal].

Para obter informações sobre como atualizar as credenciais, consulte [atualizar ou girar as credenciais de uma entidade de serviço no AKs][update-credentials].

<!-- LINKS - internal -->
[aad-service-principal]:../active-directory/develop/app-objects-and-service-principals.md
[acr-intro]: ../container-registry/container-registry-intro.md
[az-ad-sp-create]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-ad-sp-delete]: /cli/azure/ad/sp#az_ad_sp_delete
[azure-load-balancer-overview]: ../load-balancer/load-balancer-overview.md
[install-azure-cli]: /cli/azure/install-azure-cli
[service-principal]:../active-directory/develop/app-objects-and-service-principals.md
[user-defined-routes]: ../load-balancer/load-balancer-overview.md
[az-ad-app-list]: /cli/azure/ad/app#az-ad-app-list
[az-ad-app-delete]: /cli/azure/ad/app#az-ad-app-delete
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-update]: /cli/azure/aks#az-aks-update
[rbac-network-contributor]: ../role-based-access-control/built-in-roles.md#network-contributor
[rbac-custom-role]: ../role-based-access-control/custom-roles.md
[rbac-storage-contributor]: ../role-based-access-control/built-in-roles.md#storage-account-contributor
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[aks-to-acr]: cluster-container-registry-integration.md
[update-credentials]: update-credentials.md
[azure-ad-permissions]: ../active-directory/fundamentals/users-default-permissions.md
[aks-permissions]: concepts-identity.md#aks-service-permissions
