---
title: Como planejar sua implementação de Azure Active Directory Join
description: Explica as etapas necessárias para implementar o Azure Active Directory ingressado em dispositivos em seu ambiente.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3acaf4929158b24ff50655aa18c05b41aeec4b53
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96435443"
---
# <a name="how-to-plan-your-azure-ad-join-implementation"></a>Como: planejar sua implementação de junção do Azure AD

O ingresso no Azure AD permite que você ingresse dispositivos diretamente ao Azure AD sem a necessidade de ingressarem no Active Directory local, mantendo os usuários produtivos e seguros. O ingresso no Azure AD está pronto para empresas em escala e implantações no escopo.   

Esse artigo fornece as informações necessárias para começar com as APIs de relatório do Azure Active Directory.
 
## <a name="prerequisites"></a>Pré-requisitos

Este artigo presume que você esteja familiarizado com o [Introdução ao gerenciamento de dispositivos no Active Directory do Azure](./overview.md).

## <a name="plan-your-implementation"></a>Planejar sua implementação

Para planejar sua implementação de ingresso no Azure AD, você deve se familiarizar com:

> [!div class="checklist"]
> - Revisar seus cenários
> - Revisar sua infraestrutura de identidade
> - Avaliar o gerenciamento de dispositivo
> - Entenda as considerações para aplicativos e recursos
> - Entenda suas opções de provisionamento
> - Configurar o roaming de estado
> - Configurar acesso condicional

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

Um ambiente gerenciado pode ser implantado por meio da [Sincronização de Hash de senha](../hybrid/how-to-connect-password-hash-synchronization.md) ou [Autenticação de passagem](../hybrid/how-to-connect-pta-quick-start.md) com logon único contínuo.

Esses cenários não exigem que você configure um servidor de federação para autenticação.

### <a name="federated-environment"></a>Ambiente federado

Um ambiente federado deve ter um provedor de identidade que dá suporte aos protocolos WS-Trust e WS-Fed:

- **WS-Fed:** esse protocolo é necessário para ingressar em um dispositivo para o Azure AD.
- **WS-Trust:** esse protocolo é necessário entrar para um dispositivo ingressado do Azure Active Directory.

Quando você estiver usando o AD FS, será necessário habilitar os seguintes pontos de extremidade WS-Trust: `/adfs/services/trust/2005/usernamemixed`
 `/adfs/services/trust/13/usernamemixed`
 `/adfs/services/trust/2005/certificatemixed`
 `/adfs/services/trust/13/certificatemixed`

Se seu provedor de identidade não oferece suporte a esses protocolos, o ingresso no Azure Active Directory não trabalha nativamente. 

