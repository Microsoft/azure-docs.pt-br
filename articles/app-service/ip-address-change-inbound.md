---
title: Preparar para alteração de endereço IP de entrada
description: Se seu endereço IP de entrada vai ser alterado, saiba o que fazer para que seu aplicativo continue a funcionar após a alteração.
ms.topic: article
ms.date: 06/28/2018
ms.custom: seodec18
ms.openlocfilehash: 999511c4759ee761f028a61fbfaa0310ac93d9b4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "74672409"
---
# <a name="how-to-prepare-for-an-inbound-ip-address-change"></a>Como se preparar para uma alteração de endereço IP de entrada

Se você recebeu uma notificação de que o endereço IP de entrada do seu aplicativo do Serviço de Aplicativo do Azure está mudando, siga as instruções neste artigo.

## <a name="determine-if-you-have-to-do-anything"></a>Determinar se você precisa fazer alguma coisa

* Opção 1: se seu aplicativo do Serviço de Aplicativo não tiver um Domínio Personalizado, nenhuma ação é necessária.

* Opção 2: se apenas um registro CNAME (registro DNS que aponta para um URI) é configurado no Portal de Registro de Domínio (Provedor DNS de terceiros ou DNS do Azure), nenhuma ação é necessária.

* Opção 3: se um registro (registro DNS que aponta diretamente para o endereço IP) for configurado no Portal de Registro de Domínio (Provedor DNS de terceiros ou DNS do Azure), substitua o endereço IP existente pelo novo. Você pode encontrar o novo endereço IP seguindo as instruções na próxima seção.

* Opção 4: se seu aplicativo estiver atrás de um balanceador de carga, filtro de IP ou qualquer outro mecanismo IP que requer um endereço IP do aplicativo, substitua o endereço IP existente pelo novo. Você pode encontrar o novo endereço IP seguindo as instruções na próxima seção.

## <a name="find-the-new-inbound-ip-address-in-the-azure-portal"></a>Você pode encontrar o novo Endereço IP de entrada no Portal do Azure.

O novo endereço IP de entrada que está sendo fornecido para o aplicativo está no portal no campo **Endereço IP Virtual**. Esse novo endereço IP e o antigo estão conectados ao seu aplicativo agora, e mais tarde o antigo será desconectado.

1.  Abra o [Portal do Azure](https://portal.azure.com).

2.  No menu de navegação à esquerda, selecione **Serviços de Aplicativos**.

3.  Selecione o aplicativo do Serviço de Aplicativo na lista.

1.  Se o aplicativo é um aplicativo de funções, consulte [aplicativo de funções endereço IP de entrada](../azure-functions/ip-addresses.md#function-app-inbound-ip-address).

4.  No cabeçalho **Configurações**, clique em **Propriedades** no painel de navegação esquerdo e localize a seção rotulada como **Endereço IP Virtual**.

5. Copie o endereço IP e reconfigure o registro de domínio ou o mecanismo de IP.

## <a name="next-steps"></a>Próximas etapas

Este artigo explicou como se preparar para uma alteração de endereço IP que foi iniciada pelo Azure. Para obter mais informações sobre endereços IP no Serviço de Aplicativo do Azure, consulte [Entrada e endereços IP de saída no Serviço de Aplicativo do Azure](overview-inbound-outbound-ips.md).
