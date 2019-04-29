---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/25/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 5070b6ced29599660afa28308239e1ba07c2cbd0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60710774"
---
1. Selecione o botão **Criar um recurso** no canto superior esquerdo do portal do Azure e selecione **Computação** > **Aplicativo de funções**.

    ![Criar um aplicativo de funções no portal do Azure](./media/functions-create-function-app-portal/function-app-create-flow.png)

2. Use as configurações do aplicativo de funções especificadas na tabela abaixo da imagem.

    ![Definir novas configurações do aplicativo de funções](./media/functions-create-function-app-portal/function-app-create-flow2.png)

    | Configuração      | Valor sugerido  | DESCRIÇÃO                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nome do aplicativo** | Nome globalmente exclusivo | Nome que identifica seu novo aplicativo de funções. Os caracteres válidos são `a-z`, `0-9` e `-`.  | 
    | **Assinatura** | Sua assinatura | A assinatura na qual este novo aplicativo de funções será criado. | 
    | **[Grupo de Recursos](../articles/azure-resource-manager/resource-group-overview.md)** |  myResourceGroup | Nome do novo grupo de recursos no qual criar o seu aplicativo de funções. |
    | **SO** |  Windows | A hospedagem sem servidor no Linux está atualmente em versão prévia. Para mais informações, consulte [este artigo de considerações](https://aka.ms/funclinux).|
    | **[Plano de hospedagem](../articles/azure-functions/functions-scale.md)** | Plano de consumo | Plano de hospedagem que define como os recursos são alocados para seu aplicativo de funções. No **Plano de Consumo** padrão, os recursos são adicionados dinamicamente conforme exigido por suas funções. Nesta hospedagem [sem servidor](https://azure.microsoft.com/overview/serverless-computing/), você só paga pelo tempo durante o qual suas funções são executadas. Quando você executa em um Plano do Serviço de Aplicativo, deve gerenciar o [dimensionamento do aplicativo de funções](../articles/azure-functions/functions-scale.md).  |
    | **Localidade** | Europa Ocidental | Escolha uma [região](https://azure.microsoft.com/regions/) perto de você ou perto de outros serviços que suas funções acessam. |
    | **Pilha de tempo de execução** | Linguagem preferencial | Escolha um tempo de execução compatível com sua linguagem de programação de funções favorita. Escolha **.NET** para funções C# e F#. |
    | **[Armazenamento](../articles/storage/common/storage-quickstart-create-account.md)** |  Nome globalmente exclusivo |  Crie uma conta de armazenamento usada pelo seu aplicativo de funções. Os nomes da conta de armazenamento devem ter entre 3 e 24 caracteres e podem conter apenas números e letras minúsculas. Você também pode usar uma conta existente, mas ela precisa atender aos [requisitos da conta de armazenamento](../articles/azure-functions/functions-scale.md#storage-account-requirements). |
    | **[Application Insights](../articles/azure-functions/functions-monitoring.md)** | Padrão | Cria um recurso do Application Insights do mesmo *nome do aplicativo* na região com suporte mais próxima. Ao expandir essa configuração, você pode alterar o **Novo nome do recurso** ou escolher um **Local** diferente em uma [geografia do Azure](https://azure.microsoft.com/global-infrastructure/geographies/) onde deseja armazenar seus dados. |

3. Selecione **Criar** para provisionar e implantar o aplicativo de funções.

4. Selecione o ícone da Notificação no canto superior direito do portal e veja se a mensagem **Implantação concluída com êxito** é exibida.

    ![Definir novas configurações do aplicativo de funções](./media/functions-create-function-app-portal/function-app-create-notification.png)

5. Selecione **Ir para recursos** para exibir o novo aplicativo de funções.

> [!TIP]
> Se tiver problemas para localizar seus aplicativos de funções no portal, tente [adicionar os Aplicativos de funções aos seus favoritos no portal do Azure](../articles/azure-functions/functions-how-to-use-azure-function-app-settings.md#favorite).
