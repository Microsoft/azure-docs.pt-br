---
title: Azure Red Hat OpenShift executando OpenShift 4-configurar a autenticação Azure Active Directory usando o portal do Azure e o console Web do OpenShift
description: Saiba como configurar a autenticação Azure Active Directory para um cluster do Azure Red Hat OpenShift executando o OpenShift 4 usando o portal do Azure e o console Web do OpenShift
ms.service: azure-redhat-openshift
ms.topic: article
ms.date: 03/12/2020
author: sabbour
ms.author: asabbour
keywords: aro, openshift, az aro, red hat, cli
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: eda45b1a1a011a646915cf45e45218ae168a2af6
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102213072"
---
# <a name="configure-azure-active-directory-authentication-for-an-azure-red-hat-openshift-4-cluster-portal"></a>Configurar a autenticação de Azure Active Directory para um cluster do Azure Red Hat OpenShift 4 (Portal)

Se você optar por instalar e usar a CLI localmente, este tutorial exigirá a execução da CLI do Azure versão 2.6.0 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

## <a name="before-you-begin"></a>Antes de começar

Construa a URL de **retorno de chamada OAuth** do cluster e anote-a. Certifique-se de substituir **toa-RG** pelo nome do grupo de recursos e pela **toa-cluster** pelo nome do cluster.

> [!NOTE]
> A `AAD` seção na URL de retorno de chamada OAuth deve corresponder ao nome do provedor de identidade OAuth que você irá configurar mais tarde.

```azurecli-interactive
domain=$(az aro show -g aro-rg -n aro-cluster --query clusterProfile.domain -o tsv)
location=$(az aro show -g aro-rg -n aro-cluster --query location -o tsv)
echo "OAuth callback URL: https://oauth-openshift.apps.$domain.$location.aroapp.io/oauth2callback/AAD"
```

## <a name="create-an-azure-active-directory-application-for-authentication"></a>Criar um aplicativo Azure Active Directory para autenticação

Faça logon no portal do Azure e navegue até a [folha registros de aplicativo](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade)e, em seguida, clique em **novo registro** para criar um novo aplicativo.

Forneça um nome para o aplicativo, por exemplo, **toa-azuread-auth** e preencha o **URI de redirecionamento** usando o valor da URL de retorno de chamada OAuth que você recuperou anteriormente.

![Novo registro de aplicativo](media/aro4-ad-registerapp.png)

Navegue até **certificados & segredos** e clique em **novo segredo do cliente** e preencha os detalhes. Anote o valor da chave, pois você o usará em um estágio posterior. Você não poderá recuperá-lo novamente.

![Criar um segredo](media/aro4-ad-clientsecret.png)

Navegue até a **visão geral** e anote a **ID do aplicativo (cliente)** e a **ID do diretório (locatário)**. Você precisará delas em um estágio posterior.

![Recuperar IDs de aplicativo (cliente) e diretório (locatário)](media/aro4-ad-ids.png)

## <a name="configure-optional-claims"></a>Configurar declarações opcionais

Os desenvolvedores de aplicativos podem usar [declarações opcionais](../active-directory/develop/active-directory-optional-claims.md) em seus aplicativos do Azure ad para especificar quais declarações eles desejam em tokens enviados para seu aplicativo.

Você pode usar declarações opcionais para:

* Selecione declarações adicionais para incluir nos tokens para o aplicativo.
* Altere o comportamento de determinadas declarações que o Azure AD retorna em tokens.
* Adicione e acesse as declarações personalizadas para o aplicativo.

Vamos configurar OpenShift para usar a `email` declaração e voltar para `upn` para definir o nome de usuário preferencial adicionando o `upn` como parte do token de ID retornado por Azure Active Directory.

Navegue até **configuração de token (versão prévia)** e clique em **Adicionar declaração opcional**. Selecione **ID** e, em seguida, verifique o **email** e as declarações de **UPN** .

![Captura de tela que mostra as declarações de email e UPN que foram adicionadas.](media/aro4-ad-tokens.png)

## <a name="assign-users-and-groups-to-the-cluster-optional"></a>Atribuir usuários e grupos ao cluster (opcional)

Aplicativos registrados em um locatário do Azure Active Directory (Azure AD) estão, por padrão, disponíveis para todos os usuários do locatário que se autenticar com êxito. O Azure AD permite que os desenvolvedores e administradores de locatários restrinjam um aplicativo a um conjunto específico de usuários ou grupos de segurança no locatário.

Siga as instruções na documentação do Azure Active Directory para [atribuir usuários e grupos ao aplicativo](../active-directory/develop/howto-restrict-your-app-to-a-set-of-users.md#app-registration).

## <a name="configure-openshift-openid-authentication"></a>Configurar a Autenticação OpenID OpenShift

Recupere as `kubeadmin` credenciais. Execute o comando a seguir para localizar a senha para o usuário `kubeadmin`.

```azurecli-interactive
az aro list-credentials \
  --name aro-cluster \
  --resource-group aro-rg
```

A saída de exemplo a seguir mostra que a senha estará em `kubeadminPassword`.

```json
{
  "kubeadminPassword": "<generated password>",
  "kubeadminUsername": "kubeadmin"
}
```

Você pode encontrar a URL do console de cluster executando o comando a seguir, que será semelhante a `https://console-openshift-console.apps.<random>.<region>.aroapp.io/`

```azurecli-interactive
 az aro show \
    --name aro-cluster \
    --resource-group aro-rg \
    --query "consoleProfile.url" -o tsv
```

Inicie a URL do console em um navegador e faça logon usando as credenciais `kubeadmin`.

Navegue até **Administração**, clique em **configurações de cluster** e selecione a guia **configuração global** . Role para selecionar **OAuth**.

Role para baixo para selecionar **Adicionar** em **provedores de identidade** e selecione **OpenID Connect**.
![Selecione OpenID Connect na lista suspensa provedores de identidade](media/aro4-oauth-idpdrop.png)

Preencha o nome como **AAD**, a **ID do cliente** como a **ID do aplicativo** e o segredo do **cliente**. A **URL do emissor** é formatada da seguinte forma: `https://login.microsoftonline.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` . Substitua o espaço reservado pela ID de locatário que você recuperou anteriormente.

![Preencher detalhes do OAuth](media/aro4-oauth-idp-1.png)

Role para baixo até a seção de **declarações** e atualize o **nome de usuário preferencial** para usar o valor da declaração de **UPN** .

![Preencher detalhes de declarações](media/aro4-oauth-idp-2.png)

## <a name="verify-login-through-azure-active-directory"></a>Verificar o logon por meio de Azure Active Directory

Se agora fizer logoff do console Web do OpenShift e tentar fazer logon novamente, você verá uma nova opção para fazer logon com o **AAD**. Talvez seja necessário aguardar alguns minutos.

![Tela de logon com a opção Azure Active Directory](media/aro4-login-2.png)
