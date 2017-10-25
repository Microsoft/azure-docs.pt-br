---
title: "Integração de alertas da Central de Segurança do Azure com a integração de log do Azure | Microsoft Docs"
description: "Este artigo ajuda você a integrar alertas da Central de Segurança com a integração de log do Azure."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: d2d088d3-d38d-47ff-a062-c78e0fd59226
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/23/2017
ms.author: terrylan
ms.openlocfilehash: d13e5b87c446e587091551b22d80fe568d5d8093
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="integrating-azure-security-center-alerts-with-azure-log-integration"></a>Integração de alertas da Central de Segurança do Azure com a integração de log do Azure
Muitas operações de segurança e equipes de resposta a incidentes contam com uma solução SIEM (Gerenciamento de eventos e informações de segurança) como ponto de partida para separação e investigação de alertas de segurança. Com a Integração de Log do Azure, você pode integrar alertas da Central de Segurança do Azure com sua solução SIEM.

A Integração de Log do Azure atualmente dá suporte a HP ArcSight, Splunk e QRadar da IBM.

## <a name="what-logs-can-i-integrate"></a>Quais logs posso integrar?
Azure produz um log abrangente para cada serviço. Esses logs são categorizados como:

* **Logs de Controle/Gerenciamento**, que fornecem visibilidade nas operações CREATE, UPDATE e DELETE do Azure Resource Manager. Esses eventos de plano de controle são exibidos nos Logs de Atividade do Azure
* **Logs de Plano de Dados** que fornecem visibilidade nos eventos gerados ao usar um recurso do Azure. Um exemplo é o log de Eventos do Windows, em que você pode obter informações sobre eventos de segurança do Canal de Segurança do Visualizador de Eventos. Eventos de plano de dados (que são gerados por uma máquina virtual ou um serviço do Azure) são exibidos pelos Logs de Diagnóstico do Azure.

A integração do log do Azure atualmente suporta a integração de:

* Logs da VM do Azure
* Logs de Auditoria do Azure
* Alertas da Central de Segurança do Azure

## <a name="install-azure-log-integration"></a>Instalar a integração do log do Azure
Baixe a [integração do log do Azure](https://www.microsoft.com/download/details.aspx?id=53324).

O serviço de integração do log do Azure coleta os dados de telemetria do computador no qual está instalado.  Os dados de telemetria coletados são:

* Exceções que ocorrem durante a execução da integração do log do Azure
* Métricas sobre o número de consultas e eventos processados
* Estatísticas sobre quais opções da linha de comando do Azlog.exe estão sendo usadas

> [!NOTE]
> Você pode desativar a coleta dos dados de telemetria desmarcando essa opção.
>
>

## <a name="integrate-azure-audit-logs-and-security-center-alerts"></a>Integrar os Logs de Auditoria do Azure e os alertas da Central de Segurança
1. Abra o prompt de comando e **cd** em **c:\Program Files\Microsoft Azure Log Integration**.
2. Execute o comando **azlog createazureid** para criar uma [Entidade de serviço do Azure Active Directory](../active-directory/active-directory-application-objects.md) nos locatários do Azure Active Directory (AD) que hospedam as assinaturas do Azure.

    Você deverá inserir seu logon do Azure.

   > [!NOTE]
   > Você deve ser o Proprietário da assinatura ou um Coadministrador da assinatura.
   >
   >

    A autenticação do Azure é feita por meio do AD do Azure.  A criação de uma entidade de serviço para a integração de log do Azure criará a identidade do Azure AD que receberá o acesso de leitura das assinaturas do Azure.
3. Execute o comando **azlog authorize <SubscriptionID>** para atribuir acesso de Leitor na assinatura à entidade de serviço criada na etapa 2. Se você não especificar uma **SubscriptionID**, a entidade de serviço será atribuída à função de Leitor para todas as assinaturas às quais você tem acesso.

   > [!NOTE]
   > Você poderá ver avisos se executar o comando **authorize** imediatamente após o comando **createazureid**. Há alguma latência entre quando a conta do Azure AD é criada e quando a conta está disponível para uso. Se você esperar cerca de 10 segundos depois de executar o comando **createazureid** para executar o comando **authorize**, esses avisos não serão exibidos.
   >
   >
4. Verifique as seguintes pastas para confirmar se os arquivos JSON do Log de auditoria existem:

   * **c:\Users\azlog\AzureResourceManagerJson**
   * **c:\Users\azlog\AzureResourceManagerJsonLD**
5. Verifique as seguintes pastas para confirmar se os alertas da Central de Segurança existem:

   * **c:\Users\azlog\ AzureSecurityCenterJson**
   * **c:\Users\azlog\AzureSecurityCenterJsonLD**
6. Configure o conector de encaminhador de arquivo SIEM para a pasta apropriada. O procedimento variará com base no SIEM que você estiver usando.

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre os Logs de Atividade do Azure e as definições das propriedades, consulte:

* [Operações de auditoria com o Gerenciador de Recursos](../azure-resource-manager/resource-group-audit.md)

Para saber mais sobre a Central de Segurança, confira o seguinte:

* [Gerenciando e respondendo aos alertas de segurança na Central de Segurança do Azure](security-center-managing-and-responding-alerts.md) : aprenda a gerenciar e responder aos alertas de segurança.
* [Perguntas frequentes da Central de Segurança do Azure](security-center-faq.md) : encontre as perguntas frequentes sobre como usar o serviço.
* [Blog de Segurança do Azure](http://blogs.msdn.com/b/azuresecurity/) : obtenha as últimas notícias de segurança e informações do Azure.
