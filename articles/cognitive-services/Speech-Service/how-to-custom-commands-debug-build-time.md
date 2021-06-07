---
title: Erros de depuração ao criar um aplicativo de comandos personalizados (visualização)
titleSuffix: Azure Cognitive Services
description: Neste artigo, você aprenderá a depurar erros ao criar aplicativos de comandos personalizados.
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.openlocfilehash: aeb90e8e064c44f4d17f920261ed58310f0e55f0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "95025694"
---
# <a name="debug-errors-when-authoring-a-custom-commands-application"></a>Erros de depuração ao criar um aplicativo de comandos personalizados

Este artigo descreve como depurar quando você encontrar erros ao criar aplicativos de comandos personalizados. 

## <a name="errors-when-creating-an-application"></a>Erros ao criar um aplicativo
Comandos personalizados também criam um aplicativo no [Luis](https://www.luis.ai/) ao criar um aplicativo de comandos personalizados. 

[Luis limita 500 aplicativos por recurso de criação](../luis/luis-limits.md). A criação do aplicativo LUIS poderá falhar se você estiver usando um recurso de criação que já tenha 500 aplicativos. 

Verifique se o recurso de criação de LUIS selecionado tem menos de 500 aplicativos. Caso contrário, você pode criar um novo recurso de criação de LUIS, alternar para outro ou tentar limpar seus aplicativos LUIS.  

## <a name="errors-when-deleting-an-application"></a>Erros ao excluir um aplicativo
### <a name="cant-delete-luis-application"></a>Não é possível excluir o aplicativo LUIS
Ao excluir um aplicativo de comandos personalizados, os comandos personalizados também podem tentar excluir o aplicativo LUIS associado ao aplicativo de comandos personalizados.

Se a exclusão do aplicativo LUIS falhou, vá para sua conta do [Luis](https://www.luis.ai/) para excluí-los manualmente.

### <a name="toomanyrequests"></a>TooManyRequests
Quando você tenta excluir um grande número de aplicativos de uma só vez, é provável que você veja os erros ' TooManyRequests '. Esses erros significam que suas solicitações de exclusão são limitadas pelo Azure. 

Atualize sua página e tente excluir menos aplicativos.

## <a name="errors-when-modifying-an-application"></a>Erros ao modificar um aplicativo

### <a name="cant-delete-a-parameter-or-a-web-endpoint"></a>Não é possível excluir um parâmetro ou um ponto de extremidade da Web
Você não tem permissão para excluir um parâmetro quando ele está sendo usado. Remova qualquer referência do parâmetro em qualquer resposta de fala, sentenças de exemplo, condições, ações e tente novamente.

### <a name="cant-delete-a-web-endpoint"></a>Não é possível excluir um ponto de extremidade da Web
Você não tem permissão para excluir um ponto de extremidade da Web quando ele está sendo usado. Remova qualquer ação de **ponto de extremidade Web de chamada** que usa esse ponto de extremidade da Web antes de remover um ponto de extremidade da Web

## <a name="errors-when-training-an-application"></a>Erros ao treinar um aplicativo
### <a name="built-in-intents"></a>Built-In tentativas
LUIS tem intenções internas Sim/não. Ter frases de amostra com apenas "Sim", "não", falharia no treinamento. 

| Palavra-chave | Variações | 
| ------- | --------- | 
| Sim | Certo, OK |
| Não | Verão, não | 

### <a name="common-sample-sentences"></a>Sentenças de exemplo comuns
Os comandos personalizados não permitem frases de exemplo comuns compartilhadas entre comandos diferentes. O treinamento de um aplicativo poderá falhar se algumas frases de exemplo em um comando já estiverem definidas em outro comando. 

Verifique se você não tem frases de exemplo comuns compartilhadas entre comandos diferentes. 

Para obter uma prática recomendada de balanceamento de frases de exemplo em diferentes comandos, consulte a [prática recomendada do Luis](../luis/luis-concept-best-practices.md).

### <a name="empty-sample-sentences"></a>Sentenças de exemplo vazias
Você precisa ter pelo menos uma frase de exemplo para cada comando.

### <a name="undefined-parameter-in-sample-sentences"></a>Parâmetro indefinido em sentenças de exemplo
Um ou mais parâmetros são usados nas sentenças de exemplo, mas não definidos.

### <a name="training-takes-too-long"></a>O treinamento leva muito tempo
O treinamento do LUIS destina-se a aprender rapidamente com menos exemplos. Não adicione muitas frases de exemplo. 

Se você tiver muitas frases de exemplo semelhantes, defina um parâmetro, abstraia-as em um padrão e adicione-as a frases de exemplo.

Por exemplo, você pode definir um parâmetro {veículo} para as sentenças de exemplo abaixo e adicionar apenas "livro a {Vehicle}" a frases de exemplo.

| Sentenças de exemplo | Padrão | 
| ------- | ------- | 
| Reservar um carro | Agendar um {veículo} | 
| Reservar um voo | Agendar um {veículo} |
| Agendar um táxi | Agendar um {veículo} |

Para obter a melhor prática do treinamento do LUIS, consulte a [prática recomendada do Luis](../luis/luis-concept-best-practices.md).

## <a name="cant-update-luis-key"></a>Não é possível atualizar a chave LUIS
### <a name="reassign-to-e0-authoring-resource"></a>Reatribuir ao recurso de criação de E0
LUIS não dá suporte à reatribuição do aplicativo LUIS ao recurso de criação de E0.

Se você precisar alterar o recurso de criação de F0 para E0 ou alterar para um recurso E0 diferente, recrie o aplicativo. 

Para exportar rapidamente um aplicativo existente e importá-lo para um novo aplicativo, consulte [implantação contínua com o Azure DevOps](./how-to-custom-commands-deploy-cicd.md).

### <a name="save-button-is-disabled"></a>O botão salvar está desabilitado
Se você nunca atribuir um recurso de previsão LUIS ao seu aplicativo, o botão salvar será desabilitado quando você tentar alterar o recurso de criação sem adicionar um recurso de previsão.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Consulte os exemplos no GitHub](https://aka.ms/speech/cc-samples)