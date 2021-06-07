---
title: Políticas IPsec ponto a site da WAN virtual
titleSuffix: Azure Virtual WAN
description: Saiba mais sobre as políticas de conectividade IPsec ponto a site da WAN virtual do Azure.
services: virtual-wan
author: wellee
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 02/24/2021
ms.author: wellee
ms.openlocfilehash: d64748bf7719af52b76c5e4141bfbbabe80bbae2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101744270"
---
# <a name="point-to-site-ipsec-policies"></a>Políticas de IPsec de ponto a site

Este artigo mostra as combinações de política IPsec com suporte para conectividade VPN ponto a site na WAN virtual do Azure.

## <a name="default-ipsec-policies"></a>Políticas IPsec padrão

A tabela a seguir mostra os parâmetros padrão de IPsec para conexões VPN ponto a site. Esses parâmetros são escolhidos automaticamente pelo gateway de VPN ponto a site da WAN virtual quando um perfil de ponto a site é criado.

| Configuração | Parâmetros |
|--- |--- |
| Fase 1 de criptografia IKE | AES256 |
| Integridade de IKE da fase 1 |  SHA256 |
| Grupo DH | DHGroup24 |
| Criptografia IPsec da fase 2 | GCMAES256|
| Integridade IPsec da fase 2 | GCMAES25 |
| Grupo PFS |PFS24|

## <a name="custom-ipsec-policies"></a>Políticas de IPsec personalizadas

Ao trabalhar com políticas IPsec personalizadas, tenha em mente os seguintes requisitos:

* **Ike** -para Ike da fase 1, você pode selecionar qualquer parâmetro da criptografia IKE, além de qualquer parâmetro da integridade Ike, além de qualquer parâmetro do grupo DH.
* **IPSec** – para o IPsec da fase 2, você pode selecionar qualquer parâmetro da criptografia IPSec, além de qualquer parâmetro da integridade do IPSec, além do PFS. Se qualquer um dos parâmetros para criptografia IPsec ou integridade IPsec for GCM, a criptografia IPsec e a integridade deverão usar o mesmo algoritmo. Por exemplo, se GCMAES128 for escolhido para criptografia IPsec, GCMAES128 também deverá ser escolhido para a integridade do IPsec.  

A tabela a seguir mostra os parâmetros IPsec disponíveis para conexões VPN ponto a site.

| Configuração | Parâmetros |
|--- |--- |
| Fase 1 de criptografia IKE | AES128, AES256, GCMAES128, GCMAES256 |
| Integridade de IKE da fase 1 |  SHA256, SHA384 |
| Grupo DH | DHGroup14, DHGroup24, ECP256, ECP384 |
| Criptografia IPsec da fase 2 | GCMAES128, GCMAES256, SHA256|
| Integridade IPsec da fase 2 | GCMAES128, GCMAES256 |
| Grupo PFS |PFS14, PFS24, ECP256, ECP384|

## <a name="next-steps"></a>Próximas etapas

Veja [como criar uma conexão ponto a site](virtual-wan-point-to-site-portal.md)

Para obter mais informações sobre a WAN Virtual, consulte as [Perguntas frequentes sobre a WAN Virtual](virtual-wan-faq.md).
