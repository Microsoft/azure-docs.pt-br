---
title: Definir configurações de Aplicativo de funções do Azure | Microsoft Docs
description: Saiba como definir configurações de aplicativos do Azure Functions.
services: ''
documentationcenter: .net
author: ggailey777
manager: jeconnoc
ms.assetid: 81eb04f8-9a27-45bb-bf24-9ab6c30d205c
ms.service: azure-functions
ms.topic: conceptual
ms.date: 03/28/2018
ms.author: glenga
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 7497255dcad55cea86e0c640e2f1423d7d763a7f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60737767"
---
# <a name="how-to-manage-a-function-app-in-the-azure-portal"></a>Como gerenciar um aplicativo de funções no portal do Azure 

No Azure Functions, um aplicativo de funções fornece o contexto de execução para suas funções individuais. Os comportamentos do aplicativo de funções se aplicam a todas as funções hospedadas por um aplicativo função de determinada. Este tópico descreve como configurar e gerenciar seus aplicativos de funções no portal do Azure.

Acesse o [portal do Azure](https://portal.azure.com) e entre usando sua conta do Azure. Na barra de pesquisa na parte superior do portal, digite o nome de seu aplicativo de funções e selecione-o na lista. Depois de selecionar o aplicativo de funções, você deverá ver esta página:

![Visão geral do aplicativo de funções no portal do Azure](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-main.png)

## <a name="favorite"></a>Funções favoritas no portal 

Às vezes, poderá ser difícil localizar os recursos no [Portal do Azure]. Para facilitar a localização dos aplicativos de funções que você criou, adicione Aplicativos de Funções aos seus favoritos no portal. 

1. Faça logon no [Portal do Azure].

2. Clique na seta no canto inferior esquerdo para expandir todos os serviços, digite `Functions` no campo **Filtro** e, em seguida, clique na estrela ao lado de **Aplicativos de Funções**.  
 
    ![Criar um aplicativo de funções no portal do Azure](./media/functions-how-to-use-azure-function-app-settings/functions-favorite-function-apps.png)

    Isso adiciona o ícone do Functions ao menu à esquerda do portal.

3. Feche o menu e role para baixo até a parte inferior para ver o ícone do Functions. Clique nesse ícone para ver uma lista de todos os seus aplicativos de funções. Clique em seu aplicativo de funções para trabalhar com funções neste aplicativo. 
 
    ![Aplicativos de funções nos Favoritos](./media/functions-how-to-use-azure-function-app-settings/functions-function-apps-hub.png)
 
[Portal do Azure]: https://portal.azure.com/

## <a name="manage-app-service-settings"></a>Guia Configurações do aplicativo de funções

![Visão geral do aplicativo de funções no portal do Azure.](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-settings-tab.png)

É na guia **Configurações** que você pode atualizar a versão de tempo de execução do Functions usada por seu aplicativo de funções. Também é onde você pode gerenciar as chaves de host usadas para restringir o acesso HTTP para todas as funções hospedadas pelo aplicativo de funções.

O Functions oferece suporte aos planos de hospedagem de Consumo e Serviço de Aplicativo. Para saber mais, confira [Escolher o plano de serviço correto para o Azure Functions](functions-scale.md). Para aprimorar a previsibilidade no plano de Consumo, o Functions permite a limitação do uso da plataforma por meio da definição de uma cota de uso diário, em gigabytes/segundo. Assim que a cota diária de uso é atingida, o aplicativo de funções é interrompido. O aplicativo de funções interrompido como resultado do alcance da cota de gasto pode ser reabilitado no mesmo contexto na definição da cota diária de gasto. Consulte a [página de preços do Azure Functions](https://azure.microsoft.com/pricing/details/functions/) para obter detalhes sobre a cobrança.   

## <a name="platform-features-tab"></a>Guia Recursos da plataforma

![Guia Recursos da plataforma do aplicativo de funções.](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-features-tab.png)

Aplicativos de funções executados e mantidos pela plataforma de Serviço de Aplicativo do Azure. Dessa forma, seus aplicativos de funções têm acesso à maioria dos recursos da principal plataforma de hospedagem na Web do Azure. Na guia **Recursos da plataforma** você acessa vários recursos da plataforma de Serviço de Aplicativo para uso em seus aplicativos de funções. 

> [!NOTE]
> Nem todos os recursos do Serviço de Aplicativo estão disponíveis quando um aplicativo de funções é executado no plano de hospedagem de Consumo.

O restante deste tópico se concentra nos seguintes recursos de Serviço de Aplicativo do portal do Azure que são úteis para o Functions:

+ [Editor do Serviço de Aplicativo](#editor)
+ [Configurações do aplicativo](#settings) 
+ [Console](#console)
+ [Ferramentas avançadas (Kudu)](#kudu)
+ [Opções de implantação](#deployment)
+ [CORS](#cors)
+ [Autenticação](#auth)
+ [Definição da API](#swagger)

Para saber mais sobre como trabalhar com configurações da Serviço de Aplicativo, confira [Definir configurações do Serviço de Aplicativo do Azure](../app-service/web-sites-configure.md).

### <a name="editor"></a>Editor de Serviço de Aplicativo

| | |
|-|-|
| ![Editor do Serviço de Aplicativo do aplicativo de funções.](./media/functions-how-to-use-azure-function-app-settings/function-app-appsvc-editor.png)  | O Editor do Serviço de Aplicativo é um editor avançado dentro do portal que você pode usar para modificar arquivos de configuração JSON e arquivos de código. Escolher essa opção inicia uma nova guia do navegador com um editor básico. Isso permite que você se integre ao repositório do Git, execute e depure código, além de modificar as configurações do aplicativos de funções. Esse editor fornece um ambiente de desenvolvimento avançado para suas funções em comparação com a folha do aplicativo de funções padrão.    |

![O Editor de Serviço de Aplicativo](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-appservice-editor.png)

### <a name="settings"></a>Configurações do aplicativo

| | |
|-|-|
| ![Configurações de aplicativo do aplicativo de funções.](./media/functions-how-to-use-azure-function-app-settings/function-app-application-settings.png) | A folha **Configurações do aplicativo** do Serviço de Aplicativo é onde você pode configurar e gerencia versões do framework, a depuração remota, as configurações do aplicativo e cadeias de conexão. Ao integrar seu aplicativo de funções ao Azure e a outros serviços de terceiros, você pode modificar essas configurações aqui. Para excluir uma configuração, role para a direita e selecione o ícone **X** na extremidade direita da linha (não é exibida na imagem a seguir).

![Definir as configurações do aplicativo](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-settings.png)

### <a name="console"></a>Console

| | |
|-|-|
| ![Console do aplicativo de funções no portal do Azure](./media/functions-how-to-use-azure-function-app-settings/function-app-console.png) | O console no portal é uma ferramenta ideal de desenvolvimento quando você preferir interagir com seu aplicativo de funções na linha de comando. Os comandos comuns incluem criação e navegação de diretório e arquivo, bem como execução scripts e arquivos em lote. |

![Console do aplicativo de funções](./media/functions-how-to-use-azure-function-app-settings/configure-function-console.png)

### <a name="kudu"></a>Ferramentas avançadas (Kudu)

| | |
|-|-|
| ![Kudu do aplicativo de funções no portal do Azure](./media/functions-how-to-use-azure-function-app-settings/function-app-advanced-tools.png) | As ferramentas avançadas para o Serviço de Aplicativo (também conhecidas como Kudu) fornecem acesso a recursos administrativos avançados de seu aplicativo de funções. No Kudu, você pode gerenciar informações do sistema, configurações do aplicativo, variáveis de ambiente, extensões do site, cabeçalhos HTTP e variáveis de servidor. Você também pode iniciar o **Kudu** navegando até o ponto de extremidade SCM de seu aplicativo de funções, como`https://<myfunctionapp>.scm.azurewebsites.net/` |

![Configurar Kudu](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-kudu.png)


### <a name="a-namedeploymentdeployment-options"></a><a name="deployment">Opções de implantação

| | |
|-|-|
| ![Opções de implantação do aplicativo de funções no portal do Azure](./media/functions-how-to-use-azure-function-app-settings/function-app-deployment-source.png) | O Functions permite que você desenvolva o código de sua função em seu computador local. Depois, você pode carregar seu projeto de aplicativo de funções local no Azure. Além do upload tradicional por FTP, o Functions permite que você implante seu aplicativo de funções usando soluções de integração contínua populares, como GitHub, Azure DevOps, Dropbox, Bitbucket e outros. Para saber mais, confira [Implantação contínua do Azure Functions](functions-continuous-deployment.md). Para carregar manualmente usando FTP ou Git local, você deve também [configurar suas credenciais de implantação](functions-continuous-deployment.md#credentials). |


### <a name="cors"></a>CORS

| | |
|-|-|
| ![CORS do aplicativo de funções no portal do Azure](./media/functions-how-to-use-azure-function-app-settings/function-app-cors.png) | Para impedir a execução de código mal-intencionado em seus serviços, o Serviço de Aplicativo bloqueia chamadas para seus aplicativos de funções de fontes externas. O Functions oferece suporte a CORS (compartilhamento de recursos entre origens) para permitir que você defina uma "lista de permissão" com as origens permitidas das quais suas funções podem aceitar solicitações remotas.  |

![Configurar CORS do Aplicativo de funções](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-cors.png)

### <a name="auth"></a>Autenticação

| | |
|-|-|
| ![Autenticação do aplicativo de funções no portal do Azure](./media/functions-how-to-use-azure-function-app-settings/function-app-authentication.png) | Quando as funções usam um gatilho HTTP, você pode exigir que as chamadas sejam autenticadas primeiro. O Serviço de Aplicativo oferece suporte à autenticação do Azure Active Directory e a entrada com provedores sociais, como Facebook, Microsoft e Twitter. Para obter detalhes sobre como configurar provedores de autenticação específicos, consulte [Visão geral de autenticação do serviço de aplicativo do Azure](../app-service/overview-authentication-authorization.md). |

![Configurar a autenticação para um aplicativo de funções](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-authentication.png)


### <a name="swagger"></a>Definição da API

| | |
|-|-|
| ![Definição de swagger da API do aplicativo de funções no portal do Azure](./media/functions-how-to-use-azure-function-app-settings/function-app-api-definition.png) | O Functions oferece suporte ao Swagger para permitir que os clientes consumam mais facilmente suas funções disparadas por HTTP. Para obter mais informações sobre como criar definições de API com Swagger, visite [Hospedar uma API RESTful com CORS no Serviço de Aplicativo do Azure](../app-service/app-service-web-tutorial-rest-api.md). Você também pode usar os Proxies do Functions para definir uma única superfície de API para várias funções. Para saber mais, veja [Trabalhar com o Proxies do Azure Functions](functions-proxies.md). |

![Configurar API do Aplicativo de funções](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-apidef.png)



## <a name="next-steps"></a>Próximas etapas

+ [Definir configurações do Serviço de Aplicativo do Azure](../app-service/web-sites-configure.md)
+ [Implantação contínua para Azure Functions](functions-continuous-deployment.md)



