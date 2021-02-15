---
title: Guia de início rápido – Criar e gerenciar os recursos nos Serviços de Comunicação do Azure
titleSuffix: An Azure Communication Services quickstart
description: Neste guia de início rápido, você aprenderá a criar e gerenciar o seu primeiro recurso dos Serviços de Comunicação do Azure.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
zone_pivot_groups: acs-plat-azp-net
ms.openlocfilehash: a93ac3b5d988be33c0f27726a75b1006f990d1da
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/19/2020
ms.locfileid: "94886084"
---
# <a name="quickstart-create-and-manage-communication-services-resources"></a>Início Rápido: Criar e gerenciar recursos dos Serviços de Comunicação

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

Comece a usar os Serviços de Comunicação do Azure provisionando o seu primeiro recurso dos Serviços de Comunicação. Os recursos dos serviços de comunicação podem ser provisionados por meio do portal do Azure ou com a biblioteca de clientes de gerenciamento do .NET. A biblioteca de clientes de gerenciamento permite criar, configurar, atualizar e excluir o recurso e as interfaces com o [Azure Resource Manager](../../azure-resource-manager/management/overview.md), o serviço de implantação e gerenciamento do Azure. Todas as funcionalidades disponíveis nas bibliotecas de clientes estão disponíveis no portal do Azure. 

> [!WARNING]
> Observe que a disponibilidade dos Serviços de Comunicação é restrita à geografia dos EUA durante a versão prévia pública. Observe também que os recursos de comunicação não podem ser transferidos para uma assinatura diferente durante a versão prévia pública.

::: zone pivot="platform-azp"
[!INCLUDE [Azure portal](./includes/create-resource-azp.md)]
::: zone-end

::: zone pivot="platform-net"
[!INCLUDE [.NET](./includes/create-resource-net.md)]
::: zone-end

## <a name="access-your-connection-strings-and-service-endpoints"></a>Acessar as suas cadeias de conexão e pontos de extremidade de serviço

As cadeias de conexão permitem que as bibliotecas de clientes dos Serviços de Comunicação se conectem com o Azure e sejam autenticadas nele. Você pode acessar as suas cadeias de conexão dos Serviços de Comunicação e pontos de extremidade de serviço no portal do Azure ou de maneira programática com as APIs do Azure Resource Manager. 

Depois de navegar até o recurso dos Serviços de Comunicação, selecione **Chaves** no menu de navegação e copie os valores **Cadeia de conexão** ou **Ponto de extremidade** para uso pelas bibliotecas de clientes dos Serviços de Comunicação. Observe que você tem acesso a chaves primárias e secundárias. Isso pode ser útil em cenários em que você gostaria de fornecer acesso temporário aos seus recursos dos Serviços de Comunicação para um ambiente de preparo ou de terceiros.

:::image type="content" source="./media/key.png" alt-text="Captura de tela da página Chave dos Serviços de Comunicação.":::

## <a name="store-your-connection-string"></a>Armazenar a sua cadeia de conexão

As bibliotecas de clientes dos Serviços de Comunicação usam cadeias de conexão para autorizar solicitações feitas aos Serviços de Comunicação. Você tem várias opções diferentes para armazenar a cadeia de conexão:

* Um aplicativo em execução na área de trabalho ou em um dispositivo pode armazenar a cadeia de conexão em um arquivo **app.config** ou **web.config**. Adicione a cadeia de conexão à seção **AppSettings** nesses arquivos.
* Um aplicativo em execução em um Serviço de Aplicativo do Azure pode armazenar a cadeia de conexão nas [configurações de aplicativo do Serviço de Aplicativo](../../app-service/configure-common.md). Adicione a cadeia de conexão à seção **Cadeias de Conexão** da guia Configurações do Aplicativo no portal.
* Você pode armazenar a cadeia de conexão no [Azure Key Vault](../../data-factory/store-credentials-in-key-vault.md).
* Caso você esteja executando o seu aplicativo localmente, talvez queira armazenar a cadeia de conexão em uma variável de ambiente.

### <a name="store-your-connection-string-in-an-environment-variable"></a>Armazenar a sua cadeia de conexão em uma variável de ambiente

Para configurar uma variável de ambiente, abra uma janela do console e selecione o sistema operacional nas guias abaixo. Substitua `<yourconnectionstring>` pela cadeia de conexão real.

#### <a name="windows"></a>[Windows](#tab/windows)

Abra uma janela do console e insira o seguinte comando:

```console
setx COMMUNICATION_SERVICES_CONNECTION_STRING "<yourconnectionstring>"
```

Depois de adicionar a variável de ambiente, talvez seja necessário reiniciar todos os programas em execução que precisarem ler a variável de ambiente, incluindo a janela do console. Por exemplo, se estiver usando o Visual Studio como seu editor, reinicie-o antes de executar o exemplo.

#### <a name="macos"></a>[macOS](#tab/unix)

Edite o seu **.zshrc** e adicione a variável de ambiente:

```bash
export COMMUNICATION_SERVICES_CONNECTION_STRING="<yourconnectionstring>"
```

Depois de adicionar a variável de ambiente, execute `source ~/.zshrc` a partir da janela de console para que as alterações entrem em vigor. Caso você tenha criado a variável de ambiente com o IDE aberto, talvez seja necessário fechar e abrir novamente o editor, o IDE ou o shell para acessar a variável.

#### <a name="linux"></a>[Linux](#tab/linux)

Edite o seu **.bash_profile** e adicione a variável de ambiente:

```bash
export COMMUNICATION_SERVICES_CONNECTION_STRING="<yourconnectionstring>"
```

Depois de adicionar a variável de ambiente, execute `source ~/.bash_profile` a partir da janela de console para que as alterações entrem em vigor. Caso você tenha criado a variável de ambiente com o IDE aberto, talvez seja necessário fechar e abrir novamente o editor, o IDE ou o shell para acessar a variável.

---

## <a name="clean-up-resources"></a>Limpar os recursos

Se quiser limpar e remover uma assinatura dos Serviços de Comunicação, exclua o recurso ou o grupo de recursos. Excluir o grupo de recursos também exclui todos os recursos associados a ele.

Se você tiver números de telefone atribuídos ao recurso na exclusão de recursos, os números de telefone serão liberados do recurso automaticamente ao mesmo tempo. 

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você aprendeu a:

> [!div class="checklist"]
> * Criar um recurso dos Serviços de Comunicação
> * Configurar a geografia e as marcas do recurso
> * Acessar as chaves desse recurso
> * Excluir o recursos

> [!div class="nextstepaction"]
> [Criar os seus primeiros tokens de acesso de usuário](access-tokens.md)