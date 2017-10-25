---
title: Como usar o API Engagement no iOS
description: SDK do iOS mais recente - Como usar a API Engagement no iOS
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 1fb4509e-3804-46c1-949f-1cf727f91f9f
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: na
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: a31424da98205e97bdf57010cccfd044360f03dd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-the-engagement-api-on-ios"></a>Como usar o API Engagement no iOS
Este documento é um complemento para o documento Como integrar o Engagement no iOS: ele fornece detalhes aprofundados sobre como usar a API Engagement para relatar as estatísticas do aplicativo.

Tenha em mente que caso queira o Engagement apenas para relatar as sessões, atividades, falhas e informações técnicas do seu aplicativo, então a maneira mais simples é fazer todos os seus objetos personalizados `UIViewController` herdados da classe `EngagementViewController` correspondente.

Se você quiser fazer mais, por exemplo, se você precisar reportar eventos, erros e trabalhos específicos do aplicativo ou se você tiver que relatar as atividades do seu aplicativo de maneira diferente de um implementado nas classes `EngagementViewController`, você precisará usar a API do Engagement.

A API do Engagement é fornecida pela classe `EngagementAgent` . Uma instância desta classe pode ser recuperada chamando o método estático `[EngagementAgent shared]` (Observe que o objeto retornado `EngagementAgent` é um singleton).

Antes de qualquer chamada de API, o objeto `EngagementAgent` deve ser inicializado chamando o método `[EngagementAgent init:@"Endpoint={YOUR_APP_COLLECTION.DOMAIN};SdkKey={YOUR_SDK_KEY};AppId={YOUR_APPID}"];`

## <a name="engagement-concepts"></a>Conceitos de Engagement
As seguintes partes refinam os [Conceitos de Mobile Engagement](mobile-engagement-concepts.md) usuais para a plataforma iOS.

### <a name="session-and-activity"></a>`Session` e `Activity`
Uma *atividade* geralmente está associada com uma tela do aplicativo, isso significa que a *atividade* se inicia quando a tela é exibida e é interrompida quando a tela for fechada: esse é o caso quando o SDK Engagement é integrado usando as classes `EngagementViewController`.

Mas as *atividades* também podem ser controladas manualmente usando a API do Engagement. Isso permite dividir uma determinada tela em várias partes secundárias para obter mais detalhes sobre o uso desta tela (por exemplo para com frequência e por quanto tempo as caixas de diálogo são usadas dentro desta tela).

## <a name="reporting-activities"></a>Relatório de atividades
### <a name="user-starts-a-new-activity"></a>O usuário inicia uma nova atividade
            [[EngagementAgent shared] startActivity:@"MyUserActivity" extras:nil];

É necessário chamar `startActivity()` sempre que houver alterações de atividade do usuário. A primeira chamada para essa função inicia uma nova sessão de usuário.

### <a name="user-ends-his-current-activity"></a>O usuário encerra sua atividade atual
            [[EngagementAgent shared] endActivity];

> [!WARNING]
> Você **NUNCA** deve chamar essa função por conta própria, exceto se você quiser dividir um uso de seu aplicativo em várias sessões: uma chamada para essa função encerraria a sessão atual imediatamente, portanto, uma chamada subsequente para `startActivity()` deveria iniciar uma nova sessão. Essa função é chamada automaticamente pelo SDK quando seu aplicativo é fechado.
> 
> 

## <a name="reporting-events"></a>Eventos de relatório
### <a name="session-events"></a>Eventos de sessão
Eventos de sessão são geralmente usados para relatar as ações executadas por um usuário durante a sessão.

**Exemplo sem dados adicionais:**

    @implementation MyViewController {
       [...]
       - (void)willRotateToInterfaceOrientation:(UIInterfaceOrientation)toInterfaceOrientation duration:(NSTimeInterval)duration
       {
        [super willRotateToInterfaceOrientation:toInterfaceOrientation duration:duration];
            ...
        [[EngagementAgent shared] sendSessionEvent:@"will_rotate" extras:nil];
            ...
       }
       [...]
    }

