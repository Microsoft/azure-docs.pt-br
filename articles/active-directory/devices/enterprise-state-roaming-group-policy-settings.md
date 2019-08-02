---
title: Configurações de Política de Grupo e do MDM | Microsoft Docs
description: Fornece informações sobre as configurações de política de grupo e do MDM (gerenciamento de dispositivos móveis) que devem ser usadas em dispositivos corporativos.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: na
ms.collection: M365-identity-device-management
ms.openlocfilehash: a3f2b1afa67ec36da4d4da57b296e696fd6c6910
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67481937"
---
# <a name="group-policy-and-mdm-settings"></a>Configurações de Política de Grupo e do MDM
Use essas configurações da política de grupo e do MDM (gerenciamento de dispositivos móveis) somente em dispositivos corporativos, já que as políticas são aplicadas ao dispositivo inteiro do usuário. A aplicação de uma política MDM para desabilitar a sincronização de configurações para um dispositivo pessoal de propriedade do usuário afetará negativamente o uso do dispositivo. Além disso, outras contas de usuário no dispositivo também serão afetadas pela política.

As empresas que quiserem gerenciar o roaming para dispositivos pessoais (não gerenciados) poderão usar o portal do Azure para habilitar ou desabilitar o roaming, em vez de usar a Política de Grupo ou o MDM.
As tabelas a seguir descrevem as configurações de política disponíveis.

## <a name="mdm-settings"></a>Configurações do MDM
As configurações da  política de MDM se aplicam ao Windows 10 e ao Windows 10 Mobile.  Há suporte do Windows Mobile 10 somente para roaming baseado em conta da Microsoft por meio da conta do OneDrive do usuário.  Veja a seção [Dispositivos e pontos de extremidade](enterprise-state-roaming-windows-settings-reference.md) para ver detalhes sobre quais dispositivos têm suporte para sincronização baseada no Azure AD.

| NOME | DESCRIÇÃO |
| --- | --- |
| Permitir Conexão da Conta da Microsoft |Permite que os usuários se autentiquem usando uma conta da Microsoft no dispositivo |
| Permitir Sincronizar Configurações |Permite aos usuários mover dados de aplicativo e configurações do Windows. Desabilitar esta política desabilitará a sincronização, bem como backups em dispositivos móveis |

## <a name="group-policy-settings"></a>Configurações de política de grupo
As configurações de Política de Grupo se aplicam a dispositivos Windows 10 ingressados em um domínio do Active Directory. A tabela também inclui as configurações herdadas que apareceriam para gerenciar as configurações de sincronização, mas que não funcionam para o Enterprise State Roaming para Windows 10 e estão indicadas com "Não usar" na descrição.

Essas configurações estão localizadas em: `Computer Configuration > Administrative Templates > Windows Components > Sync your settings` 

| NOME | DESCRIÇÃO |
| --- | --- |
| Contas: Bloquear contas da Microsoft |Essa configuração de política impede que os usuários adicionem novas contas da Microsoft a este computador |
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

Para obter uma visão geral, confira [visão geral do Enterprise State Roaming](enterprise-state-roaming-overview.md).


