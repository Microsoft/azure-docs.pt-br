---
title: Configurar o ingresso no Azure Active Directory híbrido para domínios gerenciados | Microsoft Docs
description: Saiba como configurar o ingresso no Azure Active Directory híbrido para domínios gerenciados.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: tutorial
ms.date: 05/14/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: c7781651536275eba60bfde49e00a450dde6d3e1
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68357034"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-join-for-managed-domains"></a>Tutorial: Configurar o ingresso no Azure Active Directory híbrido para os domínios gerenciados

Como um usuário na sua organização, um dispositivo é uma identidade importante que você quer proteger. É possível usar uma identidade do dispositivo para proteger seus recursos a qualquer momento e de qualquer local. É possível atingir esse objetivo colocando e gerenciando identidades de dispositivo no Azure Active Directory (Azure AD) seguindo um dos seguintes métodos:

- Ingresso no Azure AD
- Ingresso no Azure AD Híbrido
- Registro do Azure AD

Colocar os dispositivos no Azure AD maximiza a produtividade do usuário por meio de SSO (logon único) em recursos locais e na nuvem. Você pode proteger o acesso aos recursos locais e na nuvem com [Acesso Condicional](../active-directory-conditional-access-azure-portal.md) ao mesmo tempo.

Neste tutorial, você aprenderá a configurar o ingresso no Azure Active Directory híbrido para dispositivos de computadores unidos ao domínio do Active Directory em um ambiente gerenciado. 

Um ambiente gerenciado pode ser implantado por meio da [PHS (sincronização de hash de senha)](../hybrid/whatis-phs.md) ou [PTA (autenticação de passagem)](../hybrid/how-to-connect-pta.md) com [logon único contínuo](../hybrid/how-to-connect-sso.md). Esses cenários não exigem que você configure um servidor de federação para autenticação.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Configurar ingresso no Azure AD híbrido
> * Habilitar dispositivos de nível inferior do Windows
> * Verificar dispositivos ingressados
> * Solucionar problemas

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial presume que você esteja familiarizado com estes artigos:

- [O que é uma identidade do dispositivo?](overview.md)
- [Como planejar sua implementação de ingresso no Azure AD híbrido](hybrid-azuread-join-plan.md)
- [Como realizar a validação controlada de ingresso no Azure AD híbrido](hybrid-azuread-join-control.md)

> [!NOTE]
> O Azure Active Directory não dá suporte a cartões inteligentes ou certificados em domínios gerenciados.

Para configurar o cenário neste artigo, é necessário que a [última versão do Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) (1.1.819.0 ou posterior) esteja instalada.

Verifique se o Azure AD Connect sincronizou os objetos de computador dos dispositivos que você quer que sejam unidos ao Azure AD híbrido com o Azure AD. Se os objetos de computador pertencerem a unidades organizacionais (OUs) específicas, você também deverá configurar as OUs para sincronizarem no Azure AD Connect. Para saber mais sobre como sincronizar objetos de computador usando o Azure AD Connect, confira [Configurar filtragem usando o Azure AD Connect](../hybrid/how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering).

A partir da versão 1.1.819.0, o Azure AD Connect inclui um assistente que você pode usar para configurar o ingresso no Azure AD híbrido. O assistente simplifica significativamente o processo de configuração. O assistente configura os SCPs (pontos de conexão do serviço) para registro do dispositivo.

As etapas de configuração neste artigo se baseiam no uso do assistente no Azure AD Connect.

O ingresso no Azure AD híbrido requer que os dispositivos tenham acesso aos seguintes recursos da Microsoft dentro da rede da organização:  

- `https://enterpriseregistration.windows.net`
- `https://login.microsoftonline.com`
- `https://device.login.microsoftonline.com`
- `https://autologon.microsoftazuread-sso.com` (se você usa ou planeja usar o SSO contínuo)

