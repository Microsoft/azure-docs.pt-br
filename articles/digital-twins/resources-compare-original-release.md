---
title: Diferenças da versão original
titleSuffix: Azure Digital Twins
description: Entenda o que mudou na nova versão dos Gêmeos Digitais do Azure
author: baanders
ms.author: baanders
ms.date: 1/28/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 8010581667354f2e8484bc7341227ec41713d10f
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99072546"
---
# <a name="what-is-the-new-azure-digital-twins-how-is-it-different-from-the-original-version-2018"></a>O que são os novos Gêmeos Digitais do Azure? Qual é a diferença entre a versão original (2018)?

A primeira versão prévia pública dos Gêmeos Digitais do Azure foi lançada em outubro de 2018. Embora os conceitos principais dessa versão original tenham sido transferidos para o serviço atual, muitas das interfaces e detalhes de implementação mudaram para tornar o serviço mais flexível e acessível. Essas alterações foram motivadas por comentários do cliente.

> [!IMPORTANT]
> Na luz dos recursos expandidos do novo serviço, o serviço de gêmeos digital do Azure original foi desativado. A partir de janeiro de 2021, suas APIs e os dados associados não estão mais disponíveis.

Se você usou a primeira versão do Azure digital gêmeos durante a primeira visualização pública, use as informações e as práticas recomendadas neste artigo para aprender a trabalhar com o serviço atual e aproveitar seus recursos.

## <a name="differences-by-topic"></a>Diferenças por tópico

O gráfico a seguir fornece uma exibição lado a lado dos conceitos que foram alterados entre a versão original do serviço e o serviço atual.

| Tópico | Na versão original | Na versão atual |
| --- | --- | --- | --- |
| **Modelagem**<br>*Mais flexível* | A versão original foi projetada em volta de espaços inteligentes, por isso vem com um vocabulário interno para edifícios. | O Azure digital gêmeos atual é independente de domínio. Você pode definir seu próprio vocabulário personalizado e modelos personalizados para sua solução, a fim de representar mais tipos de ambientes de maneiras mais flexíveis.<br><br>Saiba mais em [*Conceitos: modelos personalizados*](concepts-models.md). |
| **Topologia**<br>*Mais flexível*| A versão original oferecia suporte a uma estrutura de dados de árvore, personalizada para espaços inteligentes. Os gêmeos digitais foram conectados com relações hierárquicas. | Com a versão atual, seu gêmeos digital pode ser conectado a topologias de grafo arbitrárias, organizadas no desejadas. Isso proporciona mais flexibilidade para expressar as relações complexas do mundo real.<br><br>Saiba mais em [*Conceitos: gêmeos digitais e o grafo de gêmeos*](concepts-twins-graph.md). |
| **Computação**<br>*Mais avançada e mais flexível* | Na versão original, a lógica para o processamento de eventos e telemetria foi definida em UDFs (funções definidas pelo usuário) do JavaScript. A depuração com UDFs era limitada. | A versão atual tem um modelo de computação aberto: você fornece lógica personalizada anexando recursos de computação externos, como [Azure Functions](../azure-functions/functions-overview.md). Isso permite que você use uma linguagem de programação de sua escolha, acesse bibliotecas de código personalizadas sem restrição e aproveite os recursos de desenvolvimento e depuração que o serviço externo pode ter.<br><br>Saiba mais em [*Como configurar uma função do Azure para processar dados*](how-to-create-azure-function.md). |
| **Gerenciamento de dispositivo com o Hub IoT**<br>*Mais acessível* | A versão original dispositivos gerenciados com uma instância do [Hub IOT](../iot-hub/about-iot-hub.md) que era interna ao serviço gêmeos do Azure digital. Esse hub integrado não estava totalmente acessível para os desenvolvedores. | Na versão atual, você "Traga seu próprio" Hub IoT, anexando uma instância do Hub IoT criada de forma independente (juntamente com os dispositivos que ele já gerencia). Isso dá a você acesso completo aos recursos do Hub IoT e coloca você no controle do gerenciamento de dispositivos.<br><br>Saiba mais em [*Como ingerir a telemetria do Hub IoT*](how-to-ingest-iot-hub-data.md). |
| **Segurança**<br>*Mais padrão* | A versão original tinha funções predefinidas que você poderia usar para gerenciar o acesso à sua instância. | A versão atual integra-se ao mesmo serviço de back-end do [Azure RBAC (controle de acesso baseado em função)](../role-based-access-control/overview.md) que outros serviços do Azure usam. Isso pode simplificar a autenticação entre outros serviços do Azure em sua solução, como o Hub IoT, o Azure Functions, a Grade de Eventos e muito mais.<br>Com o RBAC, você ainda pode usar funções predefinidas ou pode criar e configurar funções personalizadas.<br><br>Saiba mais em [*Conceitos: segurança para soluções dos Gêmeos Digitais do Azure*](concepts-security.md). |
| **Escalabilidade**<br>*Maior* | A versão original tinha limitações de escala para dispositivos, mensagens, grafos e unidades de escala. Havia suporte apenas para uma instância dos Gêmeos Digitais do Azure por assinatura.  | A versão atual se baseia em uma nova arquitetura com escalabilidade aprimorada e tem maior capacidade de computação. Ela também dá suporte a 10 instâncias por região, por assinatura.<br><br>Consulte [*limites do serviço gêmeos do Azure digital*](reference-service-limits.md) para obter detalhes dos limites na versão atual. |

## <a name="service-limits"></a>Limites de serviço

Para obter uma lista de limites de gêmeos digitais do Azure, consulte [*limites do serviço gêmeos do Azure digital*](reference-service-limits.md).

## <a name="next-steps"></a>Próximas etapas

* Aprofunde-se em trabalhar com a versão atual no guia de início rápido: guia de [*início rápido: explorar um cenário de exemplo*](quickstart-adt-explorer.md).

* Ou, comece a ler sobre os principais conceitos com [*conceitos: modelos personalizados*](concepts-models.md).