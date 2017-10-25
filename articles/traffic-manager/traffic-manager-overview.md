---
title: "O que é o Gerenciador de Tráfego| Microsoft Docs"
description: "Este artigo o ajudará a entender o que é o Gerenciador de Tráfego e se é a opção de roteamento do tráfego correta para seu aplicativo"
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
editor: 
ms.assetid: 75d5ff9a-f4b9-4b05-af32-700e7bdfea5a
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/15/2017
ms.author: kumud
ms.openlocfilehash: 50d7f14d0d4234ee98d8a46e903b5f916cb02fab
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="overview-of-traffic-manager"></a>Visão Geral do Gerenciador de Tráfego

O Gerenciador de Tráfego do Microsoft Azure permite controlar a distribuição do tráfego do usuário para pontos de extremidade do serviço em diferentes datacenters. Os pontos de extremidade de serviço com suporte no Gerenciador de Tráfego incluem VMs do Azure, Aplicativos Web e serviços de nuvem. Você também pode usar o Gerenciador de Tráfego com pontos de extremidade externos e não do Azure.

O Gerenciador de Tráfego usa o DNS (Sistema de Nomes de Domínio) para direcionar solicitações de cliente ao ponto de extremidade mais apropriado com base em um método de roteamento de tráfego e na integridade dos pontos de extremidade. O Gerenciador de Tráfego oferece uma variedade de [métodos de roteamento de tráfego](traffic-manager-routing-methods.md) e [opções de monitoramento de ponto de extremidade](traffic-manager-monitoring.md) para atender às diferentes necessidades dos aplicativos e modelos de failover automático. O Gerenciador de Tráfego é resistente a falhas, incluindo a falha de toda a região do Azure.

## <a name="traffic-manager-benefits"></a>Benefícios do Gerenciador de Tráfego

O Gerenciador de Tráfego pode ajudá-lo a:

* **Aumentar a disponibilidade de aplicativos críticos**

    O Gerenciador de Tráfego fornece alta disponibilidade para seus aplicativos monitorando seus pontos de extremidade e fornecendo failover automático quando um ponto de extremidade fica inativo.

* **Melhora a capacidade de resposta de aplicativos de alto desempenho**

    O Azure permite executar serviços de nuvem ou sites em data centers espalhados pelo mundo inteiro. O Gerenciador de Tráfego melhora a capacidade de resposta do aplicativo direcionando o tráfego para o ponto de extremidade com a menor latência de rede para o cliente.

* **Realizar manutenção de serviço sem tempo de inatividade**

    Você pode executar operações de manutenção planejada em seus aplicativos sem tempo de inatividade. O Gerenciador de Tráfego direciona tráfego para pontos de extremidade alternativos enquanto a manutenção está em andamento.

* **Combinar aplicativos baseados em nuvem e locais**

    O Gerenciador de Tráfego tem suporte para pontos de extremidade externos não do Azure habilitando seu uso com implantações locais e de nuvem híbrida, incluindo os cenários de "intermitência para a nuvem", "migrar para a nuvem" e "failover para a nuvem".

* **Distribuir o tráfego para implantações grandes e complexas**

    Usando [perfis aninhados do Gerenciador de Tráfego](traffic-manager-nested-profiles.md), métodos de roteamento de tráfego podem ser combinados para criar regras sofisticadas e flexíveis para atender às necessidades de implantações maiores e mais complexas.

## <a name="how-traffic-manager-works"></a>Como funciona o Gerenciador de Tráfego

O Gerenciador de Tráfego do Azure permite que você controle a distribuição do tráfego entre os pontos de extremidade do aplicativo. Um ponto de extremidade é qualquer serviço para a Internet hospedado dentro ou fora do Azure.

O Gerenciador de Tráfego oferece dois benefícios principais:

1. Distribuição do tráfego de acordo com um dos vários [métodos de roteamento de tráfego](traffic-manager-routing-methods.md)
2. [Monitoramento contínuo de integridade do ponto de extremidade](traffic-manager-monitoring.md) e failover automático quando os pontos de extremidade falharem

