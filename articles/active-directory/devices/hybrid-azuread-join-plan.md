---
title: Planejar junção de Azure Active Directory híbrida-Azure Active Directory
description: Saiba como configurar dispositivos adicionados ao Azure Active Directory híbrido.
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
ms.openlocfilehash: 0c4ed5dfee80c33009874361ae6b4d23ec00bc26
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99573323"
---
# <a name="how-to-plan-your-hybrid-azure-active-directory-join-implementation"></a>Como planejar sua implementação de junção do Azure Active Directory híbrido

De maneira semelhante a um usuário, um dispositivo é outra identidade principal que você deseja proteger e usá-la para proteger seus recursos a qualquer hora e em qualquer local. É possível atingir essa meta colocando e gerenciando identidades de dispositivo no Azure AD usando um dos métodos a seguir:

- Ingresso no Azure AD
- Ingresso no Azure AD Híbrido
- Registro do Azure AD

Ao colocar os dispositivos no Azure AD, você maximiza a produtividade dos usuários por meio de SSO (logon único) em toda a nuvem e recursos locais. Ao mesmo tempo, você pode proteger o acesso a seus recursos locais e de nuvem com [acesso condicional](../conditional-access/overview.md).

Se você tiver um ambiente de Active Directory local (AD) e desejar ingressar seus computadores ingressados no domínio do AD no Azure AD, poderá fazer isso fazendo uma junção híbrida do Azure AD. Este artigo fornece as etapas relacionadas para implementar uma associação híbrida do Azure AD em seu ambiente. 

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que você esteja familiarizado com a [introdução ao gerenciamento de identidade do dispositivo no Azure Active Directory](./overview.md).

> [!NOTE]
> A versão mínima necessária do controlador de domínio para o ingresso no Azure AD híbrido do Windows 10 é o Windows Server 2008 R2.

## <a name="plan-your-implementation"></a>Planejar sua implementação

Para planejar sua implementação híbrida do AD do Azure, você deve se familiarizar com:

> [!div class="checklist"]
> - Dispositivos com suporte de revisão
> - Você deve saber de coisas de revisão
> - Examinar a validação controlada do ingresso no Azure AD híbrido
> - Selecione seu cenário com base na sua infraestrutura de identidade
> - Examine o suporte de UPN do AD local para ingressar no Azure AD híbrido

## <a name="review-supported-devices"></a>Dispositivos com suporte de revisão

Ingresso no Azure AD híbrido oferece suporte a dispositivos de uma ampla variedade de Windows. Como a configuração para dispositivos que executam versões mais antigas do Windows requer etapas adicionais ou diferentes, os dispositivos com suporte são agrupados em duas categorias:

### <a name="windows-current-devices"></a>Dispositivos atuais do Windows

- Windows 10
- Windows Server 2016
  - **Observação**: os clientes de nuvem nacional do Azure requerem a versão 1803
- Windows Server 2019

Para dispositivos que executam o sistema operacional Windows desktop, a versão com suporte está listada neste artigo [informações de versão do Windows 10](/windows/release-information/). Como prática recomendada, a Microsoft recomenda que você atualize para a versão mais recente do Windows 10.

### <a name="windows-down-level-devices"></a>Dispositivos de nível inferior do Windows

