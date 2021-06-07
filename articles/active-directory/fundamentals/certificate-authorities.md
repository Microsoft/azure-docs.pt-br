---
title: Autoridades de certificação Azure Active Directory
description: Listagem de certificados confiáveis usados no Azure
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: baselden
ms.reviewer: baselden
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: bea6f4161bcca063cd2c58d4c463473426f159ba
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96859124"
---
# <a name="certificate-authorities-used-by-azure-active-directory"></a>Autoridades de certificação usadas pelo Azure Active Directory

> [!IMPORTANT]
> As informações nesta página são relevantes apenas para entidades que especificam explicitamente uma lista de autoridades de certificação aceitáveis (CAs). Essa prática, conhecida como fixação de certificado, deve ser evitada, a menos que não haja nenhuma outra opção.

Qualquer entidade que tentar acessar os serviços de identidade do Azure Active Directory (AD do Azure) por meio dos protocolos TLS/SSL receberá certificados das CAs listadas abaixo. Se a entidade confiar nessa autoridade de certificação, ela poderá usar os certificados para verificar a identidade e a legitimidade dos serviços de identidade e estabelecer conexões seguras.

As autoridades de certificação podem ser classificadas em CAs raiz e CAs intermediárias. Normalmente, as CAs raiz têm uma ou mais CAs intermediárias associadas. Este artigo lista as CAs raiz usadas pelos serviços de identidade do Azure AD e as CAs intermediárias associadas a cada uma dessas raízes. Para cada AC, incluímos URIs (identificadores de recursos uniformes) para baixar o AIA (acesso a informações de autoridade) e os arquivos de CDP (ponto de distribuição da lista de certificados revogados). Quando apropriado, também fornecemos um URI para o ponto de extremidade OCSP (protocolo de status de certificado online).

## <a name="cas-used-in-azure-public-and-azure-us-government-clouds"></a>CAs usadas nas nuvens do Azure Public e do Azure no governo dos EUA

Serviços diferentes podem usar CAs raiz ou intermediárias diferentes. Portanto, todas as entradas listadas abaixo podem ser necessárias.

### <a name="digicert-global-root-g2"></a>DigiCert Global Root G2


