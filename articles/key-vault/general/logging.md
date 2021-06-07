---
title: Registro em Log do Azure Key Vault | Microsoft Docs
description: Saiba como monitorar o acesso aos seus cofres de chaves habilitando o log no Azure Key Vault, que salva as informações em uma conta de armazenamento do Azure fornecida por você.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 12/18/2020
ms.author: mbaldwin
ms.openlocfilehash: 5847fcb2cf553e1fcc744877e52dbbdf1f24d992
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107751824"
---
# <a name="azure-key-vault-logging"></a>Log do Azure Key Vault

Depois de criar um ou mais cofres de chaves, provavelmente você desejará monitorar como e quando os cofres de chaves serão acessados e por quem. Você pode fazer isso habilitando o registro em log do Azure Key Vault, que salva as informações em uma conta de armazenamento do Azure fornecida por você. Para obter diretrizes passo a passo sobre essa configuração, confira [Como habilitar o registro em log do Key Vault](howto-logging.md).

Você pode acessar suas informações de log 10 minutos (no máximo) após a operação do cofre de chaves. Na maioria dos casos, será mais rápido do que isso.  Cabe a você gerenciar os logs em sua conta de armazenamento:

* Use os métodos padrões de controle de acesso do Azure na sua conta de armazenamento para proteger seus logs ao restringir quem pode acessá-los.
* Exclua os logs que você não deseja manter em sua conta de armazenamento.

