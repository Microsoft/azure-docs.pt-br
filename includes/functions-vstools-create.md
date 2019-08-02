---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/05/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 6c430f22a9d4fa0fad95bcaa41675545fffd91ec
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67171691"
---
O modelo de projeto do Azure Functions no Visual Studio cria um projeto que pode ser publicado em um aplicativo de funções no Azure. Você pode usar um aplicativo de funções para as funções do grupo como uma unidade lógica para gerenciamento, implantação e compartilhamento de recursos.

1. No Visual Studio, no menu **Arquivo**, selecione **Novo** > **Projeto**.

2. Na caixa de diálogo **Novo projeto**, selecione **Instalado** > **Visual C#**   > **Nuvem** > **Azure Functions**. Insira um nome para seu projeto e selecione **OK**. O nome do aplicativo de funções deve ser válido como um namespace do C# e, portanto, não use outros caracteres não alfanuméricos, hífens ou sublinhados.

    ![Caixa de diálogo Novo Projeto para criar uma função no Visual Studio](./media/functions-vstools-create/functions-vs-new-project.png)

3. Use as configurações conforme especificado na tabela que acompanha a imagem.

    ![Nova Caixa de diálogo no Visual Studio](./media/functions-vstools-create/functions-vs-new-function.png) 

    | Configuração      | Valor sugerido  | DESCRIÇÃO                      |
    | ------------ |  ------- |----------------------------------------- |
    | **Versão** | Azure Functions 2.x <br />(.NET Core) | Essa configuração cria um projeto de função que usa o tempo de execução versão 2.x do Azure Functions, o qual dá suporte a .NET Core. O Azure Functions 1.x dá suporte ao .NET Framework. Para obter mais informações, consulte [Direcionar o tempo de execução do Azure Functions](../articles/azure-functions/functions-versions.md).   |
    | **Modelo** | Gatilho HTTP | Essa configuração cria uma função acionada por uma solicitação HTTP. |
    | **Conta de armazenamento**  | Emulador de armazenamento | Um gatilho HTTP não usa a conexão da conta de Armazenamento do Microsoft Azure. Todos os outros tipos de gatilho exigem uma cadeia de conexão de conta de armazenamento válida. |
    | **Direitos de acesso** | Anônima | A função criada pode ser acionada por qualquer cliente sem fornecer uma chave. Essa configuração de autorização torna fácil testar a nova função. Para obter mais informações sobre chaves e autorização, consulte [Chaves de autorização](../articles/azure-functions/functions-bindings-http-webhook.md#authorization-keys) no [associações HTTP e webhook](../articles/azure-functions/functions-bindings-http-webhook.md). |
    
    > [!NOTE]
    > Verifique se você definiu os **direitos de acesso** para `Anonymous`. Se você escolher o nível padrão de `Function`, é necessário que você apresente a [chave da função](../articles/azure-functions/functions-bindings-http-webhook.md#authorization-keys) em solicitações para acessar seu ponto de extremidade da função.
    
4. Selecione **OK** para criar o projeto de função e a função de gatilho HTTP.
