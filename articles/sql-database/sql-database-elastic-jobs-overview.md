---
title: Gerenciando bancos de dados de nuvem com escala horizontal | Microsoft Docs
description: "Ilustra o serviço do trabalho de banco de dados elástico"
metakeywords: azure sql database elastic databases
services: sql-database
documentationcenter: 
manager: jhubbard
author: ddove
ms.assetid: 6fa47cf2-1162-4534-a206-6e2d95b78580
ms.service: sql-database
ms.custom: scale out apps
ms.workload: sql-database
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2016
ms.author: ddove
ms.openlocfilehash: 8e84562115a866c0df5e0dee6c7f66c036a74737
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="managing-scaled-out-cloud-databases"></a>Gerenciando bancos de dados de nuvem com escalonamento horizontal
Para gerenciar bancos de dados fragmentados escalados horizontalmente, o recurso de **Trabalhos do Banco de Dados Elástico** (visualização) permite executar um script Transact-SQL (T-SQL) confiável em um grupo de bancos de dados, incluindo:

* um conjunto personalizado de bancos de dados (explicado abaixo)
* todos os bancos de dados em um [pool elástico](sql-database-elastic-pool.md)
* um conjunto de fragmentos (criado usando a [biblioteca de cliente do Banco de Dados Elástico](sql-database-elastic-database-client-library.md)). 

## <a name="documentation"></a>Documentação
* [Instalar os componentes de trabalho de Banco de Dados Elástico](sql-database-elastic-jobs-service-installation.md). 
* [Introdução aos trabalhos do Banco de Dados Elástico](sql-database-elastic-jobs-getting-started.md).
* [Criar e gerenciar trabalhos usando o PowerShell](sql-database-elastic-jobs-powershell.md).
* [Criar e gerenciar Bancos de Dados SQL do Azure com escala horizontal](sql-database-elastic-jobs-getting-started.md)

Os **trabalhos de Banco de Dados Elástico** atualmente são um serviço de nuvem do Azure hospedado pelo cliente, que permite a execução de tarefas administrativas ad hoc e agendadas, as quais são chamadas de **trabalhos**. Com os trabalhos, você pode gerenciar de maneira fácil e confiável grandes grupos de Bancos de Dados do Azure SQL executando scripts Transact-SQL para executar operações administrativas. 

![Serviço do trabalho de banco de dados elástico][1]

## <a name="why-use-jobs"></a>Por que usar os trabalhos?
**Gerenciar**

Realize facilmente alterações de esquema, gerenciamento de credenciais, atualizações de dados de referência, desempenho de coleta de dados ou coleção de telemetria do locatário (cliente).

**Relatórios**

Agregue dados de uma coleção de Bancos de Dados SQL do Azure em uma tabela de destino único.

**Reduzir a sobrecarga**

Normalmente, você deve se conectar a cada banco de dados de forma independente a fim de executar instruções Transact-SQL ou realizar outras tarefas administrativas. Um trabalho lida com a tarefa de fazer logon em cada banco de dados no grupo de destino. Você também define, mantém e persiste em scripts T-SQL que serão executados em um grupo de Bancos de Dados SQL do Azure.

**Contabilização**

Os trabalhos executam o script e registram em log o status de execução para cada banco de dados. Você também tem a repetição automática quando ocorrem falhas.

**Flexibilidade**

Defina grupos personalizados de Bancos de Dados SQL do Azure e defina agendas para executar um trabalho.

> [!NOTE]
> No portal do Azure, apenas um conjunto reduzido de funções limitadas aos pools elásticos do SQL Azure está disponível. Use as APIs do PowerShell para acessar o conjunto completo de funcionalidades atuais.
> 
> 

## <a name="applications"></a>Aplicativos
* Realize tarefas administrativas, como a implantação de um novo esquema.
* Atualize informações de dados de referência do produto comuns a todos os bancos de dados. Ou agende atualizações automáticas a cada dia da semana, após o expediente.
* Recompilar índices para melhorar o desempenho da consulta. A recriação pode ser configurada para executar em um conjunto de bancos de dados de modo recorrente, como fora dos horários de pico.
* Coletar resultados de consulta de um conjunto de bancos de dados em uma tabela central em uma base contínua. Consultas de desempenho podem ser executadas continuamente e configuradas para disparar tarefas adicionais a serem executadas.
* Executar consultas de processamento de dados mais longas em um grande conjunto de bancos de dados, por exemplo, a coleta de telemetria do cliente. Resultados são coletados em uma única tabela de destino para análise posterior.

