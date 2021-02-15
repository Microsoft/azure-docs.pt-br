---
title: Preparar para alterar o endereço IP de saída
description: Se seu endereço IP de saída vai ser alterado, saiba o que fazer para que seu aplicativo continue a funcionar após a alteração.
ms.topic: article
ms.date: 06/28/2018
ms.custom: seodec18
ms.openlocfilehash: 2be4bc92dde278b054bd04f412f937440027ece7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "74671677"
---
# <a name="how-to-prepare-for-an-outbound-ip-address-change"></a>Como se preparar para uma alteração de endereço IP de saída

Se você recebeu uma notificação de que os endereços IP de saída do seu aplicativo do Serviço de Aplicativo do Azure estão mudando, siga as instruções neste artigo.

## <a name="determine-if-you-have-to-do-anything"></a>Determinar se você precisa fazer alguma coisa

* Opção 1: se seu aplicativo do Serviço de Aplicativo não usa filtragem de IP, uma lista de inclusão explícita ou tratamento especial de tráfego de saída, como roteamento ou firewall, nenhuma ação é necessária.

* Opção 2: se seu aplicativo tiver um tratamento especial para os endereços IP de saída (consulte os exemplos abaixo), adicione novos endereços IP de saída sempre que aparecer algum existente. Não substitua os endereços IP existentes. Você pode encontrar os novos endereços IP de saída seguindo as instruções na próxima seção.

  Por exemplo, um endereço IP de saída pode ser incluído explicitamente em um firewall fora do seu aplicativo ou um serviço de pagamento externo pode ter uma lista de permitidos que contém o endereço IP de saída para seu aplicativo. Se seu endereço de saída estiver configurado em uma lista em qualquer lugar fora do seu aplicativo, isso precisa ser alterado.

## <a name="find-the-outbound-ip-addresses-in-the-azure-portal"></a>Localizar os endereços IP de saída no Portal do Azure

Novos endereços IP de saída são mostrados no portal antes de entrarem em vigor. Quando o Azure começar a usar os novos, os antigos não serão usados. Apenas um conjunto é usado por vez, portanto entradas na lista de inclusão devem ter endereços IP antigos e novos para impedir uma interrupção quando a alternância acontece. 

1.  Abra o [Portal do Azure](https://portal.azure.com).

2.  No menu de navegação à esquerda, selecione **Serviços de Aplicativos**.

3.  Selecione o aplicativo do Serviço de Aplicativo na lista.

1.  Se o aplicativo é um aplicativo de funções, consulte [endereços IP de saída do aplicativo de função](../azure-functions/ip-addresses.md#find-outbound-ip-addresses).

4.  No cabeçalho **Configurações**, clique em **Propriedades** no painel de navegação esquerdo e localize a seção rotulada como **Endereços IP de Saída**.

5. Copie os endereços IP e adicione-os ao tratamento especial de tráfego de saída, como um filtro ou lista de permitidos. Não exclua os endereços IP existentes na lista.

## <a name="next-steps"></a>Próximas etapas

Este artigo explicou como se preparar para uma alteração de endereço IP que foi iniciada pelo Azure. Para obter mais informações sobre endereços IP no Serviço de Aplicativo do Azure, consulte [Entrada e endereços IP de saída no Serviço de Aplicativo do Azure](overview-inbound-outbound-ips.md).