**Exemplo com dados adicionais:**

    @implementation MyViewController {
       [...]
       - (void)willRotateToInterfaceOrientation:(UIInterfaceOrientation)toInterfaceOrientation duration:(NSTimeInterval)duration
       {
        [super willRotateToInterfaceOrientation:toInterfaceOrientation duration:duration];
            ...
        NSMutableDictionary* extras = [NSMutableDictionary dictionary];
        [extras setObject:[NSNumber numberWithInt:toInterfaceOrientation] forKey:@"to_orientation_id"];
        [extras setObject:[NSNumber numberWithDouble:duration] forKey:@"duration"];
        [[EngagementAgent shared] sendSessionEvent:@"will_rotate" extras:extras];
            ...
       }
       [...]
    }

### <a name="standalone-events"></a>Eventos autônomos
Ao contrário dos eventos de sessão, os eventos autônomos podem ser usados fora do contexto de uma sessão.

**Exemplo:**

    [[EngagementAgent shared] sendEvent:@"received_notification" extras:nil];

## <a name="reporting-errors"></a>Erros de relatório
### <a name="session-errors"></a>Erros de sessão
Erros de sessão são geralmente usados para relatar os erros que afetam o usuário durante a sessão.

**Exemplo:**

    /** The user has entered invalid data in a form */
    @implementation MyViewController {
      [...]
      -(void)onMyFormSubmitted:(MyForm*)form {
        [...]
        /* The user has entered an invalid email address */
        [[EngagementAgent shared] sendSessionError:@"sign_up_email" extras:nil]
        [...]
      }
      [...]
    }

### <a name="standalone-errors"></a>Erros autônomos
Ao contrário dos erros de sessão, os erros autônomos podem ser usados fora do contexto de uma sessão.

**Exemplo:**

    [[EngagementAgent shared] sendError:@"something_failed" extras:nil];

## <a name="reporting-jobs"></a>Relatório de trabalhos
**Exemplo:**

Suponha que você deseja relatar a duração do processo de logon:

    [...]
    -(void)signIn
    {
      /* Start job */
      [[EngagementAgent shared] startJob:@"sign_in" extras:nil];

      [... sign in ...]

      /* End job */
      [[EngagementAgent shared] endJob:@"sign_in"];
    }
    [...]

### <a name="report-errors-during-a-job"></a>Relatar erros durante um trabalho
Os erros podem estar relacionados a um trabalho em execução, em vez de serem relacionados a sessão do usuário atual.

**Exemplo:**

Suponha que você deseja relatar um erro durante o processo de logon:

    [...]
    -(void)signin
    {
      /* Start job */
      [[EngagementAgent shared] startJob:@"sign_in" extras:nil];

      BOOL success = NO;
      while (!success) {
        /* Try to sign in */
        NSError* error = nil;
        [self trySigin:&error];
        success = error == nil;

        /* If an error occured report it */
        if(!success)
        {
          [[EngagementAgent shared] sendJobError:@"sign_in_error"
                         jobName:@"sign_in"
                          extras:[NSDictionary dictionaryWithObject:[error localizedDescription] forKey:@"error"]];

          /* Retry after a moment */
          [NSThread sleepForTimeInterval:20];
        }
      }

      /* End job */
      [[EngagementAgent shared] endJob:@"sign_in"];
    };
    [...]

### <a name="events-during-a-job"></a>Eventos durante um trabalho
Os erros podem estar relacionados a um trabalho em execução, em vez de serem relacionados a sessão do usuário atual.

**Exemplo:**

