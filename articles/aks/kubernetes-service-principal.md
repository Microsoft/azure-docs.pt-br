---
title: Entidades de serviço para o AKS (Serviço de Kubernetes do Azure)
description: Criar e gerenciar uma entidade de serviço do Azure Active Directory para um cluster no AKS (Serviço de Kubernetes do Azure)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: mlearned
ms.openlocfilehash: 304b9dae9f3a1e134809d8959a96dc4e3ec0edd3
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67615118"
---
# <a name="service-principals-with-azure-kubernetes-service-aks"></a>Entidades de serviço com o AKS (Serviço de Kubernetes do Azure)

Para interagir com as APIs do Azure, um cluster AKS requer uma [entidade de serviço do Azure Active Directory (AD)][aad-service-principal]. A entidade de serviço é necessária para criar e gerenciar outros recursos do Azure, como o Azure Load Balancer ou o ACR (Registro de Contêiner do Azure).

Este artigo mostra como criar e usar uma entidade de serviço para seus clusters do AKS.

## <a name="before-you-begin"></a>Antes de começar

Para criar uma entidade de serviço do Azure AD, você deve ter permissões para registrar um aplicativo com o locatário do Azure AD e para atribuir o aplicativo a uma função em sua assinatura. Se você não tiver as permissões necessárias, talvez precise solicitar ao administrador do seu Azure AD ou da assinatura que atribua as permissões necessárias ou crie previamente uma entidade de serviço para uso com o cluster do AKS.

Se você estiver usando uma entidade de serviço de um AD do Azure diferente locatário, há considerações adicionais para as permissões disponíveis quando você implanta o cluster. Você pode não ter as permissões apropriadas para ler e gravar informações de diretório. Para obter mais informações, consulte [quais são as permissões de usuário padrão no Azure Active Directory?][azure-ad-permissions]

Você também precisa da CLI do Azure versão 2.0.59 ou posterior instalado e configurado. Execute  `az --version` para encontrar a versão. Se você precisar instalar ou atualizar, consulte [instalar a CLI do Azure][install-azure-cli].

## <a name="automatically-create-and-use-a-service-principal"></a>Criar e usar uma entidade de serviço automaticamente

Quando você cria um cluster AKS no Azure portal ou usando o [criar az aks][az-aks-create] comando, o Azure pode gerar automaticamente uma entidade de serviço.

No exemplo a seguir da CLI do Azure, uma entidade de serviço não foi especificada. Neste cenário, a CLI do Azure cria um entidade de serviço para o cluster do AKS. Para concluir a operação com êxito, sua conta do Azure deve ter os direitos necessários para criar uma entidade de serviço.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup
```

## <a name="manually-create-a-service-principal"></a>Criar uma entidade de serviço manualmente

Para criar manualmente uma entidade de serviço com a CLI do Azure, use o [az ad sp create-for-rbac][az-ad-sp-create] comando. No exemplo abaixo, o parâmetro `--skip-assignment` impede que outras atribuições padrão sejam atribuídas:

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

A saída deverá ser semelhante ao seguinte exemplo: Anote seu próprio `appId` e `password`. Esses valores serão usados quando você criar um cluster do AKS na próxima seção.

```json
{
  "appId": "559513bd-0c19-4c1a-87cd-851a26afd5fc",
  "displayName": "azure-cli-2019-03-04-21-35-28",
  "name": "http://azure-cli-2019-03-04-21-35-28",
  "password": "e763725a-5eee-40e8-a466-dc88d980f415",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db48"
}
```

## <a name="specify-a-service-principal-for-an-aks-cluster"></a>Especificar uma entidade de serviço para um cluster do AKS

Para usar uma entidade de serviço existente quando você cria um cluster AKS usando o [criar az aks][az-aks-create] de comando, use o `--service-principal` e `--client-secret` parâmetros para especificar o `appId` e `password` da saída das [az ad sp create-for-rbac][az-ad-sp-create] comando:

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --service-principal <appId> \
    --client-secret <password>
```

