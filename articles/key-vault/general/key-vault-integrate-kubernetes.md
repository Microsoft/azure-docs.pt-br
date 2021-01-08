---
title: Integrar o Azure Key Vault ao Kubernetes
description: Neste tutorial, você acessa o Azure Key Vault e recupera segredos dele usando o driver da Secrets Store CSI (Container Storage Interface, Interface de armazenamento de contêiner) para, depois, montar em pods do Kubernetes.
author: ShaneBala-keyvault
ms.author: sudbalas
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 09/25/2020
ms.openlocfilehash: 2645842130b83fe7b4cfb33b9389b19a1306506d
ms.sourcegitcommit: 90caa05809d85382c5a50a6804b9a4d8b39ee31e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/23/2020
ms.locfileid: "97756017"
---
# <a name="tutorial-configure-and-run-the-azure-key-vault-provider-for-the-secrets-store-csi-driver-on-kubernetes"></a>Tutorial: Configurar e executar o provedor do Azure Key Vault para o driver da Secrets Store CSI no Kubernetes

> [!IMPORTANT]
> O Driver do CSI é um projeto de software livre que não tem suporte do suporte técnico do Azure. Informe todos os comentários e problemas relacionados à integração do Key Vault do Driver do CSI no link do GitHub na parte inferior da página. Essa ferramenta é fornecida para que os usuários se instalem em clusters e obtenham comentários de nossa comunidade.

Neste tutorial, você acessa o Azure Key Vault e recupera segredos dele usando o driver da Secrets Store CSI (Container Storage Interface, Interface de armazenamento de contêiner) para, depois, montar os segredos em pods do Kubernetes.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar uma entidade de serviço ou usar identidades gerenciadas.
> * Implantar um cluster do AKS (Serviço de Kubernetes do Azure) usando a CLI do Azure.
> * Instalar o Helm e driver da Secrets Store CSI.
> * Criar um Azure Key Vault e definir os segredos.
> * Criar seu próprio objeto SecretProviderClass.
> * Atribuir sua entidade de serviço ou usar identidades gerenciadas.
> * Implantar seu pod com segredos montados do cofre de chaves.

## <a name="prerequisites"></a>Pré-requisitos

* Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

* Antes de iniciar este tutorial, instale a [CLI do Azure](/cli/azure/install-azure-cli-windows?view=azure-cli-latest).

## <a name="create-a-service-principal-or-use-managed-identities"></a>Criar uma entidade de serviço ou usar identidades gerenciadas

Se você planeja usar identidades gerenciadas, pode passar para a próxima seção.

Crie uma entidade de serviço para controlar quais recursos podem ser acessados de seu Azure Key Vault. O acesso da entidade de serviço é restrito pelas funções atribuídas a ela. Esse recurso lhe dá controle sobre como a entidade de serviço pode gerenciar seus segredos. No exemplo a seguir, o nome da entidade de serviço é *contosoServicePrincipal*.

```azurecli
az ad sp create-for-rbac --name contosoServicePrincipal --skip-assignment
```
Essa operação retorna uma série de pares de chave/valor:

![Captura de tela mostrando a appId e a senha de contosoServicePrincipal](../media/kubernetes-key-vault-1.png)

Copie as credenciais de **appId** e **senha** para uso posterior.

## <a name="flow-for-using-managed-identity"></a>Fluxo para uso da identidade gerenciada

Este diagrama ilustra o fluxo de integração entre o AKS e Key Vault para a identidade gerenciada:

![Diagrama que mostra o fluxo de integração entre o AKS e o Key Vault para a identidade gerenciada](../media/aks-key-vault-integration-flow.png)

## <a name="deploy-an-azure-kubernetes-service-aks-cluster-by-using-the-azure-cli"></a>Implantar um cluster do AKS (Serviço de Kubernetes do Azure) usando a CLI do Azure

Você não precisa usar o Azure Cloud Shell. O prompt de comando (terminal) com a CLI do Azure instalada será suficiente. 

Conclua as seções "Criar um grupo de recursos", "Criar cluster do AKS" e "Conectar-se ao cluster" em [Implantar um cluster do Serviço de Kubernetes do Azure usando a CLI do Azure](../../aks/kubernetes-walkthrough.md). 

> [!NOTE] 
> Se você planeja usar uma identidade de pod em vez de uma entidade de serviço, não deixe de habilitá-la ao criar o cluster do Kubernetes, conforme mostrado no seguinte comando:
>
> ```azurecli
> az aks create -n contosoAKSCluster -g contosoResourceGroup --kubernetes-version 1.16.9 --node-count 1 --enable-managed-identity
> ```

