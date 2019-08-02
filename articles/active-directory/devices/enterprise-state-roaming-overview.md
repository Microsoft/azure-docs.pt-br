---
title: O que é o estado corporativo móvel no Azure Active Directory? | Microsoft Docs
description: O Enterprise State Roaming fornece aos usuários uma experiência unificada em seus dispositivos Windows e reduz o tempo necessário para configurar um novo dispositivo.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: overview
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: na
ms.collection: M365-identity-device-management
ms.openlocfilehash: c5b60970592180a2353860369e637d4b9a9bb8f9
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67481900"
---
# <a name="what-is-enterprise-state-roaming"></a>O que é Enterprise State Roaming?

Com o Windows 10, os usuários do [Active Directory do Azure (AD do Azure)](../fundamentals/active-directory-whatis.md) obtêm a capacidade de sincronizar com segurança suas configurações de usuário e seus dados de configurações do aplicativo com a nuvem. O Enterprise State Roaming fornece aos usuários uma experiência unificada em seus dispositivos Windows e reduz o tempo necessário para configurar um novo dispositivo. O Enterprise State Roaming funciona de maneira semelhante à [sincronização de configurações de consumidor](https://go.microsoft.com/fwlink/?linkid=2015135) padrão, introduzida no Windows 8. Além disso, o Enterprise State Roaming oferece:

* **Separação dos dados corporativos e do consumidor** – as organizações estão no controle de seus dados, e não há nenhuma combinação de dados corporativos em uma conta de nuvem do consumidor ou de dados do consumidor em uma conta de nuvem da empresa.
* **Segurança avançada** – os dados são criptografados automaticamente antes de deixar o dispositivo Windows 10 do usuário usando o Azure Rights Management (Azure RMS) e os dados permanecem criptografados em repouso na nuvem. Todo o conteúdo permanece criptografado em repouso na nuvem, exceto os namespaces, como os nomes de configurações e os nomes de aplicativo do Windows.  
* **Melhor monitoramento e gerenciamento** – oferece mais controle e visibilidade sobre quem sincroniza configurações em sua organização e em quais dispositivos por meio da integração do portal do Azure AD. 

O Enterprise State Roaming está disponível em várias regiões do Azure. Você pode encontrar a lista atualizada de regiões disponíveis na página [Serviços do Azure por Região](https://azure.microsoft.com/regions/#services) no Azure Active Directory.

| Artigo | DESCRIÇÃO |
| --- | --- |
| [Habilitar o Enterprise State Roaming no Active Directory do Azure](enterprise-state-roaming-enable.md) |O Enterprise State Roaming está disponível para todas as organizações com uma assinatura Premium do Azure AD (Active Directory). Para obter mais detalhes sobre como obter uma assinatura do Azure AD, confira a [página de produto do Azure AD](https://azure.microsoft.com/services/active-directory) . |
| [Perguntas frequentes sobre configurações e roaming de dados](enterprise-state-roaming-faqs.md) |Este tópico responde a algumas dúvidas que os administradores de TI podem ter sobre as configurações e a sincronização de dados do aplicativo. |
| [Política de grupo e as configurações do MDM para a sincronização de configurações](enterprise-state-roaming-group-policy-settings.md) |O Windows 10 fornece configurações da Política de Grupo e da política de gerenciamento de dispositivos móveis (MDM) para limitar a sincronização de configurações. |
| [Referência de configurações de roaming do Windows 10](enterprise-state-roaming-windows-settings-reference.md) |Esta é uma lista completa de todas as configurações que serão ser movidas e/ou armazenadas em backup no Windows 10. |
| [Solução de problemas](enterprise-state-roaming-troubleshooting.md) |Este tópico aborda algumas etapas básicas para solução de problemas, além de conter uma lista de problemas conhecidos. |

## <a name="next-steps"></a>Próximas etapas

Para obter informações sobre como habilitar o enterprise state roaming, consulte [habilitar o enterprise state roaming](enterprise-state-roaming-enable.md).
