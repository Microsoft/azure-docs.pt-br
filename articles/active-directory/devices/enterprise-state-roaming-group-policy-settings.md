---
title: Configurações de Política de Grupo e MDM para ESR-Azure Active Directory
description: Configurações de gerenciamento para Enterprise State Roaming
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: reference
ms.date: 02/12/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: na
ms.collection: M365-identity-device-management
ms.openlocfilehash: ab24b3113f9dc69b8f3907037e228ba212a03106
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85252926"
---
# <a name="group-policy-and-mdm-settings"></a>Configurações de Política de Grupo e do MDM

Use essas configurações da política de grupo e do MDM (gerenciamento de dispositivos móveis) somente em dispositivos corporativos, já que as políticas são aplicadas ao dispositivo inteiro do usuário. A aplicação de uma política MDM para desabilitar a sincronização de configurações para um dispositivo pessoal de propriedade do usuário afetará negativamente o uso do dispositivo. Além disso, outras contas de usuário no dispositivo também serão afetadas pela política.

As empresas que quiserem gerenciar o roaming para dispositivos pessoais (não gerenciados) poderão usar o portal do Azure para habilitar ou desabilitar o roaming, em vez de usar a Política de Grupo ou o MDM.
As tabelas a seguir descrevem as configurações de política disponíveis.

> [!NOTE]
> Este artigo se aplica ao navegador baseado em HTML herdado do Microsoft Edge, iniciado com o Windows 10 em julho de 2015. O artigo não se aplica ao novo navegador Microsoft Edge Chromium lançado em 15 de janeiro de 2020. Para obter mais informações sobre o comportamento de sincronização para o novo Microsoft Edge, consulte o artigo [sincronização do Microsoft Edge](/deployedge/microsoft-edge-enterprise-sync).

## <a name="mdm-settings"></a>Configurações do MDM

As configurações da  política de MDM se aplicam ao Windows 10 e ao Windows 10 Mobile.  Há suporte do Windows Mobile 10 somente para roaming baseado em conta da Microsoft por meio da conta do OneDrive do usuário. Consulte [dispositivos e pontos de extremidade](enterprise-state-roaming-windows-settings-reference.md) para obter detalhes sobre quais dispositivos têm suporte para a sincronização baseada no Azure AD.

| Nome | Descrição |
| --- | --- |
| Permitir Conexão da Conta da Microsoft |Permite que os usuários se autentiquem usando uma conta da Microsoft no dispositivo |
| Permitir Sincronizar Configurações |Permite aos usuários mover dados de aplicativo e configurações do Windows. Desabilitar esta política desabilitará a sincronização, bem como backups em dispositivos móveis |

## <a name="group-policy-settings"></a>Configurações de política de grupo

As configurações de Política de Grupo se aplicam a dispositivos Windows 10 ingressados em um domínio do Active Directory. A tabela também inclui as configurações herdadas que apareceriam para gerenciar as configurações de sincronização, mas que não funcionam para o Enterprise State Roaming para Windows 10 e estão indicadas com "Não usar" na descrição.

Essas configurações estão localizadas em: `Computer Configuration > Administrative Templates > Windows Components > Sync your settings` 

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

## <a name="next-steps"></a>Próximas etapas

Para obter uma visão geral, consulte [visão geral do Enterprise State roaming](enterprise-state-roaming-overview.md).