## <a name="elastic-database-jobs-end-to-end"></a>Trabalhos de Banco de Dados Elástico: ponta a ponta
1. Instalar os componentes de **trabalhos de banco de dados elástico** . Para saber mais informações, consulte [Instalando trabalhos de banco de dados elástico](sql-database-elastic-jobs-service-installation.md). Se a instalação falhar, confira [como desinstalar](sql-database-elastic-jobs-uninstall.md).
2. Use as APIs do PowerShell para acessar mais funcionalidades, por exemplo, criando coleções de bancos de dados personalizadas, adicionando agendas e/ou coletando conjuntos de resultados. Use o portal para instalação simples e criação/monitoramento de trabalhos limitados para execução em um **pool elástico**. 
3. Criar credenciais criptografadas para execução do trabalho e [adicionar o usuário (ou função) para cada banco de dados no grupo](sql-database-security-overview.md).
4. Criar um script T-SQL idempotente que pode ser executado em cada banco de dados no grupo. 
5. Siga estas etapas para criar trabalhos usando o portal do Azure: [Criando e gerenciando trabalhos do Banco de Dados Elástico](sql-database-elastic-jobs-create-and-manage.md). 
6. Ou use scripts do PowerShell: [Criar e gerenciar trabalhos de banco de dados elástico de Banco de Dados SQL usando o PowerShell (visualização)](sql-database-elastic-jobs-powershell.md).

