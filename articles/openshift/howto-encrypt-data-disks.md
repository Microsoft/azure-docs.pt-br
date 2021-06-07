---
title: Criptografar declarações de volume persistentes com uma chave gerenciada pelo cliente (CMK) no Azure Red Hat OpenShift (ARO)
description: Instruções de implantação de BYOK (traga sua própria chave)/CMK (chave gerenciada pelo cliente) para o Red Hat OpenShift do Azure
ms.topic: article
ms.date: 02/24/2021
author: stuartatmicrosoft
ms.author: stkirk
ms.service: azure-redhat-openshift
keywords: criptografia, byok, aro, CMK, openshift, Red Hat
ms.openlocfilehash: cf028456cc8971678373d36214885c3f79df8e82
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105047058"
---
# <a name="encrypt-persistent-volume-claims-with-a-customer-managed-key-cmk-on-azure-red-hat-openshift-aro-preview"></a>Criptografar declarações de volume persistentes com uma chave gerenciada pelo cliente (CMK) no Azure Red Hat OpenShift (toa) (versão prévia)

O armazenamento do Azure usa a SSE (criptografia do lado do servidor) para [criptografar](../storage/common/storage-service-encryption.md) automaticamente os dados quando eles são persistidos para a nuvem. Por padrão, os dados são criptografados com chaves gerenciadas pela plataforma Microsoft. Para obter mais controle sobre as chaves de criptografia, você pode fornecer suas próprias chaves gerenciadas pelo cliente para criptografar dados em seus clusters do Azure Red Hat OpenShift.

> [!NOTE]
> Neste estágio, o suporte existe apenas para criptografar volumes persistentes de toa com chaves gerenciadas pelo cliente. Este recurso não está disponível no momento para discos do sistema operacional do nó de trabalho ou mestre.

> [!IMPORTANT]
> Os recursos de visualização de toa estão disponíveis em uma base de autoatendimento e aceitação. As visualizações são fornecidas "como estão" e "como disponíveis" e são excluídas dos contratos de nível de serviço e da garantia limitada. As visualizações de ARO são parcialmente cobertas pelo suporte ao cliente em uma base de melhor esforço. Dessa forma, esses recursos não são destinados ao uso em produção.

## <a name="before-you-begin"></a>Antes de começar
Este artigo supõe que:

* Você tem um cluster ARO já existente na versão OpenShift 4,4 (ou superior).

* Você tem a ferramenta de linha de comando **OC** OpenShift, Base64 (parte de coreutils) e o **AZ** CLI do Azure instalado.

* Você está conectado ao seu cluster toa usando o **OC** como um usuário administrador global do cluster (kubeadmin).

* Você está conectado ao CLI do Azure usando **AZ** com uma conta autorizada para conceder acesso "colaborador" na mesma assinatura que o cluster toa.

## <a name="limitations"></a>Limitações

* A disponibilidade para criptografia de chave gerenciada pelo cliente é específica da região. Para ver o status de uma região específica do Azure, verifique [regiões do Azure][supported-regions].
* Se você quiser usar ultra discos, primeiro será necessário habilitá-los em sua assinatura antes de começar.

## <a name="declare-cluster--encryption-variables"></a>Declarar variáveis de criptografia de & de cluster
Você deve configurar as variáveis abaixo para que seja apropriado para você ser o cluster toa no qual você deseja habilitar as chaves de criptografia gerenciadas pelo cliente:
```
aroCluster="mycluster"             # The name of the ARO cluster that you wish to enable CMK on. This may be obtained from **az aro list -o table**
buildRG="mycluster-rg"             # The name of the resource group used when you initially built the ARO cluster. This may be obtained from **az aro list -o table**
desName="aro-des"                  # Your Azure Disk Encryption Set name. This must be unique in your subscription.
vaultName="aro-keyvault-1"         # Your Azure Key Vault name. This must be unique in your subscription.
vaultKeyName="myCustomAROKey"      # The name of the key to be used within your Azure Key Vault. This is the name of the key, not the actual value of the key that you will rotate.
```

## <a name="obtain-your-subscription-id"></a>Obter sua ID de assinatura
Sua ID de assinatura do Azure é usada várias vezes na configuração de CMK. Obtê-lo e armazená-lo como uma variável:
```azurecli-interactive
# Obtain your Azure Subscription ID and store it in a variable
subId="$(az account list -o tsv | grep True | awk '{print $3}')"
```

## <a name="create-an-azure-key-vault-instance"></a>Criar uma instância de Azure Key Vault
Uma instância de Azure Key Vault deve ser usada para armazenar suas chaves. Crie um novo Key Vault com a proteção de limpeza habilitada. Em seguida, crie uma nova chave dentro do cofre para armazenar sua própria chave personalizada:

