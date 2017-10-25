---
title: "Localizar aplicativos na nuvem não gerenciados com o Cloud App Discovery no Azure Active Directory | Microsoft Docs"
description: "Fornece informações sobre a localização e o gerenciamento de aplicativos com o Cloud App Discovery, quais são seus benefícios e como ele funciona."
services: active-directory
keywords: cloud app discovery, gerenciamento de aplicativos
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: db968bf5-22ae-489f-9c3e-14df6e1fef0a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 35b898aa3c03aeef914a7df574ac65a22a6c7bec
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="find-unmanaged-cloud-applications-with-cloud-app-discovery"></a>Localizar aplicativos na nuvem não gerenciados com o Cloud App Discovery
## <a name="summary"></a>Resumo

O Cloud App Discovery é um recurso do Azure Active Directory Premium que permite descobrir os aplicativos na nuvem não gerenciados usados pelas pessoas em sua organização. Nas empresas modernas, os departamentos de TI geralmente não estão cientes de todos os aplicativos em nuvem que os membros de sua organização usam para realizar seu trabalho. É fácil ver por que os administradores teriam preocupações sobre o acesso não autorizado aos dados corporativos, perda de dados e outros riscos de segurança. Essa falta de reconhecimento pode fazer com que a criação de um plano para lidar com esses riscos de segurança pareça assustadora.

> [!TIP] 
> Confira as melhorias para o Cloud App Discovery no Azure AD (Azure Active Directory), que são aprimoradas por [integração com o Microsoft Cloud App Security](https://portal.cloudappsecurity.com).

**Com o Cloud App Discovery é possível:**

* Descubra os aplicativos em nuvem usados e meça o uso pelo número de usuários, volume de tráfego ou número de solicitações Web para o aplicativo.
* Identificar os usuários que estão usando um aplicativo.
* Exporte os dados para a análise offline.
* Coloque esses aplicativos sob controle da TI e habilite o logon único para o gerenciamento de usuário.

## <a name="how-it-works"></a>Como ele funciona
1. Os agentes de uso dos aplicativos são instalados nos computadores dos usuários.
2. As informações de uso do aplicativo coletadas pelos agentes são enviadas por um canal criptografado seguro para o serviço Cloud App Discovery.
3. O serviço Cloud App Discovery avalia os dados e gera relatórios.

![Diagrama do Cloud App Discovery](./media/active-directory-cloudappdiscovery/cad01.png)


## <a name="next-steps"></a>Próximas etapas
* [Considerações de privacidade e segurança no Cloud App Discovery](active-directory-cloudappdiscovery-security-and-privacy-considerations.md)  
* [Configurações de Registro do Cloud App Discovery para Servidores Proxy com Portas Personalizadas](active-directory-cloudappdiscovery-registry-settings-for-proxy-services.md)
* [Cloud App Discovery - Log de Alteração de Agente ](http://social.technet.microsoft.com/wiki/contents/articles/24616.cloud-app-discovery-agent-changelog.aspx)
* [Índice de artigos para Gerenciamento de Aplicativos no Active Directory do Azure](active-directory-apps-index.md)

