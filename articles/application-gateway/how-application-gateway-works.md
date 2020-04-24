---
title: Como funciona um gateway de aplicativo
description: Este artigo fornece informações sobre como um gateway de aplicativo aceita solicitações recebidas e as encaminha para o backend.
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 11/16/2019
ms.author: absha
ms.openlocfilehash: 84a7bdfb9f8f7c741140cbe2086149dff90db211
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132977"
---
# <a name="how-an-application-gateway-works"></a>Como funciona um gateway de aplicativo

Este artigo explica como um gateway de aplicativo aceita solicitações recebidas e as encaminha para o backend.

![Como um gateway de aplicativo aceita uma solicitação](./media/how-application-gateway-works/how-application-gateway-works.png)

## <a name="how-an-application-gateway-accepts-a-request"></a>Como um gateway de aplicativo aceita uma solicitação

1. Antes que um cliente envie uma solicitação para um gateway de aplicativo, ele resolve o nome de domínio do gateway de aplicativo usando um servidor DNS (Domain Name System, sistema de nome de domínio). O Azure controla a entrada do DNS porque todos os gateways de aplicativo estão no domínio azure.com.

2. O DNS do Azure retorna o endereço IP para o cliente, que é o endereço IP frontend do gateway de aplicativo.

3. O gateway de aplicativo aceita tráfego de entrada em um ou mais ouvintes. Um ouvinte é uma entidade lógica que verifica pedidos de conexão. Ele é configurado com um endereço IP frontend, protocolo e número de porta para conexões de clientes para o gateway de aplicativo.

4. Se um FIREWALL de aplicação web (WAF) estiver em uso, o gateway do aplicativo verifica os cabeçalhos de solicitação e o corpo, se presente, contra as regras do WAF. Esta ação determina se a solicitação é válida ou uma ameaça à segurança. Se a solicitação for válida, ela será encaminhada para o backend. Se a solicitação não for válida e o WAF estiver no modo prevenção, ele será bloqueado como uma ameaça à segurança. Se estiver no modo Detecção, a solicitação é avaliada e registrada, mas ainda assim encaminhada para o servidor backend.

O Azure Application Gateway pode ser usado como um balanceador de carga de aplicativo interno ou como um balanceador de carga de aplicativo voltado para a Internet. Um gateway de aplicativo voltado para a Internet usa endereços IP públicos. O nome DNS de um gateway de aplicativo voltado para a Internet é publicamente solucionável para seu endereço IP público. Como resultado, os gateways de aplicativos voltados para a Internet podem direcionar as solicitações dos clientes para a internet.

Os gateways internos de aplicativos usam apenas endereços IP privados. Se você estiver usando uma [zona DNS](https://docs.microsoft.com/azure/dns/private-dns-overview)personalizada ou privada, o nome de domínio deve ser resolvido internamente no endereço IP privado do Gateway de aplicativo. Portanto, balanceadores internos de carga só podem direcionar solicitações de clientes com acesso a uma rede virtual para o gateway de aplicativo.

## <a name="how-an-application-gateway-routes-a-request"></a>Como um gateway de aplicativo encaminha uma solicitação

Se uma solicitação for válida e não for bloqueada pelo WAF, o gateway de aplicativo avaliará a regra de roteamento de solicitação associada ao ouvinte. Esta ação determina para qual pool de backend encaminhará a solicitação.

Com base na regra de roteamento de solicitações, o gateway de aplicativo determina se deve encaminhar todas as solicitações no ouvinte para um pool de backend específico, solicitações de rota para diferentes pools de backend com base no caminho da URL ou redirecionar solicitações para outra porta ou site externo.
>[!NOTE]
>As regras são processadas na ordem em que estão listadas no portal para v1 SKU. 

Quando o gateway de aplicativo seleciona o pool de backend, ele envia a solicitação para um dos servidores saudáveis no pool de backend (y.y.y.y). A saúde do servidor é determinada por uma sonda de saúde. Se o pool de backend contiver vários servidores, o gateway de aplicativo usará um algoritmo round-robin para encaminhar as solicitações entre servidores saudáveis. Essa carga equilibra as solicitações nos servidores.

Depois que o gateway de aplicativo determina o servidor backend, ele abre uma nova sessão TCP com o servidor backend com base nas configurações HTTP. As configurações HTTP especificam as configurações de protocolo, porta e outras configurações relacionadas ao roteamento que são necessárias para estabelecer uma nova sessão com o servidor backend.

A porta e o protocolo usados nas configurações HTTP determinam se o tráfego entre o gateway de aplicativo e os servidores backend é criptografado (realizando assim TLS de ponta a ponta) ou não é criptografado.

Quando um gateway de aplicativo envia a solicitação original para o servidor backend, ele honra qualquer configuração personalizada feita nas configurações HTTP relacionadas à substituição do nome do host, do caminho e do protocolo. Essa ação mantém a afinidade de sessão baseada em cookies, a drenagem de conexões, a seleção de nomes de host a partir do backend e assim por diante.

 >[!NOTE]
>Se o pool de backend:
> - **É um endpoint público**, o gateway de aplicativo usa seu IP público frontend para chegar ao servidor. Se não houver um endereço IP público frontend, um deles será atribuído para a conectividade externa de saída.
> - **Contém um FQDN resolvido internamente ou um endereço IP privado,** o gateway de aplicativo encaminha a solicitação para o servidor backend usando seu endereço IP Privado reservado da instância.
> - **Contém um endpoint externo ou um FQDN resolvido externamente,** o gateway de aplicativo encaminha a solicitação para o servidor backend usando seu endereço IP público frontend. A resolução DNS é baseada em uma zona DNS privada ou servidor DNS personalizado, se configurado, ou usa o DNS fornecido pelo Azure padrão. Se não houver um endereço IP público frontend, um deles será atribuído para a conectividade externa de saída.

### <a name="modifications-to-the-request"></a>Modificações na solicitação

Um gateway de aplicativo insere quatro cabeçalhos adicionais em todas as solicitações antes de encaminhar as solicitações para o backend. Esses cabeçalhos são x-forwarded-for, x-forwarded-proto, x-forwarded-port, e x-original-host. O formato para cabeçalho x-forwarded-for é uma lista separada por vírgula de IP:port.

Os valores válidos para x-forwarded-proto são HTTP ou HTTPS. O cabeçalho x-forwarded-port especifica a porta onde a solicitação chegou ao gateway do aplicativo. O valor x-original-host contém o cabeçalho de host original com o qual a solicitação chegou. Este cabeçalho é útil na integração do site do Azure, onde o cabeçalho do host de entrada é modificado antes que o tráfego seja roteado para o backend. Se a afinidade de sessão estiver habilitada como uma opção, ela adicionará um cookie de afinidade gerenciado por gateway.

Você pode configurar o gateway de aplicativo para modificar cabeçalhos usando [Reescrever cabeçalhos HTTP](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers) ou para modificar o caminho URI usando uma configuração de substituição de caminho. No entanto, a menos que configurado para fazê-lo, todas as solicitações recebidas são enviadas em proxy para o backend.

## <a name="next-steps"></a>Próximas etapas

[Saiba mais sobre os componentes do gateway de aplicativos](application-gateway-components.md)