Se você implantar um cluster do AKS usando o portal do Azure, na página *Autenticação* do diálogo **Criar cluster do Kubernetes**, escolha **Configurar entidade de serviço**. Selecione **Usar existente** e especifique os seguintes valores:

- **ID do cliente da entidade de serviço** é seu *appId*
- **Segredo do cliente da entidade de serviço** é o valor da *senha*

![Imagem de navegação para o Voto do Azure](media/kubernetes-service-principal/portal-configure-service-principal.png)

## <a name="delegate-access-to-other-azure-resources"></a>Delegar acesso a outros recursos do Azure

A entidade de serviço do cluster do AKS pode ser usada para acessar outros recursos. Por exemplo, se você quiser implantar o cluster AKS em uma sub-rede de rede virtual do Azure existente ou conecte-se ao registro de contêiner do Azure (ACR), você precisará Delegar acesso a esses recursos para a entidade de serviço.

Para delegar permissões, criar uma atribuição de função usando o [atribuição de função az criar][az-role-assignment-create] comando. Atribuir o `appId` em um escopo específico, como um grupo de recursos ou recurso de rede virtual. Em seguida, uma função define quais permissões a entidade de serviço tem no recurso, conforme mostrado no exemplo a seguir:

```azurecli
az role assignment create --assignee <appId> --scope <resourceScope> --role Contributor
```

O `--scope`, pois um recurso precisa ser uma ID de recurso completa, como */subscriptions/\<guid\>/resourceGroups/myResourceGroup* ou */subscriptions/\<guid\>/resourceGroups/myResourceGroupVnet/providers/Microsoft.Network/virtualNetworks/myVnet*

As seções a seguir detalham as delegações comuns que talvez você precise fazer.

### <a name="azure-container-registry"></a>Registro de Contêiner do Azure

Se você usar o ACR (Registro de Contêiner do Azure) como seu repositório de imagens, precisará conceder permissões para seu cluster do AKS ler e efetuar pull de imagens. A entidade de serviço do cluster do AKS deve ser receber a função *Leitor* no registro. Para obter etapas detalhadas, consulte [acesso de Grant AKS ao ACR][aks-to-acr].

### <a name="networking"></a>Rede

Você pode usar os recursos de rede quando rede e a sub-rede virtuais, ou endereços IP públicos, estiverem em outro grupo de recursos. Atribua um dos seguintes conjuntos de permissões de função:

- Criar uma [função personalizada][rbac-custom-role] e definir as permissões de função a seguir:
  - *Microsoft.Network/virtualNetworks/subnets/join/action*
  - *Microsoft.Network/virtualNetworks/subnets/read*
  - *Microsoft.Network/virtualNetworks/subnets/write*
  - *Microsoft.Network/publicIPAddresses/join/action*
  - *Microsoft.Network/publicIPAddresses/read*
  - *Microsoft.Network/publicIPAddresses/write*
- Ou, atribua o [Colaborador de rede][rbac-network-contributor] função interna na sub-rede dentro da rede virtual

### <a name="storage"></a>Armazenamento

Talvez você precise acessar os recursos de disco existentes em outro grupo de recursos. Atribua um dos seguintes conjuntos de permissões de função:

- Criar uma [função personalizada][rbac-custom-role] e definir as permissões de função a seguir:
  - *Microsoft.Compute/disks/read*
  - *Microsoft.Compute/disks/write*
- Ou, atribua o [colaborador da conta de armazenamento][rbac-storage-contributor] função interna no grupo de recursos

### <a name="azure-container-instances"></a>Instâncias de Contêiner do Azure

Se você usar o Virtual Kubelet para integrar-se ao AKS e optar por executar ACI (Instâncias de Contêiner do Azure) no grupo de recursos separadamente do cluster do AKS, a entidade de serviço do AKS deverá receber permissões de *Colaborador* no grupo de recursos do ACI.

## <a name="additional-considerations"></a>Considerações adicionais

Ao usar o AKS e as entidades de serviço do Azure AD, tenha em mente as considerações a seguir.

