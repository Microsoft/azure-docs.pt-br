---
title: Alta disponibilidade e balanceamento de carga-Proxy de Aplicativo do AD do Azure
description: Como a distribuição de tráfego funciona com a implantação do proxy de aplicativo. Inclui dicas de como otimizar o desempenho do conector e usar o balanceamento de carga para servidores back-end.
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/08/2019
ms.author: kenwith
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1fda858b0811eb6308b8e5588eaeae9bff5a1730
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/02/2021
ms.locfileid: "99259382"
---
# <a name="high-availability-and-load-balancing-of-your-application-proxy-connectors-and-applications"></a>Alta disponibilidade e balanceamento de carga de seus aplicativos e conectores de proxy de aplicativo

Este artigo explica como a distribuição de tráfego funciona com a implantação do proxy de aplicativo. Vamos discutir:

- Como o tráfego é distribuído entre usuários e conectores, juntamente com dicas para otimizar o desempenho do conector

- Como o tráfego flui entre conectores e servidores de aplicativos de back-end, com recomendações para balanceamento de carga entre vários servidores back-end

## <a name="traffic-distribution-across-connectors"></a>Distribuição de tráfego entre conectores

Os conectores estabelecem suas conexões com base nos princípios para alta disponibilidade. Não há nenhuma garantia de que o tráfego sempre será distribuído uniformemente entre os conectores e não há nenhuma afinidade de sessão. No entanto, o uso varia e as solicitações são enviadas aleatoriamente para instâncias de serviço de proxy de aplicativo. Como resultado, o tráfego normalmente é distribuído quase igualmente entre os conectores. O diagrama e as etapas a seguir ilustram como as conexões são estabelecidas entre usuários e conectores.

![Diagrama mostrando conexões entre usuários e conectores](media/application-proxy-high-availability-load-balancing/application-proxy-connections.png)

1. Um usuário em um dispositivo cliente tenta acessar um aplicativo local publicado por meio do proxy de aplicativo.
2. A solicitação passa por um Azure Load Balancer para determinar qual instância de serviço de proxy de aplicativo deve executar a solicitação. Por região, há dezenas de instâncias disponíveis para aceitar a solicitação. Esse método ajuda a distribuir uniformemente o tráfego entre as instâncias de serviço.
3. A solicitação é enviada ao [barramento de serviço](../../service-bus-messaging/index.yml).
4. O barramento de serviço sinaliza para um conector disponível. Em seguida, o conector pega a solicitação do barramento de serviço.
   - Na etapa 2, as solicitações vão para diferentes instâncias de serviço de proxy de aplicativo, portanto, é mais provável que as conexões sejam feitas com conectores diferentes. Como resultado, os conectores são quase usados de forma uniforme dentro do grupo.
5. O conector passa a solicitação para o servidor de back-end do aplicativo. Em seguida, o aplicativo envia a resposta de volta para o conector.
6. O conector conclui a resposta abrindo uma conexão de saída para a instância de serviço de onde a solicitação veio. Em seguida, essa conexão é fechada imediatamente. Por padrão, cada conector é limitado a 200 conexões de saída simultâneas.
7. Em seguida, a resposta é passada de volta para o cliente da instância de serviço.
8. As solicitações subsequentes da mesma conexão repetim as etapas acima.

Um aplicativo geralmente tem muitos recursos e abre várias conexões quando ele é carregado. Cada conexão passa pelas etapas acima para ser alocada a uma instância de serviço, selecione um novo conector disponível se a conexão ainda não tiver sido emparelhada anteriormente com um conector.


## <a name="best-practices-for-high-availability-of-connectors"></a>Práticas recomendadas para alta disponibilidade de conectores

- Devido à maneira como o tráfego é distribuído entre conectores para alta disponibilidade, é essencial sempre ter pelo menos dois conectores em um grupo de conectores. Três conectores são preferenciais para fornecer buffer adicional entre conectores. Para determinar o número correto de conectores necessários, siga a documentação de planejamento de capacidade.

- Coloque os conectores em conexões de saída diferentes para evitar um único ponto de falha. Se os conectores usarem a mesma conexão de saída, um problema de rede com a conexão poderá afetar todos os conectores que o utilizam.

- Evite forçar os conectores a reiniciar quando conectados a aplicativos de produção. Isso pode afetar negativamente a distribuição do tráfego entre conectores. A reinicialização de conectores faz com que mais conectores não estejam disponíveis e força conexões com o conector restante disponível. O resultado é um uso desigual dos conectores inicialmente.

- Evite todas as formas de inspeção embutida em comunicações TLS de saída entre conectores e o Azure. Esse tipo de inspeção embutida causa degradação no fluxo de comunicação.

- Certifique-se de manter as atualizações automáticas em execução para seus conectores. Se o serviço de Atualizador do Conector do proxy de aplicativo estiver em execução, os conectores serão atualizados automaticamente e receberão a atualização mais recente. Caso você não veja o serviço Atualizador do Conector no servidor, precisará reinstalar o conector para obter todas as atualizações.

## <a name="traffic-flow-between-connectors-and-back-end-application-servers"></a>Fluxo de tráfego entre conectores e servidores de aplicativos de back-end

