---
title: Conector de Gerenciamento de Serviços de TI-exportação segura em Azure Monitor-configuração com o ServiceNow
description: Este artigo mostra como conectar seus produtos/serviços de ITSM com o ServiceNow em exportação segura no Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/31/2020
ms.openlocfilehash: fc384ffbc246f5ce9fa84b8161cbc4a5226fa5c8
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/31/2020
ms.locfileid: "97837996"
---
# <a name="connect-servicenow-to-azure-monitor"></a>Conectar o ServiceNow ao Azure Monitor

As seções a seguir fornecem detalhes sobre como conectar seu produto ServiceNow e a exportação segura no Azure.

## <a name="prerequisites"></a>Pré-requisitos

Verifique se você atendeu aos seguintes pré-requisitos:

* O Azure AD está registrado.
* Você tem a versão com suporte do ServiceNow Event Management-ITOM (versão Orlando ou posterior).

## <a name="configure-the-servicenow-connection"></a>Configurar a conexão do ServiceNow

1. Use o link https://(nome da instância). Service-Now. com/API/sn_em_connector/em/inbound_event? origem = azuremonitor o URI para a definição de exportação segura.

2. Siga as instruções de acordo com a versão:
   * [Paris](https://docs.servicenow.com/bundle/paris-it-operations-management/page/product/event-management/task/azure-events-authentication.html)
   * [Orlando](https://docs.servicenow.com/bundle/orlando-it-operations-management/page/product/event-management/task/azure-events-authentication.html)
   * [Nova Iorque](https://docs.servicenow.com/bundle/newyork-it-operations-management/page/product/event-management/task/azure-events-authentication.html)