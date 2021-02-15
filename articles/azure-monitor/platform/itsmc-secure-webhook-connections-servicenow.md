---
title: Conector de Gerenciamento de Serviços de TI-exportação segura em Azure Monitor-configuração com o ServiceNow
description: Este artigo mostra como conectar seus produtos/serviços de ITSM com o ServiceNow em exportação segura no Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/31/2020
ms.openlocfilehash: 8ca77c1fa1232f7aed88b0d6942d8a0085caf0d5
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100380212"
---
# <a name="connect-servicenow-to-azure-monitor"></a>Conectar o ServiceNow ao Azure Monitor

As seções a seguir fornecem detalhes sobre como conectar seu produto ServiceNow e a exportação segura no Azure.

## <a name="prerequisites"></a>Pré-requisitos

Verifique se você atendeu aos seguintes pré-requisitos:

* O Azure AD está registrado.
* Você tem a versão com suporte do ServiceNow Event Management-ITOM (versão Nova York ou posterior).
* [Aplicativo](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ac4c9c57dbb1d090561b186c1396191a/1.2.0?referer=%2Fstore%2Fsearch%3Flistingtype%3Dallintegrations%25253Bancillary_app%25253Bcertified_apps%25253Bcontent%25253Bindustry_solution%25253Boem%25253Butility%26q%3Devent%2520management%2520connectors&sl=sh) instalado na instância do ServiceNow.

## <a name="configure-the-servicenow-connection"></a>Configurar a conexão do ServiceNow

1. Use o link https://(nome da instância). Service-Now. com/API/sn_em_connector/em/inbound_event? origem = azuremonitor o URI para a definição de exportação segura.

2. Siga as instruções de acordo com a versão:
   * [Paris](https://docs.servicenow.com/bundle/paris-it-operations-management/page/product/event-management/task/azure-events-authentication.html)
   * [Orlando](https://docs.servicenow.com/bundle/orlando-it-operations-management/page/product/event-management/task/azure-events-authentication.html)
   * [Nova Iorque](https://docs.servicenow.com/bundle/newyork-it-operations-management/page/product/event-management/task/azure-events-authentication.html)
