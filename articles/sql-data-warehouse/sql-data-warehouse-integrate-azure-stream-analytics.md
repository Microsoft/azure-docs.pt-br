---
title: Usar o Stream Analytics do Azure com o SQL Data Warehouse | Microsoft Docs
description: Dicas para usar o Stream Analytics do Azure com o Azure SQL Data Warehouse para desenvolver as soluções.
services: sql-data-warehouse
author: WenJason
manager: digimobile
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: consume
origin.date: 03/22/2019
ms.date: 04/01/2019
ms.author: v-jay
ms.reviewer: igorstan
ms.openlocfilehash: 3a260102e9964cfc248ed3dbabab487bd1c5dfc2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61474295"
---
# <a name="use-azure-stream-analytics-with-sql-data-warehouse"></a>Usar o Stream Analytics do Azure com o SQL Data Warehouse
A Stream Analytics do Azure é um serviço completamente gerenciado que oferece baixa latência, alta disponibilidade e processamento escalonável de eventos complexos ao longo do fluxo de dados na nuvem. Você pode aprender as noções básicas lendo [Introdução ao Stream Analytics do Azure][Introduction to Azure Stream Analytics]. Depois, você pode saber como criar uma solução de ponta a ponta com o Stream Analytics seguindo o tutorial [Introdução ao uso do Stream Analytics do Azure][Get started using Azure Stream Analytics].

Neste artigo, você aprenderá a usar seu banco de dados do Azure SQL Data Warehouse como um coletor de saída para suas tarefas do Stream Analytics.

## <a name="prerequisites"></a>Pré-requisitos
Primeiro, execute as etapas a seguir no tutorial [Introdução ao uso do Stream Analytics do Azure][Get started using Azure Stream Analytics].  

1. Criar uma entrada de Hub de eventos
2. Configurar e iniciar o aplicativo gerador de evento
3. Provisionar um trabalho de análise de fluxo
4. Especifique a entrada e a consulta do trabalho

Em seguida, crie um banco de dados do SQL Data Warehouse do Azure

## <a name="specify-job-output-azure-sql-data-warehouse-database"></a>Especifique a saída do trabalho: Banco de dados do SQL Data Warehouse do Azure
### <a name="step-1"></a>Etapa 1
No trabalho do Stream Analytics, clique em **saída** na parte superior da página e, em seguida, clique **adicionar**.

### <a name="step-2"></a>Etapa 2
Selecione o banco de dados SQL.

### <a name="step-3"></a>Etapa 3
Insira os seguintes valores na próxima página:

* *Alias de saída*: Insira um nome amigável para essa saída de trabalho.
* *Assinatura*:
  * se o seu banco de dados do SQL Data Warehouse estiver na mesma assinatura que o trabalho do Stream Analytics, selecione Usar Banco de Dados SQL da Assinatura Atual.
  * Se o seu banco de dados estiver em uma assinatura diferente, selecione Usar Banco de Dados SQL de Outra Assinatura.
* *Banco de dados*: Especifique o nome de um banco de dados de destino.
* *Nome do Servidor*: Especifique o nome do servidor do banco de dados que você acabou de especificar. Você pode usar o portal do Azure para encontrá-lo.

![][server-name]

* *Nome de Usuário*: Especifique o nome de usuário de uma conta que tenha permissões de gravação para o banco de dados.
* *Senha*: Forneça a senha da conta de usuário especificada.
* *Tabela*: Especifique o nome da tabela de destino no banco de dados.

![][add-database]

### <a name="step-4"></a>Etapa 4
Clique no botão de verificação para adicionar essa saída e para verificar se o Stream Analytics pode se conectar com êxito ao banco de dados.

Quando a conexão ao banco de dados tiver êxito, você verá uma notificação no portal. Você pode clicar em testar para testar a conexão ao banco de dados.

## <a name="next-steps"></a>Próximas etapas
Para obter uma visão geral da integração, consulte [Visão geral de integração do SQL Data Warehouse][SQL Data Warehouse integration overview].

Para obter mais dicas de desenvolvimento, consulte [Visão geral de desenvolvimento do SQL Data Warehouse][SQL Data Warehouse development overview].

<!--Image references-->

[add-output]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/add-output.png
[server-name]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/dw-server-name.png
[add-database]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/add-database.png
[test-connection]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/test-connection.png

<!--Article references-->

[Introduction to Azure Stream Analytics]: ../stream-analytics/stream-analytics-introduction.md
[Get started using Azure Stream Analytics]: ../stream-analytics/stream-analytics-real-time-fraud-detection.md
[SQL Data Warehouse development overview]:  ./sql-data-warehouse-overview-develop.md
[SQL Data Warehouse integration overview]:  ./sql-data-warehouse-overview-integrate.md

<!--MSDN references-->

<!--Other Web references-->
[Azure Stream Analytics documentation]: /stream-analytics/

<!-- Update_Description: update meta properties, wording update -->