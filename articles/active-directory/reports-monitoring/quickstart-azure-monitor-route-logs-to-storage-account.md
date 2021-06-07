---
title: Tutorial – Arquivar logs do diretório em uma conta de armazenamento | Microsoft Docs
description: Saiba como configurar o Diagnóstico do Azure para efetuar push de logs do Azure Active Directory para uma conta de armazenamento
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 045f94b3-6f12-407a-8e9c-ed13ae7b43a3
ms.service: active-directory
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: fa2cac7c89a2cb1bd99675727bcd51e82c4a18ef
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "74007856"
---
# <a name="tutorial-archive-azure-ad-logs-to-an-azure-storage-account"></a>Tutorial: Arquivar logs do Azure AD em uma conta de armazenamento do Azure

Neste tutorial, você aprende a definir as configurações de diagnóstico do Azure Monitor para encaminhar logs do Azure AD (Active Directory) para uma conta de armazenamento do Azure.

## <a name="prerequisites"></a>Pré-requisitos 

Para usar esse recurso, você precisa de:

* Uma assinatura do Azure com uma conta de armazenamento do Azure. Se não tiver uma assinatura do Azure, você poderá [inscrever-se em uma avaliação gratuita](https://azure.microsoft.com/free/).
* Um locatário do Azure AD.
* Um usuário que seja *administrador global* ou *administrador de segurança* do locatário do Azure AD.

## <a name="archive-logs-to-an-azure-storage-account"></a>Arquivar os logs para uma conta de armazenamento do Azure

1. Entre no [portal do Azure](https://portal.azure.com). 

2. Selecione **Azure Active Directory** > **Atividade** > **Logs de auditoria**. 

3. Selecione **Exportar Configurações**. 

4. No painel **Configurações de Diagnóstico**, siga um destes procedimentos:
   * Para alterar as configurações existentes, selecione **Editar configuração**.
   * Para adicionar novas configurações, selecione **Adicionar configuração de diagnóstico**.  
     Você pode ter até três configurações. 

     ![Exportar configurações](./media/quickstart-azure-monitor-route-logs-to-storage-account/ExportSettings.png)

5. Insira um nome amigável para a configuração para lembrá-lo da finalidade (por exemplo, *Enviar para a conta de armazenamento do Azure*). 

6. Marque a caixa de seleção **Arquivar em uma conta de armazenamento** e selecione **Conta de armazenamento**. 

7. Selecione a assinatura e a conta de assinatura do Azure para onde você deseja encaminhar os logs.
 
8. Selecione **OK** para sair da configuração.

9. Siga um destes procedimentos, ou ambos:
    * Para enviar os logs de auditoria para a conta de armazenamento, marque a caixa de seleção **AuditLogs**. 
    * Para enviar os logs de entrada para a conta de armazenamento, marque a caixa de seleção **SignInLogs**.

10. Use o controle deslizante para definir a retenção dos dados de log. Por padrão, esse valor é *0*, o que significa que os logs são mantidos na conta de armazenamento indefinidamente. Se você definir um valor diferente, os eventos mais antigos que o número de dias selecionado serão limpos automaticamente.

11. Clique em **Salvar** para salvar a configuração.

    ![Configurações de diagnóstico](./media/quickstart-azure-monitor-route-logs-to-storage-account/DiagnosticSettings.png)

12. Após cerca de 15 minutos, verifique se os logs são enviados por push para sua conta de armazenamento. Vá para o [portal do Azure](https://portal.azure.com), selecione **Contas de armazenamento**, selecione a conta de armazenamento que você usou anteriormente e selecione **Blobs**. Como **Logs de auditoria**, selecione **insights-log-audit**. Como **Logs de entrada**, selecione **insights-log-signin**.

    ![Conta de armazenamento](./media/quickstart-azure-monitor-route-logs-to-storage-account/StorageAccount.png)

## <a name="next-steps"></a>Próximas etapas

* [Interpretar o esquema de logs de auditoria no Azure Monitor](reference-azure-monitor-audit-log-schema.md)
* [Interpretar o esquema de logs de entrada no Azure Monitor](reference-azure-monitor-sign-ins-log-schema.md)
* [Perguntas frequentes e problemas conhecidos](concept-activity-logs-azure-monitor.md#frequently-asked-questions)