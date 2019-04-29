---
title: Componentes do gateway de aplicativo
description: Este artigo fornece informações sobre os vários componentes em um gateway de aplicativo
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 02/20/2019
ms.author: absha
ms.openlocfilehash: f5dfa34760bcef23bf54d65b35e3ad8f48cc2ee5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60831820"
---
# <a name="application-gateway-components"></a>Componentes do gateway de aplicativo

 Um gateway de aplicativo serve como o único ponto de contato para clientes. Ele distribui o tráfego de aplicativo em vários pools de back-end, que incluem VMs do Azure, conjuntos de dimensionamento de máquina virtual, serviço de aplicativo do Azure e os servidores no local/externa. Para distribuir o tráfego, um gateway de aplicativo usa vários componentes descritos neste artigo.

![Os componentes usados em um gateway de aplicativo](./media/application-gateway-components/application-gateway-components.png)

## <a name="frontend-ip-addresses"></a>Endereços IP de frontend

Um endereço IP de front-end é o endereço IP associado a um gateway de aplicativo. Você pode configurar um gateway de aplicativo para ter um endereço IP público, um endereço IP privado ou ambos. Um gateway de aplicativo dá suporte a um público ou um endereço IP privado. Sua rede virtual e o endereço IP público devem estar no mesmo local que o gateway de aplicativo. Depois que ele é criado, um endereço IP de front-end é associado um ouvinte.

### <a name="static-versus-dynamic-public-ip-address"></a>Estáticos em comparação com o endereço IP público dinâmico

O SKU do Gateway de aplicativo do Azure v2 oferece suporte a ambos os internos e estáticos endereços IP públicos estáticos, embora a SKU v1 dá suporte a apenas endereços IP internos estáticos. O endereço IP virtual (VIP) pode alterar se um gateway de aplicativo for interrompido e iniciado.

O nome DNS associado com um gateway de aplicativo não muda durante o ciclo de vida do gateway. Como resultado, você deve usar um alias de CNAME e apontá-lo para o endereço DNS do gateway de aplicativo.

## <a name="listeners"></a>Ouvintes

Um ouvinte é uma entidade lógica que verifica se há solicitações de conexão de entrada. Um ouvinte que aceita uma solicitação se o protocolo, porta, host e endereço IP associado à solicitação correspondem os mesmos elementos associados com a configuração de ouvinte.

Antes de usar um gateway de aplicativo, você deve adicionar pelo menos um ouvinte. Pode haver vários ouvintes anexados a um gateway de aplicativo, e eles podem ser usados para o mesmo protocolo.

Depois que um ouvinte detecta solicitações de entrada dos clientes, o gateway de aplicativo encaminha essas solicitações para os membros no pool de back-end. O gateway de aplicativo usa as regras de roteamento de solicitação definidas para o ouvinte que recebeu a solicitação de entrada.

Ouvintes de suportam as seguintes portas e protocolos.

### <a name="ports"></a>Portas

Uma porta é onde um ouvinte ouve a solicitação do cliente. Você pode configurar portas, variando de 1 a 65502 para a SKU v1 e 1 para 65199 para a SKU de v2.

### <a name="protocols"></a>Protocolos

O Gateway de aplicativo dá suporte a quatro protocolos: HTTP, HTTPS, HTTP/2 e WebSocket:

