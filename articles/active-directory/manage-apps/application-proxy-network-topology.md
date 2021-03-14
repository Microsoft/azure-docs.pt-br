---
title: Considerações sobre topologia de rede para Proxy de Aplicativo do Azure Active Directory
description: Aborda as considerações de topologia de rede ao usar o Proxy de Aplicativo do Azure Active Directory.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: a8f1f902b14dda7d95e3643c335e82ed7c7e5a3f
ms.sourcegitcommit: ec39209c5cbef28ade0badfffe59665631611199
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2021
ms.locfileid: "103232912"
---
# <a name="optimize-traffic-flow-with-azure-active-directory-application-proxy"></a>Otimizar o fluxo de tráfego com Proxy de Aplicativo do Azure Active Directory

Este artigo explica como otimizar o fluxo de tráfego e as considerações de topologia de rede ao usar o proxy de aplicativo Azure Active Directory (AD do Azure) para publicar e acessar seus aplicativos remotamente.

## <a name="traffic-flow"></a>Fluxo de tráfego

Quando um aplicativo é publicado por meio do Proxy de Aplicativo do Azure AD, o tráfego dos usuários para os aplicativos flui por meio de três conexões:

1. O usuário se conecta ao ponto de extremidade público do serviço Proxy de Aplicativo do Azure AD no Azure
1. O serviço Proxy de Aplicativo se conecta ao conector de Proxy de Aplicativo
1. O conector de Proxy de Aplicativo se conecta ao aplicativo de destino

:::image type="content" source="./media/application-proxy-network-topology/application-proxy-three-hops.png" alt-text="Diagrama mostrando o fluxo de tráfego do usuário para o aplicativo de destino." lightbox="./media/application-proxy-network-topology/application-proxy-three-hops.png":::

## <a name="optimize-connector-groups-to-use-closest-application-proxy-cloud-service-preview"></a>Otimizar grupos de conectores para usar o serviço de nuvem de proxy de aplicativo mais próximo (versão prévia)

Quando você se inscreve em um locatário do Azure AD, a região do seu locatário é determinada pelo país/região que você especificar. Quando você habilita o proxy de aplicativo, as instâncias do serviço de nuvem do proxy de aplicativo **padrão** para seu locatário são escolhidas na mesma região que o seu locatário do Azure ad ou na região mais próxima a ela.

Por exemplo, se o país ou a região do seu locatário do Azure AD for o Reino Unido, todos os conectores de proxy de aplicativo em **padrão** serão atribuídos para usar instâncias de serviço em data centers europeus. Quando os usuários acessam aplicativos publicados, seu tráfego passa pelas instâncias do serviço de nuvem do proxy de aplicativo neste local.

Se você tiver conectores instalados em regiões diferentes da sua região padrão, pode ser benéfico alterar em qual região o grupo de conectores é otimizado para melhorar o desempenho do acesso a esses aplicativos. Depois que uma região for especificada para um grupo de conectores, ela será conectada aos serviços de nuvem do proxy de aplicativo na região designada.

Para otimizar o fluxo de tráfego e reduzir a latência para um grupo de conectores, atribua o grupo de conectores à região mais próxima. Para atribuir uma região:

> [!IMPORTANT]
> Os conectores devem usar pelo menos a versão 1.5.1975.0 para usar esse recurso.

