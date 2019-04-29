---
title: Autoridades de certificação permitidas para habilitar HTTPS personalizado no Azure Front Door Service | Microsoft Docs
description: Se você estiver usando seu próprio certificado para habilitar HTTPS em um domínio personalizado, deverá usar uma AC (autoridade de certificação) permitida para criá-lo.
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.assetid: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/23/2018
ms.author: sharadag
ms.openlocfilehash: 5cf94079dcd68887d9725ffbe9124f9b6c897dd0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60736149"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-front-door-service"></a>Autoridades de certificação permitidas para habilitar HTTPS personalizado no Azure Front Door Service

Para um domínio personalizado do Azure Front Door Service, ao [habilitar o recurso HTTPS usando seu próprio certificado](front-door-custom-domain-https.md?tabs=option-2-enable-https-with-your-own-certificate), será preciso usar uma AC (autoridade de certificação) permitida para criar o certificado SSL. Caso contrário, se você usar uma AC não permitida ou um certificado autoassinado, sua solicitação será rejeitada.

As ACs a seguir são permitidas quando você criar seu próprio certificado:

- AddTrust External CA Root
- AC de raiz AlphaSSL
- AME Infra CA 01
- AME Infra CA 02
- Ameroot
- AP Root CA
- AP Root Certificate Authority 2013
- AP Root Certificate Authority 2014
- APCA-DM3P
- Autopilot Root CA
- Baltimore CyberTrust Root
- Class 3 Public Primary Certification Authority
- COMODO RSA Certification Authority
- COMODO RSA Domain Validation Secure Server CA
- D-TRUST Root Class 3 CA 2 2009
- DigiCert Cloud Services CA-1
- DigiCert Global Root CA
- DigiCert High Assurance CA-3
- DigiCert High Assurance EV Root CA
- DigiCert SHA2 CA do Servidor de Validação Estendido
- DigiCert SHA2 High Assurance Server CA
- DigiCert SHA2 Secure Server CA
- DST Raiz CA X3
- D-TRUST Classe de Raiz 3 CA 2 2009
- Em todos os lugares criptografia CA DE TLS DV
- Autoridade de certificação raiz da Entrust
- Autoridade de certificação raiz da Entrust - G2
- Autoridade de certificação raiz da Entrust (2048)
- GeoTrust Global CA
- GeoTrust Primary Certification Authority
- GeoTrust Primary Certification Authority – G2
- Geotrust RSA CA 2018
- GlobalSign
- GlobalSign Extended Validation CA – SHA256 – G2
- GlobalSign Organization Validation CA – G2
- GlobalSign Root CA
- Go Daddy Root Certificate Authority – G2
- Go Daddy Secure Certificate Authority - G2
- RapidSSL RSA CA 2018
- Root Agency
- Symantec Class 3 EV SSL CA – G3
- Symantec Class 3 Secure Server CA – G4
- Symantec Enterprise Mobile Root for Microsoft
- Thawte Primary Root CA
- Thawte Primary Root CA – G2
- Thawte Primary Root CA – G3
- Thawte RSA CA 2018
- Thawte Timestamping CA
- TrustAsia TLS RSA CA
- VeriSign Class 3 Extended Validation SSL CA
- VeriSign Class 3 Extended Validation SSL SGC CA
- VeriSign Class 3 Public Primary Certification Authority – G5
- VeriSign International Server CA – Class 3
- VeriSign Time Stamping Service Root
- VeriSign Universal Root Certification Authority
