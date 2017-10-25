---
title: Como usar o API do Engagement no Windows Phone Silverlight
description: Como usar o API do Engagement no Windows Phone Silverlight
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: ae2ba2e8-f75b-4dee-a164-a7dd65d35a23
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: na
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: ec8b6c13ea052c8063dfde4321cdd286ab6cb817
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-the-engagement-api-on-windows-phone-silverlight"></a>Como usar o API do Engagement no Windows Phone Silverlight
Este documento é um complemento para o documento [Como integrar o Mobile Engagement em seu aplicativo do Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md). Ele fornece detalhes aprofundados sobre como usar a API do Engagement para relatar as estatísticas do aplicativo.

Se você desejar apenas relatar as sessões, atividades, falhas e informações técnicas do seu aplicativo, então a maneira mais simples é fazer todas as suas subclasses `PhoneApplicationPage` herdarem da classe `EngagementPage`.

Se você quiser fazer mais, por exemplo, se você precisar reportar eventos, erros e trabalhos específicos do aplicativo ou se você tiver que relatar as atividades do seu aplicativo de maneira diferente de um implementado nas classes `EngagementPage`, você precisará usar a API do Engagement.

A API do Engagement é fornecida pela classe `EngagementAgent` . Você pode obter acesso a esses métodos por meio de `EngagementAgent.Instance`.

Mesmo se o módulo de agente não foi inicializado, cada chamada para a API é adiada e será executada novamente quando o agente está disponível.

## <a name="engagement-concepts"></a>Conceitos de Engagement
As seguintes partes refinam os Conceitos de Mobile Engagement para a plataforma do Windows Phone.

### <a name="session-and-activity"></a>`Session` e `Activity`
Uma *atividade* geralmente está associada a uma página de aplicativo. Isso significa que a *atividade* iniciará quando a página for exibida e será interrompida quando a página for fechada: esse é o caso quando o SDK do Engagement é integrado usando a classe `EngagementPage`.

Mas as *atividades* também podem ser controladas manualmente usando a API do Engagement. Isso permite dividir uma determinada página em várias partes secundárias para obter mais detalhes sobre o uso desta página (por exemplo para saber com que frequência e por quanto tempo as caixas de diálogo são usadas dentro desta página).

## <a name="reporting-activities"></a>Relatório de atividades
### <a name="user-starts-a-new-activity"></a>O usuário inicia uma nova atividade
#### <a name="reference"></a>Referência
            void StartActivity(string name, Dictionary<object, object> extras = null)

É necessário chamar `StartActivity()` sempre que houver alterações de atividade do usuário. A primeira chamada para essa função inicia uma nova sessão de usuário.

> [!IMPORTANT]
> O SDK chama automaticamente o método EndActivity quando o aplicativo é fechado. Portanto, é ALTAMENTE recomendado chamar o método StartActivity sempre que a atividade do usuário for alterada para NUNCA chamar o método EndActivity, visto que chamar esse método força o encerramento da sessão atual.
> 
> 

#### <a name="example"></a>Exemplo
            EngagementAgent.Instance.StartActivity("main", new Dictionary<object, object>() {{"example", "data"}});

### <a name="user-ends-his-current-activity"></a>O usuário encerra sua atividade atual
#### <a name="reference"></a>Referência
            void EndActivity()

É necessário chamar `EndActivity()` pelo menos uma vez quando o usuário conclui sua última atividade. Isso informa o SDK do Engagement que o usuário está ocioso no momento e que a sessão do usuário precisa ser fechada quando o tempo limite da sessão expirar (se você chamar `StartActivity()` antes de expirar o tempo limite da sessão, a sessão será simplesmente retomada).

#### <a name="example"></a>Exemplo
            EngagementAgent.Instance.EndActivity();

## <a name="reporting-jobs"></a>Relatório de trabalhos
### <a name="start-a-job"></a>Iniciar um trabalho
#### <a name="reference"></a>Referência
            void StartJob(string name, Dictionary<object, object> extras = null)

Você pode usar o trabalho para acompanhar determinadas tarefa por um período de tempo.

#### <a name="example"></a>Exemplo
            // An upload begins...

            // Set the extras
            var extras = new Dictionary<object, object>();
            extras.Add("title", "avatar");
            extras.Add("type", "image");

            EngagementAgent.Instance.StartJob("uploadData", extras);

### <a name="end-a-job"></a>Encerrar um trabalho
#### <a name="reference"></a>Referência
            void EndJob(string name)

Assim que uma tarefa controlada por um trabalho foi finalizada, você deve chamar o método EndJob para esse trabalho, fornecendo o nome do trabalho.

#### <a name="example"></a>Exemplo
            // In the previous section, we started an upload tracking with a job
            // Then, the upload ends

            EngagementAgent.Instance.EndJob("uploadData");

