---
title: Como planejar uma implementação de ingresso do Azure Active Directory híbrido no Azure Active Directory | Microsoft Docs
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
ms.openlocfilehash: abb050eb527e65b4fd31f3251d37fef7d51e867e
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67655971"
---
# <a name="how-to-plan-your-hybrid-azure-active-directory-join-implementation"></a>Como: Planejar a sua implementação do ingresso do Azure Active Directory híbrido

De maneira semelhante a um usuário, um dispositivo é outra identidade principal que você deseja proteger e usá-la para proteger seus recursos a qualquer hora e em qualquer local. É possível atingir essa meta colocando e gerenciando identidades de dispositivo no Azure AD usando um dos métodos a seguir:

- Ingresso no Azure AD
- Ingresso no Azure AD Híbrido
- Registro do Azure AD

Ao colocar os dispositivos no Azure AD, você maximiza a produtividade dos usuários por meio de SSO (logon único) em toda a nuvem e recursos locais. Ao mesmo tempo, você pode proteger o acesso aos seus recursos de nuvem e locais com [acesso condicional](../active-directory-conditional-access-azure-portal.md).

Se você tiver um ambiente do Active Directory (AD) local e você deseja ingressar os computadores ingressados no domínio do AD para o Azure AD, você pode fazer isso seguindo o ingresso no Azure AD híbrido. Este artigo fornece as etapas relacionadas para implementar uma associação híbrida do Azure AD em seu ambiente. 

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que você esteja familiarizado com o [Introdução ao gerenciamento de identidades de dispositivo no Azure Active Directory](../device-management-introduction.md).

> [!NOTE]
> O mínimo necessário funcional de domínio e níveis funcionais de floresta para ingresso no Azure AD Híbrido Windows 10 é o Windows Server 2008 R2.

## <a name="plan-your-implementation"></a>Planejar sua implementação

Para planejar sua implementação híbrida do AD do Azure, você deve se familiarizar com:

|   |   |
| --- | --- |
| ![Verificação][1] | Dispositivos com suporte de revisão |
| ![Verificação][1] | Você deve saber de coisas de revisão |
| ![Verificação][1] | Examine a validação controlada de junção do Azure AD híbrido |
| ![Verificação][1] | Selecione o seu cenário com base em sua infraestrutura de identidade |
| ![Verificação][1] | Revisão local AD UPN dão suporte para híbrido, ingresso no Azure AD |

## <a name="review-supported-devices"></a>Dispositivos com suporte de revisão

Ingresso no Azure AD híbrido oferece suporte a dispositivos de uma ampla variedade de Windows. Como a configuração para dispositivos que executam versões mais antigas do Windows requer etapas adicionais ou diferentes, os dispositivos com suporte são agrupados em duas categorias:

### <a name="windows-current-devices"></a>Dispositivos atuais do Windows

- Windows 10
- Windows Server 2016
- Windows Server 2019

