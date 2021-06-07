---
title: Criar uma função usando o Microsoft Azure for Students Starter
description: Saiba como criar uma função do Azure dentro de uma assinatura do Microsoft Azure for Students Starter
ms.topic: how-to
ms.date: 04/29/2020
ms.openlocfilehash: 3ca91999182e717500eacfa73dda834e9cf89562
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106067122"
---
# <a name="create-a-function-using-azure-for-students-starter"></a>Criar uma função usando o Microsoft Azure for Students Starter

Neste tutorial, criaremos uma função HTTP "Olá, Mundo" em uma assinatura do Microsoft Azure for Students Starter. Também examinaremos o que está disponível no Azure Functions nesse tipo de assinatura.

O Microsoft *Azure for Students Starter* oferece uma introdução aos produtos do Azure necessários para desenvolver na nuvem sem nenhum custo para você. [Saiba mais sobre essa oferta aqui.](https://azure.microsoft.com/offers/ms-azr-0144p/)

O Azure Functions lhe permite executar seu código em um ambiente [sem servidor](https://azure.microsoft.com/solutions/serverless/) sem que seja preciso primeiro criar uma VM ou publicar um aplicativo Web. [Saiba mais sobre o Functions aqui.](./functions-overview.md)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-function"></a>Criar uma função

 Neste artigo, você aprende a usar o Azure Functions para criar uma função de gatilho HTTP "Olá, Mundo" no portal do Azure.

![Criar um aplicativo de funções no portal do Azure](./media/functions-create-student-starter/function-app-in-portal-editor.png)

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no [portal do Azure](https://portal.azure.com) com sua conta do Azure.

## <a name="create-a-function-app"></a>Criar um aplicativo de funções

Você deve ter um aplicativo de funções para hospedar a execução de suas funções. Um aplicativo de funções lhe permite agrupar funções como uma unidade lógica para facilitar o gerenciamento, a implantação, o dimensionamento e o compartilhamento de recursos.

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

Em seguida, crie uma nova função no novo aplicativo de funções.

## <a name="create-an-http-trigger-function"></a><a name="create-function"></a>Criar uma função de gatilho HTTP

1. No menu esquerdo da janela **Funções**, selecione **Funções** e depois selecione **Adicionar** no menu superior. 
 
1. Na janela **Nova Função**, selecione **Gatilho HTTP**.

    ![Escolher uma função de gatilho HTTP](./media/functions-create-student-starter/function-app-select-http-trigger.png)

1. Na janela **Nova Função**, aceite o nome padrão para **Nova Função** ou insira um novo nome. 

1. Escolha **Anônimo** na lista suspensa **Nível de autorização** e, em seguida, selecione **Criar Função**.

    O Azure cria a função de gatilho HTTP. Agora você pode executar a nova função enviando uma solicitação HTTP.

## <a name="test-the-function"></a>Testar a função

1. Em sua nova função de gatilho HTTP, selecione **Codificar + Testar** no menu esquerdo e, em seguida, **Obter URL da função** no menu superior.

    ![Selecione Obter URL da função](./media/functions-create-student-starter/function-app-select-get-function-url.png)

1. Na caixa de diálogo **Obter URL da função**, selecione **padrão** na lista suspensa e, em seguida, selecione **Copiar para a área de transferência**. 

    ![Copiar a URL da função do Portal do Azure](./media/functions-create-student-starter/function-app-develop-tab-testing.png)

1. Cole a URL de função na barra de endereços do navegador. Adicione o valor da cadeia de caracteres de consulta `?name=<your_name>` ao final desta URL e pressione ENTER para executar a solicitação. 

    O exemplo a seguir mostra a resposta no navegador:

    ![Resposta da função no navegador.](./media/functions-create-student-starter/function-app-browser-testing.png)

    A URL da solicitação inclui uma chave que é necessária, por padrão, para acessar sua função via HTTP.

1. Quando a função é executada, informações de rastreamento são gravadas nos logs. Para ver a saída do rastreamento, retorne à página **Codificar + Testar** no portal e expanda a seta **Logs** na parte inferior da página.

   ![Visualizador de log de função no Portal do Azure.](./media/functions-create-student-starter/function-view-logs.png)

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="supported-features-in-azure-for-students-starter"></a>Recursos compatíveis com o Microsoft Azure for Students Starter

No Microsoft Azure for Students Starter, você tem acesso à maioria dos recursos do Azure Functions Runtime, com várias limitações importantes listadas abaixo:

* O gatilho HTTP é o único tipo de gatilho com suporte.
    * Há suporte para todas as associações de entrada e saída! [Veja a lista completa aqui.](functions-triggers-bindings.md)
* Linguagens com suporte: 
    * C# (.NET Core 2)
    * JavaScript (Node.js 8 e 10)
    * F# (.NET Core 2)
    * [Veja as linguagens com suporte em planos superiores aqui](supported-languages.md)
* O Windows é o único sistema operacional com suporte.
* A escala é restrita aos [uma instância de camada gratuita](https://azure.microsoft.com/pricing/details/app-service/windows/) em execução por até 60 minutos por dia. Você realizará escalonamento sem servidor de 0 para 1 instância automaticamente à medida que o tráfego HTTP for recebido, mas somente isso.
* Somente as [versões 2.x e posteriores](functions-versions.md) do runtime do Functions são compatíveis.
* Todas as ferramentas de desenvolvedor têm suporte para as funções de edição e publicação. Isso inclui VS Code, Visual Studio, CLI do Azure e portal do Azure. Se quiser usar algo diferente do portal, você precisará primeiro criar um aplicativo no portal e, em seguida, escolher esse aplicativo como um destino de implantação em sua ferramenta preferencial.

## <a name="next-steps"></a>Próximas etapas

Você concluiu a criação de um aplicativo de funções com uma função simples de gatilho HTTP. Em seguida, você pode explorar as ferramentas locais, mais linguagens, monitoramento e integrações.

 * [Criar sua primeira função usando o Visual Studio](./functions-create-your-first-function-visual-studio.md)
 * [Criar sua primeira função usando o Visual Studio Code](./create-first-function-vs-code-csharp.md)
 * [Guia do desenvolvedor de JavaScript do Azure Functions](./functions-reference-node.md)
 * [Usar o Azure Functions para conectar a um Banco de Dados SQL do Azure](./functions-scenario-database-table-cleanup.md)
 * [Saiba mais sobre as associações HTTP do Azure Functions](./functions-bindings-http-webhook.md).
 * [Monitorar o Azure Functions](./functions-monitoring.md)