## <a name="reporting-events"></a>Eventos de relatório
Há três tipos de eventos :

* Eventos autônomos
* Eventos de sessão
* Eventos de trabalho

### <a name="standalone-events"></a>Eventos autônomos
#### <a name="reference"></a>Referência
            void SendEvent(string name, Dictionary<object, object> extras = null)

Eventos autônomos podem ocorrer fora do contexto de uma sessão.

#### <a name="example"></a>Exemplo
            EngagementAgent.Instance.SendEvent("event", extra);

### <a name="session-events"></a>Eventos de sessão
#### <a name="reference"></a>Referência
            void SendSessionEvent(string name, Dictionary<object, object> extras = null)

Eventos de sessão são geralmente usados para relatar as ações executadas por um usuário durante a sessão.

#### <a name="example"></a>Exemplo
**Sem dados :**

            EngagementAgent.Instance.SendSessionEvent("sessionEvent");

            // or

            EngagementAgent.Instance.SendSessionEvent("sessionEvent", null);

**Com dados :**

            Dictionary<object, object> extras = new Dictionary<object,object>();
            extras.Add("name", "data");
            EngagementAgent.Instance.SendSessionEvent("sessionEvent", extras);

### <a name="job-events"></a>Eventos de trabalho
#### <a name="reference"></a>Referência
            void SendJobEvent(string eventName, string jobName, Dictionary<object, object> extras = null)

Eventos de trabalho são geralmente usados para relatar as ações executadas por um usuário durante um trabalho.

#### <a name="example"></a>Exemplo
            EngagementAgent.Instance.SendJobEvent("eventName", "jobName", extras);

## <a name="reporting-errors"></a>Erros de relatório
Há três tipos de erros:

* Erros autônomos
* Erros de sessão
* Erros de trabalho

### <a name="standalone-errors"></a>Erros autônomos
#### <a name="reference"></a>Referência
            void SendError(string name, Dictionary<object, object> extras = null)

Ao contrário dos erros de sessão, os erros autônomos podem ocorrer fora do contexto de uma sessão.

#### <a name="example"></a>Exemplo
            EngagementAgent.Instance.SendError("errorName", extras);

### <a name="session-errors"></a>Erros de sessão
#### <a name="reference"></a>Referência
            void SendSessionError(string name, Dictionary<object, object> extras = null)

Erros de sessão são geralmente usados para relatar os erros que afetam o usuário durante a sessão.

#### <a name="example"></a>Exemplo
            EngagementAgent.Instance.SendSessionError("errorName", extra);

### <a name="job-errors"></a>Erros de trabalho
#### <a name="reference"></a>Referência
            void SendJobError(string errorName, string jobName, Dictionary<object, object> extras = null)

Os erros podem estar relacionados a um trabalho em execução, em vez de serem relacionados a sessão do usuário atual.

#### <a name="example"></a>Exemplo
            EngagementAgent.Instance.SendJobError("errorName", "jobname", extra);

## <a name="reporting-crashes"></a>Relatórios de falhas
O agente fornece dois métodos para lidar com falhas.

### <a name="send-an-exception"></a>Enviar uma exceção
#### <a name="reference"></a>Referência
            void SendCrash(Exception e, bool terminateSession = false)

#### <a name="example"></a>Exemplo
Você pode enviar uma exceção a qualquer momento chamando :

            EngagementAgent.Instance.SendCrash(aCatchedException);

Você também pode usar um parâmetro opcional para encerrar a sessão do engagement ao mesmo tempo que envia a falha. Para fazer isso, chame :

            EngagementAgent.Instance.SendCrash(new Exception("example"), terminateSession: true);

Se você fizer isso, os trabalhos e a sessão serão fechados apenas após o envio da falha.

### <a name="send-an-unhandled-exception"></a>Enviar uma exceção sem tratamento
#### <a name="reference"></a>Referência
            void SendCrash(ApplicationUnhandledExceptionEventArgs e)

O Engagement também fornece um método para enviar as exceções sem tratamento. Isso é especialmente útil quando usado dentro do manipulador de eventos do aplicativo UnhandledException do silverlight.

Esse método **SEMPRE** encerrará a sessão de engagement depois de ser chamado.

#### <a name="example"></a>Exemplo
Você pode usá-lo para implementar seu próprio manipulador UnhandledException (especialmente se você tiver desativado a recurso de relatório de falhas automático do Engagement). Por exemplo, no método `Application_UnhandledException` do arquivo `App.xaml.cs` :

            // In your App.xaml.cs file

            // Code to execute on Unhandled Exceptions
            private void Application_UnhandledException(object sender, ApplicationUnhandledExceptionEventArgs e)
            {
              // your own code

              EngagementAgent.Instance.SendCrash(e);
            }

## <a name="onactivated"></a>OnActivated
### <a name="reference"></a>Referência
            void OnActivated(ActivatedEventArgs e)

