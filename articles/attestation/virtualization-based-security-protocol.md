---
title: Protocolo VBS (segurança baseada em virtualização) para o atestado do Azure
description: Protocolo de atestado VBS
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: reference
ms.date: 07/20/2020
ms.author: mbaldwin
ms.openlocfilehash: 38012c5b4bb9338c1200d9583256193ee8402c98
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99507872"
---
# <a name="trusted-platform-module-tpm-and-virtualization-based-securityvbs-enclave-attestation-protocol"></a>Trusted Platform Module (TPM) e o protocolo de atestado enclave de segurança baseada em virtualização (VBS) 

Microsoft Azure atestado para fornecer uma forte garantia de segurança depende de verificar se uma cadeia de confiança é mantida de uma raiz de confiança (TPM) até a inicialização do hipervisor e do kernel seguro. Para obter esse atestado do Azure, é necessário atestar o estado de inicialização da máquina antes de poder estabelecer confiança no Secure enclave. O sistema operacional, o hipervisor e os binários de kernel seguro devem ser assinados pelas autoridades oficiais da Microsoft corretas e configurados de forma segura. Depois de associarmos a confiança entre o Trusted Platform Module (TPM) e a integridade do hipervisor, podemos confiar na enclave (segurança baseada em virtualização) IDKs fornecida no log de inicialização medido, com isso, podemos validar que um par de chaves foi gerado pelo enclave e menta um relatório de atestado que associa a confiança nessa chave e contém outras declarações, como as propriedades de nível de segurança e de atestado de inicialização. 

VBS enclaves requer um TPM para fornecer a medida para validar a base de segurança. VBS enclaves são atestados pelo ponto de extremidade do TPM com uma adição ao objeto de solicitação no protocolo. 

## <a name="protocol-messages"></a>Mensagens de protocolo

### <a name="init-message"></a>Mensagem de inicialização

#### <a name="direction"></a>Direção

Cliente-> atestado do Azure

#### <a name="payload"></a>Carga útil

```
{ 
  "type": "aikcert" 
} 
```

"Type" (cadeia de caracteres ASCII): representa o tipo de atestado solicitado. Atualmente, há suporte apenas para "aikcert".

### <a name="challenge-message"></a>Mensagem de desafio

#### <a name="direction"></a>Direção

Atestado do Azure-cliente >

#### <a name="payload"></a>Carga útil

```
{ 
  "challenge": "<BASE64URL(CHALLENGE)>", 
  "service_context": "<BASE64URL(SERVICECONTEXT)>" 
} 
```

**desafio** (BASE64URL (octetos)): valor aleatório emitido pelo serviço.

**service_context** (BASE64URL (octetos)): contexto opaco criado pelo serviço.


### <a name="request-message"></a>Mensagem de solicitação

#### <a name="direction"></a>Direção

Cliente-> atestado do Azure 

#### <a name="payload"></a>Carga útil

```
{
  "request": "<JWS>"
}
```

**solicitação** (JWS): a solicitação consiste em uma estrutura de assinatura da Web JSON (JWS). O cabeçalho protegido JWS e a carga JWS são mostrados abaixo. Como em qualquer estrutura JWS, o valor final consiste em:

BASE64URL (UTF8 (cabeçalho protegido JWS)) | | '.' ||

BASE64URL (carga de JWS) | | '.' ||

BASE64URL (assinatura JWS)

##### <a name="jws-protected-header"></a>Cabeçalho protegido do JWS

```
{
  "alg": "PS256",
  "typ": "attReq"
  // no "kid" parameter as the key specified by attest_key MUST sign this JWS to prove possession.
}
```

##### <a name="jws-payload"></a>Carga JWS

A carga JWS pode ser do tipo Basic ou VBS. Básico é usado quando a evidência de atestado não inclui dados VBS. 

Exemplo de TPM somente: 

