---
title: Os Hubs de notificação do Azure e a migração do Google Firebase Cloud Messaging (FCM)
description: Descreve como os Hubs de notificação do Azure aborda o Google GCM para migração do FCM.
services: notification-hubs
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 04/10/2019
ms.author: jowargo
ms.openlocfilehash: 4cbfc67bc66e84b4743f3326db40872241e5d474
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61458290"
---
# <a name="azure-notification-hubs-and-the-google-firebase-cloud-messaging-fcm-migration"></a>Os Hubs de notificação do Azure e a migração do Google Firebase Cloud Messaging (FCM)

## <a name="current-state"></a>Estado atual

Quando o Google anunciou sua migração do Google Cloud Messaging (GCM) para o Firebase Cloud Messaging (FCM), serviços de envio por push, como nosso tinha que ajustar como enviamos notificações para dispositivos Android para acomodar a alteração.

Atualizado o nosso back-end do serviço, então atualizações publicadas para nossa API e os SDKs conforme necessário. Nossa implementação, tomamos a decisão para manter a compatibilidade com esquemas de notificação existentes do GCM para minimizar o impacto para o cliente. Isso significa que podemos atualmente enviar notificações para dispositivos Android usando o FCM no modo herdado de FCM. Por fim, queremos adicionar suporte a verdadeiro FCM, incluindo os novos recursos e o formato de carga. Isso é uma mudança de longo prazo e a migração atual está focada em manter a compatibilidade com aplicativos existentes e SDKs. Você pode usar os SDKs do FCM ou GCM em seu aplicativo (juntamente com o nosso SDK) e verificamos se que a notificação é enviada corretamente.

Alguns clientes recentemente receberam um email de aviso do Google sobre aplicativos usando um ponto de extremidade do GCM para notificações. Isso era apenas um aviso e nada esteja desfeito – notificações do Android do seu aplicativo ainda são enviadas para o Google para processamento e Google ainda as processa. Alguns clientes que especificado o ponto de extremidade do GCM explicitamente em sua configuração de serviço ainda estavam usando o ponto de extremidade preterido. Nós já identificaram essa lacuna e foram trabalhando para corrigir o problema, quando Google enviou o email.

Substituímos esse ponto de extremidade preterido e a correção é implantada.

## <a name="going-forward"></a>No futuro

Perguntas frequentes sobre o FCM do Google diz que você não precisa fazer nada. No [perguntas frequentes sobre o FCM](https://developers.google.com/cloud-messaging/faq), Google disse "SDKs de cliente e tokens GCM continuará a funcionar por tempo indeterminado. No entanto, você não será capaz de direcionar para a versão mais recente do Google Play Services em seu aplicativo Android, a menos que você migra para o FCM."

Se seu aplicativo usa a biblioteca do GCM, vá em frente e siga as instruções do Google para atualizar para a biblioteca FCM em seu aplicativo. Nosso SDK é compatível com qualquer um, assim você não terá que atualizar nada em seu aplicativo em nosso lado (desde que você está atualizado com a versão do SDK).

## <a name="questions-and-answers"></a>Perguntas e respostas

Eis aqui algumas respostas para perguntas comuns que ouvimos dos clientes:

**P:** O que é necessário fazer para ser compatível pela data de fechamento (Google atual da data de fechamento é 29 de maio e pode ser alterado)?

**R:** Nada. Manteremos compatibilidade com o esquema de notificação existente do GCM. Sua chave do GCM continuarão a funcionar normalmente serão qualquer GCM SDKs e as bibliotecas usadas pelo seu aplicativo.

Se/quando você decidir atualizar para os SDKs do FCM e bibliotecas para tirar proveito dos novos recursos, sua chave do GCM ainda funcionará. Você pode passar a usar uma chave FCM se quiser, mas certifique-se de que você está adicionando o Firebase ao seu projeto existente do GCM ao criar o novo projeto do Firebase. Isso garantirá a compatibilidade com versões anteriores com os clientes que estão executando versões mais antigas do aplicativo que ainda usam bibliotecas e SDKs do GCM.

Se você estiver criando um novo projeto do FCM e não anexar ao projeto existente do GCM, após a atualização dos Hubs de notificação com o novo segredo FCM você perderá a capacidade para enviar notificações por push para as instalações de aplicativo atual, já que a nova chave FCM não tem um link para o GCM antigo projeto.

**P:** Por que estou recebendo este email sobre pontos de extremidade GCM antigos que está sendo usado? O que é necessário fazer?

**R:** Nada. Podemos ter sido migrando para novos pontos de extremidade e estará tudo pronto em breve, portanto, nenhuma alteração é necessária. Nada esteja desfeito, nosso um ponto de extremidade ausente simplesmente causada mensagens de aviso do Google.

**P:** Como posso fazer a transição para os novos SDKs FCM e bibliotecas sem interromper os usuários existentes?

R: Atualizar a qualquer momento. Google ainda não anunciou qualquer substituição de bibliotecas e SDKs existentes do GCM. Para garantir que você não interrompa as notificações por push para seus usuários existentes, certifique-se de quando você criar o novo projeto do Firebase que você está associando seu projeto existente do GCM. Isso garantirá que o novo Firebase segredos funcionará para usuários que executam as versões mais antigas do seu aplicativo com SDKs do GCM e bibliotecas, bem como novos usuários do seu aplicativo com bibliotecas e SDKs do FCM.

**P:** Quando usar novos recursos FCM e esquemas para minhas notificações?

**R:** Depois que publicamos uma atualização à nossa API e os SDKs, fique ligado – Esperamos que tenha algo para você nos próximos meses.