1. Entre no [portal do Azure](https://portal.azure.com/) como um administrador de aplicativos do diretório que usa o Proxy de Aplicativo. Por exemplo, se o domínio de locatário for contoso.com, o administrador deverá ser admin@contoso.com ou qualquer outro alias de administrador nesse domínio.
1. Escolha o nome de usuário no canto superior direito. Verifique se você está conectado a um diretório que usa o Proxy de Aplicativo. Se for necessário alterar diretórios, escolha **Mudar diretório** e escolha um diretório que usa o Proxy de Aplicativo.
1. No painel de navegação à esquerda, escolha **Azure Active Directory**.
1. Em **Gerenciar**, escolha **Proxy de Aplicativo**.
1. Selecione **novo grupo de conectores**, forneça um **nome** para o grupo de conectores.
1. Em seguida, em **Configurações avançadas** e selecione a lista suspensa em otimizar para uma região específica e selecione a região mais próxima aos conectores.
1. Selecione **Criar**.
    
    :::image type="content" source="./media/application-proxy-network-topology/geo-routing.png" alt-text="Configure um novo grupo de conectores." lightbox="./media/application-proxy-network-topology/geo-routing.png":::

1. Depois que o novo grupo de conectores for criado, você poderá selecionar quais conectores atribuir a esse grupo de conectores. 
   - Você só poderá mover os conectores para o grupo de conector se ele estiver em um grupo de conectores usando a região padrão. A melhor abordagem é sempre começar com os conectores colocados no "grupo padrão" e, em seguida, movê-lo para o grupo de conectores apropriado.
   - Você só poderá alterar a região de um grupo de conectores se **não houver conectores** atribuídos a ele ou aplicativos atribuídos a ele.
1. Em seguida, atribua o grupo de conectores aos seus aplicativos. Ao acessar os aplicativos, o tráfego agora deve ir para o serviço de nuvem do proxy de aplicativo na região em que o grupo de conectores é otimizado.

## <a name="considerations-for-reducing-latency"></a>Considerações para redução da latência

Todas as soluções de proxy introduzem a latência em sua conexão de rede. Não importa a solução de proxy ou VPN escolhida como sua solução de acesso remoto, ela sempre incluirá um conjunto de servidores, permitindo a conexão dentro de sua rede corporativa.

As organizações geralmente incluem pontos de extremidade do servidor em sua rede de perímetro. No entanto, com o Proxy de Aplicativo do Azure AD, o tráfego flui por meio do serviço de proxy na nuvem, enquanto os conectores residem em sua rede corporativa. Nenhuma rede de perímetro é necessária.

As seções a seguir contêm sugestões adicionais para ajudá-lo a reduzir a latência ainda mais. 

### <a name="connector-placement"></a>Posicionamento do conector

O serviço Proxy de Aplicativo escolhe o local das instâncias para você, com base no local de seu locatário. Entretanto, você pode decidir onde instalar o conector, permitindo que você defina as características de latência de seu tráfego de rede.

Ao configurar o serviço de Proxy de Aplicativo, faça as seguintes perguntas:

- Onde o aplicativo está localizado?
- Onde está localizada a maioria dos usuários que acessam o aplicativo?
- Onde a instância do Proxy de Aplicativo está localizada?
- Você já tem uma conexão de rede dedicada com os data centers configurados do Azure, por exemplo, o Azure ExpressRoute ou uma VPN semelhante?

O conector tem de se comunicar com o Azure e seus aplicativos (as etapas 2 e 3 no diagrama de fluxo Tráfego), de modo que o posicionamento do conector afeta a latência dessas duas conexões. Ao avaliar o posicionamento do conector, tenha em mente os seguintes pontos:

- Se você quiser usar a delegação restrita de Kerberos (KCD) para logon único, o conector precisará de uma linha de visão para um datacenter. Além disso, o servidor do conector deve ser ingressado no domínio.  
- Em caso de dúvida, instale o conector mais próximo ao aplicativo.

### <a name="general-approach-to-minimize-latency"></a>Abordagem geral para minimizar a latência

Você pode minimizar a latência do tráfego de ponta a ponta otimizando cada conexão de rede. Cada conexão pode ser otimizada com:

- Reduzindo a distância entre as duas extremidades do salto.
- Escolhendo a rede certa para percorrer. Por exemplo, percorrer uma rede privada, em vez da Internet pública, pode ser mais rápido devido aos links dedicados.

Se você tiver um link dedicado de VPN ou do ExpressRoute entre o Azure e sua rede corporativa, convém usá-lo.

## <a name="focus-your-optimization-strategy"></a>Concentrar-se em sua estratégia de otimização

Não há muito que você pode fazer para controlar a conexão entre os usuários e o serviço Proxy de Aplicativo. Os usuários podem acessar seus aplicativos de uma rede doméstica, uma cafeteria ou um país/região diferente. Em vez disso, você pode otimizar as conexões do serviço Proxy de Aplicativo para os conectores de Proxy de Aplicativo para os aplicativos. Considere a possibilidade de incorporar os padrões a seguir em seu ambiente.

### <a name="pattern-1-put-the-connector-close-to-the-application"></a>Padrão 1: colocar o conector perto do aplicativo

Coloque o conector perto do aplicativo de destino na rede do cliente. Essa configuração minimiza a etapa 3 no diagrama de topografia porque o conector e o aplicativo estão próximos.

Se seu conector precisar de uma linha de visão para o controlador de domínio, então esse padrão será vantajoso. A maioria de nossos clientes usa esse padrão, porque ele funciona bem para a maioria dos cenários. Esse padrão também pode ser combinado com o padrão 2 para otimizar o tráfego entre o serviço e o conector.

### <a name="pattern-2-take-advantage-of-expressroute-with-microsoft-peering"></a>Padrão 2: aproveitar o ExpressRoute com o emparelhamento da Microsoft

Se tiver configurado o ExpressRoute com emparelhamento da Microsoft, você poderá usar a conexão do ExpressRoute mais rápida para o tráfego entre o Proxy de Aplicativo e o conector. O conector ainda está em sua rede, perto do aplicativo.

### <a name="pattern-3-take-advantage-of-expressroute-with-private-peering"></a>Padrão 3: Aproveitar o ExpressRoute com o emparelhamento privado

Se você tiver uma configuração de VPN ou ExpressRoute dedicada com emparelhamento privado entre o Azure e sua rede corporativa, terá outra opção. Nessa configuração, a rede virtual no Azure é geralmente considerada uma extensão da rede corporativa. Portanto, você pode instalar o conector no data center do Azure e ainda atender aos requisitos de baixa latência da conexão entre o aplicativo e o conector.

A latência não é comprometida, pois o tráfego está fluindo por uma conexão dedicada. Você também obtém uma menor latência de serviço para o conector do Proxy de Aplicativo porque o conector está instalado em um datacenter do Azure perto da localização do locatário do Azure AD.

:::image type="content" source="./media/application-proxy-network-topology/application-proxy-expressroute-private.png" alt-text="Diagrama mostrando o conector instalado em um datacenter do Azure" lightbox="./media/application-proxy-network-topology/application-proxy-expressroute-private.png":::

### <a name="other-approaches"></a>Outras abordagens

Embora o foco deste artigo é a instalação, você também pode alterar o posicionamento do aplicativo para obter os melhores características de latência.

As organizações estão cada vez mais migrando suas redes para ambientes hospedados. Isso permite colocar os aplicativos em um ambiente hospedado que também faz parte da rede corporativa, e ainda ser dentro do domínio. Nesse caso, os padrões discutidos nas seções anteriores podem ser aplicados para o novo local do aplicativo. Se você estiver considerando essa opção, consulte [Serviços de Domínio do Azure AD](../../active-directory-domain-services/overview.md).

Além disso, considere a possibilidade de organizar seus conectores usando [grupos de conectores](application-proxy-connector-groups.md) para ter como destino aplicativos que estejam em locais e redes diferentes.

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

:::image type="content" source="./media/application-proxy-network-topology/application-proxy-pattern1.png" alt-text="O diagrama que mostra os usuários, proxy, conector e aplicativo estão todos nos EUA." lightbox="./media/application-proxy-network-topology/application-proxy-pattern1.png":::

### <a name="use-case-2"></a>Caso de uso 2

**Cenário:** o aplicativo está na rede da organização nos EUA, com usuários distribuídos globalmente. Não há um ExpressRoute ou uma VPN entre o data center do Azure e a rede corporativa.

**Recomendação:** siga o padrão 1, explicado na seção anterior.

Novamente, o padrão mais comum é otimizar o salto 3, onde você coloca o conector perto do aplicativo. O salto 3 não costuma ser caro, se tudo estiver dentro da mesma região. No entanto, o salto 1 poderá ser mais caro dependendo de onde o usuário estiver, pois os usuários pelo mundo precisarão acessar a instância do Proxy de Aplicativo nos EUA. Vale a pena observar que qualquer solução de proxy tem características semelhantes sobre os usuários que estão sendo distribuídos globalmente.

:::image type="content" source="./media/application-proxy-network-topology/application-proxy-pattern2.png" alt-text="Os usuários são distribuídos globalmente, mas todo o restante está nos EUA" lightbox="./media/application-proxy-network-topology/application-proxy-pattern2.png":::

### <a name="use-case-3"></a>Caso de uso 3

**Cenário:** o aplicativo está na rede da organização nos EUA. Há a ExpressRoute com emparelhamento da Microsoft entre o Azure e a rede corporativa.

**Recomendação:** siga os padrões 1 e 2, explicados na seção anterior.

Primeiro, coloque o conector mais próximo possível do aplicativo. O sistema usará automaticamente o ExpressRoute para o salto 2.

Se o link de ExpressRoute estiver usando o emparelhamento da Microsoft, o tráfego entre o proxy e o conector fluirá por esse link. O salto 2 tem latência otimizada.

:::image type="content" source="./media/application-proxy-network-topology/application-proxy-pattern3.png" alt-text="Diagrama mostrando o ExpressRoute entre o proxy e o conector" lightbox="./media/application-proxy-network-topology/application-proxy-pattern3.png":::

### <a name="use-case-4"></a>Caso de uso 4

**Cenário:** o aplicativo está na rede da organização nos EUA. Há a ExpressRoute com emparelhamento privado entre o Azure e a rede corporativa.

**Recomendação:** siga o padrão 3, explicado na seção anterior.

Coloque o conector no data center do Azure que está conectado à rede corporativa por meio de emparelhamento privado do ExpressRoute.

O conector pode ser colocado no datacenter do Azure. Como o conector ainda tem uma linha de visão para o aplicativo e o data center por meio da rede privada, o salto 3 permanece otimizado. Além disso, o salto 2 é ainda mais otimizado.

:::image type="content" source="./media/application-proxy-network-topology/application-proxy-pattern4.png" alt-text="Conector no datacenter do Azure, ExpressRoute entre o conector e o aplicativo" lightbox="./media/application-proxy-network-topology/application-proxy-pattern4.png":::

### <a name="use-case-5"></a>Caso de uso 5

**Cenário:** O aplicativo está na rede de uma organização na Europa, a região de locatário padrão é US, com a maioria dos usuários na Europa.

**Recomendação:** coloque o conector próximo ao aplicativo. Atualize o grupo de conectores para que ele seja otimizado para usar instâncias de serviço de proxy de aplicativo Europe. Para ver as etapas, [Otimize os grupos de conectores para usar o serviço de nuvem de proxy de aplicativo mais próximo](application-proxy-network-topology#Optimize connector-groups-to-use-closest-Application-Proxy-cloud-service).

Como os usuários da Europa estão acessando uma instância de proxy de aplicativo que ocorre na mesma região, o salto 1 não é caro. O salto 3 está otimizado. Considere a possibilidade de usar o ExpressRoute para otimizar o salto 2.

### <a name="use-case-6"></a>Caso de uso 6

**Cenário:** O aplicativo está na rede de uma organização na Europa, a região de locatário padrão é US, com a maioria dos usuários nos EUA.

**Recomendação:** coloque o conector próximo ao aplicativo. Atualize o grupo de conectores para que ele seja otimizado para usar instâncias de serviço de proxy de aplicativo Europe. Para ver as etapas, [Otimize os grupos de conectores para usar o serviço de nuvem de proxy de aplicativo mais próximo](/application-proxy-network-topology#Optimize connector-groups-to-use-closest-Application-Proxy-cloud-service). O salto 1 pode ser mais caro, pois todos os usuários dos EUA devem acessar a instância do proxy de aplicativo na Europa.

Você também pode considerar o uso de uma outra variante nessa situação. Se a maioria dos usuários na organização estiver nos EUA, provavelmente sua rede se “estende” também para os EUA. Coloque o conector nos EUA, continue a usar a região padrão dos EUA para seus grupos de conectores e use a linha de rede corporativa interna dedicada para o aplicativo na Europa. Desta forma, os saltos 2 e 3 são otimizados.

:::image type="content" source="./media/application-proxy-network-topology/application-proxy-pattern5c.png" alt-text="O diagrama mostra os usuários, o proxy e o conector nos EUA, o aplicativo na Europa." lightbox="./media/application-proxy-network-topology/application-proxy-pattern5c.png":::

## <a name="next-steps"></a>Próximas etapas

- [Habilitar Proxy de aplicativo](application-proxy-add-on-premises-application.md)
- [Habilitar o logon único](application-proxy-configure-single-sign-on-with-kcd.md)
- [Habilitar o Acesso Condicional](application-proxy-integrate-with-sharepoint-server.md)
- [Solucionar problemas que surgirem com o Proxy de Aplicativo](application-proxy-troubleshoot.md)
