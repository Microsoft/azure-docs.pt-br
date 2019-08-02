---
title: Configurar seu ambiente de desenvolvimento do Azure Red Hat OpenShift | Microsoft Docs
description: Aqui estão os pré-requisitos para trabalhar com o Microsoft Azure Red Hat OpenShift.
services: openshift
keywords: configuração do Red hat openshift configurar
author: jimzim
ms.author: jzim
ms.date: 05/10/2019
ms.topic: conceptual
ms.service: container-service
manager: jeconnoc
ms.openlocfilehash: a31655e8c8805505bdcc5e90bf25191590d35c18
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672512"
---
# <a name="set-up-your-azure-red-hat-openshift-dev-environment"></a>Configurar seu ambiente de desenvolvimento do Red Hat OpenShift no Azure

Para compilar e executar aplicativos do Microsoft Azure Red Hat OpenShift, você precisará:

* Comprar instâncias reservada de máquina de virtual do Azure.
* Instalar versão 2.0.65 (ou superior) da CLI do Azure (ou use o Azure Cloud Shell).
* Inscreva-se a `AROGA` recurso e provedores de recursos associados.
* Crie um locatário do Azure Active Directory (Azure AD).
* Crie um objeto de aplicativo do Azure AD.
* Crie um usuário do AD do Azure.

As instruções a seguir orientará você por meio de todos esses pré-requisitos.

## <a name="purchase-azure-red-hat-openshift-application-nodes-reserved-instances"></a>Comprar instâncias reservadas de nós de aplicativo do Azure Red Hat OpenShift

Antes de usar Azure Red Hat OpenShift, você precisará comprar um mínimo de 4 nós de aplicativo do Azure Red Hat OpenShift reservados, após o qual você poderá provisionar clusters.

Se você for um cliente do Azure, [comprar instâncias reservadas do Azure Red Hat OpenShift](https://aka.ms/openshift/buy) por meio do portal do Azure. Após a compra, sua assinatura será ativada dentro de 24 horas.

Se você não for um cliente do Azure, [entre em contato com vendas](https://aka.ms/openshift/contact-sales) e preencha o formulário na parte inferior da página para iniciar o processo de venda.

Consulte a [página de preços do Azure Red Hat OpenShift](https://aka.ms/openshift/pricing) para obter mais informações.

## <a name="install-the-azure-cli"></a>Instalar a CLI do Azure

Azure Red Hat OpenShift requer a versão 2.0.65 ou posterior da CLI do Azure. Se você já tiver instalado a CLI do Azure, você pode verificar qual versão você possui, executando:

```bash
az --version
```

A primeira linha de saída terá a versão da CLI, por exemplo `azure-cli (2.0.65)`.

Aqui estão as instruções para [instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) se você precisar de uma nova instalação ou atualização.

Como alternativa, você pode usar o [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview). Ao usar o Azure Cloud Shell, certifique-se de selecionar o **Bash** ambiente se você planeja seguir junto com as [criar e gerenciar um cluster do Azure Red Hat OpenShift](tutorial-create-cluster.md) série de tutoriais.

## <a name="register-providers-and-features"></a>Registrar os provedores e recursos

O `Microsoft.ContainerService AROGA` recurso `Microsoft.Solutions`, `Microsoft.Compute`, `Microsoft.Storage`, `Microsoft.KeyVault` e `Microsoft.Network` provedores devem ser registrados para sua assinatura manualmente antes de implantar seu primeiro cluster do Azure Red Hat OpenShift.

Para registrar esses provedores e os recursos manualmente, use as instruções a seguir de um shell Bash, se você tiver instalado a CLI ou da sessão do Azure Cloud Shell (Bash) no portal do Azure:

1. Se você tiver várias assinaturas do Azure, especifique a ID de assinatura relevante:

    ```bash
    az account set --subscription <SUBSCRIPTION ID>
    ```

1. Registre o recurso do containerservice AROGA:

    ```bash
    az feature register --namespace Microsoft.ContainerService -n AROGA
    ```

1. Registre o provedor Microsoft. Storage:

    ```bash
    az provider register -n Microsoft.Storage --wait
    ```
    
1. Registre o provedor Microsoft. Compute:

    ```bash
    az provider register -n Microsoft.Compute --wait
    ```

1. Registre o provedor Microsoft.Solutions:

    ```bash
    az provider register -n Microsoft.Solutions --wait
    ```

1. Registre o provedor Microsoft. Network:

    ```bash
    az provider register -n Microsoft.Network --wait
    ```

1. Registre o provedor Microsoft. keyvault:

    ```bash
    az provider register -n Microsoft.KeyVault --wait
    ```

1. Atualize o registro do provedor de recurso do containerservice:

    ```bash
    az provider register -n Microsoft.ContainerService --wait
    ```

## <a name="create-an-azure-active-directory-azure-ad-tenant"></a>Criar um locatário do Azure Active Directory (Azure AD)

O serviço do Azure Red Hat OpenShift requer um locatário do Azure AD (Active Directory do Azure) associado que representa a sua organização e sua relação para a Microsoft. Locatário do Azure AD permite que você se registrar, compilar e gerenciar aplicativos, bem como usar outros serviços do Azure.

Se você não tiver um Azure AD para usar como o locatário para seu cluster do Azure Red Hat OpenShift, ou você deseja criar um locatário para teste, siga as instruções em [criar um locatário do Azure AD para seu cluster do Azure Red Hat OpenShift](howto-create-tenant.md) antes continuar com este guia.

## <a name="create-an-azure-ad-user-security-group-and-application-object"></a>Criar um usuário, grupo de segurança e aplicativo do Azure AD objeto

Azure Red Hat OpenShift requer permissões para executar tarefas em seu cluster, como a configuração de armazenamento. Essas permissões são representadas por meio de um [entidade de serviço](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object). Você também desejará criar um novo usuário do Active Directory para testar aplicativos em execução no cluster do Azure Red Hat OpenShift.

Siga as instruções em [criar um objeto de aplicativo do Azure AD e o usuário](howto-aad-app-configuration.md) para criar uma entidade de serviço, gerar um segredo e a autenticação de retorno de chamada URL do cliente para seu aplicativo e crie um novo grupo de segurança do Azure AD e o usuário para acessar o cluster.

## <a name="next-steps"></a>Próximas etapas

Agora você está pronto para usar o Azure Red Hat OpenShift!

Experimente o tutorial:
> [!div class="nextstepaction"]
> [Criar um cluster do Red Hat OpenShift no Azure](tutorial-create-cluster.md)

[azure-cli-install]: https://docs.microsoft.com/cli/azure/install-azure-cli
