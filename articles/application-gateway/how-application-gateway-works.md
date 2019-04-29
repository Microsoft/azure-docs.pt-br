---
title: Como funciona um gateway de aplicativo
description: Este artigo fornece informações sobre o funcionamento de um gateway de aplicativo
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 02/20/2019
ms.author: absha
ms.openlocfilehash: 2f27105aed940f0411abaa534cb09adf0be34bfe
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60830307"
---
# <a name="how-an-application-gateway-works"></a>Como funciona um gateway de aplicativo

Este artigo explica como um gateway de aplicativo aceita solicitações de entrada e encaminha-as para o back-end.

![Como um gateway de aplicativo aceita uma solicitação](./media/how-application-gateway-works/how-application-gateway-works.png)

## <a name="how-an-application-gateway-accepts-a-request"></a>Como um gateway de aplicativo aceita uma solicitação

1. Antes de um cliente envia uma solicitação para um gateway de aplicativo, ele resolve o nome de domínio do gateway de aplicativo por meio de um servidor de sistema de nome de domínio (DNS). Azure controla a entrada DNS porque todos os gateways de aplicativo estão no domínio do azure.com.

2. O DNS do Azure retorna o endereço IP para o cliente, que é o endereço IP de front-end do gateway de aplicativo.

3. O gateway de aplicativo aceita o tráfego de entrada em um ou mais ouvintes. Um ouvinte é uma entidade lógica que verifica se há solicitações de conexão. Ele é configurado com um endereço IP de front, protocolo e número da porta para conexões de clientes para o gateway de aplicativo.

4. Se o firewall do aplicativo web (WAF) está em uso, o gateway de aplicativo verifica os cabeçalhos de solicitação e o corpo, se presente, em relação às regras de WAF. Essa ação determina se a solicitação é uma ameaça à segurança ou solicitação válida. Se a solicitação for válida, ela é roteada para o back-end. Se a solicitação não for válida, ele é bloqueado como uma ameaça à segurança.

O Gateway de aplicativo do Azure pode ser usado como um balanceador de carga interno do aplicativo ou como um balanceador de carga do aplicativo para a internet. Um gateway de aplicativo para a internet usa endereços IP públicos. O nome DNS de um gateway de aplicativo para a internet pode ser publicamente resolvido para seu endereço IP público. Como resultado, os gateways de aplicativos para a internet podem rotear solicitações de cliente para a internet.

Gateways de aplicativo interno usem somente endereços IP privados. O nome DNS de um gateway de aplicativo interno pode ser publicamente resolvido para seu endereço IP privado. Portanto, os balanceadores de carga internos só podem rotear solicitações de clientes com acesso a uma rede virtual para o gateway de aplicativo.

Gateways de aplicativo interno e da Internet rotear solicitações para servidores de back-end usando endereços IP privados. Servidores de back-end não precisam de endereços IP públicos para receber solicitações de interno ou um gateway de aplicativo para a internet.

## <a name="how-an-application-gateway-routes-a-request"></a>Como um gateway de aplicativo encaminha uma solicitação

Se uma solicitação for válida ou um WAF não está em uso, o gateway de aplicativo avalia a regra de roteamento de solicitação que está associada com o ouvinte. Essa ação determina qual pool de back-end para rotear a solicitação.

As regras são processadas na ordem em que eles estão listados no portal. Com base na regra de roteamento de solicitação, o gateway de aplicativo determina se deve rotear todas as solicitações no ouvinte para um pool de back-end específico, solicitações de rota para pools de back-end diferente com base no caminho de URL ou redirecionar solicitações para outra porta ou externos site.

Quando o gateway de aplicativo seleciona o pool de back-end, ele envia a solicitação para um dos servidores de back-end íntegros no pool (y.y.y.y). A integridade do servidor é determinada por uma investigação de integridade. Se o pool de back-end contém vários servidores, o gateway de aplicativo usa um algoritmo de round-robin para rotear as solicitações entre servidores íntegros. Equilibra as solicitações nos servidores.

