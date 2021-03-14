---
title: Configurar regras de NAT de VPN para seu gateway
titleSuffix: Azure Virtual WAN
description: Saiba como configurar regras de NAT para o gateway de VPN VWAN
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 03/05/2021
ms.author: cherylmc
ms.openlocfilehash: 6fbee31f015953bd7e65648ea273e3ca84686115
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2021
ms.locfileid: "102431171"
---
# <a name="configure-nat-rules-for-your-virtual-wan-vpn-gateway---preview"></a>Configurar regras de NAT para o gateway de VPN de WAN virtual-visualização

> [!IMPORTANT]
> As regras de NAT estão atualmente em visualização pública.
> Esta versão de visualização é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Você pode configurar seu gateway de VPN de WAN virtual com regras de NAT estática um-para-um. Uma regra NAT fornece um mecanismo para configurar a tradução de um para um de endereços IP. O NAT pode ser usado para interconectar duas redes IP que têm endereços IP incompatíveis ou sobrepostos. Um cenário típico é ramificações com IPs sobrepostos que desejam acessar recursos de VNet do Azure.

Essa configuração usa uma tabela de fluxo para rotear o tráfego de um endereço IP externo (host) para um endereço IP interno associado a um ponto de extremidade dentro de uma rede virtual (máquina virtual, computador, contêiner, etc.).

   :::image type="content" source="./media/nat-rules-vpn-gateway/diagram.png" alt-text="Diagrama mostrando a arquitetura.":::

## <a name="configure-nat-rules"></a><a name="rules"></a>Configurar regras de NAT

Você pode configurar e exibir as regras de NAT em suas configurações de gateway de VPN a qualquer momento.

   :::image type="content" source="./media/nat-rules-vpn-gateway/edit-rules.png" alt-text="Captura de tela mostrando regras de edição.":::

1. Navegue até o Hub virtual.
1. Selecione **VPN (site a site)**.
1. Selecione **regras de NAT (editar)**.
1. Na página **Editar regra NAT** , você pode **Adicionar/editar/Excluir** uma regra NAT usando os seguintes valores:

   * **Nome:** Um nome exclusivo para sua regra NAT.
   * **Tipo:** Auto-estática. O NAT estático um-para-um estabelece uma relação um-para-um entre um endereço interno e um endereço externo.
   * **Modo:** IngressSnat ou EgressSnat.  
      * O modo IngressSnat (também conhecido como NAT de origem de entrada) é aplicável ao tráfego que entra no gateway de VPN site a site do Hub do Azure.
      * O modo EgressSnat (também conhecido como NAT de origem de saída) é aplicável ao tráfego que sai do gateway de VPN site a site do Hub do Azure.
   * **InternalMapping:** Um intervalo de prefixo de endereço de IPs de origem na rede interna que será mapeado para um conjunto de IPs externos. Em outras palavras, seu intervalo de prefixo de endereço de NAT.
   * **ExternalMapping:** Um intervalo de prefixo de endereço de IPs de destino na rede externa à qual os IPs de origem serão mapeados. Em outras palavras, seu intervalo de prefixo de endereço de NAT.
   * **Conexão de link:** Recurso de conexão que conecta virtualmente um site de VPN ao gateway de VPN site a site do Hub do Azure.

### <a name="configuration-considerations"></a><a name="considerations"></a>Considerações sobre configuração

* O tamanho da sub-rede para o mapeamento interno e externo deve ser o mesmo para NAT estático um-para-um.
* Certifique-se de editar o site VPN no portal do Azure para adicionar prefixos **ExternalMapping** no campo ' espaço de endereço privado '. Atualmente, os sites que têm o BGP habilitado precisam garantir que o anunciante BGP local (configurações de BGP do dispositivo) inclua uma entrada para os prefixos de mapeamento externo.

## <a name="examples-and-verification"></a><a name="examples"></a>Exemplos e verificação

### <a name="ingress-mode-nat"></a>NAT do modo de entrada

As regras NAT do modo de entrada são aplicadas em pacotes que estão entrando no Azure por meio do gateway de VPN site a site da WAN virtual. Nesse cenário, você deseja conectar duas ramificações VPN site a site ao Azure. O site da VPN 1 conecta-se via LINK1 e a VPN site 2 se conecta por meio do link 2. Cada site tem o espaço de endereço 192.169.1.0/24.

O diagrama a seguir mostra o resultado final projetado:

:::image type="content" source="./media/nat-rules-vpn-gateway/ingress.png" alt-text="Diagrama mostrando NAT do modo de entrada.":::

