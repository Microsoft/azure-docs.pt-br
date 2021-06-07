---
title: Configurar o ingresso no Azure Active Directory híbrido para domínios gerenciados | Microsoft Docs
description: Saiba como configurar o ingresso no Azure Active Directory híbrido para domínios gerenciados.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: tutorial
ms.date: 01/26/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4545e92767a427b8cd89af07ed4d06053685977a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104577998"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-join-for-managed-domains"></a>Tutorial: Configurar o ingresso no Azure Active Directory híbrido para os domínios gerenciados

Neste tutorial, você aprenderá a configurar o ingresso no Azure AD (Azure Active Directory) híbrido para dispositivos ingressados no domínio do Active Directory. Esse método dá suporte a um ambiente gerenciado que inclui o Active Directory local e o Azure AD.

Como um usuário na sua organização, um dispositivo é uma identidade importante que você quer proteger. É possível usar uma identidade do dispositivo para proteger seus recursos a qualquer momento e de qualquer local. Você pode atingir essa meta gerenciando identidades de dispositivo no Azure AD. Use um dos métodos a seguir:

- Ingresso no Azure AD
- Ingresso no Azure AD Híbrido
- Registro do Azure AD

Este artigo se concentra no ingresso no Azure AD híbrido.

Colocar os dispositivos no Azure AD maximiza a produtividade do usuário por meio de SSO (logon único) em recursos locais e na nuvem. Você pode proteger o acesso aos recursos locais e na nuvem com o [Acesso Condicional](../conditional-access/howto-conditional-access-policy-compliant-device.md) ao mesmo tempo.

Você pode implantar um ambiente gerenciado por meio de [PHS (sincronização de hash de senha)](../hybrid/whatis-phs.md) ou [PTA (autenticação de passagem)](../hybrid/how-to-connect-pta.md) com [logon único contínuo](../hybrid/how-to-connect-sso.md). Esses cenários não exigem que você configure um servidor de federação para autenticação.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Configurar ingresso no Azure AD híbrido
> * Habilitar dispositivos de nível inferior do Windows
> * Verificar dispositivos ingressados
> * Solucionar problemas

## <a name="prerequisites"></a>Pré-requisitos

- O [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) (1.1.819.0 ou posterior)
- As credenciais de um administrador global para o locatário do Azure AD
- Credenciais de administrador corporativo para cada uma das florestas

Familiarize-se com estes artigos:

- [O que é uma identidade do dispositivo?](overview.md)
- [Como planejar sua implementação de junção do Azure Active Directory híbrido](hybrid-azuread-join-plan.md)
- [Validação controlada de ingresso no Azure AD híbrido](hybrid-azuread-join-control.md)

> [!NOTE]
> O Azure Active Directory não dá suporte a cartões inteligentes ou certificados em domínios gerenciados.

Verifique se o Azure AD Connect sincronizou os objetos de computador dos dispositivos que você quer que sejam unidos ao Azure AD híbrido com o Azure AD. Se os objetos de computador pertencerem a UOs (unidades organizacionais) específicas, configure as UOs para sincronizarem no Azure AD Connect. Para saber mais sobre como sincronizar objetos de computador usando o Azure AD Connect, veja [Filtragem baseada em unidade organizacional](../hybrid/how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering).