Depois que o gateway de aplicativo determina o servidor de back-end, ele abre uma nova sessão TCP com o servidor de back-end com base nas configurações de HTTP. Configurações de HTTP de especificam o protocolo, porta e outras configurações relacionadas a roteamento que são necessárias para estabelecer uma nova sessão com o servidor de back-end.

A porta e protocolo usado nas configurações de HTTP determinam se o tráfego entre os servidores de gateway e o back-end do aplicativo é criptografado (portanto, realização de SSL de ponta a ponta) ou não criptografado.

Quando um gateway de aplicativo envia a solicitação original para o servidor de back-end, ele respeita qualquer feita nas configurações de HTTP relacionadas ao substituir o nome do host, o caminho e o protocolo de configuração personalizada. Essa ação mantém afinidade de sessão baseada em cookies, seleção de descarga, nome de host de conexão de back-end e assim por diante.

Um gateway de aplicativo interno usa apenas os endereços IP privados. O nome DNS de um gateway de aplicativo interno pode ser resolvido para seu endereço IP privado. Como resultado, os balanceadores de carga internos só podem rotear solicitações de clientes com acesso à rede virtual para o gateway de aplicativo.

 >[!NOTE]
 >Ambos os gateways de aplicativo internos e voltados para internet rotear solicitações para servidores back-end usando endereços IP privados. Essa ação ocorre quando o recurso de pool de back-end contém um endereço IP privado, configuração NIC da VM ou um endereço internamente que pode ser resolvido. Se o pool de back-end:
> - **É um ponto de extremidade público**, o gateway de aplicativo usa seu IP público de front-end para acessar o servidor. Se não houver um endereço IP público de front-end, um é atribuído para a conectividade externa de saída.
> - **Contém um FQDN resolvível internamente ou um endereço IP privado**, o gateway de aplicativo roteia a solicitação para o servidor de back-end usando seus endereços IP privados de instância.
> - **Contém um ponto de extremidade externo ou um FQDN externamente resolvível**, o gateway de aplicativo roteia a solicitação para o servidor de back-end usando seu endereço IP público de front-end. A resolução de DNS baseia-se em uma zona DNS privada ou um servidor DNS personalizado, se configurado, ou ele usa o padrão fornecido pelo Azure DNS. Se não houver um endereço IP público de front-end, um é atribuído para a conectividade externa de saída.

### <a name="modifications-to-the-request"></a>Modificações à solicitação

Um gateway de aplicativo insere cabeçalhos adicionais de quatro a todas as solicitações antes de encaminhar as solicitações para o back-end. Esses cabeçalhos são x-forwarded-for, x-forwarded-proto, x-forwarded-port e x-original-host. O formato do cabeçalho x-forwarded-for é uma lista separada por vírgulas de IP: porta.

Os valores válidos para x-forwarded-proto são HTTP ou HTTPS. X-forwarded-port Especifica a porta em que a solicitação alcançou o gateway de aplicativo. Cabeçalho X-original-host contém o cabeçalho de host original com o qual a solicitação chegou. Esse cabeçalho é útil na integração do site do Azure, onde o cabeçalho de host de entrada é modificado antes do tráfego é roteado para o back-end. Se a afinidade de sessão é habilitada como uma opção, ele adiciona um cookie de afinidade gerenciado pelo gateway.

Você pode configurar o gateway de aplicativo para modificar os cabeçalhos usando [cabeçalhos HTTP reescrever](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers) ou para modificar o caminho URI usando uma configuração de substituição de demarcador. No entanto, a menos que configurado para fazer isso, todas as solicitações de entrada são transmitidas por proxy para o back-end.

## <a name="next-steps"></a>Próximas etapas

[Saiba mais sobre componentes do gateway de aplicativo](application-gateway-components.md)
