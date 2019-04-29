---
title: Investigar os incidentes e alertas na Central de Segurança do Azure | Microsoft Docs
description: Este documento ajuda você a usar o recurso de investigação na Central de Segurança do Azure para investigar alertas e incidentes de segurança.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: a8e894a9-8781-4749-ae8f-8c8e01658566
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/29/2018
ms.author: rkarlin
ms.openlocfilehash: 6ba21c6eacd5b72d13706c08f0cc9883ccc91388
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60704689"
---
# <a name="investigate-incidents-and-alerts-in-azure-security-center-preview"></a>Investigar os incidentes e alertas na Central de Segurança do Azure (Versão prévia)
Este documento ajuda você a usar o recurso de investigação na Central de Segurança do Azure para investigar alertas e incidentes de segurança.

## <a name="what-is-investigation-in-security-center"></a>O que é a investigação na Central de Segurança?
O recurso de investigação na Central de Segurança permite que você faça triagem, compreenda o escopo e rastreie a causa raiz de um possível [incidente de segurança](https://docs.microsoft.com/azure/security-center/security-center-incident).

A intenção é facilitar o processo de investigação vinculando todas as entidades ([alertas de segurança](https://docs.microsoft.com/azure/security-center/security-center-alerts-type), usuários, computadores e incidentes) envolvidas com o incidente que você está investigando.  A Central de Segurança pode fazer isso correlacionando os dados relevantes com as entidades envolvidas e expondo essa correlação usando um grafo dinâmico que ajuda você a navegar pelos objetos e a visualizar informações relevantes.


> [!NOTE]
> * [Alertas personalizados](security-center-custom-alert.md) não têm suporte no recurso de investigação da Central de segurança.
> * Somente há suporte para a investigação para alertas com base nos dados coletados dos servidores do Windows.


## <a name="how-investigation-works"></a>Como a investigação funciona?
A investigação é composta por um grafo que ocupa a área central do painel de investigação. O grafo sempre se concentra em uma entidade específica e apresenta as entidades que estão relacionadas a ela. Uma entidade pode ser um alerta de segurança, um usuário, um computador ou um incidente.

![Mapear](./media/security-center-investigation/security-center-investigation-fig1.png)

O usuário pode navegar de uma entidade para outra clicando no grafo. O grafo centraliza na entidade selecionada e nas entidades relacionadas automaticamente. As entidades que não são mais relevantes podem ser removidas do grafo.

### <a name="investigation-path"></a>Caminho de investigação
Enquanto o usuário está navegando para entidades diferentes, o caminho de investigação ajuda a manter o controle do contexto de investigação e permite uma navegação rápida. O incidente que contém os resultados da investigação fica sempre na ponta esquerda do caminho de investigação.

![Caminho](./media/security-center-investigation/security-center-investigation-fig2.png)

### <a name="general-information"></a>Informações gerais
Quando uma entidade é apresentada no grafo, as guias mostram outras informações sobre essa entidade. A guia **Informações** apresenta informações gerais sobre a entidade de várias fontes de informação disponíveis.

![Informações gerais](./media/security-center-investigation/security-center-investigation-fig3.png)

A guia de informações mostra informações relevantes para o incidente selecionado no mapa. Incidente é um contêiner que inclui os resultados de uma investigação. Cada investigação ocorre no contexto de um incidente.

Um incidente é criado somente quando um usuário clica no botão **Iniciar investigação** em determinado alerta. A funcionalidade básica disponível para o investigador é a marcação de entidades como usuário, computador ou alerta. Quando uma entidade é marcada como relacionada, um motivo é fornecido. Desse ponto em diante, a entidade é exibida diretamente no incidente no grafo e na lista de entidades do incidente.

### <a name="entities"></a>Entidades

A guia **Entidades** mostra todas as entidades relacionadas agrupadas por tipo. Isso é útil em dois casos: quando há muitas entidades para apresentar no grafo e os nomes das entidades são muito longos, e é mais fácil examiná-los de forma tabular.

![Entidades](./media/security-center-investigation/security-center-investigation-fig4.png)

### <a name="search"></a>Search

A guia **Pesquisa** apresenta todos os tipos de log que estão disponíveis para a entidade. Para cada tipo de log, você pode ver quantos registros estão disponíveis. Clicar em cada tipo de log leva você para a tela de pesquisa. Na tela de pesquisa, você pode refinar a pesquisa e usar os diversos recursos de pesquisa, como definição de alertas. Na versão atual, a guia de pesquisa está disponível somente para entidades usuários e computadores.

![Search](./media/security-center-investigation/security-center-investigation-fig5.png)

### <a name="exploration"></a>Exploração

A guia **Exploração** permite que o investigador examine os dados relacionados a vários problemas relacionados à entidade. Por exemplo, quando uma máquina é investigada, a lista de processos executados nela é apresentada na guia de exploração. Em alguns casos, a guia de exploração apresenta dados que podem indicar um problema suspeito. O investigador pode examinar os dados na guia ou abri-los na tela de pesquisa para examinar grandes conjuntos de dados e usar opções de pesquisa avançada, como filtragem e exportação para o Excel.

![Exploração](./media/security-center-investigation/security-center-investigation-fig6.png)

### <a name="timeline"></a>Linha do tempo

A maioria dos dados apresentados no grafo e nas diversas guias é relevante para um período de tempo específico. Esse escopo de hora é definido usando o seletor de escopo de tempo no canto superior esquerdo do grafo. O investigador tem vários métodos para selecionar o escopo de tempo.

![Linha do tempo](./media/security-center-investigation/security-center-investigation-fig7.png)

Os itens abaixo são importantes para o escopo de tempo:

- Relação computador-usuário no grafo, somente os usuários que fizeram logon no computador nesse escopo de tempo são apresentados.
- Quais alertas são apresentadas ao examinar usuários e computadores: somente os alertas que ocorrem dentro do escopo de tempo são apresentados.
- A guia de entidades segue a mesma lógica do grafo.

Os seguintes itens serão apresentados independentemente do escopo de tempo selecionado:

- Quando um alerta é apresentado, todas as entidades contidas nele, como usuários e computadores, são sempre apresentadas.
- Se um incidente contiver uma entidade, ela será exibida.

> [!NOTE]
> A guia **Pesquisa** e **Exploração** mostra somente os registros nesse escopo de tempo.

## <a name="how-to-perform-an-investigation"></a>Como executar uma investigação?

Você pode iniciar a investigação de um incidente de segurança ou de um alerta; a opção que você escolher varia de acordo com suas necessidades. As etapas a seguir são usadas para iniciar uma investigação de um alerta:

1.  Abra o painel **Central de Segurança**.
2.  Clique em **Alertas de Segurança**e selecione o incidente que você deseja investigar.
3.  Na página do incidente, clique no botão **Iniciar investigação** para exibir o painel **Investigação**.

    ![Alerta](./media/security-center-investigation/security-center-investigation-fig8.png)

4. Nesse painel, você pode selecionar a entidade no mapa, e as informações relevantes sobre essa entidade são exibidas no lado direito da tela.

    ![Painel de investigação](./media/security-center-investigation/security-center-investigation-fig9.png)

Daqui, você pode explorar as entidades que estão associadas ao incidente e explorar mais detalhes sobre cada uma delas.

## <a name="see-also"></a>Consulte também
Neste documento, você aprendeu a usar o recurso de investigação na Central de Segurança. Para saber mais sobre a Central de Segurança, confira o seguinte:

* [Gerenciando e respondendo aos alertas de segurança na Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Saiba como gerenciar alertas e responder a incidentes de segurança na Central de Segurança.
* [Monitoramento da integridade de segurança na Central de Segurança do Azure](security-center-monitoring.md). Saiba como monitorar a integridade dos recursos do Azure.
* [Noções básicas de alertas de segurança na Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Saiba mais sobre os diferentes tipos de alertas de segurança.
* [Guia de solução de problemas da Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Saiba como solucionar problemas comuns na Central de Segurança.
* [Perguntas Frequentes sobre a Central de Segurança do Azure](security-center-faq.md). Encontre as perguntas frequentes sobre como usar o serviço.
* [Blog de Segurança do Azure](https://blogs.msdn.com/b/azuresecurity/). Encontre postagens no blog sobre a conformidade e segurança do Azure.
