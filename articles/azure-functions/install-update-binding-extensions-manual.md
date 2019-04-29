---
title: Instalar ou atualizar manualmente as extensões de associação do Azure Functions
description: Saiba como instalar ou atualizar as extensões de ligação do Azure Functions para aplicativos de função implantados.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: azure functions, funções, extensões de associação, NuGet, atualizações
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 09/26/2018
ms.author: glenga
ms.openlocfilehash: cda977ba59070c3ddaac05784277d6c0b5109f0f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61035703"
---
# <a name="manually-install-or-update-azure-functions-binding-extensions-from-the-portal"></a>Instalar ou atualizar manualmente as extensões de associação do Azure Functions no portal

O tempo de execução do Azure Functions versão 2.x usa extensões de associação para implementar código a gatilhos e associações. As extensões de associação são fornecidas nos pacotes NuGet. Registrar uma extensão é basicamente instalar um pacote. Ao desenvolver funções, a maneira de instalar as extensões de associação depende do ambiente de desenvolvimento. Para obter mais informações, consulte [Registrar extensões de associação](./functions-bindings-register.md) no artigo de gatilhos e associações.

Às vezes, é necessário instalar ou atualizar manualmente as extensões de associação no portal do Azure. Por exemplo, talvez seja necessário atualizar uma associação registrada para uma versão mais recente. Também pode ser necessário registrar uma associação com suporte que não pode ser instalada na guia **Integrar** no portal.

## <a name="install-a-binding-extension"></a>Instalar uma extensão de associação

Siga as etapas abaixo para instalar ou atualizar manualmente as extensões do portal.

1. No [portal do Azure](https://portal.azure.com), localize o aplicativo de funções e selecione-o. Escolha a guia **Visão geral** e selecione **Parar**.  Parar o aplicativo de funções desbloqueia arquivos para que as alterações possam ser efetivadas.

1. Escolha a guia **Recursos da plataforma** e em **Ferramentas de Desenvolvimento** selecione **Ferramentas Avançadas (Kudu)**. O ponto de extremidade do Kudu (`https://<APP_NAME>.scm.azurewebsites.net/`) abre em uma nova janela.

1. Na janela do Kudu, selecione **Console de depuração** > **CMD**.  

1. Na janela de comando, navegue até `D:\home\site\wwwroot` e escolha o ícone de exclusão próximo a `bin` para excluir a pasta. Selecione **OK** para confirmar a exclusão.

1. Escolha o ícone de edição próximo ao arquivo `extensions.csproj`, que define as extensões de associação para o aplicativo de funções. O arquivo de projeto é aberto no editor online.

1. Faça as adições e atualizações necessárias dos itens **PackageReference** em **ItemGroup**, e, em seguida, selecione **Salvar**. A lista atua de versões de pacotes com suporte pode ser encontrada no artigo do wiki [Quais pacotes eu preciso?](https://github.com/Azure/azure-functions-host/wiki/Updating-your-function-app-extensions#what-nuget-packages-do-i-need). Todas as três associações de Armazenamento do Azure exigem o pacote Microsoft.Azure.WebJobs.Extensions.Storage.

1. Na pasta `wwwroot` execute o seguinte comando para reconstruir os assemblies referenciados na pasta `bin`.

    ```cmd
    dotnet build extensions.csproj -o bin --no-incremental --packages D:\home\.nuget
    ```

1. Retorne à guia **Visão geral** no portal, escolha **Iniciar** para iniciar o aplicativo de funções.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Aprenda mais sobre gatilhos e de associações do Azure Functions](functions-triggers-bindings.md)
