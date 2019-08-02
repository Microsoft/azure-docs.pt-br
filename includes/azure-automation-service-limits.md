---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: automation
author: georgewallace
ms.service: automation
ms.topic: include
ms.date: 12/13/2018
ms.author: gwallace
ms.custom: include file
ms.openlocfilehash: 2823a33b25812a69ad463433bacd9710655c9176
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172329"
---
#### <a name="process-automation"></a>Automação de processos

| Resource | Limite máximo |Observações|
| --- | --- |---|
| Número máximo de novos trabalhos podem ser enviados a cada 30 segundos por conta de automação do Azure (trabalhos não agendados) |100 |Quando esse limite for atingido, as solicitações subsequentes para criar um trabalho falharem. O cliente recebe uma resposta de erro.|
| Número máximo de trabalhos simultâneos em execução na mesma instância do tempo por conta de automação (trabalhos não agendados) |200 |Quando esse limite for atingido, as solicitações subsequentes para criar um trabalho falharem. O cliente recebe uma resposta de erro.|
| Tamanho máximo de armazenamento de metadados de trabalho por um período de 30 dias sem interrupção | 10 GB (aproximadamente 4 milhões trabalhos)|Quando esse limite for atingido, as solicitações subsequentes para criar um trabalho falharem. |
| Limite de fluxo de trabalho máximo|1 MB|Um único fluxo não pode ser maior que 1 MB.|
| Número máximo de módulos que podem ser importados por conta de automação a cada 30 segundos |5 ||
| Tamanho máximo de um módulo |100 MB ||
| Trabalho de tempo de execução, camada gratuita |500 minutos por assinatura por mês ||
| Quantidade máxima de espaço em disco permitida por área restrita<sup>1</sup> |1 GB |Aplica-se a áreas de segurança do Azure.|
| Quantidade máxima de memória fornecida a uma área restrita<sup>1</sup> |400 MB |Aplica-se a áreas de segurança do Azure.|
| Número máximo de soquetes de rede permitido por área restrita<sup>1</sup> |1\.000 |Aplica-se a áreas de segurança do Azure.|
| Tempo de execução máximo permitido por runbook<sup>1</sup> |3 horas |Aplica-se a áreas de segurança do Azure.|
| Número máximo de contas de automação em uma assinatura |Sem limite ||
| Número máximo de grupos do Hybrid Worker por conta de automação|4\.000||
|Número máximo de trabalhos simultâneos que podem ser executados em um único trabalho de Runbook híbrido|50 ||
| Tamanho do parâmetro de trabalho de runbook máximo   | 512 Kbps||
| Parâmetros de runbook máximo   | 50|Se você atingir o limite de 50 parâmetros, você pode passar uma cadeia de caracteres JSON ou XML para um parâmetro e analisá-lo com o runbook.|
| Tamanho da carga máxima de webhook |  512 Kbps|
| Máximo de dias que os dados de trabalho são retidos|30 dias|
| Tamanho máximo de estado de fluxo de trabalho do PowerShell |5 MB| Aplica-se a runbooks de fluxo de trabalho do PowerShell ao fluxo de trabalho de ponto de verificação.|

<sup>1</sup>uma área restrita é um ambiente compartilhado que pode ser usado por vários trabalhos. Trabalhos que usam a mesma área de segurança são associados por limitações de recursos de área restrita.

#### <a name="change-tracking-and-inventory"></a>Controle de Alterações e Inventário

A tabela a seguir mostra os limites de item controladas por máquina para o controle de alterações.

| **Recurso** | **Limite**| **Observações** |
|---|---|---|
|Arquivo|500||
|Registro|250||
|Software do Windows|250|Não inclui as atualizações de software.|
|Pacotes do Linux|1,250||
|Serviços|250||
|Daemon|250||

#### <a name="update-management"></a>Gerenciamento de atualizações

A tabela a seguir mostra os limites para o gerenciamento de atualizações.

| **Recurso** | **Limite**| **Observações** |
|---|---|---|
|Número de máquinas por implantação de atualização|1000||