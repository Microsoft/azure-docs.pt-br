---
title: Guia de Início Rápido do Azure – Criar um runbook de Automação do Azure | Microsoft Docs
description: Este artigo ajuda você a começar a criar um runbook da Automação do Azure.
services: automation
ms.date: 02/05/2019
ms.topic: quickstart
ms.subservice: process-automation
ms.custom:
- mvc
- mode-api
ms.openlocfilehash: 151199d8389130b85e917a09fd8e8d7b5f56d90c
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107536551"
---
# <a name="create-an-azure-automation-runbook"></a>Criar um runbook de Automação do Azure

Os runbooks de Automação do Azure podem ser criados por meio do Azure. Esse método fornece uma interface do usuário baseada em navegador para criar runbooks de Automação. Neste Guia de Início Rápido, você passa pela criação, edição, teste e publicação de um runbook PowerShell de Automação.

Caso você não tenha uma assinatura do Azure, crie uma [conta gratuita do Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Azure em https://portal.azure.com.

## <a name="create-the-runbook"></a>Criar o runbook

Primeiro, crie um runbook. O runbook de exemplo criado neste guia de início rápido exibe `Hello World` por padrão.

1. Abra sua conta da Automação.

1. Clique em **Runbooks** em **Automação de Processo**. A lista de runbooks é exibida.

1. Clique em **Criar um runbook** na parte superior da lista.

1. Insira `Hello-World` para o nome do runbook no campo **Nome** e selecione **PowerShell** para o campo **Tipo de runbook**. 

   ![Inserir informações sobre o runbook de Automação na página](./media/automation-quickstart-create-runbook/automation-create-runbook-configure.png)

1. Clique em **Criar**. O runbook é criado e a página Editar Runbook do PowerShell é aberta.

    :::image type="content" source="./media/automation-quickstart-create-runbook/automation-edit-runbook-empty.png" alt-text="Captura de tela da página Editar Runbook do PowerShell.":::

1. Digite ou copie e cole o seguinte código no painel de edição. Ele cria um parâmetro de entrada opcional chamado `Name` com um valor padrão de `World` e gera uma cadeia de caracteres que usa este valor de entrada:

   ```powershell-interactive
   param
   (
       [Parameter(Mandatory=$false)]
       [String] $Name = "World"
   )

   "Hello $Name!"
   ```

1. Clique em **Salvar** para salvar uma cópia de rascunho do runbook.

    :::image type="content" source="./media/automation-quickstart-create-runbook/automation-edit-runbook.png" alt-text="Captura de tela da página Editar Runbook do PowerShell com um exemplo de código na janela direita.":::

## <a name="test-the-runbook"></a>Testar o runbook

Depois de criar o runbook, você precisará testá-lo para validar se ele funciona.

1. Clique em **Painel de teste** para abrir o Painel de teste.

1. Insira um valor para **Name**, e clique em **Iniciar**. O trabalho de teste começa e o status do trabalho e a saída são exibidos.

    :::image type="content" source="./media/automation-quickstart-create-runbook/automation-test-runbook.png" alt-text="Captura de tela do painel Teste com um valor de exemplo no campo nome.":::

1. Feche o painel Teste clicando no **X** no canto superior direito. Selecione **OK** no pop up exibido.

1. Na página Editar Runbook do PowerShell clique em **Publicar** para publicar o runbook como a versão oficial do runbook na conta.

   :::image type="content" source="./media/automation-quickstart-create-runbook/automation-hello-world-runbook-job.png" alt-text="Captura de tela da página Editar Runbook do PowerShell, mostrando o botão Publicar selecionado.":::

## <a name="run-the-runbook"></a>Executar o runbook

Quando o runbook é publicado, a página de visão geral é exibida.

1. Na página de visão geral do runbook, clique em **Iniciar** para abrir a página de configuração Iniciar Runbook.

   :::image type="content" source="./media/automation-quickstart-create-runbook/automation-hello-world-runbook-start.png" alt-text="Captura de tela da página Iniciar configuração do Runbook.":::

1. Deixe **Name** em branco, para que o valor padrão seja usado e clique em **OK**. O trabalho do runbook é enviado e a página Trabalho é exibida.

   :::image type="content" source="./media/automation-quickstart-create-runbook/automation-job-page.png" alt-text="Captura de tela da página Trabalho, mostrando o botão Saída selecionado.":::

1. Quando o Status do trabalho for `Running` ou `Completed`, clique em **Saída** para abrir o painel Saída e exibir a saída do runbook.

   :::image type="content" source="./media/automation-quickstart-create-runbook/automation-hello-world-runbook-job-output.png" alt-text="Captura de tela do painel Saída, mostrando a saída do runbook.":::

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não for mais necessário, exclua o runbook. Para isso, selecione o runbook na lista e clique em **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você criou, editou, testou e publicou um runbook e iniciou um trabalho de runbook. Para saber mais sobre runbooks de automação, continue com o artigo sobre diferentes tipos de runbook que você pode criar e usar na Automação.

> [!div class="nextstepaction"]
> [Tipos de runbook da Automação do Azure](./automation-runbook-types.md)
