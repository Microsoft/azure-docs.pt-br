---
ms.openlocfilehash: c87b9dc22ecd937abb27417aeddc1e30c0d724e7
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2020
ms.locfileid: "85114499"
---

## <a name="using-this-example-knowledge-base"></a>Usando esta base de dados de conhecimento de exemplo

A base de dados de conhecimento neste guia de início rápido começa com 2 pares de conversa do QnA. Isso é feito de propósito para simplificar o exemplo e ter IDs altamente previsíveis para usar no método de atualização, associando solicitações de acompanhamento com perguntas a novos pares. Isso foi planejado e implementado em uma ordem específica para este guia de início rápido.

Se você planeja desenvolver sua base de dados de conhecimento ao longo do tempo, com solicitações de acompanhamento que dependem de pares do QnA existentes, você pode optar:
* Por bases de dados de conhecimento maiores, gerenciar a base de dados de conhecimento em um editor de texto ou na ferramenta TSV com suporte para automação e substituir completamente a base de dados de conhecimento de uma só vez com uma atualização.
* Para bases de dados de conhecimento menores, gerenciar as solicitações de acompanhamento inteiramente no portal do QnA Maker.

Detalhes sobre os pares do QnA usados neste guia de início rápido:
* Tipos de par do QnA – há dois tipos de pares do QnA nesta base de dados de conhecimento, após a atualização: informações específicas de domínio e conversas. Isso é comum se a base de dados de conhecimento estiver vinculada a um aplicativo de conversa, como um chatbot.
* Embora as respostas da base de dados de conhecimento possam ser filtradas por metadados ou usar prompts de acompanhamento, este guia de início rápido não mostra isso. Procure os exemplos generateAnswer independentes de idioma [aqui](../Quickstarts/get-answer-from-knowledge-base-using-url-tool.md).
* O texto de resposta é um Markdown e pode conter uma [ampla variedade de Markdown](../reference-markdown-format.md) como imagens (imagens baseadas na Internet publicamente disponíveis), links (para URLs disponíveis publicamente) e pontos com marcadores; este guia de início rápido não usa essa variedade.