Outra área principal em que a alta disponibilidade é um fator é a conexão entre os conectores e os servidores back-end. Quando um aplicativo é publicado por meio do Azure Proxy de Aplicativo do AD, o tráfego dos usuários para os aplicativos flui por três saltos:

1. O usuário se conecta ao ponto de extremidade público do serviço de Proxy de Aplicativo do AD do Azure no Azure. A conexão é estabelecida entre o endereço IP do cliente de origem (público) do cliente e o endereço IP do ponto de extremidade do proxy de aplicativo.
2. O conector de proxy de aplicativo efetua pull da solicitação HTTP do cliente do serviço de proxy de aplicativo.
3. O conector de proxy de aplicativo se conecta ao aplicativo de destino. O conector usa seu próprio endereço IP para estabelecer a conexão.

![Diagrama de usuário que se conecta a um aplicativo por meio do proxy de aplicativo](media/application-proxy-high-availability-load-balancing/application-proxy-three-hops.png)

### <a name="x-forwarded-for-header-field-considerations"></a>Considerações de campo de cabeçalho X-Forwarded-for
Em algumas situações (como auditoria, balanceamento de carga etc.), o compartilhamento do endereço IP de origem do cliente externo com o ambiente local é um requisito. Para atender ao requisito, o conector de Proxy de Aplicativo do AD do Azure adiciona o campo de cabeçalho X-Forwarded-for com o endereço IP do cliente de origem (público) à solicitação HTTP. O dispositivo de rede apropriado (balanceador de carga, firewall) ou o servidor Web ou o aplicativo de back-end pode ler e usar as informações.

## <a name="best-practices-for-load-balancing-among-multiple-app-servers"></a>Práticas recomendadas para balanceamento de carga entre vários servidores de aplicativos
Quando o grupo de conectores atribuído ao aplicativo de proxy de aplicativo tem dois ou mais conectores, e você está executando o aplicativo Web de back-end em vários servidores (farm de servidores), é necessária uma boa estratégia de balanceamento de carga. Uma boa estratégia garante que os servidores peguem as solicitações do cliente de maneira uniforme e impeçam o excesso ou a utilização de servidores no farm de servidores.
### <a name="scenario-1-back-end-application-does-not-require-session-persistence"></a>Cenário 1: o aplicativo de back-end não requer persistência de sessão
O cenário mais simples é onde o aplicativo Web de back-end não exige a adesão da sessão (persistência da sessão). Qualquer solicitação do usuário pode ser tratada por qualquer instância de aplicativo de back-end no farm de servidores. Você pode usar um balanceador de carga de camada 4 e configurá-lo sem afinidade. Algumas opções incluem o balanceamento de carga de rede da Microsoft e Azure Load Balancer ou um balanceador de carga de outro fornecedor. Como alternativa, o DNS Round Robin pode ser configurado.
### <a name="scenario-2-back-end-application-requires-session-persistence"></a>Cenário 2: o aplicativo de back-end requer persistência de sessão
Nesse cenário, o aplicativo Web de back-end requer a adesão da sessão (persistência da sessão) durante a sessão autenticada. Todas as solicitações do usuário devem ser tratadas pela instância do aplicativo de back-end que é executada no mesmo servidor no farm de servidores.
Esse cenário pode ser mais complicado porque o cliente geralmente estabelece várias conexões com o serviço de proxy de aplicativo. Solicitações em diferentes conexões podem chegar em diferentes conectores e servidores no farm. Como cada conector usa seu próprio endereço IP para essa comunicação, o balanceador de carga não pode garantir a adesão da sessão com base no endereço IP dos conectores. A afinidade de IP de origem não pode ser usada.
Aqui estão algumas opções para o cenário 2:

- Opção 1: basear a persistência da sessão em um cookie de sessão definido pelo balanceador de carga. Essa opção é recomendada porque permite que a carga seja distribuída mais uniformemente entre os servidores back-end. Ele requer um balanceador de carga de camada 7 com esse recurso e que pode manipular o tráfego HTTP e encerrar a conexão TLS. Você pode usar Aplicativo Azure gateway (afinidade de sessão) ou um balanceador de carga de outro fornecedor.

- Opção 2: basear a persistência da sessão no campo de cabeçalho X-Forwarded-for. Essa opção requer um balanceador de carga de camada 7 com esse recurso e que pode manipular o tráfego HTTP e encerrar a conexão TLS.  

- Opção 3: Configure o aplicativo de back-end para não exigir persistência de sessão.

Consulte a documentação do fornecedor do software para entender os requisitos de balanceamento de carga do aplicativo de back-end.

## <a name="next-steps"></a>Próximas etapas

- [Habilitar Proxy de aplicativo](application-proxy-add-on-premises-application.md)
- [Habilitar o logon único](application-proxy-configure-single-sign-on-with-kcd.md)
- [Habilitar o Acesso Condicional](application-proxy-integrate-with-sharepoint-server.md)
- [Solucionar problemas que surgirem com o Proxy de Aplicativo](application-proxy-troubleshoot.md)
- [Saiba como a arquitetura do Azure AD dá suporte à alta disponibilidade](../fundamentals/active-directory-architecture.md)