---
title: Criptografia dupla para dados em repouso
titleSuffix: Azure HDInsight
description: Este artigo descreve as duas camadas de criptografia disponíveis para dados em repouso em clusters do Azure HDInsight.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/10/2020
ms.openlocfilehash: 3d4f9e3be02a64efa058ea1f84a3e261cb6166fc
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104867110"
---
# <a name="azure-hdinsight-double-encryption-for-data-at-rest"></a>Criptografia dupla do Azure HDInsight para dados em repouso

Este artigo discute métodos de criptografia de dados em repouso em clusters do Azure HDInsight. A criptografia de dados em repouso refere-se à criptografia em discos gerenciados (discos de dados, discos do sistema operacional e discos temporários) anexados às máquinas virtuais do cluster HDInsight. 

Este documento não aborda dados armazenados em sua conta de armazenamento do Azure. Seus clusters podem ter uma ou mais contas de armazenamento do Azure anexadas em que as chaves de criptografia também podem ser gerenciadas pela Microsoft ou gerenciadas pelo cliente, mas o serviço de criptografia é diferente. Para obter mais informações sobre a criptografia de armazenamento do Azure, consulte [criptografia de armazenamento do Azure para dados em repouso](../storage/common/storage-service-encryption.md).

## <a name="introduction"></a>Introdução

Há três funções de disco gerenciados principais no Azure: o disco de dados, o disco do sistema operacional e o disco temporário. Para obter mais informações sobre os diferentes tipos de discos gerenciados, consulte [introdução aos Azure Managed disks](../virtual-machines/managed-disks-overview.md). 

O HDInsight dá suporte a vários tipos de criptografia em duas camadas diferentes:

- A SSE (criptografia do lado do servidor)-a SSE é executada pelo serviço de armazenamento. No HDInsight, a SSE é usada para criptografar discos do sistema operacional e discos de dados. Ele é habilitador por padrão. SSE é um serviço de criptografia de camada 1.
- Criptografia no host usando a chave gerenciada por plataforma – semelhante à SSE, esse tipo de criptografia é executado pelo serviço de armazenamento. No entanto, ele é apenas para discos temporários e não é habilitado por padrão. A criptografia no host também é um serviço de criptografia de camada 1.
- Criptografia em repouso usando a chave gerenciada pelo cliente-esse tipo de criptografia pode ser usado em dados e discos temporários. Ele não é habilitado por padrão e exige que o cliente forneça sua própria chave por meio do cofre de chaves do Azure. A criptografia em repouso é um serviço de criptografia de camada 2.

Esses tipos são resumidos na tabela a seguir.

