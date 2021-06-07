---
title: Fornecer uma chave de criptografia em uma solicitação para o armazenamento de BLOBs
titleSuffix: Azure Storage
description: Os clientes que fazem solicitações no armazenamento de BLOBs do Azure têm a opção de fornecer uma chave de criptografia por solicitação. Incluir a chave de criptografia na solicitação fornece controle granular das configurações de criptografia para operações de armazenamento de BLOBs.
services: storage
author: tamram
ms.service: storage
ms.date: 12/14/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: fcc5c02c4a37e205622470260d3c620ad76d07d8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97694700"
---
# <a name="provide-an-encryption-key-on-a-request-to-blob-storage"></a>Fornecer uma chave de criptografia em uma solicitação para o armazenamento de BLOBs

Os clientes que fazem solicitações no armazenamento de BLOBs do Azure têm a opção de fornecer uma chave de criptografia AES-256 por solicitação. Incluir a chave de criptografia na solicitação fornece controle granular das configurações de criptografia para operações de armazenamento de BLOBs. As chaves fornecidas pelo cliente podem ser armazenadas em Azure Key Vault ou em outro repositório de chaves.

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="encrypting-read-and-write-operations"></a>Criptografando operações de leitura e gravação

Quando um aplicativo cliente fornece uma chave de criptografia na solicitação, o armazenamento do Azure executa criptografia e descriptografia de forma transparente ao ler e gravar dados de BLOB. O armazenamento do Azure grava um hash SHA-256 da chave de criptografia ao lado do conteúdo do blob. O hash é usado para verificar se todas as operações subsequentes no blob usam a mesma chave de criptografia.

O armazenamento do Azure não armazena ou gerencia a chave de criptografia que o cliente envia com a solicitação. A chave será descartada com segurança assim que o processo de criptografia ou descriptografia estiver concluído.

Quando um cliente cria ou atualiza um BLOB usando uma chave fornecida pelo cliente na solicitação, as solicitações de leitura e gravação subsequentes para esse blob também devem fornecer a chave. Se a chave não for fornecida em uma solicitação para um blob que já foi criptografado com uma chave fornecida pelo cliente, a solicitação falhará com o código de erro 409 (conflito).

Se o aplicativo cliente enviar uma chave de criptografia na solicitação e a conta de armazenamento também for criptografada usando uma chave gerenciada pela Microsoft ou uma chave gerenciada pelo cliente, o armazenamento do Azure usará a chave fornecida na solicitação de criptografia e descriptografia.

Para enviar a chave de criptografia como parte da solicitação, um cliente deve estabelecer uma conexão segura com o armazenamento do Azure usando HTTPS.

Cada instantâneo de blob pode ter sua própria chave de criptografia.

## <a name="request-headers-for-specifying-customer-provided-keys"></a>Cabeçalhos de solicitação para especificar chaves fornecidas pelo cliente

Para chamadas REST, os clientes podem usar os seguintes cabeçalhos para transmitir informações de chave de criptografia com segurança em uma solicitação para o armazenamento de BLOBs:

|Cabeçalho da solicitação | Descrição |
|---------------|-------------|
|`x-ms-encryption-key` |Necessário para solicitações de gravação e leitura. Um valor de chave de criptografia AES-256 codificado na base64. |
|`x-ms-encryption-key-sha256`| Necessário para solicitações de gravação e leitura. A SHA256 codificada em base64 da chave de criptografia. |
|`x-ms-encryption-algorithm` | Necessário para solicitações de gravação, opcional para solicitações de leitura. Especifica o algoritmo a ser usado ao criptografar dados usando a chave especificada.  O valor desse cabeçalho deve ser `AES256` . |

A especificação de chaves de criptografia na solicitação é opcional. No entanto, se você especificar um dos cabeçalhos listados acima para uma operação de gravação, deverá especificar todos eles.

## <a name="blob-storage-operations-supporting-customer-provided-keys"></a>Operações de armazenamento de BLOBs que dão suporte a chaves fornecidas pelo cliente

As operações de armazenamento de blob a seguir dão suporte ao envio de chaves de criptografia fornecidas pelo cliente em uma solicitação:

- [Put Blob](/rest/api/storageservices/put-blob)
- [Put Block List](/rest/api/storageservices/put-block-list)
- [Put Block](/rest/api/storageservices/put-block)
- [Colocar bloco da URL](/rest/api/storageservices/put-block-from-url)
- [Colocar Página](/rest/api/storageservices/put-page)
- [Colocar Página da URL](/rest/api/storageservices/put-page-from-url)
- [Acrescentar Bloco](/rest/api/storageservices/append-block)
- [Set Blob Properties](/rest/api/storageservices/set-blob-properties)
- [Set Blob Metadata](/rest/api/storageservices/set-blob-metadata)
- [Get Blob](/rest/api/storageservices/get-blob)
- [Obter propriedades do blob](/rest/api/storageservices/get-blob-properties)
- [Get Blob Metadata](/rest/api/storageservices/get-blob-metadata)
- [Blob de instantâneo](/rest/api/storageservices/snapshot-blob)

## <a name="rotate-customer-provided-keys"></a>Girar chaves fornecidas pelo cliente

Para girar uma chave de criptografia que foi usada para criptografar um blob, baixe o blob e carregue-o novamente com a nova chave de criptografia.

> [!IMPORTANT]
> O portal do Azure não pode ser usado para ler ou gravar em um contêiner ou BLOB que é criptografado com uma chave fornecida na solicitação.
>
> Certifique-se de proteger a chave de criptografia que você fornece em uma solicitação de armazenamento de BLOBs em um repositório de chaves seguro, como Azure Key Vault. Se você tentar uma operação de gravação em um contêiner ou BLOB sem a chave de criptografia, a operação falhará e você perderá o acesso ao objeto.

## <a name="next-steps"></a>Próximas etapas

- [Especifique uma chave fornecida pelo cliente em uma solicitação para o armazenamento de BLOBs com o .NET](storage-blob-customer-provided-key.md)
- [Criptografia do Armazenamento do Azure para dados em repouso](../common/storage-service-encryption.md)