>[!NOTE]
> Atualmente, o ingresso no Azure AD não funciona com [AD FS 2019 configurado com provedores de autenticação externa como o método de autenticação principal](/windows-server/identity/ad-fs/operations/additional-authentication-methods-ad-fs#enable-external-authentication-methods-as-primary). O ingresso no Azure AD usa como padrão a autenticação de senha como o método principal, o que resulta em falhas de autenticação nesse cenário


### <a name="smartcards-and-certificate-based-authentication"></a>Cartões inteligentes e autenticação baseada em certificado

É possível usar cartões inteligentes ou autenticação baseada em certificado para unir dispositivos ao Azure Active Directory. No entanto, os cartões inteligentes podem ser usado para entrar em dispositivos ingressados no Azure Active Directory se você tiver o AD FS configurado.

**Recomendação:** implementam Windows Hello para Empresas para autenticação forte, sem senha para dispositivos Windows 10.

### <a name="user-configuration"></a>Configuração do usuário

Se você criar usuários no seu:

- **Active Directory local**, você precisa sincronizá-los com o Azure AD usando o [Azure Active Directory Connect](../hybrid/how-to-connect-sync-whatis.md). 
- **Azure AD**, nenhuma configuração adicional é necessária.

UPNs locais que são diferentes de UPNs do Azure AD não têm suporte em dispositivos ingressados no Azure AD. Se os usuários usarem um UPN local, você deverá planejar passar a usar seus UPNs primários do Azure AD.

As alterações de UPN só têm suporte iniciando a atualização do Windows 10 2004. Os usuários em dispositivos com esta atualização não terão problemas depois de alterar seus UPNs. Para dispositivos anteriores à atualização do Windows 10 2004, os usuários teriam problemas de acesso condicional e de SSO em seus dispositivos. Eles precisam entrar no Windows por meio do bloco "outro usuário" usando o novo UPN para resolver esse problema. 

## <a name="assess-your-device-management"></a>Avaliar o gerenciamento de dispositivo

### <a name="supported-devices"></a>Dispositivos com suporte

Ingresso no Azure AD:

- Só é aplicável a dispositivos Windows 10. 
- Não é aplicável a versões anteriores do Windows ou outros sistemas operacionais. Se você tiver dispositivos Windows 7/8.1, você deve atualizar para o Windows 10 para implantar o ingresso no Azure Active Directory.
- Tem suporte para TPM 2,0 compatível com FIPS, mas sem suporte para TPM 1,2. Se seus dispositivos tiverem o TPM 1,2 compatível com FIPS, você deverá desabilitá-los antes de prosseguir com o ingresso no Azure AD. A Microsoft não fornece nenhuma ferramenta para desabilitar o modo FIPS para TPMs, pois depende do fabricante do TPM. Entre em contato com o OEM de hardware para obter suporte.
 
**Recomendação:** sempre usar a versão mais recente do Windows 10 para tirar proveito dos recursos atualizados.

### <a name="management-platform"></a>Plataforma de gerenciamento

O gerenciamento de dispositivos para dispositivos ingressados no Azure AD é baseado em uma plataforma MDM, como o Intune, e CSPs de MDM. Windows 10 tem um agente MDM interno que funciona com todas as soluções MDM compatíveis.

> [!NOTE]
> Não há suporte para políticas de grupo em dispositivos ingressados no Azure AD, pois eles não estão conectados a Active Directory locais. O gerenciamento de dispositivos ingressados no Azure AD só é possível por meio do MDM

Há duas abordagens para gerenciar o Azure Active Directory ingressado em dispositivos:

- **Somente MDM** - exclusivamente, um dispositivo é gerenciado por um provedor MDM como Intune. Todas as políticas são fornecidas como parte do processo de registro de MDM. Para clientes do Azure AD Premium ou EMS, o registro do MDM é uma etapa automatizada que faz parte de uma junção do Azure Active Directory.
- **Cogerenciamento** – um dispositivo é gerenciado por um provedor de MDM e o SCCM. Nessa abordagem, o agente do SCCM é instalado em um dispositivo gerenciado por MDM para determinados aspectos de administrar.

Se você estiver usando políticas de grupo, avalie o GPO e a paridade da política de MDM usando [política de grupo Analytics](/mem/intune/configuration/group-policy-analytics) no Microsoft Endpoint Manager. 

Revisar as políticas compatíveis ou não compatíveis para determinar se você pode usar uma solução MDM em vez de políticas de Grupo. Para políticas sem suporte, considere o seguinte:

- As diretivas sem suporte são necessárias para dispositivos ou usuários adicionados ao Azure Active Directory?
- As diretivas sem suporte são aplicáveis em uma nuvem controlada por implantação?

Se sua solução de MDM não estiver disponível por meio da galeria de aplicativo do Azure Active Directory, você pode adicioná-lo seguindo o processo descrito em [integração do Active Directory do Azure com o MDM](/windows/client-management/mdm/azure-active-directory-integration-with-mdm). 

Por meio de cogerenciamento, você pode usar o SCCM para gerenciar determinados aspectos de seus dispositivos, enquanto as políticas são fornecidas por meio de sua plataforma MDM. Microsoft Intune permite que o cogerenciamento com o SCCM. Para obter mais informações sobre o cogerenciamento para dispositivos Windows 10, consulte [o que é cogerenciamento?](/configmgr/core/clients/manage/co-management-overview). Se você usar um produto MDM que não seja o Intune, entre em contato com seu provedor de MDM em cenários de cogerenciamento aplicável.

**Recomendação:** considere MDM somente a dispositivos adicionados ao gerenciamento do Azure Active Directory.

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

Se você usar o AD FS, consulte [Verificar e gerenciar logon único com o AD FS](/previous-versions/azure/azure-services/jj151809(v%3dazure.100)). 

**Recomendação:** c’onsidere hospedar na nuvem (por exemplo, o Azure) e a integração com o Azure Active Directory para uma melhor experiência.

### <a name="on-premises-applications-relying-on-legacy-protocols"></a>Os aplicativos locais que dependem de protocolos herdados locais

Os usuários obtêm SSO dos dispositivos Azure Active Directory ingressado se o dispositivo tiver acesso ao controlador de domínio. 

**Recomendação:** implantar o [proxy de Aplicativo do Azure Active Directory](../manage-apps/application-proxy.md) para habilitar o acesso seguro para esses aplicativos.

### <a name="on-premises-network-shares"></a>Compartilhamento de rede local

Seus usuários têm o SSO de dispositivos ingressados no Azure Active Directory quando um dispositivo tem acesso a um controlador de domínio local.

### <a name="printers"></a>Impressoras

Para as impressoras, você precisará implantar [nuvem híbrida impressão](/windows-server/administration/hybrid-cloud-print/hybrid-cloud-print-deploy) para descobrir impressoras no Azure Active Directory e ingressado em dispositivos. 

Enquanto as impressoras não podem ser descobertas automaticamente em um único ambiente de nuvem, os usuários também podem usar o caminho UNC das impressoras para direcioná-los. 

### <a name="on-premises-applications-relying-on-machine-authentication"></a>Aplicativos locais que dependem da autenticação do computador

O Azure Active Directory não dão suporte a aplicativos locais na autenticação de máquina da terceira parte confiável. 

**Recomendação:** considere desativar esses aplicativos e mover para suas alternativas modernas.

### <a name="remote-desktop-services"></a>Serviços de área de trabalho remota

Conexão da área de trabalho remota para um dispositivos adicionados ao Azure Active Directory requer que o computador host para o Azure Active Directory ingressado ou Azure AD Híbrido ingressado. Área de trabalho remota de um dispositivo não relacionado ou não Windows que não é compatível. Para obter mais informações, consulte [Conectar ao Azure Active Directory ingressado remoto pc](/windows/client-management/connect-to-remote-aadj-pc)

Iniciando a atualização do Windows 10 2004, os usuários também podem usar a área de trabalho remota de um dispositivo Windows 10 registrado no Azure AD para um dispositivo ingressado no Azure AD. 

## <a name="understand-your-provisioning-options"></a>Entenda suas opções de provisionamento
**Observação**: os dispositivos adicionados ao Azure ad não podem ser implantados usando a ferramenta de preparação do sistema (Sysprep) ou ferramentas de geração de imagens semelhantes

Você pode provisionar o ingresso no Azure Active Directory usando as seguintes abordagens:

- **Autoatendimento nas configurações doOOBE/** – no modo autoatendimento, os usuários vão por meio do ingresso no Azure Active Directory seja durante fora do Windows Out of Box Experience (OOBE) ou Configurações do Windows. Para obter mais informações, consulte [ingressar seu dispositivo de trabalho para a rede da sua organização](../user-help/user-help-join-device-on-network.md). 
- **Windows Autopilot** - Windows Autopilot permite a configuração prévia de dispositivos para uma experiência mais suave em OOBE para realizar uma junção do Azure Active Directory. Para saber mais, confira a página [visão geral do Windows Autopilot](/windows/deployment/windows-autopilot/windows-10-autopilot). 
- **Registro em massa** - o registro em massa permite que um administrador de ingresso no Azure Active Directory usando uma ferramenta de provisionamento em massa para configurar os dispositivos. Para obter mais informações, consulte [Registro em massa para dispositivos Windows](/intune/windows-bulk-enroll).
 
Aqui está uma comparação dessas três abordagens 
 
| Elemento | Instalação do autoatendimento | Windows Autopilot | Registro em massa |
| --- | --- | --- | --- |
| Requer interação do usuário para configurar | Sim | Sim | Não |
| Requer trabalho de TI | Não | Sim | Sim |
| Fluxos aplicáveis | OOBE e Configurações | Somente OOBE | Somente OOBE |
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

### <a name="users-may-join-devices-to-azure-ad"></a>Os usuários podem ingressar dispositivos no Azure AD

Defina essa opção como **Todos** ou **Selecionados** com base no escopo de sua implantação e o dispositivo que você deseja permitir a instalação do Azure Active Directory ingressado. 

![Os usuários podem ingressar dispositivos no Azure AD](./media/azureadjoin-plan/01.png)

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

   :::image type="content" source="./media/azureadjoin-plan/04.png" alt-text="Captura de tela da Azure Active Directory adicionar uma página de aplicativo. Vários provedores D m m são listados." border="false":::

Selecione seu provedor de MDM para definir as configurações relacionadas. 

### <a name="mdm-user-scope"></a>Escopo do usuário do MDM

Selecione **Alguns** ou **Todos** com base no escopo de sua implantação. 

![Escopo do usuário do MDM](./media/azureadjoin-plan/05.png)

Com base no seu escopo, acontecerá o seguinte: 

- **Usuário está no escopo do MDM**: se você tiver uma assinatura do Azure Active Directory Premium, o registro do MDM é automatizado, juntamente com o ingresso no Azure Active Directory. Todos os usuários com escopo devem ter uma licença apropriada para seu MDM. Se o registro do MDM falhar nesse cenário, ingresso no Azure Active Directory também será revertido novamente.
- **Usuário não está no escopo do MDM**: se os usuários não estão no escopo do MDM, ingresso no Azure Active Directory é concluído sem qualquer registro de MDM. Isso resulta em um dispositivo não gerenciado.

### <a name="mdm-urls"></a>URLs do MDM

Há três URLs que estão relacionadas à sua configuração de MDM:

- Termos de MDM de uso URL
- URL de descoberta de MDM 
- URL de conformidade de MDM

:::image type="content" source="./media/azureadjoin-plan/06.png" alt-text="Captura de tela de parte da seção de configuração Azure Active Directory M D M, com campos U R L para os termos de uso, descoberta e conformidade de M D m." border="false":::

Cada URL tem um valor padrão predefinido. Se esses campos estiverem vazios, entre em contato com seu provedor de MDM para obter mais informações.

### <a name="mam-settings"></a>Configurações de MAM

MAM não é aplicável ao ingresso no Azure Active Directory. 

## <a name="configure-enterprise-state-roaming"></a>Configurar o roaming de estado

Se você quiser habilitar o roaming de estado para o Azure Active Directory para que os usuários podem sincronizar suas configurações entre dispositivos, consulte [habilitar o Enterprise State Roaming no Azure Active Directory](enterprise-state-roaming-enable.md). 

**Recomendação**: habilite esta configuração mesmo para os dispositivos do Azure Active Directory híbrido ingressado.

## <a name="configure-conditional-access"></a>Configurar acesso condicional

Se você tiver um provedor MDM configurado para os dispositivos Azure Active Directory ingressados, o provedor sinaliza o dispositivo como conformidade assim que o dispositivo está sob gerenciamento. 

![Dispositivo em conformidade](./media/azureadjoin-plan/46.png)

Você pode usar essa implementação para [exigir dispositivos gerenciados para acesso de aplicativo de nuvem com acesso condicional](../conditional-access/require-managed-devices.md).

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Ingressar em um novo dispositivo Windows 10 com o Azure ad durante uma primeira execução](azuread-joined-devices-frx.md) 
>  [Ingresse seu dispositivo de trabalho na rede da sua organização](../user-help/user-help-join-device-on-network.md)

<!--Image references-->
[1]: ./media/azureadjoin-plan/12.png
