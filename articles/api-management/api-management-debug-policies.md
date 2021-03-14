---
title: Depurar políticas de gerenciamento de API do Azure no Visual Studio Code | Microsoft Docs
description: Saiba como depurar políticas de gerenciamento de API do Azure usando a extensão de Visual Studio Code de gerenciamento de API do Azure
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/22/2020
ms.author: apimpm
ms.openlocfilehash: 2e45d1274cf7332dbca70eaa8fc51f0ac98e5359
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101648009"
---
# <a name="debug-azure-api-management-policies-in-visual-studio-code"></a>Depurar políticas de gerenciamento de API do Azure no Visual Studio Code

[As políticas](api-management-policies.md) no gerenciamento de API do Azure fornecem recursos avançados que ajudam os editores de API a resolverem questões abrangentes, como autenticação, autorização, limitação, cache e transformação. As políticas são um conjunto de instruções executadas em sequência, na solicitação ou na resposta de uma API. 

Este artigo descreve como depurar políticas de gerenciamento de API usando a [extensão de gerenciamento de API do Azure para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-apimanagement). 

## <a name="prerequisites"></a>Pré-requisitos

* Crie uma instância de camada de desenvolvedor de gerenciamento de API seguindo este [início rápido](get-started-create-service-instance.md) primeiro.

* Instale [Visual Studio Code](https://code.visualstudio.com/) e a versão mais recente da [extensão de gerenciamento de API do Azure para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-apimanagement). 

* Importe uma API para sua instância de gerenciamento de API. Por exemplo, veja [o tutorial: usar a extensão de gerenciamento de API para Visual Studio Code para importar e gerenciar APIs](visual-studio-code-tutorial.md).

## <a name="restrictions-and-limitations"></a>Restrições e limitações

Esse recurso só está disponível na camada de desenvolvedor do gerenciamento de API. Cada instância de gerenciamento de API dá suporte a apenas uma sessão de depuração simultânea.

## <a name="initiate-a-debugging-session"></a>Iniciar uma sessão de depuração

1. Iniciar o Visual Studio Code
2. Navegue até a extensão de gerenciamento de API em extensões do Azure
3. Localizar a instância de gerenciamento de API a ser depurada
4. Localizar a API e a operação a serem depuradas
5. Clique com o botão direito do mouse na operação e selecione **Iniciar Depuração de política**

Neste ponto, a extensão tentará iniciar e estabelecer uma sessão de depuração com o gateway de gerenciamento de API.

![Iniciar Depuração](media/api-management-debug-policies/initiate-debugging-session.png)

## <a name="send-a-test-request"></a>Enviar uma solicitação de teste
Quando a sessão de depuração é estabelecida, a extensão abre um novo editor que nos permite criar e enviar uma solicitação HTTP de teste para essa operação, aproveitando a [extensão de cliente REST](https://marketplace.visualstudio.com/items?itemName=humao.rest-client).

Você notará que o cabeçalho **OCP-APIM-Debug** já foi adicionado à solicitação. Esse cabeçalho é necessário e o valor deve ser definido como a chave de assinatura de todos os níveis de serviço e de acesso para disparar a funcionalidade de depuração no gateway de gerenciamento de API.

Modifique a solicitação HTTP no editor de acordo com seu cenário de teste. Em seguida, clique em **Enviar solicitação** para enviar a solicitação de teste para o gateway de gerenciamento de API.

![Enviar uma solicitação de teste](media/api-management-debug-policies/rest-client.png)

## <a name="debug-policies"></a>Políticas de depuração
Depois que a solicitação HTTP de teste for enviada, a extensão abrirá a janela de depuração mostrando as políticas efetivas dessa operação e será interrompida na primeira política efetiva. 

![políticas de depuração](media/api-management-debug-policies/main-window.png)

Para seguir o pipeline de política, você pode percorrer por políticas individuais ou definir um ponto de interrupção em uma política e passar diretamente para essa política. 

No painel **variáveis** , você pode inspecionar valores de variáveis criadas pelo sistema e pelo usuário. No painel **pontos de interrupção** , você pode ver a lista de todos os pontos de interrupção que foram definidos. No painel **pilha de chamadas** , você pode ver o escopo atual da política efetiva. 

Se houver um erro durante a execução da política, você verá os detalhes do erro na política em que ele aconteceu. 

![exceptions](media/api-management-debug-policies/exception.png)

> [!TIP]
> Lembre-se de sair da sessão de depuração clicando no botão **parar** quando terminar.


## <a name="next-steps"></a>Próximas etapas

+ Saiba mais sobre a [extensão de gerenciamento de API para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-apimanagement). 
+ Relatar problemas no [repositório GitHub](https://github.com/Microsoft/vscode-apimanagement)

