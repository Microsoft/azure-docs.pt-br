---
author: ggailey777
ms.assetid: af01771e-54eb-4aea-af5f-f883ff39572b
ms.topic: include
ms.date: 10/16/2018
ms.title: include
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 33dc766643355a5f5ebb6138e000595fd1bfe6fc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101744208"
---
## <a name="create-a-continuous-webjob"></a><a name="CreateContinuous"></a> Criar um WebJob contínuo

1. Foi obtido com a [portal do Azure](https://portal.azure.com).
1. Vá para o **serviço de aplicativo** do seu <abbr title="Seu recurso de aplicativo pode ser um aplicativo Web, um aplicativo de API ou um aplicativo móvel.">Recurso de aplicativo</abbr>.
1. Selecione **WebJobs**.

   ![Selecionar o WebJobs](../media/web-sites-create-web-jobs/select-webjobs.png)

1. Na página **WebJobs**, selecione **Adicionar**.

    ![Página do WebJob](../media/web-sites-create-web-jobs/wjblade.png)

1. Use as configurações **Adicionar WebJob**, conforme especificado na tabela.

   ![Captura de tela que mostra as configurações de adicionar WebJob que você precisa configurar.](../media/web-sites-create-web-jobs/addwjcontinuous.png)

   | Configuração      | Valor de exemplo   | 
   | ------------ | ----------------- | 
   | <abbr title="Um nome que seja exclusivo em um aplicativo do Serviço de Aplicativo. Deve começar com uma letra ou um número e não pode conter caracteres especiais além de `-` e `_` .">Nome</abbr> | myContinuousWebJob | 
   | <abbr title=" Um arquivo *.zip* que contém o executável ou o arquivo de script, bem como os arquivos de suporte necessários para executar o programa ou o script.">Carregar arquivos</abbr> | ConsoleApp.zip |
   | <abbr title="Os tipos incluem contínuo, disparado.">Type</abbr> | Contínuo | 
   | <abbr title="Disponível somente para WebJobs Contínuos. Determina se o programa ou o script é executado em todas as instâncias ou apenas em uma única instância. A opção de execução em várias instâncias não se aplica aos tipos de preço Gratuito ou Compartilhado.">Escala</abbr> | Várias instâncias | 

1. Clique em **OK**.

    O novo WebJob é exibido na página **WebJobs**.

    ![Lista de WebJobs](../media/web-sites-create-web-jobs/listallwebjobs.png)

1. **Para parar ou reiniciar** um webjob contínuo, clique com o botão direito do mouse no webjob na lista e clique em **parar** ou **Iniciar**.

   ![Interromper um WebJob contínuo](../media/web-sites-create-web-jobs/continuousstop.png)
