---
title: Configurar o ingresso no Azure Active Directory híbrido para os domínios federados | Microsoft Docs
description: Saiba como configurar o ingresso no Azure Active Directory híbrido para domínios federados.
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
ms.openlocfilehash: 738b4f47054081f0fb1b1a530bdf21cbf07a7726
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67204706"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-join-for-federated-domains"></a>Tutorial: Configurar o ingresso no Azure Active Directory híbrido para os domínios federados

Como um usuário na sua organização, um dispositivo é uma identidade importante que você quer proteger. É possível usar uma identidade do dispositivo para proteger seus recursos a qualquer momento e de qualquer local. É possível atingir esse objetivo colocando e gerenciando identidades de dispositivo no Azure Active Directory (Azure AD) seguindo um dos seguintes métodos:

- Ingresso no Azure AD
- Ingresso no Azure AD Híbrido
- Registro do Azure AD

Colocar os dispositivos no Azure AD maximiza a produtividade do usuário por meio do logon único (SSO) em recursos locais e na nuvem. Você pode proteger o acesso aos recursos locais e na nuvem com o [Acesso Condicional](../active-directory-conditional-access-azure-portal.md) ao mesmo tempo.

Neste tutorial, você aprenderá a configurar o ingresso, no Azure AD híbrido, de dispositivos de computadores unidos ao domínio do Active Directory em um ambiente federado, usando os Serviços de Federação do Active Directory (AD FS).

> [!NOTE]
> Se seu ambiente federado usa um provedor de identidade diferente do AD FS, é preciso verificar se esse provedor dá suporte protocolo WS-Trust. O WS-Trust é um requisito para autenticar seus dispositivos ingressados no Azure AD híbrido atuais do Windows com o Azure AD. Se você tiver dispositivos de nível inferior do Windows que precise ingressar no Azure AD híbrido, seu provedor de identidade precisará dar suporte à declaração WIAORMULTIAUTHN. 

Você aprenderá como:

> [!div class="checklist"]
> * Configurar ingresso no Azure AD híbrido
> * Habilitar dispositivos de nível inferior do Windows
> * Verificar o registro
> * Solucionar problemas

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial presume que você esteja familiarizado com estes artigos:

- [O que é uma identidade do dispositivo?](overview.md)
- [Como planejar sua implementação de ingresso no Azure AD híbrido](hybrid-azuread-join-plan.md)
- [Como realizar a validação controlada de ingresso no Azure AD híbrido](hybrid-azuread-join-control.md)

Para configurar o cenário neste tutorial, é necessário ter:

- Windows Server 2012 R2 com AD FS
- [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) versão 1.1.819.0 ou superior

A partir da versão 1.1.819.0, o Azure AD Connect inclui um assistente que você pode usar para configurar o ingresso no Azure AD híbrido. O assistente simplifica significativamente o processo de configuração. O assistente relacionado:

- Configura os pontos de conexão de serviço (SCPs) para registro do dispositivo
- Faz backup da terceira parte confiável do Azure AD
- Atualiza as regras de declaração na confiança do Azure AD

As etapas de configuração neste artigo se baseiam no uso do assistente do Azure AD Connect. Se você tiver uma versão mais antiga do Azure AD Connect instalada, será preciso atualizá-la para a versão 1.1.819 ou superior. Se a instalação da versão mais recente do Azure AD Connect não for uma opção para você, confira [como configurar manualmente o ingresso no Azure AD híbrido](hybrid-azuread-join-manual.md).

O ingresso no Azure AD híbrido requer que os dispositivos tenham acesso aos seguintes recursos da Microsoft dentro da rede da organização:  

- `https://enterpriseregistration.windows.net`
- `https://login.microsoftonline.com`
- `https://device.login.microsoftonline.com`
- Serviço de Token de Segurança da organização (STS) (para domínios federados)
- `https://autologon.microsoftazuread-sso.com` (se você usa ou planeja usar o SSO contínuo)

A partir do Windows 10 1803, se o ingresso instantâneo no Azure AD híbrido para o ambiente federado do AD FS falhar, dependeremos do Azure AD Connect para sincronizar o objeto de computador no Azure AD que será usado posteriormente para concluir o registro do dispositivo para ingresso no Azure AD híbrido. Verifique se o Azure AD Connect sincronizou os objetos de computador dos dispositivos que você quer que ingressem no Azure AD híbrido com o Azure AD. Se os objetos de computador pertencerem a unidades organizacionais (OUs) específicas, você também deverá configurar as OUs para sincronizarem no Azure AD Connect. Para saber mais sobre como sincronizar objetos de computador usando o Azure AD Connect, veja como [configurar a filtragem usando o Azure AD Connect](../hybrid/how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering).

