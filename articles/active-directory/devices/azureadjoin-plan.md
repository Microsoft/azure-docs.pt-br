---
title: Como planejar sua implementação de junção do Azure Active Directory (Azure AD) | Microsoft Docs
description: Explica as etapas necessárias para implementar o Azure Active Directory ingressado em dispositivos em seu ambiente.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4332c921af770cb47e9a9a779d0bd148153fcd31
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67666136"
---
# <a name="how-to-plan-your-azure-ad-join-implementation"></a>Como: Planejar sua implementação de ingresso no Azure AD

O ingresso no Azure AD permite que você ingresse dispositivos diretamente ao Azure AD sem a necessidade de ingressarem no Active Directory local, mantendo os usuários produtivos e seguros. O ingresso no Azure AD está pronto para empresas em escala e implantações no escopo.   

Esse artigo fornece as informações necessárias para começar com as APIs de relatório do Azure Active Directory.
 
## <a name="prerequisites"></a>Pré-requisitos

Este artigo presume que você esteja familiarizado com o [Introdução ao gerenciamento de dispositivos no Active Directory do Azure](../device-management-introduction.md).

## <a name="plan-your-implementation"></a>Planejar sua implementação

Para planejar sua implementação de junção do Azure AD, você deve se familiarizar com:

|   |   |
|---|---|
|![Verificação][1]|Revisar seus cenários|
|![Verificação][1]|Revisar sua infraestrutura de identidade|
|![Verificação][1]|Avaliar o gerenciamento de dispositivo|
|![Verificação][1]|Entenda as considerações para aplicativos e recursos|
|![Verificação][1]|Entenda suas opções de provisionamento|
|![Verificação][1]|Configurar o roaming de estado|
|![Verificação][1]|Configurar o acesso condicional|

## <a name="review-your-scenarios"></a>Revisar seus cenários 

Enquanto o ingresso do Ingresso no Azure AD Híbrido pode ser preferencial para determinados cenários, o ingresso do Azure Active Directory possibilita que você transacione para um modelo de primeira nuvem com o Windows. Se você estiver planejando modernizar seu gerenciamento de dispositivos e reduzir os custos de TI relacionados ao dispositivo, o ingresso no Azure AD fornece uma ótima base para atingir esses objetivos.  
 
Você deve considerar o ingresso no Azure AD se suas metas se alinham com os seguintes critérios:

- Você está adotando o Microsoft 365 como o conjunto de produtividade para seus usuários.
- Você deseja gerenciar dispositivos com uma solução de gerenciamento de dispositivo de nuvem.
- Você deseja simplificar o provisionamento de dispositivos para usuários distribuídos geograficamente.
- Você planeja modernizar sua infraestrutura de aplicativo.

## <a name="review-your-identity-infrastructure"></a>Revisar sua infraestrutura de identidade  

O ingresso no Azure AD funciona com os ambientes, gerenciados e federados.  

### <a name="managed-environment"></a>Ambiente de leitura

Um ambiente gerenciado pode ser implantado por meio da [Sincronização de Hash de senha](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-password-hash-synchronization) ou [Autenticação de passagem](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta-quick-start) com logon único contínuo.

Esses cenários não exigem que você configure um servidor de federação para autenticação.

### <a name="federated-environment"></a>Ambiente federado

Um ambiente federado deve ter um provedor de identidade que dá suporte aos protocolos WS-Trust e WS-Fed:

- **WS-Fed:** Esse protocolo é necessário para ingressar um dispositivo no Azure AD.
- **WS-Trust:** Esse protocolo é necessário para entrar em um dispositivo ingressado no Azure AD. 

