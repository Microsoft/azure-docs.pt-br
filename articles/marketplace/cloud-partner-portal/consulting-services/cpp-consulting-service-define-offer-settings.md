---
title: Oferta de serviço de consultoria do Azure e do Dynamics 365 – definir configurações da oferta | Microsoft Docs
description: Guia para definir as configurações da oferta em uma oferta de serviço de consultoria do Dynamics 365 ou do Azure no Portal do Cloud Partner.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: qianw211
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: pbutlerm
ms.openlocfilehash: 590aa440f35f97e854fa827b0d8db1c3f9211faf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60749626"
---
# <a name="offer-settings-tab"></a>Guia Configurações da Oferta

Na tela **Nova Oferta**, a primeira etapa é criar a identidade da oferta. A identidade da oferta consiste em três partes: **ID da oferta**, **ID do Editor** e **Nome**. Cada um desses blocos é abordado nas seções a seguir.

![Criar uma nova oferta de serviço de consultoria – guia Configurações da oferta](media/consultingoffer-settings-tab.png)

### <a name="offer-id"></a>ID da oferta

Esse identificador é um nome exclusivo que você cria quando envia a oferta pela primeira vez. Deve conter apenas caracteres alfanuméricos minúsculos, traços ou sublinhados. A **ID da oferta** será visível na URL e afeta os resultados do mecanismo de pesquisa. Um exemplo é *yourcompanyname_exampleservice*.

Conforme mostrado no exemplo, a **ID da oferta** é anexada à ID do editor para criar um identificador exclusivo. O identificador exclusivo é exposto como um link permanente que pode ser registrado e é indexado pelos mecanismos de pesquisa.

>[!Note]
>Depois que uma oferta estiver ativa, o identificador não poderá ser atualizado.

### <a name="publisher-id"></a>ID do fornecedor

Esse identificador está relacionado à sua conta. Depois de se conectar com sua conta organizacional, a **ID do editor** será exibida no menu suspenso.

### <a name="name"></a>NOME

Essa cadeia de caracteres será exibida como nome da oferta no AppSource ou no Azure Marketplace. O campo **Nome** é limitado a 50 caracteres. O revisor talvez precise editar o título para acrescentar a duração e o tipo de oferta ao nome da oferta.

O exemplo a seguir mostra como o nome da oferta é montado. 

![Criar uma nova oferta de serviços de consultoria](media/cppsampleconsultingoffer.png)

O nome da oferta é composto de quatro partes:

-   **Duração:** definida na guia **Detalhes da vitrine** do editor. A duração pode ser expressa em horas, dias ou semanas.
-   **Tipo de serviço:** definida na guia **Detalhes da vitrine** do editor. Os tipos de serviço são `Assessment`, `Briefing`, `Implementation`, `Proof of concept` e `Workshop`.
-   **Preposição:** inserida pelo revisor.
-   **Nome:** definida na página **Configurações da oferta**.

>[!Note]
>O campo **Nome** é limitado a 50 caracteres. O revisor talvez precise editar o título para acrescentar a duração e o tipo de oferta ao nome da oferta.

A lista a seguir fornece vários nomes de oferta adequados:

-   Conceitos básicos para Serviços profissionais: resumo de uma hora
-   Plataforma de Migração na Nuvem: resumo de uma hora
-   PowerApps e Microsoft Flow: workshop de um dia
-   Serviços do Azure Machine Learning: prova de conceito de três semanas
-   Solução de varejo Brick and Click: resumo de uma hora
-   Traga seus próprios dados: workshop de uma semana
-   Análise de nuvem: workshop de três dias
-   Treinamento em Power BI: workshop de três dias
-   Solução de gerenciamento de vendas: implementação de uma semana
-   Início Rápido para CRM: workshop de um dia
-   Dynamics 365 for Sales: avaliação de dois dias

Depois de preencher a guia **Configurações da oferta**, salve seu envio. Agora, o nome da oferta é exibido acima do editor e você pode encontrá-lo em **Todas as ofertas**.

## <a name="next-steps"></a>Próximas etapas

Agora, você pode inserir [detalhes da vitrine e determinar se deseja publicar no Azure Marketplace ou no AppSource](./cpp-consulting-service-storefront-details.md).
