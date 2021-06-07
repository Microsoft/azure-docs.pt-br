---
title: incluir arquivo
description: arquivo de inclusão
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/22/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 48f33514510618abadf329a11a9ab71a020be0bd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92479521"
---
1. Abra a página do gateway de rede virtual. É possível navegar até o gateway acessando **Nome da sua VNet -> Visão geral -> Dispositivos conectados -> Nome do seu gateway**. Também há várias outras maneiras de navegar.
1. Na página do gateway, clique em **Conexões**. Na parte superior da página Conexões, clique em **+ Adicionar** para abrir a página **Adicionar conexão**.

   :::image type="content" source="./media/vpn-gateway-add-site-to-site-connection-portal-include/connection.png" alt-text="Conexão site a site":::
1. Na página **Adicionar conexão**, configure os valores da sua conexão.

   * **Nome:** nomeie sua conexão.
   * **Tipo de conexão**: selecione **site a site (IPSec)** .
   * **Gateway de rede virtual:** o valor é fixo porque você está se conectando por esse gateway.
   * **Gateway de rede local:** clique em **Escolher um gateway de rede local** e selecione o gateway que deseja usar.
   * **Chave Compartilhada:** o valor aqui deve corresponder ao valor usado para seu dispositivo VPN local. O exemplo usa “abc123”, mas você pode (e deve) usar algo mais complexo. O importante é que o valor especificado aqui deve ser o mesmo valor especificado ao configurar seu dispositivo VPN.
   * Deixe a opção **Usar o Endereço IP Privado do Azure** desmarcada.
   * Deixe a opção **Habilitar o BGP** desmarcada.
   * Clique em **IKEv2**.
   * Os valores restantes para **Assinatura**, **Grupo de Recursos** e **Local** são corrigidos.

1. Selecione **OK** para criar a conexão. Você verá *Criar Conexão* piscar na tela.
1. Você pode exibir a conexão na página **Conexões** do seu gateway de rede virtual. O Status será alterado de *Desconhecido* para *Conectando* e então para *Êxito*.
