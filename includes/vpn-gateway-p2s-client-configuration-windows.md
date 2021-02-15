---
title: incluir arquivo
description: incluir arquivo
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/28/2020
ms.author: cherylmc
ms.openlocfilehash: f37876a0b6b7e8b520b5b6428381ebaf7b5b5c24
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93042605"
---
Você pode usar o mesmo pacote de configuração de cliente VPN em cada computador cliente do Windows, desde que a versão corresponda à arquitetura do cliente. Para obter a lista de sistemas operacionais quer recebem suporte, confira a seção Ponto a Site das [Perguntas frequentes sobre o Gateway de VPN](../articles/vpn-gateway/vpn-gateway-vpn-faq.md#P2S).

>[!NOTE]
>Você deve ter direitos de Administrador no computador cliente do Windows a partir do qual deseja conectar-se.
>

Use as seguintes etapas para configurar o cliente VPN do Windows nativo para autenticação de certificado:

1. Selecione os arquivos de configuração de cliente VPN que correspondem à arquitetura do computador com Windows. Para uma arquitetura de processador de 64 bits, escolha o pacote do instalador 'VpnClientSetupAmd64'. Para uma arquitetura de processador de 32 bits, escolha o pacote do instalador 'VpnClientSetupX86'. 
1. Clique duas vezes no pacote para instalá-lo. Se você vir um pop-up do SmartScreen, clique em **mais informações** e em **executar mesmo assim** .
1. No computador cliente, navegue até **configurações de rede** e clique em **VPN** . A conexão VPN mostra o nome da rede virtual a que ele se conecta.
1. Antes de tentar se conectar, verifique se você instalou um certificado do cliente no computador cliente. Um certificado do cliente é necessário para autenticação ao usar o tipo de autenticação de certificado do Azure nativo.