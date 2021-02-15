---
title: Recursos de gateway de Aplicativo Azure
description: Saiba mais sobre os recursos de gateway Aplicativo Azure
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 09/25/2020
ms.author: victorh
ms.openlocfilehash: ba9f42bc932a37e1052f17db2ae00413e0769d59
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91355730"
---
# <a name="azure-application-gateway-features"></a>Recursos de gateway de Aplicativo Azure

O [Gateway de Aplicativo do Azure](overview.md) é um balanceador de carga do tráfego da Web que permite que você gerencie o tráfego para seus aplicativos Web.

![Gateway de Aplicativo conceitual](media/overview/figure1-720.png)

O gateway de aplicativo inclui os seguintes recursos:

- [Terminação do protocolo SSL/TLS (Secure Sockets Layer)](#secure-sockets-layer-ssltls-termination)
- [Dimensionamento automático](#autoscaling)
- [Redundância de zona](#zone-redundancy)
- [VIP estático](#static-vip)
- [Firewall do Aplicativo Web](#web-application-firewall)
- [Controlador de entrada para AKS](#ingress-controller-for-aks)
- [Roteamento baseado em URL](#url-based-routing)
- [Hospedagem de vários sites](#multiple-site-hosting)
- [Redirecionamento](#redirection)
- [Afinidade de sessão](#session-affinity)
- [Tráfego do WebSocket e HTTP/2](#websocket-and-http2-traffic)
- [Descarregamento de conexão](#connection-draining)
- [Páginas de erro personalizadas](#custom-error-pages)
- [Reescrever cabeçalhos HTTP e URL](#rewrite-http-headers-and-url)
- [Dimensionamento](#sizing)

## <a name="secure-sockets-layer-ssltls-termination"></a>Terminação do protocolo SSL/TLS (Secure Sockets Layer)

O gateway de aplicativo dá suporte a terminação SSL/TLS no gateway, pelo qual o tráfego flui geralmente descriptografado até os servidores de back-end. Esse recurso permite que os servidores Web fiquem livres da sobrecarga da criptografia e descriptografia dispendiosa. Mas, às vezes, a comunicação não criptografada com os servidores não é uma opção aceitável. Isso pode ocorrer devido a requisitos de segurança, de conformidade ou o aplicativo pode aceitar apenas uma conexão segura. Para tais aplicativos, o Gateway de Aplicativo dá suporte à criptografia SSL/TLS de ponta a ponta.

Para obter mais informações, consulte [visão geral da terminação SSL e SSL de ponta a ponta com o gateway de aplicativo](ssl-overview.md)

## <a name="autoscaling"></a>Dimensionamento automático

O Standard_v2 de gateway de aplicativo dá suporte ao dimensionamento automático e pode escalar ou reduzir verticalmente com base na alteração dos padrões de carga de tráfego. O escalonamento automático também remove o requisito de escolher um tamanho de implantação ou contagem de instâncias durante o provisionamento. 

Para obter mais informações sobre os recursos de Standard_v2 do gateway de aplicativo, consulte [SKU de autoescala v2](application-gateway-autoscaling-zone-redundant.md).

## <a name="zone-redundancy"></a>Redundância de zona

Um Standard_v2 gateway de aplicativo pode abranger vários Zonas de Disponibilidade, oferecendo melhor resiliência de falha e removendo a necessidade de provisionar gateways de aplicativo separados em cada zona.

## <a name="static-vip"></a>VIP estático

O SKU do gateway de aplicativo Standard_v2 oferece suporte exclusivamente ao tipo de VIP estático. Isso garante que o VIP associado ao gateway de aplicativo não seja alterado mesmo durante o tempo de vida do Gateway de Aplicativo.

## <a name="web-application-firewall"></a>Firewall do Aplicativo Web

O WAF (firewall do aplicativo Web) é um serviço que fornece proteção centralizada de seus aplicativos Web contra explorações e vulnerabilidades comuns. O WAF é baseado em regras dos [conjuntos de regras de núcleo do OWASP (Open Web Application Security Project)](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3.1 (somente WAF_v2), 3.0 e 2.2.9. 

Os aplicativos Web cada vez mais são alvos de ataques mal-intencionados que exploram vulnerabilidades conhecidas comuns. Os ataques de injeção de SQL, os ataques de scripts entre sites, entre outros, são comuns entre essas explorações. Pode ser difícil impedir esses ataques no código do aplicativo e isso pode exigir manutenção, aplicação de patches e monitoramento rigorosos em muitas camadas da topologia do aplicativo. Um firewall de aplicativo Web centralizado ajuda a simplificar bastante o gerenciamento de segurança e oferece mais garantia ao administrador do aplicativo contra ameaças ou invasões. Uma solução WAF também pode reagir a uma ameaça de segurança mais rapidamente ao aplicar um patch contra uma vulnerabilidade conhecida em um local central do que a proteção de cada um dos aplicativos Web individuais. Os gateways de aplicativo existentes podem ser convertidos em um gateway de aplicativo habilitado para firewall de aplicativo Web facilmente.

Para saber mais, confira [O que é o Firewall do Aplicativo Web do Azure?](../web-application-firewall/overview.md).

## <a name="ingress-controller-for-aks"></a>Controlador de entrada para AKS
O Controlador de entrada do Gateway de Aplicativo (AGIC) permite usar o gateway de aplicativo como entrada para um cluster do [Serviço do Kubernetes do Azure (AKS)](https://azure.microsoft.com/services/kubernetes-service/). 

O controlador de entrada é executado como um pod no cluster AKS e consome [recursos de entrada do kubernetes](https://kubernetes.io/docs/concepts/services-networking/ingress/) e os converte em uma configuração de gateway de aplicativo, o que permite ao gateway balancear a carga do tráfego para o pods kubernetes. O controlador de entrada só dá suporte a Standard_v2 de gateway de aplicativo e SKUs de WAF_v2. 

Para saber mais, consulte [Controlador de entrada do Gateway de Aplicativo (AGIC)](ingress-controller-overview.md).

## <a name="url-based-routing"></a>Roteamento baseado em URL

O Roteamento Baseado em Caminho de URL permite rotear o tráfego para pools do servidor de back-end com base nos Caminhos de URL da solicitação. Um dos cenários possíveis é encaminhar as solicitações de diferentes tipos de conteúdo para diferentes pools.

Por exemplo, as solicitações de `http://contoso.com/video/*` são encaminhadas para VideoServerPool, e as de `http://contoso.com/images/*` são encaminhadas para ImageServerPool. O DefaultServerPool será selecionado se nenhum dos padrões de caminho forem compatíveis.

Para obter mais informações, consulte [visão geral de roteamento baseado em caminho de URL](url-route-overview.md).

## <a name="multiple-site-hosting"></a>Hospedagem de vários sites

Com o gateway de aplicativo, você pode configurar o roteamento com base no nome do host ou nome de domínio para mais de um aplicativo Web no mesmo gateway de aplicativo. Permite que você configure a topologia mais eficiente para suas implantações adicionando até mais de 100 sites a um gateway de aplicativo. Cada site pode ser direcionado para seu próprio pool de back-end. Por exemplo, três domínios, contoso.com, fabrikam.com e adatum.com, apontam para o endereço IP do gateway de aplicativo. Você criaria três ouvintes com vários sites e configuraria cada ouvinte para as respectivas configurações de porta e protocolo. 

As solicitações de `http://contoso.com` são roteadas para ContosoServerPool, `http://fabrikam.com` são roteadas para FabrikamServerPool e assim por diante.

Da mesma forma, dois subdomínios do mesmo domínio pai podem ser hospedados na mesma implantação de gateway de aplicativo. Exemplos de uso de subdomínios podem incluir `http://blog.contoso.com` e `http://app.contoso.com` hospedados em uma implantação de gateway de aplicativo único. Para obter mais informações, consulte [Hospedagem de vários sites do gateway de aplicativo](multiple-site-overview.md).

Você também pode definir nomes do host curinga em um ouvinte com vários sites e até cinco nomes do host por ouvinte. Para saber mais, confira [nomes de host curinga no ouvinte (versão prévia)](multiple-site-overview.md#wildcard-host-names-in-listener-preview).

## <a name="redirection"></a>Redirecionamento

Um cenário comum para muitos aplicativos Web é dar suporte ao redirecionamento automático de HTTP para HTTPS para garantir que toda a comunicação entre o aplicativo e seus usuários ocorra em um caminho criptografado.

No passado, talvez você tenha usado técnicas como a criação de um pool dedicado cujo único propósito é redirecionar as solicitações recebidas em HTTP para HTTPS. O gateway de aplicativo dá suporte à capacidade de redirecionar o tráfego no Gateway de Aplicativo. Isso simplifica a configuração do aplicativo, otimiza o uso de recursos e dá suporte a novos cenários de redirecionamento, incluindo redirecionamento global e baseado no caminho. O suporte ao redirecionamento do Gateway de Aplicativo não está limitado apenas ao redirecionamento de HTTP para HTTPS. Esse é um mecanismo de redirecionamento genérico; portanto, você pode redirecionar bidirecionalmente em qualquer porta definida usando regras. Ele também dá suporte a redirecionamento para um site externo.

O suporte a redirecionamento do Gateway de Aplicativo oferece os seguintes recursos:

- Redirecionamento global de uma porta para outra porta no Gateway. Isso habilita o redirecionamento de HTTP para HTTPS em um site.
- Redirecionamento baseado em caminho. Esse tipo de redirecionamento permite o redirecionamento de HTTP para HTTPS apenas em uma área específica do site, por exemplo, uma área de carrinho de compras indicada por `/cart/*`.
- Redirecionamento para um site externo.

Para obter mais informações, consulte [visão geral do redirecionamento do gateway de aplicativo](redirect-overview.md).

## <a name="session-affinity"></a>Afinidade de sessão

O recurso de afinidade de sessão baseada em cookies é útil quando você deseja manter uma sessão de usuário no mesmo servidor. Usando cookies gerenciados pelo gateway, o Gateway de Aplicativo pode direcionar o tráfego seguinte de uma sessão de usuário para o mesmo servidor para processamento. Isso é importante em casos em que o estado de sessão é salvo localmente no servidor para uma sessão de usuário.

Para obter mais informações, consulte [como funciona um gateway de aplicativo](how-application-gateway-works.md#modifications-to-the-request).

## <a name="websocket-and-http2-traffic"></a>Tráfego do WebSocket e HTTP/2

O Gateway de Aplicativo fornece suporte nativo para os protocolos WebSocket e HTTP/2. Não há nenhuma configuração configurável pelo usuário para habilitar ou desabilitar seletivamente o suporte ao WebSocket.

Os protocolos WebSocket e HTTP/2 permitem uma comunicação full duplex entre um servidor e um cliente em uma conexão TCP de execução longa. Isso permite uma comunicação mais interativa entre o servidor Web e o cliente, que pode ser bidirecional, sem a necessidade de sondagem, necessária nas implementações baseadas em HTTP. Esses protocolos têm baixa sobrecarga, diferentemente de HTTP, e podem reutilizar a mesma conexão TCP para várias solicitações/respostas, resultando em uma utilização de recursos mais eficiente. Esses protocolos foram projetados para funcionar em portas HTTP tradicionais de 80 e 443.

Para obter mais informações, consulte [suporte ao WebSocket](application-gateway-websocket.md) e [suporte ao HTTP/2](configuration-listeners.md#http2-support).

## <a name="connection-draining"></a>Descarregamento de conexão

O descarregamento de conexão ajuda você a efetuar a remoção normal de membros do pool de back-end durante atualizações de serviço planejadas. Essa configuração é habilitada por meio da configuração do http de back-end e pode ser aplicada a todos os membros de um pool de back-end durante a criação da regra. Uma vez habilitado, o gateway de aplicativo garante que todas as instâncias de cancelamento de registro de um pool de back-end não recebam nenhuma nova solicitação, permitindo a conclusão de solicitações existentes dentro de um limite de tempo configurado Isso se aplica a instâncias de back-end removidas explicitamente do pool de back-end por uma alteração de configuração do usuário e a instâncias de back-end relatadas como não íntegras, conforme determinado por investigações de integridade. A única exceção a isso são solicitações associadas para cancelamento de registro de instâncias, que foram desregistradas explicitamente, devido à afinidade de sessão gerenciada por gateway e continuam a ser proxy para as instâncias de cancelamento de registro.

Para obter mais informações, consulte [visão geral de configuração do gateway de aplicativo](configuration-http-settings.md#connection-draining).

## <a name="custom-error-pages"></a>Páginas de erro personalizadas

O Gateway de Aplicativo permite que você crie páginas de erro personalizadas em vez de exibir páginas de erro padrão. Você pode usar sua própria identidade visual e layout em uma página de erro personalizada.

Para obter mais informações, confira [Erros Personalizados](custom-error.md).

## <a name="rewrite-http-headers-and-url"></a>Reescrever cabeçalhos HTTP e URL

Os cabeçalhos HTTP permitem que o cliente e o servidor passem informações adicionais com a solicitação ou a resposta. A reescrita desses cabeçalhos HTTP ajuda você a realizar vários cenários importantes, como:

- Adição de campos de cabeçalho relacionados à segurança, como HSTS/X-XSS-Protection.
- Remoção de campos de cabeçalho de resposta que podem revelar informações confidenciais.
- Remoção de informações de porta de cabeçalhos X-Forwarded-For.

O Gateway de Aplicativo e a SKU v2 do WAF permitem adicionar, remover ou atualizar solicitações HTTP e cabeçalhos de resposta, enquanto os pacotes de solicitação e resposta são transferidos entre os pools de cliente e de back-end. Você também pode reescrever URLs, parâmetros de cadeia de caracteres de consulta e o nome do host. Com a reescrita de URL e o roteamento baseado em caminho de URL, você pode optar por rotear solicitações para um dos pools de back-end de acordo com o caminho original ou o caminho reescrito, usando a opção de reavaliação de mapa de caminho. 

Ele também fornece a capacidade de adicionar condições para garantir que os cabeçalhos ou URLs especificados sejam reescritos somente quando determinadas condições forem atendidas. Essas condições se baseiam nas informações de solicitação e resposta.

Para obter mais informações, consulte [reescrever cabeçalhos HTTP e URL](rewrite-http-headers-url.md).

## <a name="sizing"></a>Dimensionamento

Os Standard_v2 do gateway de aplicativo podem ser configurados para implantações de tamanho automático ou fixo. A SKU v2 não oferece tamanhos de instância diferentes. Para obter mais informações sobre o desempenho e os preços de v2, consulte [dimensionamento automático v2](application-gateway-autoscaling-zone-redundant.md) e [noções básicas sobre preços](understanding-pricing.md).

O padrão de gateway de aplicativo (v1) é oferecido em três tamanhos: **pequeno**, **médio**e **grande**. Os tamanhos de instância pequenos são destinados a cenários de desenvolvimento e teste.

Para obter uma lista completa de limites do gateway de aplicativo, consulte [Limites de serviço do Gateway de Aplicativo](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fapplication-gateway%2ftoc.json#application-gateway-limits).

A tabela a seguir mostra uma produtividade de desempenho médio para cada instância do Gateway de Aplicativo v1 com o descarregamento SSL habilitado:

| Tamanho médio de resposta de página de back-end | Pequena | Médio | grande |
| --- | --- | --- | --- |
| 6 KB |7,5 Mbps |13 Mbps |50 Mbps |
| 100 KB |35 Mbps |100 Mbps |200 Mbps |

> [!NOTE]
> Esses valores são valores aproximados para uma produtividade do Gateway de Aplicativo. A produtividade real depende de diversos detalhes de ambiente, como o tamanho médio da página, a localização das instâncias de back-end e o tempo de processamento para fornecer de uma página. Para obter números de desempenho exatos, você deve executar seus próprios testes. Esses valores são fornecidos apenas para a orientação do planejamento de capacidade.

## <a name="version-feature-comparison"></a>Comparação de recursos de versão

Para uma comparação de recursos do Application Gateway V1-V2, consulte [dimensionamento automático e gateway de aplicativo com redundância de zona v2](application-gateway-autoscaling-zone-redundant.md#feature-comparison-between-v1-sku-and-v2-sku)

## <a name="next-steps"></a>Próximas etapas

- Saiba como funciona o gateway de aplicativo- [como funciona um gateway de aplicativo](how-application-gateway-works.md)