1. [Defina a variável de ambiente PATH](https://www.java.com/en/download/help/path.xml) como o arquivo *kubectl.exe* que foi baixado.
1. Verifique sua versão do Kubernetes usando o comando a seguir, que gera a versão do cliente e do servidor. A versão do cliente é o arquivo *kubectl.exe* que você instalou, e a versão do servidor é o AKS (Serviço de Kubernetes do Azure) em que o cluster está sendo executado.
    ```azurecli
    kubectl version
    ```
1. Sua versão do Kubernetes deve ser a 1.16.0 ou superior. Para clusters do Windows, a sua versão do Kubernetes deve ser a 1.18.0 ou superior. O comando a seguir atualiza o cluster do Kubernetes e o pool de nós. O comando pode levar alguns minutos para ser executado. Neste exemplo, o grupo de recursos é *contosoResourceGroup* e o cluster do Kubernetes é *contosoAKSCluster*.
    ```azurecli
    az aks upgrade --kubernetes-version 1.16.9 --name contosoAKSCluster --resource-group contosoResourceGroup
    ```
1. Para exibir os metadados do cluster do AKS que você criou, use o comando a seguir. Copie a **principalId**, a **clientId**, **a subscriptionId** e o **nodeResourceGroup** para uso posterior. Caso o cluster do AKS não seja criado com identidades gerenciadas habilitadas, a **principalId** e a **clientId** serão nulas. 

    ```azurecli
    az aks show --name contosoAKSCluster --resource-group contosoResourceGroup
    ```

    A saída mostra os dois parâmetros realçados:
    
    ![Captura de tela da CLI do Azure com os valores de principalId e clientId realçados](../media/kubernetes-key-vault-2.png) ![Captura de tela da CLI do Azure com os valores de subscriptionId e nodeResourceGroup realçados](../media/kubernetes-key-vault-3.png)
    
## <a name="install-helm-and-the-secrets-store-csi-driver"></a>Instalar o Helm e driver da Secrets Store CSI

Para instalar o driver da Secrets Store CSI, primeiro você precisa instalar o [Helm](https://helm.sh/docs/intro/install/).

Com a interface do driver da [Secrets Store CSI](https://github.com/Azure/secrets-store-csi-driver-provider-azure/blob/master/charts/csi-secrets-store-provider-azure/README.md), você pode obter os segredos armazenados em sua instância do Azure Key Vault e usar a interface do driver para montar esses conteúdos secretos em pods do Kubernetes.

1. Verifique se a versão do Helm é a v3 ou posterior:
    ```azurecli
    helm version
    ```
1. Instale o driver do Secrets Store CSI e o provedor do Azure Key Vault para o driver:
    ```azurecli
    helm repo add csi-secrets-store-provider-azure https://raw.githubusercontent.com/Azure/secrets-store-csi-driver-provider-azure/master/charts

    helm install csi-secrets-store-provider-azure/csi-secrets-store-provider-azure --generate-name
    ```

## <a name="create-an-azure-key-vault-and-set-your-secrets"></a>Criar um Azure Key Vault e definir os segredos

Para criar seu próprio cofre de chaves e definir seus segredos, siga as instruções em [Definir e recuperar um segredo do Azure Key Vault usando a CLI do Azure](../secrets/quick-create-cli.md).

> [!NOTE] 
> Você não precisa usar o Azure Cloud Shell nem criar um grupo de recursos. Use o grupo de recursos criado anteriormente para o cluster do Kubernetes.

## <a name="create-your-own-secretproviderclass-object"></a>Criar seu próprio objeto SecretProviderClass

Para criar seu próprio objeto SecretProviderClass personalizado com parâmetros específicos do provedor para o driver da Secrets Store CSI, [use este modelo](https://github.com/Azure/secrets-store-csi-driver-provider-azure/blob/master/examples/v1alpha1_secretproviderclass_service_principal.yaml). Esse objeto fornecerá acesso de identidade ao seu cofre de chaves.

No arquivo YAML SecretProviderClass de exemplo, preencha os parâmetros ausentes. Os seguintes parâmetros são necessários:

* **userAssignedIdentityID**: # [OBRIGATÓRIO] se você estiver usando uma entidade de serviço, use a ID do cliente para especificar qual identidade gerenciada atribuída pelo usuário deverá ser usada. Se você estiver usando uma identidade atribuída pelo usuário como a identidade gerenciada da VM, especifique a ID do cliente da identidade. Se o valor estiver vazio, o padrão será usar a identidade atribuída pelo sistema na VM 
* **keyvaultName**: o nome de seu cofre de chaves
* **objects**: o conteúdo com todo o conteúdo secreto que você deseja montar
    * **objectName**: o nome do conteúdo secreto
    * **objectType**: o tipo de objeto (segredo, chave, certificado)
* **resourceGroup**: O nome do grupo de recursos. # [OBRIGATÓRIO para versões < 0.0.4] O grupo de recursos do KeyVault
* **subscriptionId**: A ID da assinatura do cofre de chaves. # [OBRIGATÓRIO para versões < 0.0.4] A ID da assinatura do KeyVault
* **tenantID**: a ID do locatário ou a ID do diretório de seu cofre de chaves

A documentação de todos os campos obrigatórios está disponível aqui: [Link](https://github.com/Azure/secrets-store-csi-driver-provider-azure#create-a-new-azure-key-vault-resource-or-use-an-existing-one)

O modelo atualizado é mostrado no código a seguir. Baixe-o como um arquivo YAML e preencha os campos obrigatórios. Neste exemplo, o cofre de chaves é **contosoKeyVault5**. Ele tem dois segredos, **secret1** e **secret2**.

> [!NOTE] 
> Se estiver usando identidades gerenciadas, defina o valor de **usePodIdentity** como *true* e o valor de **userAssignedIdentityID** como um par de aspas ( **""** ). 

```yaml
apiVersion: secrets-store.csi.x-k8s.io/v1alpha1
kind: SecretProviderClass
metadata:
  name: azure-kvname
spec:
  provider: azure
  parameters:
    usePodIdentity: "false"                   # [REQUIRED] Set to "true" if using managed identities
    useVMManagedIdentity: "false"             # [OPTIONAL] if not provided, will default to "false"
    userAssignedIdentityID: "servicePrincipalClientID"       # [REQUIRED] If you're using a service principal, use the client id to specify which user-assigned managed identity to use. If you're using a user-assigned identity as the VM's managed identity, specify the identity's client id. If the value is empty, it defaults to use the system-assigned identity on the VM
                                                             #     az ad sp show --id http://contosoServicePrincipal --query appId -o tsv
                                                             #     the preceding command will return the client ID of your service principal
    keyvaultName: "contosoKeyVault5"          # [REQUIRED] the name of the key vault
                                              #     az keyvault show --name contosoKeyVault5
                                              #     the preceding command will display the key vault metadata, which includes the subscription ID, resource group name, key vault 
    cloudName: ""                                # [OPTIONAL for Azure] if not provided, Azure environment will default to AzurePublicCloud
    objects:  |
      array:
        - |
          objectName: secret1                 # [REQUIRED] object name
                                              #     az keyvault secret list --vault-name "contosoKeyVault5"
                                              #     the above command will display a list of secret names from your key vault
          objectType: secret                  # [REQUIRED] object types: secret, key, or cert
          objectVersion: ""                   # [OPTIONAL] object versions, default to latest if empty
        - |
          objectName: secret2
          objectType: secret
          objectVersion: ""
    resourceGroup: "contosoResourceGroup"     # [REQUIRED] the resource group name of the key vault
    subscriptionId: "subscriptionID"          # [REQUIRED] the subscription ID of the key vault
    tenantId: "tenantID"                      # [REQUIRED] the tenant ID of the key vault
```
A imagem a seguir mostra a saída do console para **az keyvault show --name contosoKeyVault5** com os metadados relevantes realçados:

![Captura de tela mostrando a saída do console para "az keyvault show --name contosoKeyVault5"](../media/kubernetes-key-vault-4.png)

## <a name="assign-your-service-principal-or-use-managed-identities"></a>Atribuir sua entidade de serviço ou usar identidades gerenciadas

### <a name="assign-a-service-principal"></a>Atribuir uma entidade de serviço

Se estiver usando uma entidade de serviço, conceda as permissões para ela acessar seu cofre de chaves e recuperar segredos. Atribua a função de *Leitor* e conceda à entidade de serviço permissões para *obter* segredos do cofre de chaves fazendo o seguinte comando:

1. Atribua a entidade de serviço ao cofre de chaves existente. O parâmetro **$AZURE_CLIENT_ID** é a **appId** que você copiou depois de criar a entidade de serviço.
    ```azurecli
    az role assignment create --role Reader --assignee $AZURE_CLIENT_ID --scope /subscriptions/$SUBID/resourcegroups/$KEYVAULT_RESOURCE_GROUP/providers/Microsoft.KeyVault/vaults/$KEYVAULT_NAME
    ```

    A saída do comando é mostrada na imagem a seguir: 

    ![Captura de tela mostrando o valor de PrincipalId](../media/kubernetes-key-vault-5.png)

1. Conceda à entidade de serviço permissão para obter segredos:
    ```azurecli
    az keyvault set-policy -n $KEYVAULT_NAME --secret-permissions get --spn $AZURE_CLIENT_ID
    az keyvault set-policy -n $KEYVAULT_NAME --key-permissions get --spn $AZURE_CLIENT_ID
    ```

1. Agora, você configurou a entidade de serviço para ter permissões para ler segredos de seu cofre de chaves. O **$AZURE_CLIENT_SECRET** é a senha de sua entidade de serviço. Adicione suas credenciais da entidade de serviço como um segredo do Kubernetes acessível pelo driver da Secrets Store CSI:
    ```azurecli
    kubectl create secret generic secrets-store-creds --from-literal clientid=$AZURE_CLIENT_ID --from-literal clientsecret=$AZURE_CLIENT_SECRET
    ```

> [!NOTE] 
> Se estiver implantando o pod do Kubernetes e receber um erro sobre uma ID de Segredo do Cliente inválida, talvez você tenha uma ID de Segredo do Cliente mais antiga que expirou ou foi redefinida. Para resolver o problema, exclua seu segredo *secrets-store-creds* e crie um com a ID de Segredo do Cliente atual. Para excluir seu *secrets-store-creds*, execute este comando:
>
> ```azurecli
> kubectl delete secrets secrets-store-creds
> ```

Caso tenha esquecido a ID de Segredo do Cliente da entidade de serviço, você poderá redefini-la usando o seguinte comando:

```azurecli
az ad sp credential reset --name contosoServicePrincipal --credential-description "APClientSecret" --query password -o tsv
```

### <a name="use-managed-identities"></a>Usar identidades gerenciadas

Se estiver usando identidades gerenciadas, atribua funções específicas ao cluster do AKS que você criou. 

1. Para criar, listar ou ler uma identidade gerenciada atribuída pelo usuário, seu cluster do AKS precisa ter a função de [Operador de Identidade Gerenciada](../../role-based-access-control/built-in-roles.md#managed-identity-operator). Verifique se a **$clientId** é a do cluster do Kubernetes. Para o escopo, ele estará sob seu serviço de assinatura do Azure, especificamente, o grupo de recursos do nó que foi gerado quando o cluster AKS foi criado. Esse escopo garantirá que apenas os recursos dentro desse grupo sejam afetados pelas funções atribuídas a seguir. 

    ```azurecli
    RESOURCE_GROUP=contosoResourceGroup
    az role assignment create --role "Managed Identity Operator" --assignee $clientId --scope /subscriptions/$SUBID/resourcegroups/$RESOURCE_GROUP
    
    az role assignment create --role "Managed Identity Operator" --assignee $clientId --scope /subscriptions/$SUBID/resourcegroups/$NODE_RESOURCE_GROUP
    
    az role assignment create --role "Virtual Machine Contributor" --assignee $clientId --scope /subscriptions/$SUBID/resourcegroups/$NODE_RESOURCE_GROUP
    ```

1. Instale a identidade do Azure AD (Azure Active Directory) no AKS.
    ```azurecli
    helm repo add aad-pod-identity https://raw.githubusercontent.com/Azure/aad-pod-identity/master/charts

    helm install pod-identity aad-pod-identity/aad-pod-identity
    ```

1. Crie uma identidade do Azure AD. Na saída, copie a **clientId** e a **principalId** para uso posterior.
    ```azurecli
    az identity create -g $resourceGroupName -n $identityName
    ```

1. Atribua a função de *Leitor* à identidade do Azure AD criada na etapa anterior ao cofre de chaves e conceda as permissões de identidade para obter segredos do cofre de chaves. Use a **clientId** e a **principalId** da identidade do Azure AD.
    ```azurecli
    az role assignment create --role "Reader" --assignee $principalId --scope /subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/contosoResourceGroup/providers/Microsoft.KeyVault/vaults/contosoKeyVault5

    az keyvault set-policy -n contosoKeyVault5 --secret-permissions get --spn $clientId
    az keyvault set-policy -n contosoKeyVault5 --key-permissions get --spn $clientId
    ```

## <a name="deploy-your-pod-with-mounted-secrets-from-your-key-vault"></a>Implantar seu pod com segredos montados do cofre de chaves

Para configurar o objeto SecretProviderClass, execute este comando:
```azurecli
kubectl apply -f secretProviderClass.yaml
```

### <a name="use-a-service-principal"></a>Usar uma entidade de serviço

Se estiver usando uma entidade de serviço, use o comando a seguir para implantar seus pods do Kubernetes com a SecretProviderClass e os secrets-store-creds configurados anteriormente. Estes são os modelos de implantação:
* Para [Linux](https://github.com/Azure/secrets-store-csi-driver-provider-azure/blob/master/examples/nginx-pod-inline-volume-service-principal.yaml)
* Para [Windows](https://github.com/Azure/secrets-store-csi-driver-provider-azure/blob/master/examples/windows-pod-secrets-store-inline-volume-secret-providerclass.yaml)

```azurecli
kubectl apply -f updateDeployment.yaml
```

### <a name="use-managed-identities"></a>Usar identidades gerenciadas

Se estiver usando identidades gerenciadas, crie uma *AzureIdentity* no cluster que referencie a identidade criada anteriormente. Em seguida, crie uma *AzureIdentityBinding* que faça referência à AzureIdentity criada. Preencha os parâmetros no modelo a seguir e salve-o como *podIdentityAndBinding.yaml*.  

```yml
apiVersion: aadpodidentity.k8s.io/v1
kind: AzureIdentity
metadata:
    name: "azureIdentityName"               # The name of your Azure identity
spec:
    type: 0                                 # Set type: 0 for managed service identity
    resourceID: /subscriptions/<SUBID>/resourcegroups/<RESOURCEGROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<AZUREIDENTITYNAME>
    clientID: "managedIdentityClientId"     # The clientId of the Azure AD identity that you created earlier
---
apiVersion: aadpodidentity.k8s.io/v1
kind: AzureIdentityBinding
metadata:
    name: azure-pod-identity-binding
spec:
    azureIdentity: "azureIdentityName"      # The name of your Azure identity
    selector: azure-pod-identity-binding-selector
```
    
Execute o comando a seguir para executar a associação:

```azurecli
kubectl apply -f podIdentityAndBinding.yaml
```

Em seguida, você implanta o pod. O código a seguir é o arquivo YAML da implantação, que usa a associação de identidade do pod da etapa anterior. Salve este arquivo como *podBindingDeployment.yaml*.

```yml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-secrets-store-inline
  labels:
    aadpodidbinding: azure-pod-identity-binding-selector
spec:
  containers:
    - name: nginx
      image: nginx
      volumeMounts:
        - name: secrets-store-inline
          mountPath: "/mnt/secrets-store"
          readOnly: true
  volumes:
    - name: secrets-store-inline
      csi:
        driver: secrets-store.csi.k8s.io
        readOnly: true
        volumeAttributes:
          secretProviderClass: azure-kvname
        nodePublishSecretRef:           # Only required when using service principal mode
          name: secrets-store-creds     # Only required when using service principal mode
```

Execute o seguinte comando para implantar o pod:

```azurecli
kubectl apply -f podBindingDeployment.yaml
```

### <a name="check-the-pod-status-and-secret-content"></a>Verificar o status do pod e o conteúdo do segredo 

Para exibir os pods que você implantou, execute este comando:
```azurecli
kubectl get pods
```

Para verificar o status do pod, execute este comando:
```azurecli
kubectl describe pod/nginx-secrets-store-inline
```

![Captura de tela da saída da CLI do Azure exibindo o estado "Em execução" do pod e mostrando todos os eventos como "Normal" ](../media/kubernetes-key-vault-6.png)

Na janela de saída, o pod implantado deve estar no estado *Em execução*. Na seção **Eventos** na parte inferior, todos os tipos de evento são exibidos como *Normal*.

Após verificar se o pod está em execução, você pode verificar se ele contém os segredos de seu cofre de chaves.

Para exibir todos os segredos contidos no pod, execute este comando:
```azurecli
kubectl exec -it nginx-secrets-store-inline -- ls /mnt/secrets-store/
```

Para exibir o conteúdo de um segredo específico, execute este comando:
```azurecli
kubectl exec -it nginx-secrets-store-inline -- cat /mnt/secrets-store/secret1
```

Verifique se o conteúdo do segredo é exibido.

## <a name="next-steps"></a>Próximas etapas

Para ajudar a garantir que o cofre de chaves seja recuperável, confira:
> [!div class="nextstepaction"]
> [Ligar a exclusão temporária](./key-vault-recovery.md)
