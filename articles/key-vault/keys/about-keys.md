---
title: Sobre chaves – Azure Key Vault
description: Visão geral dos detalhes da interface REST e do desenvolvedor do Azure Key Vault para as chaves.
services: key-vault
author: amitbapat
manager: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: overview
ms.date: 02/17/2021
ms.author: ambapat
ms.openlocfilehash: 3c4bb61217c7b972220a55a4837c2b3db980f2ca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101095982"
---
# <a name="about-keys"></a>Sobre chaves

O Azure Key Vault fornece dois tipos de recursos para armazenar e gerenciar chaves de criptografia. Os cofres dão suporte a chaves protegidas por software e por HSM (Módulo de segurança de hardware). HSMs gerenciados dão suporte apenas a chaves protegidas por HSM. 

|Tipo de recurso|Métodos de proteção da chave|URL base do ponto de extremidade do plano de dados|
|--|--|--|
| **Cofres** | Protegida por software<br/><br/>e<br/><br/>Protegida por HSM (com SKU Premium)</li></ul> | https://{vault-name}.vault.azure.net |
| **HSMs gerenciados** | Protegida por HSM | https://{hsm-name}.managedhsm.azure.net |
||||

- **Cofres** – os cofres oferecem uma solução de gerenciamento de chaves altamente disponível, de baixo custo, fácil de implantar, multilocatário (quando disponível) adequada para os cenários de aplicativos de nuvem mais comuns.
- **HSMs gerenciados** – o HSM gerenciado oferece HSMs altamente disponíveis, de locatário único, com resiliência de zona (quando disponível), para armazenar e gerenciar suas chaves de criptografia. É mais adequado para aplicativos e cenários de uso que lidam com chaves de alto valor. Também ajuda a alcançar os requisitos mais rigorosos de segurança, conformidade e regulamentação. 

> [!NOTE]
> Os cofres também permitem que você armazene e gerencie vários tipos de objetos, como segredos, certificados e chaves de conta de armazenamento, além das chaves criptográficas.

As chaves de criptografia no Key Vault são representadas como objetos de chave da Web JSON [JWK]. As especificações do JSON (JavaScript Object Notation) e do JOSE (JavaScript Object Signing and Encryption) são:

-   [Chave da Web JSON (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key)  
-   [Criptografia de Web JSON (JWE)](http://tools.ietf.org/html/draft-ietf-jose-json-web-encryption)  
-   [Algoritmos da Web JSON (JWA)](http://tools.ietf.org/html/draft-ietf-jose-json-web-algorithms)  
-   [Assinatura da Web JSON (JWS)](https://tools.ietf.org/html/draft-ietf-jose-json-web-signature) 

As especificações de base JWK/JWA também são estendidas para habilitar tipos de chave exclusivos para as implementações do Azure Key Vault e de HSM Gerenciado. 

As chaves protegidas por HSM (também conhecidas como chaves HSM) são processadas em um HSM (módulo de segurança de hardware) e sempre permanecem no limite de proteção do HSM. 

- Os cofres usam HSMs validados **FIPS 140-2 Nível 2** para proteger as chaves HSM na infraestrutura de back-end do HSM compartilhado. 
- O HSM gerenciado usa módulos HSM validados **FIPS 140-2 Nível 3** para proteger suas chaves. Cada pool de HSM é uma instância de locatário única isolada com seu próprio [domínio de segurança](../managed-hsm/security-domain.md) que fornece isolamento de criptografia completo em relação a todos os outros HSMs que compartilham a mesma infraestrutura de hardware.

Essas chaves são protegidas em pools de HSM de locatário único. Você pode importar uma RSA, EC e uma chave simétrica em forma flexível ou exportar de um dispositivo HSM com suporte. Você também pode gerar chaves em pools de HSM. Quando você importa chaves HSM usando o método descrito na [especificação de BYOK (Bring Your Own Key)](../keys/byok-specification.md), isso habilita o material de chave de transporte seguro em pools do HSM Gerenciado. 

Para obter mais informações sobre fronteiras geográficas, consulte [Microsoft Azure Trust Center](https://azure.microsoft.com/support/trust-center/privacy/)

## <a name="key-types-and-protection-methods"></a>Tipos de chave e métodos de proteção

O Key Vault dá suporte a chaves de RSA e EC. O HSM Gerenciado dá suporte a chaves de RSA, EC e simétricas. 

### <a name="hsm-protected-keys"></a>Chaves protegidas por HSM

|Tipo de chave|Cofres (somente SKU Premium)|HSMs gerenciados|
|--|--|--|
|**EC-HSM**: Chave de curva elíptica | Com suporte | Com suporte|
|**RSA-HSM**: Chave RSA|Com suporte|Com suporte|
|**oct-HSM**: chave simétrica|Sem suporte|Com suporte|
|||

### <a name="software-protected-keys"></a>Chaves protegidas por software

|Tipo de chave|Cofres|HSMs gerenciados|
|--|--|--|
**RSA**: chave RSA "protegida por software"|Com suporte|Sem suporte
**EC**: chave de Curva Elíptica "protegida por software"|Com suporte|Sem suporte
|||

### <a name="compliance"></a>Conformidade

|Tipo de chave e destino|Conformidade|
|---|---|
|Chaves protegidas por software em cofres (SKUs Premium e Standard) | FIPS 140-2 Nível 1|
|Chaves protegidas por HSM em cofres (SKU Premium)| FIPS 140-2 Nível 2|
|Chaves protegidas por HSM no HSM Gerenciado|FIPS 140-2 Nível 3|
|||



Confira [Tipos de chave, algoritmos e operações](about-keys-details.md) para obter detalhes sobre cada tipo de chave, algoritmo, operação, atributo e marcação.

## <a name="next-steps"></a>Próximas etapas
- [Sobre o Key Vault](../general/overview.md)
- [Sobre o HSM Gerenciado](../managed-hsm/overview.md)
- [Sobre segredos](../secrets/about-secrets.md)
- [Sobre certificados](../certificates/about-certificates.md)
- [Visão geral da API REST do Key Vault](../general/about-keys-secrets-certificates.md)
- [Autenticação, solicitações e respostas](../general/authentication-requests-and-responses.md)
- [Guia do Desenvolvedor do Cofre de Chaves](../general/developers-guide.md)
