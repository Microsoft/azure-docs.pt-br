---
title: Conjuntos de declarações do Atestado do Azure
description: Os conjuntos de declarações do Atestado do Azure.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: eb08bb262806cb662822a75898196546a5c1058e
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/25/2021
ms.locfileid: "98762546"
---
# <a name="claim-sets"></a>Conjuntos de declarações

As declarações geradas no processo de atestado de enclaves por meio do Atestado do Microsoft Azure podem ser divididas nas categorias abaixo:

- **Declarações de entrada**: declarações geradas pelo Atestado do Microsoft Azure após a análise da evidência do atestado e podem ser usadas por autores de política para definir regras de autorização em uma política personalizada

- **Declarações de saída**: declarações geradas pelo Atestado do Azure e contêm todas as declarações que terminam no token de atestado

- **Declarações de propriedade**: declarações criadas como uma saída pelo Atestado do Azure. Ela contém todas as declarações que representam as propriedades do token de atestado, como a codificação do relatório, a duração da validade do relatório etc.

### <a name="common-incoming-claims-across-all-attestation-types"></a>Declarações de entrada comuns em todos os tipos de atestado

As declarações abaixo são geradas pelo Atestado do Azure e podem ser usadas para definir regras de autorização em uma política personalizada:
- **x-ms-ver**: versão do esquema JWT (deve ser "1.0")
- **x-ms-attestation-type**: valor da cadeia de caracteres que representa o tipo de atestado 
- **x-ms-policy-hash**: hash da política de avaliação do Atestado do Azure computada como BASE64URL(SHA256(UTF8(BASE64URL(UTF8(texto da política)))))
- **x-ms-policy-signer**: O objeto JSON com um membro "jwk" representando a chave que um cliente usou para assinar a política quando ele carrega uma política assinada

As declarações abaixo são consideradas preteridas, mas ainda têm suporte completo. É recomendável usar os nomes de declarações não preteridas.

Declaração preterida | Declaração recomendada 
--- | --- 
ver | x-ms-ver
tee | x-ms-attestation-type
maa-policyHash | x-ms-policy-hash
policy_hash | x-ms-policy-hash
policy_signer | x-ms-policy-signer

### <a name="common-outgoing-claims-across-all-attestation-types"></a>Declarações de saída comuns em todos os tipos de atestado

