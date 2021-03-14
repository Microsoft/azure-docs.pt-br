---
title: Configurar registro de contêiner interno para o Red Hat OpenShift 4 no Azure
description: Configurar registro de contêiner interno para o Red Hat OpenShift 4 no Azure
author: jiangma
ms.author: jiangma
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 10/15/2020
ms.openlocfilehash: dda050b8d824f0ff0ac1c84d2f008387de55aedf
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100636367"
---
# <a name="configure-built-in-container-registry-for-azure-red-hat-openshift-4"></a>Configurar registro de contêiner interno para o Red Hat OpenShift 4 no Azure

O Azure Red Hat OpenShift fornece um registro de imagem de contêiner integrado chamado [registro de contêiner OpenShift (OCR)](https://docs.openshift.com/container-platform/4.6/registry/architecture-component-imageregistry.html) que adiciona a capacidade de provisionar automaticamente novos repositórios de imagem sob demanda. Isso fornece aos usuários um local interno para que suas compilações de aplicativo enviem por push as imagens resultantes.

Neste artigo, você configurará o registro de imagem de contêiner interno para um cluster do Azure Red Hat OpenShift (toa) 4. Você aprenderá a:

> [!div class="checklist"]
> * Configurar o Azure AD
> * Configurar o OpenID Connect
> * Acessar o registro de imagem de contêiner interno

## <a name="before-you-begin"></a>Antes de começar

Este artigo assumiu que você tem um cluster de ARO existente. Se você precisar de um cluster toa, consulte o tutorial toa, [criar um cluster do Azure Red Hat OpenShift 4](./tutorial-create-cluster.md). Certifique-se de criar o cluster com o `--pull-secret` argumento para `az aro create` .  Isso é necessário para configurar Azure Active Directory autenticação e o registro de contêiner interno.

Depois de ter o cluster, conecte-se ao cluster seguindo as etapas em [conectar-se a um cluster do Azure Red Hat OpenShift 4](./tutorial-connect-cluster.md).
   * Certifique-se de seguir as etapas em "instalar a CLI do OpenShift", pois usaremos o `oc` comando mais adiante neste artigo.
   * Anote a URL do console de cluster, que se parece com `https://console-openshift-console.apps.<random>.<region>.aroapp.io/` . Os valores para `<random>` e `<region>` serão usados posteriormente neste artigo.
   * Observe as `kubeadmin` credenciais. Eles também serão usados posteriormente neste artigo.

### <a name="configure-azure-active-directory-authentication"></a>Configurar a autenticação do Azure Active Directory 

O Azure Active Directory (Azure AD) implementa o OpenID Connect (OIDC). O OIDC permite que você use o Azure AD para entrar no cluster toa. Siga as etapas em [Configurar a autenticação Azure Active Directory](configure-azure-ad-cli.md) para configurar o cluster.

## <a name="access-the-built-in-container-image-registry"></a>Acessar o registro de imagem de contêiner interno

Agora que você configurou os métodos de autenticação para o cluster toa, vamos habilitar o acesso ao registro interno.

#### <a name="define-the-azure-ad-user-to-be-an-administrator"></a>Definir o usuário do Azure AD como administrador

1. Entre no console Web do OpenShift do seu navegador usando as credenciais de um usuário do Azure AD. Aproveitaremos a autenticação do OpenShift OpenID em relação ao Azure Active Directory usar o OpenID para definir o administrador.

   1. Use um recurso de janela de navegador InPrivate, Incognito ou outros equivalentes para entrar no console. A janela terá uma aparência diferente depois de ter habilitado OIDC.
   
   :::image type="content" source="media/built-in-container-registry/oidc-enabled-login-window.png" alt-text="Janela de entrada habilitada para OpenID Connect.":::
   1. Selecionar **OpenID**

   > [!NOTE]
   > Anote o nome de usuário e a senha que você usa para entrar aqui. Esse nome de usuário e senha funcionarão como administrador para outras ações neste e em outros artigos.
2. Entre com a CLI do OpenShift usando as etapas a seguir.  Para discussão, esse processo é conhecido como `oc login` .
   1. Na parte superior direita do console Web, expanda o menu de contexto do usuário conectado e selecione o **comando copiar logon**.
   2. Entre em uma nova janela de guia com o mesmo usuário, se necessário.
   3. Selecione o **token de exibição**.
   4. Copie o valor listado abaixo de **fazer logon com esse token** na área de transferência e execute-o em um shell, como mostrado aqui.

       ```bash
       oc login --token=XOdASlzeT7BHT0JZW6Fd4dl5EwHpeBlN27TAdWHseob --server=https://api.aqlm62xm.rnfghf.aroapp.io:6443
       Logged into "https://api.aqlm62xm.rnfghf.aroapp.io:6443" as "kube:admin" using the token provided.

       You have access to 57 projects, the list has been suppressed. You can list all projects with 'oc projects'

       Using project "default".
       ```

3. Execute `oc whoami` no console do e anote a saída como **\<aad-user>** .  Usaremos esse valor posteriormente neste artigo.
4. Saia do console Web do OpenShift. Selecione o botão no canto superior direito da janela do navegador rotulado como o **\<aad-user>** e escolha **fazer logoff**.


#### <a name="grant-the-azure-ad-user-the-necessary-roles-for-registry-interaction"></a>Conceder ao usuário do Azure AD as funções necessárias para a interação do registro

1. Entre no console Web do OpenShift no seu navegador usando as `kubeadmin` credenciais.
1. Entre na CLI do OpenShift com o token para `kubeadmin` , seguindo as etapas `oc login` acima, mas faça isso depois de entrar no console Web com `kubeadmin` .
1. Execute os comandos a seguir para habilitar o acesso ao registro interno para o usuário do **AAD**.

   ```bash
   # Switch to project "openshift-image-registry"
   oc project openshift-image-registry
   
   # Output should look similar to the following.
   # Now using project "openshift-image-registry" on server "https://api.x8xl3f4y.eastus.aroapp.io:6443".
   ```

   ```bash
   # Expose the registry using "DefaultRoute"
   oc patch configs.imageregistry.operator.openshift.io/cluster --patch '{"spec":{"defaultRoute":true}}' --type=merge

   # Output should look similar to the following.
   # config.imageregistry.operator.openshift.io/cluster patched
   ```

   ```bash
   # Add roles to "aad-user" for pulling and pushing images
   # Note: replace "<aad-user>" with the one you wrote down before
   oc policy add-role-to-user registry-viewer <aad-user>

   # Output should look similar to the following.
   # clusterrole.rbac.authorization.k8s.io/registry-viewer added: "kaaIjx75vFWovvKF7c02M0ya5qzwcSJ074RZBfXUc34"
   ```

   ```bash
   oc policy add-role-to-user registry-editor <aad-user>
   # Output should look similar to the following.
   # clusterrole.rbac.authorization.k8s.io/registry-editor added: "kaaIjx75vFWovvKF7c02M0ya5qzwcSJ074RZBfXUc34"
   ```

#### <a name="obtain-the-container-registry-url"></a>Obter a URL do registro de contêiner

Use o `oc get route` comando conforme mostrado ao lado de obter a URL do registro de contêiner.

```bash
# Note: the value of "Container Registry URL" in the output is the fully qualified registry name.
HOST=$(oc get route default-route --template='{{ .spec.host }}')
echo "Container Registry URL: $HOST"
```

   > [!NOTE]
   > Observe a saída do console da **URL do registro de contêiner**. Ele será usado como o nome totalmente qualificado do registro para este guia e os subsequentes.

## <a name="next-steps"></a>Próximas etapas

Agora que você configurou o registro de imagem de contêiner interno, você pode começar implantando um aplicativo no OpenShift. Para aplicativos Java, confira [implantar um aplicativo Java com o Liberty/WebSphere Liberty aberto em um cluster do Azure Red Hat OpenShift 4](howto-deploy-java-liberty-app.md).