```azurecli-interactive
# Create an Azure Key Vault resource in a supported Azure region
az keyvault create -n $vaultName -g $buildRG --enable-purge-protection true -o table

# Create the actual key within the Azure Key Vault
az keyvault key create --vault-name $vaultName --name $vaultKeyName --protection software -o jsonc
```

## <a name="create-an-azure-disk-encryption-set"></a>Criar um conjunto de criptografia de disco do Azure

O conjunto de Azure Disk Encryption é usado como o ponto de referência para discos na toa. Ele está conectado à Azure Key Vault que criamos na etapa anterior e efetuará pull de chaves gerenciadas pelo cliente desse local.

```azurecli-interactive
# Retrieve the Key Vault Id and store it in a variable
keyVaultId="$(az keyvault show --name $vaultName --query [id] -o tsv)"

# Retrieve the Key Vault key URL and store it in a variable
keyVaultKeyUrl="$(az keyvault key show --vault-name $vaultName --name $vaultKeyName  --query [key.kid] -o tsv)"

# Create an Azure disk encryption set
az disk-encryption-set create -n $desName -g $buildRG --source-vault $keyVaultId --key-url $keyVaultKeyUrl -o table
```

## <a name="grant-the-disk-encryption-set-access-to-key-vault"></a>Conceder acesso ao conjunto de criptografia de disco para Key Vault
Use o conjunto de criptografia de disco que criamos nas etapas anteriores e conceda ao conjunto de criptografia de disco acesso ao Azure Key Vault:

```azurecli-interactive
# First, find the disk encryption set's Azure Application ID value.
desIdentity="$(az disk-encryption-set show -n $desName -g $buildRG --query [identity.principalId] -o tsv)"

# Next, update the Key Vault security policy settings to allow access to the disk encryption set.
az keyvault set-policy -n $vaultName -g $buildRG --object-id $desIdentity --key-permissions wrapkey unwrapkey get -o table

# Now, ensure the Disk Encryption Set can read the contents of the Azure Key Vault.
az role assignment create --assignee $desIdentity --role Reader --scope $keyVaultId -o jsonc
```

### <a name="obtain-other-ids-required-for-role-assignments"></a>Obter outras IDs necessárias para atribuições de função
Precisamos permitir que o cluster toa use o conjunto de criptografia de disco para criptografar as declarações de volume persistentes (PVCs) no cluster toa. Para fazer isso, criaremos um novo Identidade de Serviço Gerenciada (MSI). Também definiremos outras permissões para o MSI da toa e para o conjunto de criptografia de disco.

```azurecli-interactive
# First, get the Azure Application ID of the service principal used in the ARO cluster.
aroSPAppId="$(oc get secret azure-credentials -n kube-system -o jsonpath='{.data.azure_client_id}' | base64 --decode)"

# Next, get the object ID of the service principal used in the ARO cluster.
aroSPObjId="$(az ad sp show --id $aroSPAppId -o tsv --query [objectId])"

# Set the name of the ARO Managed Service Identity. 
msiName="$aroCluster-msi"

# Create the Managed Service Identity (MSI) required for disk encryption.
az identity create -g $buildRG -n $msiName -o jsonc

# Get the ARO Managed Service Identity Azure Application ID.
aroMSIAppId="$(az identity show -n $msiName -g $buildRG -o tsv --query [clientId])"

# Get the resource ID for the disk encryption set and the Key Vault resource group.
buildRGResourceId="$(az group show -n $buildRG -o tsv --query [id])"
```

### <a name="implement-other-role-assignments-required-for-cmk-encryption"></a>Implementar outras atribuições de função necessárias para a criptografia CMK
Aplique as atribuições de função necessárias usando as variáveis obtidas na etapa anterior:

```azurecli-interactive
# Ensure the Azure Disk Encryption Set can read the contents of the Azure Key Vault.
az role assignment create --assignee $desIdentity --role Reader --scope $keyVaultId -o jsonc

# Assign the MSI AppID 'Reader' permission over the disk encryption set & Key Vault resource group.
az role assignment create --assignee $aroMSIAppId --role Reader --scope $buildRGResourceId -o jsonc

# Assign the ARO Service Principal 'Contributor' permission over the disk encryption set & Key Vault Resource Group.
az role assignment create --assignee $aroSPObjId --role Contributor --scope $buildRGResourceId -o jsonc
```

## <a name="create-a-k8s-storage-class-for-encrypted-premium--ultra-disks"></a>Criar uma classe de armazenamento K8S para ultra discos de & Premium criptografados
Gere classes de armazenamento a serem usadas para CMK para os discos Premium_LRS e UltraSSD_LRS:

