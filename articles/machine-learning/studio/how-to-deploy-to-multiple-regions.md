---
title: "Como implantar um serviço Web em várias regiões | Microsoft Docs"
description: "Etapas para implantar (copiar) um novo serviço Web para outras regiões."
services: machine-learning
documentationcenter: 
author: vDonGlover
manager: raymondl
editor: cgronlun
ms.assetid: 36c60411-f2db-4ee2-9b66-b1f1d77a8f44
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.author: v-donglo
ms.openlocfilehash: 8e616bd6f13c4f97886a3eae225e188afadb2305
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-deploy-a-web-service-to-multiple-regions"></a>Como implantar um serviço Web em várias regiões
Os novos serviços Web do Azure permitem que você facilmente implante um serviço Web em várias regiões, sem a necessidade de várias assinaturas ou espaços de trabalho. 

O preço é específico de cada região; portanto, você deverá definir um plano de faturamento para cada região na qual implantará o serviço Web.

## <a name="to-create-a-plan-in-another-region"></a>Para criar um plano em outra região
1. Entre nos [serviços Web de Microsoft Azure Machine Learning](https://services.azureml.net/).
2. Clique na opção de menu **Planos** .
3. Na página de visão geral de Planos, clique em **Novo**.
4. No menu suspenso **Assinatura** , selecione a assinatura na qual o novo plano residirá.
5. No menu suspenso **Região** , selecione uma região para o novo plano. As opções de plano para a região selecionada serão exibidas na seção **Opções de Plano** da página.
6. No menu suspenso **Grupo de Recursos** , selecione um grupo de recursos para o plano. Em mais informações sobre grupos de recursos, confira [Visão geral do Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md).
7. Em **Nome do Plano** , digite o nome do plano.
8. Em **Opções do Plano**, clique no nível de cobrança para o novo plano.
9. Clique em **Criar**.

## <a name="deploying-the-web-service-to-another-region"></a>Implantando o serviço Web em outra região
1. Clique na opção de menu **Serviços Web** .
2. Selecione o serviço Web que você está implantando em uma nova região.
3. Clique em **Copiar**.
4. Em **Nome do Serviço Web**, digite um novo nome para o serviço Web.
5. Em **Descrição do serviço Web**, digite uma descrição para o serviço Web.
6. No menu suspenso **Assinatura** , selecione a assinatura na qual o novo serviço Web residirá.
7. No menu suspenso **Grupo de Recursos** , selecione um grupo de recursos para o serviço Web. Em mais informações sobre grupos de recursos, confira [Visão geral do Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md).
8. No menu suspenso **Região** , selecione a região na qual implantar o serviço Web.
9. No menu suspenso **Conta de armazenamento** , selecione uma conta de armazenamento na qual armazenar o serviço Web.
10. Na lista suspensa **Plano de preços** , selecione um plano na região que você selecionou na etapa 8.
11. Clique em **Copiar**.