> [!NOTE]
> Para que a junção de sincronização de registro de dispositivo tenha sucesso, como parte da configuração de registro de dispositivo, não exclua os atributos de dispositivo padrão da configuração de sincronização do Azure AD Connect. Para saber mais sobre os atributos de dispositivo padrão sincronizados com o AAD, confira [Atributos sincronizados pelo Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-sync-attributes-synchronized#windows-10).

Na versão 1.1.819.0 e em versões posteriores, o Azure AD Connect inclui um assistente para configurar o ingresso no Azure AD híbrido. O assistente simplifica significativamente o processo de configuração. O assistente configura os SCPs (pontos de conexão do serviço) para registro do dispositivo.

As etapas de configuração neste artigo se baseiam no uso do assistente no Azure AD Connect.

O ingresso no Azure AD híbrido requer que os dispositivos tenham acesso aos seguintes recursos da Microsoft dentro da rede da organização:  

- `https://enterpriseregistration.windows.net`
- `https://login.microsoftonline.com`
- `https://device.login.microsoftonline.com`
- `https://autologon.microsoftazuread-sso.com` (se você usa ou planeja usar o SSO contínuo)

> [!WARNING]
> Se a sua organização usa servidores proxy que interceptam o tráfego SSL para cenários como prevenção de perda de dados ou restrições de locatário do Azure AD, verifique se o tráfego para 'https://device.login.microsoftonline.com ' foi excluído do TLS de interrupção e inspeção. A falha ao excluir o 'https://device.login.microsoftonline.com ' pode causar interferência com a autenticação de certificado de cliente, causando problemas com o registro de dispositivo e o Acesso Condicional com base no dispositivo.

Se a sua organização exigir acesso à Internet por meio de um proxy de saída, você poderá usar [implementar a WPAD (Descoberta Automática de Proxy Web)](/previous-versions/tn-archive/cc995261(v=technet.10)) para permitir que computadores Windows 10 registrem-se no dispositivo com o Azure AD. Para solucionar problemas encontrados ao configurar e gerenciar a WPAD, veja [Solucionando problemas de detecção automática](/previous-versions/tn-archive/cc302643(v=technet.10)). Em dispositivos Windows 10 anteriores à atualização 1709, o WPAD é a única opção disponível para configurar um proxy para trabalhar com o ingresso no Azure AD Híbrido. 

Se você não usa a WPAD, pode configurar as definições de proxy WinHTTP no computador com o Windows 10 1709 e versões posteriores. Para obter mais informações, confira [Configurações de proxy WinHTTP implantadas por GPO](/archive/blogs/netgeeks/winhttp-proxy-settings-deployed-by-gpo).

> [!NOTE]
> Se você definir as configurações de proxy em seu computador usando as configurações de WinHTTP, qualquer computador que não possa se conectar ao proxy configurado não conseguirá se conectar à Internet.

Se a organização exigir acesso à Internet por meio de um proxy de saída autenticado, assegure que os computadores com Windows 10 possam ser autenticados com êxito no proxy de saída. Como computadores com Windows 10 executam o registro de dispositivos usando o contexto do computador, configure a autenticação de proxy de saída usando o contexto do computador. Acompanhe com o provedor de proxy de saída nos requisitos de configuração.

Verifique se o dispositivo é capaz de acessar os recursos da Microsoft mencionados acima na conta do sistema, usando o script de [conectividade de registro de dispositivo de teste](/samples/azure-samples/testdeviceregconnectivity/testdeviceregconnectivity/).

## <a name="configure-hybrid-azure-ad-join"></a>Configurar ingresso no Azure AD híbrido

Para configurar um ingresso no Azure AD híbrido usando o Azure AD Connect:

1. Inicie o Azure AD Connect e selecione **Configurar**.

1. Em **Tarefas adicionais**, selecione **Configurar opções de dispositivo** e, em seguida, **Avançar**.

   ![Tarefas adicionais](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-additional-tasks.png)

1. Em **Visão geral**, selecione **Avançar**.

1. Em **Conectar o Azure AD**, insira as credenciais de um administrador global para o locatário do Azure AD.  

1. Em **Opções do dispositivo**, selecione **Configurar ingresso no Azure AD Híbrido** e, em seguida, selecione **Avançar**.

   ![Opções do dispositivo](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-device-options.png)

1. Em **Sistemas operacionais do dispositivo**, selecione os sistemas operacionais que os dispositivos no ambiente do Active Directory usam e selecione **Avançar**.

   ![Sistema operacional do dispositivo](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-device-operating-systems.png)

1. Em **Configuração de SCP**, de cada floresta em que você deseja que o Azure AD Connect configure o SCP, conclua as seguintes etapas e selecione **Avançar**.

   1. Selecione a **Floresta**.
   1. Selecione um **Serviço de Autenticação**.
   1. Selecione **Adicionar** para inserir as credenciais de administrador corporativo.

   ![SCP](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-scp-configuration.png)

1. Em **Pronto para configurar**, selecione **Configurar**.

1. Em **Configuração completa** selecione **Sair**.

## <a name="enable-windows-down-level-devices"></a>Habilitar dispositivos de nível inferior do Windows

Se alguns dos seus dispositivos ingressados no domínio forem dispositivos de nível inferior do Windows, será necessário:

- Definir as configurações de Intranet Local para registro do dispositivo
- Configurar o SSO contínuo
- Instalar o Workplace Join da Microsoft em computadores Windows de nível inferior

> [!NOTE]
> O suporte do Windows 7 foi encerrado em 14 de janeiro de 2020. Para obter mais informações, confira [O suporte ao Windows 7 terminou](https://support.microsoft.com/help/4057281/windows-7-support-ended-on-january-14-2020).

### <a name="configure-the-local-intranet-settings-for-device-registration"></a>Definir as configurações de Intranet Local para registro do dispositivo

Para concluir o ingresso no Azure AD híbrido dos dispositivos de nível inferior do Windows e evitar prompts de certificado quando os dispositivos autenticarem no Azure AD, envie uma política por push aos dispositivos ingressados no domínio para adicionar as seguintes URLs à zona da intranet local no Internet Explorer:

- `https://device.login.microsoftonline.com`
- `https://autologon.microsoftazuread-sso.com`

Também será preciso habilitar **Permitir atualizações da barra de status via script** na zona da intranet local do usuário.

### <a name="configure-seamless-sso"></a>Configurar o SSO contínuo

Para concluir o ingresso no Azure AD híbrido de seus dispositivos de nível inferior do Windows em um domínio gerenciado que usa a [sincronização de hash de senha](../hybrid/whatis-phs.md) ou a [autenticação de passagem](../hybrid/how-to-connect-pta.md) como o método de autenticação de nuvem do Azure AD, você também precisa [configurar o SSO contínuo](../hybrid/how-to-connect-sso-quick-start.md#step-2-enable-the-feature).

### <a name="install-microsoft-workplace-join-for-windows-down-level-computers"></a>Instalar o Workplace Join da Microsoft em computadores Windows de nível inferior

Para registrar dispositivos de nível inferior do Windows, as organizações devem instalar o [Workplace Join da Microsoft nos computadores sem Windows 10](https://www.microsoft.com/download/details.aspx?id=53554). O Workplace Join da Microsoft para computadores sem Windows 10 está disponível no Centro de Download da Microsoft.

É possível implantar o pacote usando um sistema de distribuição de software como o  [Microsoft Endpoint Configuration Manager](/configmgr/). O pacote dá suporte às opções de instalação silenciosa padrão com o parâmetro `quiet`. A versão atual do Configuration Manager oferece benefícios adicionais em relação às versões anteriores, como a capacidade de rastrear registros concluídos.

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
3. Se a coluna **Registrado** indica **Pendente**, o ingresso no Azure AD Híbrido não foi concluído.
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
- [Solução de problemas do Azure Active Directory híbrido ingressado em dispositivos](troubleshoot-hybrid-join-windows-current.md)
- [Solução de problemas do Azure Active Directory híbrido ingressado em dispositivos de nível inferior](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>Próximas etapas

Continue para o próximo artigo para saber como gerenciar identidades de dispositivo usando o portal do Azure.
> [!div class="nextstepaction"]
> [Gerenciar identidades do dispositivo](device-management-azure-portal.md)
