---
title: Especificação de traga sua própria chave-Azure Key Vault | Microsoft Docs
description: Este documento descreveu traga sua própria especificação de chave.
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: conceptual
ms.date: 02/04/2021
ms.author: ambapat
ms.openlocfilehash: 141abea0c0946c98b6dfe627f32f01682a18be44
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99581016"
---
# <a name="bring-your-own-key-specification"></a>Especificação de Bring Your Own Key

Este documento descreve as especificações para importar chaves protegidas por HSM de HSMs locais de clientes para o Key Vault.

## <a name="scenario"></a>Cenário

Um cliente Key Vault gostaria de transferir com segurança uma chave de seu HSM local fora do Azure, para o backup de HSM Azure Key Vault. O processo de importação de uma chave gerada fora do Key Vault é geralmente conhecido como Bring Your Own Key (BYOK).

A seguir estão os requisitos:
* A chave a ser transferida nunca existe fora de um HSM em formato de texto sem formatação.
* Fora de um HSM, a chave a ser transferida é sempre protegida por uma chave mantida no Azure Key Vault HSM

## <a name="terminology"></a>Terminologia

|Nome da Chave|Tipo de Chave|Origem|Descrição|
|---|---|---|---|
|KEK (Chave de Troca de Chaves)|RSA|HSM do Azure Key Vault|Um par de chaves RSA com suporte de HSM gerado em Azure Key Vault
Chave de encapsulamento|AES|HSM do fornecedor|Uma chave AES [efêmeral] gerada pelo HSM local
Chave de destino|RSA, EC, AES (somente HSM gerenciado)|HSM do fornecedor|A chave a ser transferida para o HSM do Azure Key Vault

**Chave de troca de chaves**: uma chave com suporte do HSM que o cliente gera no cofre de chaves em que a chave BYOK será importada. Este KEK deve ter as seguintes propriedades:

* É uma chave RSA-HSM (4096 bits ou 3072 bits ou 2048 bits)
* Ele terá key_ops fixo (somente ' import '), que permitirá que ele seja usado somente durante BYOK
* Deve estar no mesmo cofre em que a chave de destino será importada

## <a name="user-steps"></a>Etapas do usuário

Para executar uma transferência de chave, um usuário executa as seguintes etapas:

1. Gerar KEK.
2. Recupere a chave pública do KEK.
3. Usando a ferramenta BYOK fornecida pelo fornecedor do HSM – importe o KEK para o HSM de destino e exporte a chave de destino protegida pelo KEK.
4. Importe a chave de destino protegida para Azure Key Vault.

Os clientes usam a ferramenta BYOK e a documentação fornecida pelo fornecedor do HSM para concluir as etapas 3. Ele produz um blob de transferência de chave (um arquivo ". byok").


## <a name="hsm-constraints"></a>Restrições de HSM

O HSM existente pode aplicar restrições à chave que eles gerenciam, incluindo:
* O HSM pode precisar ser configurado para permitir a exportação baseada em encapsulamento de chave
* Talvez seja necessário marcar a chave de destino CKA_EXTRACTABLE para que o HSM permita a exportação controlada
* Em alguns casos, o KEK e a chave de encapsulamento podem precisar ser marcados como CKA_TRUSTED. Isso permite que ele seja usado para encapsular chaves no HSM.

A configuração do HSM de origem é, em geral, fora do escopo desta especificação. A Microsoft espera que o fornecedor do HSM produza documentação que acompanha sua ferramenta BYOK para incluir essas etapas de configuração.

> [!NOTE]
> As etapas 1, 2 e 4 descritas abaixo podem ser executadas usando outras interfaces, como Azure PowerShell e o portal do Azure. Eles também podem ser executados programaticamente usando funções equivalentes no SDK do Key Vault.

### <a name="step-1-generate-kek"></a>Etapa 1: gerar KEK

Use o comando **AZ keyvault Key Create** para criar Kek com operações de chave definidas para importar. Anote o identificador de chave ' Kid ' retornado do comando abaixo.

```azurecli
az keyvault key create --kty RSA-HSM --size 4096 --name KEKforBYOK --ops import --vault-name ContosoKeyVaultHSM
```

### <a name="step-2-retrieve-the-public-key-of-the-kek"></a>Etapa 2: recuperar a chave pública do KEK

Baixe a parte da chave pública do KEK e armazene-a em um arquivo PEM.

```azurecli
az keyvault key download --name KEKforBYOK --vault-name ContosoKeyVaultHSM --file KEKforBYOK.publickey.pem
```

### <a name="steps-3-generate-key-transfer-blob-using-hsm-vendor-provided-byok-tool"></a>Etapas 3: gerar blob de transferência de chave usando a ferramenta BYOK fornecida pelo fornecedor do HSM

O cliente usará a ferramenta BYOK fornecida pelo fornecedor do HSM para criar um blob de transferência de chave (armazenado como um arquivo ". BYOK"). A chave pública KEK (como um arquivo. pem) será uma das entradas para essa ferramenta.

