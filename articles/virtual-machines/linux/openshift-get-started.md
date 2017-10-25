---
title: Implantar origem OpenShift no Azure | Microsoft Docs
description: "Saiba como implantar o OpenShift Origin em máquinas virtuais do Azure."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: jbinder
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 
ms.author: jbinder
ms.openlocfilehash: e03da05625e440eab29ccc28a2343d3433fc7607
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-openshift-origin-to-azure-virtual-machines"></a>Implantar o OpenShift Origin mas Máquinas Virtuais do Azure 

[OpenShift Origin](https://www.openshift.org/) é uma plataforma de contêiner de software livre criada em [Kubernetes](https://kubernetes.io/). Simplifica o processo de implantação, dimensionamento e operação de aplicativos multilocatário. 

Este guia descreve como implantar o OpenShift Origin em Máquinas Virtuais do Azure usando a CLI do Azure e os Modelos do Azure Resource Manager. Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> * Crie um KeyVault para gerenciar chaves SSH para o cluster OpenShift.
> * Implante um cluster OpenShift em VMs do Azure. 
> * Instale e configure a [CLI do OpenShift](https://docs.openshift.org/latest/cli_reference/index.html#cli-reference-index) para gerenciar o cluster.
> * Personalize a implantação de OpenShift.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

Este início rápido requer a CLI do Azure versão 2.0.8 ou posterior. Para saber qual é a versão, execute `az --version`. Se você precisa instalar ou atualizar, consulte [Instalar a CLI 2.0 do Azure]( /cli/azure/install-azure-cli). 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="log-in-to-azure"></a>Fazer logon no Azure 
Faça logon na sua assinatura do Azure com o comando [az login](/cli/azure/#login) e siga as instruções na tela ou clique em **Experimentar** para usar o Cloud Shell.

```azurecli 
az login
```
## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com o comando [az group create](/cli/azure/group#create). Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. 

O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* no local *eastus*.

```azurecli 
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-key-vault"></a>Criar um cofre de chaves
Crie um KeyVault para armazenar as chaves de SSH para o cluster com o comando [keyvault az creata](/cli/azure/keyvault#create).  

```azurecli 
az keyvault create --resource-group myResourceGroup --name myKeyVault \
       --enabled-for-template-deployment true \
       --location eastus
```

## <a name="create-an-ssh-key"></a>Criar uma chave SSH 
Uma chave SSH é necessária para proteger o acesso ao cluster de origem OpenShift. Crie um par de chaves SSH usando o comando `ssh-keygen`. 
 
 ```bash
ssh-keygen -f ~/.ssh/openshift_rsa -t rsa -N ''
```

> [!NOTE]
> O par de chaves SSH que você cria não deve ter uma senha.

Para obter mais informações sobre as chaves SSH no Windows, confira [Como criar chaves SSH no Windows](/azure/virtual-machines/linux/ssh-from-windows).

## <a name="store-ssh-private-key-in-key-vault"></a>Armazenar chave privada SSH no Key Vault
A implantação de OpenShift usa a chave SSH criada para proteger o acesso ao mestre OpenShift. Para habilitar a implantação a recuperar a chave SSH com segurança, armazene a chave no Key Vault usando o comando a seguir.

# <a name="enabled-for-template-deployment"></a>Habilitado para implantação de modelo
```azurecli
az keyvault secret set --vault-name KeyVaultName --name OpenShiftKey --file ~/.ssh/openshift.rsa
```

## <a name="create-a-service-principal"></a>Criar uma entidade de serviço 
O OpenShift se comunica com o Azure usando um nome de usuário e i,a senha ou uma entidade de serviço. Uma entidade de serviço do Azure é uma identidade de segurança que você pode usar com aplicativos, serviços e ferramentas de automação como o OpenShift. Você controla e define as permissões referentes a quais operações a entidade de serviço pode executar no Azure. Para aprimorar a segurança, em vez de apenas fornecer um nome de usuário e uma senha, este exemplo cria uma entidade de serviço básica.

Crie uma entidade de serviço com [az ad sp create-for-rbac](/cli/azure/ad/sp#create-for-rbac) e gere as credenciais necessárias para o OpenShift:

```azurecli
az ad sp create-for-rbac --name openshiftsp \
          --role Contributor --password {strong password} \
          --scopes $(az group show --name myResourceGroup --query id)
```
Anote a propriedade appId retornada do comando.
```json
{
  "appId": "a487e0c1-82af-47d9-9a0b-af184eb87646d",
  "displayName": "openshiftsp",
  "name": "http://openshiftsp",
  "password": {strong password},
  "tenant": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX"
}
```
 > [!WARNING] 
 > Não crie uma senha não segura.  Execute a orientação [Restrições e regras de senha do Azure AD](/azure/active-directory/active-directory-passwords-policy).

Para obter mais informações sobre entidades de serviço, confira [Criar entidade de serviço do Azure com a CLI 2.0 do Azure](/cli/azure/create-an-azure-service-principal-azure-cli)

## <a name="deploy-the-openshift-origin-template"></a>Implante o modelo de origem OpenShift
Em seguida, implante o OpenShift Origin usando um modelo do Azure Resource Manager. 

> [!NOTE] 
> O comando a seguir requer az CLI 2.0.8 ou posterior. Você pode verificar a versão de az CLI com o comando `az --version`. Para atualizar a versão da CLI, confira [Instalar a CLI do Azure 2.0]( /cli/azure/install-azure-cli).

Use o valor `appId` da entidade de serviço que você criou anteriormente para o parâmetro `aadClientId`.

```azurecli 
az group deployment create --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-origin/master/azuredeploy.json \
      --params \ 
        openshiftMasterPublicIpDnsLabel=myopenshiftmaster \
        infraLbPublicIpDnsLabel=myopenshiftlb \
        openshiftPassword=Pass@word!
        sshPublicKey=~/.ssh/openshift_rsa.pub \
        keyVaultResourceGroup=myResourceGroup \
        keyVaultName=myKeyVault \
        keyVaultSecret=OpenShiftKey \
        aadClientId={appId} \
        aadClientSecret={strong password} 
```
A implantação pode levar até 20 minutos para ser concluída. A URL do console openShift e o nome DNS do mestre OpenShift são impressas no terminal quando a implantação é concluída.

```json
{
  "OpenShift Console Uri": "http://openshiftlb.cloudapp.azure.com:8443/console",
  "OpenShift Master SSH": "ocpadmin@myopenshiftmaster.cloudapp.azure.com"
}
```
## <a name="connect-to-the-openshift-cluster"></a>Conectar-se ao cluster OpenShift
Quando a implantação for concluída, conecte-se ao console do OpenShift com o navegador usando o `OpenShift Console Uri`. Como alternativa, você pode se conectar ao mestre de OpenShift usando o comando a seguir.

```bash
$ ssh ocpadmin@myopenshiftmaster.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>Limpar recursos
Quando não for mais necessário, você pode usar o comando [az group delete](/cli/azure/group#delete) para remover o grupo de recursos, o cluster OpenShift e todos os recursos relacionados.

```azurecli 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, vimos como:
> [!div class="checklist"]
> * Crie um KeyVault para gerenciar chaves SSH para o cluster OpenShift.
> * Implante um cluster OpenShift em VMs do Azure. 
> * Instale e configure a [CLI do OpenShift](https://docs.openshift.org/latest/cli_reference/index.html#cli-reference-index) para gerenciar o cluster.

Agora esse cluster de origem OpenShift é implantado. Você pode seguir os tutoriais do OpenShift para aprender a implantar seu primeiro aplicativo e usar as ferramentas do OpenShift. Confira [Introdução ao OpenShift Origin](https://docs.openshift.org/latest/getting_started/index.html) para começar. 
