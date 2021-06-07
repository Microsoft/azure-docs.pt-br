---
title: Conjuntos de declarações do Atestado do Azure
description: Os conjuntos de declarações do Atestado do Azure.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: 0d6d5a08ea85ebb666acc0336f1e1d7ec5e097da
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105044661"
---
# <a name="claim-sets"></a>Conjuntos de declarações

As declarações geradas no processo de atestado de enclaves por meio do Atestado do Microsoft Azure podem ser divididas nas categorias abaixo:

- **Declarações de entrada**: as declarações geradas pelo Atestado do Microsoft Azure após a análise da evidência do atestado e podem ser usadas por autores de política para definir regras de autorização em uma política personalizada

- **Declarações de saída**: as declarações geradas pelo Atestado do Azure e incluídas no token de atestado

- **Declarações de propriedade**: as declarações criadas como uma saída pelo Atestado do Azure. Ela contém todas as declarações que representam as propriedades do token de atestado, como a codificação do relatório, a duração da validade do relatório etc.

## <a name="incoming-claims"></a>Declarações de entrada 

### <a name="sgx-attestation"></a>Atestado do SGX

Declarações a serem usadas por autores de política para definir regras de autorização em uma política de atestado SGX:

- **x-ms-sgx-is-debuggable**: um booliano que indica se o enclave tem ou não a depuração habilitada
- **x-ms-sgx-product-id**: valor da ID do produto do enclave SGX 
- **x-ms-sgx-mrsigner**: valor codificado em hexa do campo “mrsigner” da cotação
- **x-ms-sgx-mrenclave**: valor codificado em hexa do campo “mrenclave” da cotação
- **x-ms-sgx-svn**: número de versão de segurança codificado na cotação 

As declarações abaixo são consideradas preteridas, mas têm suporte completo e continuarão sendo incluídas no futuro. É recomendável usar os nomes de declarações não preteridas.

Declaração preterida | Declaração recomendada
--- | --- 
$is-debuggable | x-ms-sgx-is-debuggable
$product-id | x-ms-sgx-product-id
$sgx-mrsigner | x-ms-sgx-mrsigner
$sgx-mrenclave | x-ms-sgx-mrenclave
$svn | x-ms-sgx-svn

### <a name="tpm-attestation"></a>Atestado de TPM

As declarações de entrada podem ser usadas pelos autores de política para definir as regras de autorização em uma política de atestado TPM:

- **aikValidated**: valor booliano que contém informações que indicam se o certificado AIK (chave de identidade de atestado) foi validado ou não
- **aikPubHash**:  cadeia de caracteres que contém o base64(SHA256(chave pública AIK em formato DER))
- **tpmVersion**:   valor inteiro que contém a versão principal do TPM (Trusted Platform Module)
- **secureBootEnabled**: valor booliano que indica se a inicialização segura está habilitada
- **iommuEnabled**:  valor booliano que indica se a Iommu (unidade de gerenciamento de memória de entrada-saída) está habilitada
- **bootDebuggingDisabled**: valor booliano que indica se a depuração de inicialização está desabilitada
- **notSafeMode**:  valor booliano que indica se o Windows não está sendo executado no modo de segurança
- **notWinPE**:  valor booliano que indica se o Windows não está sendo executado no modo WinPE
- **vbsEnabled**:  valor booliano que indica se a VBS está habilitada
- **vbsReportPresent**:  valor booliano que indica se o relatório de enclave da VBS está disponível

### <a name="vbs-attestation"></a>Atestado da VBS

Além das declarações de política de atestado TPM, as declarações abaixo podem ser usadas por autores de política para definir regras de autorização em uma política de atestado VBS.

- **enclaveAuthorId**:  valor de cadeia de caracteres que contém o valor codificado em Base64Url da ID de autor do enclave, o identificador de autor do módulo primário do enclave
- **enclaveImageId**:  valor de cadeia de caracteres que contém o valor codificado em Base64Url da ID da imagem do enclave, o identificador de imagem do módulo primário do enclave
- **enclaveOwnerId**:  valor de cadeia de caracteres que contém o valor codificado em Base64Url da ID de proprietário do enclave, o identificador do proprietário do enclave
- **enclaveFamilyId**:  valor de cadeia de caracteres que contém o valor codificado em Base64Url da ID de família do enclave. O identificador de família do módulo primário do enclave
- **enclaveSvn**:  valor inteiro que contém o número de versão de segurança do módulo primário do enclave
- **enclavePlatformSvn**:  valor inteiro que contém o número de versão de segurança da plataforma que hospeda o enclave
- **enclaveFlags**:  a declaração enclaveFlags é um valor inteiro que contém sinalizadores que descrevem a política de runtime do enclave

## <a name="outgoing-claims"></a>Declarações de saída 

### <a name="common-for-all-attestation-types"></a>Comum para todos os tipos de atestado

O Atestado do Azure inclui as declarações abaixo no token de atestado para todos os tipos de atestado. 

- **x-ms-ver**: versão do esquema JWT (deve ser "1.0")
- **x-ms-attestation-type**: valor da cadeia de caracteres que representa o tipo de atestado 
- **x-ms-policy-hash**: hash da política de avaliação do Atestado do Azure computada como BASE64URL(SHA256(UTF8(BASE64URL(UTF8(texto da política)))))
- **x-ms-policy-signer**: o objeto JSON com um membro "jwk" representando a chave que um cliente usou para assinar a política. Isso é aplicável quando o cliente carrega uma política assinada

