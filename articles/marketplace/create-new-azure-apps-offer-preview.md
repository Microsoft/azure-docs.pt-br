---
title: Como adicionar um público de visualização para sua oferta de Aplicativo Azure
description: Saiba como adicionar um público de visualização para sua oferta de aplicativo do Azure no Partner Center.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 11/06/2020
ms.openlocfilehash: 1d41e9dc39b50b92351fe38a86587a67811c5eec
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94369936"
---
# <a name="how-to-add-a-preview-audience-for-your-azure-application-offer"></a>Como adicionar um público de visualização para sua oferta de Aplicativo Azure

Este artigo descreve como configurar um público de visualização para uma oferta de Aplicativo Azure no Marketplace comercial. Você precisa definir um público de visualização que possa revisar sua listagem de oferta antes que ela fique ativa.

## <a name="define-a-preview-audience"></a>Definir um público de visualização

Na página do **público de visualização** , você pode definir um público limitado que pode examinar sua oferta de aplicativo Azure antes de publicá-lo em tempo real para o público do mercado mais amplo. Você define o público de visualização usando as IDs de assinatura do Azure, juntamente com uma descrição opcional para cada uma. Nenhum desses campos pode ser visto pelos clientes. É possível encontrar a ID da assinatura na página **Assinaturas** no portal do Azure.

Adicione um mínimo de uma e até 10 IDs de assinatura do Azure, seja individualmente (até 10) ou carregando um arquivo CSV (até 100) para definir quem pode visualizar sua oferta antes de ser publicada ao vivo. Se sua oferta já estiver ativa, você ainda poderá definir um público-alvo de versão prévia para as alterações de oferta de teste ou atualizações para sua oferta.

> [!NOTE]
> Um público-alvo de versão prévia difere de um público-alvo privado. Um público de visualização tem permissão de acesso à sua oferta antes de ser publicado em tempo real nas lojas online. O público-alvo de versão prévia pode ver e validar todos os planos, incluindo aqueles que estarão disponíveis somente para um público-alvo privado depois que sua oferta for publicada integralmente no marketplace. Você pode tornar um plano disponível somente para um público privado. Um público privado (definido na guia **disponibilidade** de um plano) tem acesso exclusivo a um plano específico.

### <a name="add-email-addresses-manually"></a>Adicionar endereços de email manualmente

1. Na página do **público de visualização** , adicione uma única ID de assinatura do Azure e uma descrição opcional nas caixas fornecidas.
1. Para adicionar outro endereço de email, selecione o link **Adicionar ID (máx. 10)** .
1. Selecione **salvar rascunho** antes de continuar para a próxima guia: configuração técnica.
1. Vá para [as próximas etapas](#next-steps).

### <a name="add-email-addresses-using-the-csv-file"></a>Adicionar endereços de email usando o arquivo CSV

1. Na página **público de visualização** , selecione o link **Exportar audiência (CSV)** .
1. Abra o. Arquivo CSV em um aplicativo, como o Microsoft Excel.
1. No. Arquivo CSV, na coluna **ID** , insira as IDs de assinatura do Azure que você deseja adicionar ao público de visualização.
1. Na coluna **Descrição** , você pode opcionalmente adicionar uma descrição para cada endereço de email.
1. Na coluna **tipo** , adicione **SubscriptionId** a cada linha que tenha um endereço de email.
1. Salve o arquivo como um. Arquivo CSV.
1. Na página **público de visualização** , selecione o link **importar audiência (CSV)** .
1. Na caixa de diálogo **confirmar** , selecione **Sim**.
1. Selecione o. Arquivo CSV e, em seguida, selecione **abrir**.
1. Selecione **salvar rascunho** antes de continuar para a próxima guia: configuração técnica.

## <a name="next-steps"></a>Próximas etapas

- [Como adicionar detalhes técnicos para sua oferta de Aplicativo Azure](create-new-azure-apps-offer-technical.md)
