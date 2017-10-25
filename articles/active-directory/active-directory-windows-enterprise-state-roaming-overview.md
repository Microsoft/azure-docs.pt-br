---
title: "Visão geral do Enterprise State Roaming | Microsoft Docs"
description: "Fornece informações sobre as configurações do Enterprise State Roaming em dispositivos do Windows. O Enterprise State Roaming fornece aos usuários uma experiência unificada em seus dispositivos Windows e reduz o tempo necessário para configurar um novo dispositivo."
services: active-directory
keywords: "o que é o Enterprise State Roaming, sincronização de empresa, nuvem do windows"
documentationcenter: 
author: tanning
manager: femila
editor: curtand
ms.assetid: 83b3b58f-94c1-4ab0-be05-20e01f5ae3f0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/08/2017
ms.author: markvi
ms.openlocfilehash: b3c01f8d332d26e92dc3052681a4b2c95142d440
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="enterprise-state-roaming-overview"></a>Visão geral do Enterprise State Roaming
Com o Windows 10, os usuários do [Active Directory do Azure (AD do Azure)](active-directory-whatis.md) obtêm a capacidade de sincronizar com segurança suas configurações de usuário e seus dados de configurações do aplicativo com a nuvem. O Enterprise State Roaming fornece aos usuários uma experiência unificada em seus dispositivos Windows e reduz o tempo necessário para configurar um novo dispositivo. O Enterprise State Roaming funciona de maneira semelhante à [sincronização de configurações de consumidor](http://windows.microsoft.com/en-US/windows-8/sync-settings-pcs) padrão, introduzida no Windows 8. Além disso, o Enterprise State Roaming oferece:

* **Separação dos dados corporativos e do consumidor** – as organizações estão no controle de seus dados, e não há nenhuma combinação de dados corporativos em uma conta de nuvem do consumidor ou de dados do consumidor em uma conta de nuvem da empresa.
* **Segurança avançada** – os dados são criptografados automaticamente antes de deixar o dispositivo Windows 10 do usuário usando o Azure Rights Management (Azure RMS) e os dados permanecem criptografados em repouso na nuvem. Todo o conteúdo permanece criptografado em repouso na nuvem, exceto os namespaces, como os nomes de configurações e os nomes de aplicativo do Windows.  
* **Melhor monitoramento e gerenciamento** – oferece mais controle e visibilidade sobre quem sincroniza configurações em sua organização e em quais dispositivos por meio da integração do portal do Azure AD. 

O Enterprise State Roaming está disponível em várias regiões do Azure. Você pode encontrar a lista atualizada de regiões disponíveis na página [Serviços do Azure por Região](https://azure.microsoft.com/regions/#services) no Azure Active Directory.

| Artigo | Descrição |
| --- | --- |
| [Habilitar o Enterprise State Roaming no Active Directory do Azure](active-directory-windows-enterprise-state-roaming-enable.md) |O Enterprise State Roaming está disponível para todas as organizações com uma assinatura Premium do Azure AD (Active Directory). Para obter mais detalhes sobre como obter uma assinatura do Azure AD, confira a [página de produto do Azure AD](https://azure.microsoft.com/services/active-directory) . |
| [Perguntas frequentes sobre configurações e roaming de dados](active-directory-windows-enterprise-state-roaming-faqs.md) |Este tópico responde a algumas dúvidas que os administradores de TI podem ter sobre as configurações e a sincronização de dados do aplicativo. |
| [Política de grupo e as configurações do MDM para a sincronização de configurações](active-directory-windows-enterprise-state-roaming-group-policy-settings.md) |O Windows 10 fornece configurações da Política de Grupo e da política de gerenciamento de dispositivos móveis (MDM) para limitar a sincronização de configurações. |
| [Referência de configurações de roaming do Windows 10](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md) |Esta é uma lista completa de todas as configurações que serão ser movidas e/ou armazenadas em backup no Windows 10. |
| [Solução de problemas](active-directory-windows-enterprise-state-roaming-troubleshooting.md) |Este tópico aborda algumas etapas básicas para solução de problemas, além de conter uma lista de problemas conhecidos. |