Se seu provedor de identidade não oferece suporte a esses protocolos, o ingresso no Azure Active Directory não trabalha nativamente. Começando com o Windows 10 1809, seus usuários podem entrar um dispositivo ingressado do Azure Active Directory com um provedor de identidade baseado em SAML [web entrar no Windows 10](https://docs.microsoft.com/windows/whats-new/whats-new-windows-10-version-1809#web-sign-in-to-windows-10). Atualmente, entrada na web é um recurso de visualização e não é recomendada para implantações de produção.

### <a name="smartcards-and-certificate-based-authentication"></a>Cartões inteligentes e autenticação baseada em certificado

É possível usar cartões inteligentes ou autenticação baseada em certificado para unir dispositivos ao Azure Active Directory. No entanto, os cartões inteligentes podem ser usado para entrar em dispositivos ingressados no Azure Active Directory se você tiver o AD FS configurado.

**Recomendação:** Implemente o Windows Hello para Empresas para autenticação forte e sem senha em dispositivos Windows 10.

### <a name="user-configuration"></a>Configuração do usuário

Se você criar usuários no seu:

- **Active Directory local**, você precisa sincronizá-los com o Azure AD usando o [Azure Active Directory Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-whatis). 
- **Azure AD**, nenhuma configuração adicional é necessária.

UPNs locais que são diferentes de UPNs do Azure AD não têm suporte em dispositivos ingressados no Azure AD. Se os usuários usarem um UPN local, você deverá planejar passar a usar seus UPNs primários do Azure AD.

## <a name="assess-your-device-management"></a>Avaliar o gerenciamento de dispositivo

### <a name="supported-devices"></a>Dispositivos com suporte

Ingresso no Azure AD:

- Só é aplicável a dispositivos Windows 10. 
- Não é aplicável a versões anteriores do Windows ou outros sistemas operacionais. Se você tiver dispositivos Windows 7/8.1, você deve atualizar para o Windows 10 para implantar o ingresso no Azure Active Directory.
- Não há suporte em dispositivos com o TPM no modo FIPS.
 
**Recomendação:** Sempre use a última versão do Windows 10 para aproveitar os recursos atualizados.

### <a name="management-platform"></a>Plataforma de gerenciamento

Gerenciamento de dispositivo para dispositivos ingressados no Azure AD se baseia em uma plataforma MDM como Intune e MDM CSPs. Windows 10 tem um agente MDM interno que funciona com todas as soluções MDM compatíveis.

> [!NOTE]
> As políticas de grupo não têm suporte em dispositivos ingressados no Azure AD que não estão conectados ao Active Directory no local. Gerenciamento de dispositivos ingressados no Azure AD só é possível por meio do MDM

Há duas abordagens para gerenciar o Azure Active Directory ingressado em dispositivos:

- **Somente MDM** - exclusivamente, um dispositivo é gerenciado por um provedor MDM como Intune. Todas as políticas são fornecidas como parte do processo de registro de MDM. Para clientes do Azure AD Premium ou EMS, o registro do MDM é uma etapa automatizada que faz parte de uma junção do Azure Active Directory.
- **Cogerenciamento** – um dispositivo é gerenciado por um provedor de MDM e o SCCM. Nessa abordagem, o agente do SCCM é instalado em um dispositivo gerenciado por MDM para determinados aspectos de administrar.

Se você estiver usando políticas de grupo, avalie a paridade de política MDM usando a [Ferramenta de Análise de Migração de MDM (MMAT)](https://github.com/WindowsDeviceManagement/MMAT). 

Revisar as políticas compatíveis ou não compatíveis para determinar se você pode usar uma solução MDM em vez de políticas de Grupo. Para políticas sem suporte, considere o seguinte:

- As diretivas sem suporte são necessárias para dispositivos ou usuários adicionados ao Azure Active Directory?
- As diretivas sem suporte são aplicáveis em uma nuvem controlada por implantação?

Se sua solução de MDM não estiver disponível por meio da galeria de aplicativo do Azure Active Directory, você pode adicioná-lo seguindo o processo descrito em [integração do Active Directory do Azure com o MDM](https://docs.microsoft.com/windows/client-management/mdm/azure-active-directory-integration-with-mdm). 

Por meio de cogerenciamento, você pode usar o SCCM para gerenciar determinados aspectos de seus dispositivos, enquanto as políticas são fornecidas por meio de sua plataforma MDM. Microsoft Intune permite que o cogerenciamento com o SCCM. Para obter mais informações, consulte [cogerenciamento para dispositivos Windows 10](https://docs.microsoft.com/sccm/core/clients/manage/co-management-overview). Se você usar um produto MDM que não seja o Intune, entre em contato com seu provedor de MDM em cenários de cogerenciamento aplicável.

**Recomendação:** Considere o gerenciamento somente do MDM para dispositivos ingressados no Azure AD.

## <a name="understand-considerations-for-applications-and-resources"></a>Entenda as considerações para aplicativos e recursos

É recomendável migrar aplicativos locais para a nuvem para um usuário a melhor experiência e controle de acesso. No entanto, os dispositivos do Azure Active Directory ingressados podem perfeitamente fornecer acesso a ambos, aplicativos locais e de nuvem. Para obter mais informações, consulte [Como o SSO para recursos locais funciona em dispositivos associados ao Microsoft Azure Active Directory](azuread-join-sso.md).

As seções a seguir listam considerações para diferentes tipos de aplicativos e recursos.

### <a name="cloud-based-applications"></a>Aplicativos seguros baseados em nuvem

Se um aplicativo for adicionado à galeria de aplicativo do Azure Active Directory, os usuários obtêm SSO através dos dispositivos do Azure Active Directory ingressado. É necessário realizar uma configuração adicional. Os usuários obtêm o SSO em navegadores Microsoft Edge e Chrome. Para o Chrome, você precisará implantar a [extensão de contas do Windows 10](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji). 

Todos os aplicativos Win32 que:

- Confie no Gerenciador de Conta da Web (WAM) para solicitações de token também o SSO em dispositivos ingressados no Azure Active Directory. 
- Não confiar em WAM pode solicitar autenticação dos usuários. 

### <a name="on-premises-web-applications"></a>Aplicativos Web locais

Se seus aplicativos são personalizados criados e/ou hospedados no local, você precisará adicioná-los para sites confiáveis do seu navegador:

- Habilitar autenticação integrada do Windows para trabalhar 
- Fornecer uma experiência não prompt SSO para usuários. 

Se você usar o AD FS, consulte [Verificar e gerenciar logon único com o AD FS](https://docs.microsoft.com/previous-versions/azure/azure-services/jj151809(v%3dazure.100)). 

**Recomendação:** Considere a hospedagem na nuvem (por exemplo, Azure) e a integração com o Azure AD para uma experiência melhor.

### <a name="on-premises-applications-relying-on-legacy-protocols"></a>Os aplicativos locais que dependem de protocolos herdados locais

Os usuários obtêm SSO dos dispositivos Azure Active Directory ingressado se o dispositivo tiver acesso ao controlador de domínio. 

**Recomendação:** Implante o [proxy do Aplicativo Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy) para habilitar o acesso seguro para esses aplicativos.

### <a name="on-premises-network-shares"></a>Compartilhamento de rede local

Seus usuários têm o SSO de dispositivos ingressados no Azure Active Directory quando um dispositivo tem acesso a um controlador de domínio local.

### <a name="printers"></a>Impressoras

Para as impressoras, você precisará implantar [nuvem híbrida impressão](https://docs.microsoft.com/windows-server/administration/hybrid-cloud-print/hybrid-cloud-print-deploy) para descobrir impressoras no Azure Active Directory e ingressado em dispositivos. 

Enquanto as impressoras não podem ser descobertas automaticamente em um único ambiente de nuvem, os usuários também podem usar o caminho UNC das impressoras para direcioná-los. 

### <a name="on-premises-applications-relying-on-machine-authentication"></a>Aplicativos locais que dependem da autenticação do computador

O Azure Active Directory não dão suporte a aplicativos locais na autenticação de máquina da terceira parte confiável. 

**Recomendação:** Considere a desativação desses aplicativos e a migração para suas alternativas modernas.

### <a name="remote-desktop-services"></a>Serviços da Área de Trabalho Remota

Conexão da área de trabalho remota para um dispositivos adicionados ao Azure Active Directory requer que o computador host para o Azure Active Directory ingressado ou Azure AD Híbrido ingressado. Área de trabalho remota de um dispositivo não relacionado ou não Windows que não é compatível. Para obter mais informações, consulte [Conectar ao Azure Active Directory ingressado remoto pc](https://docs.microsoft.com/windows/client-management/connect-to-remote-aadj-pc)

## <a name="understand-your-provisioning-options"></a>Entenda suas opções de provisionamento

Você pode provisionar o ingresso no Azure Active Directory usando as seguintes abordagens:

- **Autoatendimento nas configurações doOOBE/** – no modo autoatendimento, os usuários vão por meio do ingresso no Azure Active Directory seja durante fora do Windows Out of Box Experience (OOBE) ou Configurações do Windows. Para obter mais informações, consulte [ingressar seu dispositivo de trabalho para a rede da sua organização](https://docs.microsoft.com/azure/active-directory/user-help/user-help-join-device-on-network). 
- **Windows Autopilot** - Windows Autopilot permite a configuração prévia de dispositivos para uma experiência mais suave em OOBE para realizar uma junção do Azure Active Directory. Para saber mais, confira a página [visão geral do Windows Autopilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-10-autopilot). 
- **Registro em massa** - o registro em massa permite que um administrador de ingresso no Azure Active Directory usando uma ferramenta de provisionamento em massa para configurar os dispositivos. Para obter mais informações, consulte [Registro em massa para dispositivos Windows](https://docs.microsoft.com/intune/windows-bulk-enroll).
 
Aqui está uma comparação dessas três abordagens 
 
|   | Instalação do autoatendimento | Windows Autopilot | Registro em massa |
| --- | --- | --- | --- |
| Requer interação do usuário para configurar | Sim | sim | Não |
| Requer trabalho de TI | Não | Sim | Sim |
| Fluxos aplicáveis | OOBE e Configurações | OOBE somente | OOBE somente |
| Direitos de administrador local para o usuário primário | Sim, por padrão | Configurável | Não |
| Precisar de suporte do OEM | Não | Sim | Não |
| Versões com suporte | 1511+ | 1709+ | 1703+ |
 
Escolha sua abordagem de implantação ou abordagens examinando a tabela acima e revisar as considerações para adotar a qualquer uma das abordagens a seguir:  

- Seus usuários são experientes em tecnologia para passar pela configuração? 
   - Autoatendimento pode funcionar melhor para esses usuários. Considere o Windows Autopilot para aprimorar a experiência do usuário.  
- Os seus usuários são remotos ou dentro das instalações corporativas? 
   - Autoatendimento ou trabalho de piloto automático melhor para usuários remotos para uma configuração de complicações. 
- Você prefere uma configuração conduzida ao usuário ou gerenciada pelo administrador? 
   - Inscrição em massa funciona melhor para a implantação orientada pelo administrador a fim de configurar os dispositivos antes de entregá-los aos usuários.     
- Você compra dispositivos do 1-2 OEMS ou você tem uma distribuição grande de dispositivos OEM?  
   - Se você adquirir de OEMs limitados que também dão suporte a Autopilot, você pode aproveitar uma integração maior com o Autopilot. 

## <a name="configure-your-device-settings"></a>Configurar suas configurações de dispositivo

O portal do Azure permite você a controlar a implantação dos dispositivo Azure Active Directory em sua organização. Para definir as configurações relacionadas, sobre a **página do Azure Active Directory**, selecione `Devices > Device settings`.

### <a name="users-may-join-devices-to-azure-ad"></a>Os usuários podem ingressar no Azure Active Directory com seus dispositivos

Defina essa opção como **Todos** ou **Selecionados** com base no escopo de sua implantação e o dispositivo que você deseja permitir a instalação do Azure Active Directory ingressado. 

![Os usuários podem ingressar no Azure Active Directory com seus dispositivos](./media/azureadjoin-plan/01.png)

### <a name="additional-local-administrators-on-azure-ad-joined-devices"></a>Administradores locais adicionais nos dispositivos ingressados do Azure AD

Escolher **Selecionados** e seleciona os usuários que você deseja adicionar ao grupo os administradores locais nos dispositivos de todos os dispositivos ingressados do Azure Active Directory. 

![Administradores locais adicionais nos dispositivos ingressados do Azure AD](./media/azureadjoin-plan/02.png)

### <a name="require-multi-factor-auth-to-join-devices"></a>Requer Autenticação Multifator para ingressar dispositivos

Selecione **"Sim** se você exigir que os usuários realizem a MFA durante o ingresso de dispositivos para o Azure AD. Para os usuários de ingresso de dispositivos para o Azure Active Directory usando o MFA, o próprio dispositivo se torna um segundo fator.

![Requer Autenticação Multifator para ingressar dispositivos](./media/azureadjoin-plan/03.png)

## <a name="configure-your-mobility-settings"></a>Configurar as suas configurações de mobilidade

Antes de definir as configurações de mobilidade, talvez você precise adicionar um provedor de MDM, pela primeira vez.

**Para adicionar um provedor MDM**:

1. Na página **Azure Active Directory**, na seção **Gerenciar**, clique em `Mobility (MDM and MAM)`. 
1. Clique em **Adicionar aplicativo**.
1. Selecione seu provedor de MDM da lista.

   ![Adicionar um aplicativo](./media/azureadjoin-plan/04.png)

Selecione seu provedor de MDM para definir as configurações relacionadas. 

### <a name="mdm-user-scope"></a>Escopo do usuário do MDM

Selecione **Alguns** ou **Todos** com base no escopo de sua implantação. 

![Escopo do usuário do MDM](./media/azureadjoin-plan/05.png)

Com base no seu escopo, acontecerá o seguinte: 

- **O usuário está no escopo do MDM**: Caso você tenha uma assinatura do Azure AD Premium, o registro do MDM será automatizado, juntamente com o ingresso no Azure AD. Todos os usuários com escopo devem ter uma licença apropriada para seu MDM. Se o registro do MDM falhar nesse cenário, ingresso no Azure Active Directory também será revertido novamente.
- **O usuário não está no escopo do MDM**: Se os usuários não estiverem no escopo do MDM, o ingresso no Azure AD será concluído sem nenhum registro do MDM. Isso resulta em um dispositivo não gerenciado.

### <a name="mdm-urls"></a>URLs do MDM

Há três URLs que estão relacionadas à sua configuração de MDM:

- Termos de MDM de uso URL
- URL de descoberta de MDM 
- URL de conformidade de MDM

![Adicionar um aplicativo](./media/azureadjoin-plan/06.png)

Cada URL tem um valor padrão predefinido. Se esses campos estiverem vazios, entre em contato com seu provedor de MDM para obter mais informações.

### <a name="mam-settings"></a>Configurações de MAM

MAM não é aplicável ao ingresso no Azure Active Directory. 

## <a name="configure-enterprise-state-roaming"></a>Configurar o roaming de estado

Se você quiser habilitar o roaming de estado para o Azure Active Directory para que os usuários podem sincronizar suas configurações entre dispositivos, consulte [habilitar o Enterprise State Roaming no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/devices/enterprise-state-roaming-enable). 

**Recomendação**: Habilite essa configuração mesmo para dispositivos híbridos ingressados no Azure AD.

## <a name="configure-conditional-access"></a>Configurar o acesso condicional

Se você tiver um provedor MDM configurado para os dispositivos Azure Active Directory ingressados, o provedor sinaliza o dispositivo como conformidade assim que o dispositivo está sob gerenciamento. 

![Dispositivo em conformidade](./media/azureadjoin-plan/46.png)

Você pode usar essa implementação para [exigir que os dispositivos gerenciados para acesso de aplicativo de nuvem com acesso condicional](../conditional-access/require-managed-devices.md).

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Ingressar um novo dispositivo Windows 10 com o Azure Active Directory durante uma primeira execução](azuread-joined-devices-frx.md)
> [ingressar seu dispositivo de trabalho para a rede da sua organização](https://docs.microsoft.com/azure/active-directory/user-help/user-help-join-device-on-network)

<!--Image references-->
[1]: ./media/azureadjoin-plan/12.png