Se sua organização exigir acesso à Internet por meio de um proxy de saída, a Microsoft recomendará [implementar a WPAD (Descoberta Automática de Proxy Web)](https://docs.microsoft.com/previous-versions/tn-archive/cc995261(v%3dtechnet.10)) de modo a permitir computadores Windows 10 para registro do dispositivo com o Azure AD. Se você encontrar problemas para configurar e gerenciar a WPAD, confira [Solucionar problemas de detecção automática](https://docs.microsoft.com/previous-versions/tn-archive/cc302643(v=technet.10)). 

Se você não usar a WPAD e precisar configurar as definições de proxy no computador, faça isso começando com o Windows 10 1709. Para saber mais, confira [Definir as configurações de WinHTTP usando um GPO (Objeto de Política de Grupo)](https://blogs.technet.microsoft.com/netgeeks/2018/06/19/winhttp-proxy-settings-deployed-by-gpo/).

> [!NOTE]
> Se você definir configurações de proxy em seu computador usando as configurações de WinHTTP, qualquer computador que não possa se conectar ao proxy configurado falhará ao se conectar à Internet.

Se a organização exigir acesso à Internet por meio de um proxy de saída autenticado, será preciso garantir que os computadores com Windows 10 possam ser autenticados com êxito no proxy de saída. Como computadores com Windows 10 executam o registro de dispositivos usando o contexto do computador, será preciso configurar a autenticação de proxy de saída usando o contexto do computador. Acompanhe com o provedor de proxy de saída nos requisitos de configuração.

## <a name="configure-hybrid-azure-ad-join"></a>Configurar ingresso no Azure AD híbrido

Para configurar um ingresso no Azure AD híbrido usando o Azure AD Connect, será necessário ter:

- As credenciais de um administrador global para o locatário do Azure AD
- As credenciais de administrador corporativo para cada uma das florestas

**Para configurar um ingresso no Azure AD híbrido usando o Azure AD Connect:**

1. Inicie o Azure AD Connect e selecione **Configurar**.

   ![Bem-Vindo](./media/hybrid-azuread-join-managed-domains/11.png)

1. Na página **Tarefas adicionais**, selecione **Configurar opções de dispositivo** e, em seguida, **Avançar**.

   ![Tarefas adicionais](./media/hybrid-azuread-join-managed-domains/12.png)

1. Na página **Visão geral**, selecione **Avançar**.

   ![Visão geral](./media/hybrid-azuread-join-managed-domains/13.png)

1. Na página **Conectar o Azure AD**, insira as credenciais de um administrador global para o locatário do Azure AD.  

   ![Conecte-se ao AD do Azure](./media/hybrid-azuread-join-managed-domains/14.png)

1. Na página **Opções do dispositivo**, selecione **Configurar ingresso no Azure AD Híbrido** e, em seguida,selecione **Avançar**.

   ![Opções do dispositivo](./media/hybrid-azuread-join-managed-domains/15.png)

1. Na página **SCP**, de cada floresta em que você deseja que o Azure AD Connect configure o SCP, conclua as seguintes etapas e selecione **Avançar**:

   ![SCP](./media/hybrid-azuread-join-managed-domains/16.png)

   1. Selecione a floresta.
   1. Selecione o serviço de autenticação.
   1. Selecione **Adicionar** para inserir as credenciais do administrador corporativo.

1. Na página **Sistemas operacionais do dispositivo**, selecione os sistemas operacionais que os dispositivos no ambiente do Active Directory usam e selecione **Avançar**.

   ![Sistema operacional do dispositivo](./media/hybrid-azuread-join-managed-domains/17.png)

1. Na página **Pronto para configurar**, selecione **Configurar**.

   ![Pronto para configurar](./media/hybrid-azuread-join-managed-domains/19.png)

1. Na página **Configuração completa** selecione **Sair**.

   ![Configuração concluída](./media/hybrid-azuread-join-managed-domains/20.png)

## <a name="enable-windows-downlevel-devices"></a>Habilitar dispositivos de nível inferior do Windows

Se alguns dos seus dispositivos ingressados ao domínio forem dispositivos de nível inferior do Windows, será preciso:

- Definir as configurações de Intranet Local para registro do dispositivo
- Configurar o SSO contínuo
- Instalar o Workplace Join da Microsoft para computadores Windows de nível inferior

### <a name="configure-the-local-intranet-settings-for-device-registration"></a>Definir as configurações de Intranet Local para registro do dispositivo

Para concluir com êxito o ingresso dos dispositivos de nível inferior do Windows no Azure AD híbrido e evitar prompts de certificado quando os dispositivos autenticarem no Azure AD, envie uma política por push aos dispositivos ingressados no domínio para adicionar as seguintes URLs à zona da Intranet Local no Internet Explorer:

- `https://device.login.microsoftonline.com`
- `https://autologon.microsoftazuread-sso.com`

Também será preciso habilitar **Permitir atualizações da barra de status via script** na zona da intranet local do usuário.

### <a name="configure-seamless-sso"></a>Configurar o SSO contínuo

Para concluir o ingresso no Azure AD híbrido de seus dispositivos de nível inferior do Windows em um domínio gerenciado que usa [PHS](../hybrid/whatis-phs.md) ou [PTA](../hybrid/how-to-connect-pta.md) como método de autenticação em nuvem do Azure AD, você também precisa [configurar o SSO contínuo](../hybrid/how-to-connect-sso-quick-start.md#step-2-enable-the-feature).

### <a name="install-microsoft-workplace-join-for-windows-downlevel-computers"></a>Instalar o Workplace Join da Microsoft para computadores Windows de nível inferior

Para registrar dispositivos de nível inferior do Windows, as organizações devem instalar o [Workplace Join da Microsoft nos computadores sem Windows 10](https://www.microsoft.com/download/details.aspx?id=53554). O Workplace Join da Microsoft para computadores sem Windows 10 está disponível no Centro de Download da Microsoft.

É possível implantar o pacote usando um sistema de distribuição de software como o [System Center Configuration Manager](https://www.microsoft.com/cloud-platform/system-center-configuration-manager). O pacote dá suporte às opções de instalação silenciosa padrão com o parâmetro `quiet`. O atual branch do Configuration Manager oferece benefícios adicionais em relação às versões anteriores, como a capacidade de rastrear registros concluídos.

O instalador cria uma tarefa agendada no sistema que é executada no contexto do usuário. A tarefa é disparada quando o usuário entra no Windows. A tarefa une silenciosamente o dispositivo com o Azure AD usando as credenciais do usuário depois que ele se autentica com o Azure AD.

## <a name="verify-the-registration"></a>Verificar o registro

Para verificar o estado do registro do dispositivo no locatário do Azure, é possível usar o cmdlet **[Get-MsolDevice](/powershell/msonline/v1/get-msoldevice)** no [módulo do PowerShell do Azure Active Directory](/powershell/azure/install-msonlinev1?view=azureadps-2.0).

Ao usar o cmdlet **Get-MSolDevice** para verificar os detalhes do serviço:

- É necessário que haja um objeto com a **identificação do dispositivo** correspondente à ID no cliente do Windows.
- O valor para **DeviceTrustType** deverá ser **Ingressado no Domínio**. Essa configuração equivale ao estado **ingressou no Azure AD híbrido** na página **Dispositivos** no portal do Azure AD.
- Para dispositivos que são usados em Acesso Condicional, o valor para **Enabled** deve ser **True** e **DeviceTrustLevel** deve ser **Managed**.

**Para verificar os detalhes do serviço**:

1. Abra o Windows PowerShell como administrador.
1. Digite `Connect-MsolService` para se conectar ao locatário do Azure.  
1. Digite `get-msoldevice -deviceId <deviceId>`.
1. Verifique se **Habilitado** está definido como **Verdadeiro**.

## <a name="troubleshoot-your-implementation"></a>Solucionar problemas de implementação

Se estiver com problemas para concluir o ingresso no Azure AD híbrido de dispositivos Windows unidos ao domínio, confira:

- [Solucionar problemas de ingresso no Azure AD híbrido para dispositivos atuais do Windows](troubleshoot-hybrid-join-windows-current.md)
- [Solucionar problemas de ingresso no Azure AD híbrido para dispositivos de nível inferior do Windows](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>Próximas etapas

Saiba como [gerenciar identidades de dispositivo usando o portal do Azure](device-management-azure-portal.md).
