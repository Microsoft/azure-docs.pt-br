---
title: Perguntas frequentes
description: Respostas para perguntas frequentes sobre o atestado de Microsoft Azure
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: reference
ms.date: 07/20/2020
ms.author: mbaldwin
ms.openlocfilehash: 39f628845bdc9d54b48b1c8037f4a506a9d5c00a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89236583"
---
# <a name="frequently-asked-questions-for-microsoft-azure-attestation"></a>Perguntas frequentes sobre o atestado de Microsoft Azure

Este artigo fornece respostas para algumas das perguntas mais comuns sobre o [atestado do Azure](overview.md).

Se o problema do Azure não for resolvido neste artigo, você também poderá enviar uma solicitação de suporte do Azure na [página de suporte do Azure](https://azure.microsoft.com/support/options/).

## <a name="what-is-azure-pck-caching-service-and-its-role-in-enclave-attestation"></a>O que é o serviço de cache PCK do Azure e sua função no atestado de enclave

O serviço de cache PCK do Azure define a linha de base de segurança do Azure para os nós de [ACC (computação confidencial) do Azure](../confidential-computing/overview.md) da Intel e armazena os dados em cache. As informações armazenadas em cache serão usadas mais detalhadamente pelo atestado do Azure na validação de ambientes de execução confiável (TEEs).  

Serviço de cache PCK do Azure:
   - Oferece alta disponibilidade 
   - Reduz dependências de serviços hospedados externamente e conectividade com a Internet.
   - Busca as versões mais recentes de certificados Intel, CRLs, informações de TCB (Trusted Computing base) e a identidade de enclave dos nós ACC da Intel. O serviço, portanto, confirma que a linha de base de segurança do Azure deve ser referenciada pelo atestado do Azure ao validar o TEEs, reduzindo significativamente as falhas de atestado devido à invalidação ou revogação de certificados Intel  

## <a name="is-sgx-attestation-supported-by-azure-attestation-in-non-azure-environments"></a>O atestado de SGX tem suporte do atestado do Azure em ambientes não Azure

O atestado do Azure depende da linha de base de segurança declarada pelo serviço de cache PCK do Azure para validar o TEEs. O serviço de cache PCK do Azure está atualmente projetado para dar suporte apenas a nós de computação confidencial do Azure. 

## <a name="what-validations-does-azure-attestation-perform-for-attesting-sgx-enclaves"></a>Quais validações o atestado do Azure executa para atestar enclaves SGX

O atestado do Azure é uma estrutura unificada para atestar remotamente diferentes tipos de TEEs. Atestado do Azure:

   - Valida se a raiz confiável de uma citação enclave assinada pertence à Intel.
   - Valida se a citação enclave atende à linha de base de segurança do Azure, conforme definido pelo serviço de cache PCK do Azure.
   - Valida se o hash SHA256 de enclave de dados mantidos (EHD) no objeto de solicitação de atestado corresponde aos primeiros 32 bytes do campo reportData na aspa enclave.
   - Permite que os clientes criem um provedor de atestado e configurem uma política personalizada. Além das validações acima, o atestado do Azure avalia a cotação de enclave em relação à política. As políticas definem regras de autorização para o enclave e também ditam regras de emissão para gerar o token de atestado. Para confirmar se o software pretendido está sendo executado em um enclave, os clientes podem adicionar regras de autorização para verificar se os campos **mrsigner** e **mrenclave** na citação enclave correspondem aos valores de binários do cliente.

## <a name="how-can-a-verifier-obtain-the-collateral-for-sgx-attestation-supported-by-azure-attestation"></a>Como um verificador pode obter o material de apoio para o atestado de SGX com suporte do atestado do Azure

Em geral, para os modelos de atestado com a Intel como a raiz de confiança, o cliente de atestado conversa com as APIs enclave para buscar a evidência enclave. As APIs enclave internamente chamam o serviço de cache Intel PCK para buscar certificados Intel do nó a ser atestado. Os certificados são usados para assinar a evidência de enclave, gerando assim um material de apoio que pode ser atestado remotamente.  

O mesmo processo pode ser implementado para o atestado do Azure. No entanto, para aproveitar os benefícios oferecidos pelo serviço de cache PCK do Azure, depois de instalar a máquina virtual ACC, é recomendável instalar a [Biblioteca DCAP do Azure](https://www.nuget.org/packages/Microsoft.Azure.DCAP). Com base no contrato com a Intel, quando a Biblioteca DCAP do Azure é instalada, as solicitações para gerar evidências enclave são redirecionadas do serviço de cache do Intel PCK para o serviço de cache PCK do Azure. A Biblioteca DCAP do Azure tem suporte em ambientes baseados no Windows e no Linux.

## <a name="how-to-shift-to-azure-attestation-from-other-attestation-models"></a>Como alternar para o atestado do Azure de outros modelos de atestado

- Depois de instalar a máquina virtual de computação confidencial do Azure, instale a Biblioteca DCAP do Azure ([Windows/](https://www.nuget.org/packages/Microsoft.Azure.DCAP/) [Linux](https://packages.microsoft.com/ubuntu/18.04/prod/pool/main/a/az-dcap-client/)) para aproveitar os benefícios oferecidos pelo serviço de cache PCK do Azure.
- O cliente de atestado remoto precisa ser criado, o que pode recuperar a evidência de enclave e enviar solicitações para o atestado do Azure. Consulte [exemplos de código](/samples/browse/?expanded=azure&terms=attestation) para referência 
- As solicitações de atestado podem ser enviadas para o ponto de extremidade da API REST de provedores padrão ou provedores de atestado personalizados 
- As APIs de atestado do Azure são protegidas pela autenticação do Azure AD. Portanto, o cliente que invoca as APIs de atestado deve ser capaz de obter e passar um token de acesso válido do Azure AD na solicitação de atestado 

## <a name="how-can-the-relying-party-verify-the-integrity-of-attestation-token"></a>Como a terceira parte confiável pode verificar a integridade do token de atestado

O token de atestado gerado pelo atestado do Azure é assinado usando um certificado autoassinado. Os certificados são expostos por meio de um [ponto de extremidade de metadados do OpenID](/rest/api/attestation/metadataconfiguration/get). A terceira parte confiável pode recuperar os certificados de autenticação desse ponto de extremidade e executar a verificação de assinatura do token de atestado. O tempo de validade do token de atestado é de 8 horas. 

## <a name="how-to-identify-the-certificate-to-be-used-for-signature-verification-from-the-openid-metadata-endpoint"></a>Como identificar o certificado a ser usado para verificação de assinatura do ponto de extremidade de metadados OpenID

Vários certificados expostos no ponto de extremidade de metadados OpenID correspondem a diferentes casos de uso (por exemplo, atestado de SGX) com suporte do atestado do Azure. De acordo com os padrões especificados pela [RFC 7515](https://tools.ietf.org/html/rfc7515), o certificado com a ID de chave (Kid) correspondente ao parâmetro *Kid* no cabeçalho do token de atestado será usado para verificação de assinatura. Se nenhum **filho** correspondente for encontrado, ele deverá tentar todos os certificados expostos pelo ponto de extremidade de metadados do OpenID.

## <a name="is-it-possible-for-the-relying-party-to-share-secrets-with-the-validated-trusted-execution-environments-tees"></a>É possível que a terceira parte confiável Compartilhe segredos com os TEEs (ambientes de execução confiáveis) validados

A chave pública gerada em um enclave pode ser expressa na propriedade de EHD (dados mantidos enclave) do objeto de solicitação de atestado enviada pelo cliente para o atestado do Azure. Depois de confirmar se o hash SHA256 de EHD é expresso no campo reportData da cotação, o atestado do Azure inclui EHD no token de atestado. A terceira parte confiável pode usar o EHD da resposta de atestado verificada para criptografar os segredos e compartilhar com o enclave. Consulte [conceitos básicos do atestado do Azure](basic-concepts.md) para obter mais informações.
