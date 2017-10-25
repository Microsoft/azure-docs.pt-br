---
title: Perguntas frequentes sobre o Azure IoT Suite | Microsoft Docs
description: Perguntas frequentes sobre o IoT Suite
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: cb537749-a8a1-4e53-b3bf-f1b64a38188a
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/15/2017
ms.author: corywink
ms.openlocfilehash: 5039dd30f85278b4e3bbba938ef40077af2c5c75
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="frequently-asked-questions-for-iot-suite"></a>Perguntas frequentes sobre o IoT Suite

Consulte também as [Perguntas frequentes](iot-suite-faq-cf.md) específicas sobre a fábrica conectada.

### <a name="where-can-i-find-the-source-code-for-the-preconfigured-solutions"></a>Onde encontrar o código-fonte para as soluções pré-configuradas?

O código-fonte é armazenado nos seguintes repositórios GitHub:
* [Solução pré-configurada de monitoramento remoto][lnk-remote-monitoring-github]
* [Solução pré-configurada de manutenção preditiva][lnk-predictive-maintenance-github]
* [Solução pré-configurada de fábrica conectada](https://github.com/Azure/azure-iot-connected-factory)

### <a name="how-do-i-update-to-the-latest-version-of-the-remote-monitoring-preconfigured-solution-that-uses-the-iot-hub-device-management-features"></a>Como atualizar para a versão mais recente da solução de monitoramento remoto predefinida que usa os recursos de gerenciamento de dispositivo do Hub IoT?

* Se você implantar uma solução pré-configurada do site https://www.azureiotsuite.com/, ela sempre implantará uma nova instância da versão mais recente da solução.
* Se implantar uma solução pré-configurada usando a linha de comando, você poderá atualizar uma implantação existente com o novo código. Confira [Implantação de nuvem][lnk-cloud-deployment] no [repositório][lnk-remote-monitoring-github] GitHub.

### <a name="how-can-i-add-support-for-a-new-device-method-to-the-remote-monitoring-preconfigured-solution"></a>Como adicionar suporte a um novo método de dispositivo para a solução pré-configurada de monitoramento remoto?

Confira a seção [Adicionar suporte a um novo método para o simulador][lnk-add-method] no artigo [Personalizar uma solução pré-configurada][lnk-customize].

### <a name="the-simulated-device-is-ignoring-my-desired-property-changes-why"></a>Por que o dispositivo simulado está ignorando as alterações de propriedade desejadas?
Na solução pré-configurada de monitoramento remoto, o código de dispositivo simulado usa apenas as propriedades desejadas **Desired.Config.TemperatureMeanValue** e **Desired.Config.TelemetryInterval** para atualizar as propriedades relatadas. Todas as outras solicitações de alteração de propriedade desejadas são ignoradas.

### <a name="my-device-does-not-appear-in-the-list-of-devices-in-the-solution-dashboard-why"></a>Por que o dispositivo não aparece na lista de dispositivos no painel de solução?

A lista de dispositivos no painel de solução usa uma consulta para retornar a lista de dispositivos. Atualmente, uma consulta não pode retornar mais de 10 mil dispositivos. Tente tornar os critérios de pesquisa da consulta mais restritivos.

### <a name="whats-the-difference-between-deleting-a-resource-group-in-the-azure-portal-and-clicking-delete-on-a-preconfigured-solution-in-azureiotsuitecom"></a>Qual é a diferença entre excluir um grupo de recursos no portal do Azure e clicar em excluir em uma solução pré-configurada no site azureiotsuite.com?

* Se você excluir a solução pré-configurada no site [azureiotsuite.com][lnk-azureiotsuite], todos os recursos provisionados na criação da solução pré-configurada serão excluídos. Se você adicionou mais recursos ao grupo de recursos, esses recursos também serão excluídos. 
* Se você excluir o grupo de recursos no [Portal do Azure][lnk-azure-portal], somente os recursos nesse grupo serão excluídos. Você também precisa excluir o aplicativo do Azure Active Directory associado à solução pré-configurada no [Portal Clássico do Azure][lnk-classic-portal].

### <a name="how-many-iot-hub-instances-can-i-provision-in-a-subscription"></a>Quantas instâncias do Hub IoT posso provisionar em uma assinatura?

Por padrão, você pode provisionar [10 Hubs IoT por assinatura][link-azuresublimits]. Você pode criar um [tíquete de suporte do Azure][link-azuresupportticket] para aumentar esse limite. Como resultado, uma vez que cada solução pré-configurada provisiona um novo Hub IoT, você só poderá provisionar até 10 soluções pré-configuradas em uma determinada assinatura. 

### <a name="how-many-azure-cosmos-db-instances-can-i-provision-in-a-subscription"></a>Quantas instâncias do Azure Cosmos DB posso provisionar em uma assinatura?

Cinquenta. Você pode criar um [tíquete de suporte do Azure][link-azuresupportticket] para aumentar esse limite, mas por padrão, você poderá apenas provisionar 50 instâncias do Cosmos DB por assinatura. 

### <a name="how-many-free-bing-maps-apis-can-i-provision-in-a-subscription"></a>Quantas APIs do Bing Mapas Gratuitas posso provisionar em uma assinatura?

Duas. Você só pode criar dois Bing Mapas de Transações Internas de Nível 1 para planos Enterprise em uma assinatura do Azure. A solução de monitoramento remoto é provisionada por padrão com o plano Transações Internas de Nível 1. Como resultado, você só poderá provisionar até duas soluções de monitoramento remotas em uma assinatura sem modificações.

### <a name="i-have-a-remote-monitoring-solution-deployment-with-a-static-map-how-do-i-add-an-interactive-bing-map"></a>Tenho uma implantação de solução de monitoramento remoto com um mapa estático, como posso adicionar um mapa interativo do Bing?

1. Obtenha a QueryKey da API do Bing Mapas para Empresas no [Portal do Azure][lnk-azure-portal]: 
   
   1. Navegue até o Grupo de Recursos onde está a API do Bing Mapas para Empresas no [Portal do Azure][lnk-azure-portal].
   2. Clique em **Todas as Configurações** e em **Gerenciamento de Chaves**. 
   3. Você pode ver duas chaves: **MasterKey** e **QueryKey**. Copie o valor de **QueryKey**.
      
      > [!NOTE]
      > Você não tem uma conta da API do Bing Maps para Empresa? Crie uma no [Portal do Azure][lnk-azure-portal] clicando em + Novo, procurando pela API do Bing Mapas para Empresas e seguindo os prompts de criação.
      > 
      > 
2. Obtenha o código mais recente do [Azure-IoT-Remote-Monitoring][lnk-remote-monitoring-github].
3. Execute uma implantação local ou em nuvem seguindo as diretrizes de implantação de linha de comando na pasta /docs/ do repositório. 
4. Depois de executar uma implantação local ou em nuvem, procure na pasta raiz pelo arquivo *.user.config criado durante a implantação. Abra esse arquivo em um editor de texto. 
5. Altere a linha a seguir para incluir o valor que você copiou de **QueryKey**: 
   
   `<setting name="MapApiQueryKey" value="" />`

### <a name="can-i-create-a-preconfigured-solution-if-i-have-microsoft-azure-for-dreamspark"></a>Posso criar uma solução pré-configurada se possuo o Microsoft Azure para DreamSpark?

Atualmente, você não pode criar uma solução pré-configurada com uma conta do [Microsoft Azure para DreamSpark][lnk-dreamspark]. No entanto, você pode criar uma [conta de avaliação gratuita do Azure][lnk-30daytrial] em apenas alguns minutos que permite a você criar uma solução pré-configurada.

### <a name="can-i-create-a-preconfigured-solution-if-i-have-cloud-solution-provider-csp-subscription"></a>Poderei criar uma solução pré-configurada se eu tiver uma assinatura do CSP (Provedor de Soluções na Nuvem)?

Atualmente, não é possível criar uma solução pré-configurada com uma assinatura do CSP (Provedor de Soluções na Nuvem). No entanto, você pode criar uma [conta de avaliação gratuita do Azure][lnk-30daytrial] em apenas alguns minutos que permite a você criar uma solução pré-configurada.

### <a name="how-do-i-delete-an-aad-tenant"></a>Como posso excluir um locatário do AAD?

Veja a postagem do blog de Eric Golpe, [Walkthrough of Deleting an Azure AD Tenant][lnk-delete-aad-tennant] (Passo a passo da exclusão de um locatário do Azure AD).

### <a name="next-steps"></a>Próximas etapas

Você também pode explorar alguns dos outros recursos das soluções pré-configuradas do IoT Suite:

* [Visão geral da solução pré-configurada de manutenção preditiva][lnk-predictive-overview]
* [Visão geral da solução pré-configurada de fábrica conectada](iot-suite-connected-factory-overview.md)
* [Segurança IoT desde o início][lnk-security-groundup]

[lnk-predictive-overview]: iot-suite-predictive-overview.md
[lnk-security-groundup]: securing-iot-ground-up.md

[link-azuresupportticket]: https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade 
[link-azuresublimits]: https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/#iot-hub-limits
[lnk-azure-portal]: https://portal.azure.com
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-classic-portal]: https://manage.windowsazure.com
[lnk-remote-monitoring-github]: https://github.com/Azure/azure-iot-remote-monitoring 
[lnk-dreamspark]: https://www.dreamspark.com/Product/Product.aspx?productid=99 
[lnk-30daytrial]: https://azure.microsoft.com/free/
[lnk-delete-aad-tennant]: http://blogs.msdn.com/b/ericgolpe/archive/2015/04/30/walkthrough-of-deleting-an-azure-ad-tenant.aspx
[lnk-cloud-deployment]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/cloud-deployment.md
[lnk-add-method]: iot-suite-guidance-on-customizing-preconfigured-solutions.md#add-support-for-a-new-method-to-the-simulator
[lnk-customize]: iot-suite-guidance-on-customizing-preconfigured-solutions.md
[lnk-remote-monitoring-github]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-predictive-maintenance-github]: https://github.com/Azure/azure-iot-predictive-maintenance