|Tipo de cluster |Disco do sistema operacional (disco gerenciado) |Disco de dados (disco gerenciado) |Disco de dados temporário (SSD local) |
|---|---|---|---|
|Kafka, HBase com gravações aceleradas|Layer1: [criptografia SSE](../virtual-machines/managed-disks-overview.md#encryption) por padrão|Layer1: [criptografia SSE](../virtual-machines/managed-disks-overview.md#encryption) por padrão, Layer2: criptografia opcional em repouso usando o CMK|Layer1: criptografia opcional no host usando PMK, Layer2: criptografia opcional em repouso usando o CMK|
|Todos os outros clusters (Spark, interativo, Hadoop, HBase sem gravações aceleradas)|Layer1: [criptografia SSE](../virtual-machines/managed-disks-overview.md#encryption) por padrão|N/D|Layer1: criptografia opcional no host usando PMK, Layer2: criptografia opcional em repouso usando o CMK|

## <a name="encryption-at-rest-using-customer-managed-keys"></a>Criptografia em repouso usando chaves gerenciadas pelo cliente

A criptografia de chave gerenciada pelo cliente é um processo de uma etapa manipulado durante a criação do cluster sem custo adicional. Tudo o que você precisa fazer é autorizar uma identidade gerenciada com Azure Key Vault e adicionar a chave de criptografia ao criar o cluster.

Os discos de dados e os discos temporários em cada nó do cluster são criptografados com uma chave de criptografia de dados simétrica (DEK). A DEK é protegida usando a KEK (Chave de Criptografia de Chave) do seu cofre de chaves. Os processos de criptografia e descriptografia são realizados inteiramente pelo Azure HDInsight.

Para discos do sistema operacional anexados às VMs do cluster, apenas uma camada de criptografia (PMK) está disponível. É recomendável que os clientes evitem copiar dados confidenciais para discos do sistema operacional se tiver uma criptografia CMK necessária para seus cenários.

Se o Firewall do cofre de chaves estiver habilitado no cofre de chaves em que a chave de criptografia de disco estiver armazenada, os endereços IP do provedor de recursos regionais do HDInsight para a região em que o cluster será implantado deverão ser adicionados à configuração do firewall do cofre de chaves. Isso é necessário porque o HDInsight não é um serviço de cofre de chaves do Azure confiável.

Você pode usar o portal do Azure ou a CLI do Azure para gorar as chaves com segurança no cofre de chaves. Quando uma tecla gira, o cluster HDInsight começa a usar a nova chave em minutos. Habilite os recursos de proteção de chave de [exclusão reversível](../key-vault/general/soft-delete-overview.md) para proteger contra cenários de ransomware e exclusão acidental. Não há suporte para cofres de chaves sem esse recurso de proteção.

### <a name="get-started-with-customer-managed-keys"></a>Começar a usar chaves gerenciadas pelo cliente

Para criar um cluster HDInsight habilitado para chave gerenciada pelo cliente, percorreremos as seguintes etapas:

1. Criar identidades gerenciadas para recursos do Azure
1. Criar Azure Key Vault
1. Chave Create
1. Criar política de acesso
1. Criar cluster HDInsight com a chave gerenciada pelo cliente habilitada
1. Girando a chave de criptografia

Cada etapa é explicada em uma das seções a seguir em detalhes.

### <a name="create-managed-identities-for-azure-resources"></a>Criar identidades gerenciadas para recursos do Azure

Crie uma identidade gerenciada atribuída pelo usuário para se autenticar no Key Vault.

Consulte [criar uma identidade gerenciada atribuída pelo usuário](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) para etapas específicas. Para obter mais informações sobre como as identidades gerenciadas funcionam no Azure HDInsight, consulte [identidades gerenciadas no Azure hdinsight](hdinsight-managed-identities.md). Certifique-se de salvar a ID do recurso de identidade gerenciada para quando você a adicionar à política de acesso do Key Vault.

### <a name="create-azure-key-vault"></a>Criar Azure Key Vault

Crie um cofre da chave. Consulte [criar Azure Key Vault](../key-vault/general/quick-create-portal.md) para obter etapas específicas.

O HDInsight é compatível apenas com o Azure Key Vault. Se você tiver seu próprio cofre de chaves, poderá importar suas chaves para o Azure Key Vault. Lembre-se de que o cofre de chaves deve ter a **exclusão reversível** habilitada. Para obter mais informações sobre como importar as chaves existentes, visite [Sobre chaves, segredos e certificados](../key-vault/general/about-keys-secrets-certificates.md).

### <a name="create-key"></a>Chave Create

1. Em seu novo cofre de chaves, navegue até **configurações**  >  **chaves**  >  **+ gerar/importar**.

    :::image type="content" source="./media/disk-encryption/create-new-key.png" alt-text="Gerar uma nova chave no Azure Key Vault":::

1. Forneça um nome e, em seguida, selecione **criar**. Mantenha o **tipo de chave** padrão do **RSA**.

    :::image type="content" source="./media/disk-encryption/create-key.png" alt-text="gera o nome da chave":::

1. Ao retornar para a página **chaves** , selecione a chave que você criou.

    :::image type="content" source="./media/disk-encryption/key-vault-key-list.png" alt-text="lista de chaves do Key Vault":::

1. Selecione a versão para abrir a página de **versão da chave** . Ao usar sua própria chave para a criptografia do cluster HDInsight, você precisa fornecer o URI da chave. Copie o **Identificador de chave** e salve-o em algum local até que esteja pronto para criar o cluster.

    :::image type="content" source="./media/disk-encryption/get-key-identifier.png" alt-text="obter identificador de chave":::

### <a name="create-access-policy"></a>Criar política de acesso

1. Em seu novo cofre de chaves, navegue até **configurações**  >  **políticas de acesso**  >  **+ Adicionar política de acesso**.

    :::image type="content" source="./media/disk-encryption/key-vault-access-policy.png" alt-text="Criar uma nova política de acesso do Azure Key Vault":::

1. Na página **Adicionar política de acesso** , forneça as seguintes informações:

    |Propriedade |Descrição|
    |---|---|
    |Permissões de chave|Selecione **obter**, **desencapsular chave** e **encapsular chave**.|
    |Permissões de segredo|Selecione **obter**, **definir** e **excluir**.|
    |Selecionar entidade de segurança|Selecione a identidade gerenciada atribuída pelo usuário que você criou anteriormente.|

    :::image type="content" source="./media/disk-encryption/azure-portal-add-access-policy.png" alt-text="Definir Selecionar Entidade de Segurança para a política de acesso do Azure Key Vault":::

1. Selecione **Adicionar**.

1. Clique em **Salvar**.

    :::image type="content" source="./media/disk-encryption/add-key-vault-access-policy-save.png" alt-text="Salvar política de acesso de Azure Key Vault":::

### <a name="create-cluster-with-customer-managed-key-disk-encryption"></a>Criar cluster com criptografia de disco de chave gerenciada pelo cliente

Agora você está pronto para criar um novo cluster do HDInsight. As chaves gerenciadas pelo cliente só podem ser aplicadas a novos clusters durante a criação do cluster. A criptografia não pode ser removida de clusters de chaves gerenciados pelo cliente, e chaves gerenciadas pelo cliente não podem ser adicionadas a clusters existentes.

A partir da versão de novembro de 2020, o HDInsight dá suporte à criação de clusters usando URIs de chave com versão e menos de versão. Se você criar o cluster com um URI de chave sem versão, o cluster HDInsight tentará executar a rotação automática de chave quando a chave for atualizada em seu Azure Key Vault. Se você criar o cluster com um URI de chave com versão, será necessário executar uma rotação de chave manual, conforme discutido em [girando a chave de criptografia](#rotating-the-encryption-key).

Para clusters criados antes da versão de novembro de 2020, você precisará executar a rotação de chaves manualmente usando o URI de chave com versão.

#### <a name="using-the-azure-portal"></a>Usando o portal do Azure

Durante a criação do cluster, você pode usar uma chave com versão ou uma chave não com versão da seguinte maneira:

- Com **controle de versão** -durante a criação do cluster, forneça o identificador de **chave** completo, incluindo a versão da chave. Por exemplo, `https://contoso-kv.vault.azure.net/keys/myClusterKey/46ab702136bc4b229f8b10e8c2997fa4`.
- **Versão** não-durante a criação do cluster, forneça somente o **identificador de chave**. Por exemplo, `https://contoso-kv.vault.azure.net/keys/myClusterKey`.

Você também precisa atribuir a identidade gerenciada ao cluster.

:::image type="content" source="./media/disk-encryption/create-cluster-portal.png" alt-text="Criar novo cluster":::

#### <a name="using-azure-cli"></a>Usando a CLI do Azure

O exemplo a seguir mostra como usar CLI do Azure para criar um novo cluster de Apache Spark com a criptografia de disco habilitada. Para obter mais informações, consulte [CLI do Azure AZ hdinsight Create](/cli/azure/hdinsight#az-hdinsight-create). O parâmetro `encryption-key-version` é opcional.

```azurecli
az hdinsight create -t spark -g MyResourceGroup -n MyCluster \
-p "HttpPassword1234!" --workernode-data-disks-per-node 2 \
--storage-account MyStorageAccount \
--encryption-key-name SparkClusterKey \
--encryption-key-version 00000000000000000000000000000000 \
--encryption-vault-uri https://MyKeyVault.vault.azure.net \
--assign-identity MyMSI
```

#### <a name="using-azure-resource-manager-templates"></a>Usar modelos do Azure Resource Manager

O exemplo a seguir mostra como usar um modelo de Azure Resource Manager para criar um novo cluster de Apache Spark com a criptografia de disco habilitada. Para obter mais informações, consulte [o que são modelos de ARM?](../azure-resource-manager/templates/overview.md). A propriedade de modelo do Resource Manager `diskEncryptionKeyVersion` é opcional.

Este exemplo usa o PowerShell para chamar o modelo.

```powershell
$templateFile = "azuredeploy.json"
$ResourceGroupName = "MyResourceGroup"
$clusterName = "MyCluster"
$password = ConvertTo-SecureString 'HttpPassword1234!' -AsPlainText -Force
$diskEncryptionVaultUri = "https://MyKeyVault.vault.azure.net"
$diskEncryptionKeyName = "SparkClusterKey"
$diskEncryptionKeyVersion = "00000000000000000000000000000000"
$managedIdentityName = "MyMSI"

New-AzResourceGroupDeployment `
  -Name mySpark `
  -TemplateFile $templateFile `
  -ResourceGroupName $ResourceGroupName `
  -clusterName $clusterName `
  -clusterLoginPassword $password `
` -sshPassword $password `
  -diskEncryptionVaultUri $diskEncryptionVaultUri `
  -diskEncryptionKeyName $diskEncryptionKeyName `
  -diskEncryptionKeyVersion $diskEncryptionKeyVersion `
  -managedIdentityName $managedIdentityName
```

O conteúdo do modelo de gerenciamento de recursos, `azuredeploy.json` :

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "0.9.0.0",
  "parameters": {
    "clusterName": {
      "type": "string",
      "metadata": {
        "description": "The name of the HDInsight cluster to create."
      }
    },
    "clusterLoginUserName": {
      "type": "string",
      "defaultValue": "admin",
      "metadata": {
        "description": "These credentials can be used to submit jobs to the cluster and to log into cluster dashboards."
      }
    },
    "clusterLoginPassword": {
      "type": "securestring",
      "metadata": {
        "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "The location where all azure resources will be deployed."
      }
    },
    "sshUserName": {
      "type": "string",
      "defaultValue": "sshuser",
      "metadata": {
        "description": "These credentials can be used to remotely access the cluster."
      }
    },
    "sshPassword": {
      "type": "securestring",
      "metadata": {
        "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
      }
    },
    "headNodeSize": {
      "type": "string",
      "defaultValue": "Standard_D12_v2",
      "metadata": {
        "description": "The VM size of the head nodes."
      }
    },
    "workerNodeSize": {
      "type": "string",
      "defaultValue": "Standard_D13_v2",
      "metadata": {
        "description": "The VM size of the worker nodes."
      }
    },
    "diskEncryptionVaultUri": {
      "type": "string",
      "metadata": {
        "description": "The Key Vault DNSname."
      }
    },
    "diskEncryptionKeyName": {
      "type": "string",
      "metadata": {
        "description": "The Key Vault key name."
      }
    },
    "diskEncryptionKeyVersion": {
      "type": "string",
      "metadata": {
        "description": "The Key Vault key version for the selected key."
      }
    },
    "managedIdentityName": {
      "type": "string",
      "metadata": {
        "description": "The user-assigned managed identity."
      }
    }
  },
  "variables": {
    "defaultStorageAccount": {
      "name": "[uniqueString(resourceGroup().id)]",
      "type": "Standard_LRS"
    }
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('defaultStorageAccount').name]",
      "location": "[parameters('location')]",
      "apiVersion": "2019-06-01",
      "sku": {
        "name": "[variables('defaultStorageAccount').type]"
      },
      "kind": "Storage",
      "properties": {}
    },
    {
      "apiVersion": "2018-06-01-preview",
      "name": "[parameters('clusterName')]",
      "type": "Microsoft.HDInsight/clusters",
      "location": "[parameters('location')]",
      "properties": {
        "clusterVersion": "3.6",
        "osType": "Linux",
        "tier": "standard",
        "clusterDefinition": {
          "kind": "spark",
          "componentVersion": {
            "Spark": "2.3"
          },
          "configurations": {
            "gateway": {
              "restAuthCredential.isEnabled": true,
              "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
              "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
            }
          }
        },
        "storageProfile": {
          "storageaccounts": [
            {
              "name": "[replace(replace(reference(resourceId('Microsoft.Storage/storageAccounts', variables('defaultStorageAccount').name), '2019-06-01').primaryEndpoints.blob,'https://',''),'/','')]",
              "isDefault": true,
              "container": "[parameters('clusterName')]",
              "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('defaultStorageAccount').name), '2019-06-01').keys[0].value]"
            }
          ]
        },
        "computeProfile": {
          "roles": [
            {
              "name": "headnode",
              "minInstanceCount": 1,
              "targetInstanceCount": 2,
              "hardwareProfile": {
                "vmSize": "[parameters('headNodeSize')]"
              },
              "osProfile": {
                "linuxOperatingSystemProfile": {
                  "username": "[parameters('sshUserName')]",
                  "password": "[parameters('sshPassword')]"
                },
              },
            },
            {
              "name": "workernode",
              "targetInstanceCount": 1,
              "hardwareProfile": {
                "vmSize": "[parameters('workerNodeSize')]"
              },
              "osProfile": {
                "linuxOperatingSystemProfile": {
                  "username": "[parameters('sshUserName')]",
                  "password": "[parameters('sshPassword')]"
                },
              },
            }
          ]
        },
        "minSupportedTlsVersion": "1.2",
        "diskEncryptionProperties": {
          "vaultUri": "[parameters('diskEncryptionVaultUri')]",
          "keyName": "[parameters('diskEncryptionKeyName')]",
          "keyVersion": "[parameters('diskEncryptionKeyVersion')]",
          "msiResourceId": "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('managedIdentityName'))]"
        }
      },
      "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
          "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('managedIdentityName'))]": {}
        }
      }
    }
  ]
}
```

### <a name="rotating-the-encryption-key"></a>Girando a chave de criptografia

Você pode alterar as chaves de criptografia usadas no cluster em execução, usando o portal do Azure ou CLI do Azure. Para essa operação, o cluster deve ter acesso à chave atual e à nova chave pretendida, caso contrário, a operação de rotação de teclas falhará. Para clusters criados após a versão de novembro de 2020, você pode escolher se deseja que sua nova chave tenha uma versão ou não. Para clusters criados antes da versão de novembro de 2020, você deve usar uma chave com versão ao girar a chave de criptografia.

#### <a name="using-the-azure-portal"></a>Usando o portal do Azure

Para girar a chave, você precisa do URI do cofre de chaves de base. Depois de fazer isso, vá para a seção de propriedades do cluster HDInsight no portal e clique em **alterar chave** em **URL da chave de criptografia do disco**. Insira na nova URL de chave e envie para girar a chave.

:::image type="content" source="./media/disk-encryption/change-key.png" alt-text="girar chave de criptografia de disco":::

#### <a name="using-azure-cli"></a>Usando a CLI do Azure

O exemplo a seguir mostra como girar a chave de criptografia de disco para um cluster HDInsight existente. Para obter mais informações, consulte [CLI do Azure AZ hdinsight Rotate-Disk-Encryption-Key](/cli/azure/hdinsight#az-hdinsight-rotate-disk-encryption-key).

```azurecli
az hdinsight rotate-disk-encryption-key \
--encryption-key-name SparkClusterKey \
--encryption-key-version 00000000000000000000000000000000 \
--encryption-vault-uri https://MyKeyVault.vault.azure.net \
--name MyCluster \
--resource-group MyResourceGroup
```

## <a name="faq-for-customer-managed-key-encryption"></a>Perguntas frequentes para criptografia de chave gerenciada pelo cliente

**Como o cluster HDInsight acessa meu cofre de chaves?**

O HDInsight acessa sua instância de Azure Key Vault usando a identidade gerenciada que você associa ao cluster HDInsight. Essa identidade gerenciada pode ser criada antes ou durante a criação do cluster. Você também precisa conceder o acesso de identidade gerenciada ao cofre de chaves em que a chave está armazenada.

**Esse recurso está disponível para todos os clusters no HDInsight?**

A criptografia de chave gerenciada pelo cliente está disponível para todos os tipos de cluster, exceto Spark 2,1 e 2,2.

**Posso usar várias chaves para criptografar diferentes discos ou pastas?**

Não, todos os discos gerenciados e discos de recursos são criptografados pela mesma chave.

**O que acontecerá se o cluster perder o acesso ao cofre de chaves ou à chave?**

Se o cluster perder o acesso à chave, os avisos serão mostrados no portal do Apache Ambari. Nesse estado, a operação de **alteração da chave** falhará. Depois que o acesso à chave for restaurado, os avisos Ambaris desaparecerão e as operações como a rotação de chaves poderão ser executadas com êxito.

:::image type="content" source="./media/disk-encryption/ambari-alert.png" alt-text="alerta de Ambari de acesso à chave":::

**Como poderei recuperar o cluster se as chaves forem excluídas?**

Como apenas as chaves habilitadas para "exclusão reversível" têm suporte, se as chaves forem recuperadas no cofre de chaves, o cluster deverá recuperar o acesso às chaves. Para recuperar uma chave de Azure Key Vault, consulte [Undo-AzKeyVaultKeyRemoval](/powershell/module/az.keyvault/Undo-AzKeyVaultKeyRemoval) ou [AZ-keyvault-Key-Recover](/cli/azure/keyvault/key#az-keyvault-key-recover).


**Se um cluster for escalado verticalmente, os novos nós oferecerão suporte a chaves gerenciadas pelo cliente de forma direta?**

Sim. O cluster precisa acessar a chave no cofre de chaves durante ao escalar verticalmente. A mesma chave é usada para criptografar discos gerenciados e discos de recursos no cluster.

**As chaves gerenciadas pelo cliente estão disponíveis no meu local?**

As chaves gerenciadas pelo cliente do HDInsight estão disponíveis em todas as nuvens públicas e nuvens nacionais.

## <a name="encryption-at-host-using-platform-managed-keys"></a>Criptografia no host usando chaves gerenciadas pela plataforma

### <a name="enable-in-the-azure-portal"></a>Habilitar no Portal do Azure

A criptografia no host pode ser habilitada durante a criação do cluster no portal do Azure.

> [!Note]
> Quando a criptografia no host estiver habilitada, você não poderá adicionar aplicativos ao cluster HDInsight do Azure Marketplace.

:::image type="content" source="media/disk-encryption/encryption-at-host.png" alt-text="Habilite a criptografia no host.":::

Essa opção habilita a [criptografia no host](../virtual-machines/disks-enable-host-based-encryption-portal.md) para discos de dados temporários de VMs do HDInsight usando o PMK. A criptografia no host só [tem suporte em determinados SKUs de VM em regiões limitadas](../virtual-machines/disks-enable-host-based-encryption-portal.md) e o HDInsight dá suporte à [seguinte configuração de nó e SKUs](./hdinsight-supported-node-configuration.md).

Para entender o tamanho correto da VM para seu cluster HDInsight, confira [selecionando o tamanho correto da VM para o cluster hdinsight do Azure](hdinsight-selecting-vm-size.md). O SKU da VM padrão para o nó Zookeeper quando a criptografia no host estiver habilitada será DS2V2.

### <a name="enable-using-powershell"></a>Habilitar uso do PowerShell

O trecho de código a seguir mostra como você pode criar um novo cluster HDInsight do Azure que tem criptografia no host habilitada usando o PowerShell. Ele usa o parâmetro `-EncryptionAtHost $true` para habilitar o recurso.

```powershell
$storageAccountResourceGroupName = "Group"
$storageAccountName = "yourstorageacct001"
$storageAccountKey = Get-AzStorageAccountKey `
    -ResourceGroupName $storageAccountResourceGroupName `
    -Name $storageAccountName | %{ $_.Key1 }
$storageContainer = "container002"
# Cluster configuration info
$location = "East US 2"
$clusterResourceGroupName = "Group"
$clusterName = "your-hadoop-002"
$clusterCreds = Get-Credential
# If the cluster's resource group doesn't exist yet, run:
# New-AzResourceGroup -Name $clusterResourceGroupName -Location $location
# Create the cluster
New-AzHDInsightCluster `
    -ClusterType Hadoop `
    -ClusterSizeInNodes 4 `
    -ResourceGroupName $clusterResourceGroupName `
    -ClusterName $clusterName `
    -HttpCredential $clusterCreds `
    -Location $location `
    -DefaultStorageAccountName "$storageAccountName.blob.core.contoso.net" `
    -DefaultStorageAccountKey $storageAccountKey `
    -DefaultStorageContainer $storageContainer `
    -SshCredential $clusterCreds `
    -EncryptionAtHost $true `
```

### <a name="enable-using-azure-cli"></a>Habilitar usando a CLI do Azure

O trecho de código a seguir mostra como você pode criar um novo cluster HDInsight do Azure que tem criptografia no host habilitada, usando CLI do Azure. Ele usa o parâmetro `--encryption-at-host true` para habilitar o recurso.

```azurecli
az hdinsight create -t spark -g MyResourceGroup -n MyCluster \\
-p "yourpass" \\
--storage-account MyStorageAccount --encryption-at-host true
```

## <a name="next-steps"></a>Próximas etapas

* Para obter mais informações sobre Azure Key Vault, consulte [o que é Azure Key Vault](../key-vault/general/overview.md).
* [Visão geral da segurança corporativa no Azure HDInsight](./domain-joined/hdinsight-security-overview.md).