Suponha que temos uma rede social e usamos um trabalho para relatar o tempo total durante o qual o usuário está conectado ao servidor. O usuário pode receber mensagens de seus amigos, esse é um evento de trabalho.

    [...]
    - (void) signin
    {
      [...Sign in code...]
      [[EngagementAgent shared] startJob:@"connection" extras:nil];
    }
    [...]
    - (void) signout
    {
      [...Sign out code...]
      [[EngagementAgent shared] endJob:@"connection"];
    }
    [...]
    - (void) onMessageReceived
    {
      [...Notify user...]
      [[EngagementAgent shared] sendJobEvent:@"connection" jobName:@"message_received" extras:nil];
    }
    [...]

## <a name="extra-parameters"></a>Parâmetros adicionais
Os dados arbitrários podem ser anexados aos eventos, erros, atividades e trabalhos.

Esses dados podem ser estruturados, eles usam a classe NSDictionary do iOS.

Observe que os extras podem conter `arrays(NSArray, NSMutableArray)`, `numbers(NSNumber class)`, `strings(NSString, NSMutableString)`, `urls(NSURL)`, `data(NSData, NSMutableData)` ou outras instâncias de `NSDictionary`. 

> [!NOTE]
> Esse parâmetro adicional é serializado em JSON. Se você quiser passar objetos diferentes dos descritos acima, você deve implementar o método a seguir em sua classe:
> 
> -(NSString*)JSONRepresentation;
> 
> O método deve retornar uma representação JSON de seu objeto.
> 
> 

### <a name="example"></a>Exemplo
    NSMutableDictionary* extras = [NSMutableDictionary dictionaryWithCapacity:2];
    [extras setObject:[NSNumber numberWithInt:123] forKey:@"video_id"];
    [extras setObject:@"http://foobar.com/blog" forKey:@"ref_click"];
    [[EngagementAgent shared] sendEvent:@"video_clicked" extras:extras];

### <a name="limits"></a>Limites
#### <a name="keys"></a>simétricas
Cada chave no `NSDictionary` deve corresponder a seguinte expressão regular:

`^[a-zA-Z][a-zA-Z_0-9]*`

Isso significa que as chaves devem começar com pelo menos uma letra, seguida por letras, dígitos ou sublinhados (\_).

#### <a name="size"></a>Tamanho
Os adicionais estão limitados à **1024** caracteres por chamada (uma vez codificado no JSON pelo agente de Engagement).

No exemplo anterior, o JSON enviado para o servidor tem 58 caracteres:

    {"ref_click":"http:\/\/foobar.com\/blog","video_id":"123"}

## <a name="reporting-application-information"></a>Relatório de informações de aplicativo
Você pode relatar manualmente informações (ou quaisquer outras informações específicas do aplicativo) de controle usando a função `sendAppInfo:` .

Observe que essas informações podem ser enviadas de forma incremental: somente o último valor para uma determinada chave será mantido por um determinado dispositivo.

Como os adicionais de evento, a classe `NSDictionary` é usada para abstrair as informações do aplicativo, observe que matrizes ou subdicionários serão tratados como cadeias de caracteres simples (usando a serialização JSON).

**Exemplo:**

    NSMutableDictionary* appInfo = [NSMutableDictionary dictionaryWithCapacity:2];
    [appInfo setObject:@"female" forKey:@"gender"];
    [appInfo setObject:@"1983-12-07" forKey:@"birthdate"]; // December 7th 1983
    [[EngagementAgent shared] sendAppInfo:appInfo];

### <a name="limits"></a>Limites
#### <a name="keys"></a>simétricas
Cada chave no `NSDictionary` deve corresponder a seguinte expressão regular:

`^[a-zA-Z][a-zA-Z_0-9]*`

Isso significa que as chaves devem começar com pelo menos uma letra, seguida por letras, dígitos ou sublinhados (\_).

#### <a name="size"></a>Tamanho
Os adicionais estão limitados à **1024** caracteres por chamada (uma vez codificado no JSON pelo agente de Engagement).

No exemplo anterior, o JSON enviado para o servidor tem 44 caracteres:

    {"birthdate":"1983-12-07","gender":"female"}
