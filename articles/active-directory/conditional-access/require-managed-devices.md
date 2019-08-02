---
title: Como a - exigir dispositivos para acesso de aplicativo de nuvem com acesso condicional do Azure Active Directory gerenciados | Microsoft Docs
description: Saiba como configurar o Azure Active Directory (Azure AD) com base em dispositivo políticas de acesso condicional que exigem dispositivos gerenciados para acesso de aplicativo de nuvem.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 06/14/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: e9c99b8390cd43c3f0767123684fe06e0ae74f86
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509366"
---
# <a name="how-to-require-managed-devices-for-cloud-app-access-with-conditional-access"></a>Como: Exigir que os dispositivos gerenciados para acesso de aplicativo de nuvem com acesso condicional

Em um mundo que prioriza os dispositivos móveis e a nuvem, o Azure AD (Azure Active Directory) permite o logon único em aplicativos e serviços de qualquer lugar. Os usuários autorizados podem acessar aplicativos em nuvem em uma grande variedade de dispositivos, inclusive dispositivos para dispositivos móveis e também pessoais. No entanto, muitos ambientes tem pelo menos alguns aplicativos que devem ser acessados por dispositivos que atendem aos padrões de segurança e conformidade. Esses dispositivos também são conhecidos como dispositivos gerenciados. 

Este artigo explica como você pode configurar políticas de acesso condicional que exigem que os dispositivos gerenciados para acessar determinados aplicativos de nuvem em seu ambiente. 

## <a name="prerequisites"></a>Pré-requisitos

Exigir que os dispositivos gerenciados para vínculos de acesso do aplicativo de nuvem **acesso condicional do Azure AD** e **gerenciamento de dispositivo do Azure AD** juntos. Caso ainda não esteja familiarizado com uma dessas áreas, você deverá ler os seguintes tópicos primeiro:

- **[Acesso condicional no Azure Active Directory](../active-directory-conditional-access-azure-portal.md)**  -este artigo fornece uma visão geral conceitual do acesso condicional e a terminologia relacionada.
- **[Introduction to device management in Azure Active Directory](../devices/overview.md)** - esse artigo fornece uma visão geral das várias opções para conseguir dispositivos sob controle organizacional. 

## <a name="scenario-description"></a>Descrição do cenário

Controle do equilíbrio entre produtividade e segurança é um desafio. A proliferação de dispositivos com suporte para acessar os recursos de nuvem ajuda a melhorar a produtividade dos usuários. Por outro lado, não convém certos recursos em seu ambiente para ser acessado por dispositivos com um nível de proteção desconhecido. Para os recursos afetados, você deve exigir que os usuários possam acessar somente usando um dispositivo gerenciado. 

Com acesso condicional do Azure AD, você pode atender a esse requisito com uma única política que concede acesso:

- Para aplicativos na nuvem selecionados
- Para usuários e grupos selecionados
- Necessidade de um dispositivo gerenciado

## <a name="managed-devices"></a>Dispositivos gerenciados  

Em termos simples, os dispositivos gerenciados são dispositivos que estão sob *algum tipo* de controle organizacional. No Microsoft Azure AD, o pré-requisito para um dispositivo gerenciado é que ele tenha sido registrado com o Azure AD. Registrar um dispositivo cria uma identidade para o dispositivo na forma de um objeto de dispositivo. Esse objeto é usado pelo Azure para rastrear as informações de status sobre um dispositivo. Como um administrador do Microsoft Azure AD, você já pode usar esse objeto para alternar (ativar/desativar) o estado de um dispositivo.
  
![Condições baseadas no dispositivo](./media/require-managed-devices/32.png)

Para obter um dispositivo registrado com o Microsoft Azure AD, você tem três opções: 

- **Dispositivos registrados no Azure AD** – para obter um dispositivo pessoal registrado com o Azure AD
- **Dispositivos ingressados no Azure AD** – para obter um dispositivo Windows 10 organizacional que não ingressou em um local do AD registrado no Azure AD. 
- **Dispositivos ingressados no Azure AD híbrido** – para obter um Windows 10 ou o dispositivo com suporte de nível inferior que ingressou em um local do AD registrado no Azure AD.

