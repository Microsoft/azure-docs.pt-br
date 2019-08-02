---
title: Criar um cluster Pivotal Cloud Foundry no Azure
description: Saiba como configurar os parâmetros necessários para provisionar um cluster de PCF (Pivotal Cloud Foundry) no Azure
services: Cloud Foundry
documentationcenter: CloudFoundry
author: ruyakubu
manager: brunoborges
editor: ruyakubu
ms.assetid: ''
ms.author: ruyakubu
ms.date: 09/13/2018
ms.devlang: ''
ms.service: azure
ms.tgt_pltfrm: multiple
ms.topic: tutorial
ms.workload: web
ms.openlocfilehash: f5ae599b516ac3ce6a9fcc40c0e26d242134e7d7
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2019
ms.locfileid: "68226628"
---
# <a name="create-a-pivotal-cloud-foundry-cluster-on-azure"></a>Criar um cluster Pivotal Cloud Foundry no Azure

Este tutorial fornece etapas rápidas para criar e gerar os parâmetros necessários para provisionar um cluster de PCF (Pivotal Cloud Foundry) no Azure. Para localizar a solução Pivotal Cloud Foundry, realize uma pesquisa no Azure [Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry).

![Pesquisar o Pivotal Cloud Foundry no Azure](media/deploy/pcf-marketplace.png)


## <a name="generate-an-ssh-public-key"></a>Gerar uma chave pública SSH

Há várias maneiras de gerar uma chave SSH (secure shell) pública usando o Windows, Mac ou Linux.

```Bash
ssh-keygen -t rsa -b 2048
```

Para saber mais, confira [Use SSH keys with Windows on Azure](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows) (Usar chaves SSH com o Windows no Azure).

## <a name="create-a-service-principal"></a>Criar uma entidade de serviço

> [!NOTE]
>
> Para criar uma entidade de serviço, é necessária uma permissão de conta de proprietário. Também é possível escrever um script para automatizar a criação da entidade de serviço. Por exemplo, é possível usar [az ad sp create-for-rbac](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest) da CLI do Azure.

1. Entre na sua conta do Azure.

    `az login`

    ![Logon da CLI do Azure](media/deploy/az-login-output.png )
 
    Copie o valor da "id" como sua **ID de assinatura** e o valor de “tenantId” para ser usado mais tarde.

2. Defina sua assinatura padrão para essa configuração.

    `az account set -s {id}`

3. Criar um aplicativo Azure Active Directory para seu PCF. Especifique uma senha alfanumérica exclusiva. Armazene a senha como **clientSecret** para ser usada posteriormente.

    `az ad app create --display-name "Svc Principal for OpsManager" --password {enter-your-password} --homepage "{enter-your-homepage}" --identifier-uris {enter-your-homepage}`

    Copie o valor de "appId" na saída como sua **clientID** para ser usado posteriormente.

    > [!NOTE]
    >
    > Escolha sua própria página inicial de aplicativo e URI do identificador, por exemplo, http\://www\.contoso.com.

4. Crie uma entidade de serviço com a nova ID do aplicativo.

    `az ad sp create --id {appId}`

5. Defina a função de permissão da sua entidade de serviço como um Colaborador.

    `az role assignment create --assignee “{enter-your-homepage}” --role “Contributor”`

    Ou também é possível usar

    `az role assignment create --assignee {service-principal-name} --role “Contributor”`

    ![Atribuição de função da entidade de serviço](media/deploy/svc-princ.png )

6. Verifique se é possível entrar com êxito em sua entidade de serviço usando a ID do aplicativo, a senha e a ID do locatário.

    `az login --service-principal -u {appId} -p {your-password}  --tenant {tenantId}`

7. Crie um arquivo .json com o seguinte formato. Use os valores de **ID da assinatura**, **tenantID**, **clientID** e **clientSecret** que você copiou anteriormente. Salve o arquivo.

    ```json
    {
        "subscriptionID": "{enter-your-subscription-Id-here}",
        "tenantID": "{enter-your-tenant-Id-here}",
        "clientID": "{enter-your-app-Id-here}",
        "clientSecret": "{enter-your-key-here}"
    }
    ```

## <a name="get-the-pivotal-network-token"></a>Obter o token do Pivotal Network

1. Registre-se ou entre em sua conta do [Pivotal Network](https://network.pivotal.io).
2. Selecione o nome do perfil no canto superior direito da página. Selecione **Editar perfil**.
3. Role até a parte inferior da página e copie o valor **LEGACY API TOKEN**. Esse valor é o do **Token do Pivotal Network** para ser usado posteriormente.

## <a name="provision-your-cloud-foundry-cluster-on-azure"></a>Provisionar o cluster do Cloud Foundry no Azure

Agora você tem todos os parâmetros necessários para provisionar o cluster do [Pivotal Cloud Foundry no Azure](https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry).
Insira os parâmetros e crie o cluster do PCF.

## <a name="verify-the-deployment-and-sign-in-to-the-pivotal-ops-manager"></a>Verificar a implantação e entrar no Pivotal Ops Manager

1. O cluster do PCF mostra um status de implantação.

    ![Status da implantação do Azure](media/deploy/deployment.png )

2. Selecione o link **Implantações** na navegação à esquerda para obter as credenciais para o PCF Ops Manager. Selecione o **Nome da implantação** na próxima página.
3. Na navegação à esquerda, selecione o link **Saídas** para exibir a URL, o nome de usuário e a senha do PCF Ops Manager. O valor de “OPSMAN-FQDN” é a URL.
 
    ![Saída da implantação do Cloud Foundry](media/deploy/deploy-outputs.png )
 
4. Inicie a URL em um navegador da Web. Insira as credenciais da etapa anterior para entrar.

    ![Página de entrada do Pivotal](media/deploy/pivotal-login.png )
         
    > [!NOTE]
    >
    > Se o navegador Internet Explorer falhar devido a uma mensagem de aviso “Site não seguro”, selecione **Mais informações** e acesse a página da Web. Para o Firefox, selecione **Avançar** e adicione a certificação para continuar.

5. O PCF Ops Manager exibe as instâncias implantadas do Azure. Agora é possível implantar e gerenciar seus aplicativos aqui.
               
    ![Instância implantada do Azure no Pivotal](media/deploy/ops-mgr.png )
 
