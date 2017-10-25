---
title: "Integração do SDK do Android do Azure Mobile Engagement"
description: "Atualizações e procedimentos mais recentes para o SDK do Android do Azure Mobile Engagement"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: a5487793-1a12-4f6c-a1cf-587c5a671e6b
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 35bd92e52b7a02f58620a03156902f9f91be57ae
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-integrate-engagement-on-android"></a>Como integrar o Engagement ao Android
> [!div class="op_single_selector"]
> * [Windows Universal](mobile-engagement-windows-store-integrate-engagement.md)
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
> * [iOS](mobile-engagement-ios-integrate-engagement.md)
> * [Android](mobile-engagement-android-integrate-engagement.md)
> 
> 

Este procedimento descreve a maneira mais simples de ativar as funções de Análise e Monitoramento do Engagement em seu aplicativo Android.

> [!IMPORTANT]
> O nível mínimo de API do Android SDK deve ser 10 ou superior (Android 2.3.3 ou superior).
> 
> 

As etapas a seguir são suficientes para ativar o relatório de logs necessário para calcular todas as estatísticas referentes a Usuários, Sessões, Atividades, Falhas e Suporte Técnico. O relatório de logs necessários para calcular outras estatísticas, como Trabalhos, Erros e Eventos deve ser feito manualmente usando a API do Engagement (consulte [Como usar a marcação avançada de API do Mobile Engagement em seu Android](mobile-engagement-android-use-engagement-api.md) já que essas estatísticas são dependentes do aplicativo.

## <a name="embed-the-engagement-sdk-and-service-into-your-android-project"></a>Incorpore o SDK e serviço do Engagement em seu projeto Android
Baixe o Android SDK [aqui](https://aka.ms/vq9mfn) Obtenha `mobile-engagement-VERSION.jar` e coloque-as na pasta `libs` do seu projeto Android (crie a pasta de bibliotecas se ela ainda não existir).

> [!IMPORTANT]
> Se você compilar seu pacote de aplicativo com o ProGuard, você precisa manter algumas classes. Você pode usar o seguinte trecho de código de configuração:
> 
> -manter classe pública * extends android.os.IInterface -keep class com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity$EngagementReachContentJS {
> 
> <methods>; }
> 
> 

Especifique a cadeia de conexão do Engagement chamando o método a seguir na atividade de inicializador:

            EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
            engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
            EngagementAgent.getInstance(this).init(engagementConfiguration);

A cadeia de conexão para o seu aplicativo é exibida no Portal do Azure.

* Se estiver faltando, adicione as seguintes permissões do Android (antes da marca `<application>`):
  
          <uses-permission android:name="android.permission.INTERNET"/>
          <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
* Adicione a seção a seguir (entre as marcas `<application>` e `</application>`):
  
          <service
            android:name="com.microsoft.azure.engagement.service.EngagementService"
            android:exported="false"
            android:label="<Your application name>Service"
            android:process=":Engagement"/>
* Substitua `<Your application name>` pelo nome do seu aplicativo.

> [!TIP]
> A chave `android:label` permite que você escolha o nome do serviço do Engagement como ele aparecerá para os usuários finais na tela "Serviços em execução" dos seus respectivos telefones. É recomendável definir esse atributo para `"<Your application name>Service"` (por exemplo, `"AcmeFunGameService"`).
> 
> 

Especificar o atributo `android:process` garante que o serviço do Engagement será executado em seu próprio processo (executando o Engagement no mesmo processo que seu aplicativo fará o thread/interface de usuário principal potencialmente menos responsivos).

> [!NOTE]
> Qualquer código que você colocar em `Application.onCreate()` e outros retornos de chamada do aplicativo serão executados para todos os processos de seu aplicativo, incluindo o serviço Engagement. Ele pode ter efeitos colaterais indesejados (como alocações desnecessárias e threads no processo do Engagement, serviços ou receptores de difusão duplicados).
> 
> 

Se você substituir `Application.onCreate()`, recomenda-se adicionar o seguinte trecho de código no início da sua função `Application.onCreate()`:

             public void onCreate()
             {
               if (EngagementAgentUtils.isInDedicatedEngagementProcess(this))
                 return;

               ... Your code...
             }

Você pode fazer a mesma coisa para `Application.onTerminate()`, `Application.onLowMemory()` e `Application.onConfigurationChanged(...)`.

Você também pode estender `EngagementApplication` em vez de ampliar `Application`: o retorno de chamada `Application.onCreate()` faz a verificação do processo e chama `Application.onApplicationProcessCreate()` somente se o processo atual não for aquele que hospeda o serviço do Engagement. As mesmas regras se aplicam para os outros retornos de chamada.

## <a name="basic-reporting"></a>Relatórios básicos
### <a name="recommended-method-overload-your-activity-classes"></a>Método recomendado: sobrecarregar suas classes `Activity`
Para ativar o relatório de todos os logs exigidos pelo Engagement para calcular os Usuários, Sessões, Atividades, Falhas e Estatísticas técnicas, você só precisa fazer com que todas as suas subclasses `*Activity` herdem classes `Engagement*Activity` correspondentes (por exemplo, se sua atividade herdada estende `ListActivity`, faça com que ela estenda `EngagementListActivity`).

**Sem o Engagement :**

            package com.company.myapp;

            import android.app.Activity;
            import android.os.Bundle;

            public class MyApp extends Activity
            {
              @Override
              public void onCreate(Bundle savedInstanceState)
              {
                super.onCreate(savedInstanceState);
                setContentView(R.layout.main);
              }
            }

**Com o Engagement :**

            package com.company.myapp;

            import com.microsoft.azure.engagement.activity.EngagementActivity;
            import android.os.Bundle;

            public class MyApp extends EngagementActivity
            {
              @Override
              public void onCreate(Bundle savedInstanceState)
              {
                super.onCreate(savedInstanceState);
                setContentView(R.layout.main);
              }
            }

> [!IMPORTANT]
> Ao usar `EngagementListActivity` ou `EngagementExpandableListActivity`, certifique-se de que qualquer chamada para `requestWindowFeature(...);` seja feita antes da chamada para `super.onCreate(...);`, caso contrário, ocorrerá uma falha.
> 
> 

Você pode encontrar essas classes na pasta `src` e copiá-las para seu projeto. As classes também estão no **JavaDoc**.

### <a name="alternate-method-call-startactivity-and-endactivity-manually"></a>Método alternativo: chame `startActivity()` e `endActivity()` manualmente
Se você não pode ou não quer sobrecarregar as suas classes `Activity`, é possível iniciar suas atividades chamando diretamente os métodos de `EngagementAgent`.

> [!IMPORTANT]
> O SDK do Android nunca chama o método `endActivity()`, mesmo quando o aplicativo é fechado (no Android, aplicativos nunca são, na verdade, fechados). Dessa forma, é *ALTAMENTE* recomendável chamar o método `startActivity()` no retorno de chamada `onResume` de *TODAS* as suas atividades, e o método `endActivity()` no retorno de chamada `onPause()` de *TODAS* as suas atividades. Essa é a única maneira de certificar-se de que as sessões não serão perdidas. Se ocorrer perda de uma sessão, o serviço Engagement nunca se desconectará do back-end do Engagement (desde que o serviço permaneça conectado enquanto houver uma sessão pendente).
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

Este exemplo é muito semelhante à classe `EngagementActivity` e suas variantes, cujo código-fonte é fornecido na pasta `src`.

## <a name="test"></a>Teste
Agora, verifique se a integração ao executar seu aplicativo móvel em um dispositivo ou emulador e verifique se ele registra uma sessão na guia Monitor.

As seções a seguir são opcionais.

## <a name="location-reporting"></a>Relatórios de local
Se quiser que locais sejam informados, você precisa adicionar algumas linhas de configuração (entre as marcas `<application>` e `</application>`).

### <a name="lazy-area-location-reporting"></a>Relatórios de local de área lenta
O relatório de local de área lenta permite relatar o país, a região e a localidade associados aos dispositivos. Esse tipo de relatório de local usa apenas os locais de rede (com base na ID da célula ou WIFI). A área de dispositivo é relatada no máximo uma vez por sessão. O GPS nunca é usado e, portanto, esse tipo de relatório de local tem pouco impacto (ou quase nenhum) sobre a bateria.

As áreas relatadas são usadas para computar as estatísticas geográficas sobre usuários, sessões, eventos e erros. Elas também podem ser usadas como critério nas campanhas do Reach.

Para habilitar o relatório de localização de área simples, você pode fazer isso usando a configuração mencionada anteriormente neste procedimento:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setLazyAreaLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

Você também precisa adicionar a permissão a seguir, se estiver ausente:

            <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>

Ou pode continuar usando ``ACCESS_FINE_LOCATION`` se você já usa em seu aplicativo.

### <a name="real-time-location-reporting"></a>Relatórios de local em tempo real
Os relatórios de local em tempo real permitem relatar a latitude e a longitude associados aos dispositivos. Por padrão, esse tipo de relatório local usa apenas os locais de rede (com base na ID da célula ou WIFI) e o relatório estará disponível apenas quando o aplicativo for executado em primeiro plano (ou seja, durante uma sessão).

Os locais em tempo real *NÃO* são usados para calcular estatísticas. Sua única finalidade é permitir o uso do critério de isolamento geográfico em tempo real \<Reach-Audience-geofencing\> em campanhas de alcance.

Para habilitar o relatório local em tempo real, você pode fazer isso usando a configuração mencionada anteriormente neste procedimento:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

Você também precisa adicionar a permissão a seguir, se estiver ausente:

            <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>

Ou pode continuar usando ``ACCESS_FINE_LOCATION`` se você já usa em seu aplicativo.

#### <a name="gps-based-reporting"></a>Relatórios com base em GPS
Por padrão, os relatórios de local em tempo real usam apenas locais com base em rede. Para habilitar o uso do GPS com base em locais (que são muito mais precisos), use o objeto de configuração:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    engagementConfiguration.setFineRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

Você também precisa adicionar a permissão a seguir, se estiver ausente:

            <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"/>

#### <a name="background-reporting"></a>Relatório de segundo plano
Por padrão, os relatórios de local em tempo real ficam ativos apenas quando o aplicativo é executado em primeiro plano (ou seja, durante uma sessão). Para habilitar o relatório também em segundo plano, use o objeto de configuração:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    engagementConfiguration.setBackgroundRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

> [!NOTE]
> Quando o aplicativo é executado em segundo plano, somente locais baseados em rede são relatados, mesmo se você tiver habilitado o GPS.
> 
> 

O relatório de local de segundo plano será interrompido se o usuário reiniciar o dispositivo; você pode adicionar isso para fazê-lo reiniciar automaticamente no momento de inicialização:

            <receiver android:name="com.microsoft.azure.engagement.EngagementLocationBootReceiver"
               android:exported="false">
               <intent-filter>
                  <action android:name="android.intent.action.BOOT_COMPLETED" />
               </intent-filter>
            </receiver>

Você também precisa adicionar a permissão a seguir, se estiver ausente:

            <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />

### <a name="android-m-permissions"></a>Permissões do Android M
A partir do Android M, algumas permissões são gerenciadas em tempo de execução e precisam de aprovação do usuário.

As permissões de tempo de execução serão desativadas por padrão para novas instalações do aplicativo se você selecionar o nível 23 da API do Android. Caso contrário, elas serão ativadas por padrão.

O usuário pode habilitar/desabilitar essas permissões no menu de configurações do dispositivo. A desativação de permissões no menu do sistema interrompe os processos em segundo plano do aplicativo; esse é um comportamento do sistema e não tem nenhum impacto na capacidade de receber push em segundo plano.

No contexto do Mobile Engagement, as permissões que exigem aprovação em tempo de execução são:

* `ACCESS_COARSE_LOCATION`
* `ACCESS_FINE_LOCATION`
* `WRITE_EXTERNAL_STORAGE` (somente para direcionamento de nível 23 da API do Android para essa)

O armazenamento externo é usado apenas para o recurso Acessar visão global. Se você acha que pedir essa permissão aos usuários é incômodo, é possível removê-la se a usou somente para o Mobile Engagement, mas isso desabilitará o recurso de visão global.

Para os recursos de localização, você deve solicitar permissões para usuário usando uma caixa de diálogo padrão do sistema. Se o usuário aprovar, é necessário pedir a ``EngagementAgent`` para levar em conta essa alteração em tempo real (caso contrário, a alteração será processada na próxima vez que o usuário iniciar o aplicativo).

Aqui está um exemplo de código para usar em uma atividade do seu aplicativo para solicitar permissões e encaminhar o resultado se for positivo para ``EngagementAgent``:

    @Override
    public void onCreate(Bundle savedInstanceState)
    {
      /* Other code... */

      /* Request permissions */
      requestPermissions();
    }

    @TargetApi(Build.VERSION_CODES.M)
    private void requestPermissions()
    {
      /* Avoid crashing if not on Android M */
      if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.M)
      {
        /*
         * Request location permission, but this won't explain why it is needed to the user.
         * The standard Android documentation explains with more details how to display a rationale activity to explain the user why the permission is needed in your application.
         * Putting COARSE vs FINE has no impact here, they are part of the same group for runtime permission management.
         */
        if (checkSelfPermission(android.Manifest.permission.ACCESS_FINE_LOCATION) != PackageManager.PERMISSION_GRANTED)
          requestPermissions(new String[] { android.Manifest.permission.ACCESS_FINE_LOCATION }, 0);

        /* Only if you want to keep features using external storage */
        if (checkSelfPermission(android.Manifest.permission.WRITE_EXTERNAL_STORAGE) != PackageManager.PERMISSION_GRANTED)
          requestPermissions(new String[] { android.Manifest.permission.WRITE_EXTERNAL_STORAGE }, 1);
      }
    }

    @Override
    public void onRequestPermissionsResult(int requestCode, String[] permissions, int[] grantResults)
    {
      /* Only a positive location permission update requires engagement agent refresh, hence the request code matching from above function */
      if (requestCode == 0 && grantResults[0] == PackageManager.PERMISSION_GRANTED)
        getEngagementAgent().refreshPermissions();
    }

## <a name="advanced-reporting"></a>Relatórios avançados
Opcionalmente, se deseja relatar trabalhos, erros e eventos específicos do aplicativo, você precisa usar a API do Engagement por meio dos métodos da classe `EngagementAgent` . Um objeto dessa classe pode ser recuperado chamando o método estático `EngagementAgent.getInstance()` .

A API do Engagement permite usar todos os recursos avançados do Engagement e é detalhada em Como usar a API do Engagement em Android (além da documentação técnica da classe `EngagementAgent` ).

## <a name="advanced-configuration-in-androidmanifestxml"></a>Configuração avançada (em Androidmanifest.xml)
### <a name="wake-locks"></a>Bloqueios de ativação
Se quiser ter certeza de que as estatísticas são enviadas em tempo real ao usar Wifi ou quando a tela estiver desligada, adicione a seguinte permissão opcional:

            <uses-permission android:name="android.permission.WAKE_LOCK"/>

### <a name="crash-report"></a>Relatório de falha
Se você deseja desabilitar relatórios de falha, adicione (entre as marcas `<application>` e `</application>`):

            <meta-data android:name="engagement:reportCrash" android:value="false"/>

### <a name="burst-threshold"></a>Limite de intermitência
Por padrão, o serviço Engagement reporta logs em tempo real. Se seu aplicativo reporta logs com muita frequência, é melhor armazenar os logs em buffer e relatá-los todos de uma vez em uma base de tempo normal (isso é chamado de "modo de intermitência"). Para fazer isso, adicione (entre as marcas `<application>` e `</application>`):

            <meta-data android:name="engagement:burstThreshold" android:value="{interval between too bursts (in milliseconds)}"/>

O modo de intermitência aumenta ligeiramente a vida útil da bateria, mas tem um impacto no Monitor do Engagement: a duração de todas as sessões e trabalhos será arredondada para o limite de intermitência (portanto, as sessões e trabalhos mais curtos do que o limite de intermitência podem não estar visíveis). É recomendável usar um limite de intermitência não maior que 30.000 (30s).

### <a name="session-timeout"></a>Tempo limite da sessão
Por padrão, uma sessão é encerrada 10s após o término de sua última atividade (que geralmente ocorre pressionando a tecla Página Inicial ou Voltar, definindo o telefone como ocioso ou indo diretamente para outro aplicativo). Isso é para evitar uma divisão de sessão cada vez que o usuário sair e retornar para o aplicativo rapidamente (o que pode acontecer quando ele pegar uma imagem, verificar uma notificação, etc.). Convém modificar esse parâmetro. Para fazer isso, adicione (entre as marcas `<application>` e `</application>`):

            <meta-data android:name="engagement:sessionTimeout" android:value="{session timeout (in milliseconds)}"/>

## <a name="disable-log-reporting"></a>Desabilitar o relatório de log
### <a name="using-a-method-call"></a>Usando uma chamada de método
Se desejar que o Engagement pare de enviar logs, você pode chamar:

            EngagementAgent.getInstance(context).setEnabled(false);

Essa chamada é persistente: ela utiliza um arquivo de preferências compartilhado.

Se o Engagement está ativo quando você chama essa função, pode levar um minuto para parar o serviço. No entanto, ele nem sequer abrirá o serviço na próxima vez que você iniciar o aplicativo.

Você pode habilitar o log de relatórios novamente chamando a mesma função com `true`.

### <a name="integration-in-your-own-preferenceactivity"></a>Integração em seu próprio `PreferenceActivity`
Em vez de chamar essa função, você também pode integrar esta configuração diretamente em seu arquivo existente `PreferenceActivity`.

Você pode configurar o Engagement para usar o arquivo de preferências (com o modo desejado) no arquivo `AndroidManifest.xml` com `application meta-data`:

* A chave `engagement:agent:settings:name` é usada para definir o nome do arquivo de preferências compartilhado.
* A chave `engagement:agent:settings:mode` é usada para definir o modo do arquivo de preferências compartilhado. Você deve usar o mesmo modo que em seu `PreferenceActivity`. O modo deve ser passado como um número: se você estiver usando uma combinação de sinalizadores constantes em seu código, verifique o valor total.

O Engagement sempre usa a chave booliana `engagement:key` dentro do arquivo de preferências para gerenciar esta configuração.

O exemplo a seguir de `AndroidManifest.xml` mostra os valores padrão:

            <application>
                [...]
                <meta-data
                  android:name="engagement:agent:settings:name"
                  android:value="engagement.agent" />
                <meta-data
                  android:name="engagement:agent:settings:mode"
                  android:value="0" />

Em seguida, você pode adicionar um `CheckBoxPreference` em seu layout de preferência como o seguinte:

            <CheckBoxPreference
              android:key="engagement:enabled"
              android:defaultValue="true"
              android:title="Use Engagement"
              android:summaryOn="Engagement is enabled."
              android:summaryOff="Engagement is disabled." />

<!-- URLs. -->
[Device API]: http://go.microsoft.com/?linkid=9876094
