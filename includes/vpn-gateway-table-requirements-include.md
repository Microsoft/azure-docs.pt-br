---
title: incluir arquivo
description: incluir arquivo
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: dc018b5d09c9b33c10cd2d54ac6572537e05ed25
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96010738"
---
A tabela a seguir lista os requisitos para gateways de VPN PolicyBased e RouteBased. Esta tabela aplica-se a ambos os modelos de implantação do Gerenciador de Recursos e clássico. Para o modelo clássico, os gateways de VPN PolicyBased são iguais aos gateways Estáticos, e os gateways Baseados em rota são iguais aos gateways Dinâmicos.

|  | **Gateway Básico de VPN PolicyBased** | **Gateway Básico de VPN RouteBased** | **Gateway Padrão de VPN RouteBased** | **Gateway de Alto Desempenho de VPN RouteBased** |
| --- | --- | --- | --- | --- |
| **Conectividade site a site (S2S)** |Configuração de VPN PolicyBased |Configuração de VPN RouteBased |Configuração de VPN RouteBased |Configuração de VPN RouteBased |
| **Conectividade P2S (Ponto a Site**) |Sem suporte |Com suporte (pode coexistir com S2S) |Com suporte (pode coexistir com S2S) |Com suporte (pode coexistir com S2S) |
| **Método de autenticação** |Chave pré-compartilhada |Chave pré-compartilhada para conectividade S2S, Certificados para conectividade P2S |Chave pré-compartilhada para conectividade S2S, Certificados para conectividade P2S |Chave pré-compartilhada para conectividade S2S, Certificados para conectividade P2S |
| **Número máximo de conexões S2S** |1 |10 |10 |30 |
| **Número máximo de conexões P2S** |Sem suporte |128 |128 |128 |
| **Suporte ao roteamento ativo (BGP)** (*) |Sem suporte |Sem suporte |Com suporte |Com suporte |

  (*) Não há suporte para GBP no modelo de implantação clássico.
