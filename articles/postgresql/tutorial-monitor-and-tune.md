---
title: 'Tutorial: Monitorar e ajustar – Banco de Dados do Azure para PostgreSQL – Servidor único'
description: Este tutorial descreve o monitoramento e o ajuste no Banco de Dados do Azure para PostgreSQL – Servidor único.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: tutorial
ms.date: 5/6/2019
ms.openlocfilehash: a12068259d82e833826bcac5e6c58059fb51c56c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93336972"
---
# <a name="tutorial-monitor-and-tune-azure-database-for-postgresql---single-server"></a>Tutorial: Monitorar e ajustar o Banco de Dados do Azure para PostgreSQL – Servidor único

Banco de Dados do Azure para PostgreSQL tem recursos que ajudam você a compreender e melhorar o desempenho do servidor. Neste tutorial, você aprenderá a:
> [!div class="checklist"]
> * Habilitar a consulta e aguardar a coleta de estatísticas
> * Acessar e utilizar os dados coletados
> * Exibir desempenho de consulta e estatísticas de espera ao longo do tempo
> * Analisar um banco de dados para obter as recomendações de desempenho
> * Aplicar recomendações do desempenho

## <a name="prerequisites"></a>Pré-requisitos
Você precisa de um Banco de Dados do Azure para PostgreSQL versão 9.6 ou 10. Você pode seguir as etapas no [Tutorial de criação ](tutorial-design-database-using-azure-portal.md) para criar um servidor.

> [!IMPORTANT]
> **Repositório de Consultas**, **Análise de Desempenho de Consultas**, e **Recomendação de Desempenho** estão em Visualização Pública.

## <a name="enabling-data-collection"></a>Habilitar coleta de dados
O [Repositório de Consultas](concepts-query-store.md) captura um histórico das consultas e estatísticas de espera em seu servidor e os armazena no banco de dados **azure_sys** em seu servidor. É um recurso opcional. Para habilitá-lo:

1. Abra o portal do Azure.

2. Selecione seu servidor de Banco de Dados do Azure para PostgreSQL.

3. Selecionar **Servidor de parâmetros** que estiver nas seções de **Configurações** do menu à esquerda.

4. Definir **pg_qs.query_capture_mode** a **superior** para começar a coletar dados de desempenho de consulta. Definir **pgms_wait_sampling.query_capture_mode** à **todos os** para iniciar a coltar as estatísticas de espera. Salve.
   
   :::image type="content" source="./media/tutorial-performance-intelligence/query-store-parameters.png" alt-text="Parâmetros de consulta do servidor Repositório de Consultas":::

5. Permita que o primeiro lote de dados persista no banco de dados **azure_sys** por até 20 minutos.


## <a name="performance-insights"></a>Insights de desempenho
A visualização da [Análise de Desempenho de Consultas](concepts-query-performance-insight.md) no portal do Azure será superficial visualizações em informações do Repositório de Consultas. 

1. Na página do portal do Banco de Dados do Azure para PostgreSQL, selecione **Insight do Desempenho de Consulta** em **suporte + seção solução de problemas** do menu à esquerda.

2. A guia **consultas de Longa execução** mostra as 5 consultas superiores por duração média por execução, agregadas em intervalos de 15 minutos. 
   
   :::image type="content" source="./media/tutorial-performance-intelligence/query-performance-insight-landing-page.png" alt-text="Página de aterrissagem de Análise de Desempenho de Consultas":::

   Você pode exibir mais consultas, selecionando a partir do **Número de consultas** lista suspensa. As cores do gráfico pode ser alteradas para uma ID de consulta específica ao fazer isso.

3. Você pode clicar e arrastar no gráfico para restringi-lo a uma janela de tempo específico.

4. Use os ícones de ampliar e afastar para exibir um período maior ou menor, respectivamente.

5. Exiba a tabela abaixo do gráfico para obter mais detalhes sobre as consultas de longa execução na janela de tempo.

6. Selecione a guia das **Estatísticas de Espera** guia para exibir as visualizações correspondentes em espera no servidor.
   
   :::image type="content" source="./media/tutorial-performance-intelligence/query-performance-insight-wait-statistics.png" alt-text="Estatísticas de espera de Análise de Desempenho de Consultas":::

### <a name="permissions"></a>Permissões
**Permissões do Proprietário** ou **Colaborador** necessárias para exibir o texto das consultas na Análise de Desempenho de Consultas **Leitor** podem exibir gráficos e tabelas, mas não o texto da consulta.


## <a name="performance-recommendations"></a>Recomendações do desempenho
O recurso das [Recomendações de Desempenho](concepts-performance-recommendations.md) recurso analisa as cargas de trabalho entre seu servidor para identificar os índices com o potencial de melhorar o desempenho.

1. Abra **Recomendações de Desempenho** da seção **suporte + solução de problemas** da barra de menus, na página do portal do Azure para seu servidor PostgreSQL.
   
   :::image type="content" source="./media/tutorial-performance-intelligence/performance-recommendations-landing-page.png" alt-text="Página das Recomendações de Desempenho":::

2. Selecione **Analisar** e escolha um banco de dados. Isso iniciará a análise.

3. Dependendo de sua carga de trabalho, isso pode levar vários minutos para ser concluído. Quando a análise for concluída, haverá uma notificação no portal.

4. A janela **Recomendações de Desempenho** Mostrará uma lista de recomendações, caso seja encontrada. 

5. Uma recomendação mostrará informações sobre os relevantes **banco de dados**, **tabela**, **coluna**, e **tamanho de índice**.

   :::image type="content" source="./media/tutorial-performance-intelligence/performance-recommendations-result.png" alt-text="Recomendações de Desempenho":::

6. Para implementar a recomendação, copie o texto da consulta e executá-lo do seu cliente de escolha.

### <a name="permissions"></a>Permissões
**Permissões de Proprietário** ou **Colaborador** necessárias para executar a análise usando o recurso de recomendações de desempenho.

## <a name="clean-up-resources"></a>Limpar os recursos

Nas etapas anteriores, você criou recursos do Azure em um grupo de servidores. Caso esses recursos não sejam mais necessários no futuro, exclua o grupo de servidores. Pressione o botão *Excluir* na página *Visão geral* do grupo de servidores. Quando solicitado em uma página pop-up, confirme o nome do grupo de servidores e clique no botão *Excluir* final.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> Saiba mais sobre [monitoramento e ajuste](concepts-monitoring.md) no Banco de Dados do Azure para PostgreSQL.