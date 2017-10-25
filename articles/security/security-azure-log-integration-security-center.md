---
title: "Integração de Log do Azure com a Central de segurança | Microsoft Docs"
description: "Saiba como obter alertas da Central de segurança do Azure trabalhando com a integração de log"
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
ms.date: 08/29/2017
ms.author: barclayn
ms.custom: azlog
ms.openlocfilehash: 9acce21d544a43adcd0c0983771c02c6bb39caec
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-get-your-security-center-alerts-in-azure-log-integration"></a>Como obter os alertas da Central de segurança na integração de log do Azure

Este artigo fornece as etapas necessárias para habilitar o serviço de integração de Log do Azure para obter informações sobre o alerta de segurança gerado pela Central de segurança do Azure. Você deve ter concluído com êxito as etapas no artigo de [Introdução](security-azure-log-integration-get-started.md) antes de executar as etapas neste artigo.

## <a name="detailed-steps"></a>Etapas detalhadas

As etapas a seguir vão criar a Entidade de Serviço do Azure Active Directory necessária e atribuir as permissões de leitura da Entidade de Serviço para a assinatura:
1. Abra o prompt de comando e navegue até **c:\Program Files\Microsoft Azure Log Integration**
2. Executar o comando ``azlog createazureid``

    Esse comando solicitará o logon do Azure. O comando cria uma [Entidade de Serviço do Azure Active Directory](../active-directory/develop/active-directory-application-objects.md) em locatários do Azure AD que hospedam as assinaturas do Azure nas quais o usuário conectado é um Administrador, um Coadministrador ou um Proprietário. O comando falhará se o usuário conectado for apenas um usuário Convidado no Locatário do Azure AD. A autenticação do Azure é feita por meio do Azure Active Directory (AD). Criar uma entidade de serviço para a Integração do Azlog criará a identidade do Azure AD que terá o acesso de leitura a partir das assinaturas do Azure.

3. Em seguida, você executará um comando que atribui o acesso de leitor na assinatura à entidade de serviço recém-criada. Se você não especificar um SubscriptionID, o comando tentará atribuir a função de leitor da entidade de serviço para todas as assinaturas às quais você tem acesso. </br></br>
``azlog authorize <SubscriptionID>`` </br> por exemplo </br>
``azlog authorize 0ee55555-0bc4-4a32-a4e8-c29980000000``

    >[!NOTE]
    Você poderá ver avisos se executar o comando authorize imediatamente após o comando ```createazureid```. Há alguma latência entre quando a conta do Azure AD é criada e quando a conta está disponível para uso. Se você aguardar cerca de 60 segundos depois de executar o comando ```createazureid``` para executar o comando authorize, esses avisos não deverão ser exibidos.

4. Verifique as seguintes pastas para confirmar se os arquivos JSON do Log de auditoria existem:
 * **c:\Users\azlog\AzureResourceManagerJson**
 * **c:\Users\azlog\AzureResourceManagerJsonLD** </br></br>
5. Verifique as seguintes pastas para confirmar se os alertas da Central de Segurança existem:</br></br>
 * **c:\Users\azlog\AzureSecurityCenterJson**
 * **c:\Users\azlog\AzureSecurityCenterJsonLD** </br></br>

Se você tiver algum problema durante a instalação e configuração, abra uma [solicitação de suporte](/azure-supportability/how-to-create-azure-support-request.md) e selecione **Integração de Log** como o serviço para o qual você está solicitando suporte.

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre a Integração do Log do Azure, consulte os seguintes documentos:

* [Integração de log do Microsoft Azure para os logs do Azure](https://www.microsoft.com/download/details.aspx?id=53324) – visite o Centro de Download para obter os detalhes, os requisitos de sistema e as instruções de instalação da integração de log do Azure.
* [Introdução à integração de log do Azure](security-azure-log-integration-overview.md) – este documento apresenta a integração do registro do Azure, seus principais recursos e como ele funciona.
* [Etapas de configuração de parceiro](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/) – esta postagem de blog mostra a você como configurar a integração de log do Azure para trabalhar com as soluções de parceiros Splunk, HP ArcSight e IBM QRadar.
* [Perguntas frequentes sobre o log de integração do Azure](security-azure-log-integration-faq.md) – encontre as respostas para as perguntas frequentes sobre a integração de log do Azure.
* [Novos recursos para o Diagnóstico do Azure e para os Logs de Auditoria do Azure](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/) – esta postagem de blog apresenta os Logs de Auditoria do Azure e outros recursos que ajudam você a obter ideias sobre as operações de seus recursos do Azure.
