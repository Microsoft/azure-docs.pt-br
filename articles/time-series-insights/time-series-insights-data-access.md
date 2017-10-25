---
title: "Políticas de acesso de dados nas Análise de Séries Temporais do Azure | Microsoft Docs"
description: "Neste tutorial, você aprenderá a gerenciar políticas de acesso de dados nas Análise de Séries Temporais"
keywords: 
services: time-series-insights
documentationcenter: 
author: op-ravi
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: tsi
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/01/2017
ms.author: omravi
ms.openlocfilehash: 5258bf5de6f7aa1ea246f1235e7d362b1b7d0181
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="grant-data-access-to-a-time-series-insights-environment-using-azure-portal"></a>Conceder acesso a dados em um ambiente de Análise de Séries Temporais usando o portal do Azure

Os ambientes de Análise de Séries Temporais possuem dois tipos independentes de políticas de acesso:

* Políticas de acesso de gerenciamento
* Políticas de acesso de dados

Os dois tipos de políticas concedem às entidades de segurança (usuários e aplicativos) do Azure Active Directory várias permissões em um ambiente específico. As entidades de segurança (usuários e aplicativos) devem pertencer ao active directory (ou "Azure locatário") associado à assinatura que contém o ambiente.

As políticas de acesso de gerenciamento concedem permissões relacionadas à configuração do ambiente, como
*   Criação e exclusão do ambiente, origens de evento, conjuntos de dados de referência e
*   Gerenciamento das políticas de acesso de dados.

As políticas de acesso a dados concedem permissões para emitir consultas de dados, manipular dados de referência no ambiente e salvar consultas compartilhadas e perspectivas associadas ao ambiente.

Os dois tipos de políticas permitem uma separação clara entre o acesso ao gerenciamento do ambiente e o acesso aos dados dentro do ambiente. Por exemplo, é possível configurar um ambiente de modo que o proprietário/criador do ambiente é removido do acesso aos dados. Assim como usuários e serviços que têm permissão para ler dados do ambiente podem não receber permissão de acesso à configuração do ambiente.

## <a name="grant-data-access"></a>Conceder acesso a dados
As etapas a seguir mostram como conceder acesso a dados para uma entidade de usuário:

1.  Entre no [Portal do Azure](https://portal.azure.com).
2.  Clique em "Todos os recursos" no menu à esquerda do portal do Azure.
3.  Selecione o seu ambiente de Análise de Séries Temporais.

  ![Gerenciar a fonte das Análise de Séries Temporais - ambiente](media/data-access/getstarted-grant-data-access1.png)

4.  Selecione "Acesso ao plano de dados", clique em "Adicionar"

  ![Gerenciar a fonte das Análise de Séries Temporais - adicionar](media/data-access/getstarted-grant-data-access2.png)

5.  Clique em “Selecionar usuário”.
6.  Procure e selecione o usuário por email.
7.  Clique em "Selecionar" na folha "Selecionar usuário".

  ![Gerenciar a fonte das Análise de Séries Temporais - selecionar usuário](media/data-access/getstarted-grant-data-access3.png)

8.  Clique em “Selecionar função”.
9.  Selecione "Colaborador" se quiser permitir que o usuário altere os dados de referência e compartilhe consultas salvas e perspectivas com outros usuários do ambiente. Caso contrário, selecione "Leitor" para permitir que o usuário consulte os dados no ambiente e salve as consultas (não compartilhadas) pessoais no ambiente.
10. Clique em "Ok" na folha "Selecionar função".

  ![Gerenciar a fonte das Análise de Séries Temporais - selecionar função](media/data-access/getstarted-grant-data-access4.png)

11. Clique em "Ok" na folha "Selecionar função do usuário".
12. Você deverá ver:

  ![Gerenciar a fonte das Análise de Séries Temporais - resultados](media/data-access/getstarted-grant-data-access5.png)

## <a name="next-steps"></a>Próximas etapas

* [Criar uma origem de evento](time-series-insights-add-event-source.md)
* [Enviar eventos](time-series-insights-send-events.md) para a origem do evento
* Exibir seu ambiente no [Portal de Análise de Séries Temporais](https://insights.timeseries.azure.com)
