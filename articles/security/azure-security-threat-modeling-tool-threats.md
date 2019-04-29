---
title: Ameaças - Microsoft Threat Modeling Tool - Azure | Microsoft Docs
description: Página de categoria da ameaça da Microsoft Threat Modeling Tool, contendo categorias para todas as ameaças geradas expostas.
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: rodsan
ms.openlocfilehash: fd7c5fd929163dc7fcd22fbb045dee0fe3070394
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60611552"
---
# <a name="microsoft-threat-modeling-tool-threats"></a>Ameaças da Microsoft Threat Modeling Tool

A ferramenta de modelagem de ameaça é um elemento principal do Microsoft Security Development Lifecycle (SDL). Ele permite identificar e mitigar possíveis problemas de segurança no início, quando eles são relativamente fácil e econômica para resolver os arquitetos de software. Como resultado, ele reduz o custo total de desenvolvimento. Além disso, criamos a ferramenta com especialistas de não-segurança em mente, facilitando a modelagem de ameaças para todos os desenvolvedores fornecendo orientação clara sobre a criação e análise de modelos de ameaças.

> Visite o **[Threat Modeling Tool](./azure-security-threat-modeling-tool.md)** para começar a usá-lo hoje mesmo!

A ferramenta de modelagem de ameaças ajuda a responder algumas perguntas, como aqueles abaixo:

* Como um invasor pode alterar os dados de autenticação?
* Qual é o impacto se um invasor pode ler os dados de perfil do usuário?
* O que acontece se o acesso é negado para o banco de dados de perfil do usuário?

## <a name="stride-model"></a>Modelo STRIDE

A melhor ajuda você formula esses tipos de perguntas pontuais, a Microsoft usa o modelo STRIDE, que categoriza os diferentes tipos de ameaças e simplifica as conversas de segurança geral.

| Categoria | DESCRIÇÃO |
| -------- | ----------- |
| **Falsificação** | Envolve acessar ilegalmente e, em seguida, usando as informações de autenticação de outro usuário, como nome de usuário e senha |
| **Violação** | Envolve a modificação mal-intencionada de dados. Os exemplos incluem alterações não autorizadas feitas em dados persistentes, como as mantidas em um banco de dados e a alteração de dados enquanto estão fluindo entre dois computadores em uma rede aberta, como a Internet |
| **Repúdio** | Associado aos usuários que negam a execução de uma ação sem ter alguma maneira de provar o contrário — por exemplo, um usuário executa uma operação ilegal em um sistema que não tem capacidade para rastrear as operações proibidas. Não repúdio refere-se à capacidade de um sistema para combater ameaças de repúdio. Por exemplo, um usuário que adquire um item pode ter que inscrever o item após o recebimento. O vendedor pode usar o recibo assinado como prova de que o usuário recebeu o pacote |
| **Divulgação de Informações** | Envolve a exposição de informações para os indivíduos que não devem para ter acesso a ele — por exemplo, a capacidade dos usuários para ler um arquivo que não tem permissão de acesso para ou a capacidade de um intruso ler dados em trânsito entre dois computadores |
| **Negação de Serviço** | Ataques negação de serviço (DoS) negar serviços a usuários válidos — por exemplo, fazendo um servidor Web temporariamente indisponível ou inutilizável. Você deve se proteger contra determinados tipos de ameaças DoS simplesmente para melhorar a confiabilidade e disponibilidade do sistema |
| **Elevação de Privilégio** | Um usuário sem privilégios obtém acesso privilegiado e, portanto, tem acesso suficiente para comprometer ou destruir todo o sistema. Elevação de ameaças de privilégios incluem situações em que um invasor penetra de forma eficaz todas as defesas do sistema e se tornam parte do sistema confiável em si, uma situação perigosa, de fato |

## <a name="next-steps"></a>Próximas etapas

Vá para **[Threat Modeling Tool Mitigations](./azure-security-threat-modeling-tool-mitigations.md)** (Mitigações do Threat Modeling Tool) para conhecer as diferentes maneiras de mitigar essas ameaças com o Azure.