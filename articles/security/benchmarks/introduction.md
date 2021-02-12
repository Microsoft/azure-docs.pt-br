---
title: Introdução ao Azure Security Benchmark
description: Introdução ao benchmark de segurança
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/16/2019
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 32aa64a9343a8e3c62af4322b3320b28ce805064
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/13/2020
ms.locfileid: "97369184"
---
# <a name="azure-security-benchmark-introduction"></a>Introdução ao benchmark de segurança do Azure

Novos serviços e recursos são lançados diariamente no Azure, os desenvolvedores estão publicando rapidamente novos aplicativos de nuvem criados nesses serviços, e os invasores sempre buscam novas maneiras de explorar recursos configurados incorretamente. A nuvem se move rapidamente, os desenvolvedores se movem rapidamente e os invasores estão sempre em movimento. Como acompanhar e certificar-se de que suas implantações na nuvem estão protegidas? Como as práticas de segurança para sistemas de nuvem são diferentes dos sistemas locais? Como monitorar a consistência em muitas equipes de desenvolvimento independentes?

A Microsoft descobriu que o uso de *benchmarks de segurança* pode ajudá-lo a proteger rapidamente as implantações em nuvem. As recomendações de benchmark do seu provedor de serviços de nuvem oferecem um ponto de partida para selecionar configurações de segurança específicas em seu ambiente e permitir que você reduza rapidamente o risco para sua organização.

O benchmark de segurança do Azure inclui uma coleção de recomendações de segurança de alto impacto que você pode usar para ajudar a proteger os serviços que você usa no Azure:

- **Controles de segurança**: essas recomendações geralmente são aplicáveis em seu locatário do Azure e nos serviços do Azure. Cada recomendação identifica uma lista de participantes que normalmente estão envolvidos no planejamento, na aprovação ou na implementação do parâmetro de comparação. 
- **Linhas de base de serviço**: elas aplicam os controles aos serviços individuais do Azure para fornecer recomendações sobre a configuração de segurança desse serviço.

## <a name="implement-the-azure-security-benchmark"></a>Implementar o benchmark de segurança do Azure
- **Planeje** a implementação do benchmark de segurança do Azure examinando a [documentação](overview.md) dos controles corporativos e das linhas de base específicas do serviço para planejar sua estrutura de controle e como ela é MAPEADA para orientação como CIS (controles v 7.1) e estrutura NIST (SP 800-53).
- **Monitore** sua conformidade com o status de benchmark de segurança do Azure (e outros conjuntos de controle) usando o [painel de conformidade regulatória](../../security-center/security-center-compliance-dashboard.md)da central de segurança do Azure.
- **Estabeleça o guardrails** para automatizar as configurações seguras e impor a conformidade com o benchmark de segurança do Azure (e outros requisitos em sua organização) com os planos gráficos e Azure Policy do Azure.
 
Observe que o benchmark de segurança do Azure v2 está alinhado com [as práticas recomendadas de segurança da Microsoft](/security/compass/microsoft-security-compass-introduction) (antiga bússola de segurança do Azure) para que o benchmark de segurança do Azure forneça uma exibição consolidada única das recomendações de segurança do Azure da Microsoft.

## <a name="common-use-cases"></a>Casos de uso comuns

O benchmark de segurança do Azure é usado frequentemente para resolver esses desafios comuns para clientes ou parceiros de serviço que são:
- Novo no Azure e está procurando práticas recomendadas de segurança para garantir uma implantação segura.
- Melhorar a postura de segurança de implantações existentes do Azure para priorizar os principais riscos e mitigações.
- Aprovar os serviços do Azure para uso por tecnologia e uso comercial para atender a diretrizes de segurança específicas.
- Atender aos requisitos regulatórios para clientes que são de setores governamentais ou altamente regulamentados, como finanças e saúde (ou fornecedores de serviços que precisam criar sistemas para esses clientes). Esses clientes precisam garantir que a configuração do Azure atenda aos recursos de segurança especificados em uma estrutura do setor, como CIS, NIST ou PCI. O benchmark de segurança do Azure fornece uma abordagem eficiente com os controles já mapeados para esses benchmarks do setor.

## <a name="terminology"></a>Terminologia

Os termos "controle", "parâmetro de comparação" e "linha de base" são usados frequentemente na documentação de benchmark de segurança do Azure e é importante entender como o Azure usa esses termos.


| Termo | Descrição | Exemplo |
|--|--|--|
| Control | Um controle é uma descrição de alto nível de um recurso ou atividade que precisa ser resolvida e não é específica para uma tecnologia ou implementação. | A proteção de dados é um dos controles de segurança. Esse controle contém ações específicas que devem ser abordadas para ajudar a garantir que os dados sejam protegidos. |
| Parâmetro de comparação | Um parâmetro de comparação contém recomendações de segurança para uma tecnologia específica, como o Azure. As recomendações são categorizadas pelo controle ao qual elas pertencem. | O benchmark de segurança do Azure consiste nas recomendações de segurança específicas para a plataforma Azure |
| Linha de base | Uma linha de base é a implementação do parâmetro de comparação no serviço individual do Azure. Cada organização decide a recomendação de benchmark e as configurações correspondentes são necessárias no escopo de implementação do Azure. | A empresa contoso procura habilitar recursos de segurança do SQL Azure seguindo a configuração recomendada na linha de base de segurança do SQL do Azure.

Agradecemos seus comentários sobre o benchmark de segurança do Azure! Incentivamos você a fornecer comentários na área de comentários abaixo. Se você preferir compartilhar sua entrada de forma mais particular com a equipe de benchmark de segurança do Azure, será bem-vindo a preencher o formulário em https://aka.ms/AzSecBenchmark
