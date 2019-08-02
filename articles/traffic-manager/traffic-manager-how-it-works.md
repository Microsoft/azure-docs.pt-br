---
title: Como o Gerenciador de Tráfego do Azure funciona | Microsoft Docs
description: Este artigo ajudará você a entender como o Traffic Manager roteia tráfego para alto desempenho e disponibilidade de seus aplicativos da Web
services: traffic-manager
documentationcenter: ''
author: asudbring
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/05/2019
ms.author: allensu
ms.openlocfilehash: 281e1e591d7c3cc31b77a116fb42af49dc27798c
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68312142"
---
# <a name="how-traffic-manager-works"></a>Como funciona o Gerenciador de tráfego

O Gerenciador de Tráfego do Azure permite que você controle a distribuição do tráfego entre os pontos de extremidade do aplicativo. Um ponto de extremidade é qualquer serviço para a Internet hospedado dentro ou fora do Azure.

O Gerenciador de Tráfego oferece dois benefícios principais:

- Distribuição do tráfego de acordo com um dos vários [métodos de roteamento de tráfego](traffic-manager-routing-methods.md)
- [Monitoramento contínuo de integridade do ponto de extremidade](traffic-manager-monitoring.md) e failover automático quando os pontos de extremidade falharem

Quando um cliente tenta se conectar a um serviço, ele primeiro deve resolver o nome DNS do serviço para um endereço IP. O cliente se conecta a esse endereço IP para acessar o serviço.

**O ponto mais importante para entender é que o Gerenciador de Tráfego funciona no nível de DNS.**  O Gerenciador de Tráfego usa o DNS para direcionar clientes para pontos de extremidade de serviço específicos com base nas regras do método de roteamento de tráfego. Os clientes se conectam **diretamente** ao ponto de extremidade selecionado. O Gerenciador de Tráfego não é um proxy nem um gateway. O Gerenciador de Tráfego não vê o tráfego que passa entre o cliente e o serviço.

## <a name="traffic-manager-example"></a>Exemplo de Gerenciador de Tráfego

A Contoso Corp desenvolveu um novo portal de parceiros. A URL para este portal é https://partners.contoso.com/login.aspx. O aplicativo é hospedado em três regiões do Azure. Para melhorar a disponibilidade e maximizar o desempenho global, elas usam o Gerenciador de Tráfego para distribuir o tráfego do cliente para o ponto de extremidade mais próximo disponível.

Para alcançar essa configuração, eles concluem as etapas a seguir:

1. Implantam três instâncias de seu serviço. Os nomes DNS dessas implantações são “contoso-us.cloudapp.net”, “contoso-eu.cloudapp.net” e “contoso-asia.cloudapp.net”.
1. Criam um perfil do Gerenciador de Tráfego chamado “contoso.trafficmanager.net” e configuram-no para usar o método de roteamento de tráfego por “Desempenho” nos três pontos de extremidade.
1. Configuram seu nome de domínio intuitivo “partners.contoso.com” para apontar para “contoso.trafficmanager.net” usando um registro DNS CNAME.

![Configuração DNS do Gerenciador de Tráfego][1]

> [!NOTE]
> Ao usar um domínio personalizado com o Gerenciador de Tráfego do Azure, você deve usar um CNAME para indicar seu nome de domínio intuitivo para seu nome de domínio do Gerenciador de Tráfego. Os padrões do DNS não permitem que você crie um CNAME no “apex” (ou raiz) de um domínio. Portanto, não é possível criar um CNAME para “contoso.com” (às vezes chamado de domínio raiz). Você só pode criar um CNAME para um domínio em “contoso.com”, como “www.contoso.com”. Para contornar essa limitação, é recomendável hospedar o domínio DNS na [DNS do Azure](../dns/dns-overview.md) e o usar [Registros de alias](../dns/tutorial-alias-tm.md) para apontar para seu perfil do gerenciador de tráfego. Outra opção é usar um simples HTTP para redirecionar as solicitações diretas de “contoso.com” para um nome alternativo como "www.contoso.com".

### <a name="how-clients-connect-using-traffic-manager"></a>Como os clientes se conectam usando o Gerenciador de Tráfego

Continuando do exemplo anterior, quando um cliente solicita a página https://partners.contoso.com/login.aspx, o cliente executa as seguintes etapas para resolver o nome DNS e estabelecer uma conexão:

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

## <a name="faqs"></a>Perguntas frequentes

* [Qual endereço IP o Gerenciador de tráfego usa?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-ip-address-does-traffic-manager-use)

* [Quais tipos de tráfego podem ser roteados usando o Gerenciador de tráfego?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-types-of-traffic-can-be-routed-using-traffic-manager)

* [O Gerenciador de tráfego dá suporte a sessões "adesivas"?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#does-traffic-manager-support-sticky-sessions)

* [Por que estou vendo um erro HTTP ao usar o Gerenciador de tráfego?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#why-am-i-seeing-an-http-error-when-using-traffic-manager)

* [Qual é o impacto no desempenho do uso do Gerenciador de tráfego?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-is-the-performance-impact-of-using-traffic-manager)

* [Quais protocolos de aplicativo posso usar com o Gerenciador de tráfego?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-application-protocols-can-i-use-with-traffic-manager)

* [Posso usar o Gerenciador de tráfego com um nome de domínio "Naked"?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-use-traffic-manager-with-a-naked-domain-name)

* [O Gerenciador de tráfego considera o endereço de sub-rede do cliente ao manipular consultas DNS?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#does-traffic-manager-consider-the-client-subnet-address-when-handling-dns-queries)

* [O que é o TTL do DNS e como ele afeta meus usuários?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#what-is-dns-ttl-and-how-does-it-impact-my-users)

* [O quão alto ou baixo posso definir o TTL das respostas do Gerenciador de tráfego?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-high-or-low-can-i-set-the-ttl-for-traffic-manager-responses)

* [Como posso entender o volume de consultas que estão chegando ao meu perfil?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-can-i-understand-the-volume-of-queries-coming-to-my-profile)

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o [failover automático e monitoramento do ponto de extremidade](traffic-manager-monitoring.md)do Gerenciador de Tráfego.

Saiba mais sobre os [métodos de roteamento de tráfego](traffic-manager-routing-methods.md)do Gerenciador de Tráfego.

<!--Image references-->
[1]: ./media/traffic-manager-how-traffic-manager-works/dns-configuration.png
[2]: ./media/traffic-manager-how-traffic-manager-works/flow.png

