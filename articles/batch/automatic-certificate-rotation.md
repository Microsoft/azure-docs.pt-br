---
title: Habilitar a rotação de certificado automática em um pool do lote
description: Você pode criar um pool do lote com uma identidade gerenciada e um certificado que será renovado automaticamente.
ms.topic: conceptual
ms.date: 03/23/2021
ms.custom: references_regions
ms.openlocfilehash: e8bea49b2980deb8f20258ab7ea5619ece8cd2bf
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "104962446"
---
# <a name="enable-automatic-certificate-rotation-in-a-batch-pool"></a>Habilitar a rotação de certificado automática em um pool do lote

 Você pode criar um pool do lote com um certificado que será renovado automaticamente. Para fazer isso, o pool deve ser criado com uma [identidade gerenciada atribuída pelo usuário](managed-identity-pools.md) que terá acesso ao certificado no [Azure Key Vault](../key-vault/general/overview.md).

> [!IMPORTANT]
> O suporte para pools do lote do Azure com identidades gerenciadas atribuídas pelo usuário está atualmente em visualização pública para as seguintes regiões: oeste dos EUA 2, Sul EUA Central, leste dos EUA, US Gov Arizona e US Gov-Virgínia.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="create-a-user-assigned-identity"></a>Criar uma identidade atribuída pelo usuário

Primeiro, [Crie sua identidade gerenciada atribuída pelo usuário](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity) no mesmo locatário como sua conta do lote. Essa identidade gerenciada não precisa estar no mesmo grupo de recursos ou mesmo na mesma assinatura.

Certifique-se de anotar a **ID do cliente** da identidade gerenciada atribuída pelo usuário. Você precisará desse valor mais tarde.

:::image type="content" source="media/automatic-certificate-rotation/client-id.png" alt-text="Captura de tela mostrando a ID do cliente de uma identidade gerenciada atribuída pelo usuário no portal do Azure.":::

## <a name="create-your-certificate"></a>Criar seu certificado

Em seguida, você precisará criar um certificado e adicioná-lo a Azure Key Vault. Se você ainda não criou um cofre de chaves, precisará fazer isso primeiro. Para obter instruções, consulte [início rápido: definir e recuperar um certificado de Azure Key Vault usando o portal do Azure](../key-vault/certificates/quick-create-portal.md).

Ao criar seu certificado, certifique-se de definir o **tipo de ação de tempo de vida** para renovar automaticamente e especifique o número de dias após o qual o certificado deve ser renovado.

:::image type="content" source="media/automatic-certificate-rotation/certificate.png" alt-text="Captura de tela de criação de certificado na portal do Azure.":::

Depois que o certificado tiver sido criado, anote seu **identificador secreto**. Você precisará desse valor mais tarde.

:::image type="content" source="media/automatic-certificate-rotation/secret-identifier.png" alt-text="Captura de tela mostrando o identificador secreto de um certificado.":::

## <a name="add-an-access-policy-in-azure-key-vault"></a>Adicionar uma política de acesso no Azure Key Vault

No cofre de chaves, atribua uma política de acesso Key Vault que permite que sua identidade gerenciada atribuída pelo usuário acesse segredos e certificados. Para obter instruções detalhadas, consulte [atribuir uma política de acesso Key Vault usando o portal do Azure](../key-vault/general/assign-access-policy-portal.md).

## <a name="create-a-batch-pool-with-a-user-assigned-managed-identity"></a>Criar um pool do lote com uma identidade gerenciada atribuída pelo usuário

Crie um pool do lote com sua identidade gerenciada usando a [biblioteca de gerenciamento .net do lote](/dotnet/api/overview/azure/batch#management-library). Para obter mais informações, consulte [Configurar identidades gerenciadas em pools do lote](managed-identity-pools.md).

O exemplo a seguir usa a API REST de gerenciamento do lote para criar um pool. Certifique-se de usar o **identificador secreto** do certificado para `observedCertificates` o e a **ID do cliente** da sua identidade gerenciada para `msiClientId` o, substituindo os dados de exemplo abaixo.

URI da API REST

```http
PUT https://management.azure.com/subscriptions/<subscriptionid>/resourceGroups/<resourcegroupName>/providers/Microsoft.Batch/batchAccounts/<batchaccountname>/pools/<poolname>?api-version=2021-01-01
```

Corpo da solicitação

```json
{
    "name": "test2",
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
                        "name": "KVExtensions",
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
                                    "https://testkvwestus2s.vault.azure.net/secrets/authcertforumatesting/8f5f3f491afd48cb99286ba2aacd39af"
                                ]
                            },
                            "authenticationSettings": {
                                "msiEndpoint": "http://169.254.169.254/metadata/identity",
                                "msiClientId": "b9f6dd56-d2d6-4967-99d7-8062d56fd84c"
                            }  }, "protectedSettings":{}
                    }                ]            }
        },
        "scaleSettings": {
            "fixedScale": {
                "targetDedicatedNodes": 1,
                "resizeTimeout": "PT15M"
            }
        },
    },
    "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
            "/subscriptions/042998e4-36dc-4b7d-8ce3-a7a2c4877d33/resourceGroups/ACR/providers/Microsoft.ManagedIdentity/userAssignedIdentities/testumaforpools": {}
        }
    }
}

```

## <a name="validate-the-certificate"></a>Validar o certificado

Para confirmar que o certificado foi implantado com êxito, faça logon no nó de computação. Você deverá ver uma saída semelhante à seguinte:

```
root@74773db5fe1b42ab9a4b6cf679d929da000000:/var/lib/waagent/Microsoft.Azure.KeyVault.KeyVaultForLinux-1.0.1363.13/status# cat 1.status
[{"status":{"code":0,"formattedMessage":{"lang":"en","message":"Successfully started Key Vault extension service. 2021-03-03T23:12:23Z"},"operation":"Service start.","status":"success"},"timestampUTC":"2021-03-03T23:12:23Z","version":"1.0"}]root@74773db5fe1b42ab9a4b6cf679d929da000000:/var/lib/waagent/Microsoft.Azure.KeyVault.KeyVaultForLinux-1.0.1363.13/status#
```

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [identidades gerenciadas para recursos do Azure](../active-directory/managed-identities-azure-resources/overview.md).
- Saiba como usar [chaves gerenciadas pelo cliente com identidades gerenciadas pelo usuário](batch-customer-managed-key.md).