``` 
{ 
  "att_type": "basic", 
  "att_data": { 
    "rp_id": "<URL>", 
    "rp_data": "<BASE64URL(RPCUSTOMDATA)>", 
    "challenge": "<BASE64URL(CHALLENGE)>", 

    "tpm_att_data": { 
      "srtm_boot_log": "<BASE64URL(SRTMBOOTLOG)>", 
      "srtm_resume_log": "<BASE64URL(SRTMRESUMELOG)>", 
      "drtm_boot_log": "<BASE64URL(DRTMBOOTLOG)>", 
      "drtm_resume_log": "<BASE64URL(DRTMRESUMELOG)>", 
      "aik_cert": "<BASE64URL(AIKCERTIFICATE)>", 

      // aik_pub is represented as a JSON Web Key (JWK) object (RFC 7517). 

      "aik_pub": { 
        "kty": "RSA", 
        "n": "<Base64urlUInt(MODULUS)>", 
        "e": "<Base64urlUInt(EXPONENT)>" 
      }, 
      "current_claim": "<BASE64URL(CURRENTCLAIM)>", 
      "boot_claim": "<BASE64URL(BOOTCLAIM)>" 
    }, 

    // attest_key is represented as a JSON Web Key (JWK) object (RFC 7517). 

    "attest_key": { 
      "kty": "RSA", 
      "n": "<Base64urlUInt(MODULUS)>", 
      "e": "<Base64urlUInt(EXPONENT)>" 
    }, 
    "custom_claims": [ 
      { 
        "name": "<name>", 
        "value": "<value>", 
        "value_type": "<value_type>" 
      }, 
      { 
        "name": "<name>", 
        "value": "<value>", 
        "value_type": "<value_type>" 
      } 
    ], 
    "service_context": "<BASE64URL(SERVICECONTEXT)>" 
  } 
} 
```

Exemplo de enclave TPM + VBS: 

``` 
{ 
  "att_type": "vbs", 
  "att_data": { 
    "report_signed": { 
      "rp_id": "<URL>", 
      "rp_data": "<BASE64URL(RPCUSTOMDATA)>", 
      "challenge": "<BASE64URL(CHALLENGE)>", 
      "tpm_att_data": { 
        "srtm_boot_log": "<BASE64URL(SRTMBOOTLOG)>", 
        "srtm_resume_log": "<BASE64URL(SRTMRESUMELOG)>", 
        "drtm_boot_log": "<BASE64URL(DRTMBOOTLOG)>", 
        "drtm_resume_log": "<BASE64URL(DRTMRESUMELOG)>", 
        "aik_cert": "<BASE64URL(AIKCERTIFICATE)>", 

        // aik_pub is represented as a JSON Web Key (JWK) object (RFC 7517). 

        "aik_pub": { 
          "kty": "RSA", 
          "n": "<Base64urlUInt(MODULUS)>", 
          "e": "<Base64urlUInt(EXPONENT)>" 
        }, 
        "current_claim": "<BASE64URL(CURRENTCLAIM)>", 
        "boot_claim": "<BASE64URL(BOOTCLAIM)>" 
      }, 

      // attest_key is represented as a JSON Web Key (JWK) object (RFC 7517). 

      "attest_key": { 
        "kty": "RSA", 
        "n": "<Base64urlUInt(MODULUS)>", 
        "e": "<Base64urlUInt(EXPONENT)>" 
      }, 
      "custom_claims": [ 
        { 
          "name": "<name>", 
          "value": "<value>", 
          "value_type": "<value_type>" 
        }, 
        { 
          "name": "<name>", 
          "value": "<value>", 
          "value_type": "<value_type>" 
        } 
      ], 
      "service_context": "<BASE64URL(SERVICECONTEXT)>" 
    }, 
    "vsm_report": "<BASE64URL(REPORT)>" 
  } 
} 
``` 

**rp_id** (StringOrURI): identificador de terceira parte confiável. Usado pelo serviço na computação da declaração de ID do computador

