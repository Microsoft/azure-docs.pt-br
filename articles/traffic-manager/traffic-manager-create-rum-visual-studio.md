---
title: Medidas Reais de Usuário para o Gerenciador de Tráfego do Azure com o Visual Studio Mobile Center | Microsoft Docs
description: Configurar seu aplicativo móvel desenvolvido usando o Visual Studio Mobile Center para enviar as Medidas Reais de Usuário ao Gerenciador de Tráfego
services: traffic-manager
documentationcenter: traffic-manager
author: KumudD
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/16/2018
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: 1a5b883a8c9688d4545c0e98c00f78a2e982a611
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60884017"
---
# <a name="how-to-send-real-user-measurements-to-traffic-manager-with-visual-studio-mobile-center"></a>Como enviar as Medidas Reais de Usuário ao Gerenciador de Tráfego com o Visual Studio Mobile Center

Você pode configurar seu aplicativo móvel desenvolvido usando o Visual Studio Mobile Center para enviar as Medidas Reais de Usuário ao Gerenciador de Tráfego, seguindo as etapas:

>[!NOTE]
> No momento, somente o Android dá suporte ao envio das Medidas Reais de Usuário para o Gerenciador de Tráfego.

Para configurar as Medidas Reais de Usuário, você precisa obter uma chave e instrumentar o aplicativo com o pacote de RUM.

## <a name="step-1-obtain-a-key"></a>Etapa 1: Obter uma chave
    
As medidas obtidas e enviadas para o Gerenciador de Tráfego do aplicativo cliente são identificadas pelo serviço usando uma cadeia de caracteres exclusiva, chamada de chave de RUM (Medidas Reais de Usuário). Você pode obter uma chave de RUM usando o portal do Azure, uma API REST ou as interfaces do PowerShell ou da CLI.

Para obter a chave de RUM pelo portal do Azure use o procedimento a seguir:
1. Em um navegador, entre no portal do Azure. Se você ainda não tiver uma conta, inscreva-se para uma avaliação gratuita de um mês.
2. Na barra de pesquisa do portal, pesquise o nome do perfil do Gerenciador de Tráfego que deseja modificar e, em seguida, clique no perfil do Gerenciador de Tráfego nos resultados exibidos.
3. Na página de perfil do Gerenciador de Tráfego, clique em **Medidas Reais de Usuário** em **Configurações**.
4. Clique em **Gerar Chave** para criar uma nova chave de RUM.
        
   ![Gerar chave de Medidas Reais de Usuário](./media/traffic-manager-create-rum-visual-studio/generate-rum-key.png)

   **Figura 1: Geração de chave de medidas de usuário real**

5. A página exibe a chave de RUM que é gerada e um snippet de código JavaScript que precisa ser inserido em sua página HTML.
 
   ![Código Javascript da chave de Medidas Reais de Usuário](./media/traffic-manager-create-rum-visual-studio/rum-key.png)

   **Figura 2: chave de Medidas de Usuário Reais e JavaScript de medidas**
 
6. Clique no botão **Copiar** para copiar a chave de RUM. 

## <a name="step-2-instrument-your-app-with-the-rum-package-of-mobile-center-sdk"></a>Etapa 2: Instrumentar seu aplicativo com o pacote de RUM do SDK do Mobile Center

Se você é novo no Visual Studio Mobile Center, visite seu [site](https://mobile.azure.com). Para obter instruções detalhadas sobre a integração do SDK, consulte [Getting Started with the Android SDK](https://docs.microsoft.com/mobile-center/sdk/getting-started/Android) (Introdução ao SDK do Android).

Para usar as Medidas Reais de Usuário, conclua o procedimento a seguir:

1.  Adicione o SDK ao projeto

    Durante a versão prévia do SDK do ATM RUM, você precisa referenciar explicitamente o repositório do pacote.

    No arquivo **app/build.gradle** adicione as seguintes linhas:

    ```groovy
    repositories {
        maven {
            url "https://dl.bintray.com/mobile-center/mobile-center-snapshot"
        }
    }
    ```
    No arquivo **app/build.gradle** adicione as seguintes linhas:

    ```groovy
    dependencies {
     
        def mobileCenterSdkVersion = '0.12.1-16+3fe5b08'
        compile "com.microsoft.azure.mobile:mobile-center-rum:${mobileCenterSdkVersion}"
    }
    ```

2. Iniciar o SDK

    Abra a classe de atividade principal do aplicativo e adicione as seguintes instruções de importação:

    ```java
    import com.microsoft.azure.mobile.MobileCenter;
    import com.microsoft.azure.mobile.rum.RealUserMeasurements;
    ```

    Procure o retorno de chamada `onCreate` no mesmo arquivo e adicione o seguinte código:

    ```java
    RealUserMeasurements.setRumKey("<Your RUM Key>");
    MobileCenter.start(getApplication(), "<Your Mobile Center AppSecret>", RealUserMeasurements.class);
    ```

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre as [Medidas Reais de Usuário](traffic-manager-rum-overview.md)
- Saiba [como funciona o Gerenciador de Tráfego](traffic-manager-overview.md)
- Saiba mais sobre o [Mobile Center](https://docs.microsoft.com/mobile-center/)
- [Inscreva-se](https://mobile.azure.com) no Mobile Center
- Saiba mais sobre os [métodos de roteamento do tráfego](traffic-manager-routing-methods.md) com suporte pelo Gerenciador de Tráfego
- Aprenda a [criar um perfil do Gerenciador de Tráfego](traffic-manager-create-profile.md)