Se sua organização exige acesso à Internet por meio de um proxy de saída, a Microsoft recomenda [implementar a Descoberta Automática de Proxy Web (WPAD)](https://docs.microsoft.com/previous-versions/tn-archive/cc995261(v%3dtechnet.10)) de modo a permitir computadores Windows 10 para registro do dispositivo com o Azure AD. Se você encontrar problemas para configurar e gerenciar a WPAD, veja como [solucionar problemas de detecção automática](https://docs.microsoft.com/previous-versions/tn-archive/cc302643(v=technet.10)). 

Se você não usa a WPAD e precisa configurar as definições de proxy no computador, é possível fazer isso a partir do Windows 10 1709. Para obter mais informações, veja como [definir as configurações de WinHTTP usando um Objeto de Política de Grupo (GPO)](https://blogs.technet.microsoft.com/netgeeks/2018/06/19/winhttp-proxy-settings-deployed-by-gpo/).

> [!NOTE]
> Se você definir as configurações de proxy em seu computador usando as configurações de WinHTTP, qualquer computador que não possa se conectar ao proxy configurado não conseguirá se conectar à Internet.

Se a organização exigir acesso à Internet por meio de um proxy de saída autenticado, será preciso garantir que os computadores com Windows 10 possam ser autenticados com êxito no proxy de saída. Como computadores com Windows 10 executam o registro de dispositivos usando o contexto do computador, será preciso configurar a autenticação de proxy de saída usando o contexto do computador. Acompanhe com o provedor de proxy de saída nos requisitos de configuração.

## <a name="configure-hybrid-azure-ad-join"></a>Configurar ingresso no Azure AD híbrido

Para configurar um ingresso no Azure AD híbrido usando o Azure AD Connect, será necessário ter:

- Credenciais de administrador global para o locatário do Azure AD  
- Credenciais de administrador corporativo para cada uma das florestas
- Credenciais do administrador do AD FS

**Para configurar o ingresso no Azure AD híbrido usando o Azure AD Connect:**

1. Inicie o Azure AD Connect e selecione **Configurar**.

   ![Bem-Vindo](./media/hybrid-azuread-join-federated-domains/11.png)

1. Na página **Tarefas adicionais**, selecione **Configurar opções de dispositivo** e, em seguida, **Avançar**.

   ![Tarefas adicionais](./media/hybrid-azuread-join-federated-domains/12.png)

1. Na página **Visão geral**, selecione **Avançar**.

   ![Visão geral](./media/hybrid-azuread-join-federated-domains/13.png)

1. Na página **Conectar o Azure AD**, insira as credenciais de administrador global para o locatário do Azure AD e selecione **Avançar**.

   ![Conecte-se ao AD do Azure](./media/hybrid-azuread-join-federated-domains/14.png)

1. Na página **Opções do dispositivo**, selecione **Configurar o ingresso ao Azure AD híbrido** e selecione **Avançar**.

   ![Opções do dispositivo](./media/hybrid-azuread-join-federated-domains/15.png)

1. Na página **SCP**, conclua as etapas a seguir e, em seguida, selecione **Avançar**:

   ![SCP](./media/hybrid-azuread-join-federated-domains/16.png)

   1. Selecione a floresta.
   1. Selecione o serviço de autenticação. Você deve selecionar o servidor do **AD FS**, a menos que sua organização tenha exclusivamente clientes Windows 10 e você tenha configurado a sincronização do computador/dispositivo ou sua organização use o SSO contínuo.
   1. Selecione **Adicionar** para inserir as credenciais de administrador corporativo.

1. Na página **Sistemas operacionais do dispositivo**, selecione os sistemas operacionais que os dispositivos no ambiente do Active Directory usam e selecione **Avançar**.

   ![Sistema operacional do dispositivo](./media/hybrid-azuread-join-federated-domains/17.png)

1. Na página **Configuração de federação**, insira as credenciais do administrador do AD FS e selecione **Avançar**.

   ![Configuração de federação](./media/hybrid-azuread-join-federated-domains/18.png)

1. Na página **Pronto para configurar**, selecione **Configurar**.

   ![Pronto para configurar](./media/hybrid-azuread-join-federated-domains/19.png)

1. Na página **Configuração completa** selecione **Sair**.

   ![Configuração concluída](./media/hybrid-azuread-join-federated-domains/20.png)

## <a name="enable-windows-downlevel-devices"></a>Habilitar dispositivos de nível inferior do Windows

Se alguns dos seus dispositivos ingressados ao domínio forem dispositivos de nível inferior do Windows, será preciso:

- Definir as configurações de Intranet Local para registro do dispositivo
- Instalar o Workplace Join da Microsoft para computadores Windows de nível inferior

### <a name="configure-the-local-intranet-settings-for-device-registration"></a>Definir as configurações de Intranet Local para registro do dispositivo

Para concluir com êxito o ingresso dos dispositivos de nível inferior do Windows no Azure AD híbrido e evitar prompts de certificado quando os dispositivos autenticarem no Azure AD, envie uma política por push aos dispositivos ingressados no domínio para adicionar as seguintes URLs à zona da Intranet Local no Internet Explorer:

- `https://device.login.microsoftonline.com`
- STS de sua organização (para domínios federados)
- `https://autologon.microsoftazuread-sso.com` (para SSO Contínuo)

Também será preciso habilitar **Permitir atualizações da barra de status via script** na zona da intranet local do usuário.

### <a name="install-microsoft-workplace-join-for-windows-downlevel-computers"></a>Instalar o Workplace Join da Microsoft para computadores Windows de nível inferior

Para registrar dispositivos de nível inferior do Windows, as organizações devem instalar o [Workplace Join da Microsoft nos computadores sem Windows 10](https://www.microsoft.com/download/details.aspx?id=53554). O Workplace Join da Microsoft para computadores sem Windows 10 está disponível no Centro de Download da Microsoft.

É possível implantar o pacote usando um sistema de distribuição de software como o [System Center Configuration Manager](https://www.microsoft.com/cloud-platform/system-center-configuration-manager). O pacote dá suporte às opções de instalação silenciosa padrão com o parâmetro `quiet`. O atual branch do Configuration Manager oferece benefícios adicionais em relação às versões anteriores, como a capacidade de rastrear registros concluídos.

O instalador cria uma tarefa agendada no sistema que é executada no contexto do usuário. A tarefa é disparada quando o usuário entra no Windows. A tarefa ingressa silenciosamente o dispositivo com o Azure AD usando as credenciais do usuário depois que ele é autenticado com o Azure AD.

## <a name="verify-the-registration"></a>Verificar o registro

Para verificar o estado do registro do dispositivo no locatário do Azure, é possível usar o cmdlet **[Get-MsolDevice](/powershell/msonline/v1/get-msoldevice)** no [módulo do PowerShell do Azure Active Directory](/powershell/azure/install-msonlinev1?view=azureadps-2.0).

Ao usar o cmdlet **Get-MSolDevice** para verificar os detalhes do serviço:

- É necessário que haja um objeto com a **identificação do dispositivo** correspondente à ID no cliente do Windows.
- O valor para **DeviceTrustType** deverá ser **Ingressado no Domínio**. Essa configuração equivale ao estado **ingressou no Azure AD híbrido** na página **Dispositivos** no portal do Azure AD.
- Nos dispositivos que são usados em Acesso Condicional, o valor para **Enabled** deve ser **True** e **DeviceTrustLevel** deve ser **Managed**.

**Para verificar os detalhes do serviço**:

1. Abra o Windows PowerShell como administrador.
1. Digite `Connect-MsolService` para se conectar ao locatário do Azure.  
1. Digite `get-msoldevice -deviceId <deviceId>`.
1. Verifique se **Habilitado** está definido como **Verdadeiro**.

## <a name="troubleshoot-your-implementation"></a>Solucionar problemas de implementação

Se tiver problemas para concluir o ingresso de dispositivos Windows unidos ao domínio no Azure AD híbrido, confira:

- [Solucionar problemas de ingresso no Azure AD híbrido para dispositivos atuais do Windows](troubleshoot-hybrid-join-windows-current.md)
- [Solucionar problemas de ingresso no Azure AD híbrido para dispositivos de nível inferior do Windows](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>Próximas etapas

Saiba como [gerenciar identidades de dispositivo usando o portal do Azure](device-management-azure-portal.md).

<!--Image references-->
[1]: ./media/active-directory-conditional-access-automatic-device-registration-setup/12.png