**rp_data** (BASE64URL (octetos)): dados opacos passados pela terceira parte confiável. Normalmente, isso é usado pela terceira parte confiável como um nonce para garantir a atualização do relatório

**desafio** (BASE64URL (octetos)): valor aleatório emitido pelo serviço

**tpm_att_data**: dados de atestado relacionados ao TPM

- **srtm_boot_log (BASE64URL (octetos))**: SRTM logs de inicialização como recuperados por função Tbsi_Get_TCG_Log_Ex com o tipo de log = TBS_TCGLOG_SRTM_BOOT

- **srtm_resume_log (BASE64URL (octetos))**: SRTM retoma o log como recuperado pela função Tbsi_Get_TCG_Log_Ex com o tipo de log = TBS_TCGLOG_SRTM_RESUME

- **drtm_boot_log (BASE64URL (octetos))**: DRTM logs de inicialização como recuperados por função Tbsi_Get_TCG_Log_Ex com o tipo de log = TBS_TCGLOG_DRTM_BOOT

- **drtm_resume_log (BASE64URL (octetos))**: DRTM retoma o log como recuperado pela função Tbsi_Get_TCG_Log_Ex com o tipo de log = TBS_TCGLOG_DRTM_RESUME

- **aik_cert (BASE64URL (octetos))**: o certificado X. 509 para o AIK, conforme retornado pela função NCryptGetProperty com a propriedade = NCRYPT_CERTIFICATE_PROPERTY

- **aik_pub**: a parte pública do AIK representada como um objeto de chave da Web JSON (JWK) (RFC 7517)

- **current_claim (BASE64URL (octetos))**: a declaração de atestado para o estado atual de PCR, conforme retornado pela função NCryptCreateClaim com dwClaimType = NCRYPT_CLAIM_PLATFORM e parâmetro NCRYPTBUFFER_TPM_PLATFORM_CLAIM_PCR_MASK definido para incluir todos os PCRs. O desafio enviado pelo serviço também deve ser usado na computação desta declaração

- **boot_claim (BASE64URL (octetos))**: a declaração de atestado para o estado de PCR na inicialização, conforme retornado pela função NCryptCreateClaim com dwClaimType = NCRYPT_CLAIM_PLATFORM e parâmetro NCRYPTBUFFER_TPM_PLATFORM_CLAIM_PCR_MASK definido para incluir todos os PCRs

**vsm_report**   (BASE64URL (OCTETos)): o relatório de atestado enclave do VBS, como retornado pela função EnclaveGetAttestationReport. O parâmetro EnclaveData deve ser o hash SHA-512 do valor de report_signed (incluindo as chaves de abertura e fechamento). A entrada da função de hash é UTF8 (report_signed)

**attest_key**: a parte pública da chave enclave representada como um objeto de chave da Web JSON (JWK) (RFC 7517)

**custom_claims**: matriz de declarações enclave personalizadas enviadas ao serviço que podem ser avaliadas pela política. A declaração

- **nome (cadeia de caracteres)**: nome da declaração. Esse nome será anexado a uma URL determinada pelo serviço de atestado (para evitar conflitos) e a cadeia de caracteres concatenada se tornará o tipo da declaração que pode ser usada na política

- **valor (cadeia de caracteres)**: valor da declaração

- **value_type (cadeia de caracteres)**: tipo de dados do valor da declaração

**service_context** (BASE64URL (octetos)): contexto opaco criado pelo serviço.

### <a name="report-message"></a>Mensagem de relatório

#### <a name="direction"></a>Direção

Atestado do Azure-cliente >

#### <a name="payload"></a>Carga útil

```
{
  "report": "<JWT>"
}
```

**relatório** (JWT): o relatório de atestado no formato JWT (token Web JSON) (RFC 7519).

## <a name="next-steps"></a>Próximas etapas

- [Fluxo de trabalho do atestado do Azure](workflow.md)
