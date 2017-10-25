---
title: "Introdução ao Azure Advisor | Microsoft Docs"
description: "Use o Azure Advisor para otimizar as implantações do Azure."
services: advisor
documentationcenter: NA
author: KumudD
manager: carmonm
editor: 
ms.assetid: 
ms.service: advisor
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/16/2016
ms.author: kumud
ms.openlocfilehash: 96925f251cf4984a11516a962740e19a7b9589dc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-azure-advisor"></a>Introdução ao Azure Advisor

Saiba mais sobre o Assistente do Azure e suas principais funcionalidades e obtenha respostas para perguntas frequentes.

## <a name="what-is-advisor"></a>O que é o Assistente?
O Assistente é um consultor de nuvem personalizado que ajuda você a seguir as melhores práticas para otimizar suas implantações do Azure. Ele analisa a telemetria de uso e configuração do recurso e, depois, recomenda soluções que podem ajudar você a melhorar a economia, o desempenho, a alta disponibilidade e a segurança de seus recursos do Azure.

Com o Assistente, é possível:
* Obter recomendações de melhores práticas proativas, personalizadas e prontas para uso. 
* Melhorar o desempenho, a segurança e a alta disponibilidade de seus recursos, enquanto você identifica oportunidades para reduzir o gasto geral do Azure.
* Obter recomendações com ações propostas embutidas.

Você pode acessar o Advisor pelo [Portal do Azure](https://aka.ms/azureadvisordashboard). Entre no [portal](https://portal.azure.com), selecione **Procurar** e, em seguida, role até **Assistente do Azure**. O painel do Advisor exibe recomendações personalizadas para uma assinatura selecionada. 

As recomendações são divididas em quatro categorias: 

* **Alta Disponibilidade**: para garantir e melhorar a continuidade dos aplicativos críticos para os negócios. Para saber mais, veja [Advisor High Availability recommendations](advisor-high-availability-recommendations.md) (Recomendações de alta disponibilidade do Advisor).

* **Segurança**: para detectar ameaças e vulnerabilidades que podem levar a possíveis violações de segurança. Para saber mais, veja [Advisor Security recommendations](advisor-security-recommendations.md) (Recomendações de segurança do Advisor).

* **Desempenho**: para melhorar a velocidade dos aplicativos. Para saber mais, veja [Advisor Performance recommendations](advisor-performance-recommendations.md) (Recomendações de desempenho do Advisor).

* **Custo**: para otimizar e reduzir o gasto geral do Azure. Para saber mais, veja [Advisor Cost recommendations](advisor-cost-recommendations.md) (Recomendações de custo do Advisor).

  ![Tipos de recomendação do Advisor](./media/advisor-overview/advisor-all-tab-examples.png)

> [!NOTE]
> Para acessar as recomendações do Assistente, você deve primeiro *registrar sua assinatura* no Assistente. Uma assinatura é registrada quando um *Proprietário de assinatura* inicia o painel do Assistente e clica no botão **Obter recomendações**. Essa é uma *operação única*. Depois que a assinatura for registrada, você poderá acessar as recomendações do Assistente como *Proprietário*, *Colaborador* ou *Leitor* de uma assinatura, um grupo de recursos ou um recurso específico.

Clique em uma recomendação para saber mais sobre ela. Saiba mais sobre as ações que você pode realizar para aproveitar uma oportunidade ou resolver um problema. 

O Advisor oferece recomendações com ações embutidas ou links de documentação. Ao clicar em uma ação embutida, você é levado para uma "jornada interativa de usuário" para implementá-la. Clicar em um link de documentação aponta para a documentação que descreve como implementar a ação manualmente. 

O Assistente atualiza as recomendações a cada hora. Se você não pretender tomar uma ação imediata de acordo com uma recomendação, será possível adiá-la por um período especificado ou descartá-la. 

## <a name="frequently-asked-questions"></a>Perguntas frequentes

### <a name="how-do-i-access-advisor"></a>Como acesso o Advisor?
Você pode acessar o Advisor pelo [Portal do Azure](https://aka.ms/azureadvisordashboard). Entre no [portal](https://portal.azure.com), selecione **Procurar** e, em seguida, role até **Assistente do Azure**. O painel do Advisor exibe recomendações personalizadas para uma assinatura selecionada. 

Você também pode exibir as recomendações do Advisor por meio da folha de recursos de máquina virtual. Escolha uma máquina virtual e role até as recomendações do Advisor no menu. 

### <a name="what-permissions-do-i-need-to-access-advisor"></a>De quais permissões preciso para acessar o Advisor?

Para acessar as recomendações do Assistente, você deve primeiro *registrar sua assinatura* no Assistente. Uma assinatura é registrada quando um *Proprietário de assinatura* inicia o painel do Assistente e clica no botão **Obter recomendações**. Essa é uma *operação única*. Depois que a assinatura for registrada, você poderá acessar as recomendações do Assistente como *Proprietário*, *Colaborador* ou *Leitor* de uma assinatura, um grupo de recursos ou um recurso específico.

### <a name="how-often-are-advisor-recommendations-updated"></a>Com que frequência as recomendações do Advisor são atualizadas?

As recomendações do Assistente são atualizadas a cada hora.

### <a name="what-resources-does-advisor-provide-recommendations-for"></a>Para quais recursos o Advisor fornece recomendações?

O Assistente fornece recomendações para máquinas virtuais, conjuntos de disponibilidade, gateways de aplicativo, Serviços de Aplicativos, SQL Servers, bancos de dados SQL e para o Cache Redis.

### <a name="can-i-snooze-or-dismiss-a-recommendation"></a>Posso adiar ou descartar uma recomendação?

Para adiar ou descartar uma recomendação, clique no botão ou link **Adiar**. Você pode especificar uma hora de adiamento ou selecionar **Nunca** para descartar a recomendação.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre as recomendações do Assistente, consulte:

* [Introdução ao Advisor](advisor-get-started.md)
* [Recomendações de alta disponibilidade do Advisor](advisor-high-availability-recommendations.md)
* [Recomendações de segurança do Advisor](advisor-security-recommendations.md)
* [Recomendações de desempenho do Advisor](advisor-performance-recommendations.md)
* [Recomendações de custo do Advisor](advisor-cost-recommendations.md)
