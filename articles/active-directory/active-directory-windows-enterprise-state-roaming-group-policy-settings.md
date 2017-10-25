---
title: "Configurações de Política de Grupo e do MDM | Microsoft Docs"
description: "Fornece informações sobre as configurações de política de grupo e do MDM (gerenciamento de dispositivos móveis) que devem ser usadas em dispositivos corporativos. Essas políticas são aplicadas ao dispositivo inteiro do usuário."
services: active-directory
keywords: "quais são as configurações da Política e do MDM para o Enterprise State Roaming, Enterprise State Roaming, nuvem do windows"
documentationcenter: 
author: tanning
manager: femila
editor: curtand
ms.assetid: 6471a9b3-8dd4-4237-89d1-bfbeca9f8252
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/08/2017
ms.author: markvi
ms.openlocfilehash: 71dd5281a618fe7367eab3e97daac069f77ab491
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="group-policy-and-mdm-settings"></a>Configurações de Política de Grupo e do MDM
Use essas configurações da política de grupo e do MDM (gerenciamento de dispositivos móveis) somente em dispositivos corporativos, já que as políticas são aplicadas ao dispositivo inteiro do usuário. A aplicação de uma política MDM para desabilitar a sincronização de configurações para um dispositivo pessoal de propriedade do usuário afetará negativamente o uso do dispositivo. Além disso, outras contas de usuário no dispositivo também serão afetadas pela política.

As empresas que quiserem gerenciar o roaming para dispositivos pessoais (não gerenciados) poderão usar o portal do Azure para habilitar ou desabilitar o roaming, em vez de usar a Política de Grupo ou o MDM.
As tabelas a seguir descrevem as configurações de política disponíveis.

## <a name="mdm-settings"></a>Configurações do MDM
As configurações da  política de MDM se aplicam ao Windows 10 e ao Windows 10 Mobile.  Há suporte do Windows Mobile 10 somente para roaming baseado em conta da Microsoft por meio da conta do OneDrive do usuário.  Consulte a seção "Dispositivos e pontos de extremidade" para ver detalhes sobre quais dispositivos têm suporte para sincronização baseada no Azure AD.

| Nome | Descrição |
| --- | --- |
| Permitir Conexão da Conta da Microsoft |Permite que os usuários se autentiquem usando uma conta da Microsoft no dispositivo |
| Permitir Sincronizar Configurações |Permite aos usuários mover dados de aplicativo e configurações do Windows. Desabilitar esta política desabilitará a sincronização, bem como backups em dispositivos móveis |

## <a name="group-policy-settings"></a>Configurações de Política de Grupo
As configurações de Política de Grupo se aplicam a dispositivos Windows 10 ingressados em um domínio do Active Directory. A tabela também inclui as configurações herdadas que apareceriam para gerenciar as configurações de sincronização, mas que não funcionam para o Enterprise State Roaming para Windows 10 e estão indicadas com "Não usar" na descrição.

| Nome | Descrição |
| --- | --- |
| Contas: Bloquear Contas da Microsoft |Essa configuração de política impede que os usuários adicionem novas contas da Microsoft a este computador |
| Não sincronizar |Impede que os usuários movam dados de aplicativo e configurações do Windows |
| Não sincronizar personalização |Desabilita a sincronização do grupo Temas |
| Não sincronizar configurações do navegador |Desabilita a sincronização do grupo Internet Explorer |
| Não sincronizar senhas |Desabilita a sincronização do grupo Senhas |
| Não sincronizar outras configurações do Windows |Desabilita a sincronização do grupo Outras configurações do Windows |
| Não sincronizar personalização da área de trabalho |Não use; não tem nenhum efeito |
| Não sincronizar em conexões limitadas |Desabilita o roaming em conexões limitadas, como o 3G do celular |
| Não sincronizar aplicativos |Não use; não tem nenhum efeito |
| Não sincronizar configurações do aplicativo |Desabilita o roaming de dados do aplicativo |
| Não sincronizar configurações iniciais |Não use; não tem nenhum efeito |

## <a name="related-topics"></a>Tópicos relacionados
* [Visão geral do Enterprise State Roaming](active-directory-windows-enterprise-state-roaming-overview.md)
* [Habilitar o enterprise state roaming no Active Directory do Azure](active-directory-windows-enterprise-state-roaming-enable.md)
* [Perguntas frequentes sobre configurações e roaming de dados](active-directory-windows-enterprise-state-roaming-faqs.md)
* [Referência de configurações de roaming do Windows 10](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)
* [Solução de problemas](active-directory-windows-enterprise-state-roaming-troubleshooting.md)

