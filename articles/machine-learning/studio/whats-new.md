---
title: Novidades no Azure Machine Learning | Microsoft Docs
description: "Novos recursos que estão disponíveis no Azure Machine Learning."
services: machine-learning
documentationcenter: 
author: vDonGlover
manager: raymondl
editor: 
ms.assetid: ddc716ed-2615-4806-bf27-6c9a5662a7f2
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2017
ms.author: v-donglo
ms.openlocfilehash: 1e3dc7e1375488ae9473cdd26f4d00871a091fbd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="whats-new-in-azure-machine-learning"></a>Novidades do Azure Machine Learning

### <a name="the-march-2017-release-of-microsoft-azure-machine-learning-updates-provides-the-following-feature"></a>A versão de março de 2017 das atualizações do Microsoft Azure Machine Learning fornece o seguinte recurso:



* Capacidade dedicada para trabalhos BES do Machine Learning

    O processamento em Pool do Lote do Machine Learning usa o [serviço de Lote do Azure](../../batch/batch-technical-overview.md) para fornecer escala gerenciada pelo cliente para o Serviço de Execução em Lotes do Machine Learning do Azure. O processamento em Pool do Lote permite criar pools de Lote do Azure nos quais é possível enviar trabalhos em lotes e tê-los executados de maneira previsível.

    Para saber mais, confira [serviço de Lote do Azure para trabalhos do Machine Learning](dedicated-capacity-for-bes-jobs.md).


### <a name="the-august-2016-release-of-microsoft-azure-machine-learning-updates-provide-the-following-features"></a>A versão de agosto de 2016 do Microsoft Azure Machine Learning fornece os seguintes recursos:
* Os serviços da Web clássicos agora podem ser gerenciados no novo portal dos [Serviços Web do Microsoft Azure Machine Learning](https://services.azureml.net/), que fornece um local para gerenciar todos os aspectos do serviço da Web.    
  * Que fornece [estatísticas de uso](manage-new-webservice.md)do serviço da Web.
  * Simplifica o teste de chamadas de solicitação remotas do Azure Machine Learning usando dados de exemplo.
  * Fornece uma nova página de teste do Serviço de Execução em Lote com dados de exemplo e histórico de envio de trabalhos.
  * Fornece gerenciamento do ponto de extremidade simplificado.

### <a name="the-july-2016-release-of-microsoft-azure-machine-learning-updates-provide-the-following-features"></a>A versão de julho de 2016 do Microsoft Azure Machine Learning fornece os seguintes recursos:
* Os serviços da Web agora são gerenciados como recursos do Azure gerenciados por meio das interfaces do [Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) , permitindo os seguintes aprimoramentos:
  * Há novas [APIs REST](https://msdn.microsoft.com/library/azure/Dn950030.aspx) para implantar e gerenciar seus serviços da Web baseados no Resource Manager.
  * Há um novo portal dos [Serviços Web do Microsoft Azure Machine Learning](https://services.azureml.net/) que fornece um local para gerenciar todos os aspectos do serviço da Web.
* Incorpora um novo modelo de implantação de serviço da Web de várias regiões, baseado em assinatura, usando APIs baseadas no Resource Manager que utilizam o Provedor de Recursos do Resource Manager para Serviços da Web.
* Introduz novos [planos de preços](https://azure.microsoft.com/pricing/details/machine-learning/) e recursos de gerenciamento de planos usando o novo RP do Resource Manager para Cobranças.
  * Agora você pode [implantar seu serviço da Web em várias regiões](how-to-deploy-to-multiple-regions.md) sem a necessidade de criar uma assinatura para cada região.
* Fornece [estatísticas de uso](manage-new-webservice.md)do serviço da Web.
* Simplifica o teste de chamadas de solicitação remotas do Azure Machine Learning usando dados de exemplo.
* Fornece uma nova página de teste do Serviço de Execução em Lote com dados de exemplo e histórico de envio de trabalhos.

Além disso, o Machine Learning Studio foi atualizado para permitir que você implante o novo modelo de serviço da Web ou continue a implantar o modelo de serviço da Web clássico. 

