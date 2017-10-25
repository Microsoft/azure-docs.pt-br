---
title: "Considerações de topologia de rede ao usar o Proxy de Aplicativo do Azure Active Directory | Microsoft Docs"
description: "Cobre as considerações de topologia de rede ao usar o Proxy de Aplicativo do Azure AD."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/28/2017
ms.author: kgremban
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 11244e0044eef8441e3a37ab8aeff0da30dacdb8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="network-topology-considerations-when-using-azure-active-directory-application-proxy"></a>Considerações de topologia de rede ao usar o Proxy de Aplicativo do Azure Active Directory

Este artigo explica as considerações de topologia de rede ao usar o Proxy de Aplicativo do Azure AD (Azure Active Directory) para publicar e acessar seus aplicativos remotamente.

## <a name="traffic-flow"></a>Fluxo de tráfego

Quando um aplicativo é publicado por meio do Proxy de Aplicativo do Azure AD, o tráfego dos usuários para os aplicativos flui por meio de três conexões:

1. O usuário se conecta ao ponto de extremidade público do serviço Proxy de Aplicativo do Azure AD no Azure
2. O serviço Proxy de Aplicativo se conecta ao conector de Proxy de Aplicativo
3. O conector de Proxy de Aplicativo se conecta ao aplicativo de destino

![Diagrama mostrando o fluxo de tráfego de usuário para o aplicativo de destino](./media/application-proxy-network-topologies/application-proxy-three-hops.png)

## <a name="tenant-location-and-application-proxy-service"></a>Local do locatário e serviço Proxy de Aplicativo

Quando você se inscreve para um locatário do Azure AD, a região do seu locatário é determinada pelo país especificado. Quando você habilita o Proxy de Aplicativo, as instâncias do serviço Proxy de Aplicativo de seu locatário são escolhidas ou criadas na mesma região que seu locatário do Azure AD, ou na região mais próxima a ele.

Por exemplo, se a região do seu locatário do Azure AD é a UE (União Europeia), todos os conectores de Proxy de Aplicativo usam instâncias de serviço em datacenters do Azure na UE. Quando seus usuários acessarem aplicativos publicados, o tráfego deles passará por instâncias do serviço Proxy de Aplicativo nessa localização.

## <a name="considerations-for-reducing-latency"></a>Considerações para redução da latência

Todas as soluções de proxy introduzem a latência em sua conexão de rede. Não importa a solução de proxy ou VPN escolhida como sua solução de acesso remoto, ela sempre incluirá um conjunto de servidores, permitindo a conexão dentro de sua rede corporativa.

As organizações geralmente incluem pontos de extremidade do servidor em sua rede de perímetro. No entanto, com o Proxy de Aplicativo do Azure AD, o tráfego flui por meio do serviço de proxy na nuvem, enquanto os conectores residem em sua rede corporativa. Nenhuma rede de perímetro é necessária.

As seções a seguir contêm sugestões adicionais para ajudá-lo a reduzir a latência ainda mais. 

### <a name="connector-placement"></a>Posicionamento do conector

O serviço Proxy de Aplicativo escolhe o local das instâncias para você, com base no local de seu locatário. Entretanto, você pode decidir onde instalar o conector, permitindo que você defina as características de latência de seu tráfego de rede.

Ao configurar o serviço de Proxy de Aplicativo, faça as seguintes perguntas:

* Onde o aplicativo está localizado?
* Onde está localizada a maioria dos usuários que acessam o aplicativo?
* Onde a instância do Proxy de Aplicativo está localizada?
* Você já tem uma conexão de rede dedicada com os data centers configurados do Azure, por exemplo, o Azure ExpressRoute ou uma VPN semelhante?

O conector tem de se comunicar com o Azure e seus aplicativos (as etapas 2 e 3 no diagrama de fluxo Tráfego), de modo que o posicionamento do conector afeta a latência dessas duas conexões. Ao avaliar o posicionamento do conector, tenha em mente os seguintes pontos:

* Se você quiser usar a delegação restrita de Kerberos (KCD) para logon único, o conector precisará de uma linha de visão para um datacenter. Além disso, o servidor do conector deve ser ingressado no domínio.  
* Em caso de dúvida, instale o conector mais próximo ao aplicativo.

### <a name="general-approach-to-minimize-latency"></a>Abordagem geral para minimizar a latência

Você pode minimizar a latência do tráfego de ponta a ponta otimizando cada conexão de rede. Cada conexão pode ser otimizada com:

* Reduzindo a distância entre as duas extremidades do salto.
* Escolhendo a rede certa para percorrer. Por exemplo, percorrer uma rede privada, em vez da Internet pública, pode ser mais rápido devido aos links dedicados.

