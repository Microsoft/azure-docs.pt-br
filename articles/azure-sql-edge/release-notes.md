---
title: Notas de versão do Azure SQL Edge
description: Notas de versão detalhando o que há de novo ou o que mudou nas imagens do Azure SQL Edge.
keywords: Borda do SQL das notas de versão
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
ms.subservice: ''
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sstein
ms.date: 11/24/2020
ms.openlocfilehash: e078fb91b3279b6f4321cd51dfb094f82bbe5f14
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98696372"
---
# <a name="azure-sql-edge-release-notes"></a>Notas de versão do Azure SQL Edge 

Este artigo descreve as novidades e o que mudou em todas as novas compilações do Azure SQL Edge.

## <a name="azure-sql-edge-102"></a>1.0.2 de borda do SQL do Azure

15.0.2000.1554 de Build do mecanismo SQL

### <a name="fixes"></a>Correções

- Streaming T-SQL  
   - Correção na propriedade e permissões para objetos de streaming
   - Aprimoramentos de log com a rotação de log e a prefixação de log
   - Azure Stream Analytics: melhorias de log, melhorar o código de erro/mensagens de erro em adaptadores 

- ONNX
    - Correções de bugs para cenários de consulta paralela e falhas de limpeza de modelo
    - Tempo de execução do ONNX atualizado para 1.5.1

## <a name="azure-sql-edge-101"></a>1.0.1 do Azure SQL Edge

15.0.2000.1553 de Build do mecanismo SQL

### <a name="whats-new"></a>Novidades

- Permitir Date_Bucket expressões definidas em colunas computadas.

### <a name="fixes"></a>Correções

- Correção da política de retenção para remover uma tabela que tem uma política de retenção habilitada com um tempo limite infinito
- Suporte à implantação do DacFx para recursos de streaming e recursos de política de retenção 
- Correção de implantação do DacFx para habilitar a implantação de uma pasta aninhada em uma URL SAS 
- PREVER correção para dar suporte a nomes de coluna longos em mensagens de erro

## <a name="azure-sql-edge-100-rtm"></a>Azure SQL Edge 1.0.0 (RTM)

15.0.2000.1552 de Build do mecanismo SQL

### <a name="whats-new"></a>Novidades
- Imagens de contêiner baseadas no Ubuntu 18, 4 
- Suporte para `IGNORE NULL` e `RESPECT NULL` sintaxe com `LAST_VALUE()` e `FIRST_VALUE()` funções 
- Melhorias de confiabilidade para prever com ONNX
- Suporte para limpeza com base na política de retenção de dados:
   - Suporte ao buffer de anéis para uma tarefa de limpeza de retenção para solução de problemas
- Suporte ao novo recurso: 
   - Recuperação rápida
   - Ajuste automático de consultas
   - Cenários de execução paralela
- Melhorias de economia de energia para o modo de baixa energia
- Suporte ao novo recurso de streaming: 
   - [Janelas de instantâneo](/stream-analytics-query/snapshot-window-azure-stream-analytics): um novo tipo de janela permite que você agrupe por eventos que chegam ao mesmo tempo.
   - [TopOne](/stream-analytics-query/topone-azure-stream-analytics) e [CollectTop](/stream-analytics-query/collecttop-azure-stream-analytics) podem ser habilitados como funções analíticas. Você pode retornar registros ordenados pela coluna de sua escolha. Eles não precisam fazer parte de uma janela. 
   - Melhorias no [MATCH_RECOGNIZE](/stream-analytics-query/match-recognize-stream-analytics). 

### <a name="fixes"></a>Correções
- Mensagens de erro adicionais e detalhes para solução de problemas de operações de streaming do T-SQL 
- Melhorias para preservar a vida útil da bateria no modo ocioso 
- Correções do mecanismo de streaming T-SQL: 
   - Limpeza de trabalhos de streaming interrompidos 
   - Correções para localização 
   - Manipulação de Unicode aprimorada 
   - Depuração aprimorada para streaming T-SQL do SQL Edge, permitindo que os usuários consultem erros de falha de trabalho de get_streaming_job
- Limpeza baseada na política de retenção de dados: 
    - Correções para a criação da política de retenção e cenários de limpeza
- Correções para tarefas de temporizador em segundo plano para melhorar a economia de energia para o modo de baixa energia

### <a name="known-issues"></a>Problemas conhecidos 
- A função T-SQL Date_Bucket não pode ser usada em uma coluna computada.


