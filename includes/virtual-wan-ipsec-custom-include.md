---
title: incluir arquivo
description: incluir arquivo
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 10/07/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 83f0ce27172879a37de9488499e46de30b8e112c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98147416"
---
Ao trabalhar com políticas IPsec personalizadas, tenha em mente os seguintes requisitos:

* **Ike** -para Ike, você pode selecionar qualquer parâmetro da criptografia IKE, além de qualquer parâmetro da integridade Ike, além de qualquer parâmetro do grupo DH.
* **IPSec** -para IPsec, você pode selecionar qualquer parâmetro da criptografia IPSec, além de qualquer parâmetro da integridade do IPSec, além do PFS. Se qualquer um dos parâmetros para criptografia IPsec ou integridade IPsec for GCM, os parâmetros para ambas as configurações deverão ser GCM.

>[!NOTE]
> Com as políticas IPsec personalizadas, não há nenhum conceito de respondente e iniciador (ao contrário das políticas IPsec padrão). Ambos os lados (local e gateway de VPN do Azure) usarão as mesmas configurações para a fase 1 de IKE e a fase 2 da IKE. Há suporte para os protocolos IKEv1 e IKEv2. Não há suporte apenas para o Azure como respondente.
>

**Configurações e parâmetros disponíveis**

| Configuração | Parâmetros |
|--- |--- |
| Criptografia IKE | GCMAES256, GCMAES128, AES256, AES128 |
| Integridade do IKE | SHA384, SHA256 |
| Grupo DH | ECP384, ECP256, DHGroup24, DHGroup14 |
| Criptografia IPsec | GCMAES256, GCMAES128, AES256, AES128, nenhum |
| Integridade do IPsec | GCMAES256, GCMAES128, SHA256 |
| Grupo PFS | ECP384, ECP256, PFS24, PFS14, None |
| Tempo de vida da SA |valores mínimo de 300/padrão 27000 segundos |
