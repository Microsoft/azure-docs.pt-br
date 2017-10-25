---
title: "Integração de Log do Azure com os logs de auditoria do Azure Active Directory | Microsoft Docs"
description: "Saiba como instalar o serviço de Integração de Logs do Azure e integre os registros de logs de auditoria do Azure"
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomShinder
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ums.workload: na
ms.date: 08/08/2017
ms.author: barclayn
ms.custom: azlog
ms.openlocfilehash: 8a1295cc86057ed72940e774d0bd423d61142e31
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="integrate-azure-active-directory-audit-logs"></a>Integrar o Azure Active Directory a logs de auditoria

Eventos de auditoria do Azure AD (Azure Active Directory) ajudam a identificar ações privilegiadas que ocorreram no Azure Active Directory. Você pode ver os tipos de eventos que você pode acompanhar examinando os [eventos de relatório de auditoria do Azure Active Directory](/active-directory/active-directory-reporting-audit-events#list-of-audit-report-events.md).

> [!NOTE]
> Antes de experimentar as etapas neste artigo, você deve examinar o artigo [Introdução](security-azure-log-integration-get-started.md) e concluir as etapas dele.

## <a name="steps-to-integrate-azure-active-directory-audit-logs"></a>Etapas para integrar os logs de auditoria do Azure Active Directory

1. Abra o prompt de comando e execute este comando:

   ``cd c:\Program Files\Microsoft Azure Log Integration``

2. Execute este comando: 
 
   ``azlog createazureid``

   Esse comando solicitará o logon do Azure. O comando cria uma entidade de serviço do Azure Active Directory em locatários do Azure AD que hospedam as assinaturas do Azure nas quais o usuário conectado é um administrador, um coadministrador ou um proprietário. O comando falhará se o usuário conectado for apenas um usuário convidado no locatário do Azure AD. A autenticação do Azure é feita por meio do AD do Azure. A criação de uma entidade de serviço para a Integração de Logs do Azure criará a identidade do Azure AD que receberá o acesso de leitura das assinaturas do Azure.

3. Execute o seguinte comando para fornecer sua ID de locatário. Você precisa ser membro da função de administrador de locatário para executar o comando.

   ``Azlog.exe authorizedirectoryreader tenantId``

   Exemplo:

   ``AZLOG.exe authorizedirectoryreader ba2c0000-d24b-4f4e-92b1-48c4469999``

4. Verifique as seguintes pastas para confirmar se os arquivos JSON do Log de Auditoria do Azure Active Directory são criados nelas:

   * **C:\Users\azlog\AzureActiveDirectoryJson**
   * **C:\Users\azlog\AzureActiveDirectoryJsonLD**

O vídeo a seguir demonstra as etapas abordadas neste artigo:

> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Azure-AD-Integration/player]


> [!NOTE]
> Para obter instruções específicas sobre como colocar as informações nos arquivos JSON em seu SIEM (sistema de gerenciamento de eventos e informações de segurança), contate o fornecedor do SIEM.

Ajuda da comunidade está disponível por meio de [Fórum do MSDN de integração de Log do Azure](https://social.msdn.microsoft.com/Forums/office/home?forum=AzureLogIntegration). Este fórum permite que as pessoas na comunidade de Integração de Log do Azure deem suporte umas às outras com perguntas, respostas, dicas e truques. Além disso, a equipe de Integração de Logs do Azure monitora esse fórum e ajuda sempre que possível.

Você também pode abrir uma [solicitação de suporte](../azure-supportability/how-to-create-azure-support-request.md). Selecione **Integração de Log** como o serviço para o qual você está solicitando suporte.

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre a Integração de Log do Azure, veja:

* [Integração de Logs do Microsoft Azure para os logs do Azure](https://www.microsoft.com/download/details.aspx?id=53324): a página Centro de Download oferece os detalhes, os requisitos de sistema e as instruções de instalação da Integração de Logs do Azure.
* [Introdução à Integração de Logs do Azure](security-azure-log-integration-overview.md): este documento apresenta a Integração de Logs do Azure, seus principais recursos e como ele funciona.
* [Etapas de configuração de parceiro](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/): esta postagem de blog mostra a você como configurar a Integração de Logs do Azure para trabalhar com as soluções de parceiros Splunk, HP ArcSight e IBM QRadar.
* [Perguntas frequentes sobre a Integração do Log do Azure](security-azure-log-integration-faq.md): este artigo responde perguntas sobre a Integração do Log do Azure.
* [Integração dos alertas da Central de Segurança com a Integração de Log do Azure](../security-center/security-center-integrating-alerts-with-log-integration.md): este artigo mostra como sincronizar os alertas da Central de Segurança, juntamente com os eventos de segurança de máquina virtual coletados pelo Diagnóstico do Azure e pelos logs de auditoria do Azure, com o Log Analytics ou com a solução SIEM.
* [Novos recursos para o Diagnóstico do Azure e para os Logs de Auditoria do Azure](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/): esta postagem de blog apresenta os logs de auditoria do Azure e outros recursos que ajudam você a obter ideias sobre as operações de seus recursos do Azure.
