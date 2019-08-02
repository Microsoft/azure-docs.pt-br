---
title: Integrar o Azure Active Directory ao Serviço de Kubernetes do Azure
description: Como criar clusters do serviço de Kubernetes do Azure habilitados pelo Active Directory do Azure (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 04/26/2019
ms.author: mlearned
ms.openlocfilehash: 80137023643630e8472a70fcca6cb656aeba7123
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67616385"
---
# <a name="integrate-azure-active-directory-with-azure-kubernetes-service"></a>Integrar o Azure Active Directory ao Serviço de Kubernetes do Azure

Serviço de Kubernetes do Azure (AKS) pode ser configurado para usar o Azure Active Directory (Azure AD) para autenticação do usuário. Nessa configuração, você pode entrar um cluster AKS usando o token de autenticação do AD do Azure.

Os administradores de cluster podem configurar Kubernetes controle de acesso baseado em função (RBAC) com base na associação de grupo de diretório ou a identidade do usuário.

Este artigo explica como:

- Implante os pré-requisitos para o AKS e o Azure AD.
- Implante um cluster do Azure AD habilitado.
- Crie uma função RBAC básica no cluster AKS usando o portal do Azure.

Você também pode executar essas etapas usando o [CLI do Azure][azure-ad-cli].

> [!NOTE]
> Azure AD só pode ser habilitado quando você cria um novo cluster habilitado pelo RBAC. Não é possível habilitar o Azure AD em um cluster existente do AKS.

## <a name="authentication-details"></a>Detalhes de autenticação

Autenticação do Azure AD é fornecida para clusters AKS com o OpenID Connect. O OpenID Connect é uma camada de identidade compilada sobre o protocolo OAuth 2.0.

Para obter mais informações sobre OpenID Connect, consulte [autorizar o acesso a aplicativos web usando o OpenID Connect e o Azure AD][open-id-connect].

Dentro de um cluster Kubernetes, autenticação de token do webhook é usada para tokens de autenticação. A autenticação de token do Webhook é configurada e gerenciada como parte do cluster AKS.

Para obter mais informações sobre autenticação de token do webhook, consulte o [autenticação de Token do Webhook][kubernetes-webhook] seção na documentação do Kubernetes.

Para fornecer a autenticação do Azure AD para um cluster do AKS, dois aplicativos do AD do Azure são criados. O primeiro aplicativo é um componente de servidor que fornece autenticação de usuário. O segundo aplicativo é um componente de cliente que é usado quando for solicitado pela CLI para autenticação. Esse aplicativo cliente usa o aplicativo de servidor para a autenticação real das credenciais fornecidas pelo cliente.

> [!NOTE]
> Quando você configurar o Azure AD para autenticação do AKS, dois aplicativos do AD do Azure são configurados. As etapas para delegar permissões para cada aplicativo devem ser concluídas por um administrador de locatário do Azure.

## <a name="create-the-server-application"></a>Criar o aplicativo de servidor

O primeiro aplicativo do Azure AD é aplicado para obter a associação de grupo do usuário do Azure AD. Para criar esse aplicativo no portal do Azure:

1. Selecione **Azure Active Directory** > **registros do aplicativo** > **novo registro**.

    a. Nomeie o aplicativo, tais como *AKSAzureADServer*.

    b. Para **suporte para tipos de conta**, selecione **contas neste diretório organizacional apenas**.
    
    c. Escolher **Web** para o URI de redirecionamento de tipo e, em seguida, insira qualquer valor URI formatado, tais como *https://aksazureadserver* .

    d. Selecione **registrar** quando tiver terminado.

2. Selecione **manifesto**e, em seguida, edite o **groupMembershipClaims:** valor como **todos os**. Quando tiver terminado com as atualizações, selecione **salvar**.

    ![Atualizar a associação de grupo para todos](media/aad-integration/edit-manifest.png)

3. No painel esquerdo do aplicativo do Azure AD, selecione **certificados e segredos**.

    a. Selecione **+ novo segredo do cliente**.

    b. Adicione uma descrição da chave, como *servidor do AD do Azure do AKS*. Escolha um tempo de expiração e, em seguida, selecione **adicionar**.

    c. Observe o valor da chave, que é exibido apenas no momento. Quando você implanta um cluster do AKS do Azure AD habilitado, esse valor é chamado o segredo do aplicativo de servidor.

4. No painel esquerdo do aplicativo do Azure AD, selecione **permissões de API**e, em seguida, selecione **+ adicionar uma permissão**.

    a. Sob **APIs do Microsoft**, selecione **o Microsoft Graph**.

    b. Selecione **permissões delegadas**e, em seguida, selecione a caixa de seleção ao lado de **Directory > Read (ler dados do diretório)** .

    c. Se um padrão recebido permissão para **usuário > Read (entrar e ler perfil do usuário)** não existir, selecione a caixa de seleção ao lado dele.

    d. Selecione **permissões de aplicativo**e, em seguida, selecione a caixa de seleção ao lado de **Directory > Read (ler dados do diretório)** .

    ![Conjunto de permissões do graph](media/aad-integration/graph-permissions.png)

    e. Selecione **adicionar permissões** para salvar as atualizações.

    f. Sob **conceder consentimento**, selecione **conceder consentimento do administrador**. Esse botão não está disponível se a conta atual não é um administrador de locatário.

    Quando as permissões são concedidas com êxito, a notificação a seguir é exibida no portal:

   ![Notificação de permissões concedidas com êxito](media/aad-integration/permissions-granted.png)

5. No painel esquerdo do aplicativo do Azure AD, selecione **expor uma API**e, em seguida, selecione **+ adicionar um escopo**.
    
    a. Insira um **nome do escopo**, um **nome de exibição de consentimento do administrador**e, em seguida, uma **descrição de consentimento do administrador** como *AKSAzureADServer*.

    b. Certifique-se **estado** é definido como **habilitado**.

    ![Expor o aplicativo de servidor como uma API para uso com outros serviços](media/aad-integration/expose-api.png)

    c. Selecione **adicionar escopo**.

6. Retornar para o aplicativo **visão geral** página e anote as **ID do aplicativo (cliente)** . Quando você implanta um cluster do AKS do Azure AD habilitado, esse valor é chamado a ID do aplicativo de servidor.

    ![Obter ID do aplicativo](media/aad-integration/application-id.png)

## <a name="create-the-client-application"></a>Criar o aplicativo cliente

O segundo aplicativo do Azure AD é usado quando você entrar com a CLI Kubernetes (kubectl).

1. Selecione **Azure Active Directory** > **registros do aplicativo** > **novo registro**.

    a. Nomeie o aplicativo, tais como *AKSAzureADClient*.

    b. Para **suporte para tipos de conta**, selecione **contas neste diretório organizacional apenas**.

    c. Selecione **Web** para o URI de redirecionamento de tipo e, em seguida, insira qualquer valor URI formatado como *https://aksazureadclient* .

    d. Selecione **registrar** quando tiver terminado.

2. No painel esquerdo do aplicativo do Azure AD, selecione **permissões de API**e, em seguida, selecione **+ adicionar uma permissão**.

    a. Selecione **Minhas APIs**e, em seguida, escolha o aplicativo de servidor do AD do Azure criado na etapa anterior, como *AKSAzureADServer*.

    b. Selecione **permissões delegadas**e, em seguida, selecione a caixa de seleção ao lado de seu aplicativo de servidor do AD do Azure.

    ![Configurar permissões de aplicativo](media/aad-integration/select-api.png)

    c. Selecione **adicionar permissões**.

    d. Sob **conceder consentimento**, selecione **conceder consentimento do administrador**. Esse botão não estará disponível se a conta atual não é um administrador de locatário. Quando as permissões são concedidas, a notificação a seguir é exibida no portal:

    ![Notificação de permissões concedidas com êxito](media/aad-integration/permissions-granted.png)

3. No painel esquerdo do aplicativo do Azure AD, selecione **autenticação**.

    - Sob **tipo de cliente padrão**, selecione **Yes** para **tratar o cliente como um cliente público**.

5. No painel esquerdo do aplicativo do AD do Azure, observe a ID do aplicativo. Quando você implanta um cluster do AKS do Azure AD habilitado, esse valor é chamado de ID do aplicativo cliente.

   ![Obtenha a ID do aplicativo](media/aad-integration/application-id-client.png)

## <a name="get-the-tenant-id"></a>Obter a ID do locatário

Em seguida, obtenha a ID do seu locatário do Azure. Esse valor é usado quando você cria o cluster do AKS.

No portal do Azure, selecione **Azure Active Directory** > **as propriedades** e observe o **ID de diretório**. Quando você cria um cluster do AKS do Azure AD habilitado, esse valor é chamado de ID de locatário.

![Obter a ID do locatário do Azure](media/aad-integration/tenant-id.png)

## <a name="deploy-the-aks-cluster"></a>Implantar o cluster do AKS

Use o [criar grupo de az][az-group-create] comando para criar um grupo de recursos para o cluster do AKS.

```azurecli
az group create --name myResourceGroup --location eastus
```

Use o [criar az aks][az-aks-create] comando para implantar o cluster do AKS. Em seguida, substitua os valores no comando de exemplo a seguir. Use os valores coletados durante a criação de aplicativos do Azure AD para a ID do aplicativo de servidor, segredo do aplicativo, ID do aplicativo cliente e ID de locatário.

```azurecli
az aks create \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --generate-ssh-keys \
  --aad-server-app-id b1536b67-29ab-4b63-b60f-9444d0c15df1 \
  --aad-server-app-secret wHYomLe2i1mHR2B3/d4sFrooHwADZccKwfoQwK2QHg= \
  --aad-client-app-id 8aaf8bd5-1bdd-4822-99ad-02bfaa63eea7 \
  --aad-tenant-id 72f988bf-0000-0000-0000-2d7cd011db47
```

Um cluster do AKS leva alguns minutos para criar.

## <a name="create-an-rbac-binding"></a>Criar uma associação de RBAC

Antes de usar uma conta do Azure Active Directory com um cluster do AKS, você deve criar associação de função ou associação de função de cluster. As funções definem as permissões para conceder e associações de aplicá-las aos usuários desejados. Essas atribuições podem ser aplicadas a um determinado namespace ou em todo o cluster. Para obter mais informações, consulte [autorização usando RBAC][rbac-authorization].

Primeiro, use o [az aks get-credentials][az-aks-get-credentials] com o `--admin` argumento para entrar no cluster com acesso de administrador.

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

Em seguida, crie ClusterRoleBinding para uma conta do Azure AD que você deseja conceder acesso ao cluster do AKS. O exemplo a seguir fornece acesso completo à conta para todos os namespaces no cluster:

- Se o usuário que você conceder que a associação de RBAC para está no mesmo locatário do AD do Azure, atribua permissões com base no nome de usuário principal (UPN). Vá para a etapa para criar o manifesto YAML para ClusterRoleBinding.

- Se o usuário estiver em um Azure AD diferente de locatário, consultar e usar o **objectId** propriedade em vez disso. Se necessário, obtenha o ID do objeto da conta de usuário necessários usando o [Mostrar de usuário do ad az][az-ad-user-show] comando. Forneça o nome principal do usuário (UPN) da conta necessária:

    ```azurecli-interactive
    az ad user show --upn-or-object-id user@contoso.com --query objectId -o tsv
    ```

Crie um arquivo, como *rbac-aad-user.yaml*e, em seguida, cole o seguinte conteúdo. Na última linha, substitua **userPrincipalName_or_objectId** com a ID de objeto ou UPN. A escolha depende se o usuário é o mesmo locatário do AD do Azure ou não.

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: contoso-cluster-admins
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: User
  name: userPrincipalName_or_objectId
```

Aplicar a associação usando o [kubectl aplicar][kubectl-apply] comando conforme mostrado no exemplo a seguir:

```console
kubectl apply -f rbac-aad-user.yaml
```

Uma associação de função também pode ser criada para todos os membros de um grupo do Azure AD. Grupos do Azure AD são especificados usando a ID de objeto do grupo, conforme mostrado no exemplo a seguir.

Crie um arquivo, como *rbac-aad-group.yaml*e, em seguida, cole o seguinte conteúdo. Atualize a ID do objeto do grupo com um do seu locatário do Azure AD:

 ```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: contoso-cluster-admins
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- apiGroup: rbac.authorization.k8s.io
   kind: Group
   name: "894656e1-39f8-4bfe-b16a-510f61af6f41"
```

Aplicar a associação usando o [kubectl aplicar][kubectl-apply] comando conforme mostrado no exemplo a seguir:

```console
kubectl apply -f rbac-aad-group.yaml
```

Para obter mais informações sobre como proteger um cluster Kubernetes com o RBAC, consulte [usando a autorização do RBAC][rbac-authorization].

## <a name="access-the-cluster-with-azure-ad"></a>Acessar o cluster com o Azure AD

Extrair o contexto para o usuário não administrador usando o [az aks get-credentials][az-aks-get-credentials] comando.

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Depois de executar o `kubectl` de comando, você será solicitado a autenticar usando o Azure. Siga na tela instruções para concluir o processo, conforme mostrado no exemplo a seguir:

```console
$ kubectl get nodes

To sign in, use a web browser to open https://microsoft.com/devicelogin. Next, enter the code BUJHWDGNL to authenticate.

NAME                       STATUS    ROLES     AGE       VERSION
aks-nodepool1-79590246-0   Ready     agent     1h        v1.13.5
aks-nodepool1-79590246-1   Ready     agent     1h        v1.13.5
aks-nodepool1-79590246-2   Ready     agent     1h        v1.13.5
```

Quando o processo for concluído, o token de autenticação é armazenado em cache. Somente você será solicitado a entrar novamente quando o token expira ou o arquivo de configuração do Kubernetes é recriado.

Se você vir uma mensagem de erro de autorização, depois de entrar com êxito, verifique os seguintes critérios:

```console
error: You must be logged in to the server (Unauthorized)
```


- Você definiu a ID de objeto apropriado ou o UPN, dependendo se a conta de usuário está no mesmo locatário do Azure AD ou não.
- O usuário não é um membro dos grupos de mais de 200.
- O segredo definido no registro do aplicativo para servidor corresponde ao valor configurado usando `--aad-server-app-secret`.

## <a name="next-steps"></a>Próximas etapas

Para usar grupos e usuários do AD do Azure para controlar o acesso aos recursos do cluster, consulte [controlar o acesso aos recursos de cluster usando o controle de acesso baseado em função e identidades do Azure AD no AKS][azure-ad-rbac].

Para obter mais informações sobre como proteger clusters de Kubernetes, consulte [opções de acesso e identidade para o AKS][rbac-authorization].

Para saber mais sobre o controle de identidade e recursos, consulte [práticas recomendadas para autenticação e autorização no AKS][operator-best-practices-identity].

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[open-id-connect]:../active-directory/develop/v1-protocols-openid-connect-code.md
[az-ad-user-show]: /cli/azure/ad/user#az-ad-user-show
[rbac-authorization]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[azure-ad-cli]: azure-ad-integration-cli.md
