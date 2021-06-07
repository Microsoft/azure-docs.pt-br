---
title: Como gerar e transferir as chaves protegidas por HSM – Azure Key Vault
description: Saiba como planejar, gerar e transferir suas chaves protegidas por HSM para usá-las com o Azure Key Vault. Também conhecido como BYOK ou Traga sua própria chave.
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: tutorial
ms.date: 02/24/2021
ms.author: ambapat
ms.openlocfilehash: a7e709ba9a4de5ff77524a2d2b1b64a5933131a2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "102489405"
---
# <a name="import-hsm-protected-keys-to-key-vault"></a>Importar chaves protegidas por HSM para o Key Vault

Para garantia extra, ao usar o Cofre da Chave do Azure, você pode importar ou gerar chaves em módulos de segurança de hardware (HSM) que nunca extrapolam o limite do HSM. Normalmente, este cenário é conhecido como *BYOK* ou Bring Your Own Key. O Azure Key Vault usa a família nCipher nShield de HSMs (com validação FIPS 140-2 Nível 2) para proteger suas chaves.

Essa funcionalidade não está disponível para a Azure China 21Vianet.

> [!NOTE]
> Para obter mais informações sobre o Cofre da Chave do Azure, consulte [O que é o Cofre da Chave do Azure?](../general/overview.md)  
> Para obter um tutorial de Introdução, que inclui a criação de um cofre da chaves para chaves de HSM protegido, confira [O que é o Azure Key Vault?](../general/overview.md).

## <a name="supported-hsms"></a>HSMs compatíveis

Há suporte para a transferência de chaves protegidas por HSM para o Key Vault por meio de dois métodos diferentes, dependendo dos HSMs utilizados. Use a tabela abaixo para determinar qual método deve ser usado para os HSMs a serem gerados e transfira as próprias chaves protegidas por HSM a serem usadas com o Azure Key Vault. 

|Nome do Fornecedor|Tipo de fornecedor|Modelos de HSM compatíveis|Método de transferência de chave HSM compatível|
|---|---|---|---|
|[nCipher](https://www.ncipher.com/products/key-management/cloud-microsoft-azure)|Fabricante,<br/>HSM como serviço|<ul><li>Família de HSMs nShield</li><li>nShield como serviço</ul>|**Método 1:** [BYOK nCipher](hsm-protected-keys-ncipher.md) (preterido)<br/>**Método 2:** [usar o novo método BYOK](hsm-protected-keys-byok.md) (recomendado)|
|Thales|Fabricante|<ul><li>Família HSM 7 da Luna com versão de firmware 7.3 ou mais recente</li></ul>| [usar o novo método BYOK](hsm-protected-keys-byok.md)|
|Fortanix|Fabricante,<br/>HSM como serviço|<ul><li>SDKMS (Serviço de Gerenciamento de Chaves de Proteção Automática)</li><li>Equinix SmartKey</li></ul>|[usar o novo método BYOK](hsm-protected-keys-byok.md)|
|Marvell|Fabricante|Todos os HSMs LiquidSecurity com<ul><li>Firmware versão 2.0.4 ou posterior</li><li>Firmware versão 3.2 ou mais recente</li></ul>|[usar o novo método BYOK](hsm-protected-keys-byok.md)|
|Cryptomathic|ISV (Sistema de Gerenciamento de Chaves Empresariais)|Várias marcas e modelos de HSM, incluindo<ul><li>nCipher</li><li>Thales</li><li>Utimaco</li></ul>Acesse o [site da Cryptomathic para obter detalhes](https://www.cryptomathic.com/azurebyok)|[usar o novo método BYOK](hsm-protected-keys-byok.md)|
|Securosys SA|Fabricante,<br/>HSM como serviço|Família HSM Primus, Securosys Clouds HSM|[usar o novo método BYOK](hsm-protected-keys-byok.md)|
|StorMagic|ISV (Sistema de Gerenciamento de Chaves Empresariais)|Várias marcas e modelos de HSM, incluindo<ul><li>Utimaco</li><li>Thales</li><li>nCipher</li></ul>Confira o [site da StorMagic para obter detalhes](https://stormagic.com/doc/svkms/Content/Integrations/Azure_KeyVault_BYOK.htm)|[usar o novo método BYOK](hsm-protected-keys-byok.md)|
|IBM|Fabricante|IBM 476x, CryptoExpress|[usar o novo método BYOK](hsm-protected-keys-byok.md)|
|Utimaco|Fabricante,<br/>HSM como serviço|Âncora u.trust, CryptoServer|[usar o novo método BYOK](hsm-protected-keys-byok.md)|
|||||

## <a name="next-steps"></a>Próximas etapas

* Examine a [Visão geral de segurança do Key Vault](../general/security-overview.md) para garantir a segurança, a durabilidade e o monitoramento das suas chaves.
* Veja [Especificação de BYOK](./byok-specification.md) para obter uma descrição completa do novo método BYOK