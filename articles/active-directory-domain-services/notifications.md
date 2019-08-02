---
title: 'Azure Active Directory Domain Services: Configurações de notificação | Microsoft Docs'
description: Configurações de notificação para o Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: b9af1792-0b7f-4f3e-827a-9426cdb33ba6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/20/2019
ms.author: iainfou
ms.openlocfilehash: a89b13d40f4eea08ecdb0f1eb8d68d1a146aca2b
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2019
ms.locfileid: "67472788"
---
# <a name="notification-settings-in-azure-ad-domain-services"></a>Configurações de notificação no Azure AD Domain Services

As notificações para o Azure AD Domain Services permitem que você receba uma atualização assim que um alerta de integridade for detectado em seu domínio gerenciado.  

Esse recurso só está disponível para domínios gerenciados que não estão em redes virtuais clássicas.


## <a name="how-to-check-your-azure-ad-domain-services-email-notification-settings"></a>Como verificar suas configurações de notificação por email do Azure AD Domain Services

1. Navegue até a página [Azure Active Directory Domain Services](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices) no portal do Azure
2. Escolha seu domínio gerenciado na tabela
3. No painel de navegação esquerdo, escolha **Configurações de notificação**

A página mostra todos os destinatários de email para as notificações por email do Azure AD Domain Services.

## <a name="what-does-an-email-notification-look-like"></a>Qual é a aparência de uma notificação por email?

A imagem a seguir contém um exemplo de uma notificação por email:

![Exemplo de notificação por email](./media/active-directory-domain-services-alerts/email-alert.png)

O email especifica o domínio gerenciado no qual o alerta está presente, além do horário de detecção e um link para a página de integridade do Azure AD Domain Services no Portal do Azure.

> [!WARNING]
> Verifique sempre se o remetente do email é verificado pela Microsoft, antes de clicar em links contidos em emails. Os emails sempre são provenientes do email azure-noreply@microsoft.com


## <a name="why-would-i-receive-email-notifications"></a>Por que eu receberia notificações por email?

O Azure AD Domain Services envia notificações por email para as atualizações importantes sobre o seu domínio.  Essas notificações servem apenas para assuntos urgentes que afetarão o seu serviço e devem ser resolvidas imediatamente. Cada notificação de email é disparada por um alerta em seu domínio gerenciado. Esses alertas também aparecerão no Portal do Azure e podem ser exibidos na [página de integridade do Azure AD Domain Services](check-health.md).

O Azure AD Domain Services não envia emails a essa lista com anúncios, atualizações ou vendas.

## <a name="when-will-i-receive-email-notifications"></a>Quando eu receberei notificações por email?

Uma notificação será enviada imediatamente quando um [novo alerta](troubleshoot-alerts.md) for encontrado em seu domínio gerenciado. Se o alerta não for resolvido, uma notificação por email será enviada como lembrete a cada quatro dias.

## <a name="who-should-receive-the-email-notifications"></a>Quem deve receber as notificações por email?


 Recomendamos que a lista de destinatários de email para o Azure AD Domain Services seja composta por pessoas capazes de administrar e fazer alterações no domínio gerenciado. Esta lista de emails deve ser considerada como os "primeiros socorristas" para qualquer problema encontrado. Se você quiser adicionar mais de cinco emails, recomendamos a criação de uma lista de distribuição para adicionar à lista de notificação.

É possível adicionar até cinco emails para notificações referentes ao Azure AD Domain Services. Além disso, você também pode optar por enviar as notificações por email do Azure AD Domain Services a todos os Administradores Globais do seu diretório e a todos os membros do grupo "Administradores do AAD DC". O Azure AD Domain Services enviará notificações somente para 100 endereços de email, incluindo a lista de administradores globais e administradores do AAD DC.


## <a name="how-to-add-an-additional-email-recipient"></a>Como adicionar outro destinatário de email

> [!WARNING]
> Ao alterar as configurações de notificação, você altera as configurações de notificação de todo o domínio gerenciado, não apenas a sua.

1. Navegue até a página [Azure AD Domain Services](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices) no Portal do Azure.
2. Clique no domínio gerenciado.
3. No painel de navegação esquerdo, clique em **Configurações de notificação**.
4. Para adicionar um email, digite o endereço de email na tabela de destinatários adicionais.
5. Clique em "Salvar" na barra de navegação superior.

## <a name="frequently-asked-questions"></a>Perguntas frequentes

#### <a name="i-received-an-email-notification-for-an-alert-but-when-i-logged-on-to-the-azure-portal-there-was-no-alert-what-happened"></a>Recebi uma notificação por email para um alerta, mas quando me conectei ao Portal do Azure não havia nenhum alerta. O que aconteceu?

Se um alerta for resolvido, o alerta desaparecerá do Portal do Azure. O motivo mais provável é que outra pessoa que recebe as notificações por email tenha resolvido o alerta em seu domínio gerenciado, ou ele foi resolvido automaticamente pelo Azure AD Domain Services.


#### <a name="why-can-i-not-edit-the-notification-settings"></a>Por que eu não posso editar as configurações de notificação?

Se você não conseguir acessar a página de configurações de notificação no Portal do Azure, você não terá as permissões para editar o Azure AD Domain Services. Entre em contato com seu administrador global para obter permissões de edição dos recursos do Azure AD Domain Services ou para ser removido da lista de destinatários.

#### <a name="i-dont-seem-to-be-receiving-email-notifications-even-though-i-provided-my-email-address-why"></a>Apesar de ter fornecido meu endereço de email, não estou recebendo as notificações por email. Por quê?

Verifique se a notificação está em sua pasta de spam ou lixo eletrônico e inclua o remetente na lista de permissões (azure-noreply@microsoft.com).

## <a name="next-steps"></a>Próximas etapas
- [Resolver alertas no domínio gerenciado](troubleshoot-alerts.md)
- [Ler mais sobre o Azure AD Domain Services](overview.md)
- [Entrar em contato com a equipe do produto](contact-us.md)

## <a name="contact-us"></a>Fale conosco
Entre em contato com a equipe de produto do Azure Active Directory Domain Services para [compartilhar comentários ou obter suporte](contact-us.md).
