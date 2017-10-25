---
title: "Procedimentos de atualização do SDK do Windows Phone Silverlight"
description: "Procedimentos de atualização do SDK do Windows Phone Silverlight para o Azure Mobile Engagement"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 87130026-9759-4659-9184-788a3627a165
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: na
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: f87f65788075c7f4067e77946e1bcbc8f3709317
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="windows-phone-silverlight-sdk-upgrade-procedures"></a>Procedimentos de atualização do SDK do Windows Phone Silverlight
Se você já tiver integrado uma versão anterior do SDK no seu aplicativo, você deve considerar os seguintes pontos ao atualizar o SDK.

Você precisará seguir vários procedimentos se perdeu várias versões do SDK. Por exemplo, se você migrar do 0.10.1 para 0.11.0 você tem que primeiro seguir o procedimento "de 0.9.0 a 0.10.1” e depois o procedimento "de 0.10.1 a 0.11.0".

## <a name="from-200-to-330"></a>De 2.0.0 a 3.3.0
### <a name="test-logs"></a>Logs de teste
Agora, os logs do console produzidos pelo SDK podem ser habilitados/desabilitados/filtrados. Para personalizar esse recurso, atualize a propriedade `EngagementAgent.Instance.TestLogEnabled` para um dos valores disponíveis na enumeração `EngagementTestLogLevel`, por exemplo:

            EngagementAgent.Instance.TestLogLevel = EngagementTestLogLevel.Verbose;
            EngagementAgent.Instance.Init();

## <a name="from-111-to-200"></a>De 1.1.1 a 2.0.0
O seguinte descreve como migrar uma integração do SDK do serviço Capptain oferecido pelo Capptain SAS em um aplicativo acionado pelo Azure Mobile Engagement. 

> [!IMPORTANT]
> O Capptain e o Mobile Engagement não são os mesmos serviços e o procedimento fornecido abaixo destaca apenas como migrar o aplicativo cliente. Migrar o SDK no aplicativo NÃO migrará os dados dos servidores Capptain para os servidores do Mobile Engagement
> 
> 

Se você estiver migrando de uma versão anterior, consulte o site do Capptain para migrar primeiro para a 1.1.1 e depois aplicar o procedimento a seguir

### <a name="nuget-package"></a>Pacote NuGet
Substitua **Capptain.WindowsPhone** pelo pacote Nuget **MicrosoftAzure.MobileEngagement**.

### <a name="applying-mobile-engagement"></a>Aplicando o Mobile Engagement
O SDK usa o termo `Engagement`. Você precisa atualizar seu projeto para corresponder a esta alteração.

Você precisa desinstalar o pacote nuget do Capptain atual. Considere que todas as alterações na pasta de recursos Capptain serão removidas. Se você quiser manter esses arquivos, então faça uma cópia deles.

Depois disso, instale o novo pacote nuget do Engagement do Microsoft Azure em seu projeto. Você pode encontrá-lo diretamente no [Nuget](http://www.nuget.org/packages/MicrosoftAzure.MobileEngagement). Essa ação substitui todos os arquivos de recursos usados pelo Engagement e adiciona a nova DLL do Engagement às suas referências do projeto.

Você precisa limpar as referências do projeto, excluindo as referências de Capptain DLL. Se você não fizer isso, a versão do Capptain entrará em conflito e ocorrerão erros.

Se você personalizou os recursos do Capptain, copie o conteúdo de arquivos antigos e cole-os em novos arquivos do Engagement. Observe que os arquivos xaml e cs devem ser atualizados.

Quando essas etapas forem concluídas, você só precisará substituir as referências antigas do Capptain por novas referências do Engagement.

1. Todos os namespaces Capptain precisam ser atualizados.
   
    Antes da migração:
   
        using Capptain.Agent;
        using Capptain.Reach;
   
    Após a migração:
   
        using Microsoft.Azure.Engagement;
2. Todas as classes Capptain que contêm "Capptain" devem conter “Engagement".
   
    Antes da migração:
   
        public sealed partial class MainPage : CapptainPage
        {
          protected override string GetCapptainPageName()
          {
            return "Capptain Demo";
          }
          ...
        }
   
    Após a migração:
   
        public sealed partial class MainPage : EngagementPage
        {
          protected override string GetEngagementPageName()
          {
            return "Engagement Demo";
          }
          ...
        }
3. Para os arquivos xaml o namespace e atributos Capptain também se alteram.
   
    Antes da migração:
   
        <capptain:CapptainPage
        ...
        xmlns:capptain="clr-namespace:Capptain.Agent;assembly=Capptain.Agent.WP"
        ...
        </capptain:CapptainPage>
   
    Após a migração:
   
        <engagement:EngagementPage
        ...
        xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP"
        ...
        </engagement:EngagementPage>
4. Para outros recursos como as imagens do Capptain, observe que eles também foram renomeados para usar “Engagement".

### <a name="application-id--sdk-key"></a>ID do aplicativo / chave do SDK
O Engagement usa uma cadeia de conexão. Você não precisa especificar uma ID de aplicativo e uma chave do SDK com o Mobile Engagement, você só precisa especificar uma cadeia de conexão. Você pode configurá-la em seu arquivo EngagementConfiguration.

A configuração do Engagement pode ser definida no arquivo `Resources\EngagementConfiguration.xml` do seu projeto.

Edite esse arquivo para especificar:

* A cadeia de conexão do aplicativo entre as marcas `<connectionString>` and `<\connectionString>`.

Se você quiser especificá-lo em tempo de execução, você pode chamar o método a seguir antes da inicialização do agente do Engagement:

        /* Engagement configuration. */
        EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
        engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

        /* Initialize Engagement angent with above configuration. */
        EngagementAgent.Instance.Init(engagementConfiguration);

A cadeia de conexão do seu aplicativo é exibida no Portal Clássico do Azure.

### <a name="items-name-change"></a>Alteração do nome de itens
Todos os itens chamados *capptain* foram nomeados como *engagement*. Da mesma forma de *Capptain* para *Engagement*.

Exemplos de itens do Capptain usados normalmente :

* CapptainConfiguration agora denominado EngagementConfiguration
* CapptainAgent agora denominado EngagementAgent
* CapptainReach agora denominado EngagementReach
* CapptainHttpConfig agora denominado EngagementHttpConfig
* GetCapptainPageName agora denominado GetEngagementPageName

Observe que renomear também afeta métodos substituídos.

