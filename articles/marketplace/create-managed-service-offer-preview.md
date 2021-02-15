---
title: Como adicionar um público de visualização para sua oferta de serviço gerenciado
description: Saiba como adicionar um público de visualização para sua oferta de serviço gerenciado no Microsoft Partner Center.
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.reviewer: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 12/23/2020
ms.openlocfilehash: 072f1d63e0b3df898ef170205b5c560432d870b0
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97918076"
---
# <a name="how-to-add-a-preview-audience-for-your-managed-service-offer"></a>Como adicionar um público de visualização para sua oferta de serviço gerenciado

Este artigo descreve como configurar um público de visualização para uma oferta de serviço gerenciado no Marketplace comercial usando o Partner Center. O público-alvo da visualização pode revisar sua oferta antes de entrar em tempo real.

## <a name="define-a-preview-audience"></a>Definir um público de visualização

Na página do **público de visualização** , você pode definir um público limitado que pode revisar sua oferta de serviço gerenciado antes de publicá-lo em tempo real para o público do Marketplace mais amplo. Você define o público de visualização usando as IDs de assinatura do Azure, juntamente com uma descrição opcional para cada uma. Nenhum desses campos pode ser visto pelos clientes. Você pode encontrar sua ID de assinatura do Azure na página **assinaturas** no portal do Azure.

Adicione pelo menos uma ID de assinatura do Azure, seja individualmente (até 10) ou carregando um arquivo CSV (até 100) para definir quem pode visualizar a sua oferta antes de ser publicada ao vivo. Se sua oferta já estiver ativa, você ainda poderá definir um público de visualização para testar atualizações em sua oferta.

> [!NOTE]
> Um público de *Visualização* difere de um público *privado* . Um público de visualização tem permissão de acesso à sua oferta antes de ser publicado em tempo real nas lojas online. Eles podem ver e validar todos os planos, incluindo aqueles que estarão disponíveis somente para um público privado depois que sua oferta for totalmente publicada no Marketplace. Você pode tornar um plano disponível somente para um público privado. Um público privado (definido na guia disponibilidade de um plano) tem acesso exclusivo a um plano específico.

## <a name="add-email-addresses-manually"></a>Adicionar endereços de email manualmente

1. Na página do **público de visualização** , adicione uma única ID de assinatura do Azure e uma descrição opcional nas caixas fornecidas.
2. Para adicionar outro endereço de email, selecione o link **Adicionar ID (máx. 10)** .
3. Selecione **salvar rascunho** antes de continuar para a próxima guia.

## <a name="add-email-addresses-using-a-csv-file"></a>Adicionar endereços de email usando um arquivo CSV

1. Na página Visualizar audiência da **Visualização** do público-alvo, selecione o link **Exportar audiência (CSV)** .
2. Abra o arquivo CSV. Na coluna **ID** , insira as IDs de assinatura do Azure que você deseja adicionar ao público de visualização.
3. Na coluna **Descrição** , você tem a opção de adicionar uma descrição para cada entrada.
4. Na coluna **tipo** , adicione **SubscriptionId** a cada linha que tenha uma ID.
5. Salve o arquivo como um arquivo CSV.
6. Na página **público de visualização** , selecione o link **importar audiência (CSV)** .
7. Na caixa de diálogo **confirmar** , selecione **Sim** e carregue o arquivo CSV.
8. Selecione **salvar rascunho** antes de continuar para a próxima guia.

## <a name="next-steps"></a>Próximas etapas

* [Criar planos](create-managed-service-offer-plans.md)