Veja abaixo as declarações definidas pelo [IETF JWT](https://tools.ietf.org/html/rfc7519) e usadas pelo Atestado do Azure no objeto de resposta:

- **Declaração "jti" (ID JWT)**
- **Declaração "iss" (Emissor)**
- **Declaração "iat" (Emitida às)**
- **Declaração "exp" (Hora de expiração)**
- **Declaração "nbf" (Não Anterior)**

Veja abaixo as declarações definidas pelo [IETF EAT](https://tools.ietf.org/html/draft-ietf-rats-eat-03#page-9) e usadas pelo Atestado do Azure no objeto de resposta:
- **"Declaração nonce" (nonce)**

As declarações abaixo são geradas por padrão com base nas declarações de entrada
- **x-ms-ver**: versão do esquema JWT (deve ser "1.0")
- **x-ms-attestation-type**: valor da cadeia de caracteres que representa o tipo de atestado 
- **x-ms-policy-hash**: valor de cadeia de caracteres que contém o hash SHA256 do texto da política computado por BASE64URL(SHA256(UTF8(BASE64URL(UTF8(policy text)))))
- **x-ms-policy-signer**: contém um JWK com a chave pública ou a cadeia de certificados presente no cabeçalho da política assinada. x-ms-policy-signer será adicionado somente se a política for assinada

## <a name="claims-specific-to-sgx-enclaves"></a>Declarações específicas para enclaves SGX

### <a name="incoming-claims-specific-to-sgx-attestation"></a>Declarações de entrada específicas para o atestado SGX

As declarações abaixo são geradas pelo serviço para o atestado SGX e podem ser usadas para definir regras de autorização em uma política personalizada:
- **x-ms-sgx-is-debuggable**: um booliano que indica se o enclave tem ou não a depuração habilitada
- **x-ms-sgx-product-id**
- **x-ms-sgx-mrsigner**: valor codificado em hexa do campo “mrsigner” da cotação
- **x-ms-sgx-mrenclave**: valor codificado em hexa do campo “mrenclave” da cotação
- **x-ms-sgx-svn**: número de versão de segurança codificado na cotação 

### <a name="outgoing-claims-specific-to-sgx-attestation"></a>Declarações de saída específicas para o atestado SGX

As declarações abaixo são geradas pelo serviço e incluídas no objeto de resposta para o atestado SGX:
- **x-ms-sgx-is-debuggable**: um booliano que indica se o enclave tem ou não a depuração habilitada
- **x-ms-sgx-product-id**
- **x-ms-sgx-mrsigner**: valor codificado em hexa do campo “mrsigner” da cotação
- **x-ms-sgx-mrenclave**: valor codificado em hexa do campo “mrenclave” da cotação
- **x-ms-sgx-svn**: número de versão de segurança codificado na cotação 
- **x-ms-sgx-ehd**: dados contidos no enclave formatados como BASE64URL (dados contidos no enclave)
- **x-ms-sgx-collateral**: objeto JSON que descreve o manual e acessórios usados para executar o atestado. O valor para a declaração x-ms-sgx-collateral é um objeto JSON aninhado com os seguintes pares chave/valor:
    - **qeidcertshash**: valor SHA256 da Identidade QE emissora de certificados
    - **qeidcrlhash**: valor SHA256 da Identidade QE emissora da lista CRL de certificados
    - **qeidhash**: valor SHA256 do manual e acessórios da Identidade QE
    - **quotehash**: valor SHA256 da cotação avaliada
    - **tcbinfocertshash**: valor SHA256 das Informações TCB emissoras de certificados
    - **tcbinfocrlhash**: valor SHA256 das Informações TCB emissoras da lista CRL de certificados
    - **tcbinfohash**: objeto JSON que descreve o manual e acessórios usados para executar o atestado

As declarações abaixo são consideradas preteridas, mas têm suporte completo e continuarão sendo incluídas no futuro. É recomendável usar os nomes de declarações não preteridas.

Declaração preterida | Declaração recomendada
--- | --- 
$is-debuggable | x-ms-sgx-is-debuggable
$sgx-mrsigner | x-ms-sgx-mrsigner
$sgx-mrenclave | x-ms-sgx-mrenclave
$product-id | x-ms-sgx-product-id
$svn | x-ms-sgx-svn
$tee | x-ms-attestation-type
maa-ehd | x-ms-sgx-ehd
aas-ehd | x-ms-sgx-ehd
maa-attestationcollateral | x-ms-sgx-collateral

## <a name="claims-specific-to-trusted-platform-module-tpm-vbs-attestation"></a>Declarações específicas do atestado TPM (Trusted Platform Module)/VBS

### <a name="incoming-claims-for-tpm-attestation"></a>Declarações de entrada para atestado TPM

Declarações emitidas pelo Atestado do Azure para o atestado TPM. A disponibilidade das declarações depende das evidências fornecidas para atestado.

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

### <a name="incoming-claims-for-vbs-attestation"></a>Declarações de entrada para atestado VBS

As declarações emitidas pelo Atestado do Azure para o atestado VBS são feitas além das declarações disponibilizadas para o atestado de TPM. A disponibilidade das declarações depende das evidências fornecidas para atestado.

- **enclaveAuthorId**:  valor de cadeia de caracteres que contém o valor codificado em Base64Url da ID de autor do enclave, o identificador de autor do módulo primário do enclave
- **enclaveImageId**:  valor de cadeia de caracteres que contém o valor codificado em Base64Url da ID da imagem do enclave, o identificador de imagem do módulo primário do enclave
- **enclaveOwnerId**:  valor de cadeia de caracteres que contém o valor codificado em Base64Url da ID de proprietário do enclave, o identificador do proprietário do enclave
- **enclaveFamilyId**:  valor de cadeia de caracteres que contém o valor codificado em Base64Url da ID de família do enclave. O identificador de família do módulo primário do enclave
- **enclaveSvn**:  valor inteiro que contém o número de versão de segurança do módulo primário do enclave
- **enclavePlatformSvn**:  valor inteiro que contém o número de versão de segurança da plataforma que hospeda o enclave
- **enclaveFlags**:  a declaração enclaveFlags é um valor inteiro que contém sinalizadores que descrevem a política de runtime do enclave

### <a name="outgoing-claims-specific-to-tpm-and-vbs-attestation"></a>Declarações de saída específicas para o atestado TPM e VBS

- **cnf (Confirmação)** : a declaração "cnf" é usada para identificar a chave de prova de posse. A declaração de confirmação, conforme definido no RFC 7800, contém a parte pública da chave do enclave atestada representada como um objeto JWK (Chave Web JSON) (RFC 7517)
- **rp_data (dados de terceira parte confiável)** : dados de terceira parte confiável, se houver, especificados na solicitação, usados pela terceira parte confiável como um nonce para garantir a atualização do relatório. rp_data só será adicionado se houver rp_data

### <a name="property-claims"></a>Declarações de propriedade

- **report_validity_in_minutes**: uma declaração de inteiro que representa o tempo durante o qual o token é válido.
  - **Valor padrão (hora)** : um dia em minutos.
  - **Valor máximo (hora)** : um ano em minutos.
- **omit_x5c**: uma declaração booliana que indica se o Atestado do Azure deve omitir o certificado usado para fornecer a prova de autenticidade do serviço. Se essa declaração for verdadeira, x5t será adicionado ao token de atestado. Se ela for falsa (padrão), x5c será adicionado ao token de atestado.

## <a name="next-steps"></a>Próximas etapas
- [Como criar e assinar uma política de atestado](author-sign-policy.md)
- [Configurar o Atestado do Azure usando o PowerShell](quickstart-powershell.md)
