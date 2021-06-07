---
title: Hubs de notificação do Azure e a migração do Google firebase Cloud Messaging (FCM)
description: Descreve como os hubs de notificação do Azure abordam o Google GCM para a migração FCM.
services: notification-hubs
author: sethmanheim
manager: femila
editor: jwargo
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 04/10/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 04/10/2019
ms.openlocfilehash: 2f2ca4b56445b3f399477e396de579d8a8c539e3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "80127033"
---
# <a name="azure-notification-hubs-and-google-firebase-cloud-messaging-migration"></a>Hubs de notificação do Azure e migração de mensagens de nuvem do Google firebase

## <a name="current-state"></a>Estado atual

Quando o Google anunciou sua migração do Google Cloud Messaging (GCM) para o firebase Cloud Messaging (FCM), os serviços de push como os nossos tinham de ajustar as notificações para dispositivos Android para acomodar a alteração.

Atualizamos nosso back-end de serviço e publicamos as atualizações em nossa API e SDKs, conforme necessário. Com nossa implementação, tomamos a decisão de manter a compatibilidade com os esquemas de notificação GCM existentes para minimizar o impacto do cliente. Isso significa que atualmente enviamos notificações para dispositivos Android usando FCM no modo herdado do FCM. Por fim, queremos adicionar o verdadeiro suporte para FCM, incluindo os novos recursos e o formato de carga. Essa é uma alteração de longo prazo e a migração atual concentra-se em manter a compatibilidade com aplicativos e SDKs existentes. Você pode usar os SDKs do GCM ou do FCM em seu aplicativo (juntamente com nosso SDK) e garantimos que a notificação seja enviada corretamente.

Alguns clientes receberam recentemente um email do Google Warning sobre aplicativos usando um ponto de extremidade do GCM para notificações. Isso era apenas um aviso e nada é quebrado – as notificações do Android do seu aplicativo ainda são enviadas ao Google para processamento e o Google ainda os processa. Alguns clientes que especificaram o ponto de extremidade do GCM explicitamente em sua configuração de serviço ainda estavam usando o ponto de extremidade preterido. Já tínhamos identificado essa lacuna e estava trabalhando na correção do problema quando o Google enviou o email.

Substituímos esse ponto de extremidade preterido e a correção é implantada.

## <a name="going-forward"></a>Em frente

As perguntas frequentes do FCM do Google afirma que você não precisa fazer nada. Nas [perguntas frequentes do FCM](https://developers.google.com/cloud-messaging/faq), o Google disse que "SDKs de cliente e tokens GCM continuarão a funcionar indefinidamente. No entanto, não será possível direcionar a versão mais recente do Google Play Services em seu aplicativo Android, a menos que você migre para o FCM. "

Se seu aplicativo usar a biblioteca do GCM, vá em frente e siga as instruções do Google para atualizar para a biblioteca FCM em seu aplicativo. Nosso SDK é compatível com qualquer um, portanto, você não precisará atualizar nada em seu aplicativo em nosso lado (desde que esteja atualizado com a versão do SDK).

## <a name="questions-and-answers"></a>Perguntas e respostas

Aqui estão algumas respostas para perguntas comuns que ouvimos dos clientes:

**P:** O que preciso fazer para ser compatível com a data de corte (a data de corte atual do Google pode ser 29 e pode mudar)?

**R:** Nada. Manteremos a compatibilidade com o esquema de notificação GCM existente. A chave do GCM continuará funcionando normalmente como qualquer SDK e biblioteca do GCM usados pelo seu aplicativo.

Se/quando você decidir atualizar para os SDKs e bibliotecas do FCM para aproveitar os novos recursos, sua chave do GCM ainda funcionará. Você pode alternar para o uso de uma chave FCM, se desejar, mas certifique-se de que você está adicionando firebase ao seu projeto GCM existente ao criar o novo projeto firebase. Isso garantirá a compatibilidade com os clientes que executam versões mais antigas do aplicativo que ainda usam SDKs e bibliotecas do GCM.

Se você estiver criando um novo projeto FCM e não estiver anexando ao projeto GCM existente, depois de atualizar os hubs de notificação com o novo segredo FCM, você perderá a capacidade de enviar notificações por push para suas instalações de aplicativo atuais, já que a nova chave FCM não tem nenhum link para o antigo projeto GCM.

**P:** Por que estou recebendo esse email sobre os antigos pontos de extremidade do GCM em uso? O que preciso fazer?

**R:** Nada. Estamos migrando para os novos pontos de extremidade e serão concluídos em breve, portanto, nenhuma alteração será necessária. Nada é quebrado, nosso ponto de extremidade perdido simplesmente causou mensagens de aviso do Google.

**P:** Como posso fazer a transição para os novos SDKs e bibliotecas do FCM sem interromper os usuários existentes?

R: atualizar a qualquer momento. O Google ainda não anunciou nenhuma substituição de SDKs e bibliotecas do GCM existentes. Para garantir que você não interrompa as notificações por push para os usuários existentes, certifique-se de criar o novo projeto firebase que você está associando ao seu projeto GCM existente. Isso garantirá que os novos segredos do firebase funcionem para os usuários que executam as versões mais antigas de seu aplicativo com SDKs e bibliotecas do GCM, bem como novos usuários de seu aplicativo com SDKs e bibliotecas do FCM.

**P:** Quando posso usar novos recursos e esquemas do FCM para minhas notificações?

**R:** Depois de publicar uma atualização em nossa API e SDKs, fique atento – esperamos ter algo para você nos próximos meses.