Quando um cliente tenta se conectar a um serviço, ele primeiro deve resolver o nome DNS do serviço para um endereço IP. O cliente se conecta a esse endereço IP para acessar o serviço.

**O ponto mais importante para entender é que o Gerenciador de Tráfego funciona no nível de DNS.**  O Gerenciador de Tráfego usa o DNS para direcionar clientes para pontos de extremidade de serviço específicos com base nas regras do método de roteamento de tráfego. Os clientes se conectam **diretamente** ao ponto de extremidade selecionado. O Gerenciador de Tráfego não é um proxy nem um gateway. O Gerenciador de Tráfego não vê o tráfego que passa entre o cliente e o serviço.

### <a name="traffic-manager-example"></a>Exemplo de Gerenciador de Tráfego

A Contoso Corp desenvolveu um novo portal de parceiros. A URL para este portal é https://partners.contoso.com/login.aspx. O aplicativo é hospedado em três regiões do Azure. Para melhorar a disponibilidade e maximizar o desempenho global, elas usam o Gerenciador de Tráfego para distribuir o tráfego do cliente para o ponto de extremidade mais próximo disponível.

Para alcançar essa configuração, eles concluem as etapas a seguir:

1. Implantam três instâncias de seu serviço. Os nomes DNS dessas implantações são “contoso-us.cloudapp.net”, “contoso-eu.cloudapp.net” e “contoso-asia.cloudapp.net”.
2. Criam um perfil do Gerenciador de Tráfego chamado “contoso.trafficmanager.net” e configuram-no para usar o método de roteamento de tráfego por “Desempenho” nos três pontos de extremidade.
* Configuram seu nome de domínio intuitivo “partners.contoso.com” para apontar para “contoso.trafficmanager.net” usando um registro DNS CNAME.

![Configuração DNS do Gerenciador de Tráfego][1]

> [!NOTE]
> Ao usar um domínio personalizado com o Gerenciador de Tráfego do Azure, você deve usar um CNAME para indicar seu nome de domínio intuitivo para seu nome de domínio do Gerenciador de Tráfego. Os padrões do DNS não permitem que você crie um CNAME no “apex” (ou raiz) de um domínio. Portanto, não é possível criar um CNAME para “contoso.com” (às vezes chamado de domínio raiz). Você só pode criar um CNAME para um domínio em “contoso.com”, como “www.contoso.com”. Para solucionar esse problema, recomendamos o uso um redirecionamento HTTP simples para redirecionar as solicitações de “contoso.com” para um nome alternativo como “www.contoso.com”.

### <a name="how-clients-connect-using-traffic-manager"></a>Como os clientes se conectam usando o Gerenciador de Tráfego

Continuando o exemplo anterior, quando um cliente solicita a página https://partners.contoso.com/login.aspx, ele realiza as seguintes etapas para resolver o nome DNS e estabelecer uma conexão:

![Estabelecimento de conexão usando o Gerenciador de Tráfego][2]

