---
title: Política de emparelhamento da Microsoft
titleSuffix: Azure
description: Política de emparelhamento da Microsoft
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: conceptual
ms.date: 12/15/2020
ms.author: prmitiki
ms.openlocfilehash: bee41bb8e5beb4df3086ab50499cb185a83e4efe
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/16/2020
ms.locfileid: "97592322"
---
# <a name="peering-policy"></a>Política de emparelhamento
A Microsoft mantém uma política de emparelhamento seletivo projetada para garantir a melhor experiência possível do cliente apoiada por padrões do setor e práticas recomendadas, dimensionando para demanda futura e posicionamento estratégico de emparelhamento. Como tal, a Microsoft se reserva o direito de fazer exceções à política, conforme necessário. Os requisitos gerais da Microsoft da sua rede são explicados nas seções a seguir. Elas se aplicam a solicitações de emparelhamento direto e emparelhamento do Exchange. 

## <a name="technical-requirements"></a>Requisitos técnicos

* Uma rede totalmente redundante com capacidade suficiente para trocar o tráfego sem congestionamento.
* O par terá um número de sistema autônomo roteável publicamente (ASN).
* Há suporte para IPv4 e IPv6 e a Microsoft espera estabelecer sessões de ambos os tipos em cada local de emparelhamento.
* Não há suporte para MD5.
* **Detalhes de ASN:**

    * A Microsoft gerencia o AS8075 juntamente com os seguintes ASNs: AS8068, AS8069, AS12076. Para obter uma lista completa de ASNs com emparelhamento AS8075, faça referência como-SET MICROSOFT.
    * Todas as partes que emparelham com a Microsoft concordam em não aceitar rotas do AS12076 (rota expressa) em nenhuma circunstância e devem filtrar AS12076 em todos os pares.

* **Política de roteamento:**
    * O par terá pelo menos um roteável publicamente/24.
    * A Microsoft substituirá os discriminadores de múltipla saída recebidos (MED).
    * A Microsoft prefere receber marcas de comunidade BGP de pares para indicar a origem da rota.
    * Recomendamos que os pares definam um prefixo máximo de rotas 2000 (IPv4) e 500 (IPv6) em sessões de emparelhamento com a Microsoft.
    * A menos que seja especificamente acordado anteriormente, os pares devem anunciar rotas consistentes em todos os locais onde emparelham com a Microsoft.
    * Em geral, as sessões de emparelhamento com AS8075 anunciarão todas as rotas AS-MICROSOFT. A Microsoft pode anunciar algumas especificidades regionais.
    * Nenhuma das partes estabelecerá uma rota estática, uma rota de último recurso ou enviará tráfego para a outra entidade para uma rota não anunciada via BGP.
    * O par é necessário para registrar suas rotas em um banco de dados de TIR (registro de roteamento de Internet) público, para fins de filtragem e manter essas informações atualizadas.      
    * Os colegas aderirão aos padrões do setor MANRS para segurança de rota.  A seu critério exclusivo, a Microsoft pode escolher: 1.) não estabelecer emparelhamento com empresas que não têm rotas assinadas e registradas; 2.) para remover rotas RPKI inválidas; 3.) não aceitar rotas de pontos estabelecidos que não estejam registrados e assinados. 

## <a name="operational-requirements"></a>Requisitos operacionais
* Um NOC (centro de operações de rede) 24x7, capaz de auxiliar na resolução de todos os problemas técnicos e de desempenho, violações de segurança, ataques de negação de serviço ou qualquer outro abuso originado no par ou em seus clientes.
* Espera-se que os pares tenham um perfil completo e atualizado em [PeeringDB](https://www.peeringdb.com) , incluindo um email do NOC 24x7 do domínio corporativo e do número de telefone. Usamos essas informações para validar os detalhes do par, como informações do NOC, informações de contato técnico e sua presença nos recursos de emparelhamento, etc. As contas pessoais Yahoo, Gmail e hotmail não são aceitas.

## <a name="physical-connection-requirements"></a>Requisitos de conexão física
* Os locais em que você pode se conectar à Microsoft para emparelhamento direto ou emparelhamento do Exchange estão listados em [PeeringDB](https://www.peeringdb.com/net/694)

* **Emparelhamento de troca:**
    * Espera-se que os pares tenham, no mínimo, uma conexão de 10 GB com a troca.
    * Espera-se que os pares atualizem suas portas quando a utilização de pico exceder 50%.
    * A Microsoft incentiva os colegas a manter a conectividade diversificada com o Exchange para dar suporte a cenários de failover.

* **Emparelhamento direto:**
    * A interconexão deve estar sobre a fibra de modo único usando fibra óptica de 100 Gbps.
    * A Microsoft estabelecerá somente o emparelhamento direto com provedores de serviços de rede ou ISP.
    * Espera-se que os pares atualizem suas portas quando a utilização de pico exceder 50% e manter a capacidade diversificada em cada metro, seja dentro de um único local ou em vários locais em um metro.
    * Cada emparelhamento direto consiste em duas conexões com dois roteadores do Microsoft Edge dos roteadores do par localizados na borda do par. A Microsoft requer sessões BGP duplas entre essas conexões. O par pode optar por não implantar dispositivos redundantes no final deles.


## <a name="traffic-requirements"></a>Requisitos de tráfego

* Os pares do emparelhamento do Exchange devem ter, no mínimo, 500 MB de tráfego e menos de 2 GB. Para o tráfego que excede o emparelhamento direto de 2 GB deve ser estabelecido.
* A Microsoft requer, no mínimo, 2 GB para emparelhamento direto. Cada local acordado mutuamente de emparelhamento deve dar suporte ao failover que garante que o emparelhamento permaneça localizado durante um cenário de failover. 

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre as etapas para configurar o emparelhamento direto com a Microsoft, siga [a explicação do emparelhamento direto](walkthrough-direct-all.md).
* Para saber mais sobre as etapas para configurar o emparelhamento do Exchange com a Microsoft, siga [a explicação do emparelhamento do Exchange](walkthrough-exchange-all.md).