| AC Raiz| Número de Série| Data de vencimento da data de emissão| Impressão digital SHA1| URIs |
| - |- |-|-|-|-|
| DigiCert Global Root G2| 033af1e6a711a 9a0bb2864b11d09fae5| 1º de agosto de 2013 <br>15 de janeiro de 2038| df3c24f9bfd666761b268 073fe06d1cc8d4f82a4| [AIA](http://cacerts.digicert.com/DigiCertGlobalRootG2.crt)<br>[Protection](http://crl3.digicert.com/DigiCertGlobalRootG2.crl) |


#### <a name="associated-intermediate-cas"></a>ACS intermediárias associadas

| CA emissora e intermediária| Número de Série| Data de vencimento da data de emissão| Impressão digital SHA1| URIs |
| - | - | - | - | - | 
| AC emissora de Microsoft Azure TLS 01| 0aafa6c5ca63c45141 ea3be1f7c75317| 29 de julho de 2020<br>27 de junho de 2024| 2f2877c5d778c31e0f29c 7e371df5471bd673173| [AIA](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2001%20-%20xsign.crt)<br>[Protection](https://www.microsoft.com/pkiops/crl/Microsoft%20Azure%20TLS%20Issuing%20CA%2001.crl)|
|CA de emissão de Microsoft Azure TLS 02| 0c6ae97cced59983 8690a00a9ea53214| 29 de julho de 2020<br>27 de junho de 2024| e7eea674ca718e3befd 90858e09f8372ad0ae2aa| [AIA](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2002%20-%20xsign.crt)<br>[Protection](https://www.microsoft.com/pkiops/crl/Microsoft%20Azure%20TLS%20Issuing%20CA%2002.crl) |
| Microsoft Azure a CA de emissão TLS 05| 0d7bede97d8209967a 52631b8bdd18bd| 29 de julho de 2020<br>27 de junho de 2024| 6c3af02e7f269aa73a fd0eff2a88a4a1f04ed1e5| [AIA](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2005%20-%20xsign.crt)<br>[Protection](https://www.microsoft.com/pkiops/crl/Microsoft%20Azure%20TLS%20Issuing%20CA%2005.crl) |
| Microsoft Azure a AC emissora do TLS 06| 02e79171fb8021e93fe 2d983834c50c0| 29 de julho de 2020<br>27 de junho de 2024| 30e01761ab97e59a06b 41ef20af6f2de7ef4f7b0| [AIA](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2006.cer)<br>[Protection](https://www.microsoft.com/pkiops/crl/Microsoft%20Azure%20TLS%20Issuing%20CA%2006.crl) |


 ### <a name="baltimore-cybertrust-root"></a>Baltimore CyberTrust Root

| AC Raiz| Número de Série| Data de vencimento da data de emissão| Impressão digital SHA1| URIs |
| - | - | - | - | - | 
| Baltimore CyberTrust Root| 020000b9| 12 de maio de 2000<br>12 de maio de 2025| d4de20d05e66fc53fe 1a50882c78db2852cae474|<br>[Protection](http://crl3.digicert.com/Omniroot2025.crl)<br>[OCSP](http://ocsp.digicert.com/) |


#### <a name="associated-intermediate-cas"></a>ACS intermediárias associadas

| CA emissora e intermediária| Número de Série| Data de vencimento da data de emissão| Impressão digital SHA1| URIs |
| - | - | - | - | - | 
| Microsoft RSA TLS CA 01| 703d7a8f0ebf55aaa 59f98eaf4a206004eb2516a| 21 de julho de 2020<br>8 de outubro de 2024| 417e225037fbfaa4f9 5761d5ae729e1aea7e3a42| [AIA](https://www.microsoft.com/pki/mscorp/Microsoft%20RSA%20TLS%20CA%2001.crt)<br>[Protection](https://mscrl.microsoft.com/pki/mscorp/crl/Microsoft%20RSA%20TLS%20CA%2001.crl)<br>[OCSP](http://ocsp.msocsp.com/) |
| Microsoft RSA TLS CA 02| b0c2d2d13cdd56cdaa 6ab6e2c04440be4a429c75| 21 de julho de 2020<br>20 de maio de 2024| 54d9d20239080c32316ed 9ff980a48988f4adf2d| [AIA](https://www.microsoft.com/pki/mscorp/Microsoft%20RSA%20TLS%20CA%2002.crt)<br>[Protection](https://mscrl.microsoft.com/pki/mscorp/crl/Microsoft%20RSA%20TLS%20CA%2002.crl)<br>[OCSP](http://ocsp.msocsp.com/) |


 ### <a name="digicert-global-root-ca"></a>DigiCert Global Root CA

| AC Raiz| Número de Série| Data de vencimento da data de emissão| Impressão digital SHA1| URIs |
| - | - | - | - | - | 
| DigiCert Global Root CA| 083be056904246 b1a1756ac95991c74a| 9 de novembro de 2006<br>9 de novembro de 2031| a8985d3a65e5e5c4b2d7 d66d40c6dd2fb19c5436| [Protection](http://crl3.digicert.com/DigiCertGlobalRootCA.crl)<br>[OCSP](http://ocsp.digicert.com/) |


#### <a name="associated-intermediate-cas"></a>ACS intermediárias associadas

| CA emissora e intermediária| Número de Série| Data de vencimento da data de emissão| Impressão digital SHA1| URIs |
| - | - | - | - | - |
| DigiCert SHA2 Secure Server CA| 01fda3eb6eca75c 888438b724bcfbc91| 8 de março de 2013 a 8 de março de 2023| 1fb86b1168ec743154062 e8c9cc5b171a4b7ccb4| [AIA](http://cacerts.digicert.com/DigiCertSHA2SecureServerCA.crt)<br>[Protection](http://crl3.digicert.com/ssca-sha2-g6.crl)<br>[OCSP](http://ocsp.digicert.com/) |
| DigiCert SHA2 Secure Server CA |02742eaa17ca8e21 c717bb1ffcfd0ca0 |22 de setembro de 2020<br>22 de setembro de 2030|626d44e704d1ceabe3bf 0d53397464ac8080142c|[AIA](http://cacerts.digicert.com/DigiCertSHA2SecureServerCA-2.crt)<br>[Protection](http://crl3.digicert.com/DigiCertSHA2SecureServerCA.crl)<br>[OCSP](http://ocsp.digicert.com/)|


## <a name="cas-used-in-azure-china-21vianet-cloud"></a>CAs usadas na nuvem da 21Vianet do Azure na China

### <a name="digicert-global-root-ca"></a>DigiCert Global Root CA


| AC Raiz| Número de Série| Data de vencimento da data de emissão| Impressão digital SHA1| URIs |
| - | - | - | - | - |
| DigiCert Global Root CA| 083be056904246b 1a1756ac95991c74a| 9 de novembro de 2006<br>9 de novembro de 2031| a8985d3a65e5e5c4b2d7 d66d40c6dd2fb19c5436| [Protection](http://ocsp.digicert.com/)<br>[OCSP](http://crl3.digicert.com/DigiCertGlobalRootCA.crl) |


#### <a name="associated-intermediate-ca"></a>Autoridade de certificação intermediária associada

| CA emissora e intermediária| Número de Série| Data de vencimento da data de emissão| Impressão digital SHA1| URIs |
| - | - | - | - | - | - |
| CA DigiCert básico RSA CN G2| 02f7e1f982bad 009aff47dc95741b2f6| 4 de março de 2020<br>4 de março de 2030| 4d1fa5d1fb1ac3917c08e 43f65015e6aea571179| [AIA](http://cacerts.digicert.cn/DigiCertBasicRSACNCAG2.crt)<br>[Protection](http://crl.digicert.cn/DigiCertBasicRSACNCAG2.crl)<br>[OCSP](http://ocsp.digicert.cn/) |

## <a name="next-steps"></a>Próximas etapas
[Saiba mais sobre Microsoft 365 cadeias de criptografia](/microsoft-365/compliance/encryption-office-365-certificate-chains)
