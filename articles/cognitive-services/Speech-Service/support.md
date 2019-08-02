---
title: Opções de suporte e ajuda-serviço de fala
titleSuffix: Azure Cognitive Services
description: Como obter ajuda e suporte para perguntas e problemas ao criar aplicativos que se integram com o Serviço de Fala
services: cognitive-services
author: jhakulin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: jhakulin
ms.openlocfilehash: 26d66868ab4c003dbd962e0c94009bc38bb36345
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68558835"
---
# <a name="support-and-help-options"></a>Opções de Ajuda e suporte

Você está apenas começando a explorar a funcionalidade dos serviços de fala? Você está implementando um novo recurso para o seu aplicativo? Aqui estão sugestões onde você pode obter ajuda como desenvolvedor.

> [!div class="checklist"]
> * Mantenha-se informado sobre novos desenvolvimentos dos *Serviços Cognitivos do Azure*, ou localizar as notícias mais recentes relacionadas ao *Serviço de Fala*.
> * As notas de versão contêm informações para todas as versões.
> * Pesquise para ver se o problema foi discutido pela comunidade, ou se a documentação existente para o recurso que você deseja implementar já existe.
> * Se você não conseguir encontrar uma resposta satisfatória, faça uma pergunta *Stack Overflow*.
> * Se você encontrar um problema com um dos exemplos no GitHub, gere um problema *GitHub*.
> * Pesquisar uma solução no *fórum UserVoice*.

## <a name="stay-informed"></a>Continue informado

Notícias sobre os serviços Cognitivos são coletadas no [blog de Serviços Cognitivos](https://azure.microsoft.com/blog/topics/cognitive-services/). Para obter as informações mais recentes sobre os serviços de fala, acompanhe o [blog de serviços de fala](https://azure.microsoft.com/blog/tag/speech-service/).

## <a name="release-notes"></a>Notas de versão

As [notas de versão](https://aka.ms/csspeech/whatsnew) são atualizadas assim que uma nova versão estiver disponível. As notas contêm informações sobre novos recursos, aprimoramentos e correções de bugs.

## <a name="search"></a>Pesquisar

Você pode encontrar a resposta de que precisa na documentação, as amostras ou respostas para as perguntas do [Stack Overflow](https://www.stackoverflow.com) ou nas amostras.

### <a name="scoped-search"></a>Pesquisa com Escopo

Para resultados mais rápidos, especifique sua pesquisa para o Stack Overflow, documentação e amostras de código usando a seguinte consulta em seu [mecanismo de pesquisa favorito](https://bing.com):

```
{Your Search Terms} (site:stackoverflow.com OR site:docs.microsoft.com OR site:github.com/azure-samples)
```

No campo *{Seus Termos de Pesquisa}* estão as palavras-chave de pesquisa.

## <a name="create-an-azure-support-request"></a>Como criar uma solicitação de suporte do Azure

Os clientes do Azure podem criar e gerenciar solicitações de suporte no portal do Azure.

* [Portal do Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)
* [Portal do Azure para o governo dos Estados Unidos](https://portal.azure.us)

## <a name="post-a-question-to-stack-overflow"></a>Postar uma pergunta no Stack Overflow

O Stack Overflow é o canal preferido para o desenvolvimento de questões relacionadas. É onde os membros da comunidade e membros da equipe da Microsoft estão diretamente envolvidos em ajudá-lo a resolver seus problemas.

Se não conseguir encontrar uma resposta para seu problema por meio da pesquisa, envie uma nova pergunta ao Stack Overflow. Use uma das seguintes tags ao formular a pergunta:

|Componente/Área  |Marcas  |
|---------|---------|
|Reconhecimento de fala |[[microsoft-cognitivas + fala em texto]](https://stackoverflow.com/questions/tagged/microsoft-cognitive+speech-to-text)|
|Síntese de fala |[[microsoft-cognitivas + texto em fala]](https://stackoverflow.com/questions/tagged/microsoft-cognitive+text-to-speech)|
|Tradução de fala |[[microsoft-cognitivas + conversão]](https://stackoverflow.com/questions/tagged/microsoft-cognitive+translation)|
|Intenção de fala |[[microsoft-cognitivas + luis]](https://stackoverflow.com/questions/tagged/microsoft-cognitive+luis)|
|SDK de fala geral |[[microsoft-cognitivas + microsoft-fala-api]](https://stackoverflow.com/questions/tagged/microsoft-cognitive+microsoft-speech-api)|
| Assistentes virtuais da primeira voz (visualização) | [[microsoft-cognitive+virtual-assistant+botframework]](https://stackoverflow.com/questions/tagged/microsoft-cognitive+virtual-assistant+botframework) |

> [!TIP]
> As seguintes postagens do Stack Overflow contêm dicas sobre as perguntas de formulário e adicione o código-fonte. As diretrizes a seguir podem ajudar a aumentar as chances de os membros da comunidade avaliarem e responderem à sua pergunta rapidamente:  
> * [Como fazer uma boa pergunta?](https://stackoverflow.com/help/how-to-ask)
> * [Como criar um exemplo mínimo, completo e verificável](https://stackoverflow.com/help/mcve)

## <a name="create-a-github-issue"></a>Criar um problema do GitHub

Normalmente, os exemplos são lançados como código-fonte aberto. Para perguntas e problemas, crie uma *problema* no respectivo repositório do GitHub. Você pode enviar uma solicitação de pull, também. A lista a seguir contém links para os repositórios de exemplo:

* [SDK da fala](https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues)
* [SDK de Dispositivos de Fala](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK/issues)

Você pode criar um relatório de bugs, uma solicitação de recurso ou fazer uma pergunta geral e compartilhar práticas recomendadas. Para relatórios de bugs, siga o modelo fornecido:

**Descrever o bug**

Uma descrição clara e concisa sobre o que o bug é.

**Para reproduzir**

Etapas para reproduzir o comportamento:
1. ...
2. ...

**Comportamento esperado**

Descrição clara e concisa do que você esperava que acontecesse.

**Versão da SDK de Reconhecimento de Fala dos serviços Cognitivos**

Qual versão do SDK você está usando.

**Sistema Operacional da Plataforma e Linguagem de Programação**

 - Sistema operacional: [por exemplo, Windows, Linux, Android, iOS,...] - seja específico
 - Hardware - x64, x86, ARM,...
 - Navegador [por exemplo, Chrome, Safari] (se aplicável) – seja específico

**Contexto adicional**

 - Mensagens de erro, informações de log, rastreamento ...
 - Se você relatar um erro para uma interação de serviço específica, relate a SessionId e a hora (incluindo o fuso horário) dos incidentes relatados. A SessionId é informada em todos os retornos de chamada/eventos que você receber.
 - Quaisquer outras informações adicionais


## <a name="uservoice-forum"></a>Fórum do UserVoice

Compartilhe suas ideias para criar Serviços Cognitivos e as APIs acompanhantes funcionam melhor para os aplicativos desenvolvidos. Use nossa crescente Base de conhecimento para encontrar respostas para perguntas comuns sobre:

[UserVoice](https://cognitive.uservoice.com/)