## <a name="ctp-23"></a>CTP 2.3
15.0.2000.1549 de Build do mecanismo SQL
### <a name="whats-new"></a>Novidades
- Suporte para origens personalizadas na função Date_Bucket () 
- Suporte para arquivos BACPAC como parte da implantação do SQL
- Suporte para limpeza com base na política de retenção de dados:      
   - Suporte a DDL para habilitar a política de retenção 
   - Limpar os procedimentos armazenados e a tarefa de limpeza em segundo plano
   - Eventos estendidos para monitorar tarefas de limpeza

### <a name="fixes"></a>Correções
- Mensagens de erro adicionais e detalhes para solução de problemas de operações de streaming do T-SQL 
- Melhorias para preservar a vida útil da bateria no modo ocioso 
- Mecanismo de streaming T-SQL: 
   - Correção da marca d' água presa na janela de salto Subfluxo 
   - Correção para tratamento de exceção de estrutura para garantir que ele seja coletado como um erro de usuário acionável


## <a name="ctp-22"></a>CTP 2.2
15.0.2000.1546 de Build do mecanismo SQL
### <a name="whats-new"></a>Novidades
- Suporte para contêineres não raiz 
- Suporte para a coleta de dados de diagnóstico e uso 
- Atualizações de streaming T-SQL:
   - Suporte para caracteres Unicode para nomes de objetos de fluxo

### <a name="fixes"></a>Correções
- Atualizações de streaming T-SQL:
   - Aprimoramentos na limpeza de processos
   - Aprimoramentos no log e diagnóstico
- Melhoria de desempenho para ingestão de dados

## <a name="ctp-21"></a>CTP 2.1 
15.0.2000.1545 de Build do mecanismo SQL
### <a name="fixes"></a>Correções
- Permitiu que os modelos de previsão-com-ONNX manipulassem um problema de CPUID no ARM 
- Tratamento aprimorado do caminho de falha quando o streaming T-SQL começa
- Valor corrigido do atraso da marca d' água em métricas de trabalho quando não há dados.
- Correção de um problema com o adaptador de saída quando o adaptador tiver um esquema variável entre os lotes  

## <a name="ctp-20"></a>CTP 2.0 
15.0.2000.1401 de Build do mecanismo SQL
### <a name="whats-new"></a>Novidades
-   Nome do produto atualizado para o *Azure SQL Edge*
-  Função Date_Bucket:
    - Suporte para tipos de data, hora e DateTime
- PREVER com ONNX:
    - Requisito de ONNX para o parâmetro de tempo de execução  
- Suporte a streaming T-SQL (versão prévia limitada) 
 
### <a name="known-issues"></a>Problemas conhecidos

- Problema: falhas potenciais com a aplicação de DACPAC na inicialização devido a um problema de tempo.
- Solução alternativa: reinicie SQL Server. Caso contrário, o contêiner tentará aplicar o DACPAC novamente.

### <a name="request-support"></a>Solicitar suporte
Você pode solicitar suporte na [página de suporte](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Selecione os seguintes campos: 
- **Tipo de problema**: *Técnico* 
- **Serviço**: *IOT Edge*
- **Tipo de problema**: *meu problema está relacionado a um módulo de IOT Edge*
- **Subtipo de problema**: *borda do SQL do Azure*

:::image type="content" source="media/get-support/support-ticket.png" alt-text="Captura de tela mostrando um tíquete de suporte de exemplo.":::

## <a name="ctp-15"></a>CTP 1,5
15.0.2000.1331 de Build do mecanismo SQL
### <a name="whats-new"></a>Novidades
- Função Date_Bucket:
    - Suporte para o tipo DateTimeOffset
- PREVER com modelos ONNX:
  - Suporte a NVARCHAR
 
## <a name="ctp-14"></a>CTP 1,4
15.0.2000.1247 de Build do mecanismo SQL
### <a name="whats-new"></a>Novidades
-   PREVER com modelos ONNX:
    - Suporte a VARCHAR
    - Migração para o ONNX Runtime versão 1,0 

- Os seguintes recursos estão habilitados:
  - Suporte a CDC
  - Tabela de histórico com compactação
  - Um fator de escala superior para leitura antecipada de log
  - Aplicação de filtro ES do modo de lote
  - Otimizações de Read-Ahead
 
## <a name="ctp-13"></a>CTP 1,3
15.0.2000.1147 de Build do mecanismo SQL
### <a name="whats-new"></a>Novidades
- Implantação do portal do Azure IoT: 
    - Suporte para implantação de imagens AMD64 e ARM
    - Suporte para criação de trabalhos de streaming
    - Implantação do DACPAC
- PREVER com modelos ONNX:
    - Suporte a tipo numérico
- Os seguintes recursos estão habilitados:
    - Verificação de aplicação agregada ao repositório de coluna
    - Verificações em boas viagens
- Trabalho de redução de consumo de memória e espaço