## <a name="idempotent-scripts"></a>Scripts idempotentes
Os scripts devem ser [idempotentes](https://en.wikipedia.org/wiki/Idempotence). Em termos simples, "idempotente" significa que, se o script tiver êxito e for executado novamente, o mesmo resultado ocorrerá. Um script pode falhar devido a problemas de rede transitórios. Nesse caso, o trabalho repetirá automaticamente a execução do script por um número predefinido de vezes antes de desistir. Um script idempotente tem o mesmo resultado, mesmo que tenha sido executado com êxito duas vezes. 

É uma tática simples para testar a existência de um objeto antes de criá-lo.  

    IF NOT EXIST (some_object)
    -- Create the object 
    -- If it exists, drop the object before recreating it.

Da mesma forma, um script deve ser capaz de realizar a execução com êxito testando e combatendo logicamente quaisquer condições que encontrar.

## <a name="failures-and-logs"></a>Falhas e logs
Se um script falhar após várias tentativas, o trabalho registrará em log o erro e continuará. Após o término de um trabalho (ou seja, uma execução em relação a todos os bancos de dados no grupo), você pode verificar sua lista de tentativas com falha. Os logs fornecem detalhes para depurar scripts com erros. 

## <a name="group-types-and-creation"></a>Tipos de grupos e criação
Há dois tipos de grupos: 

1. Conjuntos de fragmento
2. Grupos personalizados

Grupos de conjuntos de fragmentos são criados usando as [ferramentas de Banco de Dados Elástico](sql-database-elastic-scale-introduction.md). Quando você cria um grupo de conjunto de fragmentos, bancos de dados são adicionados ou removidos do grupo automaticamente. Por exemplo, um novo fragmento estará automaticamente no grupo quando você adicioná-lo ao mapa de fragmentos. Um trabalho pode, então, ser executado no grupo.

Os grupos personalizados, por outro lado, são definidos rigidamente. Você deve adicionar ou remover explicitamente bancos de dados de grupos personalizados. Se um banco de dados no grupo for interrompido, o trabalho tentará executar o script em relação ao banco de dados, resultando em uma eventual falha. No momento, grupos criados usando o portal do Azure são grupos personalizados. 

## <a name="components-and-pricing"></a>Componentes e preços
Os seguintes componentes trabalham juntos para criar um Serviço de Nuvem do Azure que permite a execução ad hoc de trabalhos administrativos. Os componentes são instalados e configurados automaticamente durante a configuração, em sua assinatura. Você pode identificar os serviços, uma vez que todos têm o mesmo nome gerado automaticamente. O nome é exclusivo e é formado pelo prefixo "edj" seguido por 21 caracteres gerados aleatoriamente.

* **Serviço de Nuvem do Azure**: os trabalhos de banco de dados elástico (visualização) são fornecidos como um Serviço de Nuvem do Azure hospedado pelo cliente para a execução das tarefas solicitadas. No portal, o serviço é implantado e hospedado em sua assinatura do Microsoft Azure. O serviço implantado por padrão é executado com o mínimo de duas funções de trabalho a fim de manter a alta disponibilidade. O tamanho padrão de cada função de trabalho (ElasticDatabaseJobWorker) é executado em uma instância de A0. Para obter os preços, confira [preços dos serviços de Nuvem](https://azure.microsoft.com/pricing/details/cloud-services/). 
* **Banco de dados SQL do Azure**: O serviço usa um Banco de Dados SQL do Azure conhecido como o **banco de dados de controle** para armazenar todos os metadados de trabalho. A camada de serviço padrão é S0. Para obter os preços, confira [Preços de Banco de Dados SQL](https://azure.microsoft.com/pricing/details/sql-database/).
* **Barramento de Serviço do Azure**: um Barramento de Serviço do Azure serve para coordenação do trabalho no Serviço de Nuvem do Azure. Consulte [Preços de Barramento de Serviço](https://azure.microsoft.com/pricing/details/service-bus/).
* **Armazenamento do Azure**: uma conta de Armazenamento do Azure é usada para armazenar o log de saída de diagnóstico no caso em que um problema exige mais depuração (consulte [Habilitando o diagnóstico nos Serviços de Nuvem do Azure e em máquinas virtuais](../cloud-services/cloud-services-dotnet-diagnostics.md)). Para obter os preços, confira [Preços de Armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/).

## <a name="how-elastic-database-jobs-work"></a>Como os trabalhos de banco de dados elástico funcionam
1. Um **banco de dados de controle** , que armazena todos os dados de estado e metadados, é atribuído a um banco de dados SQL do Azure.
2. O banco de dados de controle é acessado pelo **serviço de trabalhos** para iniciar e acompanhar os trabalhos a serem executados.
3. Duas funções diferentes se comunicam com o banco de dados de controle: 
   * Controlador: determina quais trabalhos necessitam de tarefas para executar o trabalho solicitado e faz novas tentativas com aqueles trabalhos com falha, criando novas tarefas de trabalho.
   * Execução de tarefas de trabalho: executa as tarefas de trabalho.

### <a name="job-task-types"></a>Tipos de tarefa de trabalho
Há vários tipos de tarefas de trabalho que realizarão a execução de trabalhos:

* ShardMapRefresh: consulta o mapa do fragmentos para determinar todos os bancos de dados usados como fragmentos
* ScriptSplit: divide o script contido em instruções “GO” em lotes
* ExpandJob: cria trabalhos filho para cada banco de dados de um trabalho que se destina a um grupo de bancos de dados
* ScriptExecution: executa um script em um banco de dados específico usando credenciais definidas
* Dacpac: aplica um DACPAC a um banco de dados específico usando credenciais específicas

## <a name="end-to-end-job-execution-work-flow"></a>Fluxo de trabalho de execução de trabalho de ponta a ponta
1. Usando o Portal ou a API do PowerShell, um trabalho é inserido no **banco de dados de controle**. O trabalho solicita a execução de um script Transact-SQL em um grupo de bancos de dados usando credenciais específicas.
2. O controlador identifica o novo trabalho. Tarefas de trabalho são criadas e executadas para dividir o script e para atualizar os bancos de dados do grupo. Por fim, um novo trabalho é criado e executado para expandir o trabalho e criar novos trabalhos filho; cada um desses trabalhos filho é especificado para executar o script Transact-SQL em um banco de dados individual no grupo.
3. O controlador identifica os trabalhos filho criados. Para cada trabalho, o controlador cria e dispara uma tarefa de trabalho para executar o script em um banco de dados. 
4. Depois de concluir todas as tarefas de trabalho, o controlador atualiza os trabalhos para um estado concluído. 
   A qualquer momento durante a execução do trabalho, a API do PowerShell pode ser usada para exibir o estado atual dessa execução. Todos os tempos retornados pelas APIs do PowerShell são representados em formato UTC. Se desejado, uma solicitação de cancelamento pode ser iniciada para interromper um trabalho. 

## <a name="next-steps"></a>Próximas etapas
[Instale os componentes](sql-database-elastic-jobs-service-installation.md) e [crie e adicione um log a cada banco de dados no grupo de bancos de dados](sql-database-manage-logins.md). Para entender mais a criação de trabalho e o gerenciamento, consulte [criar e gerenciar trabalhos do banco de dados elástico](sql-database-elastic-jobs-create-and-manage.md). Consulte também a [Introdução aos trabalhos de Banco de Dados Elástico](sql-database-elastic-jobs-getting-started.md).

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-overview/elastic-jobs.png
<!--anchors-->