- A entidade de serviço para o Kubernetes é parte da configuração do cluster. No entanto, não use a identidade para implantar o cluster.
- Por padrão, as credenciais da entidade de serviço são válidas por um ano. Você pode [atualizar ou girar as credenciais da entidade de serviço][update-credentials] a qualquer momento.
- Cada entidade de serviço é associada a um aplicativo Azure AD. A entidade de serviço de um cluster Kubernetes pode ser associada a qualquer nome de aplicativo válido do Azure AD (por exemplo: *https://www.contoso.org/example* ). A URL para o aplicativo não precisa ser um ponto de extremidade real.
- Ao especificar a **ID do cliente** da entidade de serviço, use o valor de `appId`.
- No nó de agente VMs no cluster Kubernetes, as credenciais da entidade de serviço são armazenadas no arquivo. `/etc/kubernetes/azure.json`
- Quando você usa o [criar az aks][az-aks-create] comando para gerar a entidade de serviço automaticamente, o serviço de credenciais de entidade são gravadas no arquivo `~/.azure/aksServicePrincipal.json` no computador usado para executar o comando.
- Quando você exclui um cluster do AKS que foi criado por [criar az aks][az-aks-create], a entidade de serviço que foi criada automaticamente não será excluída.
    - Para excluir a entidade de serviço, consultar seu cluster *servicePrincipalProfile.clientId* e, em seguida, excluir com [exclusão de aplicativo do ad az][az-ad-app-delete]. Substitua os seguintes nomes de cluster e de grupo de recursos por seus próprios valores:

        ```azurecli
        az ad sp delete --id $(az aks show -g myResourceGroup -n myAKSCluster --query servicePrincipalProfile.clientId -o tsv)
        ```

## <a name="troubleshoot"></a>Solução de problemas

A CLI do Azure armazenados em cache as credenciais de entidade de serviço para um cluster do AKS. Se essas credenciais expiraram, você encontrar erros de implantação de clusters AKS. A seguinte mensagem de erro ao executar [criar az aks][az-aks-create] pode indicar um problema com as credenciais da entidade de serviço em cache:

```console
Operation failed with status: 'Bad Request'.
Details: The credentials in ServicePrincipalProfile were invalid. Please see https://aka.ms/aks-sp-help for more details.
(Details: adal: Refresh request failed. Status Code = '401'.
```

Verificar a idade do arquivo de credenciais usando o seguinte comando:

```console
ls -la $HOME/.azure/aksServicePrincipal.json
```

O tempo de expiração padrão para as credenciais da entidade de serviço é um ano. Se sua *aksServicePrincipal.json* arquivo é mais antigo que um ano, exclua o arquivo e tente implantar um cluster do AKS novamente.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre entidades de serviço do Azure Active Directory, consulte [objetos de entidade de serviço e aplicativo][service-principal].

Para obter informações sobre como atualizar as credenciais, consulte [atualizar ou girar as credenciais para uma entidade de serviço no AKS][update-credentials].

<!-- LINKS - internal -->
[aad-service-principal]:../active-directory/develop/app-objects-and-service-principals.md
[acr-intro]: ../container-registry/container-registry-intro.md
[az-ad-sp-create]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[azure-load-balancer-overview]: ../load-balancer/load-balancer-overview.md
[install-azure-cli]: /cli/azure/install-azure-cli
[service-principal]:../active-directory/develop/app-objects-and-service-principals.md
[user-defined-routes]: ../load-balancer/load-balancer-overview.md
[az-ad-app-list]: /cli/azure/ad/app#az-ad-app-list
[az-ad-app-delete]: /cli/azure/ad/app#az-ad-app-delete
[az-aks-create]: /cli/azure/aks#az-aks-create
[rbac-network-contributor]: ../role-based-access-control/built-in-roles.md#network-contributor
[rbac-custom-role]: ../role-based-access-control/custom-roles.md
[rbac-storage-contributor]: ../role-based-access-control/built-in-roles.md#storage-account-contributor
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[aks-to-acr]: ../container-registry/container-registry-auth-aks.md?toc=%2fazure%2faks%2ftoc.json#grant-aks-access-to-acr
[update-credentials]: update-credentials.md
[azure-ad-permissions]: ../active-directory/fundamentals/users-default-permissions.md
