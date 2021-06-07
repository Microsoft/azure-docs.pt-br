---
title: Azure Red Hat OpenShift executando OpenShift 4-configurar a autenticação Azure Active Directory usando a linha de comando
description: Saiba como configurar a autenticação Azure Active Directory para um cluster do Azure Red Hat OpenShift executando o OpenShift 4 usando a linha de comando
ms.service: azure-redhat-openshift
ms.topic: article
ms.date: 03/12/2020
author: sabbour
ms.author: asabbour
keywords: aro, openshift, az aro, red hat, cli
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 06f7bfea9a88627733eb9ce9166e05d05790e23a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102213055"
---
# <a name="configure-azure-active-directory-authentication-for-an-azure-red-hat-openshift-4-cluster-cli"></a>Configurar a autenticação de Azure Active Directory para um cluster do Azure Red Hat OpenShift 4 (CLI)

Se você optar por instalar e usar a CLI localmente, este artigo exigirá que você esteja executando o CLI do Azure versão 2.6.0 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

Recupere as URLs específicas do cluster que serão usadas para configurar o aplicativo Azure Active Directory.

Defina as variáveis para o grupo de recursos e o nome do cluster.

Substitua **\<resource_group>** pelo nome do grupo de recursos e **\<aro_cluster>** pelo nome do seu cluster.

```azurecli-interactive
resource_group=<resource_group>
aro_cluster=<aro_cluster>
```

Construa a URL de retorno de chamada OAuth do cluster e armazene-a em uma variável **oauthCallbackURL**. 

> [!NOTE]
> A `AAD` seção na URL de retorno de chamada OAuth deve corresponder ao nome do provedor de identidade OAuth que você irá configurar mais tarde.


```azurecli-interactive
domain=$(az aro show -g $resource_group -n $aro_cluster --query clusterProfile.domain -o tsv)
location=$(az aro show -g $resource_group -n $aro_cluster --query location -o tsv)
apiServer=$(az aro show -g $resource_group -n $aro_cluster --query apiserverProfile.url -o tsv)
webConsole=$(az aro show -g $resource_group -n $aro_cluster --query consoleProfile.url -o tsv)
```

O formato do oauthCallbackURL é ligeiramente diferente com domínios personalizados:

* Execute o comando a seguir se você estiver usando um domínio personalizado, por exemplo, `contoso.com` . 

    ```azurecli-interactive
    oauthCallbackURL=https://oauth-openshift.apps.$domain/oauth2callback/AAD
    ```

* Se você não estiver usando um domínio personalizado, o `$domain` será uma cadeia de caracteres alnum de oito caracteres e será estendido pelo `$location.aroapp.io` .

    ```azurecli-interactive
    oauthCallbackURL=https://oauth-openshift.apps.$domain.$location.aroapp.io/oauth2callback/AAD
    ```

> [!NOTE]
> A `AAD` seção na URL de retorno de chamada OAuth deve corresponder ao nome do provedor de identidade OAuth que você irá configurar mais tarde.

## <a name="create-an-azure-active-directory-application-for-authentication"></a>Criar um aplicativo Azure Active Directory para autenticação

Substitua **\<client_secret>** por uma senha segura para o aplicativo.

```azurecli-interactive
client_secret=<client_secret>
```

Crie um aplicativo Azure Active Directory e recupere o identificador de aplicativo criado.

```azurecli-interactive
app_id=$(az ad app create \
  --query appId -o tsv \
  --display-name aro-auth \
  --reply-urls $oauthCallbackURL \
  --password $client_secret)
```

Recupere a ID de locatário da assinatura proprietária do aplicativo.

```azure
tenant_id=$(az account show --query tenantId -o tsv)
```

## <a name="create-a-manifest-file-to-define-the-optional-claims-to-include-in-the-id-token"></a>Criar um arquivo de manifesto para definir as declarações opcionais a serem incluídas no token de ID

Os desenvolvedores de aplicativos podem usar [declarações opcionais](../active-directory/develop/active-directory-optional-claims.md) em seus aplicativos do Azure ad para especificar quais declarações eles desejam em tokens enviados para seu aplicativo.

Você pode usar declarações opcionais para:

- Selecione declarações adicionais para incluir nos tokens para o aplicativo.
- Altere o comportamento de determinadas declarações que o Azure AD retorna em tokens.
- Adicione e acesse as declarações personalizadas para o aplicativo.

Vamos configurar OpenShift para usar a `email` declaração e voltar para `upn` para definir o nome de usuário preferencial adicionando o `upn` como parte do token de ID retornado por Azure Active Directory.

