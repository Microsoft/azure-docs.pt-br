---
title: Criar um aplicativo de funções do Portal do Azure no Linux
description: Saiba como criar sua primeira função do Azure no Linux usando o portal do Azure.
ms.topic: how-to
ms.date: 04/29/2020
ms.openlocfilehash: 34a93795b5e041ccef8e9576f97092e16c429444
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97937171"
---
# <a name="create-a-function-app-on-linux-in-an-azure-app-service-plan"></a>Criar um aplicativo de funções em um Plano do Serviço de Aplicativo do Azure

Azure Functions permite hospedar funções no Linux em um contêiner de Serviço de Aplicativo do Azure padrão. Este artigo explica como usar a CLI do Azure para criar o [portal do Azure](https://portal.azure.com) para criar um aplicativo de funções hospedado em Linux que é executado em um [Plano do Serviço de Aplicativo](dedicated-plan.md). Você também pode [usar seu próprio contêiner](functions-create-function-linux-custom-image.md).

![Criar um aplicativo de funções no portal do Azure](./media/create-function-app-linux-app-service-plan/function-app-in-portal-editor.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no portal do Azure em <https://portal.azure.com> com sua conta do Azure.

## <a name="create-a-function-app"></a>Criar um aplicativo de funções

Você deve ter um aplicativo de funções para hospedar a execução de suas funções no Linux. O aplicativo de funções fornece um ambiente para execução do seu código de função. Ele permite que você agrupe funções como uma unidade lógica para facilitar o gerenciamento, a implantação, o dimensionamento e o compartilhamento de recursos. Neste artigo, você criará um plano do serviço de aplicativo quando criar seu aplicativo de funções.

1. No menu do portal do Azure ou na **Página Inicial**, selecione **Criar um recurso**.

1. Na página **Novo**, selecione **Computação** > **Aplicativo de Funções**.

    :::image type="content" source="./media/create-function-app-linux-app-service-plan/function-app-create-flow.png" alt-text="Criar um aplicativo de funções no portal do Azure":::

1. Na página **Informações Básicas**, use as configurações do aplicativo de funções especificadas na tabela a seguir.

    | Configuração      | Valor sugerido  | Descrição |
    | ------------ | ---------------- | ----------- |
    | **Assinatura** | Sua assinatura | A assinatura na qual este novo aplicativo de funções será criado. |
    | **[Grupo de Recursos](../azure-resource-manager/management/overview.md)** |  *myResourceGroup* | Nome do novo grupo de recursos no qual criar o seu aplicativo de funções. |
    | **Nome do aplicativo de funções** | Nome globalmente exclusivo | Nome que identifica seu novo aplicativo de funções. Os caracteres válidos são `a-z` (não diferencia maiúsculas de minúsculas), `0-9` e `-`.  |
    |**Publicar**| **Código** (padrão) | Opção para publicar arquivos de código ou um contêiner do Docker. |
    | **Pilha de runtime** | Linguagem preferencial | Escolha um runtime compatível com sua linguagem de programação de funções favorita. Escolha **.NET Core** para as funções de C# e F#. |
    |**Versão**| Número de versão | Escolha a versão do seu runtime instalado.  |
    |**Região**| Região preferencial | Escolha uma [região](https://azure.microsoft.com/regions/) perto de você ou perto de outros serviços que suas funções acessam. |

    :::image type="content" source="./media/create-function-app-linux-app-service-plan/function-app-create-basics-linux.png" alt-text="Página de noções básicas":::

1. Selecione **Avançar: Hospedagem**. Na página **Hospedagem**, insira as configurações a seguir.

    | Configuração      | Valor sugerido  | Descrição |
    | ------------ | ---------------- | ----------- |
    | **[Conta de armazenamento](../storage/common/storage-account-create.md)** |  Nome globalmente exclusivo |  Crie uma conta de armazenamento usada pelo seu aplicativo de funções. Os nomes da conta de armazenamento devem ter entre 3 e 24 caracteres e podem conter apenas números e letras minúsculas. Use também uma conta existente, que precisará atender aos [requisitos da conta de armazenamento](../azure-functions/storage-considerations.md#storage-account-requirements). |
    |**Sistema operacional**| **Linux** | Um sistema operacional é pré-selecionado para você com base na seleção da pilha de runtime, mas você pode alterar a configuração, se necessário. |
    | **[Plano](../azure-functions/functions-scale.md)** | **Consumo (Sem servidor)** | Plano de hospedagem que define como os recursos são alocados para seu aplicativo de funções. No plano **Consumo** padrão, os recursos são adicionados dinamicamente conforme exigido por suas funções. Nesta hospedagem [sem servidor](https://azure.microsoft.com/overview/serverless-computing/), você paga somente pelo tempo durante o qual suas funções são executadas. Quando você executa em um Plano do Serviço de Aplicativo, deve gerenciar o [dimensionamento do aplicativo de funções](../azure-functions/functions-scale.md).  |

    :::image type="content" source="./media/create-function-app-linux-app-service-plan/function-app-create-hosting-linux.png" alt-text="Plano de hospedagem":::

1. Selecione **Avançar: Monitoramento**. Na página **Monitoramento**, insira as configurações a seguir.

    | Configuração      | Valor sugerido  | Descrição |
    | ------------ | ---------------- | ----------- |
    | **[Application Insights](../azure-functions/functions-monitoring.md)** | **Sim** (padrão) | Cria um recurso do Application Insights do mesmo *nome do aplicativo* na região com suporte mais próxima. Ao expandir essa configuração ou selecionar **Criar**, você pode alterar o nome do Application Insights ou escolher uma região diferente em uma [geografia do Azure](https://azure.microsoft.com/global-infrastructure/geographies/) onde deseja armazenar seus dados. |

   :::image type="content" source="./media/create-function-app-linux-app-service-plan/function-app-create-monitoring-linux.png" alt-text="Página de monitoramento":::

1. Selecione **Examinar + criar** para examinar as seleções de configuração do aplicativo.

1. Na página **Examinar + criar**, examine as configurações e, em seguida, selecione **Criar** para provisionar e implantar o aplicativo de funções.

1. Selecione o ícone **Notificações** no canto superior direito do portal e veja se a mensagem **Implantação concluída com êxito** é exibida.

1. Selecione **Ir para recursos** para exibir o novo aplicativo de funções. Você também pode selecionar **Fixar no painel**. A fixação torna mais fácil retornar a esse recurso de aplicativo de função no seu painel.

    ![Notificação de implantação](./media/create-function-app-linux-app-service-plan/function-app-create-notification2.png)

    Mesmo depois que seu aplicativo de funções estiver disponível, ele poderá levar alguns minutos para ser totalmente inicializado.

Em seguida, crie uma nova função no novo aplicativo de funções.

## <a name="create-an-http-trigger-function"></a><a name="create-function"></a>Criar uma função de gatilho HTTP

Esta seção mostra como criar uma função no novo aplicativo de funções no portal.

> [!NOTE]
> A experiência de desenvolvimento do portal pode ser útil para experimentar o Azure Functions. Na maioria dos cenários, considere desenvolver suas funções localmente e publicar o projeto de seu aplicativo de funções usando um [Visual Studio Code](./create-first-function-vs-code-csharp.md#create-an-azure-functions-project) ou [Azure Functions Core Tools](functions-run-local.md#create-a-local-functions-project).  

1. No menu esquerdo da janela **Funções**, selecione **Funções** e depois selecione **Adicionar** no menu superior. 
 
1. Na janela **Nova Função**, selecione **Gatilho HTTP**.

    ![Escolher uma função de gatilho HTTP](./media/create-function-app-linux-app-service-plan/function-app-select-http-trigger.png)

1. Na janela **Nova Função**, aceite o nome padrão para **Nova Função** ou insira um novo nome. 

1. Escolha **Anônimo** na lista suspensa **Nível de autorização** e, em seguida, selecione **Criar Função**.

    O Azure cria a função de gatilho HTTP. Agora você pode executar a nova função enviando uma solicitação HTTP.

## <a name="test-the-function"></a>Testar a função

1. Em sua nova função de gatilho HTTP, selecione **Codificar + Testar** no menu esquerdo e, em seguida, **Obter URL da função** no menu superior.

    ![Selecione Obter URL da função](./media/create-function-app-linux-app-service-plan/function-app-select-get-function-url.png)

1. Na caixa de diálogo **Obter URL da função**, selecione **padrão** na lista suspensa e, em seguida, selecione **Copiar para a área de transferência**. 

    ![Copiar a URL da função do Portal do Azure](./media/create-function-app-linux-app-service-plan/function-app-develop-tab-testing.png)

1. Cole a URL de função na barra de endereços do navegador. Adicione o valor da cadeia de caracteres de consulta `?name=<your_name>` ao final desta URL e pressione ENTER para executar a solicitação. 

    O exemplo a seguir mostra a resposta no navegador:

    ![Resposta da função no navegador.](./media/create-function-app-linux-app-service-plan/function-app-browser-testing.png)

    A URL da solicitação inclui uma chave que é necessária, por padrão, para acessar sua função via HTTP.

1. Quando a função é executada, informações de rastreamento são gravadas nos logs. Para ver a saída do rastreamento, retorne à página **Codificar + Testar** no portal e expanda a seta **Logs** na parte inferior da página.

   ![Visualizador de log de função no Portal do Azure.](./media/create-function-app-linux-app-service-plan/function-view-logs.png)

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Próximas etapas

Você criou um aplicativo de funções com uma função simples de gatilho HTTP.  

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

Para obter mais informações, veja [Associações HTTP do Azure Functions](functions-bindings-http-webhook.md).