#### <a name="key-transfer-blob"></a>Blob de transferência de chaves
A longo prazo, a Microsoft gostaria de usar o mecanismo de CKM_RSA_AES_KEY_WRAP PKCS # 11 para transferir a chave de destino para Azure Key Vault, já que esse mecanismo produz um único BLOB e, o mais importante, a chave AES intermediária é manipulada por dois HSMs e tem a garantia de ser efêmera. Esse mecanismo não está disponível atualmente em alguns HSMs, mas a combinação de proteger a chave de destino com CKM_AES_KEY_WRAP_PAD usando uma chave AES e proteger a chave AES com CKM_RSA_PKCS_OAEP produz um blob equivalente.

O texto não criptografado da chave de destino depende do tipo de chave: 
* Para uma chave RSA, a chave privada ASN. 1 codificação de DER [RFC3447] encapsulada em PKCS # 8 [RFC5208] 
* Para uma chave de EC, a chave privada ASN. 1 codificação de DER [RFC5915] encapsulada no PKCS # 8 [RFC5208]
* Para uma chave de octeto, os bytes brutos da chave

Os bytes da chave de texto não criptografado são então transformados usando o mecanismo de CKM_RSA_AES_KEY_WRAP:
* Uma chave AES efêmera é gerada e criptografada com a chave RSA de encapsulamento usando RSA-OAEP com SHA1.
* A chave codificada em texto sem formatação é criptografada usando a chave AES usando a quebra de chave AES com preenchimento.
* A chave AES criptografada e a chave de texto não criptografado são concatenadas para produzir o blob de texto cifrado final.

O formato do blob de transferência usa a RFC7516 (serialização compacta de criptografia da Web JSON), principalmente como um veículo para entregar os metadados necessários ao serviço para a descriptografia correta.

Se CKM_RSA_AES_KEY_WRAP_PAD for usado, a serialização JSON do blob de transferência será:

```json
{
  "schema_version": "1.0.0",
  "header":
  {
    "kid": "<key identifier of the KEK>",
    "alg": "dir",
    "enc": "CKM_RSA_AES_KEY_WRAP"
  },
  "ciphertext":"BASE64URL(<ciphertext contents>)",
  "generator": "BYOK tool name and version; source HSM name and firmware version"
}

```

* Kid = identificador de chave de KEK. Para Key Vault chaves, ela tem esta aparência: https://ContosoKeyVaultHSM.vault.azure.net/keys/mykek/eba63d27e4e34e028839b53fac905621
* ALG = algoritmo. 
* Dir = modo direto, ou seja, o filho referenciado é usado para proteger diretamente o texto cifrado, que é uma representação precisa do CKM_RSA_AES_KEY_WRAP
* Generator = um campo informativo que denota o nome e a versão da ferramenta BYOK e o fabricante e modelo do HSM de origem. Essas informações são destinadas para uso em solução de problemas e suporte.

O blob JSON é armazenado em um arquivo com uma extensão ". byok" para que os clientes do Azure PowerShell/CLI os tratem corretamente quando os comandos ' Add-AzKeyVaultKey ' (PSH) ou ' AZ keyvault Key import ' (CLI) são usados.

### <a name="step-4-upload-key-transfer-blob-to-import-hsm-key"></a>Etapa 4: carregar o blob de transferência de chave para importar chave HSM

O cliente transferirá o blob de transferência de chave (arquivo ". byok") para uma estação de trabalho online e, em seguida, executará um comando **AZ keyvault Key Import** para importar esse blob como uma nova chave com suporte do HSM para Key Vault. 

Para importar uma chave RSA, use este comando:
```azurecli
az keyvault key import --vault-name ContosoKeyVaultHSM --name ContosoFirstHSMkey --byok-file KeyTransferPackage-ContosoFirstHSMkey.byok --ops encrypt decrypt
```
Para importar uma chave de curva elíptica, você precisa especificar o tipo de chave e o nome da curva.

```azurecli
az keyvault key import --vault-name ContosoKeyVaultHSM --name ContosoFirstHSMkey --byok-file --kty EC-HSM --curve-name "P-256" KeyTransferPackage-ContosoFirstHSMkey.byok --ops sign verify
```


Quando o comando acima é executado, ele resulta no envio de uma solicitação da API REST da seguinte maneira:

```
PUT https://contosokeyvaulthsm.vault.azure.net/keys/ContosoFirstHSMKey?api-version=7.0
```

Corpo da solicitação ao importar uma chave RSA:
```json
{
  "key": {
    "kty": "RSA-HSM",
    "key_ops": [
      "decrypt",
      "encrypt"
    ],
    "key_hsm": "<Base64 encoded BYOK_BLOB>"
  },
  "attributes": {
    "enabled": true
  }
}
```

Corpo da solicitação ao importar uma chave do EC:
```json
{
  "key": {
    "kty": "EC-HSM",
    "crv": "P-256",
    "key_ops": [
      "sign",
      "verify"
    ],
    "key_hsm": "<Base64 encoded BYOK_BLOB>"
  },
  "attributes": {
    "enabled": true
  }
}
```

o valor "key_hsm" é todo o conteúdo do KeyTransferPackage-ContosoFirstHSMkey. byok codificado no formato base64.

## <a name="references"></a>Referências
- [Guia do Desenvolvedor do Cofre de Chaves](../general/developers-guide.md)

## <a name="next-steps"></a>Próximas etapas
* Instruções BYOK passo a passo: [importar chaves protegidas por HSM para Key Vault (BYOK)](hsm-protected-keys-byok.md)

