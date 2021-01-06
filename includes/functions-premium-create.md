---
title: incluir arquivo
description: incluir arquivo
services: functions
author: jeffhollan
ms.service: azure-functions
ms.topic: include
ms.date: 04/24/2020
ms.author: jehollan, glenga
ms.custom: include file
ms.openlocfilehash: 4eaea46044f0da4766fda051655d57caee9d98b6
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97937001"
---
1. No menu do portal do Azure ou na **Página Inicial**, selecione **Criar um recurso**.

1. Na página **Novo**, selecione **Computação** > **Aplicativo de Funções**.

1. Na página **Informações básicas**, use as configurações do aplicativo de funções conforme especificado na seguinte tabela:

    | Configuração      | Valor sugerido  | Descrição |
    | ------------ | ---------------- | ----------- |
    | **Assinatura** | Sua assinatura | A assinatura na qual este novo aplicativo de funções será criado. |
    | **[Grupo de Recursos](../articles/azure-resource-manager/management/overview.md)** |  *myResourceGroup* | Nome do novo grupo de recursos no qual criar o seu aplicativo de funções. |
    | **Nome do aplicativo de funções** | Nome globalmente exclusivo | Nome que identifica seu novo aplicativo de funções. Os caracteres válidos são `a-z` (não diferencia maiúsculas de minúsculas), `0-9` e `-`.  |
    |**Publicar**| Código | Opção para publicar arquivos de código ou um contêiner do Docker. |
    | **Pilha de runtime** | Linguagem preferencial | Escolha um runtime compatível com sua linguagem de programação de funções favorita. Escolha **.NET** para funções C# e F#. |
    |**Região**| Região preferencial | Escolha uma [região](https://azure.microsoft.com/regions/) perto de você ou perto de outros serviços que suas funções acessam. |

    ![Página de informações básicas](./media/functions-premium-create/function-app-create-basics.png)

1. Selecione **Avançar: Hospedagem**. Na página **Hospedagem**, insira as seguintes configurações:

    | Configuração      | Valor sugerido  | Descrição |
    | ------------ | ---------------- | ----------- |
    | **[Conta de armazenamento](../articles/storage/common/storage-account-create.md)** |  Nome globalmente exclusivo |  Crie uma conta de armazenamento usada pelo seu aplicativo de funções. Os nomes da conta de armazenamento devem ter entre 3 e 24 caracteres e podem conter apenas números e letras minúsculas. Use também uma conta existente, que precisará atender aos [requisitos da conta de armazenamento](../articles/azure-functions/storage-considerations.md#storage-account-requirements). |
    |**Sistema operacional**| Sistema operacional preferencial | Um sistema operacional é pré-selecionado para você com base na seleção da pilha de runtime, mas você pode alterar a configuração, se necessário. O Python só tem suporte no Linux. |
    | **[Plano](../articles/azure-functions/functions-scale.md)** | Premium | Plano de hospedagem que define como os recursos são alocados para seu aplicativo de funções. Selecione **Premium**. Por padrão, um plano do Serviço de Aplicativo é criado. O **SKU e o tamanho** padrão é **EP1**, em que EP significa _elástico premium_. Para saber mais, confira a [lista de SKUs Premium](../articles/azure-functions/functions-premium-plan.md#available-instance-skus).<br/>Ao executar funções JavaScript em um plano Premium, você deve escolher uma instância que tem menos vCPUs. Para obter mais informações, confira [Escolher planos Premium de núcleo único](../articles/azure-functions/functions-reference-node.md#considerations-for-javascript-functions).  |

    ![Plano de hospedagem](./media/functions-premium-create/function-app-premium-create-hosting.png)

1. Selecione **Avançar: Monitoramento**. Na página **Monitoramento**, insira as seguintes configurações:

    | Configuração      | Valor sugerido  | Descrição |
    | ------------ | ---------------- | ----------- |
    | **[Application Insights](../articles/azure-functions/functions-monitoring.md)** | Padrão | Cria um recurso do Application Insights do mesmo *nome do aplicativo* na região com suporte mais próxima. Expandindo essa configuração, você pode alterar o **Novo nome do recurso** ou escolher um **Local** diferente em uma [geografia do Azure](https://azure.microsoft.com/global-infrastructure/geographies/) para armazenar seus dados. |

    ![Página de monitoramento](./media/functions-create-function-app-portal/function-app-create-monitoring.png)

1. Selecione **Examinar + criar** para examinar as seleções de configuração do aplicativo.

1. Na página **Examinar + criar**, examine as configurações e, em seguida, selecione **Criar** para provisionar e implantar o aplicativo de funções.

1. Selecione o ícone **Notificações** no canto superior direito do portal e veja se a mensagem **Implantação concluída com êxito** é exibida.

1. Selecione **Ir para recursos** para exibir o novo aplicativo de funções. Você também pode selecionar **Fixar no painel**. A fixação torna mais fácil retornar a esse recurso de aplicativo de função no seu painel.

    ![Notificação de implantação](./media/functions-premium-create/function-app-create-notification2.png)
