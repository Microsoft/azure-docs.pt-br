---
title: Erros de depuração ao executar um aplicativo de comandos personalizados
titleSuffix: Azure Cognitive Services
description: Neste artigo, você aprenderá a depurar erros de tempo de execução em um aplicativo de comandos personalizados.
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.openlocfilehash: 49d9b91df896646da7bf36e077d9f3c9187137dd
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/21/2020
ms.locfileid: "95021789"
---
# <a name="debug-errors-when-running-a-custom-commands-application"></a>Erros de depuração ao executar um aplicativo de comandos personalizados

Este artigo descreve como depurar quando você encontrar erros durante a execução de comandos personalizados aplicativo. 

## <a name="connection-failed"></a>Falha na conexão

Se o aplicativo executar comandos personalizados do [aplicativo cliente (com o SDK de fala)](./how-to-custom-commands-setup-speech-sdk.md) ou o [cliente do assistente de voz do Windows](./how-to-custom-commands-developer-flow-test.md), você poderá enfrentar erros de conexão, conforme listado abaixo:

| Código do erro | Detalhes |
| ------- | -------- |
| [401](#error-401) | AuthenticationFailure: falha na atualização do WebSocket com um erro de autenticação |
| [1002](#error-1002)] | O servidor retornou o código de status ' 404 ' quando o código de status ' 101 ' era esperado. |

### <a name="error-401"></a>Erro 401
- A região especificada no aplicativo cliente não corresponde à região do aplicativo de comando personalizado

- A chave de recurso de fala é inválida
    
    Verifique se a chave de recurso de fala está correta.

### <a name="error-1002"></a>Erro 1002 
- Seu aplicativo de comando personalizado não está publicado
    
    Publique seu aplicativo no Portal.

- Seu applicationId de comando personalizado não é válido

    Verifique se a ID do aplicativo de comando personalizado está correta.
 aplicativo de comando personalizado fora do seu recurso de fala

    Verifique se o aplicativo de comando personalizado foi criado em seu recurso de fala.

Para obter mais informações sobre como solucionar problemas de conexão, consulte [solução de problemas do cliente do assistente de voz do Windows](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/tree/master/clients/csharp-wpf#troubleshooting)


## <a name="dialog-is-canceled"></a>Caixa de diálogo cancelada

Ao executar o aplicativo de comandos personalizados, a caixa de diálogo será cancelada quando ocorrerem alguns erros.

- Se você estiver testando o aplicativo no portal, ele exibiria diretamente a descrição do cancelamento e reproduzirá um erro earcon. 

- Se você estiver executando o aplicativo com o [cliente do assistente do Windows Voice](./how-to-custom-commands-developer-flow-test.md), ele executará um erro earcon. Você pode encontrar o **evento: CancelledDialog** nos **logs de atividade**.

- Se você estiver seguindo nosso aplicativo cliente de exemplo de aplicativo [de clientes (com o SDK de fala)](./how-to-custom-commands-setup-speech-sdk.md), ele reproduziria um erro earcon. Você pode encontrar o **evento: CancelledDialog** sob o **status**.

- Se você estiver criando seu próprio aplicativo cliente, você sempre poderá criar suas lógicas desejadas para manipular os eventos CancelledDialog.

O evento CancelledDialog consiste no código de cancelamento e na descrição, conforme listado abaixo:

| Código de cancelamento | Descrição do cancelamento |
| ------- | --------------- | ----------- |
| [MaxTurnThresholdReached](#no-progress-was-made-after-the-max-number-of-turns-allowed) | Nenhum progresso foi feito após o número máximo de ativações permitidas |
| [RecognizerQuotaExceeded](#recognizer-usage-quota-exceeded) | Cota de uso do reconhecedor excedida |
| [RecognizerConnectionFailed](#connection-to-the-recognizer-failed) | Falha na conexão com o reconhecedor |
| [RecognizerUnauthorized](#this-application-cannot-be-accessed-with-the-current-subscription) | Este aplicativo não pode ser acessado com a assinatura atual |
| [RecognizerInputExceededAllowedLength](#input-exceeds-the-maximum-supported-length) | A entrada excede o comprimento máximo com suporte para o reconhecedor |
| [RecognizerNotFound](#recognizer-not-found) | Reconhecedor não encontrado |
| [RecognizerInvalidQuery](#invalid-query-for-the-recognizer) | Consulta inválida para o reconhecedor |
| [RecognizerError](#recognizer-return-an-error) | O reconhecedor retorna um erro |

### <a name="no-progress-was-made-after-the-max-number-of-turns-allowed"></a>Nenhum progresso foi feito após o número máximo de ativações permitidas
A caixa de diálogo é cancelada quando um slot necessário não é atualizado com êxito após determinado número de ativações. O número máximo de Build é 3.

### <a name="recognizer-usage-quota-exceeded"></a>Cota de uso do reconhecedor excedida
Reconhecimento vocal (LUIS) tem limites de uso de recursos. Normalmente "erro de cota de uso do reconhecedor excedido" pode ser causado por: 
- A criação do LUIS excede o limite

    Adicione um recurso de previsão ao aplicativo de comandos personalizados: 
    1. Vá para **configurações**, recurso Luis
    1. Escolha um recurso de previsão do **recurso de previsão** ou clique em **criar novo recurso** 

- O recurso de previsão do LUIS excede o limite

    Se você estiver em um recurso de previsão F0, ele terá um limite de 10 mil/mês, 5 consultas/segundo.

Para obter mais detalhes sobre os limites de recursos do LUIS, consulte [reconhecimento vocal o uso e o limite de recursos](../luis/luis-limits.md#resource-usage-and-limits)

### <a name="connection-to-the-recognizer-failed"></a>Falha na conexão com o reconhecedor
Normalmente, isso significa falha de conexão transitória para reconhecedor de Reconhecimento vocal (LUIS). Tente novamente e o problema deve ser resolvido.

### <a name="this-application-cannot-be-accessed-with-the-current-subscription"></a>Este aplicativo não pode ser acessado com a assinatura atual
Sua assinatura não está autorizada a acessar o aplicativo LUIS. 

### <a name="input-exceeds-the-maximum-supported-length"></a>A entrada excede o comprimento máximo com suporte
Sua entrada excedeu 500 caracteres. Permitimos apenas no máximo 500 caracteres para expressão de entrada.

### <a name="invalid-query-for-the-recognizer"></a>Consulta inválida para o reconhecedor
Sua entrada excedeu 500 caracteres. Permitimos apenas no máximo 500 caracteres para expressão de entrada.

### <a name="recognizer-return-an-error"></a>Reconhecedor retorna um erro
O reconhecedor LUIS retornou um erro ao tentar reconhecer sua entrada.

### <a name="recognizer-not-found"></a>Reconhecedor não encontrado
Não é possível localizar o tipo de reconhecedor especificado no seu modelo de caixa de diálogo de comandos personalizados. Atualmente, só há suporte para o [Reconhecedor reconhecimento vocal (Luis)](https://www.luis.ai/).

## <a name="other-common-errors"></a>Outros erros comuns
### <a name="unexpected-response"></a>Resposta inesperada
Respostas inesperadas podem ser causadas por várias coisas. Algumas verificações a serem iniciadas:
- Sim/não há intenções nas sentenças de exemplo

    Como atualmente não há suporte para Sim/não tentativas, exceto ao usar o com o recurso de confirmação. Todas as tentativas Sim/não definidas nas frases de exemplo não seriam detectadas.

- Frases de exemplos e intenções semelhantes entre comandos

    A precisão do reconhecimento de LUIS pode ser afetada quando dois comandos compartilham frases de exemplos e intenções semelhantes. Você pode tentar tornar os comandos a funcionalidade e as frases de exemplo o mais distinto possível.

    Para obter uma prática recomendada de melhorar a precisão do reconhecimento, consulte a [prática recomendada do Luis](../luis/luis-concept-best-practices.md).

- Caixa de diálogo cancelada
    
    Verifique os motivos do cancelamento na seção acima.

### <a name="error-while-rendering-the-template"></a>Erro ao renderizar o modelo
Um parâmetro indefinido é usado na resposta de fala. 

### <a name="object-reference-not-set-to-an-instance-of-an-object"></a>Referência de objeto não definida para uma instância de um objeto
Você tem um parâmetro vazio no conteúdo JSON definido na ação **Enviar atividade para o cliente** .

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Consulte os exemplos no GitHub](https://aka.ms/speech/cc-samples)