Essas três opções são discutidas no artigo [o que é uma identidade do dispositivo?](../devices/overview.md)

Para se tornar um dispositivo gerenciado, um dispositivo registrado pode ser um **dispositivo ingressado no Microsoft Azure AD Híbrido** ou um **dispositivo que tenha sido marcado como em conformidade**.  

![Condições baseadas no dispositivo](./media/require-managed-devices/47.png)
 
## <a name="require-hybrid-azure-ad-joined-devices"></a>Requer dispositivos que tenham ingressado no Microsoft Azure AD Híbrido

Em sua política de acesso condicional, você pode selecionar **exigir dispositivo ingressado do Azure AD híbrido** declarar que os aplicativos de nuvem selecionados só podem ser acessados usando um dispositivo gerenciado. 

![Condições baseadas no dispositivo](./media/require-managed-devices/10.png)

Essa configuração aplica-se somente ao Windows 10 ou a dispositivos de nível inferior, como o Windows 7 ou o Windows 8, associados a um AD local. Só é possível registrar esses dispositivos com o Microsoft Azure AD usando uma junção do Microsoft Azure AD Híbrido, que é um [processo automatizado](../devices/hybrid-azuread-join-plan.md) para obter um dispositivo Windows 10 registrado. 

![Condições baseadas no dispositivo](./media/require-managed-devices/45.png)

O que torna um dispositivo ingressado no Microsoft Azure AD Híbrido um dispositivo gerenciado?  Para dispositivos que fazem parte de um AD local, presume-se que o controle sobre esses dispositivos é imposto usando soluções de gerenciamento, como **System Center Configuration Manager (SCCM)** ou **política de grupo (GP)** para gerenciá-los. Como não há nenhum método para que o Microsoft Azure AD determine se qualquer um dos métodos a seguir foram aplicados a um dispositivo, exigir um dispositivo ingressado no Microsoft Azure AD híbrido é um mecanismo relativamente fraco para solicitar um dispositivo gerenciado. Cabe a você como administrador avaliar se os métodos que são aplicados a seus dispositivos ingressados no domínio local são fortes o suficiente para constituir um dispositivo gerenciado, se tal dispositivo também for um dispositivo ingressado no Microsoft Azure AD híbrido.

## <a name="require-device-to-be-marked-as-compliant"></a>Exigir que o dispositivo seja marcado como em conformidade

A opção de *exigir que um dispositivo seja marcado como em conformidade* é a forma mais segura de solicitar um dispositivo gerenciado.

![Condições baseadas no dispositivo](./media/require-managed-devices/11.png)

Essa opção requer que um dispositivo seja registrado com o Microsoft Azure AD e também marcado como em conformidade por:
         
- Intune.
- Um sistema gerenciado por dispositivo móvel de terceiros (MDM) que gerencia os dispositivos Windows 10 por meio da integração do Azure AD. Sistemas MDM de terceiros para tipos de dispositivo OS, exceto Windows 10, não são suportados.
 
![Condições baseadas no dispositivo](./media/require-managed-devices/46.png)

Para um dispositivo que esteja marcado como em conformidade, você pode presumir que: 

- Os dispositivos móveis que sua força de trabalho usa para acessar os dados da empresa são gerenciados
- Os aplicativos móveis que sua força de trabalho usa são gerenciados
- As informações da sua empresa são protegidas ao ajudar a controlar a maneira como sua força de trabalho as acessa e compartilha
- O dispositivo e seus aplicativos são compatíveis com os requisitos de segurança da empresa

## <a name="next-steps"></a>Próximas etapas

Antes de configurar uma política de acesso condicional baseado em dispositivo em seu ambiente, você deve dar uma olhada a [práticas recomendadas para acesso condicional no Azure Active Directory](best-practices.md).
