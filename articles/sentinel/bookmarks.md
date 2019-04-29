---
title: Manter o controle de dados durante a procura de sentinela versão prévia do Azure usando os indicadores de procura | Microsoft Docs
description: Este artigo descreve como usar os indicadores de procura Sentinel do Azure para manter o controle de dados.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 320ccdad-8767-41f3-b083-0bc48f1eeb37
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: 9a7ceeab6d2ad761752f778038692256bd87624b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60596949"
---
# <a name="keep-track-of-data-during-hunting"></a>Manter o controle de dados durante a procura

> [!IMPORTANT]
> No momento, o Azure Sentinel está em versão prévia pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
 
Procura de ameaças normalmente requer a revisão Montanhas de dados de log procurando evidências de comportamentos mal-intencionados. Durante esse processo, investigadores encontrar eventos que eles desejam se lembrar, revisitar e analisar como parte da validação de hipóteses possíveis e Noções básicas sobre a história completa de um comprometimento.
Indicadores de procura ajudarão-lo a fazer isso, preservando as consultas executadas no Log Analytics, juntamente com os resultados da consulta que você julgar relevantes. Também pode registrar suas observações contextuais e fazer referência a suas descobertas com a adição de notas e marcas. Dados indicadores são visíveis para você e seus colegas de equipe para fácil colaboração.   

Você pode rever seus dados com indicadores a qualquer momento na **indicador** guia da **procura** página. Você pode usar a filtragem e opções para localizar rapidamente dados específicos para a sua investigação atual de pesquisa. Como alternativa, você pode exibir os dados com indicador diretamente na **HuntingBookmark** tabela no Log Analytics. Isso permite que você filtrar, resumir e unir dados marcados com outras fontes de dados, tornando mais fácil procurar corroborating evidência.

Você também pode visualizar os dados com indicador, clicando em **investigar**. Isso inicia a experiência de investigação no qual você pode exibir, investigar e comunicar visualmente os resultados usando um diagrama gráfico interativo de entidade e a linha do tempo.


## <a name="run-a-log-analytics-query-from-azure-sentinel"></a>Executar uma consulta do Log Analytics do Azure Sentinel

1. No portal do Azure Sentinel, clique em **procura** para executar consultas para o comportamento suspeito e anormal.

1. Para executar uma campanha de procura, selecione uma das consultas de procura e sobre a revisão à esquerda, os resultados. 

1. Clique em **exibir resultados da consulta** na consulta procura **detalhes** página para exibir a consulta resulta no Log Analytics. Aqui está um exemplo do que você vê se você executou uma consulta personalizada de ataque de bruteforce SSH.
  
   ![Mostrar resultados](./media/bookmarks/ssh-bruteforce-example.png)

## <a name="add-a-bookmark"></a>Adicionar um indicador

1. Na lista de resultados de consulta do Log Analytics, expanda a linha que contém as informações que você achar interessantes.

4. Selecione as reticências (...) no final da linha e, em seguida, selecione **adicionar indicadores procura**.
5. À direita, na **detalhes** página, atualize o nome e adicionar marcas e anotações para ajudá-lo a identificar o que era interessante sobre o item.
6. Clique em **salvar** para confirmar as alterações. Todos os dados com indicador é compartilhado com outros investigadores e é uma primeira etapa para uma experiência de investigação de colaboração.

   ![Mostrar resultados](./media/bookmarks/add-bookmark-la.png)

 
> [!NOTE]
> Você também pode usar indicadores com consultas do Log Analytics arbitrárias, iniciadas a partir da página de Logs de análise de Log Sentinel do Azure ou consultas criadas em tempo real da página do Log Analytics e aberto na página de procura de. Você não poderá adicionar um indicador de se iniciar o Log Analytics de fora do Azure Sentinel. 

## <a name="view-and-update-bookmarks"></a>Exibir e atualizar indicadores 

1. No portal do Azure Sentinel, clique em **procura**. 
2. Clique o **indicadores** guia no meio da página para exibir a lista de indicadores.
3. Use as opções de filtro ou de caixa de pesquisa para localizar um indicador específico.
4. Selecione indicadores individuais na grade abaixo para exibir os detalhes de indicador no painel de detalhes à direita.
5. Para atualizar as marcas e anotações, clique nas caixas de texto editável e clique em **salvar** para preservar suas alterações.

   ![Mostrar resultados](./media/bookmarks/view-update-bookmarks.png)

## <a name="view-bookmarked-data-in-log-analytics"></a>Um indicador de exibir dados no Log Analytics 

Há várias opções para exibir seus dados indicadores no Log Analytics. 

A maneira mais fácil para exibir consultas com indicador, resultados ou histórico está selecionando o indicador desejado na **indicadores** de tabela e use os links fornecidos no painel de detalhes. As opções incluem: 
- Clique em **consulta de exibição** para exibir a consulta de origem no Log Analytics.  
- Clique em **exibir o histórico de indicador** para ver todos os indicadores de metadados, incluindo: quem fez a atualização, os valores atualizados e a hora em que ocorreu a atualização. 

- Você também pode exibir os dados brutos de indicador para todos os indicadores clicando em **logs de indicador** acima da grade de indicador. Essa exibição mostrará todos os seus indicadores na tabela de indicador de procura com metadados associados. Você pode usar consultas KQL para filtrar até a versão mais recente do indicador específico que você está procurando.  


> [!NOTE]
> Pode haver um atraso significativo (medido em minutos) entre a criação de um indicador e quando ele é exibido na **HuntingBookmark** tabela. É recomendável criar seus indicadores pela primeira vez, em seguida, analisá-los depois que os dados são ingeridos. 

## <a name="delete-a-bookmark"></a>Excluir um indicador
Se você quiser excluir um indicador de fazer o seguinte: 
1.  Abra th **indicador procura** guia. 
2.  Selecione o indicador de destino.
3.  Selecione as reticências (...) no final da linha e selecione **indicador Delete**.
    
Excluir o indicador remove o indicador na lista de **indicador** guia.  O Log Analytics "HuntingBookmark" tabela continuará conter as entradas anteriores do indicador, mas a entrada mais recente será alterado de **SoftDelete** valor como true, tornando mais fácil de filtrar os indicadores antigos.  Excluir um indicador não remove quaisquer entidades com a experiência de investigação que estão associados a outros indicadores ou alertas. 


## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a executar uma investigação de procura usando indicadores no Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:


- [Proativamente de busca de ameaças](hunting.md)
- [Usar notebooks para executar campanhas de procura automatizado](notebooks.md)