Crie um **manifest.jsno** arquivo para configurar o aplicativo Azure Active Directory.

```bash
cat > manifest.json<< EOF
[{
  "name": "upn",
  "source": null,
  "essential": false,
  "additionalProperties": []
},
{
"name": "email",
  "source": null,
  "essential": false,
  "additionalProperties": []
}]
EOF
```

## <a name="update-the-azure-active-directory-applications-optionalclaims-with-a-manifest"></a>Atualizar o optionalClaims do aplicativo de Azure Active Directory com um manifesto

```azurecli-interactive
az ad app update \
  --set optionalClaims.idToken=@manifest.json \
  --id $app_id
```

## <a name="update-the-azure-active-directory-application-scope-permissions"></a>Atualizar as permissões de escopo do aplicativo Azure Active Directory

Para poder ler as informações do usuário de Azure Active Directory, precisamos definir os escopos adequados.

Adicione permissão para o escopo **Azure Active Directory Graph. User. Read** para habilitar a entrada e ler o perfil do usuário.

```azurecli-interactive
az ad app permission add \
 --api 00000002-0000-0000-c000-000000000000 \
 --api-permissions 311a71cc-e848-46a1-bdf8-97ff7156d8e6=Scope \
 --id $app_id
```

> [!NOTE]
> Você pode ignorar com segurança a mensagem para conceder o consentimento, a menos que você esteja autenticado como um administrador global para esse Azure Active Directory. Os usuários de domínio padrão serão solicitados a conceder consentimento quando fizerem logon no cluster pela primeira vez usando suas credenciais do AAD.

## <a name="assign-users-and-groups-to-the-cluster-optional"></a>Atribuir usuários e grupos ao cluster (opcional)

Aplicativos registrados em um locatário do Azure Active Directory (Azure AD) estão, por padrão, disponíveis para todos os usuários do locatário que se autenticar com êxito. O Azure AD permite que os desenvolvedores e administradores de locatários restrinjam um aplicativo a um conjunto específico de usuários ou grupos de segurança no locatário.

Siga as instruções na documentação do Azure Active Directory para [atribuir usuários e grupos ao aplicativo](../active-directory/develop/howto-restrict-your-app-to-a-set-of-users.md#app-registration).

## <a name="configure-openshift-openid-authentication"></a>Configurar a Autenticação OpenID OpenShift

Recupere as `kubeadmin` credenciais. Execute o comando a seguir para localizar a senha para o usuário `kubeadmin`.

```azurecli-interactive
kubeadmin_password=$(az aro list-credentials \
  --name $aro_cluster \
  --resource-group $resource_group \
  --query kubeadminPassword --output tsv)
```

Faça logon no servidor de API do cluster OpenShift usando o comando a seguir. 

```azurecli-interactive
oc login $apiServer -u kubeadmin -p $kubeadmin_password
```

Crie um segredo OpenShift para armazenar o segredo do aplicativo Azure Active Directory.

```azurecli-interactive
oc create secret generic openid-client-secret-azuread \
  --namespace openshift-config \
  --from-literal=clientSecret=$client_secret
```

Crie um arquivo **oidc. YAML** para configurar a autenticação do OpenShift OpenID em relação ao Azure Active Directory. 

```bash
cat > oidc.yaml<< EOF
apiVersion: config.openshift.io/v1
kind: OAuth
metadata:
  name: cluster
spec:
  identityProviders:
  - name: AAD
    mappingMethod: claim
    type: OpenID
    openID:
      clientID: $app_id
      clientSecret:
        name: openid-client-secret-azuread
      extraScopes:
      - email
      - profile
      extraAuthorizeParameters:
        include_granted_scopes: "true"
      claims:
        preferredUsername:
        - email
        - upn
        name:
        - name
        email:
        - email
      issuer: https://login.microsoftonline.com/$tenant_id
EOF
```

Aplique a configuração ao cluster.

```azurecli-interactive
oc apply -f oidc.yaml
```

Você receberá uma resposta semelhante à seguinte.

```output
oauth.config.openshift.io/cluster configured
```

## <a name="verify-login-through-azure-active-directory"></a>Verificar o logon por meio de Azure Active Directory

Se agora fizer logoff do console Web do OpenShift e tentar fazer logon novamente, você verá uma nova opção para fazer logon com o **AAD**. Talvez seja necessário aguardar alguns minutos.

![Tela de logon com a opção Azure Active Directory](media/aro4-login-2.png)
