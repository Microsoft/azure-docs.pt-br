---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: vpn-gateway
author: WenJason
ms.service: vpn-gateway
ms.topic: include
origin.date: 03/21/2018
ms.date: 12/24/2018
ms.author: v-jay
ms.custom: include file
ms.openlocfilehash: 1199819d274590cc81d0234680f8765f9cc36c0a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60419572"
---
### <a name="noconnection"></a>Para modificar prefixos de endereço IP de gateway de rede local - sem conexão de gateway

#### <a name="to-add-additional-address-prefixes"></a>Para adicionar prefixos de endereço adicional:

1. No recurso Gateway de Rede Local, na seção **Configurações**, clique em **Configuração**.
2. Adicione o espaço de endereço IP na caixa *Adicionar intervalo de endereços adicional*.
3. Para salvar suas configurações, clique em **Salvar** .

#### <a name="to-remove-address-prefixes"></a>Para remover os prefixo de endereço:

1. No recurso Gateway de Rede Local, na seção **Configurações**, clique em **Configuração**.
2. Clique em **'...'** na linha que contém o prefixo que você deseja remover.
3. Clique em **Remover**.
4. Para salvar suas configurações, clique em **Salvar** .

### <a name="withconnection"></a>Para modificar prefixos de endereço IP de gateway de rede local - conexão de gateway existente

Se você possui uma conexão de gateway e deseja adicionar ou remover os prefixos do endereço IP contidos no gateway de rede local, você precisará executar as etapas a seguir nessa ordem. Isso resulta em algum tempo de inatividade para a conexão VPN. Ao modificar prefixos de endereço IP, você não precisa excluir o gateway de VPN. Você precisa apenas remover a conexão.

#### <a name="1-remove-the-connection"></a>1. Remova a conexão.

1. No recurso de Gateway de Rede Local, na seção **Configurações**, clique em **Conexões**.
2. Clique em **...** na linha para cadaa conexão e então clique em **Excluir**.
3. Para salvar suas configurações, clique em **Salvar** .

#### <a name="2-modify-the-address-prefixes"></a>2. Modifique os prefixos de endereço.

Para adicionar prefixos de endereço adicional:

1. No recurso Gateway de Rede Local, na seção **Configurações**, clique em **Configuração**.
2. Adicione o espaço de endereço IP.
3. Para salvar suas configurações, clique em **Salvar** .

Para remover os prefixo de endereço:

1. No recurso Gateway de Rede Local, na seção **Configurações**, clique em **Configuração**.
2. Clique em **...** na linha que contém o prefixo que você deseja remover.
3. Clique em **Remover**.
4. Para salvar suas configurações, clique em **Salvar** .

#### <a name="3-recreate-the-connection"></a>3. Recrie a conexão.

1. Navegue até o Gateway de Rede Virtual para sua rede virtual. (Não o Gateway de Rede Local).
2. No Gateway de Rede Virtual, na seção **Configurações**, clique em **Conexões**.
3. Clique em **+ Adicionar** para abrir a folha **Adicionar conexão**.
4. Recrie sua conexão.
5. Clique em **OK** para criar a conexão.