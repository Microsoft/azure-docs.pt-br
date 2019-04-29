---
title: Metadados de OpenAPI no Azure Functions | Microsoft Docs
description: Visão geral do suporte OpenAPI em Azure Functions
services: functions
author: alexkarcher-msft
manager: jeconnoc
ms.assetid: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alkarche
ms.openlocfilehash: 6d11961f06a75341e633c7a8963e6b83ed37cf13
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61341615"
---
# <a name="openapi-20-metadata-support-in-azure-functions-preview"></a>Suporte aos metadados de OpenAPI 2.0 no Azure Functions (versão prévia)
O suporte aos metadados de OpenAPI 2.0 (conhecido antes como Swagger) no Azure Functions é um recurso de visualização que você pode usar para gravar uma definição de OpenAPI 2.0 dentro de um aplicativo de funções. Depois, você pode hospedar esse arquivo usando o aplicativo de funções.

> [!IMPORTANT]
> A versão prévia do recurso do OpenAPI só está disponível no tempo de execução 1.x, no momento. Informações de como criar um aplicativo de funções 1.x [podem ser encontradas aqui](./functions-versions.md#creating-1x-apps).

[Metadados de OpenAPI](https://swagger.io/) permitem que uma função que hospeda uma API REST seja consumida por vários outros softwares. Entre esses softwares estão as ofertas da Microsoft como o PowerApps e o [recurso de Aplicativos de API do Serviço de Aplicativo do Azure](../app-service/overview.md), ferramentas de desenvolvedor de terceiros, como [Postman](https://www.getpostman.com/docs/importing_swagger) e [muitos outros pacotes](https://swagger.io/tools/).

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

>[!TIP]
>É recomendável iniciar com o [tutorial de introdução](./functions-api-definition-getting-started.md) e, em seguida, retornar a este documento para saber mais sobre recursos específicos.

## <a name="enable"></a>Habilitar o suporte à definição de OpenAPI
Você pode definir todas as configurações de OpenAPI na página **Definição de API** nos **Recursos da plataforma** de seu aplicativo de funções.

> [!NOTE]
> O recurso de definição de API de função não tem suporte no tempo de execução beta no momento.

Para habilitar a geração de uma definição de OpenAPI hospedada e uma definição de início rápido, defina **Origem de definição da API** para **Função (Versão prévia)**. **URL Externa** permite que sua função use uma definição de OpenAPI que é hospedada em outro lugar.

## <a name="generate-definition"></a>Gerar um esqueleto de Swagger dos metadados de sua função
Um modelo pode ajudar você a começar a gravar sua primeira definição de OpenAPI. O recurso de modelo de definição cria uma definição de OpenAPI esparsa usando todos os metadados no arquivo function.json para cada uma de suas funções de gatilho HTTP. Você precisará preencher mais informações sobre sua API na [especificação de OpenAPI](https://swagger.io/specification/), como modelos de solicitação e resposta.

Para obter instruções passo a passo, confira este [tutorial de introdução](./functions-api-definition-getting-started.md).

### <a name="templates"></a>Modelos disponíveis

|NOME| DESCRIÇÃO |
|:-----|:-----|
|Definição gerada|Uma definição de OpenAPI com a quantidade máxima de informações que podem ser inferidas de metadados existentes da função.|

### <a name="quickstart-details"></a>Metadados incluídos na definição gerada

A tabela a seguir representa as configurações do Portal do Azure e dados correspondentes no function.json conforme ele são mapeados para o esqueleto de Swagger gerado.

|Swagger.json|Interface do usuário do portal|Function.json|
|:----|:-----|:-----|
|[Host](https://swagger.io/specification/#fixed-fields-15)|**Configurações do aplicativo de funções** > **Configurações do Serviço de Aplicativo** > **Visão geral** > **URL**|*Não presente*
|[Caminhos](https://swagger.io/specification/#paths-object-29)|**Integrar** > **Métodos HTTP selecionados**|Associações: Rota
|[Item de caminho](https://swagger.io/specification/#path-item-object-32)|**Integrar** > **Modelos de rota**|Associações: Métodos
|[Segurança](https://swagger.io/specification/#security-scheme-object-112)|**Chaves**|*Não presente*|
|operationID*|**Rota + Verbos permitidos**|Rota + verbos permitidos|

\*A ID da operação só é necessário para a integração com PowerApps e Flow.
> [!NOTE]
> A extensão x-ms-summary fornece um nome de exibição nos Aplicativos Lógicos, PowerApps e Flow.
>
> Para saber mais, confira [Personalizar sua definição de Swagger para PowerApps](https://powerapps.microsoft.com/tutorials/customapi-how-to-swagger/).

## <a name="CICD"></a>Usar o CI/CD para configurar uma definição de API

 Você deve habilitar a hospedagem de definição de API no portal antes de habilitar o controle de código-fonte para modificar sua definição da API do controle do código-fonte. Siga estas instruções:

1. Navegue até **Definição de API (prévia)** em suas configurações de aplicativo de funções.
   1. Definir **Origem de definição de API** como **Função**.
   1. Clique em **Gerar modelo de definição de API** e depois em **Salvar** para criar uma definição de modelo a fim de modificar posteriormente.
   1. Anote a URL e a chave de definição da API.
1. [Configurar a integração contínua/implantação contínua(CI/CD)](https://docs.microsoft.com/azure/azure-functions/functions-continuous-deployment#continuous-deployment-requirements).
2. Modifique o swagger.json no controle do código-fonte em \site\wwwroot\.azurefunctions\swagger\swagger.json.

Agora, as alterações em swagger.json em seu repositório são hospedadas por seu aplicativo de funções na URL e chave de definição de API que você anotou na etapa 1.c.

## <a name="next-steps"></a>Próximas etapas
* [Tutorial de introdução](functions-api-definition-getting-started.md). Experimente o nosso passo a passo para exibir uma definição de OpenAPI em ação.
* [Repositório do GitHub do Azure Functions](https://github.com/Azure/Azure-Functions/). Confira o repositório Funções para fornecer comentários sobre a versão prévia de suporte de definição de API. Execute o GitHub para qualquer coisa que você gostaria de ver atualizada.
* [Referência do desenvolvedor do Azure Functions](functions-reference.md). Saiba mais sobre a codificação de funções e definição de gatilhos e associações.