Se você tiver um link dedicado de VPN ou do ExpressRoute entre o Azure e sua rede corporativa, convém usá-lo.

## <a name="focus-your-optimization-strategy"></a>Concentrar-se em sua estratégia de otimização

Não há muito que você pode fazer para controlar a conexão entre os usuários e o serviço Proxy de Aplicativo. Os usuários podem acessar seus aplicativos de uma rede doméstica, de uma cafeteria ou de um país diferente. Em vez disso, você pode otimizar as conexões do serviço Proxy de Aplicativo para os conectores de Proxy de Aplicativo para os aplicativos. Considere a possibilidade de incorporar os padrões a seguir em seu ambiente.

### <a name="pattern-1-put-the-connector-close-to-the-application"></a>Padrão 1: colocar o conector perto do aplicativo

Coloque o conector perto do aplicativo de destino na rede do cliente. Essa configuração minimiza a etapa 3 no diagrama de topografia porque o conector e o aplicativo estão próximos. 

Se seu conector precisar de uma linha de visão para o controlador de domínio, então esse padrão será vantajoso. A maioria de nossos clientes usa esse padrão, porque ele funciona bem para a maioria dos cenários. Esse padrão também pode ser combinado com o padrão 2 para otimizar o tráfego entre o serviço e o conector.

### <a name="pattern-2-take-advantage-of-expressroute-with-public-peering"></a>Padrão 2: Aproveitar o ExpressRoute com o emparelhamento público

Se você tiver configurado com o emparelhamento público do ExpressRoute, você poderá usar a conexão do ExpressRoute mais rápida para o tráfego entre o Proxy de Aplicativo e o conector. O conector ainda está em sua rede, perto do aplicativo.

### <a name="pattern-3-take-advantage-of-expressroute-with-private-peering"></a>Padrão 3: Aproveitar o ExpressRoute com o emparelhamento privado

Se você tiver uma configuração de VPN ou ExpressRoute dedicada com emparelhamento privado entre o Azure e sua rede corporativa, terá outra opção. Nessa configuração, a rede virtual no Azure é geralmente considerada uma extensão da rede corporativa. Portanto, você pode instalar o conector no data center do Azure e ainda atender aos requisitos de baixa latência da conexão entre o aplicativo e o conector.

A latência não é comprometida, pois o tráfego está fluindo por uma conexão dedicada. Você também obtém uma menor latência de serviço para o conector do Proxy de Aplicativo porque o conector está instalado em um datacenter do Azure perto da localização do locatário do Azure AD.

![Diagrama mostrando o conector instalado em um datacenter do Azure](./media/application-proxy-network-topologies/application-proxy-expressroute-private.png)

### <a name="other-approaches"></a>Outras abordagens

Embora o foco deste artigo é a instalação, você também pode alterar o posicionamento do aplicativo para obter os melhores características de latência.

As organizações estão cada vez mais migrando suas redes para ambientes hospedados. Isso permite colocar os aplicativos em um ambiente hospedado que também faz parte da rede corporativa, e ainda ser dentro do domínio. Nesse caso, os padrões discutidos nas seções anteriores podem ser aplicados para o novo local do aplicativo. Se você estiver considerando essa opção, consulte [Serviços de Domínio do Azure AD](../active-directory-domain-services/active-directory-ds-overview.md).

Além disso, considere a possibilidade de organizar seus conectores usando [grupos de conectores](active-directory-application-proxy-connectors.md) para ter como destino aplicativos que estejam em locais e redes diferentes. 

## <a name="common-use-cases"></a>Casos de uso comuns

Nesta seção, veremos alguns cenários comuns. Suponha que o locatário do Azure AD (e, portanto, ponto de extremidade de serviço de proxy) está localizado nos Estados Unidos (EUA). As considerações discutidas nesses casos de uso também se aplicam a outras regiões pelo mundo.

Nesses cenários, chamamos cada conexão de um "salto" e os numeramos para uma discussão mais fácil:

- **Salto 1**: usuário para o serviço Proxy de Aplicativo
- **Salto 2**: Serviço de Proxy de Aplicativo para o conector de Proxy de Aplicativo
- **Salto 3**: Conector de Proxy de Aplicativo para o aplicativo de destino 

### <a name="use-case-1"></a>Caso de uso 1

**Cenário:** o aplicativo está na rede da organização nos EUA, com usuários na mesma região. Não há um ExpressRoute ou uma VPN entre o data center do Azure e a rede corporativa.

**Recomendação:** siga o padrão 1, explicado na seção anterior. Para melhorar a latência, considere o uso do ExpressRoute, se necessário.

