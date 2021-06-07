---
title: incluir arquivo
description: incluir arquivo
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/22/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 5358bbbca716f5152a943c90cb7a5f735ae12047
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92479523"
---
1. No [portal do Azure](https://portal.azure.com), em **Recursos de pesquisa, serviços e documentos (G+/)** , digite **gateway de rede local**. Localize o **gateway de rede local** nos resultados da pesquisa no **Marketplace** e selecione-o. Isso abrirá a página **Criar um gateway de rede local**.
1. Na **página Criar gateway de rede local**, especifique os valores para seu objeto de gateway de rede local.

   :::image type="content" source="./media/vpn-gateway-add-local-network-gateway-portal-include/create-ip.png" alt-text="Criar um gateway de rede local com um endereço IP":::

   * **Nome:** especifique um nome para seu objeto de gateway de rede local.
   * **Ponto de extremidade:** Selecione o tipo de ponto de extremidade para o dispositivo VPN local – **endereço IP** ou **FQDN (Nome de Domínio Totalmente Qualificado)** .
      * **Endereço IP**: Se você tiver um endereço IP público estático alocado do seu provedor de serviços de Internet para seu dispositivo VPN, selecione a opção de endereço IP e preencha o endereço IP, conforme mostrado no exemplo. Este é o endereço IP público do dispositivo VPN ao qual você deseja que o gateway de VPN do Azure se conecte. Se você não tiver o endereço IP no momento, poderá usar os valores mostrados no exemplo, mas precisará voltar e substituir o seu endereço IP de espaço reservado com o endereço IP público de seu dispositivo VPN. Caso contrário, o Azure não será capaz de se conectar.
      * **FQDN**: se você tiver um endereço IP público dinâmico que possa ser alterado após determinado período de tempo, geralmente determinado pelo seu provedor de serviços de Internet, você poderá usar um nome DNS constante com um serviço DNS dinâmico para apontar para seu endereço IP público atual do seu dispositivo VPN. O gateway de VPN do Azure resolverá o FQDN para determinar o endereço IP público ao qual se conectar. 
   * **Espaço de Endereço** refere-se aos intervalos de endereços para a rede que é representada por esse local. Você pode adicionar vários intervalos de espaço de endereço. Verifique se os intervalos que você especifica aqui não se sobrepõem aos intervalos de outras redes com que você deseja se conectar. O Azure roteará o intervalo de endereços especificado para o endereço IP do dispositivo VPN local. *Se deseja se conectar ao site local, use seus próprios valores aqui, e não os valores mostrados no exemplo*.
   * **Configurar as definições de BGP ASN**: Use somente ao configurar o BGP. Caso contrário, não selecione essa opção.
   * **Assinatura:** Verifique se a assinatura correta está sendo exibida.
   * **Grupo de recursos:** Selecione o grupo de recursos que você deseja usar. Você pode criar um novo grupo de recursos ou selecionar um que você já criou.
   * **Localização:** a localização é a mesma que a **Região** em outras configurações. Selecione o local em que esse objeto será criado. Você pode querer selecionar o mesmo local onde reside a sua rede virtual, mas não é necessário fazê-lo.

   > [!NOTE]
   >
   > * A VPN do Azure dá suporte a apenas um endereço IPv4 para cada FQDN. Se o nome de domínio for resolvido para vários endereços IP, o Gateway de VPN do Azure usará o primeiro endereço IP retornado pelos servidores DNS. Para eliminar a incerteza, recomendamos que seu FQDN sempre resolva para um endereço IPv4. Não há suporte para IPv6.
   > * O Gateway de VPN do Azure mantém um cache DNS atualizado a cada 5 minutos. O gateway tenta resolver FQDNs somente para túneis desconectados. A redefinição do gateway também vai disparar a resolução do FQDN.
   >

1. Quando você terminar de especificar os valores, selecione o botão **Criar** na parte inferior da página para criar o gateway de rede local.
