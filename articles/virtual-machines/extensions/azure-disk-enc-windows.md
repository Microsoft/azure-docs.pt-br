---
title: Azure Disk Encryption para o Windows
description: Implante o Azure Disk Encryption na máquina virtual do Windows usando uma extensão da máquina virtual.
ms.topic: article
ms.service: virtual-machines
ms.subservice: disks
author: ejarvi
ms.author: ejarvi
ms.collection: windows
ms.date: 03/19/2020
ms.openlocfilehash: 10268f8041f21f74e8ebcfaee41d207a53618260
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102566236"
---
# <a name="azure-disk-encryption-for-windows-microsoftazuresecurityazurediskencryption"></a>Azure Disk Encryption para Windows (Microsoft.Azure.Security.AzureDiskEncryption)

## <a name="overview"></a>Visão geral

O Azure Disk Encryption utiliza o Bitlocker para fornecer criptografia de disco completa em máquinas virtuais do Azure que executam o Windows.  Está solução é integrada ao Azure Key Vault para gerenciar os segredos e as chaves de criptografia de disco em sua assinatura do cofre de chaves. 

## <a name="prerequisites"></a>Pré-requisitos

Para obter uma lista completa de pré-requisitos, consulte [Azure Disk Encryption para VMs do Windows](../windows/disk-encryption-overview.md), especificamente as seguintes seções:

- [Sistemas operacionais e VMs com suporte](../windows/disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Requisitos de rede](../windows/disk-encryption-overview.md#networking-requirements)
- [Requisitos de Política de Grupo](../windows/disk-encryption-overview.md#group-policy-requirements)

## <a name="extension-schema"></a>Esquema de Extensão

Há duas versões do esquema de extensão para Azure Disk Encryption (ADE):
- v 2.2-um esquema recomendado mais recente que não usa as propriedades Azure Active Directory (AAD).
- v 1.1-um esquema mais antigo que requer Propriedades Azure Active Directory (AAD). 

Para selecionar um esquema de destino, a `typeHandlerVersion` propriedade deve ser definida igual à versão do esquema que você deseja usar.

### <a name="schema-v22-no-aad-recommended"></a>Esquema v 2.2: sem AAD (recomendado)

O esquema v 2.2 é recomendado para todas as novas VMs e não requer Azure Active Directory Propriedades.

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2019-07-01",
  "location": "[location]",
  "properties": {
        "publisher": "Microsoft.Azure.Security",
        "type": "AzureDiskEncryption",
        "typeHandlerVersion": "2.2",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "EncryptionOperation": "[encryptionOperation]",
          "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
          "KeyVaultURL": "[keyVaultURL]",
          "KeyVaultResourceId": "[keyVaultResourceID]",
          "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
          "KekVaultResourceId": "[kekVaultResourceID]",
          "SequenceVersion": "sequenceVersion]",
          "VolumeType": "[volumeType]"
        }
  }
}
```


### <a name="schema-v11-with-aad"></a>Esquema v 1.1: com AAD 

O esquema 1,1 requer o `aadClientID` e o `aadClientSecret` ou `AADClientCertificate` e não é recomendado para novas VMS.

Usando `aadClientSecret`:

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2019-07-01",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientSecret": "[aadClientSecret]"
    },    
    "publisher": "Microsoft.Azure.Security",
    "type": "AzureDiskEncryption",
    "typeHandlerVersion": "1.1",
    "settings": {
      "AADClientID": "[aadClientID]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyVaultURL": "[keyVaultURL]",
      "KeyVaultResourceId": "[keyVaultResourceID]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KekVaultResourceId": "[kekVaultResourceID]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    }
  }
}
```

Usando `AADClientCertificate`:

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2019-07-01",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientCertificate": "[aadClientCertificate]"
    },    
    "publisher": "Microsoft.Azure.Security",
    "type": "AzureDiskEncryption",
    "typeHandlerVersion": "1.1",
    "settings": {
      "AADClientID": "[aadClientID]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyVaultURL": "[keyVaultURL]",
      "KeyVaultResourceId": "[keyVaultResourceID]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KekVaultResourceId": "[kekVaultResourceID]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    }
  }
}
```


### <a name="property-values"></a>Valores de propriedade

| Nome | Valor/Exemplo | Tipo de Dados |
| ---- | ---- | ---- |
| apiVersion | 2019-07-01 | date |
| publicador | Microsoft.Azure.Security | string |
| type | AzureDiskEncryption | string |
| typeHandlerVersion | 2,2, 1,1 | string |
| (esquema 1,1) AADClientID | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | guid | 
| (esquema 1,1) AADClientSecret | password | string |
| (esquema 1,1) AADClientCertificate | thumbprint | string |
| EncryptionOperation | EnableEncryption | string | 
| (opcional-padrão RSA-OAEP) KeyEncryptionAlgorithm | 'RSA-OAEP', 'RSA-OAEP-256', 'RSA1_5' | string |
| KeyVaultURL | url | string |
| KeyVaultResourceId | url | string |
| adicional KeyEncryptionKeyURL | url | string |
| adicional KekVaultResourceId | url | string |
| adicional SequenceVersion | UNIQUEIDENTIFIER | string |
| VolumeType | Sistema operacional, Dados, Tudo | string |

## <a name="template-deployment"></a>Implantação de modelo

Para obter um exemplo de implantação de modelo com base no esquema v 2.2, consulte modelo de início rápido do Azure [201-Encrypt-running-Windows-VM-sem-AAD](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm-without-aad).

Para obter um exemplo de implantação de modelo com base no esquema v 1.1, consulte modelo de início rápido do Azure [201-Encrypt-running-Windows-VM](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm).

>[!NOTE]
> Além disso `VolumeType` , se o parâmetro for definido como todos, os discos de dados serão criptografados somente se estiverem formatados corretamente. 

## <a name="troubleshoot-and-support"></a>Solução de problemas e suporte

### <a name="troubleshoot"></a>Solucionar problemas

Para solução de problemas, consulte o [Guia de solução de problemas do Azure Disk Encryption](../windows/disk-encryption-troubleshooting.md).

### <a name="support"></a>Suporte

Caso precise de mais ajuda em qualquer ponto deste artigo, entre em contato com os especialistas do Azure nos [fóruns do Azure e do Stack Overflow no MSDN](https://azure.microsoft.com/support/community/). 

Como alternativa, você pode registrar um incidente de suporte do Azure. Vá para o [suporte do Azure](https://azure.microsoft.com/support/options/) e selecione obter suporte. Para obter informações sobre como usar o suporte do Azure, leia as [perguntas frequentes sobre suporte do Microsoft Azure](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Próximas etapas

* Para obter mais informações sobre extensões, consulte [Recursos e extensões da máquina virtual para Windows](features-windows.md).
* Para obter mais informações sobre Azure Disk Encryption para Windows, consulte [máquinas virtuais do Windows](../../security/fundamentals/azure-disk-encryption-vms-vmss.md#windows-virtual-machines).
