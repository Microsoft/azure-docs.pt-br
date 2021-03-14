---
title: Solucionar problemas durante a experiência de integração do Azure Percept DK
description: Obtenha dicas de solução para alguns dos problemas mais comuns encontrados durante a experiência de integração
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/18/2021
ms.custom: template-how-to
ms.openlocfilehash: d71cfa6ba52052e4b68175be84934c8b4294ed25
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101661730"
---
# <a name="azure-percept-dk-onboarding-experience-troubleshooting-guide"></a>Guia de solução de problemas da experiência de integração do Azure Percept DK

Aqui estão alguns problemas que você pode encontrar durante a experiência de integração do Azure Percept DK. Se, depois de usar as etapas neste guia, o problema ainda persistir. Contate o suporte ao cliente do Azure.

|Problema|Motivo|Solução alternativa|
|:-----|:------|:----------|
|Ao conectar-se às páginas de inscrição da conta do Azure ou ao portal do Azure, você poderá entrar automaticamente com uma conta armazenada em cache. Se essa não for a conta que você pretende usar, isso poderá resultar em uma experiência inconsistente com a documentação.|Isso geralmente ocorre devido a uma configuração no navegador para "lembrar" uma conta que você usou anteriormente.|Na página do Azure, clique no canto superior direito da página em que ele mostra o nome da conta e, em seguida, clique em "sair". Você poderá entrar com a conta correta.|
|A rede do ponto de acesso do Azure Percept DK (scz-XXXX) não aparece na lista de redes Wi-Fi disponíveis.|Normalmente, esse é um problema temporário que se resolve com um pouco de tempo.|Aguarde até que a rede seja exibida. Se não depois de mais de 15 minutos, reinicialize o dispositivo.|
|A conexão com o ponto de acesso do Azure Percept DK frequentemente se desconecta.|Isso pode ser devido a uma conexão ruim entre o dispositivo e o computador host. Ela também pode ser causada por interferência de outras conexões de Wi-Fi no computador host.|Verifique se as antenas estão conectadas corretamente ao kit de desenvolvimento. Se o kit de desenvolvimento estiver longe do computador host, tente movê-lo mais perto. Desative qualquer outra conexão com a Internet, como LTE/5G, se elas estiverem em execução no computador host.|
|O computador host mostra um aviso de segurança sobre a conexão com o ponto de acesso do Azure Percept DK.|Esse é um problema conhecido que será corrigido em uma atualização posterior.|É seguro prosseguir com a experiência de integração sobre o ponto de acesso do devkit Wi-Fi.|
|A rede do ponto de acesso do Azure Percept DK (scz-XXXX) aparece na lista de redes, mas não se conecta.|Isso pode ser devido a uma corrupção temporária do ponto de acesso do devkit Wi-Fi.|Reinicialize o devkit e tente novamente.|
|Não é possível conectar-se a uma rede Wi-Fi durante a experiência de instalação.|Atualmente, a rede Wi-Fi deve ter conectividade com a Internet para que possamos se comunicar com o Azure. O EAP [PEAP/MSCHAP], portais prisioneiros e conectividade Enterprise EAP-TLS não tem suporte no momento.|Verifique se o tipo Wi-Fi rede à qual você está se conectando tem suporte e tem conectividade com a Internet.|