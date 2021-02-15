---
title: O acesso condicional requer dispositivo gerenciado-Azure Active Directory
description: Saiba como configurar políticas de acesso condicional com base no dispositivo Azure Active Directory (Azure AD) que exigem dispositivos gerenciados para acesso ao aplicativo de nuvem.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 10/16/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: c11b58954eefda67f981d618b04ab2bd69fa6b43
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93077754"
---
# <a name="how-to-require-managed-devices-for-cloud-app-access-with-conditional-access"></a>Como: exigir dispositivos gerenciados para acesso ao aplicativo de nuvem com acesso condicional

Em um mundo que prioriza os dispositivos móveis e a nuvem, o Azure AD (Azure Active Directory) permite o logon único em aplicativos e serviços de qualquer lugar. Os usuários autorizados podem acessar aplicativos em nuvem em uma grande variedade de dispositivos, inclusive dispositivos para dispositivos móveis e também pessoais. No entanto, muitos ambientes tem pelo menos alguns aplicativos que devem ser acessados por dispositivos que atendem aos padrões de segurança e conformidade. Esses dispositivos também são conhecidos como dispositivos gerenciados. 

Este artigo explica como você pode configurar políticas de acesso condicional que exigem que dispositivos gerenciados acessem determinados aplicativos de nuvem em seu ambiente. 

## <a name="prerequisites"></a>Pré-requisitos

Exigir dispositivos gerenciados para acesso ao aplicativo de nuvem vincula o **acesso condicional do Azure ad** e o **Gerenciamento de dispositivos do Azure ad** juntos. Caso ainda não esteja familiarizado com uma dessas áreas, você deverá ler os seguintes tópicos primeiro:

- **[Acesso condicional no Azure Active Directory](./overview.md)** -este artigo fornece uma visão geral conceitual do acesso condicional e da terminologia relacionada.
- **[Introduction to device management in Azure Active Directory](../devices/overview.md)** - esse artigo fornece uma visão geral das várias opções para conseguir dispositivos sob controle organizacional. 
- Para obter suporte ao Chrome na **atualização do Windows 10 para criadores (versão 1703)** ou posterior, instale a [extensão de contas do Windows 10](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji). Essa extensão é necessária quando uma política de acesso condicional requer detalhes específicos do dispositivo.

>[!NOTE] 
> É recomendável usar a política de acesso condicional baseada em dispositivo do Azure AD para obter a melhor imposição após a autenticação inicial do dispositivo. Isso inclui sessões de fechamento se o dispositivo ficar sem conformidade e fluxo de código de dispositivo.


## <a name="scenario-description"></a>Descrição do cenário

Controle do equilíbrio entre produtividade e segurança é um desafio. A proliferação de dispositivos com suporte para acessar os recursos de nuvem ajuda a melhorar a produtividade dos usuários. Por outro lado, não convém certos recursos em seu ambiente para ser acessado por dispositivos com um nível de proteção desconhecido. Para os recursos afetados, você deve exigir que os usuários possam acessar somente usando um dispositivo gerenciado. 

Com o acesso condicional do Azure AD, você pode atender a esse requisito com uma única política que concede acesso:

- Para aplicativos na nuvem selecionados
- Para usuários e grupos selecionados
- Necessidade de um dispositivo gerenciado

## <a name="managed-devices"></a>Dispositivos gerenciados  

Em termos simples, os dispositivos gerenciados são dispositivos que estão sob *algum tipo* de controle organizacional. No Microsoft Azure AD, o pré-requisito para um dispositivo gerenciado é que ele tenha sido registrado com o Azure AD. Registrar um dispositivo cria uma identidade para o dispositivo na forma de um objeto de dispositivo. Esse objeto é usado pelo Azure para rastrear as informações de status sobre um dispositivo. Como um administrador do Microsoft Azure AD, você já pode usar esse objeto para alternar (ativar/desativar) o estado de um dispositivo.
  
:::image type="content" source="./media/require-managed-devices/32.png" alt-text="Captura de tela do painel do dispositivo no Azure A D. habilitar e Desabilitar itens são realçados." border="false":::

Para obter um dispositivo registrado com o Microsoft Azure AD, você tem três opções: 

- **Dispositivos registrados do Microsoft Azure AD** - para obter um dispositivo pessoal registrado com o Microsoft Azure AD
- **Dispositivos ingressados no Azure AD** – para obter um dispositivo Windows 10 organizacional que não esteja ingressado em um AD local registrado no Azure AD. 
- **Os dispositivos híbridos do Azure AD participaram de dispositivos** - para obter um dispositivo Windows 10 ou de nível inferior com suporte associado a um AD local registrado no Azure AD.

Essas três opções são discutidas no artigo [o que é uma identidade de dispositivo?](../devices/overview.md)

Para se tornar um dispositivo gerenciado, um dispositivo registrado pode ser um **dispositivo ingressado no Microsoft Azure AD Híbrido** ou um **dispositivo que tenha sido marcado como em conformidade** .  

:::image type="content" source="./media/require-managed-devices/47.png" alt-text="Captura de tela do painel do dispositivo no Azure A D. habilitar e Desabilitar itens são realçados." border="false":::
 
## <a name="require-hybrid-azure-ad-joined-devices"></a>Requer dispositivos que tenham ingressado no Microsoft Azure AD Híbrido

Em sua política de acesso condicional, você pode selecionar **exigir dispositivo ingressado no Azure ad híbrido** para declarar que os aplicativos de nuvem selecionados só podem ser acessados usando um dispositivo gerenciado. 

:::image type="content" source="./media/require-managed-devices/10.png" alt-text="Captura de tela do painel do dispositivo no Azure A D. habilitar e Desabilitar itens são realçados." border="false":::

