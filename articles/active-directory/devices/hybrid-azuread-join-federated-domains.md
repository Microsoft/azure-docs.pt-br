---
title: Configurar o ingresso no Azure Active Directory híbrido para os domínios federados | Microsoft Docs
description: Saiba como configurar o ingresso no Azure Active Directory híbrido para domínios federados.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: tutorial
ms.date: 05/20/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 221b7bdbb8ab5d0121e9c8032be8f18d8ae60d1e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104578049"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-join-for-federated-domains"></a>Tutorial: Configurar o ingresso no Azure Active Directory híbrido para os domínios federados

Como um usuário na sua organização, um dispositivo é uma identidade importante que você quer proteger. É possível usar uma identidade do dispositivo para proteger seus recursos a qualquer momento e de qualquer local. É possível atingir esse objetivo colocando e gerenciando identidades de dispositivo no Azure Active Directory (Azure AD) seguindo um dos seguintes métodos:

- Ingresso no Azure AD
- Ingresso no Azure AD Híbrido
- Registro do Azure AD

Colocar os dispositivos no Azure AD maximiza a produtividade do usuário por meio de SSO (logon único) em recursos locais e na nuvem. Você pode proteger o acesso aos recursos locais e na nuvem com o [Acesso Condicional](../conditional-access/howto-conditional-access-policy-compliant-device.md) ao mesmo tempo.

Um ambiente federado deve ter um provedor de identidade que dá suporte aos requisitos a seguir. Se você tem um ambiente federado usando o AD FS (Serviços de Federação do Active Directory), os requisitos abaixo já são compatíveis.

- **Declaração WIAORMULTIAUTHN:** Essa declaração é necessária para fazer o ingresso de dispositivos de nível inferior do Windows no Azure AD híbrido.
- **Protocolo WS-Trust:** Esse protocolo é um requisito para autenticar os dispositivos atuais do Windows ingressados no Azure AD híbrido com o Azure AD.
  Quando você estiver usando o AD FS, será necessário habilitar os seguintes pontos de extremidade WS-Trust: `/adfs/services/trust/2005/windowstransport`
   `/adfs/services/trust/13/windowstransport`
   `/adfs/services/trust/2005/usernamemixed`
   `/adfs/services/trust/13/usernamemixed`
   `/adfs/services/trust/2005/certificatemixed`
   `/adfs/services/trust/13/certificatemixed` 

