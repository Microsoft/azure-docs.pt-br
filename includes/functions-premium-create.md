---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: functions
author: jeffhollan
ms.service: azure-functions
ms.topic: include
ms.date: 04/01/2019
ms.author: jehollan, glenga
ms.custom: include file
ms.openlocfilehash: 9bac92bc1cc94b88dc694b468b795049db4ac9df
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68443976"
---
1. Vá para o [Portal do Azure](https://portal.azure.com).

1. Selecione **+ criar um recurso** no lado esquerdo e, em seguida, escolha **aplicativo de funções**.

1. Para o **plano de hospedagem**, escolha plano do serviço de **aplicativo**e, em seguida, selecione **plano/local do serviço de aplicativo**.

    ![Criar um aplicativo de funções](./media/functions-premium-create/create-function-app-resource.png)

1. Selecione **criar novo**, digite um nome de **plano do serviço de aplicativo** , escolha um **local** em uma [região](https://azure.microsoft.com/regions/) perto de você ou perto de outros serviços que suas funções acessam e selecione **tipo de preço**.

    ![Criar plano de Serviço de Aplicativo](./media/functions-premium-create/new-app-service-plan.png)

1. Escolha o plano **ep1** (Premium elástico) e, em seguida, selecione **aplicar**.

    ![Selecionar plano Premium](./media/functions-premium-create/hosting-plan.png) 

1. Selecione **OK** para criar o plano e, em seguida, use as configurações do aplicativo de funções restantes, conforme especificado na tabela abaixo da imagem. 

    ![Plano do serviço de aplicativo concluído](./media/functions-premium-create/create-function-app.png)  

    | Configuração      | Valor sugerido  | Descrição                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nome do aplicativo** | Nome globalmente exclusivo | Nome que identifica seu novo aplicativo de funções. Os caracteres válidos são `a-z`, `0-9` e `-`.  | 
    | **Assinatura** | Sua assinatura | A assinatura na qual este novo aplicativo de funções será criado. |
    | **[Grupo de Recursos](../articles/azure-resource-manager/resource-group-overview.md)** |  myResourceGroup | Nome do novo grupo de recursos no qual criar o seu aplicativo de funções. Você também pode usar o valor sugerido. |
    | **OS** | Sistema operacional preferencial | O Linux e o Windows têm suporte no plano Premium. |
    | **Pilha de tempo de execução** | Linguagem preferencial | Escolha um tempo de execução compatível com sua linguagem de programação de funções favorita. Escolha **.NET** para funções C# e F#. Somente os idiomas com suporte no **sistema operacional** escolhido são exibidos. |
    | **[Armazenamento](../articles/storage/common/storage-quickstart-create-account.md)** |  Nome globalmente exclusivo |  Crie uma conta de armazenamento usada pelo seu aplicativo de funções. Os nomes da conta de armazenamento devem ter entre 3 e 24 caracteres e podem conter apenas números e letras minúsculas. Você também pode usar uma conta existente, mas ela precisa atender aos [requisitos da conta de armazenamento](../articles/azure-functions/functions-scale.md#storage-account-requirements). |
    | **[Application Insights](../articles/azure-functions/functions-monitoring.md)** | Padrão | Cria um recurso do Application Insights do mesmo *nome do aplicativo* na região com suporte mais próxima. Ao expandir essa configuração, você pode alterar o **Novo nome do recurso** ou escolher um **Local** diferente em uma [geografia do Azure](https://azure.microsoft.com/global-infrastructure/geographies/) onde deseja armazenar seus dados. |

1. Depois que as configurações forem validadas, selecione **criar**.

1. Selecione o ícone da Notificação no canto superior direito do portal e veja se a mensagem **Implantação concluída com êxito** é exibida.

    ![Definir novas configurações do aplicativo de funções](./media/functions-premium-create/function-app-create-notification.png)

1. Selecione **Ir para recursos** para exibir o novo aplicativo de funções. Você também pode selecionar **Fixar no painel**. A fixação torna mais fácil retornar a esse recurso de aplicativo de função no seu painel.