1. O cliente envia uma consulta DNS para seu serviço DNS recursivo configurado para resolver o nome “partners.contoso.com”. Um serviço DNS recursivo, às vezes chamado de serviço “DNS local”, não hospeda domínios DNS diretamente. Em vez disso, o cliente descarrega o trabalho de entrar em contato com os diversos serviços DNS autoritativos pela Internet necessários para resolver um nome DNS.
2. Para resolver o nome DNS, o serviço DNS recursivo localiza os servidores de nomes para o domínio “contoso.com”. Ele então entra em contato com os servidores de nomes para solicitar o registro DNS de “partners.contoso.com”. Os servidores DNS de contoso.com retornam o registro CNAME que aponta para contoso.trafficmanager.net.
3. Em seguida, o serviço DNS recursivo localiza os servidores de nomes para o domínio “trafficmanager.net”, que são fornecidos pelo serviço do Gerenciador de Tráfego do Azure. Em seguida, ele envia uma solicitação para o registro DNS de “contoso.trafficmanager.net” para os servidores DNS.
4. Os servidores de nomes do Gerenciador de Tráfego recebem a solicitação. Eles escolhem um ponto de extremidade que se baseia:

    - No estado configurado de cada ponto de extremidade (pontos de extremidade desabilitados não são retornados)
    - A integridade atual de cada ponto de extremidade, conforme determinado pela integridade Gerenciador de Tráfego. Para obter mais informações, consulte [Monitoramento do Ponto de Extremidade do Gerenciador de Tráfego](traffic-manager-monitoring.md).
    - O método de roteamento de tráfego escolhido. Para obter mais informações, consulte [Métodos de roteamento de tráfego do Gerenciador de Tráfego](traffic-manager-routing-methods.md).

5. O ponto de extremidade escolhido é retornado como outro registro DNS CNAME. Nesse caso, vamos supor que contoso-us.cloudapp.net seja retornado.
6. Em seguida, o serviço DNS recursivo localiza os servidores de nomes para o domínio “cloudapp.net”. Ele entra em contato com os servidores de nomes para solicitar o registro DNS de “contoso-us.cloudapp.net”. Um registro DNS “A” que contém o endereço IP do ponto de extremidade de serviço dos EUA é retornado.
7. O serviço DNS recursivo consolida os resultados e retorna uma única resposta DNS para o cliente.
8. O cliente recebe os resultados DNS e conecta-se ao endereço IP fornecido. O cliente se conecta ao ponto de extremidade de serviço do aplicativo diretamente, não pelo Gerenciador de Tráfego. Como é um ponto de extremidade HTTPS, o cliente executa o handshake SSL/TLS necessário e faz uma solicitação HTTP GET para a página “/login.aspx”.

O serviço DNS recursivo armazena em cache as respostas DNS recebidas. O resolvedor DNS no dispositivo do cliente também armazena em cache o resultado. O cache permite que as próximas consultas DNS sejam respondidas mais rapidamente usando dados do cache em vez de consultar outros servidores de nomes. A duração do cache é determinada pela propriedade TTL (“vida útil”) de cada registro DNS. Valores menores resultam na expiração de cache mais rápida e, portanto, mais viagens de ida e volta para os servidores de nomes do Gerenciador de Tráfego. Valores maiores significam que pode levar mais tempo para direcionar o tráfego para fora de um ponto de extremidade com falha. O Gerenciador de Tráfego permite configurar o TTL usado em respostas de DNS do Gerenciador de Tráfego para serem de apenas 0 segundo e de até 2.147.483.647 segundos (o intervalo máximo em conformidade com [RFC-1035](https://www.ietf.org/rfc/rfc1035.txt)), habilitando-o a escolher o valor que melhor equilibre as necessidades do seu aplicativo.

## <a name="pricing"></a>Preços

Para obter informações sobre preço, consulte [Preços do Gerenciador de Tráfego](https://azure.microsoft.com/pricing/details/traffic-manager/).

## <a name="faq"></a>Perguntas frequentes

Para conferir as perguntas frequentes sobre o Gerenciador de Tráfego, confira [Perguntas frequentes sobre o Gerenciador de Tráfego](traffic-manager-FAQs.md)

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o [failover automático e monitoramento do ponto de extremidade](traffic-manager-monitoring.md)do Gerenciador de Tráfego.

Saiba mais sobre os [métodos de roteamento de tráfego](traffic-manager-routing-methods.md)do Gerenciador de Tráfego.

Saiba mais sobre alguns dos outros principais [recursos de rede](../networking/networking-overview.md) do Azure.

<!--Image references-->
[1]: ./media/traffic-manager-how-traffic-manager-works/dns-configuration.png
[2]: ./media/traffic-manager-how-traffic-manager-works/flow.png

