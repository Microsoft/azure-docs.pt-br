---
title: Solução de problemas de certificados com o Azure Stack Edge pro com GPU | Microsoft Docs
description: Descreve a solução de problemas de erros de certificado com Azure Stack dispositivo de GPU pro de borda.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 67dd2b35229c15ae4df5ec8acb357aa35621d67c
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2021
ms.locfileid: "102436627"
---
# <a name="troubleshooting-certificate-errors"></a>Solucionando problemas de erros de certificado

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

O artigo fornece a solução de problemas de erros de certificado comuns ao instalar certificados em seu dispositivo Azure Stack Edge pro.

## <a name="common-certificate-errors"></a>Erros comuns de certificado

A tabela a seguir mostra os erros de certificado comuns e informações detalhadas sobre esses erros e as possíveis soluções:

> [!NOTE]
> Ocorrências de &#8220;{0} , {1} ,..., {n} &#8221; indicam parâmetros posicionais. Os parâmetros posicionais terão valores, dependendo dos certificados que você estiver usando.

| Código do Erro | Descrição |
|---|---|
| CertificateManagement_UntrustedCertificate | O certificado com o nome da entidade {0} tem uma cadeia de certificados quebrada. Carregue o certificado da cadeia de assinatura antes de carregar esse certificado.|
| CertificateManagement_DeviceNotRegistered| Seu dispositivo não está ativado. Você pode carregar um certificado de suporte somente após a ativação.|
| CertificateManagement_ExpiredCertificate | O certificado com o tipo {0} expirou ou expira em breve. Verifique a expiração do certificado e, se necessário, traga um novo certificado.|
| CertificateManagement_FormatMismatch | Não há suporte para o formato de certificado. Verifique o formato do certificado e, se necessário, traga um novo certificado.  Esperado {0} , encontrado {1} . |
| CertificateManagement_GenericError | Não foi possível executar a operação de gerenciamento de certificados. Repita essa operação em alguns minutos. Se o problema persistir, contate o Suporte da Microsoft. |
| CertificateManagement_HttpsBindingFailure | O certificado com o nome da entidade {0} não pôde criar um canal https seguro. Verifique o certificado que você carregou e, se necessário, traga um novo certificado. Esse erro ocorre com o certificado do nó do dispositivo.|
| CertificateManagement_IncorrectKeyCertSignKeyUsage | O certificado com o nome da entidade {0} não deve ter assinatura de certificado de uso de chave. Verifique o uso da chave do certificado e, se necessário, traga um novo certificado. Esse erro ocorre com o certificado de cadeia de assinatura. |
| CertificateManagement_IncorrectKeyNumber | O certificado com o nome da entidade {0} tem um número de chave incorreto {1} . Verifique o número de chave do certificado e, se necessário, traga um novo certificado.|
| CertificateManagement_InvalidP7b | O arquivo de certificado carregado não é válido.  Verifique o formato do certificado e, se necessário, traga um novo certificado.|
| CertificateManagement_KeyAlgorithmNotRSA | Esse certificado não usa o algoritmo de chave RSA. Somente os certificados RSA têm suporte. |
| CertificateManagement_KeySizeNotSufficient | O certificado com o nome da entidade não {0} tem tamanho de chave suficiente {1} . O tamanho mínimo da chave é 4096.|
| CertificateManagement_MissingClientOid | O certificado com o nome da entidade {0} não tem OID de autenticação de cliente. Verifique as propriedades do certificado e, se necessário, traga um novo certificado.|
| CertificateManagement_MissingDigitalSignatureKeyUsage | O certificado com {0} o nome da entidade não tem uma assinatura digital no uso da chave. Verifique as propriedades do certificado e, se necessário, traga um novo certificado. |
| CertificateManagement_MissingKeyCertSignKeyUsage | O certificado com {0} o nome da entidade não tem uma assinatura de certificado no uso da chave. Verifique as propriedades do certificado e, se necessário, traga um novo certificado.|
| CertificateManagement_MissingKeyEnciphermentKeyUsage | O certificado com {0} o nome da entidade não tem codificação de chave no uso da chave. Verifique as propriedades do certificado e, se necessário, traga um novo certificado. |
| CertificateManagement_MissingServerOid | O certificado com o nome da entidade {0} não tem OID de autenticação de servidor. Verifique as propriedades do certificado e, se necessário, traga um novo certificado.|
| CertificateManagement_NameMismatch | Incompatibilidade de tipo de certificado. Escopo esperado: {0} , encontrado {1} . Carregue o certificado apropriado.|
| CertificateManagement_NoPrivateKeyPresent | O certificado com o nome da entidade {0} não tem uma chave privada presente. Carregue um certificado. pfx com uma chave privada.|
| CertificateManagement_NoRSACryptoPrivateKey | A chave privada do certificado com o nome da entidade {0} não está acessível. Verifique se você está usando um certificado com suporte. Somente o provedor de criptografia RSA/Schannel da Microsoft tem suporte. |
| CertificateManagement_NotSelfSignedCertificate | O certificado com o nome da entidade {0} não é assinado automaticamente. Os certificados raiz devem ser autoassinados |
| CertificateManagement_NotSupportedOnVirtualAppliance | Não há suporte para esta operação no dispositivo virtual. Esse erro indica que a assinatura só ocorrerá com Gateway do Data Box em execução no dispositivo de nuvem tático. Esse erro ocorre durante o gerenciamento do dispositivo por meio do Windows PowerShell.|
| CertificateManagement_SelfSignedCertificate | O certificado com o nome da entidade {0} é auto-assinado. Carregue um certificado que esteja assinado corretamente.|
| CertificateManagement_SignatureAlgorithmSha1 | O certificado com o nome da entidade {0} tem um algoritmo de assinatura sem suporte. SHA1-RSA não é suportado. |
| CertificateManagement_SubjectNamesInvalid | O certificado com {0} o nome da entidade não tem o nome da entidade ou nomes alternativos da entidade corretos para o {1} certificado. Verifique o certificado que você carregou e, se necessário, traga um novo certificado. Você também deve verificar o nome DNS para coincidir com os nomes dos SANS.|
| CertificateManagement_UnreadableCertificate | Não foi possível ler o certificado com o tipo {0} . Esse erro ocorre quando o certificado está ilegível ou corrompido. Traga um novo certificado.|
| CertificateSubjectNotFound | Não foi possível encontrar o certificado com o nome da entidade {0} . Traga um novo certificado.|
| CertificateRotationGenericFailure | Falha na rotação de um ou mais certificados. Tente novamente em alguns minutos. Se o problema persistir, contate o Suporte da Microsoft.|
| CertificateImportFailure | O certificado com impressão digital {0} não foi importado no nó {1} . Se o problema persistir, contate o Suporte da Microsoft. |
| CertificateApplyFailure | O certificado com impressão digital {0} não foi aplicado ao nó {1} . Se o problema persistir, contate o Suporte da Microsoft.|
| NodeNotReachable | Não foi possível validar o certificado no {0} . Verifique o hardware do sistema e a integridade do software.|


## <a name="next-steps"></a>Próximas etapas

[Requisitos de certificado](azure-stack-edge-gpu-certificate-requirements.md)