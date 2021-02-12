---
title: Conector de Gerenciamento de Serviços de TI-exportação segura no Azure Monitor-configurações do Azure
description: Este artigo mostra como configurar o Azure para conectar seus produtos/serviços de ITSM com exportação segura em Azure Monitor para monitorar e gerenciar centralmente os itens de trabalho de ITSM.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 01/03/2021
ms.openlocfilehash: 03a16bfbcb3a8d46a6cb4faa03aa6b6e96cf3db3
ms.sourcegitcommit: 16887168729120399e6ffb6f53a92fde17889451
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/13/2021
ms.locfileid: "98165953"
---
# <a name="configure-azure-to-connect-itsm-tools-using-secure-export"></a>Configurar o Azure para conectar as ferramentas de ITSM usando a exportação segura

Este artigo fornece informações sobre como configurar o Azure para usar a "exportação segura".
Para usar a "exportação segura", siga estas etapas:

1. [Registrar um aplicativo no Azure AD.](./itsm-connector-secure-webhook-connections-azure-configuration.md#register-with-azure-active-directory)
1. [Defina a entidade de serviço.](./itsm-connector-secure-webhook-connections-azure-configuration.md#define-service-principal)
1. [Crie um grupo de ação de webhook seguro.](./itsm-connector-secure-webhook-connections-azure-configuration.md#create-a-secure-webhook-action-group)
1. Configure seu ambiente de parceiro.
    A exportação segura dá suporte a conexões com as seguintes ferramentas de ITSM:
    * [ServiceNow](./itsmc-secure-webhook-connections-servicenow.md)
    * [BMC Helix](./itsmc-secure-webhook-connections-bmc.md)

## <a name="register-with-azure-active-directory"></a>Registrar com Azure Active Directory

Siga estas etapas para registrar o aplicativo com o Azure AD:

1. Siga as etapas em [registrar um aplicativo com a plataforma de identidade da Microsoft](../../active-directory/develop/quickstart-register-app.md).
2. No Azure AD, selecione **expor aplicativo**.
3. Selecione **definir** para o **URI da ID do aplicativo**.

   [![Captura de tela da opção de configuração do U R I do aplicativo I D.](media/it-service-management-connector-secure-webhook-connections/azure-ad.png)](media/it-service-management-connector-secure-webhook-connections/azure-ad-expand.png#lightbox)
4. Selecione **Salvar**.

## <a name="define-service-principal"></a>Definir entidade de serviço

O serviço do grupo de ações é um aplicativo de primeira parte, portanto, ele tem permissão para adquirir tokens de autenticação do seu aplicativo do AAD para autenticação com o serviço agora.
Como uma etapa opcional, você pode definir a função de aplicativo no manifesto do aplicativo criado, que pode permitir que você restrinja ainda mais o acesso de forma que apenas determinados aplicativos com essa função específica possam enviar mensagens. Essa função deve ser atribuída à entidade de serviço do grupo de ações (requer privilégios de administrador de locatário).

Essa etapa pode ser feita por meio dos mesmos [comandos do PowerShell](./action-groups.md#secure-webhook-powershell-script).

## <a name="create-a-secure-webhook-action-group"></a>Criar um grupo de ação de webhook seguro

Depois que o aplicativo for registrado no Azure AD, você poderá criar itens de trabalho em sua ferramenta de ITSM com base nos alertas do Azure, usando a ação proteger webhook em grupos de ações.

Os grupos de ações fornecem uma maneira modular e reutilizável de disparar ações para alertas do Azure. Você pode usar grupos de ação com alertas de métrica, alertas do log de atividades e alertas de Log Analytics do Azure no portal do Azure.
Para saber mais sobre grupos de ações, veja [Criar e gerenciar grupos de ações no portal do Azure](./action-groups.md).

Para adicionar um webhook a uma ação, siga estas instruções para o webhook seguro:

1. No [portal do Azure](https://portal.azure.com/), pesquise e selecione **Monitor**. O painel **Monitor** consolida todas as configurações e dados de monitoramento em uma exibição.
2. Selecione **alertas**  >  **Gerenciar ações**.
3. Selecione [Adicionar grupo de ações](./action-groups.md#create-an-action-group-by-using-the-azure-portal) e preencha os campos.
4. Insira um nome na caixa **Nome do grupo de ação** e, em seguida, digite um nome na caixa **Nome curto**. O nome curto é usado no lugar de um nome de grupo de ação completo quando as notificações são enviadas usando esse grupo.
5. Selecione **proteger webhook**.
6. Selecione estes detalhes:
   1. Selecione a ID de objeto da instância de Azure Active Directory que você registrou.
   2. Para o URI, Cole a URL do webhook que você copiou do [ambiente da ferramenta de ITSM](#configure-the-itsm-tool-environment).
   3. Defina **habilitar o esquema de alerta comum** como **Sim**. 

   A imagem a seguir mostra a configuração de uma ação de webhook segura de exemplo:

   ![Captura de tela que mostra uma ação de webhook segura.](media/it-service-management-connector-secure-webhook-connections/secure-webhook.png)

## <a name="configure-the-itsm-tool-environment"></a>Configurar o ambiente da ferramenta de ITSM

A configuração contém duas etapas:

1. Obtenha o URI para a definição de exportação segura.
2. Definições de acordo com o fluxo da ferramenta de ITSM.

## <a name="next-steps"></a>Próximas etapas

* [Configuração de exportação segura do ServiceNow](./itsmc-secure-webhook-connections-servicenow.md)
* [Configuração de exportação segura do BMC](./itsmc-secure-webhook-connections-bmc.md)
