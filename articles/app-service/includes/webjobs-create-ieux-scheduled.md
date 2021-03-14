---
author: ggailey777
ms.assetid: af01771e-54eb-4aea-af5f-f883ff39572b
ms.topic: include
ms.date: 10/16/2018
ms.title: include
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: ef0aa8ba1983ca30fd44c27fe570b6b5f51733a5
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101744210"
---
## <a name="create-a-scheduled-webjob"></a><a name="CreateScheduledCRON"></a> Criar um WebJob agendado


1. Foi obtido com a [portal do Azure](https://portal.azure.com).
1. Vá para o **serviço de aplicativo** do seu <abbr title="Seu recurso de aplicativo pode ser um aplicativo Web, um aplicativo de API ou um aplicativo móvel.">Recurso de aplicativo</abbr>.
1. Selecione **WebJobs**.

   ![Selecionar o WebJobs](../media/web-sites-create-web-jobs/select-webjobs.png)

1. Na página **WebJobs**, selecione **Adicionar**.

    ![Página do WebJob](../media/web-sites-create-web-jobs/wjblade.png)

1. Use as configurações **Adicionar WebJob**, conforme especificado na tabela.

    ![Página Adicionar WebJob](../media/web-sites-create-web-jobs/addwjscheduled.png)
    
    | Configuração      | Valor de exemplo   |
    | ------------ | ----------------- | 
    | <abbr title="Um nome que seja exclusivo em um aplicativo do Serviço de Aplicativo. Deve começar com uma letra ou um número e não pode conter caracteres especiais além de `-` e `_` .">Name</a> | myScheduledWebJob |  |
    | <abbr title="Um arquivo *.zip* que contém o executável ou o arquivo de script, bem como os arquivos de suporte necessários para executar o programa ou o script.">Carregar arquivos</abbr> | ConsoleApp.zip |
    | <abbr title="Os tipos incluem contínuo, disparado.">Tipo</abbr> | Disparado |
    | <abbr title="Para que o agendamento funcione de modo confiável, habilite o recurso AlwaysOn. O AlwaysOn está disponível apenas nos tipos de preço Básico, Standard e Premium.">Gatilhos</a> | Agendado |
    | Expressão CRON</a> | 0 0/20 * * * * | 
    
    <br>
    
    <details>
     <summary>Saiba mais sobre expressões CRON</summary>
     <a name="#ncrontab-expressions"></a>
    
     Você pode inserir uma [expressão NCRONTAB](../../azure-functions/functions-bindings-timer.md#ncrontab-expressions) no portal ou incluir um `settings.job` arquivo na raiz do seu arquivo WebJob *. zip* , como no exemplo a seguir:
     
     ```json
     {
         "schedule": "0 */15 * * * *"
     }
     ```
     
     Para saber mais, consulte [agendando um WebJob disparado](../webjobs-dotnet-deploy-vs.md#scheduling-a-triggered-webjob).
     
     [!INCLUDE [webjobs-cron-timezone-note](../../../includes/webjobs-cron-timezone-note.md)]
     </details>
     <br>

1. Clique em **OK**.

    O novo WebJob é exibido na página **WebJobs**.
    
    ![Lista de WebJobs](../media/web-sites-create-web-jobs/listallwebjobs.png)
