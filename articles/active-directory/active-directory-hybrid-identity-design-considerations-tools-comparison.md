---
title: "Identidade Híbrida: comparação de ferramentas de integração de diretório | Microsoft Docs"
description: "Esta é a página que fornece uma tabela abrangente que compara as diversas ferramentas de integração de diretório que podem ser usadas para a integridade de diretório."
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: curtand
ms.assetid: 1e62a4bd-4d55-4609-895e-70131dedbf52
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/18/2017
ms.author: billmath
ms.openlocfilehash: f87b5837c7a786c8b309a6a52e69b53c0ad4c9d8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="hybrid-identity-directory-integration-tools-comparison"></a>Comparação de ferramentas de integração de diretório da Identidade Híbrida
Ao longo dos anos, as ferramentas de integração de diretório cresceram e evoluíram.  Este documento tem o objetivo de ajudar a fornecer uma visão consolidada dessas ferramentas e uma comparação dos recursos que estão disponíveis em cada uma.

<!-- The hardcoded link is a workaround for campaign ids not working in acom links-->

> [!NOTE]
> O Azure AD Connect incorpora componentes e funcionalidades lançados anteriormente como Dirsync e AAD Sync. Essas ferramentas não são mais lançadas individualmente e todas as melhorias futuras serão incluídas nas atualizações do Azure AD Connect, para que você sempre saiba onde obter a funcionalidade mais atual.
> 
> DirSync e Azure AD Sync foram preteridos. Mais informações podem ser encontradas [aqui](active-directory-aadconnect-dirsync-deprecated.md).
> 
> 

Use a seguinte chave para cada uma das tabelas.

● = Disponível Agora  
FR = Versão Futura  
PP = Versão prévia pública  

## <a name="on-premises-to-cloud-synchronization"></a>Local para a sincronização de nuvem
| Recurso | Conexão do Active Directory do Azure | Serviços de sincronização do Active Directory do Azure (sincronização do AAD) | Ferramenta de sincronização do Active Directory do Azure (DirSync) | O Forefront Identity Manager 2010 R2 (FIM) | Microsoft Identity Manager 2016 (MIM) |
|:--- |:---:|:---:|:---:|:---:|:---:|
| Conectar-se a única floresta do AD local |● |● |● |● |● |
| Conectar-se a várias florestas do AD local |● |● | |● |● |
| Conectar-se a várias organizações de Exchange local |● | | | | |
| Conectar-se a um único diretório LDAP local |FR | | |● |● |
| Conectar-se a vários diretórios LDAP local |FR | | |● |● |
| Conectar-se a diretórios AD locais e LDAP locais |FR | | |● |● |
| Conecte-se aos sistemas personalizados (por exemplo, SQL, Oracle, MySQL, etc.) |FR | | |● |● |
| Sincronize atributos definido pelo cliente (extensões de diretório) |● | | | | |
| Conectar-se ao RH local (isto é, SAP, Oracle eBusiness,PeopleSoft) |FR | | |● |● |
| Oferece suporte a regras de sincronização do FIM e conectores para provisionamento para os sistemas locais. | | | |● |● |

## <a name="cloud-to-on-premises-synchronization"></a>Nuvem para sincronização no local
| Recurso | Conexão do Active Directory do Azure | Serviços de sincronização do Active Directory do Azure | Ferramenta de sincronização do Active Directory do Azure (DirSync) | O Forefront Identity Manager 2010 R2 (FIM) | Microsoft Identity Manager 2016 (MIM) |
|:--- |:---:|:---:|:---:|:---:|:---:|
| Write-back de dispositivos |● | |● | | |
| Write-back de atributo (para implantação híbrida do Exchange) |● |● |● |● |● |
| Write-back de objetos de grupos |● | | | | |
| Write-back de senhas (de redefinição de senha de autoatendimento (SSPR) e alteração de senha) |● |● | | | |

## <a name="authentication-feature-support"></a>Suporte ao recurso de autenticação
| Recurso | Conexão do Active Directory do Azure | Serviços de sincronização do Active Directory do Azure | Ferramenta de sincronização do Active Directory do Azure (DirSync) | O Forefront Identity Manager 2010 R2 (FIM) | Microsoft Identity Manager 2016 (MIM) |
|:--- |:---:|:---:|:---:|:---:|:---:|
| Sincronização de senha para a floresta única do AD local |● |● |● | | |
| Sincronização de senha para várias florestas do AD local |● |● | | | |
| Logon único com federação |● |● |● |● |● |
| Write-back de senhas (de alteração SSPR e senha) |● |● | | | |

## <a name="set-up-and-installation"></a>Instalação e configuração
| Recurso | Conexão do Active Directory do Azure | Serviços de sincronização do Active Directory do Azure | Ferramenta de sincronização do Active Directory do Azure (DirSync) | Microsoft Identity Manager 2016 (MIM) |
|:--- |:---:|:---:|:---:|:---:|
| Oferece suporte à instalação em um controlador de domínio |● |● |● | |
| Oferece suporte à instalação usando o SQL Express |● |● |● | |
| Atualização fácil de DirSync |● | | | |
| Localização do UX do Administrador para linguagens do Windows Server |● |● |● | |
| Localização do UX do usuário final para linguagens do Windows Server | | | |● |
| Suporte para Windows Server 2008 e Windows Server 2008 R2 |● para sincronização, não para federação |● |● |● |
| Suporte para o Windows Server 2012 e Windows Server 2012 R2 |● |● |● |● |

## <a name="filtering-and-configuration"></a>Filtragem e configuração
| Recurso | Conexão do Active Directory do Azure | Serviços de sincronização do Active Directory do Azure | Ferramenta de sincronização do Active Directory do Azure (DirSync) | O Forefront Identity Manager 2010 R2 (FIM) | Microsoft Identity Manager 2016 (MIM) |
|:--- |:---:|:---:|:---:|:---:|:---:|
| Filtrar em domínios e unidades organizacionais |● |● |● |● |● |
| Filtrar os valores de atributo de objetos |● |● |● |● |● |
| Permitir que um conjunto mínimo de atributos seja sincronizado (MinSync) |● |● | | | |
| Permitir que os modelos de serviço diferentes a sejam aplicados para fluxos de atributo |● |● | | | |
| Permitir a remoção de atributos do fluxo do AD para o AD do Azure |● |● | | | |
| Permitir a personalização avançada para fluxos de atributo |● |● | |● |● |

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre [Como integrar suas identidades locais ao Active Directory do Azure](active-directory-aadconnect.md).