- Windows 8.1
- O suporte do Windows 7 foi encerrado em 14 de janeiro de 2020. Para obter mais informações, consulte o [suporte para o Windows 7 terminou](https://support.microsoft.com/en-us/help/4057281/windows-7-support-ended-on-january-14-2020).
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2. Para obter informações de suporte sobre o Windows Server 2008 e 2008 R2, consulte [preparar para o Windows server 2008 fim do suporte](https://www.microsoft.com/cloud-platform/windows-server-2008).

Como primeira etapa do planejamento, você deve revisar seu ambiente e determinar se precisa dar suporte a dispositivos de baixo nível do Windows.

## <a name="review-things-you-should-know"></a>Você deve saber de coisas de revisão

### <a name="unsupported-scenarios"></a>Cenários sem suporte
- No momento, não há suporte para o ingresso do Azure AD híbrido se seu ambiente consistir em uma única floresta do AD Sincronizando dados de identidade para mais de um locatário do Azure AD.

- O ingresso no Azure AD híbrido não tem suporte no Windows Server que executa a função de controlador de domínio (DC).

- O ingresso no Azure AD híbrido não tem suporte em dispositivos de nível inferior do Windows ao usar a mobilidade de credencial ou o perfil obrigatório ou de roaming de perfil do usuário.

- O sistema operacional Server Core não dá suporte a nenhum tipo de registro de dispositivo.

- A Ferramenta de Migração do Usuário (USMT) não funciona com o registro de dispositivo.  

### <a name="os-imaging-considerations"></a>Considerações sobre imagens de so
- Se você estiver contando com a ferramenta de preparação do sistema (Sysprep) e se estiver usando uma imagem **anterior ao Windows 10 1809** para instalação, verifique se a imagem não é de um dispositivo que já está registrado com o Azure ad como uma junção híbrida do Azure AD.

- Se você estiver contando com um instantâneo de VM (máquina virtual) para criar VMs adicionais, verifique se o instantâneo não é de uma VM que já está registrada com o Azure AD como uma junção híbrida do Azure AD.

- Se você estiver usando o [filtro de gravação unificado](/windows-hardware/customize/enterprise/unified-write-filter) e tecnologias semelhantes que desmarcam alterações no disco na reinicialização, eles deverão ser aplicados depois que o dispositivo for ingressado no Azure ad híbrido. A habilitação dessas tecnologias antes da conclusão da junção híbrida do Azure AD resultará na injunção do dispositivo em cada reinicialização

### <a name="handling-devices-with-azure-ad-registered-state"></a>Manipulando dispositivos com o estado registrado do Azure AD
Se seus dispositivos ingressados no domínio do Windows 10 forem [registrados no Azure ad](overview.md#getting-devices-in-azure-ad) para seu locatário, isso poderá levar a um estado duplo de ingressado no Azure ad híbrido e ao dispositivo registrado do Azure AD. É recomendável atualizar para o Windows 10 1803 (com o KB4489894 aplicado) ou superior para resolver esse cenário automaticamente. Em versões anteriores a 1803, você precisará remover manualmente o estado registrado do Azure AD antes de habilitar a junção híbrida do Azure AD. Nas versões 1803 e acima, as seguintes alterações foram feitas para evitar esse estado duplo:

- Qualquer estado registrado do Azure AD existente para um usuário seria removido automaticamente <i>depois que o dispositivo for ingressado no Azure ad híbrido e o mesmo usuário fizer logon</i>. Por exemplo, se o usuário A tiver um estado registrado do Azure AD no dispositivo, o estado duplo para o usuário A será limpo somente quando o usuário A fizer logon no dispositivo. Se houver vários usuários no mesmo dispositivo, o estado duplo será limpo individualmente quando esses usuários fizerem logon. Além de remover o estado registrado do Azure AD, o Windows 10 também cancelará o registro do dispositivo do Intune ou de outro MDM, se o registro tiver ocorrido como parte do registro do Azure AD por meio do registro automático.
- Você pode impedir que o dispositivo ingressado no domínio seja registrado no Azure AD adicionando o seguinte valor de registro a HKLM\SOFTWARE\Policies\Microsoft\Windows\WorkplaceJoin: "BlockAADWorkplaceJoin" = DWORD: 00000001.
- No Windows 10 1803, se você tiver o Windows Hello para empresas configurado, o usuário precisará reinstalar o Windows Hello para empresas após a limpeza do estado duplo. Esse problema foi resolvido com o KB4512509

> [!NOTE]
> Embora o Windows 10 Remova automaticamente o estado registrado do Azure AD localmente, o objeto de dispositivo no Azure AD não será excluído imediatamente se for gerenciado pelo Intune. Você pode validar a remoção do estado registrado do Azure AD executando dsregcmd/status e considerar que o dispositivo não seja registrado no AD do Azure com base nele.

### <a name="additional-considerations"></a>Considerações adicionais
- Se seu ambiente usa VDI (Virtual Desktop Infrastructure), consulte [identidade do dispositivo e virtualização de área de trabalho](./howto-device-identity-virtual-desktop-infrastructure.md).

- O ingresso no Azure AD híbrido tem suporte para TPM 2,0 compatível com FIPS e sem suporte para TPM 1,2. Se seus dispositivos tiverem o TPM 1,2 compatível com FIPS, você deverá desabilitá-los antes de prosseguir com a junção híbrida do Azure AD. A Microsoft não fornece nenhuma ferramenta para desabilitar o modo FIPS para TPMs, pois depende do fabricante do TPM. Entre em contato com o OEM de hardware para obter suporte. 

- A partir da versão 10 1903 do Windows, TPMs 1,2 não são usadas com o ingresso híbrido do Azure AD e os dispositivos com esses TPMs serão considerados como se não tivessem um TPM.

- As alterações de UPN só têm suporte iniciando a atualização do Windows 10 2004. Para dispositivos anteriores à atualização do Windows 10 2004, os usuários teriam problemas de acesso condicional e de SSO em seus dispositivos. Para resolver esse problema, você precisa desassociar o dispositivo do Azure AD (execute "dsregcmd/Leave" com privilégios elevados) e reingresse (ocorre automaticamente). No entanto, os usuários que entram no Windows Hello para Empresas não enfrentam esse problema.

## <a name="review-controlled-validation-of-hybrid-azure-ad-join"></a>Examinar a validação controlada do ingresso no Azure AD híbrido

Quando todos os pré-requisitos estiverem em vigor, os dispositivos Windows serão automaticamente registrados como dispositivos no seu locatário do Azure AD. O estado dessas identidades de dispositivo no Azure AD é chamado de junção híbrida do Azure AD. Mais informações sobre os conceitos abordados neste artigo podem ser encontradas no artigo [introdução ao gerenciamento de identidade do dispositivo no Azure Active Directory](overview.md).

As organizações podem querer fazer uma validação controlada do ingresso no Azure AD híbrido antes de habilitá-lo em toda a organização de uma só vez. Examine o artigo [validação controlada do ingresso no Azure ad híbrido](hybrid-azuread-join-control.md) para entender como realizá-lo.

## <a name="select-your-scenario-based-on-your-identity-infrastructure"></a>Selecione seu cenário com base na sua infraestrutura de identidade

O ingresso no Azure AD híbrido funciona com ambientes gerenciados e federados dependendo se o UPN é roteável ou não roteável. Veja a parte inferior da página para ver a tabela em cenários com suporte.  

### <a name="managed-environment"></a>Ambiente de leitura

Um ambiente gerenciado pode ser implantado por meio da [PHS (Sincronização de Hash de Senha)](../hybrid/whatis-phs.md) ou [PTA (Autenticação de Passagem)](../hybrid/how-to-connect-pta.md) com [Logon Único Contínuo](../hybrid/how-to-connect-sso.md).

Esses cenários não exigem que você configure um servidor de federação para autenticação.

> [!NOTE]
> A [autenticação de nuvem usando a distribuição em etapas](../hybrid/how-to-connect-staged-rollout.md) só tem suporte iniciando a atualização do Windows 10 1903

### <a name="federated-environment"></a>Ambiente federado

Um ambiente federado deve ter um provedor de identidade que dá suporte aos requisitos a seguir. Se você tem um ambiente federado usando o AD FS (Serviços de Federação do Active Directory), os requisitos abaixo já são compatíveis.

- **Declaração WIAORMULTIAUTHN:** Essa declaração é necessária para fazer o ingresso de dispositivos de nível inferior do Windows no Azure AD híbrido.
- **Protocolo WS-Trust:** Esse protocolo é um requisito para autenticar os dispositivos atuais do Windows ingressados no Azure AD híbrido com o Azure AD. Quando você estiver usando o AD FS, será necessário habilitar os seguintes pontos de extremidade WS-Trust: `/adfs/services/trust/2005/windowstransport`  
`/adfs/services/trust/13/windowstransport`  
  `/adfs/services/trust/2005/usernamemixed` 
  `/adfs/services/trust/13/usernamemixed`
  `/adfs/services/trust/2005/certificatemixed` 
  `/adfs/services/trust/13/certificatemixed` 

> [!WARNING] 
> O **adfs/services/trust/2005/windowstransport** e também o **adfs/services/trust/13/windowstransport** devem ser habilitados como pontos de extremidade voltados para a intranet e NÃO devem ser expostos como pontos de extremidade voltados a uma extranet por meio do proxy de aplicativo Web. Para saber mais sobre como desabilitar os pontos de extremidade do Windows do WS-Trust, confira [Desabilitar pontos de extremidade do Windows do WS-Trust no proxy](/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs#disable-ws-trust-windows-endpoints-on-the-proxy-ie-from-extranet). Veja quais pontos de extremidade estão habilitados por meio do console de gerenciamento do AD FS em **Serviço** > **Pontos de extremidade**.

> [!NOTE]
> O Azure AD não dá suporte a cartões inteligentes ou certificados em domínios gerenciados.

A partir da versão 1.1.819.0, o Azure AD Connect fornece um assistente para configurar o ingresso no Azure AD híbrido. O assistente permite simplificar significativamente o processo de configuração. Se a instalação da versão necessária do Azure AD Connect não for uma opção, consulte [como configurar manualmente o registro do dispositivo](hybrid-azuread-join-manual.md). 

Com base no cenário que corresponde à sua infraestrutura de identidade, consulte:

- [Configurar a junção de Azure Active Directory híbrida para o ambiente federado](hybrid-azuread-join-federated-domains.md)
- [Configurar a junção de Azure Active Directory híbrida para o ambiente gerenciado](hybrid-azuread-join-managed-domains.md)

## <a name="review-on-premises-ad-users-upn-support-for-hybrid-azure-ad-join"></a>Examinar o suporte a UPN de usuários do AD local para ingressar no Azure AD híbrido

Às vezes, os UPNs dos usuários do AD local podem ser diferentes dos UPNs do Azure AD. Nesses casos, o ingresso no Azure AD Híbrido do Windows 10 dá suporte limitado aos UPNs do AD local com base no [método de autenticação](../hybrid/choose-ad-authn.md), no tipo de domínio e na versão do Windows 10. Há dois tipos de UPNs do AD local que podem existir em seu ambiente:

- UPN de usuários roteáveis: um UPN roteável tem um domínio verificado válido, que é registrado com um registrador de domínios. Por exemplo, se contoso.com é o domínio primário do Azure AD, contoso.org é o domínio primário no AD local pertencente à Contoso e [verificado no Azure AD](../fundamentals/add-custom-domain.md)
- UPN de usuários não roteáveis: um UPN não roteável não tem um domínio verificado. É aplicável somente dentro da rede privada da sua organização. Por exemplo, se contoso.com é o domínio primário no Azure AD, contoso.local é o domínio primário no AD local, mas não é um domínio verificável na Internet e é usado apenas na rede da Contoso.

> [!NOTE]
> As informações nesta seção aplicam-se somente a um UPN de usuários locais. Ele não é aplicável a um sufixo de domínio de computador local (exemplo: Computador1. contoso. local).

A tabela a seguir fornece detalhes sobre o suporte a esses UPNs do AD local no ingresso no Azure AD Híbrido do Windows 10

| Tipo de UPN do AD local | Tipo de domínio | Versão do Windows 10 | Descrição |
| ----- | ----- | ----- | ----- |
| Roteável | Federado | Da versão 1703 | Disponível para o público geral |
| Não roteável | Federado | Da versão 1803 | Disponível para o público geral |
| Roteável | Gerenciados | Da versão 1803 | Em geral, não há suporte para o SSPR do Azure AD na tela de bloqueio do Windows |
| Não roteável | Gerenciados | Sem suporte | |

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Configurar a junção de Azure Active Directory híbrida para o ambiente federado](hybrid-azuread-join-federated-domains.md) 
>  [Configurar a junção de Azure Active Directory híbrida para o ambiente gerenciado](hybrid-azuread-join-managed-domains.md)

<!--Image references-->
[1]: ./media/hybrid-azuread-join-plan/12.png