Para obter informações de visão geral sobre o Key Vault, consulte [O que é o Azure Key Vault?](overview.md). Para obter informações sobre onde o Key Vault está disponível, consulte a [página de preços](https://azure.microsoft.com/pricing/details/key-vault/). Para obter informações sobre como usar o [Azure Monitor para Key Vault](../../azure-monitor/insights/key-vault-insights-overview.md).

## <a name="interpret-your-key-vault-logs"></a>Interpretar os logs do Cofre de Chave

Quando você habilita o registro em log, um contêiner chamado **insights-logs-auditevent** é criado automaticamente para a conta de armazenamento especificada. Você pode usar essa mesma conta de armazenamento para coletar logs de vários cofres de chaves.

Os blobs individuais são armazenados como texto, formatados como um blob JSON. Vamos examinar um exemplo de entrada de log. 

```json
    {
        "records":
        [
            {
                "time": "2016-01-05T01:32:01.2691226Z",
                "resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT",
                "operationName": "VaultGet",
                "operationVersion": "2015-06-01",
                "category": "AuditEvent",
                "resultType": "Success",
                "resultSignature": "OK",
                "resultDescription": "",
                "durationMs": "78",
                "callerIpAddress": "104.40.82.76",
                "correlationId": "",
                "identity": {"claim":{"http://schemas.microsoft.com/identity/claims/objectidentifier":"d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn":"live.com#username@outlook.com","appid":"1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},
                "properties": {"clientInfo":"azure-resource-manager/2.0","requestUri":"https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id":"https://contosokeyvault.vault.azure.net/","httpStatusCode":200}
            }
        ]
    }
```

A tabela a seguir lista os nomes e as descrições de campo:

| Nome do campo | Descrição |
| --- | --- |
| **time** |Data e hora em UTC. |
| **resourceId** |ID do Recurso do Azure Resource Manager. Para os logs do Cofre da Chave, isso sempre será a ID do recurso do Cofre da Chave. |
| **operationName** |Nome da operação, como documentado na tabela a seguir. |
| **operationVersion** |Versão da API REST solicitada pelo cliente. |
| **category** |Tipo de resultado. Para logs do Key Vault, `AuditEvent` é o único valor disponível. |
| **resultType** |Resultado da solicitação à API REST. |
| **resultSignature** |Código de status HTTP. |
| **resultDescription** |Descrição adicional sobre o resultado, quando disponível. |
| **durationMs** |Tempo necessário para atender à solicitação da API REST, em milissegundos. Isso não inclui a latência de rede e, portanto, o tempo medido no lado cliente pode não corresponder a esse tempo. |
| **callerIpAddress** |Endereço IP do cliente que fez o a solicitação. |
| **correlationId** |Um GUID opcional que o cliente pode passar para correlacionar os logs do lado do cliente aos logs do lado do serviço (Chave do Cofre). |
| **identidade** |Identidade do token que foi apresentado na solicitação à API REST. Geralmente, é um "usuário", uma "entidade de serviço" ou uma combinação "usuário+appId" como no caso de uma solicitação resultante de um cmdlet do Azure PowerShell. |
| **properties** |Informações que variam de acordo com a operação (**operationName**). Na maioria dos casos, este campo contém informações de cliente (a cadeia de caracteres do agente do usuário passada pelo cliente), o URI exato de solicitação da API REST e o código de status HTTP. Além disso, quando um objeto é retornado como resultado de uma solicitação (por exemplo, **KeyCreate** ou **VaultGet**), também conterá o URI da chave (como `id`), o URI do cofre ou o URI do segredo. |

Os valores do campo **operationName** estão no formato *ObjectVerb*. Por exemplo:

* Todas as operações do cofre de chaves têm o formato `Vault<action>`, como `VaultGet` e `VaultCreate`.
* Todas as operações de chave têm o formato `Key<action>`, como `KeySign` e `KeyList`.
* Todas as operações de segredo têm o formato `Secret<action>`, como `SecretGet` e `SecretListVersions`.

A tabela a seguir lista os valores de **operationName** e os comandos da API REST correspondentes:

### <a name="operation-names-table"></a>Tabela de nomes de operação

# <a name="vault"></a>[Cofre](#tab/Vault)

| operationName | Comando da API REST |
| --- | --- |
| **Autenticação** |Autenticação via ponto de extremidade do Azure Active Directory |
| **VaultGet** |[Obter informações sobre um cofre de chaves](/rest/api/keyvault/vaults) |
| **VaultPut** |[Criar ou atualizar um cofre de chaves](/rest/api/keyvault/vaults) |
| **VaultDelete** |[Excluir um cofre de chaves](/rest/api/keyvault/vaults) |
| **VaultPatch** |[Atualizar um cofre da chave](/rest/api/keyvault/vaults) |
| **VaultList** |[Listar todos os cofres de chaves em um grupo de recursos](/rest/api/keyvault/vaults) |
| **VaultPurge** |[Limpar um cofre excluído](/rest/api/keyvault/vaults/purgedeleted) |
| **VaultRecover** |Recuperar um cofre excluído|
| **VaultGetDeleted** |[Obter um cofre excluído](/rest/api/keyvault/vaults/getdeleted) |
| **VaultListDeleted** |[Listar os cofres excluídos](/rest/api/keyvault/vaults/listdeleted) |
| **VaultAccessPolicyChangedEventGridNotification** | Evento de alteração da política de acesso ao cofre publicado |

# <a name="keys"></a>[Chaves](#tab/Keys)

| operationName | Comando da API REST |
| --- | --- |
| **KeyCreate** |[Criar uma chave](/rest/api/keyvault/createkey) |
| **KeyGet** |[Obter informações sobre uma chave](/rest/api/keyvault/getkey) |
| **KeyImport** |[Importar uma chave para um cofre](/rest/api/keyvault/vaults) |
| **KeyDelete** |[Excluir uma chave](/rest/api/keyvault/deletekey) |
| **KeySign** |[Assinar com uma chave](/rest/api/keyvault/sign) |
| **KeyVerify** |[Verificar com uma chave](/rest/api/keyvault/vaults) |
| **KeyWrap** |[Encapsular uma chave](/rest/api/keyvault/wrapkey) |
| **KeyUnwrap** |[Desencapsular uma chave](/rest/api/keyvault/unwrapkey) |
| **KeyEncrypt** |[Criptografar com uma chave](/rest/api/keyvault/encrypt) |
| **KeyDecrypt** |[Descriptografar com uma chave](/rest/api/keyvault/decrypt) |
| **KeyUpdate** |[Atualizar uma chave](/rest/api/keyvault/updatekey) |
| **KeyList** |[Listar as chaves em um cofre](/rest/api/keyvault/getkeys) |
| **KeyListVersions** |[Listar as versões de uma chave](/rest/api/keyvault/getkeyversions) |
| **KeyPurge** |[Limpar uma chave](/rest/api/keyvault/purgedeletedkey) |
| **KeyBackup** |[Fazer backup de uma chave](/rest/api/keyvault/backupkey) |
| **KeyRestore** |[Restaurar uma chave](/rest/api/keyvault/restorekey) |
| **KeyRecover** |[Recuperar uma chave](/rest/api/keyvault/recoverdeletedkey) |
| **KeyGetDeleted** |[Obter uma chave excluída](/rest/api/keyvault/getdeletedkey) |
| **KeyListDeleted** |[Listar as chaves excluídas em um cofre](/rest/api/keyvault/getdeletedkeys) |
| **KeyNearExpiryEventGridNotification** |Evento de chave próxima do vencimento publicado |
| **KeyExpiredEventGridNotification** |Evento de chave vencida publicado |

# <a name="secrets"></a>[Segredos](#tab/Secrets)

| operationName | Comando da API REST |
| --- | --- |
| **SecretSet** |[Criar um segredo](/rest/api/keyvault/updatecertificate) |
| **SecretGet** |[Obter um segredo](/rest/api/keyvault/getsecret) |
| **SecretUpdate** |[Atualizar um segredo](/rest/api/keyvault/updatesecret) |
| **SecretDelete** |[Excluir um segredo](/rest/api/keyvault/deletesecret) |
| **SecretList** |[Listar segredos em um cofre](/rest/api/keyvault/getsecrets) |
| **SecretListVersions** |[Listar versões de um segredo](/rest/api/keyvault/getsecretversions) |
| **SecretPurge** |[Limpar um segredo](/rest/api/keyvault/purgedeletedsecret) |
| **SecretBackup** |[Fazer backup de um segredo](/rest/api/keyvault/backupsecret) |
| **SecretRestore** |[Restaurar um segredo](/rest/api/keyvault/restoresecret) |
| **SecretRecover** |[Recuperar um segredo](/rest/api/keyvault/recoverdeletedsecret) |
| **SecretGetDeleted** |[Obter um segredo excluído](/rest/api/keyvault/getdeletedsecret) |
| **SecretListDeleted** |[Listar os segredos excluídos em um cofre](/rest/api/keyvault/getdeletedsecrets) |
| **SecretNearExpiryEventGridNotification** |Evento de segredo próximo do vencimento publicado |
| **SecretExpiredEventGridNotification** |Evento de segredo vencido publicado |

# <a name="certificates"></a>[Certificados](#tab/Cerificates)

| operationName | Comando da API REST |
| --- | --- |
| **CertificateGet** |[Obter informações sobre um certificado](/rest/api/keyvault/getcertificate) |
| **CertificateCreate** |[Criar um certificado](/rest/api/keyvault/createcertificate) |
| **CertificateImport** |[Importar um certificado em um cofre](/rest/api/keyvault/importcertificate) |
| **CertificateUpdate** |[Atualizar um certificado](/rest/api/keyvault/updatecertificate) |
| **CertificateList** |[Listar os certificados em um cofre](/rest/api/keyvault/getcertificates) |
| **CertificateListVersions** |[Listar as versões de um certificado](/rest/api/keyvault/getcertificateversions) |
| **CertificateDelete** |[Excluir um certificado](/rest/api/keyvault/deletecertificate) |
| **CertificatePurge** |[Limpar um certificado](/rest/api/keyvault/purgedeletedcertificate) |
| **CertificateBackup** |[Fazer backup de um certificado](/rest/api/keyvault/backupcertificate) |
| **CertificateRestore** |[Restaurar um certificado](/rest/api/keyvault/restorecertificate) |
| **CertificateRecover** |[Recuperar um certificado](/rest/api/keyvault/recoverdeletedcertificate) |
| **CertificateGetDeleted** |[Obter um certificado excluído](/rest/api/keyvault/getdeletedcertificate) |
| **CertificateListDeleted** |[Listar os certificados excluídos em um cofre](/rest/api/keyvault/getdeletedcertificates) |
| **CertificatePolicyGet** |[Obter uma política de certificação](/rest/api/keyvault/getcertificatepolicy) |
| **CertificatePolicyUpdate** |[Atualizar uma política de certificação](/rest/api/keyvault/updatecertificatepolicy) |
| **CertificatePolicySet** |[Criar uma política de certificação](/rest/api/keyvault/createcertificate) |
| **CertificateContactsGet** |[Obter contatos de certificado](/rest/api/keyvault/getcertificatecontacts) |
| **CertificateContactsSet** |[Definir contatos de certificado](/rest/api/keyvault/setcertificatecontacts) |
| **CertificateContactsDelete** |[Excluir contatos de certificado](/rest/api/keyvault/deletecertificatecontacts) |
| **CertificateIssuerGet** |[Obter o emissor do certificado](/rest/api/keyvault/getcertificateissuer) |
| **CertificateIssuerSet** |[Definir o emissor do certificado](/rest/api/keyvault/setcertificateissuer) |
| **CertificateIssuerUpdate** |[Atualizar o emissor do certificado](/rest/api/keyvault/updatecertificateissuer) |
| **CertificateIssuerDelete** |[Excluir o emissor do certificado](/rest/api/keyvault/deletecertificateissuer) |
| **CertificateIssuersList** |[Listar os emissores do certificado](/rest/api/keyvault/getcertificateissuers) |
| **CertificateEnroll** |Registrar um certificado |
| **CertificateRenew** |{1&gt;Renovar um certificado&lt;1} |
| **CertificatePendingGet** |Recuperar certificado pendente |
| **CertificatePendingMerge** |Colocar uma mesclagem de certificado como pendente |
| **CertificatePendingUpdate** |Colocar uma atualização de certificado como pendente |
| **CertificatePendingDelete** |Excluir certificado pendente |
| **CertificateNearExpiryEventGridNotification** |Evento de certificado próximo do vencimento publicado |
| **CertificateExpiredEventGridNotification** |Evento de certificado vencido publicado |

---

## <a name="use-azure-monitor-logs"></a>Usar os logs do Azure Monitor

É possível usar a solução do Key Vault nos logs do Azure Monitor para examinar os logs `AuditEvent` do Key Vault. Nos logs do Azure Monitor, você usa consultas de log para analisar dados e obter as informações necessárias. 

Para obter mais informações, incluindo como configurar isso, confira [Azure Key Vault no Azure Monitor](../../azure-monitor/insights/key-vault-insights-overview.md).

## <a name="next-steps"></a>Próximas etapas

- [Como habilitar o registro em log do Key Vault](howto-logging.md)
- [Azure Monitor](../../azure-monitor/index.yml)
- Para obter um tutorial que usa o Azure Key Vault em um aplicativo Web .NET, confira [Usar o Azure Key Vault em um aplicativo Web](tutorial-net-create-vault-azure-web-app.md).
- Para referências de programação, consulte [Guia do desenvolvedor do Cofre da Chave do Azure](developers-guide.md).
- Para obter uma lista dos cmdlets do Azure PowerShell 1.0 para o Azure Key Vault, confira [Cmdlets do Azure Key Vault](/powershell/module/az.keyvault/#key_vault).