- Especifica entre HTTP e HTTPS protocolos na configuração de ouvinte.
- Suporte para [WebSockets e HTTP/2 protocolos](https://docs.microsoft.com/azure/application-gateway/overview#websocket-and-http2-traffic) é fornecido de modo nativo, e [suporte ao WebSocket](https://docs.microsoft.com/azure/application-gateway/application-gateway-websocket) é habilitado por padrão. Não há nenhuma configuração configurável pelo usuário para habilitar ou desabilitar seletivamente o suporte ao WebSocket. Use o WebSockets com ouvintes HTTP e HTTPS.
- O suporte ao protocolo HTTP/2 está disponível para os clientes que se conectam apenas os ouvintes do Gateway de Aplicativo. A comunicação para pools de servidores back-end é sobre HTTP/1.1. Por padrão, o suporte HTTP/2 está desabilitado. Você pode optar por habilitá-lo.

Use um ouvinte HTTPS para a terminação SSL. Um ouvinte HTTPS descarrega o trabalho de criptografia e descriptografia para seu gateway de aplicativo, de modo seus servidores web não são carregados pela sobrecarga. Seus aplicativos, em seguida, são livres para se concentrar na lógica de negócios.

### <a name="custom-error-pages"></a>Páginas de erro personalizadas

O Gateway de aplicativo permite criar páginas de erro personalizadas em vez de exibir páginas de erro padrão. Você pode usar sua própria identidade visual e layout em uma página de erro personalizada. O Gateway de aplicativo exibe uma página de erro personalizada quando uma solicitação não é possível acessar o back-end.

Para obter mais informações, consulte [páginas de erro personalizado para seu gateway de aplicativo](https://docs.microsoft.com/azure/application-gateway/custom-error).

### <a name="types-of-listeners"></a>Tipos de ouvintes

Há dois tipos de ouvintes:

- **Básica**. Esse tipo de ouvinte escuta em um site de domínio único, em que ele tem um único mapeamento de DNS para o endereço IP do gateway de aplicativo. Essa configuração de ouvinte é necessária quando você hospeda um único site por trás de um gateway de aplicativo.

- **Multissite**. Essa configuração de ouvinte é necessária quando você configurar mais de um aplicativo web na mesma instância de gateway de aplicativo. Ele permite que você configure a topologia mais eficiente para suas implantações adicionando até 100 sites da Web para um gateway de aplicativo. Cada site pode ser direcionado para seu próprio pool de back-end. Por exemplo, três subdomínios, abc.contoso.com, xyz.contoso.com e pqr.contoso.com, apontam para o endereço IP do gateway de aplicativo. Crie três ouvintes multissites e configurar cada ouvinte para a configuração de protocolo e porta respectiva.

    Para obter mais informações, consulte [hospedagem de vários sites](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-app-overview).

Depois de criar um ouvinte, você pode associá-lo com uma regra de roteamento de solicitação. Esta regra determina como a solicitação recebida no ouvinte deve ser roteada para o back-end.

O Gateway de aplicativo processa ouvintes na ordem mostrada. Se o ouvinte básico corresponder a uma solicitação de entrada, ela é processada primeiro. Para rotear o tráfego para o back-end corretos, configure um ouvinte multissite antes de um ouvinte básico.

## <a name="request-routing-rules"></a>As regras de roteamento de solicitação

Uma regra de roteamento de solicitação é um componente fundamental de um gateway de aplicativo, pois ela determina como rotear o tráfego no ouvinte. A regra vincula o ouvinte, pool de servidores de back-end e as configurações de HTTP de back-end.

Quando um ouvinte aceita uma solicitação, a regra de roteamento de solicitação encaminha a solicitação para o back-end ou redireciona-o em outro lugar. Se a solicitação é encaminhada para o back-end, a regra de roteamento de solicitação define qual pool de servidores de back-end para encaminhar para. Além disso, a regra de roteamento de solicitação também determina se os cabeçalhos na solicitação devem ser reescrito. Um ouvinte pode ser anexado a uma regra.

Há dois tipos de regras de roteamento de solicitação:

- **Básica**. Todas as solicitações no ouvinte associado (por exemplo, blog.contoso.com/*) são encaminhadas para o pool de back-end associados usando a configuração de HTTP associada.

- **Com base em caminho**. Esta regra de roteamento permite rotear as solicitações no ouvinte associado a um pool de back-end específico, com base na URL na solicitação. Se o caminho da URL em uma solicitação corresponde ao padrão de caminho em uma regra com base em caminho, a regra encaminha essa solicitação. Ele aplica o padrão de caminho somente para o caminho da URL, não para seus parâmetros de consulta. Se o caminho da URL em uma solicitação de ouvinte não corresponder a qualquer uma das regras com base em caminho, ele encaminha a solicitação para o pool de back-end padrão e configurações de HTTP.

Para obter mais informações, consulte [roteamento baseado em URL](https://docs.microsoft.com/azure/application-gateway/url-route-overview).

### <a name="redirection-support"></a>Suporte para redirecionamento

A regra de roteamento de solicitação também permite que você redirecione o tráfego no gateway de aplicativo. Este é um mecanismo de redirecionamento genérico, portanto, você pode redirecionar para e de qualquer porta que você define usando regras.

Você pode escolher o destino de redirecionamento a ser outro ouvinte (que pode ajudar a habilitar automático de HTTP para redirecionamento a HTTPS) ou um site externo. Você também pode optar por um redirecionamento a ser temporário ou permanente ou acrescentar a cadeia de caracteres de consulta e o caminho URI à URL redirecionada.

Para obter mais informações, consulte [redirecionar o tráfego em seu gateway de aplicativo](https://docs.microsoft.com/azure/application-gateway/redirect-overview).

### <a name="rewrite-http-headers"></a>Reescrever cabeçalhos HTTP

Usando as regras de roteamento de solicitação, você pode adicionar, remover ou atualizar a solicitação de HTTP (S) e cabeçalhos de resposta como os pacotes de solicitação e resposta mover entre o cliente e o back-end pools por meio do gateway de aplicativo.

Os cabeçalhos podem ser definidos como valores estáticos ou a outros cabeçalhos e as variáveis de servidor. Isso ajuda com importantes casos de uso, como extração de endereços IP do cliente, removendo informações confidenciais sobre o back-end, adicionando mais segurança e assim por diante.

Para obter mais informações, consulte [cabeçalhos de HTTP de reescrita em seu gateway de aplicativo](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers).

## <a name="http-settings"></a>Configurações de HTTP

Um gateway de aplicativo roteia o tráfego para os servidores de back-end (especificada na regra de roteamento de solicitação que incluem configurações de HTTP) usando o número da porta, protocolo e outras configurações detalhadas neste componente.

A porta e protocolo usado nas configurações de HTTP determinam se o tráfego entre os servidores de gateway e o back-end do aplicativo está criptografado (fornecendo a SSL de ponta a ponta) ou sem criptografia.

Esse componente também é usado para:

- Determinar se uma sessão de usuário deve ser mantido no mesmo servidor usando o [afinidade de sessão baseada em cookie](https://docs.microsoft.com/azure/application-gateway/overview#session-affinity).

- Normalmente, remover membros do pool de back-end usando [drenagem de conexão](https://docs.microsoft.com/azure/application-gateway/overview#connection-draining).

- Associe uma investigação personalizada para monitorar a integridade de back-end, defina o intervalo de tempo limite de solicitação, substituir o nome de host e o caminho na solicitação e fornecer a facilidade de um clique para especificar configurações para o back-end do serviço de aplicativo.

## <a name="backend-pools"></a>Pools de back-end

Um pool de back-end encaminha a solicitação para servidores de back-end, que irá atender à solicitação. Pools de back-end podem conter:

- NICs
- conjuntos de escala de máquina virtual
- Endereços IP públicos
- Endereços IP internos
- FQDN
- Back-ends multilocatário (como o serviço de aplicativo)

Membros do pool de back-end de Gateway de aplicativo não estão vinculados a um conjunto de disponibilidade. Um gateway de aplicativo pode se comunicar com instâncias fora da rede virtual que está. Como resultado, os membros dos pools de back-end podem ser em clusters, entre data centers ou fora do Azure, desde que exista conectividade IP.

Se você usar IPs internos como membros do pool de back-end, você deve usar [emparelhamento de rede virtual](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) ou um [gateway de VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Emparelhamento de rede virtual é suportado e útil para tráfego de balanceamento de carga em outras redes virtuais.

Um gateway de aplicativo também pode comunicar com servidores locais quando eles estão conectados por túneis de VPN ou ExpressRoute do Azure se o tráfego é permitido.

Você pode criar pools de back-end diferente para diferentes tipos de solicitações. Por exemplo, crie um pool de back-end para solicitações gerais e, em seguida, outro pool de back-end para solicitações para os microsserviços para seu aplicativo.

## <a name="health-probes"></a>Investigações de integridade

Por padrão, um gateway de aplicativo monitora a integridade de todos os recursos em seu pool de back-end e remove automaticamente aqueles não íntegro. Em seguida, ele monitora as instâncias não íntegras e adiciona de volta ao pool de back-end Íntegro quando eles se tornarem disponíveis e respondem a investigações de integridade.

Além de usar o monitoramento da investigação de integridade padrão, você também pode personalizar a investigação de integridade para atender às necessidades do seu aplicativo. Investigações personalizadas permitem que o controle mais granular sobre o monitoramento de integridade. Ao usar investigações personalizadas, você pode configurar o intervalo de investigação, a URL e caminho a testar e quantas respostas com falha devem aceitar antes da instância do pool de back-end é marcada como não íntegra. É recomendável que você configure as investigações personalizadas para monitorar a integridade de cada pool de back-end.

Para obter mais informações, consulte [monitorar a integridade do seu gateway de aplicativo](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview).

## <a name="next-steps"></a>Próximas etapas

Criar um Application Gateway:

* No [portal do Azure](quick-create-portal.md)
* [Usando o PowerShell do Azure](quick-create-powershell.md)
* [Usando a CLI do Azure](quick-create-cli.md)
