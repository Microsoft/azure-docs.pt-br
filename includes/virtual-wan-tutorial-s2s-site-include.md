---
title: incluir arquivo
description: incluir arquivo
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 02/11/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 708baa83ca919adcc374be36c229ce3ff30da384
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100362907"
---
1. Na página de portal da sua WAN virtual, na seção **Conectividade**, selecione **Sites VPN** para abrir a página Sites VPN.
1. Na página **Sites VPN**, clique em **+Criar site**.
1. Na página **Criar Site VPN**, na guia **Básico**, preencha os seguintes campos:

   :::image type="content" source="./media/virtual-wan-tutorial-site-include/site-basics.png" alt-text="Guia Básico" lightbox="./media/virtual-wan-tutorial-site-include/site-basics.png":::

    * **Região** - anteriormente conhecida como Local. Esse é o local em que você deseja criar o recurso de site.
    * **Nome**: o nome que você deseja dar ao site local.
    * **Fornecedor do dispositivo** - o nome do fornecedor do dispositivo VPN (por exemplo: Citrix, Cisco, Barracuda). Adicionar o fornecedor do dispositivo pode ajudar a equipe do Azure a entender melhor seu ambiente para possível e futuramente adicionar otimizações ou para ajudá-lo a solucionar problemas.
    * **Espaço de endereço privado** - o espaço do endereço IP localizado no local. O tráfego destinado a esse espaço de endereço é roteado para o site local. Isso é necessário quando o BGP não está habilitado para o site.
    
      >[!NOTE]
      >Caso edite o espaço de endereço depois de criar o site (por exemplo, inserir um espaço de endereço adicional), pode levar de 8 a 10 minutos para atualizar as rotas efetivas enquanto os componentes são recriados.
      >
1. Selecione **Links** para adicionar informações sobre os links físicos no branch. Se você tiver um dispositivo CPE do parceiro de WAN Virtual, verifique com o parceiro para ver se essas informações são trocadas com o Azure como parte do upload de informações do branch configurado em seus sistemas.

   :::image type="content" source="./media/virtual-wan-tutorial-site-include/site-links.png" alt-text="Guia de links" lightbox="./media/virtual-wan-tutorial-site-include/site-links.png":::

   * **Nome do link** - um nome que você deseja fornecer para o link físico no site VPN. Exemplo: mylink1.
   * **Velocidade do link** – Essa é a velocidade do dispositivo VPN na localização do branch. Exemplo: 50, que significa 50 Mbps na velocidade do dispositivo VPN no branch.
   * **Nome do provedor do link** – O nome do link físico no Site VPN. Exemplo: ATT, Verizon.
   * **Endereço IP/FQDN do link** – O endereço IP público do dispositivo local que usa esse link. Opcionalmente, você pode fornecer o endereço IP privado do dispositivo VPN local por trás do ExpressRoute. Você também pode incluir um nome de domínio totalmente qualificado. Por exemplo, *algumacoisa.contoso.com*. O FQDN deve ser resolvido do gateway de VPN. Isso será possível se o servidor DNS que hospeda esse FQDN puder ser acessado pela Internet. O endereço IP tem precedência quando o endereço IP e o FQDN são especificados.

     >[!NOTE]
     >
     >* Dá suporte a um endereço IPv4 por FQDN. Se o FQDN precisasse ser resolvido para vários endereços IP, o gateway de VPN selecionaria o primeiro endereço IP4 da lista. Não há compatibilidade com endereços IPv6 no momento.
     >
     >* O gateway de VPN mantém um cache DNS atualizado a cada 5 minutos. O gateway tenta resolver FQDNs somente para túneis desconectados. Uma redefinição de gateway ou alteração de configuração também pode disparar a resolução de FQDN.
     >
   * **Border Gateway Protocol do link** – Configurar o BGP em um link da WAN Virtual é equivalente a configurar o BGP em uma VPN do gateway de rede virtual do Azure. Seu endereço de par no nível de protocolo BGP local não deve ser o mesmo que o endereço IP público do VPN para dispositivo ou o espaço de endereço da VNet do site VPN. Use um endereço IP diferente no dispositivo VPN para o IP de par no nível de protocolo BGP. Pode ser um endereço atribuído à interface de loopback no dispositivo. Especifique esse endereço no site VPN correspondente que representa o local.  Para saber os pré-requisitos do BGP, confira [Sobre o BGP com o Gateway de VPN do Azure](../articles/vpn-gateway/vpn-gateway-bgp-overview.md). Você sempre pode editar uma conexão de link da VPN para atualizar os seus parâmetros do BGP (o IP de emparelhamento no link e o número do AS).
1. Você pode adicionar ou excluir mais links. Há suporte a quatro links por site VPN. Por exemplo, se você tiver quatro ISPs (provedor de serviços de Internet) na localização do branch, poderá criar quatro links, um para cada ISP, e fornecer informações para cada link.
1. Depois de terminar de preencher os campos, selecione **Revisão + Criar** para verificar e criar o site.
1. Navegue até o hub virtual desejado e desmarque a **Associação de hub** para conectar o seu site de VPN ao hub.

   :::image type="content" source="./media/virtual-wan-tutorial-site-include/connect.png" alt-text="Conectar-se a este hub" lightbox="./media/virtual-wan-tutorial-site-include/connect.png":::
