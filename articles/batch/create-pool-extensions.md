---
title: Usar extensões com pools do lote
description: As extensões são pequenos aplicativos que facilitam a configuração do provisionamento e a configuração em nós de computação do lote.
ms.topic: how-to
ms.date: 02/10/2021
ms.openlocfilehash: 1bf9847af57347c143ee3d790d89988ba7cd48e4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100416907"
---
# <a name="use-extensions-with-batch-pools"></a>Usar extensões com pools do lote

As extensões são pequenos aplicativos que facilitam a configuração do provisionamento e a configuração em nós de computação do lote. Você pode selecionar qualquer uma das extensões que são permitidas pelo lote do Azure e tê-las instaladas nos nós de computação à medida que elas são provisionadas. Depois disso, a extensão pode executar sua operação pretendida.

Você pode verificar o status ao vivo das extensões usadas e recuperar as informações que elas retornam para buscar qualquer recurso de detecção, correção ou diagnóstico.

## <a name="prerequisites"></a>Pré-requisitos

- Os pools com extensões devem usar a [configuração de máquina virtual](nodes-and-pools.md#virtual-machine-configuration).
- O tipo de extensão CustomScript é reservado para o serviço de lote do Azure e não pode ser substituído.

### <a name="supported-extensions"></a>Extensões com suporte

As extensões a seguir podem ser instaladas no momento ao criar um pool do lote. 

- Extensão de Azure Key Vault para [Linux](../virtual-machines/extensions/key-vault-linux.md) e [Windows](../virtual-machines/extensions/key-vault-windows.md)
- Log Analytics e monitoramento de extensão para [Linux](../virtual-machines/extensions/oms-linux.md) e [Windows](../virtual-machines/extensions/oms-windows.md)
- Pacote de segurança do Azure

Você pode solicitar suporte para Publicadores adicionais e/ou tipos de extensão abrindo uma solicitação de suporte.

## <a name="create-a-pool-with-extensions"></a>Criar um pool com extensões

O exemplo a seguir cria um pool do lote de nós do Linux que usa a extensão Azure Key Vault.

URI da API REST

```http
 PUT https://management.azure.com/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.Batch/batchAccounts/<batchaccountName>/pools/<batchpoolName>?api-version=2021-01-01
```

Corpo da solicitação

```json
{
    "name": "test1",
    "type": "Microsoft.Batch/batchAccounts/pools",
    "properties": {
        "vmSize": "STANDARD_DS2_V2",
        "taskSchedulingPolicy": {
            "nodeFillType": "Pack"
        },
        "deploymentConfiguration": {
            "virtualMachineConfiguration": {
                "imageReference": {
                    "publisher": "canonical",
                    "offer": "ubuntuserver",
                    "sku": "18.04-lts",
                    "version": "latest"
                },
                "nodeAgentSkuId": "batch.node.ubuntu 18.04",
                "extensions": [
                    {
                        "name": "secretext",
                        "type": "KeyVaultForLinux",
                        "publisher": "Microsoft.Azure.KeyVault",
                        "typeHandlerVersion": "1.0",
                        "autoUpgradeMinorVersion": true,
                        "settings": {
                            "secretsManagementSettings": {
                                "pollingIntervalInS": "300",
                                "certificateStoreLocation": "/var/lib/waagent/Microsoft.Azure.KeyVault",
                                "requireInitialSync": true,
                                "observedCertificates": [
                                    "https://testkvwestus2.vault.azure.net/secrets/authsecreat"
                                ]
                            },
                            "authenticationSettings": {
                                "msiEndpoint": "http://169.254.169.254/metadata/identity",
                                "msiClientId": "885b1a3d-f13c-4030-afcf-9f05044d78dc"
                            }
                        },
                        "protectedSettings":{}
                    }
                ]
            }
        },
        "scaleSettings": {
            "fixedScale": {
                "targetDedicatedNodes": 1,
                "targetLowPriorityNodes": 0,
                "resizeTimeout": "PT15M"
            }
        }
```

## <a name="get-extension-data-from-a-pool"></a>Obter dados de extensão de um pool

O exemplo a seguir recupera dados da extensão Azure Key Vault.

URI da API REST

```http
 GET https://<accountname>.<region>.batch.azure.com/pools/test3/nodes/tvmps_a3ce79db285d6c124399c5bd3f3cf308d652c89675d9f1f14bfc184476525278_d/extensions/secretext?api-version=2010-01-01
```

Corpo da resposta

```json
{
  "odata.metadata":"https://testwestus2batch.westus2.batch.azure.com/$metadata#extensions/@Element","instanceView":{
    "name":"secretext","statuses":[
      {
        "code":"ProvisioningState/succeeded","level":0,"displayStatus":"Provisioning succeeded","message":"Successfully started Key Vault extension service. 2021-02-08T19:49:39Z"
      }
    ]
  },"vmExtension":{
    "name":"KVExtensions","publisher":"Microsoft.Azure.KeyVault","type":"KeyVaultForLinux","typeHandlerVersion":"1.0","autoUpgradeMinorVersion":true,"settings":"{\r\n  \"secretsManagementSettings\": {\r\n    \"pollingIntervalInS\": \"300\",\r\n    \"certificateStoreLocation\": \"/var/lib/waagent/Microsoft.Azure.KeyVault\",\r\n    \"requireInitialSync\": true,\r\n    \"observedCertificates\": [\r\n      \"https://testkvwestus2.vault.azure.net/secrets/testumi\"\r\n    ]\r\n  },\r\n  \"authenticationSettings\": {\r\n    \"msiEndpoint\": \"http://169.254.169.254/metadata/identity\",\r\n    \"msiClientId\": \"885b1a3d-f13c-4030-afcf-922f05044d78dc\"\r\n  }\r\n}"
  }
}

```

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre várias maneiras de [copiar aplicativos e dados para nós de pool](batch-applications-to-pool-nodes.md).
- Saiba mais sobre como trabalhar com [nós e pools](nodes-and-pools.md).
