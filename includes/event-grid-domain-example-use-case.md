---
title: incluir arquivo
description: incluir arquivo
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 03/04/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 2459cf6c5055dcde83dccf37addc160aeeaa64ad
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102198636"
---
Os domínios de eventos são mais facilmente explicados usando um exemplo. Digamos que você execute máquinas de construção da Contoso, em que você fabrica os tratores, a utilização de equipamentos e outras máquinas pesadas. Como parte da execução dos negócios, você envia informações em tempo real aos clientes sobre manutenção de equipamentos, integridade de sistemas e atualizações de contrato. Todas essas informações vão para vários pontos de extremidade, incluindo seu aplicativo, endpoints de clientes e outras infraestruturas que os clientes configuraram.

Todas essas informações vão para vários pontos de extremidade, incluindo seu aplicativo, endpoints de clientes e outras infraestruturas que os clientes configuraram. Cada um dos seus clientes é representado como um tópico dentro do domínio. Autenticação e autorização são tratadas usando o Active Directory do Azure. Cada um de seus clientes pode se inscrever no tópico deles e receber os eventos deles. O acesso gerenciado por meio do domínio do evento garante que eles possam acessar apenas o tópico deles.

Além disso, também fornece um ponto de extremidade exclusivo, em que todos os eventos de cliente podem ser publicados. A Event Grid cuidará de garantir que cada tópico esteja ciente dos eventos definidos para o locatário.

:::image type="content" source="./media/event-grid-domain-example-use-case/contoso-construction-example.png" alt-text="Exemplo de construção Contoso" lightbox="./media/event-grid-domain-example-use-case/contoso-construction-example.png":::