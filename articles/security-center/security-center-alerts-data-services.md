---
title: Detecção de ameaças para serviços de dados na central de segurança do Azure | Microsoft Docs
description: Este tópico apresenta os alertas de serviços de dados disponíveis na central de segurança do Azure.
services: security-center
documentationcenter: na
author: monhaber
manager: rkarlin
editor: ''
ms.assetid: da960861-0b6c-4d80-932d-898cdebb4f83
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/24/2019
ms.author: v-mohabe
ms.openlocfilehash: f33b69ac443a1bb8f6b7d6e1b19f2f077bf38f58
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/25/2019
ms.locfileid: "68501474"
---
# <a name="threat-detection-for-data-services-in-azure-security-center"></a>Detecção de ameaças para serviços de dados na central de segurança do Azure

 A central de segurança analisa os logs dos serviços de armazenamento de dados e dispara alertas quando detecta uma ameaça aos recursos de dados. Este tópico lista os alertas que a central de segurança gera para os seguintes serviços:

* [Banco de dados SQL do Azure e SQL Data Warehouse](#data-sql)
* [Armazenamento do Azure](#azure-storage)
* [Cosmos DB](#cosmos-db)

## Banco de dados SQL do Azure e SQL Data Warehouse<a name="data-sql"></a>

A detecção de ameaças do SQL detecta atividades anormais que indicam tentativas incomuns e potencialmente prejudiciais de acessar ou explorar bancos de dados. A central de segurança analisa os logs de auditoria do SQL e é executado nativamente no mecanismo do SQL.

|Alerta|Descrição|
|---|---|
|**Vulnerabilidade à injeção de SQL**|Um aplicativo gerou uma instrução SQL com falha no banco de dados. Isso pode indicar uma possível vulnerabilidade a ataques de injeção de SQL. Há dois motivos possíveis para a geração de uma instrução com erro: Seja, um defeito no código do aplicativo construiu a instrução SQL com falha. Ou, o código do aplicativo ou os procedimentos armazenados não corrigiram a entrada do usuário ao construir a instrução SQL com falha, que pode ser explorada para injeção de SQL.|
|**Possível injeção de SQL**|Uma exploração ativa ocorreu em um aplicativo identificado vulnerável à injeção de SQL. Isso significa que um invasor está tentando injetar instruções SQL mal-intencionadas usando o código do aplicativo vulnerável ou procedimentos armazenados.|
|**Acesso de local incomum**|Houve uma alteração no padrão de acesso para o SQL Server, em que alguém fez logon no SQL Server a partir de uma localização geográfica incomum. Em alguns casos, o alerta detecta uma ação legítima (um novo aplicativo ou manutenção do desenvolvedor). Em outros casos, o alerta detecta uma ação mal-intencionada (funcionário antigo, invasor externo).|
|**Acesso de entidade de segurança não familiar**|Houve uma alteração no padrão de acesso para o SQL Server-alguém fez logon no SQL Server usando uma entidade de segurança incomum (usuário do SQL). Em alguns casos, o alerta detecta uma ação legítima (novo aplicativo ou manutenção do desenvolvedor). Em outros casos, o alerta detecta uma ação mal-intencionada (funcionário antigo, invasor externo).|
|**Acesso de um aplicativo potencialmente prejudicial**|Um aplicativo potencialmente prejudicial foi usado para acessar o banco de dados. Em alguns casos, o alerta detecta um teste de segurança que está sendo executado. Em outros casos, o alerta detecta um ataque usando ferramentas comuns de ataque.|
|**Credenciais SQL de força bruta**|Ocorreu um número alto anormal de logons com falha com credenciais diferentes. Em alguns casos, o alerta detecta um teste de segurança que está sendo executado. Em outros casos, o alerta detecta ataques de força bruta.|

Para obter mais informações sobre alertas de detecção de ameaças do SQL, consulte[detecção de ameaças do banco de dados SQL do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview)e examine a seção alertas de detecção de ameaças. Veja também [como a central de segurança do Azure ajuda a revelar um Cyberattack](https://azure.microsoft.com/blog/how-azure-security-center-helps-reveal-a-cyberattack/) para exibir um exemplo de como a central de segurança usou a detecção de atividades mal-INTENCIONADAS do SQL para descobrir um ataque.

## Armazenamento do Azure<a name="azure-storage"></a>

>[!NOTE]
> A proteção avançada contra ameaças para o armazenamento do Azure está disponível atualmente somente para armazenamento de BLOBs.

A Proteção Avançada contra Ameaças do Armazenamento do Azure oferece uma camada adicional de inteligência de segurança que detecta tentativas incomuns e potencialmente prejudiciais de acessar ou explorar contas de armazenamento. Essa camada de proteção permite que você resolva as ameaças sem exigir que você seja um especialista em segurança e gerencie sistemas de monitoramento de segurança.

A central de segurança analisa os logs de diagnóstico de solicitações de leitura, gravação e exclusão para o armazenamento de BLOBs para detectar ameaças e dispara alertas quando ocorrem anomalias na atividade. Para obter mais informações, consulte para [Configurar o log de análise de armazenamento](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account#configure-logging) para obter mais informações.

> [!div class="mx-tableFixed"]

|Alerta|Descrição|
|---|---|
|**Anomalias de acesso à localização incomum**|A análise de tráfego de rede de amostra detectou comunicação de saída anômala protocolo RDP (RDP) proveniente de um recurso em sua implantação. Essa atividade é considerada anormal para esse ambiente e pode indicar que o recurso foi comprometido e agora é usado para ponto de extremidade RDP externo de força bruta. Observe que esse tipo de atividade poderia possivelmente fazer com que seu IP fosse sinalizado como mal-intencionado por entidades externas.|
|**Anomalias de acesso ao aplicativo**|Indica que um aplicativo incomum acessou essa conta de armazenamento. Uma causa potencial é que um invasor acessou sua conta de armazenamento usando um novo aplicativo.|
|**Anomalias de acesso anônimo**|Indica que há uma alteração no padrão de acesso para uma conta de armazenamento. Por exemplo, a conta foi acessada anonimamente (sem nenhuma autenticação), o que é inesperado em comparação com o padrão de acesso recente nessa conta. Uma causa potencial é que um invasor explorou o acesso de leitura público a um contêiner que mantém o armazenamento de BLOBs.|
|**Anomalias de vazamento de dados**|Indica que uma quantidade incomum de dados muito grande foi extraída em comparação com a atividade recente neste contêiner de armazenamento. Uma causa potencial é que um invasor extraiu uma grande quantidade de dados de um contêiner que mantém o armazenamento de BLOBs.|
|**Anomalia de exclusão inesperada**|Indica que uma ou mais operações de exclusão inesperadas ocorreram em uma conta de armazenamento, em comparação com a atividade recente nesta conta. Uma causa potencial é que um invasor excluiu dados de sua conta de armazenamento.|
|**Carregar o pacote de serviço de nuvem do Azure**|Indica que um pacote de serviço de nuvem do Azure (arquivo. cspkg) foi carregado em uma conta de armazenamento de maneira incomum, em comparação com a atividade recente nessa conta. Uma causa potencial é que um invasor está se preparando para implantar código mal-intencionado de sua conta de armazenamento em um serviço de nuvem do Azure.|
|**Anomalia de acesso de permissão**|Indica que as permissões de acesso deste contêiner de armazenamento foram alteradas de maneira incomum. Causa potencial é que um invasor alterou as permissões de contêiner para enfraquecer sua postura de segurança ou para obter persistência.|
|**Anomalias de acesso de inspeção**|Indica que as permissões de acesso de uma conta de armazenamento foram inspecionadas de forma incomum, em comparação com a atividade recente nessa conta. Uma causa potencial é que um invasor executou o reconhecimento para um ataque futuro.|
|**Anomalias de exploração de dados**|Indica que os BLOBs ou contêineres em uma conta de armazenamento foram enumerados de forma anormal, em comparação com a atividade recente nessa conta. Uma causa potencial é que um invasor executou o reconhecimento para um ataque futuro.|

>[!NOTE]
>A proteção avançada contra ameaças para o armazenamento do Azure não está disponível atualmente nas regiões do Azure governamental e do soberanas Cloud.

Para obter mais informações sobre os alertas de armazenamento, consulte o artigo [proteção avançada contra ameaças para armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-advanced-threat-protection) e examine a seção alertas de proteção.

## Cosmos DB<a name="cosmos-db"></a>

Os alertas a seguir são gerados por tentativas incomuns e potencialmente prejudiciais de acessar ou explorar contas de Azure Cosmos DB:

|Alerta|Descrição|
|---|---|
|**Acesso de local incomum**|Indica que houve uma alteração no padrão de acesso para uma conta de Cosmos DB. Alguém acessou essa conta a partir de um endereço IP desconhecido, em comparação com a atividade recente. Um invasor acessou uma conta de Cosmos DB ou um usuário legítimo acessou a conta de Cosmos DB de uma localização geográfica nova e incomum. Por exemplo: uma nova manutenção de aplicativo ou desenvolvedor do remoto.|
|**Vazamento de dados incomum**|Indica que houve uma alteração no padrão de extração de dados de uma conta de Cosmos DB. Alguém extraiu uma quantidade incomum de dados em comparação com a atividade recente. Um invasor extraiu uma grande quantidade de dados de um banco de dado Cosmos DB. Por exemplo: data vazamento/vazamento, transferência não autorizada de dados. Ou um usuário ou aplicativo legítimo extraiu uma quantidade incomum de dados de um contêiner. Por exemplo: atividade de backup de manutenção.|

Para obter mais informações, consulte [proteção avançada contra ameaças para Azure Cosmos DB](../cosmos-db/cosmos-db-advanced-threat-protection.md).