```azurecli-interactive
# Premium Disks
cat > managed-premium-encrypted-cmk.yaml<< EOF
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: managed-premium-encrypted-cmk
provisioner: kubernetes.io/azure-disk
parameters:
  skuname: Premium_LRS
  kind: Managed
  diskEncryptionSetID: "/subscriptions/$subId/resourceGroups/$buildRG/providers/Microsoft.Compute/diskEncryptionSets/$desName"
  resourceGroup: $buildRG
reclaimPolicy: Delete
allowVolumeExpansion: true
volumeBindingMode: WaitForFirstConsumer
EOF

# Ultra Disks
cat > managed-ultra-encrypted-cmk.yaml<< EOF
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: managed-ultra-encrypted-cmk
provisioner: kubernetes.io/azure-disk
parameters:
  skuname: UltraSSD_LRS
  kind: Managed
  diskEncryptionSetID: "/subscriptions/$subId/resourceGroups/$buildRG/providers/Microsoft.Compute/diskEncryptionSets/$desName"
  resourceGroup: $buildRG
  cachingmode: None
  diskIopsReadWrite: "2000"  # minimum value: 2 IOPS/GiB
  diskMbpsReadWrite: "320"   # minimum value: 0.032/GiB
reclaimPolicy: Delete
allowVolumeExpansion: true
volumeBindingMode: WaitForFirstConsumer
EOF
```

Em seguida, execute esta implantação em seu cluster toa para aplicar a configuração de classe de armazenamento:

```azurecli-interactive
# Update cluster with the new storage classes
oc apply -f managed-premium-encrypted-cmk.yaml
oc apply -f managed-ultra-encrypted-cmk.yaml
```

## <a name="test-encryption-with-customer-managed-keys-optional"></a>Testar a criptografia com chaves gerenciadas pelo cliente (opcional)
Para verificar se o cluster está usando uma chave gerenciada pelo cliente para a criptografia de PVC, criaremos uma declaração de volume persistente usando a nova classe de armazenamento. O trecho de código abaixo cria um pod e monta uma declaração de volume persistente usando discos Premium.
```
# Create a pod which uses a persistent volume claim referencing the new storage class
cat > test-pvc.yaml<< EOF
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mypod-with-cmk-encryption-pvc
spec:
  accessModes:
  - ReadWriteOnce
  storageClassName: managed-premium-encrypted-cmk
  resources:
    requests:
      storage: 1Gi
---
kind: Pod
apiVersion: v1
metadata:
  name: mypod-with-cmk-encryption
spec:
  containers:
  - name: mypod-with-cmk-encryption
    image: nginx:1.15.5
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
    volumeMounts:
    - mountPath: "/mnt/azure"
      name: volume
  volumes:
    - name: volume
      persistentVolumeClaim:
        claimName: mypod-with-cmk-encryption-pvc
EOF
```
### <a name="apply-the-test-pod-configuration-file-optional"></a>Aplicar o arquivo de configuração do pod de teste (opcional)
Execute os comandos abaixo para aplicar a configuração de pod de teste e retornar a UID da nova declaração de volume persistente. A UID será usada para verificar se o disco está criptografado usando CMK.
```azurecli-interactive
# Apply the test pod configuration file and set the PVC UID as a variable to query in Azure later.
pvcUid="$(oc apply -f test-pvc.yaml -o jsonpath='{.items[0].metadata.uid}')"

# Determine the full Azure Disk name.
pvName="$(oc get pv pvc-$pvcUid -o jsonpath='{.spec.azureDisk.diskName}')"
```
> [!NOTE]
> Ocasionalmente, pode haver um pequeno atraso ao aplicar atribuições de função dentro de Azure Active Directory. Dependendo da velocidade em que essas instruções são executadas, o comando para "determinar o nome completo do disco do Azure" pode não ter sucesso. Se isso ocorrer, examine a saída de **OC descrever PVC mypod-with-CMK-Encryption-PVC** para garantir que o disco foi provisionado com êxito. Se a propagação da atribuição de função não tiver sido concluída, talvez seja necessário *excluir* e *aplicar* novamente o Pod & PVC YAML.

### <a name="verify-pvc-disk-is-configured-with-encryptionatrestwithcustomerkey-optional"></a>Verificar se o disco do PVC está configurado com "EncryptionAtRestWithCustomerKey" (opcional)
O Pod deve criar uma declaração de volume persistente que faça referência à classe de armazenamento CMK. A execução do comando a seguir validará que o PVC foi implantado conforme o esperado:
```azurecli-interactive
# Describe the OpenShift cluster-wide persistent volume claims
oc describe pvc

# Verify with Azure that the disk is encrypted with a customer-managed key
az disk show -n $pvName -g $buildRG -o json --query [encryption]
```

<!-- LINKS - external -->

<!-- LINKS - internal -->
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[best-practices-security]: ../aks/operator-best-practices-cluster-security.md
[byok-azure-portal]: ../storage/common/customer-managed-keys-configure-key-vault.md
[customer-managed-keys]: ../virtual-machines/disk-encryption.md#customer-managed-keys
[key-vault-generate]: ../key-vault/general/manage-with-cli2.md
[supported-regions]: ../virtual-machines/disk-encryption.md#supported-regions