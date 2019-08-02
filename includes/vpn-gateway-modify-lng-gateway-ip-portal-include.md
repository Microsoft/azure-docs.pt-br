---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 52084b065ef65a69a6691b6646d1e199f011910d
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67171757"
---
### <a name="gwipnoconnection"></a> Para modificar o endereço IP do gateway de rede local - sem conexão de gateway

Use o exemplo para modificar um gateway de rede local que não tenha uma conexão de gateway. Ao modificar esse valor, você também pode modificar os prefixos do endereço ao mesmo tempo.

1. No recurso Gateway de Rede Local, na seção **Configurações**, clique em **Configuração**.
2. Na caixa **Endereço IP**, modifique o endereço IP.
3. Clique em **Salvar** para salvar as configurações.

### <a name="gwipwithconnection"></a>Para modificar o endereço IP do gateway de rede local - conexão de gateway existente

Para modificar um gateway de rede local com uma conexão, você precisa primeiro remover a conexão. Após a conexão ser removida, você pode modificar o endereço IP do gateway e recriar uma nova conexão. Você também pode modificar os prefixos do endereço ao mesmo tempo. Isso resulta em algum tempo de inatividade para a conexão VPN. Ao modificar o endereço IP de gateway, você não precisa excluir o gateway de VPN. Você precisa apenas remover a conexão.
 
#### <a name="1-remove-the-connection"></a>1. Remova a conexão.

1. No recurso de Gateway de Rede Local, na seção **Configurações**, clique em **Conexões**.
2. Clique em **...** na linha para a conexão e então clique em **Excluir**.
3. Para salvar suas configurações, clique em **Salvar** .

#### <a name="2-modify-the-ip-address"></a>2. Modifique o endereço IP.

Você também pode modificar os prefixos do endereço ao mesmo tempo.

1. Na caixa **Endereço IP**, modifique o endereço IP.
2. Clique em **Salvar** para salvar as configurações.

#### <a name="3-recreate-the-connection"></a>3. Recrie a conexão.

1. Navegue até o Gateway de Rede Virtual para sua rede virtual. (Não o Gateway de Rede Local).
2. No Gateway de Rede Virtual, na seção **Configurações**, clique em **Conexões**.
3. Clique em **+ Adicionar** para abrir a folha **Adicionar conexão**.
4. Recrie sua conexão.
5. Clique em **OK** para criar a conexão.