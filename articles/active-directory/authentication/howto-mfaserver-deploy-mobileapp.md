---
title: Serviço Web de aplicativo móvel do Azure MFA Server-Azure Active Directory
description: Configure o servidor MFA para enviar notificações por push para usuários com o Aplicativo Autenticador da Microsoft.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/11/2018
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6263491ce5b319c3968c542ebbaf00294c5152cd
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/06/2020
ms.locfileid: "96742418"
---
# <a name="enable-mobile-app-authentication-with-azure-multi-factor-authentication-server"></a>Habilitar a autenticação de aplicativo móvel com o Servidor de Autenticação Multifator do Azure

O aplicativo Microsoft Authenticator oferece uma opção de verificação adicional fora de banda. Em vez de fazer uma chamada telefônica automatizada ou SMS para o usuário durante o logon, a autenticação multifator do Azure envia uma notificação para o aplicativo Microsoft Authenticator no smartphone ou Tablet do usuário. O usuário simplesmente toca em **verificar** (ou insere um PIN e toca em "autenticar") no aplicativo para concluir sua entrada.

É preferível a utilização de um aplicativo móvel para a verificação em duas etapas quando a recepção do telefone não é confiável. Se você usar o aplicativo como um gerador de token OATH, ele não exigirá nenhuma conexão de rede ou Internet.

> [!IMPORTANT]
> A partir de 1º de julho de 2019, a Microsoft não oferece mais o servidor MFA para novas implantações. Novos clientes que desejam exigir a MFA (autenticação multifator) durante eventos de entrada devem usar a autenticação multifator do Azure AD baseada em nuvem.
>
> Para começar a usar a MFA baseada em nuvem, consulte [tutorial: proteger eventos de entrada de usuário com a autenticação multifator do Azure ad](tutorial-enable-azure-mfa.md).
>
> Os clientes existentes que ativaram o servidor MFA antes de 1º de julho de 2019 podem baixar a versão mais recente, atualizações futuras e gerar credenciais de ativação como de costume.

> [!IMPORTANT]
> Se você tiver instalado o Azure Multi-Factor Authentication Server v8.x ou superior, a maioria das etapas a seguir não será necessária. A autenticação do aplicativo móvel pode ser configurada seguindo as etapas em [Configurar o aplicativo móvel](#configure-the-mobile-app-settings-in-the-azure-multi-factor-authentication-server).

## <a name="requirements"></a>Requisitos

Para usar o aplicativo Microsoft Authenticator, você deve estar executando o servidor Azure Multi-Factor Authentication v8.x ou superior

## <a name="configure-the-mobile-app-settings-in-the-azure-multi-factor-authentication-server"></a>Configurar as definições do aplicativo móvel no Servidor de Autenticação Multifator do Azure

1. No console do Servidor de Autenticação Multifator, clique no ícone Portal do Usuário. Se os usuários têm permissão para controlar seus métodos de autenticação, marque **Aplicativo Móvel** na guia Configurações, em **Permitir que usuários selecionem o método**. Sem esse recurso habilitado, os usuários finais precisam entrar em contato com o Suporte Técnico para concluir a ativação para Aplicativos Móveis.
2. Marque a caixa **permitir que os usuários ativem o aplicativo móvel** .
3. Marque a caixa **Permitir registro de usuário** .
4. Clique no ícone **aplicativo móvel** .
5. Preencha o campo **Nome da conta** com o nome da empresa ou organização a ser exibido no aplicativo móvel para a conta.
   ![Configurações de Aplicativo Móvel de configuração do Servidor MFA](./media/howto-mfaserver-deploy-mobileapp/mobile.png)

## <a name="next-steps"></a>Próximas etapas

- [Cenários avançados com a autenticação multifator do Azure e VPNs de](howto-mfaserver-nps-vpn.md)terceiros.
