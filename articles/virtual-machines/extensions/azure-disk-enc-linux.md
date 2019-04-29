---
title: Azure Disk Encryption para Linux | Microsoft Docs
description: Implante o Azure Disk Encryption na máquina virtual do Linux usando uma extensão da máquina virtual.
services: virtual-machines-linux
documentationcenter: ''
author: ejarvi
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/12/2018
ms.author: ejarvi
ms.openlocfilehash: 3ce881da4b683cf7034100d5044dd0f3c93edb52
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60800176"
---
# <a name="azure-disk-encryption-for-linux-microsoftazuresecurityazurediskencryptionforlinux"></a>Azure Disk Encryption para Linux (Microsoft.Azure.Security.AzureDiskEncryptionForLinux)

## <a name="overview"></a>Visão geral

Azure Disk Encryption aproveita o subsistema de dm-crypt no Linux para fornecer criptografia de disco completo em [selecionar distribuições de Linux do Azure](https://aka.ms/adelinux).  A solução é integrada ao Azure Key Vault para gerenciar as chaves de criptografia de disco e segredos.

## <a name="prerequisites"></a>Pré-requisitos

Para obter uma lista completa dos pré-requisitos, consulte [Pré-requisitos do Azure Disk Encryption](
../../security/azure-security-disk-encryption-prerequisites.md).

### <a name="operating-system"></a>Sistema operacional

O Azure Disk Encryption tem suporte nas versões selecionadas do servidor Linux.  Consulte as [perguntas frequentes sobre o Azure Disk Encryption](../../security/azure-security-disk-encryption-faq.md#bkmk_LinuxOSSupport) para a lista de distribuições do Linux que têm suporte.

### <a name="internet-connectivity"></a>Conectividade com a Internet

O Azure Disk Encryption para Linux exige conectividade com a Internet para acesso ao Active Directory, o Cofre de chaves, armazenamento e pontos de extremidade de gerenciamento de pacote.  Para obter mais informações, confira [Azure Disk Encryption](../../security/azure-security-disk-encryption-prerequisites.md).

## <a name="extension-schema"></a>Esquema de extensão

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2015-06-15",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientSecret": "[aadClientSecret]",
      "Passphrase": "[passphrase]"
    },
    "publisher": "Microsoft.Azure.Security",
    "settings": {
      "AADClientID": "[aadClientID]",
      "DiskFormatQuery": "[diskFormatQuery]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KeyVaultURL": "[keyVaultURL]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    },
    "type": "AzureDiskEncryptionForLinux",
    "typeHandlerVersion": "[extensionVersion]"
  }
}
```

### <a name="property-values"></a>Valores de propriedade

| Nome | Valor/Exemplo | Tipo de Dados |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| publicador | Microsoft.Azure.Security | string |
| Tipo | AzureDiskEncryptionForLinux | string |
| typeHandlerVersion | 0.1, 1.1 (VMSS) | int |
| AADClientID | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | GUID | 
| AADClientSecret | Senha | string |
| AADClientCertificate | impressão digital | string |
| DiskFormatQuery | {"dev_path":"","name":"","file_system":""} | Dicionário JSON |
| EncryptionOperation | EnableEncryption, EnableEncryptionFormatAll | string | 
| KeyEncryptionAlgorithm | 'RSA-OAEP', 'RSA-OAEP-256', 'RSA1_5' | string |
| KeyEncryptionKeyURL | url | string |
| KeyVaultURL | url | string |
| Senha | Senha | string | 
| SequenceVersion | uniqueidentifier | string |
| VolumeType | Sistema operacional, Dados, Tudo | string |

## <a name="template-deployment"></a>Implantação de modelo

Para obter um exemplo de implantação de modelo, consulte [Habilitar a criptografia em uma VM do Linux em execução](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm).

## <a name="azure-cli-deployment"></a>Implantação da CLI do Azure

As instruções podem ser encontradas na última [documentação da CLI do Azure](/cli/azure/vm/encryption?view=azure-cli-latest). 

## <a name="troubleshoot-and-support"></a>Solução de problemas e suporte

### <a name="troubleshoot"></a>Solução de problemas

Para solução de problemas, consulte o [Guia de solução de problemas do Azure Disk Encryption](../../security/azure-security-disk-encryption-tsg.md).

### <a name="support"></a>Suporte

Caso precise de mais ajuda em qualquer ponto deste artigo, entre em contato com os especialistas do Azure nos [fóruns do Azure e do Stack Overflow no MSDN](https://azure.microsoft.com/support/community/). Como alternativa, você pode registrar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione Obter suporte. Para saber mais sobre como usar o suporte do Azure, leia as [Perguntas frequentes sobre o suporte do Microsoft Azure](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre extensões de VM, consulte [Recursos e extensões da máquina virtual para Linux](features-linux.md).