Essa configuração aplica-se somente ao Windows 10 ou a dispositivos de nível inferior, como o Windows 7 ou o Windows 8, associados a um AD local. Só é possível registrar esses dispositivos com o Microsoft Azure AD usando uma junção do Microsoft Azure AD Híbrido, que é um [processo automatizado](../devices/hybrid-azuread-join-plan.md) para obter um dispositivo Windows 10 registrado. 

:::image type="content" source="./media/require-managed-devices/45.png" alt-text="Captura de tela do painel do dispositivo no Azure A D. habilitar e Desabilitar itens são realçados." border="false":::

O que torna um dispositivo ingressado no Microsoft Azure AD Híbrido um dispositivo gerenciado?  Para dispositivos que ingressaram em um AD local, supõe-se que o controle desses dispositivos seja imposto usando soluções de gerenciamento, como **Configuration Manager** ou **GP (política de grupo)** para gerenciá-los. Como não há nenhum método para que o Microsoft Azure AD determine se qualquer um dos métodos a seguir foram aplicados a um dispositivo, exigir um dispositivo ingressado no Microsoft Azure AD híbrido é um mecanismo relativamente fraco para solicitar um dispositivo gerenciado. Cabe a você como administrador avaliar se os métodos que são aplicados a seus dispositivos ingressados no domínio local são fortes o suficiente para constituir um dispositivo gerenciado, se tal dispositivo também for um dispositivo ingressado no Microsoft Azure AD híbrido.

## <a name="require-device-to-be-marked-as-compliant"></a>Exigir que o dispositivo seja marcado como em conformidade

A opção de *exigir que um dispositivo seja marcado como em conformidade* é a forma mais segura de solicitar um dispositivo gerenciado.

:::image type="content" source="./media/require-managed-devices/11.png" alt-text="Captura de tela do painel do dispositivo no Azure A D. habilitar e Desabilitar itens são realçados." border="false":::

Essa opção requer que um dispositivo seja registrado com o Microsoft Azure AD e também marcado como em conformidade por:
         
- Intune
- Um sistema gerenciado por dispositivo móvel de terceiros (MDM) que gerencia os dispositivos Windows 10 por meio da integração do Azure AD. Sistemas MDM de terceiros para tipos de dispositivo OS, exceto Windows 10, não são suportados.
 
:::image type="content" source="./media/require-managed-devices/46.png" alt-text="Captura de tela do painel do dispositivo no Azure A D. habilitar e Desabilitar itens são realçados." border="false":::

Para um dispositivo que esteja marcado como em conformidade, você pode presumir que: 

- Os dispositivos móveis que sua força de trabalho usa para acessar os dados da empresa são gerenciados
- Os aplicativos móveis que sua força de trabalho usa são gerenciados
- As informações da sua empresa são protegidas ao ajudar a controlar a maneira como sua força de trabalho as acessa e compartilha
- O dispositivo e seus aplicativos são compatíveis com os requisitos de segurança da empresa

### <a name="scenario-require-device-enrollment-for-ios-and-android-devices"></a>Cenário: exigir registro de dispositivo para dispositivos iOS e Android

Nesse cenário, a Contoso decidiu que todo o acesso móvel para Microsoft 365 recursos deve usar um dispositivo registrado. Todos os seus usuários já entram com as credenciais do Azure AD e têm licenças atribuídas a eles que incluem o Azure AD Premium P1 ou P2 e o Microsoft Intune.

As organizações devem concluir as seguintes etapas para exigir o uso de um dispositivo móvel registrado.

1. Entre no **portal do Azure** como administrador global, administrador de segurança ou administrador de acesso condicional.
1. Procure **Azure Active Directory** > **Segurança** > **Acesso Condicional** .
1. Selecione **Nova política** .
1. Dê um nome à sua política. Recomendamos que as organizações criem um padrão significativo para os nomes de suas políticas.
1. Em **Atribuições** , selecione **Usuários e grupos** .
   1. Em **Incluir** , selecione **Todos os usuários** ou os **usuários e grupos** específicos aos quais você deseja aplicar essa política. 
   1. Selecione **Concluído** .
1. Em **aplicativos de nuvem ou ações**  >  **incluem** , selecione **Office 365** .
1. Em **Condições** , selecione **Plataformas de dispositivo** .
   1. Defina **Configurar** como **Sim** .
   1. Inclua **Android** e **iOS** .
1. Em **Controles de acesso** > **Conceder** , selecione as seguintes opções:
   - **Exigir que o dispositivo seja marcado como em conformidade**
1. Confirme suas configurações e defina **Habilitar política** como **Ativado** .
1. Selecione **Criar** para criar e habilitar sua política.

### <a name="known-behavior"></a>Comportamento conhecido

Ao usar o [fluxo OAuth do código do dispositivo](../develop/v2-oauth2-device-code.md), a condição exigir controle de concessão de dispositivo gerenciado ou estado do dispositivo não é suportada. Isso ocorre porque o dispositivo que executa a autenticação não pode fornecer o estado do dispositivo para o dispositivo que fornece um código e o estado do dispositivo no token está bloqueado para o dispositivo que executa a autenticação. Em vez disso, use o controle exigir autenticação multifator.

No Windows 7, iOS, Android, macOS e alguns navegadores da Web de terceiros, o Azure AD identifica o dispositivo usando um certificado de cliente que é provisionado quando o dispositivo é registrado no Azure AD. Quando um usuário entra pela primeira vez por meio do navegador, é solicitado que o usuário selecione o certificado. O usuário final deve selecionar esse certificado antes que possa continuar a usar o navegador.

## <a name="next-steps"></a>Próximas etapas

[Avalie o impacto das políticas de acesso condicional antes de habilitar amplamente com o modo somente de relatório](concept-conditional-access-report-only.md).