Quando o usuário navega para frente, para longe de um aplicativo, após o evento Deactivated ser gerado, o sistema operacional tenta colocar o aplicativo em um estado inativo. Depois, o aplicativo é a marcado para exclusão. Nesse processo, um aplicativo é encerrado, mas alguns dados sobre o estado do aplicativo e as páginas individuais dentro do aplicativo são preservados.

Você precisa inserir `EngagementAgent.Instance.OnActivated(e)` no método `Application_Activated` do arquivo App.xaml.cs para redefinir o Agente do Engagement quando o aplicativo foi marcado para a exclusão.

### <a name="example"></a>Exemplo
            // Inside your App.xaml.cs file

            // Code to execute when the application is activated (brought to foreground)
            // This code will not execute when the application is first launched
            private void Application_Activated(object sender, ActivatedEventArgs e)
            {
              EngagementAgent.Instance.OnActivated(e);
            }

## <a name="device-id"></a>Id do dispositivo
            String GetDeviceId()

Você pode obter a id do dispositivo do engagement chamando esse método.

## <a name="extras-parameters"></a>Parâmetros adicionais
Dados arbitrários podem ser anexados a um evento, um erro, uma atividade ou um trabalho. Esses dados podem ser estruturados usando um dicionário. Chaves e valores podem ser de qualquer tipo.

Dados adicionais são serializados para que se desejar inserir seu próprio tipo em adicionais, você precisará adicionar um contrato de dados para esse tipo.

### <a name="example"></a>Exemplo
Criamos uma nova classe "Person".

            using System.Runtime.Serialization;

            namespace Engagement.Agent
            {
              [DataContract]
              public class Person
              {
                public Person(string name, int age)
                {
                  Age = age;
                  Name = name;
                }

                // Properties

                [DataMember]
                public int Age
                {
                  get;
                  set;
                }

                [DataMember]
                public string Name
                {
                  get;
                  set; 
                }
              }
            }

Depois, adicionaremos uma instância `Person` para um arquivo extra.

            Person person = new Person("Engagement Haddock", 51);
            var extras = new Dictionary<object, object>();
            extras.Add("people", person);

            EngagementAgent.Instance.SendEvent("Event", extras);

> [!WARNING]
> Se você colocar outros tipos de objetos, verifique se o método ToString () é implementado para retornar uma cadeia de caracteres legível humana.
> 
> 

### <a name="limits"></a>Limites
#### <a name="keys"></a>simétricas
Cada chave no objeto deve corresponder à seguinte expressão regular:

`^[a-zA-Z][a-zA-Z_0-9]*$`

Isso significa que as chaves devem começar com pelo menos uma letra, seguida por letras, dígitos ou sublinhados (\_).

#### <a name="size"></a>Tamanho
Os arquivos extras estão limitados a **1024** caracteres por chamada.

## <a name="reporting-application-information"></a>Relatório de informações de aplicativo
### <a name="reference"></a>Referência
            void SendAppInfo(Dictionary<object, object> appInfos)

Você pode relatar manualmente informações (ou quaisquer outras informações específicas do aplicativo) de controle usando a função SendAppInfo().

Observe que essas informações podem ser enviadas de forma incremental: somente o último valor para uma determinada chave será mantido por um determinado dispositivo. Como extras de eventos, use um Dicionário\<object, object\> para anexar informações.

### <a name="example"></a>Exemplo
            Dictionary<object, object> appInfo = new Dictionary<object, object>()
            {
               {"subscription", "2013-12-07"},
               {"premium", "true"}
            };

            EngagementAgent.Instance.SendAppInfo(appInfo);

### <a name="limits"></a>Limites
#### <a name="keys"></a>simétricas
Cada chave no objeto deve corresponder à seguinte expressão regular:

`^[a-zA-Z][a-zA-Z_0-9]*$`

Isso significa que as chaves devem começar com pelo menos uma letra, seguida por letras, dígitos ou sublinhados (\_).

#### <a name="size"></a>Tamanho
As informações do aplicativo estão limitadas a **1024** caracteres por chamada.

No exemplo anterior, o JSON enviado para o servidor tem 44 caracteres:

            {"subscription":"2013-12-07","premium":"true"}

## <a name="logging"></a>Registro em log
### <a name="enable-logging"></a>Habilitar registro em log
O SDK pode ser configurado para gerar logs de teste no console do IDE.
Esses logs não são ativados por padrão. Para personalizar esse recurso, atualize a propriedade `EngagementAgent.Instance.TestLogEnabled` para um dos valores disponíveis na enumeração `EngagementTestLogLevel`, por exemplo:

            EngagementAgent.Instance.TestLogLevel = EngagementTestLogLevel.Verbose;
            EngagementAgent.Instance.Init();
