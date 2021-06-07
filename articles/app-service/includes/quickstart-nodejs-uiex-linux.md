---
title: 'Início Rápido: criar um aplicativo Web Node.js do Linux'
description: Implante seu primeiro Olá, Mundo em Node.js no Serviço de Aplicativo do Azure em minutos.
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.topic: quickstart
ms.date: 08/01/2020
ms.custom: mvc, devcenter, seodec18
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 59a2c96987762e6b56cc7b453877cebe3124e443
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "102109306"
---
<!-- default for linux -->

## <a name="3-deploy-to-azure-app-service-from-visual-studio-code"></a>3. Implantar no Serviço de Aplicativo do Azure por meio do Visual Studio Code

1. Abra a pasta do aplicativo no Visual Studio Code.

    ```bash
    code .
    ```

1. No explorador do **SERVIÇO DE APLICATIVO DO AZURE**, selecione **Entrar no Azure...** e siga as instruções. Após a conexão, o gerenciador deverá mostrar o nome da sua assinatura do Azure.

    ![Entrar no Azure](../media/quickstart-nodejs/sign-in.png)
    <br>
    <details>
    <summary>Solução de problemas de entrada no Azure</summary>
    
    Se for exibido o erro **"Não é possível encontrar a assinatura com o nome [ID da assinatura]"** quando você entrar no Azure, isso poderá ocorrer porque talvez você esteja atrás de um proxy e não consiga acessar a API do Azure. Configure as variáveis de ambiente `HTTP_PROXY` e `HTTPS_PROXY` com suas informações de proxy no seu terminal usando o `export`.
    
    ```bash
    export HTTPS_PROXY=https://username:password@proxy:8080
    export HTTP_PROXY=http://username:password@proxy:8080
    ```

    [Relatar um problema](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&step=deploy-app)


1. No explorador **SERVIÇO DE APLICATIVO DO AZURE**, selecione o ícone de seta azul para cima para implantar seu aplicativo no Azure. 

    :::image type="content" source="../media/quickstart-nodejs/deploy.png" alt-text="Captura de tela do serviço de Aplicativo do Azure no VS Code que mostra o ícone de seta azul selecionado.":::

1. Escolha o diretório que está aberto no momento, `nodejs-docs-hello-world`.

1. Escolha **Criar aplicativo Web**, que é implantado no Serviço de Aplicativo no Linux por padrão.

1. Digite um nome globalmente exclusivo <abbr title="Os caracteres válidos para um nome de aplicativo são “a-z”, “0-9” e “-”.">name</abbr> para o aplicativo Web e pressione **Enter**. 

1. Escolha a **versão do Node.js**; LTS é recomendado.

    O canal de notificação mostra os recursos do Azure que estão sendo criados para seu aplicativo.

1. Selecione **Sim** quando solicitado a atualizar sua configuração para ser executar `npm install` no servidor de destino. Seu aplicativo será implantado.

    :::image type="content" source="../media/quickstart-nodejs/server-build.png" alt-text="Captura de tela do prompt para atualizar a sua configuração no servidor de destino com o botão Sim selecionado.":::

1. Escolha **Sim** quando receber um prompt para atualizar o workspace, para que futuras implantações direcionem automaticamente o mesmo Aplicativo Web do Serviço de Aplicativo. 

    :::image type="content" source="../media/quickstart-nodejs/save-configuration.png" alt-text="Captura de tela do prompt para atualizar o seu workspace com o botão Sim selecionado.":::




1. Quando a implantação for concluída, selecione **Procurar no Site** no prompt para exibir seu aplicativo Web implantado recentemente.

<br/>
<details>
<summary><strong>Solução de problemas</strong></summary>

Se você não conseguiu concluir estas etapas, verifique o seguinte:

* Verifique se seu aplicativo está escutando na porta fornecida pela variável de ambiente PORT: `process.env.PORT`.

* Se você vir o erro **"Você não tem permissão para exibir este diretório ou página."** , o aplicativo provavelmente terá falhado em iniciar corretamente. Examine a saída do log para localizar e corrigir o erro. 

</details>

<br>

[Relatar um problema](https://www.research.net/r/PWZWZ52?tutorial=node-deployment-azure-app-service&prepare-your-environment)


<br/>
<hr/>


