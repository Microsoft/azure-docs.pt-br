---
title: "Opções de relatório avançado para o SDK do Azure Mobile Engagement para Android"
description: "Descreve como fazer relatórios avançados para capturar a análise do SDK do Azure Mobile Engagement para Android"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 7da7abd5-19d6-4892-94d8-818e5424b2cd
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 08/10/2016
ms.author: piyushjo;ricksal
ms.openlocfilehash: 2a1445afa2c2fca1a31ad9c012b9c8a917ebf65c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="advanced-reporting-with-engagement-on-android"></a>Relatório avançado com o Engagement no Android
> [!div class="op_single_selector"]
> * [Universal do Windows](mobile-engagement-windows-store-integrate-engagement.md)
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
> * [iOS](mobile-engagement-ios-integrate-engagement.md)
> * [Android](mobile-engagement-android-advanced-reporting.md)
> 
> 

Este tópico descreve cenários de relatório adicionais em seu aplicativo Android. Você pode aplicar estas opções ao aplicativo criado no tutorial [Introdução](mobile-engagement-android-get-started.md) .

## <a name="prerequisites"></a>Pré-requisitos
[!INCLUDE [Prereqs](../../includes/mobile-engagement-android-prereqs.md)]

O tutorial que você concluiu era deliberadamente simples e direto, mas há opções avançadas dentre as quais você pode escolher.

## <a name="modifying-your-activity-classes"></a>Modificação das classes `Activity`
No [tutorial Introdução](mobile-engagement-android-get-started.md), era necessário apenas fazer com que as subclasses `*Activity` herdassem das classes `Engagement*Activity` correspondentes. Por exemplo, se sua atividade herdada tiver estendido o `ListActivity`, você a faria estender `EngagementListActivity`.

> [!IMPORTANT]
> Ao usar `EngagementListActivity` ou `EngagementExpandableListActivity`, certifique-se de que qualquer chamada para `requestWindowFeature(...);` seja feita antes da chamada para `super.onCreate(...);`, caso contrário, ocorrerá uma falha.
> 
> 

Você pode encontrar essas classes na pasta `src` e copiá-las para seu projeto. As classes também estão no **JavaDoc**.

## <a name="alternate-method-call-startactivity-and-endactivity-manually"></a>Método alternativo: chame `startActivity()` e `endActivity()` manualmente
Se você não puder ou não quiser sobrecarregar as classes `Activity`, será possível iniciar e encerrar suas atividades chamando diretamente os métodos de `EngagementAgent`.

> [!IMPORTANT]
> O SDK do Android nunca chama o método `endActivity()`, mesmo quando o aplicativo é fechado (no Android, os aplicativos nunca são fechados). Assim, é *ALTAMENTE* recomendado chamar o método `startActivity()` no retorno de chamada `onResume` de *TODAS* as suas atividades e o método `endActivity()` no retorno de chamada `onPause()` de *TODAS* as suas atividades. Essa é a única maneira de certificar-se de que as sessões não serão perdidas. Se ocorrer perda de uma sessão, o serviço Engagement nunca se desconectará do back-end do Engagement (desde que o serviço permaneça conectado enquanto houver uma sessão pendente).
> 
> 

Aqui está um exemplo:

    public class MyActivity extends Some3rdPartyActivity
    {
      @Override
      protected void onResume()
      {
        super.onResume();
        String activityNameOnEngagement = EngagementAgentUtils.buildEngagementActivityName(getClass()); // Uses short class name and removes "Activity" at the end.
        EngagementAgent.getInstance(this).startActivity(this, activityNameOnEngagement, null);
      }

      @Override
      protected void onPause()
      {
        super.onPause();
        EngagementAgent.getInstance(this).endActivity();
      }
    }

Este exemplo é semelhante à classe `EngagementActivity` e suas variantes, cujo código-fonte é fornecido na pasta `src`.

## <a name="using-applicationoncreate"></a>Usando Application.onCreate()
Qualquer código que você colocar em `Application.onCreate()` , e em outros retornos de chamada do aplicativo, será executado para todos os processos de seu aplicativo, incluindo o serviço Engagement. Ele pode ter efeitos colaterais indesejados, como alocações de memória desnecessárias e threads no processo do Engagement, serviços ou receptores de difusão duplicados.

Caso você substitua `Application.onCreate()`, recomenda-se adicionar o seguinte trecho de código no início da função `Application.onCreate()`:

     public void onCreate()
     {
       if (EngagementAgentUtils.isInDedicatedEngagementProcess(this))
         return;

       ... Your code...
     }

Você pode fazer a mesma coisa para `Application.onTerminate()`, `Application.onLowMemory()` e `Application.onConfigurationChanged(...)`.

Você também pode estender `EngagementApplication` em vez de ampliar `Application`: o retorno de chamada `Application.onCreate()` faz a verificação do processo e chama `Application.onApplicationProcessCreate()` somente se o processo atual não for aquele que hospeda o serviço do Engagement. As mesmas regras se aplicam para os outros retornos de chamada.

## <a name="tags-in-the-androidmanifestxml-file"></a>Marcas no arquivo AndroidManifest.xml
Na marcação de serviço no arquivo AndroidManifest.xml, o atributo `android:label` permite que você escolha o nome do serviço do Engagement como ele aparecerá para os usuários finais na tela "Serviços em execução" de seus respectivos telefones. É recomendável definir esse atributo como `"<Your application name>Service"` (por exemplo, `"AcmeFunGameService"`).

Especificar o atributo `android:process` garante que o serviço do Engagement será executado em seu próprio processo (executando o Engagement no mesmo processo que seu aplicativo fará o thread/interface de usuário principal potencialmente menos responsivos).

## <a name="building-with-proguard"></a>Compilando com o ProGuard
Se você compilar seu pacote de aplicativo com o ProGuard, você precisa manter algumas classes. Você pode usar o seguinte trecho de código de configuração:

    -keep public class * extends android.os.IInterface
    -keep class com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity$EngagementReachContentJS {
    <methods>;
     }
