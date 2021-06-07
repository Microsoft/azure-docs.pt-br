---
title: Como funciona um gateway de aplicativo
description: Este artigo fornece informações sobre como um gateway de aplicativo aceita solicitações de entrada e as roteia para o back-end.
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: conceptual
ms.date: 11/16/2019
ms.author: absha
ms.openlocfilehash: d2055bf812c3dc986a907d4358fa0e74e8af20fa
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104599656"
---
# <a name="how-an-application-gateway-works"></a>Como funciona um gateway de aplicativo

Este artigo explica como um gateway de aplicativo aceita solicitações de entrada e as roteia para o back-end.

![Como um gateway de aplicativo aceita uma solicitação](./media/how-application-gateway-works/how-application-gateway-works.png)

## <a name="how-an-application-gateway-accepts-a-request"></a>Como um gateway de aplicativo aceita uma solicitação

1. Antes que um cliente envie uma solicitação para um gateway de aplicativo, ele resolve o nome de domínio do gateway de aplicativo usando um servidor DNS (sistema de nomes de domínio). O Azure controla a entrada DNS porque todos os gateways de aplicativo estão no domínio azure.com.

2. O DNS do Azure retorna o endereço IP para o cliente, que é o endereço IP de front-end do gateway de aplicativo.

3. O gateway de aplicativo aceita o tráfego de entrada em um ou mais ouvintes. Um ouvinte é uma entidade lógica que verifica solicitações de conexão. Ele é configurado com um endereço IP de front-end, um protocolo e um número de porta para conexões de clientes com o gateway de aplicativo.

4. Se um firewall do aplicativo Web (WAF) estiver em uso, o gateway de aplicativo verificará os cabeçalhos de solicitação e o corpo, se estiver presente, em relação às regras de WAF. Essa ação determina se a solicitação é uma solicitação válida ou uma ameaça à segurança. Se a solicitação for válida, ela será roteada para o back-end. Se a solicitação não for válida e WAF estiver no modo de prevenção, ela será bloqueada como uma ameaça à segurança. Se ele estiver no modo de detecção, a solicitação será avaliada e registrada em log, mas ainda será encaminhada para o servidor de back-end.

Aplicativo Azure gateway pode ser usado como um balanceador de carga de aplicativo interno ou como um balanceador de carga de aplicativo voltado para a Internet. Um gateway de aplicativo voltado para a Internet usa endereços IP públicos. O nome DNS de um gateway de aplicativo voltado para a Internet é publicamente resolvível para seu endereço IP público. Como resultado, os gateways de aplicativo voltados para a Internet podem rotear solicitações de cliente da Internet.

Os gateways de aplicativo internos usam apenas endereços IP privados. Se você estiver usando uma zona personalizada ou [DNS privado](../dns/private-dns-overview.md), o nome de domínio deverá ser resolvido internamente para o endereço IP privado do gateway de aplicativo. Portanto, os balanceadores de carga internos só podem rotear solicitações de clientes com acesso a uma rede virtual para o gateway de aplicativo.

## <a name="how-an-application-gateway-routes-a-request"></a>Como um gateway de aplicativo roteia uma solicitação

Se uma solicitação for válida e não for bloqueada pelo WAF, o gateway de aplicativo avaliará a regra de roteamento de solicitação associada ao ouvinte. Essa ação determina o pool de back-end para o qual rotear a solicitação.

Com base na regra de roteamento de solicitação, o gateway de aplicativo determina se todas as solicitações no ouvinte devem ser roteadas para um pool de back-end específico, rotear solicitações para diferentes pools de back-end com base no caminho da URL ou redirecionar solicitações para outra porta ou site externo.
>[!NOTE]
>As regras são processadas na ordem em que estão listadas no portal para SKU v1. 

Quando o gateway de aplicativo seleciona o pool de back-end, ele envia a solicitação para um dos servidores de back-end íntegros no pool (y. y. y. y). A integridade do servidor é determinada por uma investigação de integridade. Se o pool de back-end contiver vários servidores, o gateway de aplicativo usará um algoritmo Round Robin para rotear as solicitações entre servidores íntegros. Essa carga equilibra as solicitações nos servidores.

Depois que o gateway de aplicativo determina o servidor back-end, ele abre uma nova sessão TCP com o servidor back-end com base nas configurações de HTTP. As configurações de HTTP especificam o protocolo, a porta e outras configurações relacionadas ao roteamento que são necessárias para estabelecer uma nova sessão com o servidor de back-end.

A porta e o protocolo usados nas configurações de HTTP determinam se o tráfego entre o gateway de aplicativo e os servidores de back-end é criptografado (o que realiza o TLS de ponta a ponta) ou não está criptografado.

Quando um gateway de aplicativo envia a solicitação original para o servidor de back-end, ele honra qualquer configuração personalizada feita nas configurações de HTTP relacionadas à substituição do nome do host, caminho e protocolo. Essa ação mantém a afinidade de sessão baseada em cookie, o descarregamento de conexão, a seleção de nome de host do back-end e assim por diante.

 >[!NOTE]
>Se o pool de back-end:
> - **É um ponto de extremidade público**, o gateway de aplicativo usa seu IP público de front-end para acessar o servidor. Se não houver um endereço IP público de front-end, um será atribuído para a conectividade externa de saída.
> - **Contém um FQDN que possa ser resolvido internamente ou um endereço IP privado**, o gateway de aplicativo roteia a solicitação para o servidor de back-end usando seus endereços IP privados de instância.
> - **Contém um ponto de extremidade externo ou um FQDN que possa ser resolvido externamente**, o gateway de aplicativo roteia a solicitação para o servidor de back-end usando seu endereço IP público de front-end. A resolução DNS é baseada em uma zona DNS privada ou em um servidor DNS personalizado, se configurada, ou usa o DNS padrão fornecido pelo Azure. Se não houver um endereço IP público de front-end, um será atribuído para a conectividade externa de saída.

### <a name="modifications-to-the-request"></a>Modificações na solicitação

Um gateway de aplicativo insere quatro cabeçalhos adicionais a todas as solicitações antes de encaminhar as solicitações para o back-end. Esses cabeçalhos são x-forwardd-for, x-forwardd-proto, x-Forwarded-Port e x-original-host. O formato do cabeçalho x-Forwarded-for é uma lista separada por vírgulas de IP: porta.

Os valores válidos para o proto x-forwardd são HTTP ou HTTPS. X-Forwarded-Port especifica a porta em que a solicitação atingiu o gateway de aplicativo. X-original-o cabeçalho de host contém o cabeçalho de host original com o qual a solicitação chegou. Esse cabeçalho é útil na integração de sites do Azure, em que o cabeçalho de host de entrada é modificado antes de o tráfego ser roteado para o back-end. Se a afinidade de sessão estiver habilitada como uma opção, ela adicionará um cookie de afinidade gerenciada por gateway.

Você pode configurar o gateway de aplicativo para modificar cabeçalhos e URL de solicitação e resposta usando [regravar cabeçalhos HTTP e URL](rewrite-http-headers-url.md) ou para modificar o caminho do URI usando uma configuração de substituição de caminho. No entanto, a menos que esteja configurado para fazer isso, todas as solicitações de entrada são coativadas para o back-end.

## <a name="next-steps"></a>Próximas etapas

[Saiba mais sobre os componentes do gateway de aplicativo](application-gateway-components.md)