> [!WARNING] 
> O **adfs/services/trust/2005/windowstransport** e o **adfs/services/trust/13/windowstransport** devem ser habilitados como pontos de extremidade voltados para a intranet e NÃO devem ser expostos como pontos de extremidade voltados a uma extranet por meio do proxy de aplicativo Web. Para saber mais sobre como desabilitar os pontos de extremidade do Windows do WS-Trust, confira [Desabilitar pontos de extremidade do Windows do WS-Trust no proxy](/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs#disable-ws-trust-windows-endpoints-on-the-proxy-ie-from-extranet). Veja quais pontos de extremidade estão habilitados por meio do console de gerenciamento do AD FS em **Serviço** > **Pontos de extremidade**.

Neste tutorial, você aprenderá como configurar o ingresso no Azure AD híbrido para dispositivos de computadores ingressados no domínio do Active Directory em um ambiente federado, usando o AD FS.

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

> [!WARNING]
> Se a sua organização usa servidores proxy que interceptam o tráfego SSL para cenários como prevenção de perda de dados ou restrições de locatário do Azure AD, verifique se o tráfego para 'https://device.login.microsoftonline.com ' foi excluído do TLS de interrupção e inspeção. A falha ao excluir o 'https://device.login.microsoftonline.com ' pode causar interferência com a autenticação de certificado de cliente, causando problemas com o registro de dispositivo e o Acesso Condicional com base no dispositivo.

A partir do Windows 10 1803, se o ingresso instantâneo no Azure AD híbrido para o ambiente federado do AD FS falhar, dependeremos do Azure AD Connect para sincronizar o objeto de computador no Azure AD que será usado posteriormente para concluir o registro do dispositivo para ingresso no Azure AD híbrido. Verifique se o Azure AD Connect sincronizou os objetos de computador dos dispositivos que você quer que sejam unidos ao Azure AD híbrido com o Azure AD. Se os objetos de computador pertencerem a unidades organizacionais (OUs) específicas, você também deverá configurar as OUs para sincronizarem no Azure AD Connect. Para saber mais sobre como sincronizar objetos de computador usando o Azure AD Connect, veja como [configurar a filtragem usando o Azure AD Connect](../hybrid/how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering).

> [!NOTE]
> Para que a junção de sincronização de registro de dispositivo tenha sucesso, como parte da configuração de registro de dispositivo, não exclua os atributos de dispositivo padrão da configuração de sincronização do Azure AD Connect. Para saber mais sobre os atributos de dispositivo padrão sincronizados com o AAD, confira [Atributos sincronizados pelo Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-sync-attributes-synchronized#windows-10).

Se sua organização exigir acesso à Internet por meio de um proxy de saída, a Microsoft recomendará [implementar a WPAD (Descoberta Automática de Proxy Web)](/previous-versions/tn-archive/cc995261(v%3dtechnet.10)) de modo a permitir computadores Windows 10 para registro do dispositivo com o Azure AD. Se você encontrar problemas para configurar e gerenciar a WPAD, veja como [solucionar problemas de detecção automática](/previous-versions/tn-archive/cc302643(v=technet.10)). 

Se você não usa a WPAD e precisa configurar as definições de proxy no computador, é possível fazer isso a partir do Windows 10 1709. Para obter mais informações, veja como [definir as configurações de WinHTTP usando um Objeto de Política de Grupo (GPO)](/archive/blogs/netgeeks/winhttp-proxy-settings-deployed-by-gpo).

> [!NOTE]
> Se você definir as configurações de proxy em seu computador usando as configurações de WinHTTP, qualquer computador que não possa se conectar ao proxy configurado não conseguirá se conectar à Internet.

Se a organização exigir acesso à Internet por meio de um proxy de saída autenticado, será preciso garantir que os computadores com Windows 10 possam ser autenticados com êxito no proxy de saída. Como computadores com Windows 10 executam o registro de dispositivos usando o contexto do computador, será preciso configurar a autenticação de proxy de saída usando o contexto do computador. Acompanhe com o provedor de proxy de saída nos requisitos de configuração.

Para verificar se o dispositivo é capaz de acessar os recursos da Microsoft mencionados acima na conta do sistema, você pode usar o script de [conectividade de registro de dispositivo de teste](/samples/azure-samples/testdeviceregconnectivity/testdeviceregconnectivity/).

## <a name="configure-hybrid-azure-ad-join"></a>Configurar ingresso no Azure AD híbrido

Para configurar um ingresso no Azure AD híbrido usando o Azure AD Connect, será necessário ter:

- As credenciais de um administrador global para o locatário do Azure AD  
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

1. Na página **Opções do dispositivo**, selecione **Configurar ingresso no Azure AD Híbrido** e, em seguida,selecione **Avançar**.

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

> [!NOTE]
> O suporte do Windows 7 foi encerrado em 14 de janeiro de 2020. Para obter mais informações, confira [O suporte do Windows 7 foi encerrado](https://support.microsoft.com/en-us/help/4057281/windows-7-support-ended-on-january-14-2020).

### <a name="configure-the-local-intranet-settings-for-device-registration"></a>Definir as configurações de Intranet Local para registro do dispositivo

Para concluir com êxito o ingresso dos dispositivos de nível inferior do Windows no Azure AD híbrido e evitar prompts de certificado quando os dispositivos autenticarem no Azure AD, envie uma política por push aos dispositivos ingressados no domínio para adicionar as seguintes URLs à zona da Intranet Local no Internet Explorer:

- `https://device.login.microsoftonline.com`
- STS de sua organização (para domínios federados)
- `https://autologon.microsoftazuread-sso.com` (para SSO Contínuo)

Também será preciso habilitar **Permitir atualizações da barra de status via script** na zona da intranet local do usuário.

### <a name="install-microsoft-workplace-join-for-windows-downlevel-computers"></a>Instalar o Workplace Join da Microsoft para computadores Windows de nível inferior

Para registrar dispositivos de nível inferior do Windows, as organizações devem instalar o [Workplace Join da Microsoft nos computadores sem Windows 10](https://www.microsoft.com/download/details.aspx?id=53554). O Workplace Join da Microsoft para computadores sem Windows 10 está disponível no Centro de Download da Microsoft.

É possível implantar o pacote usando um sistema de distribuição de software como o  [Microsoft Endpoint Configuration Manager](/configmgr/). O pacote dá suporte às opções de instalação silenciosa padrão com o parâmetro `quiet`. O atual branch do Configuration Manager oferece benefícios adicionais em relação às versões anteriores, como a capacidade de rastrear registros concluídos.

O instalador cria uma tarefa agendada no sistema que é executada no contexto do usuário. A tarefa é disparada quando o usuário entra no Windows. A tarefa une silenciosamente o dispositivo com o Azure AD usando as credenciais do usuário depois que ele se autentica com o Azure AD.

## <a name="verify-the-registration"></a>Verificar o registro

Aqui estão três maneiras de localizar e verificar o estado do dispositivo:

### <a name="locally-on-the-device"></a>Localmente no dispositivo

1. Abra o Windows PowerShell.
2. Digite `dsregcmd /status`.
3. Verifique se **AzureAdJoined** e **DomainJoined** estão definidos como **Sim**.
4. Você pode usar a **DeviceID** e comparar o status no serviço usando o portal do Azure ou o PowerShell.

### <a name="using-the-azure-portal"></a>Usando o portal do Azure

1. Acesse a página de dispositivos usando um [link direto](https://portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/Devices).
2. Informações sobre como localizar um dispositivo podem ser encontradas em [Como gerenciar identidades de dispositivo usando o portal do Azure](./device-management-azure-portal.md).
3. Se a coluna **Registrado** indica **Pendente**, o ingresso no Azure AD Híbrido não foi concluído. Em ambientes federados, isso pode ocorrer somente se ele falhar ao se registrar e a conexão do AAD estiver configurada para sincronizar os dispositivos.
4. Se a coluna **Registrado** contiver uma **data/hora**, o ingresso no Azure AD Híbrido foi concluído.

### <a name="using-powershell"></a>Usando o PowerShell

Verifique o estado de registro do dispositivo em seu locatário do Azure usando **[Get-MsolDevice](/powershell/module/msonline/get-msoldevice)** . Esse cmdlet está no [módulo do PowerShell do Azure Active Directory](/powershell/azure/active-directory/install-msonlinev1).

Ao usar o cmdlet **Get-MSolDevice** para verificar os detalhes do serviço:

- É necessário que haja um objeto com a **identificação do dispositivo** correspondente à ID no cliente do Windows.
- O valor para **DeviceTrustType** é **Ingressado no Domínio**. Essa configuração equivale ao estado **ingressou no Azure AD híbrido** na página **Dispositivos** no portal do Azure AD.
- Nos dispositivos que são usados em Acesso Condicional, o valor para **Enabled** é **True** e o de **DeviceTrustLevel** é **Managed**.

1. Abra o Windows PowerShell como administrador.
2. Digite `Connect-MsolService` para se conectar ao locatário do Azure.

#### <a name="count-all-hybrid-azure-ad-joined-devices-excluding-pending-state"></a>Contar todos os dispositivos ingressados no Azure AD Híbrido (excluindo o estado **Pendente**)

```azurepowershell
(Get-MsolDevice -All -IncludeSystemManagedDevices | where {($_.DeviceTrustType -eq 'Domain Joined') -and (([string]($_.AlternativeSecurityIds)).StartsWith("X509:"))}).count
```

#### <a name="count-all-hybrid-azure-ad-joined-devices-with-pending-state"></a>Contar todos os dispositivos ingressados no Azure AD Híbrido com estado **Pendente**

```azurepowershell
(Get-MsolDevice -All -IncludeSystemManagedDevices | where {($_.DeviceTrustType -eq 'Domain Joined') -and (-not([string]($_.AlternativeSecurityIds)).StartsWith("X509:"))}).count
```

#### <a name="list-all-hybrid-azure-ad-joined-devices"></a>Listar todos os dispositivos que tenham ingressado no Azure AD Híbrido

```azurepowershell
Get-MsolDevice -All -IncludeSystemManagedDevices | where {($_.DeviceTrustType -eq 'Domain Joined') -and (([string]($_.AlternativeSecurityIds)).StartsWith("X509:"))}
```

#### <a name="list-all-hybrid-azure-ad-joined-devices-with-pending-state"></a>Listar todos os dispositivos ingressados no Azure AD Híbrido com estado **Pendente**

```azurepowershell
Get-MsolDevice -All -IncludeSystemManagedDevices | where {($_.DeviceTrustType -eq 'Domain Joined') -and (-not([string]($_.AlternativeSecurityIds)).StartsWith("X509:"))}
```

#### <a name="list-details-of-a-single-device"></a>Listar detalhes de um único dispositivo:

1. Insira `get-msoldevice -deviceId <deviceId>` (esta é a **DeviceID** obtida localmente no dispositivo).
2. Verifique se **Habilitado** está definido como **Verdadeiro**.

## <a name="troubleshoot-your-implementation"></a>Solucionar problemas de implementação

Se estiver com problemas para concluir o ingresso no Azure AD híbrido de dispositivos Windows unidos ao domínio, confira:

- [Solução de problemas de dispositivos usando o comando dsregcmd](./troubleshoot-device-dsregcmd.md)
- [Solucionar problemas de ingresso no Azure AD híbrido para dispositivos atuais do Windows](troubleshoot-hybrid-join-windows-current.md)
- [Solucionar problemas de ingresso no Azure AD híbrido para dispositivos de nível inferior do Windows](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>Próximas etapas

Saiba como [gerenciar identidades de dispositivo usando o portal do Azure](device-management-azure-portal.md).

<!--Image references-->
[1]: ./media/active-directory-conditional-access-automatic-device-registration-setup/12.png