Para dispositivos que executam o sistema operacional da área de trabalho do Windows, versão com suporte estão listados neste artigo [informações de versão do Windows 10](https://docs.microsoft.com/windows/release-information/). Como prática recomendada, a Microsoft recomenda que você atualize para a versão mais recente do Windows 10.

### <a name="windows-down-level-devices"></a>Dispositivos de nível inferior do Windows

- Windows 8.1
- Windows 7. Para obter informações de suporte no Windows 7, leia este artigo [terminará o suporte para o Windows 7](https://www.microsoft.com/en-us/windowsforbusiness/end-of-windows-7-support)
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2

Como primeira etapa do planejamento, você deve revisar seu ambiente e determinar se precisa dar suporte a dispositivos de baixo nível do Windows.

## <a name="review-things-you-should-know"></a>Você deve saber de coisas de revisão

Ingresso no Azure AD híbrido não é suportado atualmente se seu ambiente consistir em uma única floresta do AD sincronizando dados de identidade com mais de um locatário do AD do Azure.

Ingresso no Azure AD híbrido não tem suporte atualmente ao usar a infraestrutura da área de trabalho virtual (VDI).

Não há suporte para o ingresso no Azure AD híbrido para TPMs compatíveis com FIPS. Se os dispositivos têm TPMs compatíveis com FIPS, você deve desabilitá-los antes de prosseguir com a junção do Azure AD híbrido. Microsoft não fornece as ferramentas para desabilitar o modo FIPS para TPMs conforme ele é dependente do fabricante do TPM. Entre em contato com seu hardware OEM para obter suporte.

Não há suporte para o ingresso no Azure AD híbrido para o Windows Server que executa a função de controlador de domínio (DC).

Não há suporte para ingresso no Azure AD híbrido em dispositivos de nível inferior do Windows ao usar a mobilidade de credenciais ou o perfil de usuário móvel.

Se você está confiando na ferramenta de preparação do sistema (Sysprep) e se você estiver usando um **anteriores ao Windows 10 1809** de imagem para a instalação, verifique se essa imagem não for de um dispositivo que já está registrado com o Azure AD como o ingresso no Azure AD híbrido.

Se você depender de um instantâneo de máquina Virtual (VM) para criar VMs adicionais, verifique se que esse instantâneo não é de uma VM que já está registrada com o Azure AD como o ingresso no Azure AD híbrido.

Se os dispositivos incluídos no domínio do Windows 10 já estiverem [registrados pelo Azure AD](https://docs.microsoft.com/azure/active-directory/devices/overview#getting-devices-in-azure-ad) em seu locatário, recomendamos remover esse estado antes de habilitar o ingresso no Azure AD Híbrido. No Windows 10 versão 1809, as seguintes alterações foram feitas para evitar esse estado duplo:

- Qualquer estado existente registrado pelo Azure Active Directory será automaticamente removido depois que os dispositivos forem incluídos no Azure Active Directory Híbrido.
- Você pode impedir que o dispositivo ingressado no domínio que está sendo o Azure AD registrado ao adicionar essa chave do registro - HKLM\SOFTWARE\Policies\Microsoft\Windows\WorkplaceJoin, "BlockAADWorkplaceJoin" = DWORD: 00000001.
- Essa alteração está agora disponível para a versão do Windows 10 1803 com KB4489894 aplicado. No entanto, se você tiver Windows Hello para empresas configurada, o usuário é necessário para re-instalação do Windows Hello para empresas após o estado de duplo limpar.

## <a name="review-controlled-validation-of-hybrid-azure-ad-join"></a>Examine a validação controlada de junção do Azure AD híbrido

Quando todos os pré-requisitos estão em vigor, os dispositivos do Windows serão registrados automaticamente como dispositivos em seu locatário do Azure AD. O estado dessas identidades de dispositivo no Azure AD é conhecido como ingresso no Azure AD híbrido. Para obter mais informações sobre os conceitos abordados neste artigo podem ser encontradas nos artigos [Introdução ao gerenciamento de identidades de dispositivo no Azure Active Directory](overview.md) e [planejar seu ingresso no Azure Active Directory híbrido implementação](hybrid-azuread-join-plan.md).

As organizações talvez queira fazer uma validação controlada de ingresso no Azure AD híbrido antes de habilitá-la em toda a sua organização ao mesmo tempo. Examine o artigo [controlado de validação de associação do Azure AD híbrido](hybrid-azuread-join-control.md) para entender como realizá-la.

## <a name="select-your-scenario-based-on-your-identity-infrastructure"></a>Selecione o seu cenário com base em sua infraestrutura de identidade

Ingresso híbrido do Azure AD funciona com os ambientes, gerenciados e federados.  

### <a name="managed-environment"></a>Ambiente de leitura

Um ambiente gerenciado pode ser implantado por meio da [PHS (Sincronização de Hash de Senha)](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-phs) ou [PTA (Autenticação de Passagem)](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta) com [Logon Único Contínuo](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso).

Esses cenários não exigem que você configure um servidor de federação para autenticação.

### <a name="federated-environment"></a>Ambiente federado

Um ambiente federado deve ter um provedor de identidade que suporta os seguintes requisitos:

- **Protocolo WS-Trust:** Esse protocolo é necessário para autenticação do Windows atual híbrida do Azure AD com dispositivos ingressados no Azure AD.
- **WIAORMULTIAUTHN de declaração:** Essa declaração é necessária fazer o ingresso no Azure AD híbrido para dispositivos de nível inferior do Windows.

Se você tiver um ambiente federado, usando os serviços de Federação do Active Directory (AD FS), os requisitos acima já têm suporte.

> [!NOTE]
> O Azure AD não dá suporte a cartões inteligentes ou certificados em domínios gerenciados.

A partir da versão 1.1.819.0, o Azure AD Connect fornece um assistente para configurar o ingresso no Azure AD híbrido. O assistente permite simplificar significativamente o processo de configuração. Se a instalação da versão necessária do Azure AD Connect não for uma opção, consulte [como configurar manualmente o registro do dispositivo](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-manual). 

Com base no cenário que corresponda à sua infraestrutura de identidade, consulte:

- [Configurar o ingresso no Azure Active Directory híbrido para o ambiente federado](hybrid-azuread-join-federated-domains.md)
- [Configurar o ingresso no Azure Active Directory híbrido para ambiente gerenciado](hybrid-azuread-join-managed-domains.md)

## <a name="review-on-premises-ad-upn-support-for-hybrid-azure-ad-join"></a>Revisão de suporte de UPN do AD para o ingresso no Azure AD híbrido no local

Às vezes, seus UPNs do AD local podem ser diferentes dos UPNs do Azure AD. Nesses casos, o ingresso no Azure AD Híbrido do Windows 10 dá suporte limitado aos UPNs do AD local com base no [método de autenticação](https://docs.microsoft.com/azure/security/azure-ad-choose-authn), no tipo de domínio e na versão do Windows 10. Há dois tipos de UPNs do AD local que podem existir em seu ambiente:

- UPN roteável: um nome UPN roteável tem um domínio verificado válido, que é registrado em um registrador de domínios. Por exemplo, se contoso.com é o domínio primário do Azure AD, contoso.org é o domínio primário no AD local pertencente à Contoso e [verificado no Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain)
- UPN não roteável: um UPN não roteável não tem um domínio verificado. É aplicável somente dentro da rede privada da sua organização. Por exemplo, se contoso.com é o domínio primário no Azure AD, contoso.local é o domínio primário no AD local, mas não é um domínio verificável na Internet e é usado apenas na rede da Contoso.

A tabela a seguir fornece detalhes sobre o suporte a esses UPNs do AD local no ingresso no Azure AD Híbrido do Windows 10

| Tipo de UPN do AD local | Tipo de domínio | Versão do Windows 10 | DESCRIÇÃO |
| ----- | ----- | ----- | ----- |
| Roteável | Federado | Da versão 1703 | Disponível para o público geral |
| Não roteável | Federado | Da versão 1803 | Disponível para o público geral |
| Roteável | Gerenciada | Sem suporte | |
| Não roteável | Gerenciada | Sem suporte | |

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Configurar ingresso no Azure Active Directory híbrido para o ambiente federado](hybrid-azuread-join-federated-domains.md)
> [configurar ingresso no Azure Active Directory híbrido para ambiente gerenciado](hybrid-azuread-join-managed-domains.md)

<!--Image references-->
[1]: ./media/hybrid-azuread-join-plan/12.png