Este é um padrão simples. Você otimiza o salto 3 colocando o conector perto do aplicativo. Essa também é uma opção natural, pois o conector normalmente é instalado com uma linha de visão para o aplicativo e com o data center para executar operações KCD.

![Diagrama mostrando que usuários, proxy, conector e aplicativo estão todos nos EUA](./media/application-proxy-network-topologies/application-proxy-pattern1.png)

### <a name="use-case-2"></a>Caso de uso 2

**Cenário:** o aplicativo está na rede da organização nos EUA, com usuários distribuídos globalmente. Não há um ExpressRoute ou uma VPN entre o data center do Azure e a rede corporativa.

**Recomendação:** siga o padrão 1, explicado na seção anterior. 

Novamente, o padrão mais comum é otimizar o salto 3, onde você coloca o conector perto do aplicativo. O salto 3 não costuma ser caro, se tudo estiver dentro da mesma região. No entanto, o salto 1 poderá ser mais caro dependendo de onde o usuário estiver, pois os usuários pelo mundo precisarão acessar a instância do Proxy de Aplicativo nos EUA. Vale a pena observar que qualquer solução de proxy tem características semelhantes sobre os usuários que estão sendo distribuídos globalmente.

![Diagrama mostrando que os usuários estão distribuídos globalmente, mas o proxy, o conector e o aplicativo estão nos EUA](./media/application-proxy-network-topologies/application-proxy-pattern2.png)

### <a name="use-case-3"></a>Caso de uso 3

**Cenário:** o aplicativo está na rede da organização nos EUA. Há a ExpressRoute com emparelhamento público entre o Azure e a rede corporativa.

**Recomendação:** siga os padrões 1 e 2, explicados na seção anterior.

Primeiro, coloque o conector mais próximo possível do aplicativo. O sistema usará automaticamente o ExpressRoute para o salto 2. 

Se o link de ExpressRoute estiver usando o emparelhamento público, o tráfego entre o proxy e o conector flui por esse link. O salto 2 tem latência otimizada.

![Diagrama mostrando o ExpressRoute entre o proxy e o conector](./media/application-proxy-network-topologies/application-proxy-pattern3.png)

### <a name="use-case-4"></a>Caso de uso 4

**Cenário:** o aplicativo está na rede da organização nos EUA. Há a ExpressRoute com emparelhamento privado entre o Azure e a rede corporativa.

**Recomendação:** siga o padrão 3, explicado na seção anterior.

Coloque o conector no data center do Azure que está conectado à rede corporativa por meio de emparelhamento privado do ExpressRoute. 

O conector pode ser colocado no datacenter do Azure. Como o conector ainda tem uma linha de visão para o aplicativo e o data center por meio da rede privada, o salto 3 permanece otimizado. Além disso, o salto 2 é ainda mais otimizado.

![Diagrama mostrando o conector em um datacenter do Azure e o ExpressRoute entre o conector e o aplicativo](./media/application-proxy-network-topologies/application-proxy-pattern4.png)

### <a name="use-case-5"></a>Caso de uso 5

**Cenário:** o aplicativo está na rede da organização na UE, com a instância do Proxy de Aplicativo e a maioria dos usuários nos Estados Unidos.

**Recomendação:** coloque o conector próximo ao aplicativo. Como os usuários dos Estados Unidos estão acessando uma instância do Proxy de Aplicativo que está na mesma região, o salto 1 não é muito caro. O salto 3 está otimizado. Considere a possibilidade de usar o ExpressRoute para otimizar o salto 2. 

![Diagrama mostrando o proxy e usuários nos EUA, com o conector e o aplicativo na UE](./media/application-proxy-network-topologies/application-proxy-pattern5b.png)

Você também pode considerar o uso de uma outra variante nessa situação. Se a maioria dos usuários na organização estiver nos EUA, provavelmente sua rede se “estende” também para os EUA. Coloque o conector nos EUA e use a linha de rede corporativa interna dedicada para o aplicativo na UE. Desta forma, os saltos 2 e 3 são otimizados.

![Diagrama mostrando os usuários, o proxy e o conector nos EUA e o aplicativo na UE](./media/application-proxy-network-topologies/application-proxy-pattern5c.png)

## <a name="next-steps"></a>Próximas etapas

- [Habilitar Proxy de aplicativo](active-directory-application-proxy-enable.md)
- [Habilitar o logon único](active-directory-application-proxy-sso-using-kcd.md)
- [Habilitar o acesso condicional](active-directory-application-proxy-conditional-access.md)
- [Solucionar problemas que surgirem com o Proxy de Aplicativo](active-directory-application-proxy-troubleshoot.md)