Os nomes de declaração abaixo são usados na [especificação do JWT da IETF](https://tools.ietf.org/html/rfc7519)

- **Declaração "jti" (ID do JWT)** : identificador exclusivo do JWT
- **Declaração "iss" (Emissor)** : a entidade que emitiu o JWT 
- **Declaração "iat" (Emitido Em)** : a hora em que o JWT foi emitido 
- **Declaração "exp" (Hora de Expiração)** : hora de expiração após a qual o JWT não deve ser aceito para processamento
- **Declaração "nbf" (Não Antes)** : hora antes da qual o JWT não deve ser aceito para processamento 

Os nomes de declaração abaixo são usados na [especificação de rascunho do EAT da IETF](https://tools.ietf.org/html/draft-ietf-rats-eat-03#page-9)

- **"Declaração de nonce" (nonce)** : uma cópia direta não transformada de um valor de nonce opcional fornecido por um cliente 

As declarações abaixo são consideradas preteridas, mas têm suporte completo e continuarão sendo incluídas no futuro. É recomendável usar os nomes de declarações não preteridas.

Declaração preterida | Declaração recomendada
--- | --- 
ver | x-ms-ver
tee | x-ms-attestation-type
policy_hash | x-ms-policy-hash
maa-policyHash | x-ms-policy-hash
policy_signer  | x-ms-policy-signer

### <a name="sgx-attestation"></a>Atestado do SGX 

As declarações abaixo são geradas e incluídas no token de atestado pelo serviço para o atestado SGX.

- **x-ms-sgx-is-debuggable**: um booliano que indica se o enclave tem ou não a depuração habilitada
- **x-ms-sgx-product-id**: valor da ID do produto do enclave SGX 
- **x-ms-sgx-mrsigner**: valor codificado em hexa do campo “mrsigner” da cotação
- **x-ms-sgx-mrenclave**: valor codificado em hexa do campo “mrenclave” da cotação
- **x-ms-sgx-svn**: número de versão de segurança codificado na cotação 
- **x-ms-sgx-ehd**: dados contidos no enclave formatados como BASE64URL (dados contidos no enclave)
- **x-ms-sgx-collateral**: objeto JSON que descreve o manual e acessórios usados para executar o atestado. O valor para a declaração x-ms-sgx-collateral é um objeto JSON aninhado com os seguintes pares chave/valor:
    - **qeidcertshash**: valor SHA256 de certificados emissores de Identidade de QE (Enclave de Delimitação)
    - **qeidcrlhash**: valor SHA256 da Identidade QE emissora da lista CRL de certificados
    - **qeidhash**: valor SHA256 do manual e acessórios da Identidade QE
    - **quotehash**: valor SHA256 da cotação avaliada
    - **tcbinfocertshash**: valor SHA256 das Informações TCB emissoras de certificados
    - **tcbinfocrlhash**: valor SHA256 das Informações TCB emissoras da lista CRL de certificados
    - **tcbinfohash**: valor SHA256 do manual e dos acessórios de Informações de TCB

As declarações abaixo são consideradas preteridas, mas têm suporte completo e continuarão sendo incluídas no futuro. É recomendável usar os nomes de declarações não preteridas.

Declaração preterida | Declaração recomendada
--- | --- 
$is-debuggable | x-ms-sgx-is-debuggable
$product-id | x-ms-sgx-product-id
$sgx-mrsigner | x-ms-sgx-mrsigner
$sgx-mrenclave | x-ms-sgx-mrenclave
$svn | x-ms-sgx-svn
$maa-ehd | x-ms-sgx-ehd
$aas-ehd | x-ms-sgx-ehd
$maa-attestationcollateral | x-ms-sgx-collateral

### <a name="tpm-and-vbs-attestation"></a>Atestado TPM e VBS

- **cnf (Confirmação)** : a declaração "cnf" é usada para identificar a chave de prova de posse. A declaração de confirmação, conforme definido no RFC 7800, contém a parte pública da chave do enclave atestada representada como um objeto JWK (Chave Web JSON) (RFC 7517)
- **rp_data (dados de terceira parte confiável)** : dados de terceira parte confiável, se houver, especificados na solicitação, usados pela terceira parte confiável como um nonce para garantir a atualização do relatório. rp_data só será adicionado se houver rp_data

## <a name="property-claims"></a>Declarações de propriedade

### <a name="tpm-and-vbs-attestation"></a>Atestado TPM e VBS

- **report_validity_in_minutes**: uma declaração de inteiro que indica o tempo durante o qual o token é válido.
  - **Valor padrão (hora)** : um dia em minutos.
  - **Valor máximo (hora)** : um ano em minutos.
- **omit_x5c**: uma declaração booliana que indica se o Atestado do Azure deve omitir o certificado usado para fornecer a prova de autenticidade do serviço. Se essa declaração for verdadeira, x5t será adicionado ao token de atestado. Se ela for falsa (padrão), x5c será adicionado ao token de atestado.

## <a name="next-steps"></a>Próximas etapas
- [Como criar e assinar uma política de atestado](author-sign-policy.md)
- [Configurar o Atestado do Azure usando o PowerShell](quickstart-powershell.md)
