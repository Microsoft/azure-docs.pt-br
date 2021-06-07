---
title: Planejar a implantação do dispositivo Azure Active Directory
description: Escolha as estratégias de integração de dispositivos do Azure AD que atendam às suas necessidades organizacionais.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 06/15/2020
ms.author: baselden
author: BarbaraSelden
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: fc631e4329c1df2cdcbfe57c2b43ccccf14afa85
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97936440"
---
# <a name="plan-your-azure-active-directory-device-deployment"></a>Planejar a implantação do dispositivo Azure Active Directory

Este artigo ajuda você a avaliar os métodos para integrar seu dispositivo ao Azure AD, escolher o plano de implementação e fornece links de chave para as ferramentas de gerenciamento de dispositivos com suporte.

O panorama dos dispositivos dos quais seus usuários se conectam está se expandindo. As organizações podem fornecer desktops, laptops, telefones, tablets e outros dispositivos. Os usuários podem trazer sua própria matriz de dispositivos e acessar informações de locais variados. Nesse ambiente, seu trabalho como administrador é manter os recursos organizacionais protegidos em todos os dispositivos.

O Azure Active Directory (AD do Azure) permite que sua organização atenda a essas metas com o gerenciamento de identidade do dispositivo. Agora você pode obter seus dispositivos no Azure AD e controlá-los de um local central no [portal do Azure](https://portal.azure.com/). Isso oferece uma experiência unificada, segurança aprimorada e reduz o tempo necessário para configurar um novo dispositivo.

Há vários métodos para integrar seus dispositivos ao Azure AD:

* Você pode [registrar dispositivos com o](concept-azure-ad-register.md) Azure AD

* [Unir dispositivos](concept-azure-ad-join.md) ao Azure AD (somente em nuvem) ou

* [Crie uma junção híbrida do Azure ad](concept-azure-ad-join-hybrid.md) entre dispositivos no seu local Active Directory e no Azure AD. 

## <a name="learn"></a>Learn

Antes de começar, verifique se você está familiarizado com a [visão geral do gerenciamento de identidade do dispositivo](overview.md).

### <a name="benefits"></a>Benefícios

Os principais benefícios de fornecer a seus dispositivos uma identidade do Azure AD:

* Aumentar a produtividade – com o Azure AD, os usuários podem fazer [logon contínuo (SSO)](./azuread-join-sso.md) para seus recursos locais e na nuvem, o que permite que eles sejam produtivos onde quer que estejam.

* Aumentar a segurança – os dispositivos do Azure AD permitem que você aplique [políticas de acesso condicional](../conditional-access/require-managed-devices.md) a recursos com base na identidade do dispositivo ou usuário. As políticas de acesso condicional podem oferecer proteção extra usando [Azure ad Identity Protection](../identity-protection/overview-identity-protection.md). Ingressar um dispositivo no Azure AD é um pré-requisito para aumentar sua segurança com uma estratégia de [autenticação sem senha](../authentication/concept-authentication-passwordless.md) .

* Melhorar a experiência do usuário – com identidades de dispositivo no Azure AD, você pode fornecer aos usuários acesso fácil aos recursos baseados em nuvem de sua organização de dispositivos pessoais e corporativos. Os administradores podem habilitar a [Enterprise State roaming](enterprise-state-roaming-overview.md) para uma experiência unificada em todos os dispositivos Windows.

* Simplifique a implantação e o gerenciamento – o gerenciamento de identidades de dispositivo simplifica o processo de colocar dispositivos no Azure AD com o [Windows AutoPilot](/windows/deployment/windows-autopilot/windows-10-autopilot), [provisionamento em massa](/mem/intune/enrollment/windows-bulk-enroll)e [autoatendimento: OOBE (configuração inicial pelo uso)](../user-help/user-help-join-device-on-network.md). Você pode gerenciar esses dispositivos com ferramentas de MDM (gerenciamento de dispositivo móvel), como [Microsoft Intune](/mem/intune/fundamentals/what-is-intune), e suas identidades no [portal do Azure](https://portal.azure.com/).

### <a name="training-resources"></a>Recursos de treinamento

Vídeo:  [acesso condicional com controles de dispositivo](https://youtu.be/NcONUf-jeS4)

Perguntas frequentes: perguntas [frequentes sobre gerenciamento de dispositivo do Azure ad](faq.md)  e [perguntas frequentes sobre roaming de dados](enterprise-state-roaming-faqs.md) 

## <a name="plan-the-deployment-project"></a>Planejar o projeto de implantação

Considere suas necessidades organizacionais enquanto determina a estratégia para essa implantação em seu ambiente.

### <a name="engage-the-right-stakeholders"></a>Envolva os participantes certos

Quando os projetos de tecnologia falham, eles normalmente fazem isso devido a expectativas incompatíveis com o impacto, os resultados e as responsabilidades. Para evitar essas armadilhas, [Verifique se você está participando dos participantes certos](../fundamentals/active-directory-deployment-plans.md) e se as funções de Stakeholder no projeto são bem compreendidas. 

Para este plano, adicione os seguintes participantes à sua lista:

| Função| Descrição |
| - | - |
| Administrador do dispositivo| Um representante da equipe do dispositivo que pode verificar se o plano atenderá aos requisitos do dispositivo de sua organização. |
| Administrador de rede| Um representante da equipe de rede que pode se certificar de atender aos requisitos de rede. |
| Equipe de gerenciamento de dispositivos| Equipe que gerencia o inventário de dispositivos. |
| Equipes de administração específicas do sistema operacional| Equipes que dão suporte e gerenciam versões específicas do sistema operacional. Por exemplo, pode haver uma equipe focada em Mac ou iOS. |

### <a name="plan-communications"></a>Planejar comunicações

A comunicação é fundamental para o sucesso de qualquer novo serviço. Comunique de forma proativa a seus usuários como e quando sua experiência será alterada e como obter suporte em caso de problemas.

### <a name="plan-a-pilot"></a>Planejar um piloto

É recomendável que a configuração inicial do seu método de integração esteja em um ambiente de teste ou com um pequeno grupo de dispositivos de teste. Consulte [Melhores práticas para obter um piloto](../fundamentals/active-directory-deployment-plans.md).

A implantação de ingresso no Azure AD híbrido é simples e é 100% da tarefa de um administrador sem a necessidade de ação do usuário final. Talvez você queira fazer uma [validação controlada do ingresso no Azure ad híbrido](hybrid-azuread-join-control.md) antes de habilitá-lo em toda a organização de uma só vez.

## <a name="choose-your-integration-methods"></a>Escolha seus métodos de integração

Sua organização pode usar vários métodos de integração de dispositivos em um único locatário do Azure AD. O objetivo é escolher os métodos adequados para que seus dispositivos sejam gerenciados com segurança no Azure AD. Há muitos parâmetros que orientam essa decisão, incluindo a propriedade, os tipos de dispositivo, o público principal e a infraestrutura da sua organização.

As informações a seguir podem ajudá-lo a decidir quais métodos de integração usar.

### <a name="decision-tree-for-devices-integration"></a>Árvore de decisão para integração de dispositivos

Use esta árvore para determinar as opções para dispositivos de propriedade da organização. 

> [!NOTE]
> Cenários pessoais ou BYOD (Traga seu próprio dispositivo) não são mostrados neste diagrama. Eles sempre resultam no registro do Azure AD.

 ![Árvore de decisão](./media/plan-device-deployment/flowchart.png)

### <a name="comparison-matrix"></a>Matriz de comparação

dispositivos iOS e Android só podem ser registrados no AD do Azure. A tabela a seguir apresenta considerações de alto nível para dispositivos cliente do Windows. Use-o como uma visão geral e explore os métodos de integração diferentes detalhadamente.

| Consideração | Azure AD registrado| Ingresso no Azure AD| Ingresso no Azure AD Híbrido |
| - | - | - | - |
| **Sistemas operacionais cliente**| | |  |
| Dispositivos Windows 10| ![Marca de seleção para esses valores.](./media/plan-device-deployment/check.png)| ![Marca de seleção para esses valores.](./media/plan-device-deployment/check.png)| ![Marca de seleção para esses valores.](./media/plan-device-deployment/check.png) |
| Dispositivos de nível inferior do Windows (Windows 8.1 ou Windows 7)| | | ![Marca de seleção para esses valores.](./media/plan-device-deployment/check.png) |
|**Opções de entrada**| | |  |
| Credenciais locais do usuário final| ![Marca de seleção para esses valores.](./media/plan-device-deployment/check.png)| |  |
| Senha| ![Marca de seleção para esses valores.](./media/plan-device-deployment/check.png)| ![Marca de seleção para esses valores.](./media/plan-device-deployment/check.png)| ![Marca de seleção para esses valores.](./media/plan-device-deployment/check.png) |
| PIN do dispositivo| ![Marca de seleção para esses valores.](./media/plan-device-deployment/check.png)| |  |
| Windows Hello| ![Marca de seleção para esses valores.](./media/plan-device-deployment/check.png)| |  |
| Windows Hello para Empresas| | ![Marca de seleção para esses valores.](./media/plan-device-deployment/check.png)| ![Marca de seleção para esses valores.](./media/plan-device-deployment/check.png) |
| Chaves de segurança do FIDO 2,0| | ![Marca de seleção para esses valores.](./media/plan-device-deployment/check.png)| ![Marca de seleção para esses valores.](./media/plan-device-deployment/check.png) |
| Microsoft Authenticator aplicativo (com senha)| ![Marca de seleção para esses valores.](./media/plan-device-deployment/check.png)| ![Marca de seleção para esses valores.](./media/plan-device-deployment/check.png)| ![Marca de seleção para esses valores.](./media/plan-device-deployment/check.png) |
|**Principais funcionalidades**| | |  |
| SSO para recursos de nuvem| ![Marca de seleção para esses valores.](./media/plan-device-deployment/check.png)| ![Marca de seleção para esses valores.](./media/plan-device-deployment/check.png)| ![Marca de seleção para esses valores.](./media/plan-device-deployment/check.png) |
| Recursos de SSO a locais| | ![Marca de seleção para esses valores.](./media/plan-device-deployment/check.png)| ![Marca de seleção para esses valores.](./media/plan-device-deployment/check.png) |
| Acesso condicional <br> (Exigir que os dispositivos sejam marcados como em conformidade) <br> (Deve ser gerenciado pelo MDM)| ![Marca de seleção para esses valores.](./media/plan-device-deployment/check.png) | ![Marca de seleção para esses valores.](./media/plan-device-deployment/check.png)|![Marca de seleção para esses valores.](./media/plan-device-deployment/check.png) |
Acesso condicional <br>(Exigir dispositivos ingressados no Azure AD híbrido)| | | ![Marca de seleção para esses valores.](./media/plan-device-deployment/check.png)
| Tela de logon de autoatendimento de redefinição de senha do Windows| | ![Marca de seleção para esses valores.](./media/plan-device-deployment/check.png)| ![Marca de seleção para esses valores.](./media/plan-device-deployment/check.png) |
| Redefinição de PIN do Windows Hello| | ![Marca de seleção para esses valores.](./media/plan-device-deployment/check.png)| ![Marca de seleção para esses valores.](./media/plan-device-deployment/check.png) |
| Roaming de estado empresarial entre dispositivos| | ![Marca de seleção para esses valores.](./media/plan-device-deployment/check.png)| ![Marca de seleção para esses valores.](./media/plan-device-deployment/check.png) |


## <a name="azure-ad-registration"></a>Registro do Azure AD 

Dispositivos registrados geralmente são gerenciados com [Microsoft Intune](/mem/intune/enrollment/device-enrollment). Os dispositivos são registrados no Intune de várias maneiras, dependendo do sistema operacional. 

Os dispositivos registrados no Azure AD fornecem suporte para BYOD (traga seus próprios dispositivos) e dispositivos corporativos para SSO para recursos de nuvem. O acesso a recursos é baseado nas políticas de [acesso condicional](../conditional-access/require-managed-devices.md) do Azure ad aplicadas ao dispositivo e ao usuário.

### <a name="registering-devices"></a>Registro de dispositivos

Dispositivos registrados geralmente são gerenciados com [Microsoft Intune](/mem/intune/enrollment/device-enrollment). Os dispositivos são registrados no Intune de várias maneiras, dependendo do sistema operacional. 

O BYOD e o dispositivo móvel de propriedade corporativa são registrados por usuários que instalam o aplicativo do portal da empresa.

* [iOS](/mem/intune/user-help/install-and-sign-in-to-the-intune-company-portal-app-ios)

* [Android](/mem/intune/user-help/enroll-device-android-company-portal)

* [Windows 10](/mem/intune/user-help/enroll-windows-10-device)

Se o registro de seus dispositivos for a melhor opção para sua organização, consulte os seguintes recursos:

* Esta visão geral dos [dispositivos registrados no Azure ad](concept-azure-ad-register.md).

* Esta documentação do usuário final sobre como [registrar seu dispositivo pessoal na rede da sua organização](../user-help/user-help-register-device-on-network.md).

## <a name="azure-ad-join"></a>Ingresso no Azure AD

O ingresso no Azure AD permite que você faça a transição para um modelo de nuvem primeiro com o Windows. Ele fornecerá uma ótima base se você estiver planejando modernizar o gerenciamento de dispositivos e reduzir os custos de ti relacionados a dispositivos. O ingresso no Azure AD funciona somente com dispositivos Windows 10. Considere-o como a primeira opção para novos dispositivos.

No entanto, os [dispositivos ingressados no Azure ad podem fazer SSO para recursos locais](azuread-join-sso.md) quando eles estão na rede da organização, podem se autenticar em servidores locais, como arquivos, impressão e outros aplicativos.

Se essa for a melhor opção para sua organização, consulte os seguintes recursos:

* Esta visão geral dos [dispositivos adicionados ao Azure ad](concept-azure-ad-join.md).

* Familiarize-se com o [plano de implementação de ingresso no Azure ad](azureadjoin-plan.md).

### <a name="provisioning-azure-ad-join-to-your-devices"></a>Provisionamento do ingresso no Azure AD em seus dispositivos

Para provisionar o ingresso no Azure AD, você tem as seguintes abordagens:

* Autoatendimento: [experiência de primeira execução do Windows 10](azuread-joined-devices-frx.md)

Se você tiver o Windows 10 Professional ou o Windows 10 Enterprise em um dispositivo, a experiência usará como padrão o processo de instalação de dispositivos de propriedade da empresa.

* [Configurações de OOBE (configuração inicial pelo uso do Windows) ou do Windows](../user-help/user-help-join-device-on-network.md)

* [Windows Autopilot](/windows/deployment/windows-autopilot/windows-autopilot)

* [Registro em massa](/mem/intune/enrollment/windows-bulk-enroll)

Escolha seu procedimento de implantação após [uma comparação](azureadjoin-plan.md)cuidadosa dessas abordagens.

Você pode determinar que o ingresso no Azure AD é a melhor solução para um dispositivo e que o dispositivo pode já estar em Estados diferentes. Aqui estão as considerações de atualização.

| Estado atual do dispositivo| Estado do dispositivo desejado| Como fazer |
| - | - | - |
| Ingressado no domínio local| Ingresso no AD do Azure| Desassociar o dispositivo do domínio local antes de ingressar no Azure AD |
| Ingresso híbrido no Azure AD| Ingresso no AD do Azure| Desassociar o dispositivo do domínio local e do Azure AD antes de ingressar no Azure AD |
| Azure AD registrado| Ingresso no AD do Azure| Cancelar o registro do dispositivo antes de ingressar no Azure AD |


## <a name="hybrid-azure-ad-join"></a>Ingresso no Azure AD Híbrido

Se você tiver um ambiente de Active Directory local e desejar unir seus computadores ingressados no domínio do Active Directory ao Azure AD, poderá fazer isso com o ingresso no Azure AD híbrido. Ele dá suporte a uma [ampla variedade de dispositivos Windows](hybrid-azuread-join-plan.md), incluindo dispositivos Windows atuais e de nível inferior do Windows.

A maioria das organizações já tem dispositivos ingressados no domínio e os gerencia via Política de Grupo ou System Center Configuration Manager (SCCM). Nesse caso, é recomendável configurar o ingresso híbrido do Azure AD para começar a obter benefícios e aproveitar o investimento existente.

Se o ingresso no Azure AD híbrido for a melhor opção para sua organização, consulte os seguintes recursos:

* Esta visão geral dos [dispositivos adicionados ao Azure ad híbrido](concept-azure-ad-join-hybrid.md).

* Familiarize-se com o plano de [implementação de ingresso do Azure ad híbrido](hybrid-azuread-join-plan.md) .

### <a name="provisioning-hybrid-azure-ad-join-to-your-devices"></a>Provisionando o ingresso no Azure AD híbrido em seus dispositivos

[Examine sua infraestrutura de identidade](hybrid-azuread-join-plan.md). Azure AD Connect fornece um assistente para configurar o ingresso no Azure AD híbrido para:

* [Domínios federados](hybrid-azuread-join-federated-domains.md)

* [Domínios gerenciados](hybrid-azuread-join-managed-domains.md)

Se a instalação da versão necessária do Azure AD Connect não for uma opção para você, consulte [como configurar manualmente a junção híbrida do Azure ad](hybrid-azuread-join-manual.md). 

> [!NOTE] 
> O dispositivo Windows 10 ingressado no domínio local tenta ingressar automaticamente no Azure AD para se tornar ingressado no Azure AD híbrido por padrão. Isso só terá sucesso se você tiver configurado o ambiente certo. 

Você pode determinar que o ingresso do Azure AD híbrido é a melhor solução para um dispositivo e que o dispositivo pode já estar em um estado diferente. Aqui estão as considerações de atualização.

| Estado atual do dispositivo| Estado do dispositivo desejado| Como fazer |
| - | - | - |
| Ingresso no domínio local| Ingresso híbrido no Azure AD| Usar o Azure AD Connect ou AD FS para ingressar no Azure |
| Ingressado no grupo de trabalho local ou novo| Ingresso híbrido no Azure AD| Com suporte com o [Windows AutoPilot](/windows/deployment/windows-autopilot/windows-autopilot). Caso contrário, o dispositivo precisa ser ingressado no domínio local antes da junção híbrida do Azure AD |
| Adicionado ao Azure AD| Ingresso híbrido no Azure AD| Desingresse do Azure AD, que o coloca no grupo de trabalho local ou no novo estado. |
| Azure AD registrado| Ingresso híbrido no Azure AD| Depende da versão do Windows. [Veja essas considerações](hybrid-azuread-join-plan.md). |

## <a name="manage-your-devices"></a>Gerenciar seus dispositivos

Depois de registrar ou ingressar seus dispositivos no Azure AD, use o [portal do Azure](https://portal.azure.com/) como um local central para gerenciar suas identidades de dispositivo. A página dispositivos Azure Active Directory permite que você:

* [Configurar suas configurações de dispositivo](device-management-azure-portal.md#configure-device-settings)
* Você precisa ser um administrador local para gerenciar dispositivos Windows. O [Azure ad atualiza essa associação para dispositivos ingressados no Azure ad](assign-local-admin.md), adicionando-os automaticamente com a função de Gerenciador de dispositivos como administradores a todos os dispositivos ingressados.

Certifique-se de manter o ambiente limpo [Gerenciando dispositivos obsoletos](manage-stale-devices.md)e concentre seus recursos no gerenciamento de dispositivos atuais.

* [Examinar os logs de auditoria relacionados ao dispositivo](device-management-azure-portal.md#audit-logs)

### <a name="supported-device-management-tools"></a>Ferramentas de gerenciamento de dispositivos com suporte

Os administradores podem proteger e controlar ainda mais esses dispositivos registrados e ingressados usando ferramentas de gerenciamento de dispositivos adicionais. Essas ferramentas fornecem um meio de impor configurações necessárias à organização, como exigir que o armazenamento seja criptografado, complexidade de senha, instalações e atualizações de software. 

Examine as plataformas com suporte e sem suporte para dispositivos integrados:

| Ferramentas de gerenciamento de dispositivo| Azure AD registrado| Ingresso no Azure AD| Ingresso no Azure AD Híbrido|
| - | - | - | - |
| [MDM (gerenciamento de dispositivo móvel) ](/windows/client-management/mdm/azure-active-directory-integration-with-mdm) <br>Exemplo: Microsoft Intune| ![Marca de seleção para esses valores.](./media/plan-device-deployment/check.png)| ![Marca de seleção para esses valores.](./media/plan-device-deployment/check.png)| ![Marca de seleção para esses valores.](./media/plan-device-deployment/check.png)|  |
| [Gerenciamento de co com Microsoft Intune e Microsoft Endpoint Configuration Manager](/mem/configmgr/comanage/overview) <br>(Windows 10 e posterior)| | ![Marca de seleção para esses valores.](./media/plan-device-deployment/check.png)| ![Marca de seleção para esses valores.](./media/plan-device-deployment/check.png)|  |
| [Política de grupo](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831791(v=ws.11))<br>(Somente Windows)| | | ![Marca de seleção para esses valores.](./media/plan-device-deployment/check.png)|  |



 Recomendamos que você considere [Microsoft INTUNE MAM (gerenciamento de aplicativo móvel)](/mem/intune/apps/app-management) com ou sem gerenciamento de dispositivo para dispositivos IOS ou Android registrados.

 Os administradores também podem [implantar as plataformas de VDI (Virtual Desktop Infrastructure)](howto-device-identity-virtual-desktop-infrastructure.md) que hospedam sistemas operacionais Windows em suas organizações para simplificar o gerenciamento e reduzir os custos por meio de consolidação e centralização de recursos. 

### <a name="troubleshoot-device-identities"></a>Solucionar problemas de identidades de dispositivos

* [Solucionando problemas de dispositivos usando o comando dsregcmd](troubleshoot-device-dsregcmd.md)

* [Solucionando problemas de configurações do Enterprise State Roaming no Azure Active Directory](enterprise-state-roaming-troubleshooting.md)

Se estiver com problemas para concluir o ingresso no Azure AD híbrido de dispositivos Windows unidos ao domínio, confira:

* [Solucionar problemas de ingresso no Azure AD híbrido para dispositivos atuais do Windows](troubleshoot-hybrid-join-windows-current.md)

* [Solucionar problemas de ingresso no Azure AD híbrido para dispositivos de nível inferior do Windows](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>Próximas etapas

* [Planejar sua implementação de ingresso no Azure AD](azureadjoin-plan.md)
* [Planejar sua implementação de ingresso no Azure AD híbrido](hybrid-azuread-join-plan.md)
* [Gerenciar identidades do dispositivo](device-management-azure-portal.md)