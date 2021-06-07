---
title: incluir arquivo
description: arquivo de inclusão
services: automation
author: mgoedtel
ms.service: automation
ms.topic: include
ms.date: 02/08/2021
ms.author: magoedte
ms.custom: include file
ms.openlocfilehash: 3ee44509997a16fc7f06fd5a24e473d7011bde40
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99974255"
---
#### <a name="process-automation"></a>Automação de processos

| Recurso | Limite |Observações|
| --- | --- |---|
| Número máximo de novos trabalhos que podem ser enviados a cada 30 segundos por conta de Automação do Azure (trabalhos não agendados) |100 |Quando esse limite for atingido, haverá falha nas solicitações subsequentes para criar um trabalho. O cliente recebe uma resposta de erro.|
| Número máximo de trabalhos simultâneos em execução na mesma instância de tempo por conta de Automação (trabalhos não agendados) |200 |Quando esse limite for atingido, haverá falha nas solicitações subsequentes para criar um trabalho. O cliente recebe uma resposta de erro.|
| Tamanho máximo de armazenamento de metadados de trabalho por um período contínuo de 30 dias | 10 GB (aproximadamente 4 milhões de trabalhos)|Quando esse limite for atingido, haverá falha nas solicitações subsequentes para criar um trabalho. |
| Limite máximo de fluxos de trabalho|1 MiB|Um fluxo não pode ser maior do que 1 MiB.|
| Número máximo de módulos que podem ser importados a cada 30 segundos por conta de Automação |5 ||
| Tamanho máximo de um módulo |100 MB ||
| Tamanho máximo de um arquivo de configuração de nó | 1 MB | Aplica-se à configuração de estado |
| Tempo de execução do trabalho, camada gratuita |500 minutos por assinatura por mês ||
| Quantidade máxima de espaço em disco permitida por área restrita<sup>1</sup> |1 GB |Aplicável somente às áreas restritas do Azure.|
| Quantidade máxima de memória fornecida a uma área restrita<sup>1</sup> |400 MB |Aplicável somente às áreas restritas do Azure.|
| Número máximo de soquetes de rede permitidos por área restrita<sup>1</sup> |1,000 |Aplicável somente às áreas restritas do Azure.|
| Runtime máximo permitido por runbook<sup>1</sup> |3 horas |Aplicável somente às áreas restritas do Azure.|
| Número máximo de contas de Automação em uma assinatura |Sem limite ||
| Número máximo de Grupos do Hybrid Worker por Conta de Automação|4.000||
|Número máximo de trabalhos simultâneos que podem ser executados em um Hybrid Runbook Worker|50 ||
| Tamanho máximo do parâmetro de trabalho do runbook   | 512 quilobytes||
| Parâmetros máximos do runbook   | 50|Caso alcance o limite de 50 parâmetros, será possível passar uma cadeia de caracteres JSON ou XML para um parâmetro e analisá-la com o runbook.|
| Tamanho máximo do conteúdo do webhook |  512 quilobytes|
| Máximo de dias durante os quais os dados do trabalho serão retidos|30 dias|
| Tamanho máximo do estado do fluxo de trabalho do PowerShell |6 MB| Aplicável a runbooks do fluxo de trabalho do PowerShell na execução do ponto de verificação do fluxo de trabalho.|

<sup>1</sup>A área restrita é um ambiente compartilhado que pode ser usado por vários trabalhos. Os trabalhos que usam a mesma área restrita são controlados pelas limitações de recursos da área restrita.

#### <a name="change-tracking-and-inventory"></a>Controle de Alterações e Inventário

A tabela a seguir mostrará os limites de itens rastreados por computador para controle de alterações.

| **Recurso** | **Limite**| **Observações** |
|---|---|---|
|Arquivo|500||
|Registro|250||
|Software do Windows|250|Não inclui as atualizações de software.|
|Pacotes do Linux|1\.250||
|Serviços|250||
|Daemon|250||

#### <a name="update-management"></a>Gerenciamento de atualizações

A tabela a seguir mostrará os limites para o Gerenciamento de Atualizações.

| **Recurso** | **Limite**| **Observações** |
|---|---|---|
|Número de computadores por implantação de atualização|1000||