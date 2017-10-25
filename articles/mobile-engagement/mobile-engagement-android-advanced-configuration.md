---
title: "Configuração avançada do SDK do Azure Mobile Engagement para Android"
description: "Descreve as opções de configuração avançada, incluindo o Manifesto do Android com o SDK do Azure Mobile Engagement para Android"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 37d2c09a-86fa-473d-8987-c7e35a0eb3e8
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 10/04/2016
ms.author: piyushjo;ricksal
ms.openlocfilehash: 0301f71c76872714aa1bf727a6c21dd7a63db036
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="advanced-configuration-for-azure-mobile-engagement-android-sdk"></a>Configuração avançada do SDK do Azure Mobile Engagement para Android
> [!div class="op_single_selector"]
> * [Universal do Windows](mobile-engagement-windows-store-advanced-configuration.md)
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
> * [iOS](mobile-engagement-ios-integrate-engagement.md)
> * [Android](mobile-engagement-android-advanced-configuration.md)
>
>

Este procedimento descreve como definir várias opções de configuração para aplicativos Azure Mobile Engagement do Android.

## <a name="prerequisites"></a>Pré-requisitos
[!INCLUDE [Prereqs](../../includes/mobile-engagement-android-prereqs.md)]

## <a name="permission-requirements"></a>Requisitos de permissão
Algumas opções exigem permissões específicas, todas relacionadas aqui para referência, e alinhadas ao recurso específico. Adicionar essas permissões ao AndroidManifest.xml do projeto imediatamente anteriores ou posteriores à marcação `<application>` .

O código de permissão deve se parecer com o código a seguir, no qual você preenche a permissão apropriada da tabela a seguir.

    <uses-permission android:name="android.permission.[specific permission]"/>


| Permissão | Quando usado |
| --- | --- |
| INTERNET |Obrigatório. Para relatório básico |
| ACCESS_NETWORK_STATE |Obrigatório. Para relatório básico |
| RECEIVE_BOOT_COMPLETED |Obrigatório. Para mostrar o centro de notificações após a reinicialização do dispositivo |
| WAKE_LOCK |Recomendável. Habilita a coleta de dados ao usar Wi-Fi ou quando a tela estiver desligada |
| VIBRATE |Opcional. Habilita a vibração após o recebimento de notificações |
| DOWNLOAD_WITHOUT_NOTIFICATION |Opcional. Habilita a notificação de visão geral do Android |
| WRITE_EXTERNAL_STORAGE |Opcional. Habilita a notificação de visão geral do Android |
| ACCESS_COARSE_LOCATION |Opcional. Habilita o relatório de local em tempo real |
| ACCESS_FINE_LOCATION |Opcional. Habilita o relatório de local baseado em GPS |

A partir do Android M, [algumas permissões são gerenciadas em tempo de execução](mobile-engagement-android-location-reporting.md#android-m-permissions).

Se você já estiver usando o ``ACCESS_FINE_LOCATION``, não precisará usar o ``ACCESS_COARSE_LOCATION`` também.

## <a name="android-manifest-configuration-options"></a>Opções de configuração do manifesto do Android
### <a name="crash-report"></a>Relatório de falha
Para desabilitar relatórios de falha, adicione este código entre as marcas `<application>` e `</application>`:

    <meta-data android:name="engagement:reportCrash" android:value="false"/>

### <a name="burst-threshold"></a>Limite de intermitência
Por padrão, o serviço Engagement reporta logs em tempo real. Se os logs de relatório de seu aplicativo variam com muita frequência, convém armazená-los em buffer e relatá-los todos de uma vez em uma base de tempo normal (isso é chamado de "modo de disparo contínuo"). Para fazer isso, adicione este código entre as marcas `<application>` e `</application>`:

    <meta-data android:name="engagement:burstThreshold" android:value="{interval between too bursts (in milliseconds)}"/>

O modo de disparo contínuo aumenta ligeiramente a vida útil da bateria, mas tem um impacto no Monitor do Engagement: a duração de todas as sessões e trabalhos será arredondada para o limite de intermitência (portanto, as sessões e trabalhos mais curtos do que o limite de intermitência podem não estar visíveis). O limite de disparo contínuo não deve ser maior do que 30.000 (30 s).

### <a name="session-timeout"></a>Tempo limite da sessão
 Você pode finalizar uma atividade pressionando a tecla **Home** ou **Voltar**, definindo o telefone como ocioso ou acessando diretamente outro aplicativo. Por padrão, uma sessão será encerrada dez segundos após o término de sua última atividade. Isso serve para evitar uma divisão de sessão sempre que o usuário sair e retornar ao aplicativo rapidamente, o que pode acontecer quando ele pegar uma imagem, verificar uma notificação etc. Convém modificar esse parâmetro. Para fazer isso, adicione este código entre as marcas `<application>` e `</application>`:

    <meta-data android:name="engagement:sessionTimeout" android:value="{session timeout (in milliseconds)}"/>

## <a name="disable-log-reporting"></a>Desabilitar o relatório de log
### <a name="using-a-method-call"></a>Usando uma chamada de método
Se desejar que o Engagement pare de enviar logs, você pode chamar:

    EngagementAgent.getInstance(context).setEnabled(false);

Essa chamada é persistente: ela utiliza um arquivo de preferências compartilhado.

Se o Engagement estiver ativo quando você chamar essa função, a parada do serviço poderá demorar um minuto. No entanto, ele nem sequer abrirá o serviço na próxima vez que você iniciar o aplicativo.

Você pode habilitar o log de relatórios novamente chamando a mesma função com `true`.

### <a name="integration-in-your-own-preferenceactivity"></a>Integração em seu próprio `PreferenceActivity`
Em vez de chamar essa função, você também pode integrar esta configuração diretamente em seu arquivo existente `PreferenceActivity`.

Você pode configurar o Engagement para usar o arquivo de preferências (com o modo desejado) no arquivo `AndroidManifest.xml` com `application meta-data`:

* A chave `engagement:agent:settings:name` é usada para definir o nome do arquivo de preferências compartilhado.
* A chave `engagement:agent:settings:mode` é usada para definir o modo do arquivo de preferências compartilhado. Use o mesmo modo que em seu `PreferenceActivity`. O modo deve ser passado como um número: se você estiver usando uma combinação de sinalizadores constantes em seu código, verifique o valor total.

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