1. Especifique uma regra NAT.

   Especifique uma regra NAT para garantir que o gateway de VPN site a site seja capaz de distinguir entre as duas ramificações com espaços de endereço sobrepostos (como 192.168.1.0/24). Neste exemplo, nos concentramos no LINK1 para o site 1 de VPN.

   A regra NAT a seguir pode ser configurada e associada ao link 1 de uma das ramificações. Como essa é uma regra NAT estática, os espaços de endereço de InternalMapping e ExternalMapping contêm o mesmo número de endereços IP.

   * **Nome:** IngressRule01
   * **Tipo:** Auto-estática
   * **Modo:** IngressSnat
   * **InternalMapping:** 192.168.1.0/24
   * **ExternalMapping:** 10.1.1.0/24
   * **Conexão de link:** Link 1

1. Anuncie o ExternalMapping correto.

   Nesta etapa, verifique se o gateway de VPN site a site anuncia o espaço de endereço ExternalMapping correto para o restante dos recursos do Azure. Há instruções diferentes, dependendo se o BGP está habilitado ou não.

   **Exemplo 1: o BGP está habilitado**

   * Verifique se o alto-falante do BGP local localizado no site da VPN 1 está configurado para anunciar o espaço de endereço 10.1.1.0/24.
   * Durante essa visualização, os sites que têm o BGP habilitado precisam garantir que o anunciante BGP local (configurações de BGP do dispositivo) inclua uma entrada para os prefixos de mapeamento externo.

   **Exemplo 2: o BGP não está habilitado**

   * Navegue até o recurso de Hub virtual que contém o gateway de VPN site a site. Na página Hub virtual, em **conectividade**, selecione **VPN (site a site)**.
   * Selecione o site VPN que está conectado ao Hub WAN virtual via link 1. Selecione **Editar site** e entrada 10.1.1.0/24 como o espaço de endereço privado para o site VPN.

     :::image type="content" source="./media/nat-rules-vpn-gateway/edit-site.png" alt-text="Captura de tela mostrando a página Editar site VPN.":::

### <a name="packet-flow"></a>Fluxo de pacotes

Neste exemplo, um dispositivo local deseja acessar uma rede virtual spoke. O fluxo de pacotes é o seguinte, com as traduções NAT em negrito.

1. O tráfego do local é iniciado.
   * Endereço IP de origem: **192.168.1.1**
   * Endereço IP de destino: 30.0.0.1
1. O tráfego entra no gateway site a site e é convertido usando a regra NAT e, em seguida, enviado para o spoke.
   * Endereço IP de origem: **10.1.1.1**
   * Endereço IP de destino: 30.0.0.1
1. A resposta do spoke foi iniciada.
   * Endereço IP de origem: 30.0.0.1
   * Endereço IP de destino: **10.1.1.1**
1. O tráfego entra no gateway de VPN site a site e a conversão é revertida e enviada para o local.
   * Endereço IP de origem: 30.0.0.1
   * Endereço IP de destino: **192.168.1.1**

### <a name="verification-checks"></a>Verificações de verificação

Esta seção mostra verificações para verificar se a configuração está configurada corretamente.

#### <a name="validate-defaultroutetable-rules-and-routes"></a>Validar defaultroutetable, regras e rotas

As ramificações na WAN virtual são associadas à **Defaultroutetable**, o que implica que todas as conexões de ramificação aprendem rotas que são preenchidas dentro de defaultroutetable. Você verá a regra NAT com o prefixo de mapeamento externo nas rotas efetivas do defaultroutetable.

Exemplo:

* **Prefixo:** 10.1.1.0/24  
* **Tipo do próximo salto:** VPN_S2S_Gateway
* **Próximo salto:** VPN_S2S_Gateway recurso

#### <a name="validate-address-prefixes"></a>Validar prefixos de endereço

Este exemplo se aplica a recursos em redes virtuais que estão associadas a defaultroutetable.

As **rotas efetivas** nas placas de interface de rede (NIC) de qualquer máquina virtual que está localizada em uma rede virtual spoke conectada ao Hub Wan virtual também devem conter os prefixos de endereço das regras NAT **ExternalMapping**.

#### <a name="validate-bgp-advertisements"></a>Validar anúncios de BGP

Se você tiver o BGP configurado na conexão do site VPN, verifique o viva-voz do BGP local para certificar-se de que ele está anunciando uma entrada para os prefixos de mapeamento externo.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre configurações de site a site, consulte [Configurar uma conexão site a site de WAN virtual](virtual-wan-site-to-site-portal.md).
