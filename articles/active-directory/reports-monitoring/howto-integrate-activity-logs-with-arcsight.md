---
title: Integrar logs com o ArcSight usando o Azure Monitor | Microsoft Docs
description: Saiba como integrar logs de Azure Active Directory com o ArcSight usando Azure Monitor
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: b37bef0d-982e-4e28-86b2-6c61ca524ae1
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/19/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: fc7bcab04da005fd0d46d18e7b708dcb1c9d58e5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89230510"
---
# <a name="integrate-azure-active-directory-logs-with-arcsight-using-azure-monitor"></a>Integrar logs do Azure Active Directory ao ArcSight usando o Azure Monitor

[Micro Focus ArcSight](https://software.microfocus.com/products/siem-security-information-event-management/overview) uma solução SIEM (gerenciamento de eventos e informações de segurança) que ajuda a detectar e responder a ameaças de segurança na plataforma. Agora você pode rotear os logs do Azure AD (Azure Active Directory) para ArcSight usando o Azure Monitor usando o conector ArcSight para Azure AD. Esse recurso permite que você monitore o locatário por comprometimento de segurança usando o ArcSight.  

Neste artigo, você aprende como rotear logs do Azure AD para ArcSight usando o Azure Monitor. 

## <a name="prerequisites"></a>Pré-requisitos

Para usar esse recurso, você precisa de:
* Um hub de eventos do azure contendo logs de atividades do Microsoft Azure AD. Saiba como [enviar seus logs de atividades para um hub de eventos](./tutorial-azure-monitor-stream-logs-to-event-hub.md). 
* Uma instância configurada do SmartConnector Daemon Syslog NG (SmartConnector) do ArcSight ou Balanceador de Carga do ArcSight. Se os eventos forem enviados ao Balanceador de Carga do ArcSight, eles serão enviados ao SmartConnector pelo Balanceador de Carga.

Baixe e abra o [guia de configuração do ArcSight SmartConnector para Hub de Eventos do Azure Monitor](https://community.microfocus.com/t5/ArcSight-Connectors/SmartConnector-for-Microsoft-Azure-Monitor-Event-Hub/ta-p/1671292). Este guia contém as etapas necessárias para instalar e configurar o ArcSight SmartConnector para Azure Monitor. 

## <a name="integrate-azure-ad-logs-with-arcsight"></a>Integrar logs do Azure AD com ArcSight

1. Primeiro, conclua as etapas na seção **Pré-requisitos** do guia de configuração. Esta seção inclui as etapas a seguir:
    * Definir permissões de usuário no Azure para garantir que tenha um usuário com a função **proprietário** para implantar e configurar o conector.
    * Abrir portas no servidor com SmartConnector Daemon Syslog NG para acessá-lo a partir do Azure. 
    * A implantação executa um script do Windows PowerShell, portanto, é necessário habilitar o PowerShell para executar scripts no computador em que você que implantar o conector.

2. Siga as etapas na seção **Implantar o conector** do guia de configuração para implantar o conector. Esta seção explica como baixar e extrair o conector, configurar propriedades do aplicativo e executar o script de implantação a partir da pasta extraída. 

3. Use as etapas em **Verificar a implantação no Azure** para certificar-se de que o conector está configurado e funcionando corretamente. Verifique o seguinte:
    * As funções necessárias do Azure são criadas na assinatura do Azure.
    * Os logs do Azure AD são transmitidos para o destino correto. 
    * As configurações do aplicativo da implantação são mantidas nas configurações do aplicativo em aplicativos do Azure Functions. 
    * Um novo grupo de recursos para ArcSight é criado no Azure com um aplicativo do Azure AD para o conector ArcSight e contas de armazenamento contendo os arquivos mapeados no formato CEF.

4. Por fim, conclua as etapas de pós-implantação nas **Configurações de Pós-Implantação** do guia de configuração. Esta seção explica como executar uma configuração adicional se você estiver em um plano do serviço de aplicativo para impedir que os aplicativos de funções fiquem ociosos após um período de tempo limite, configurar o streaming de logs de recursos do hub de eventos e atualizar o certificado SysLog NG daemon SmartConnector do repositório de chaves para associá-lo à conta de armazenamento recém-criada.

5. O guia de configuração também explica como personalizar as propriedades do conector no Azure e como atualizar e desinstalar o conector. Há também uma seção sobre aprimoramentos de desempenho, incluindo upgrade para um [Plano de Consumo do Azure](https://azure.microsoft.com/pricing/details/functions) e configuração de um Balanceador de Carga do ArcSight se a carga de eventos for maior que um SmartConnector Daemon Syslog NG único pode manipular.

## <a name="next-steps"></a>Próximas etapas

[Guia de configuração do ArcSight SmartConnector para Hub de Eventos do Azure Monitor](https://community.microfocus.com/t5/ArcSight-Connectors/SmartConnector-for-Microsoft-Azure-Monitor-Event-Hub/ta-p/1671292)