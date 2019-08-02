---
title: Visão geral da política SSL para o Gateway de Aplicativo do Azure | Microsoft Docs
description: Saiba mais sobre como o Gateway de Aplicativo do Azure permite que você configure a política SSL
services: application gateway
documentationcenter: na
author: amsriva
manager: ''
editor: ''
tags: azure resource manager
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure services
ms.date: 08/03/2017
ms.author: amsriva
ms.openlocfilehash: 1710635f145136e564a2bad48d539f242c9bc228
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68359901"
---
# <a name="application-gateway-ssl-policy-overview"></a>Visão geral da política SSL do Gateway de Aplicativo

É possível usar o Gateway de Aplicativo do Azure para centralizar o gerenciamento de certificados SSL e reduzir a sobrecarga de criptografia e descriptografia de um farm de servidores de back-end. Esse tratamento SSL centralizado também permite que você especifique uma política SSL central adequada aos seus requisitos de segurança organizacional. Isso ajuda a atender aos requisitos de conformidade, bem como às diretrizes de segurança e às práticas recomendadas.

A política SSL inclui controle de versão de protocolo SSL, bem como conjuntos de criptografia e a ordem em que as criptografias são usadas durante um handshake SSL. O Gateway de Aplicativo oferece dois mecanismos para controlar a política de SSL. Você pode usar uma política predefinida ou uma política personalizada.

## <a name="predefined-ssl-policy"></a>Política SSL predefinida

O Gateway de Aplicativo tem três políticas de segurança predefinidos. Você pode configurar o gateway com alguma dessas políticas para obter o nível adequado de segurança. Os nomes de política são anotados pelo ano e mês no qual eles foram configurados. Cada política oferece diferentes versões protocolo SSL e conjuntos de codificação. É recomendável usar as políticas SSL mais recentes para garantir a melhor segurança SSL.

### <a name="appgwsslpolicy20150501"></a>AppGwSslPolicy20150501

|Propriedade  |Valor  |
|---|---|
|Nome     | AppGwSslPolicy20150501        |
|MinProtocolVersion     | TLSv1_0        |
|Padrão| True (se nenhuma política predefinida é especificada) |
|CipherSuites     |TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_DHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_DHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_DHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_DHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA<br>TLS_DHE_DSS_WITH_AES_256_CBC_SHA256<br>TLS_DHE_DSS_WITH_AES_128_CBC_SHA256<br>TLS_DHE_DSS_WITH_AES_256_CBC_SHA<br>TLS_DHE_DSS_WITH_AES_128_CBC_SHA<br>TLS_RSA_WITH_3DES_EDE_CBC_SHA<br>TLS_DHE_DSS_WITH_3DES_EDE_CBC_SHA |
  
### <a name="appgwsslpolicy20170401"></a>AppGwSslPolicy20170401
  
|Propriedade  |Valor  |
|   ---      |  ---       |
|Nome     | AppGwSslPolicy20170401        |
|MinProtocolVersion     | TLSv1_1        |
|Padrão| False |
|CipherSuites     |TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA |
  
### <a name="appgwsslpolicy20170401s"></a>AppGwSslPolicy20170401S

|Propriedade  |Valor  |
|---|---|
|Nome     | AppGwSslPolicy20170401S        |
|MinProtocolVersion     | TLSv1_2        |
|Padrão| False |
|CipherSuites     |TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256 <br>    TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384 <br>    TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA <br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA <br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA<br> |

## <a name="custom-ssl-policy"></a>Política SSL personalizada

Se uma política SSL predefinida tiver de ser configurada para suas necessidades, você terá de definir sua própria política SSL personalizada. Com uma política SSL personalizada, você tem controle total sobre a versão mínima do protocolo SSL para dar suporte, bem como conjuntos de criptografia com suporte e a ordem de prioridade.
 
### <a name="ssl-protocol-versions"></a>Versões de protocolo SSL

* SSL 2.0 e 3.0 são desabilitados por padrão para todos os gateways do aplicativo. Essas versões de protocolo não são configuráveis.
* Uma política SSL personalizada oferece a opção de selecionar qualquer um dos três protocolos a seguir como a versão mínima do protocolo SSL para seu gateway: TLSv1_0, TLSv1_1 e TLSv1_2.
* Se nenhuma política SSL for definida, todos os três protocolos (TLSv1_0, TLSv1_1 e TLSv1_2) deverão ser habilitados.

### <a name="cipher-suites"></a>Conjuntos de criptografia

O Gateway de Aplicativo dá suporte aos seguintes conjuntos de criptografia dos quais a política personalizada pode ser escolhida. A ordenação dos pacotes de codificação determina a ordem de prioridade durante a negociação SSL.


- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA
- TLS_DHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_DHE_RSA_WITH_AES_256_CBC_SHA
- TLS_DHE_RSA_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_AES_256_GCM_SHA384
- TLS_RSA_WITH_AES_128_GCM_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA256
- TLS_RSA_WITH_AES_128_CBC_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA
- TLS_RSA_WITH_AES_128_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_3DES_EDE_CBC_SHA
- TLS_DHE_DSS_WITH_3DES_EDE_CBC_SHA

> [!NOTE]
> Os conjuntos de codificação SSL usados para a conexão também são baseados no tipo de certificado que está sendo usado. No cliente para conexões de gateway de aplicativo, os conjuntos de codificação usados são baseados no tipo de certificados de servidor no ouvinte do gateway de aplicativo. No gateway de aplicativo para conexões de pool de back-end, os conjuntos de codificação usados são baseados no tipo de certificados de servidor nos servidores do pool de back-end.

## <a name="next-steps"></a>Próximas etapas

Se desejar aprender a configurar uma política SSL, confira [Configurar política SSL em um gateway de aplicativo](application-gateway-configure-ssl-policy-powershell.md).
