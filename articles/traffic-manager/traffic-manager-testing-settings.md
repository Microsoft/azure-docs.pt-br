---
title: Verificar as Configurações do Gerenciador de Tráfego do Azure
description: Este artigo ajudará você a verificar as configurações do Gerenciador de Tráfego.
services: traffic-manager
author: rockboyfor
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
origin.date: 03/16/2017
ms.date: 02/18/2019
ms.author: v-yeche
ms.openlocfilehash: 1e954e3c4ebba245d91cfb84ab583b314150e5b2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60771610"
---
# <a name="verify-traffic-manager-settings"></a>Verificar as configurações do Gerenciador de Tráfego

Para testar as configurações do Gerenciador de Tráfego, você precisa ter vários clientes, em diversos locais a partir dos quais você pode executar os testes. Em seguida, coloque os pontos de extremidade no perfil do Gerenciador de Tráfego, um de cada vez.

* Defina o valor TTL DNS como baixo, de forma que as alterações sejam propagadas rapidamente (30 segundos, por exemplo).
* Conheça os endereços IP dos sites e serviços de nuvem do Azure no perfil que você está testando.
* Use ferramentas que permitam resolver um nome DNS para um endereço IP e exibir esse endereço.

Você está verificando se os nomes DNS são resolvidos na forma de endereços IP dos pontos de extremidade em seu perfil. Os nomes devem ser resolvidos de maneira consistente com o método de roteamento de tráfego definido no perfil do Gerenciador de Tráfego. Você pode usar ferramentas como **nslookup** ou **dig** para resolver nomes DNS.

Os exemplos a seguir o ajudarão a testar seu perfil do Gerenciador de Tráfego.

### <a name="check-traffic-manager-profile-using-nslookup-and-ipconfig-in-windows"></a>Verifique o perfil do Gerenciador de Tráfego usando nslookup e ipconfig no Windows

1. Abra um prompt de comando ou do Windows PowerShell como administrador.
2. Digite `ipconfig /flushdns` para liberar o cache do resolvedor DNS.
3. Digite `nslookup <your Traffic Manager domain name>`. Por exemplo, o comando a seguir verifica o nome do domínio com o prefixo *myapp.contoso*

        nslookup myapp.contoso.trafficmanager.cn

    Um resultado típico mostra as seguintes informações:

    + O nome DNS e o endereço IP do servidor DNS que está sendo acessado para resolver o nome de domínio do Gerenciador de Tráfego.
    + O nome de domínio do Gerenciador de Tráfego digitado na linha de comando após "nslookup" e o endereço IP para o qual o domínio do Gerenciador de Tráfego é resolvido. O segundo endereço IP é o importante para a verificação. Ele deve corresponder a um endereço VIP (IP virtual) público de um dos serviços de nuvem ou sites no perfil do Gerenciador de Tráfego que você está testando.

## <a name="how-to-test-the-failover-traffic-routing-method"></a>Como testar o método de roteamento de tráfego de failover

1. Deixe todos os pontos de extremidade ativados.
2. Usando um único cliente, solicite a resolução de DNS para o nome de domínio da empresa usando nslookup ou um utilitário semelhante.
3. Verifique se o endereço IP resolvido corresponde ao ponto de extremidade primário.
4. Desativar seu ponto de extremidade primário ou remover o arquivo de monitoramento, para que o Gerenciador de Tráfego pense que o aplicativo está desativado.
5. Aguarde o TTL (período de vida útil) do DNS do perfil do Gerenciador de Tráfego, mais um tempo adicional de dois minutos. Por exemplo, se a TTL do DNS for de 300 segundos (cinco minutos), você deverá aguardar por sete minutos.
6. Libere o cache de seu cliente DNS e solicite a resolução do DNS usando nslookup. No Windows, você pode liberar o cache DNS com o comando ipconfig /flushdns.
7. Verifique se o endereço IP resolvido corresponde ao seu ponto de extremidade secundário.
8. Repita o processo, desativando um ponto de extremidade por vez. Verifique se o DNS retorna o endereço IP do próximo ponto de extremidade na lista. Quando todos os pontos de extremidade estiverem desativados, você deverá obter o endereço IP do ponto de extremidade primário novamente.

## <a name="how-to-test-the-weighted-traffic-routing-method"></a>Como testar o método de roteamento de tráfego ponderado

1. Deixe todos os pontos de extremidade ativados.
2. Usando um único cliente, solicite a resolução de DNS para o nome de domínio da empresa usando nslookup ou um utilitário semelhante.
3. Verifique se o endereço IP resolvido corresponde a um dos seus pontos de extremidade.
4. Libere o cache do cliente DNS e repita as etapas 2 e 3 para cada ponto de extremidade. Você deverá ver diferentes endereços IP retornados para cada um de seus pontos de extremidade.

## <a name="how-to-test-the-performance-traffic-routing-method"></a>Como testar o método de roteamento de tráfego de desempenho

Para testar efetivamente um método de roteamento de tráfego de desempenho, você deve ter clientes localizados em diferentes partes do mundo. Você pode criar clientes em diferentes regiões do Azure que podem ser usados para testar seus serviços. Se você tiver uma rede global, você pode, remotamente, se conectar a clientes em outras partes do mundo e executar seus testes.

Como alternativa, há serviços gratuitos e disponíveis de dig e pesquisa de DNS baseados na Web. Algumas dessas ferramentas possibilitam que você verifique a resolução de nome DNS a partir de vários locais no mundo. Pesquise "Pesquisa de DNS" para obter exemplos. Serviços de terceiros, como Gomez ou Keynote, podem ser usados para confirmar que os perfis estão distribuindo o tráfego conforme o esperado.

## <a name="next-steps"></a>Próximas etapas

* [Sobre os métodos de roteamento de tráfego do Gerenciador de Tráfego](traffic-manager-routing-methods.md)
* [Considerações sobre desempenho do Gerenciador de Tráfego](traffic-manager-performance-considerations.md)
* [Solucionando problemas de estado degradado do Gerenciador de Tráfego](traffic-manager-troubleshooting-degraded.md)

<!-- Update_Description: update meta properties -->