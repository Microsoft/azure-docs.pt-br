---
title: 'Azure AD Connect: conta de serviço ADSync | Microsoft Docs'
description: Este tópico descreve a conta de serviço do ADSync e fornece as práticas recomendadas sobre a conta.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/17/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: fb23d79caa6964c3f61fbb84c8b8f229f475b8ab
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "104722150"
---
# <a name="adsync-service-account"></a>Conta do serviço ADSync
Azure AD Connect instala um serviço local que orquestra a sincronização entre Active Directory e Azure Active Directory.  O Microsoft Azure AD ADSync (serviço de sincronização sincronizada) é executado em um servidor no seu ambiente local.  As credenciais para o serviço são definidas por padrão nas instalações expressas, mas podem ser personalizadas para atender aos requisitos de segurança organizacional.  Essas credenciais não são usadas para se conectar às suas florestas ou Azure Active Directory locais.

Escolher a conta de serviço do ADSync é uma decisão de planejamento importante a ser feita antes da instalação do Azure AD Connect.  Qualquer tentativa de alterar as credenciais após a instalação resultará na falha na inicialização do serviço, na perda do acesso ao banco de dados de sincronização e na falha na autenticação com seus diretórios conectados (Azure e AD DS).  Nenhuma sincronização ocorrerá até que as credenciais originais sejam restauradas.

O serviço de sincronização pode ser executado em contas diferentes. Ele pode ser executado em uma VSA (conta de serviço virtual), uma conta de serviço gerenciado (gMSA/sMSA) ou uma conta de usuário normal. As opções com suporte foram alteradas com a versão de 2017 de abril e a versão de 2021 de março do Azure AD Connect ao fazer uma nova instalação. Se você atualizar de uma versão anterior do Azure AD Connect, essas opções adicionais não estarão disponíveis. 


|Tipo de conta|Opção de instalação|Descrição| 
|-----|------|-----|
|Conta de Serviço Virtual|Expressa e personalizada, abril de 2017 e posterior| Uma conta de serviço virtual é usada para todas as instalações expressas, exceto para instalações em um controlador de domínio. Ao usar a instalação personalizada, ela é a opção padrão, a menos que outra opção seja usada.| 
|Conta de Serviço Gerenciado|Personalizada, abril de 2017 e posterior|Se você usar um SQL Server remoto, é recomendável usar uma conta de serviço gerenciado de grupo. |
|Conta de Serviço Gerenciado|Expressa e personalizada, 2021 de março e posterior|Uma conta de serviço gerenciado independente prefixada com ADSyncMSA_ é criada durante a instalação para instalações expressas quando instalada em um controlador de domínio. Ao usar a instalação personalizada, ela é a opção padrão, a menos que outra opção seja usada.|
|Conta de Usuário|Expressa e personalizada, 2017 de abril a 2021 de março|Uma conta de usuário prefixada com AAD_ é criada durante a instalação para instalações expressas quando instalada em um controlador de domínio. Ao usar a instalação personalizada, ela é a opção padrão, a menos que outra opção seja usada.|
|Conta de Usuário|Expressa e personalizada, março de 2017 e versões anteriores|Uma conta de usuário prefixada com AAD_ é criada durante a instalação para instalações expressas. Ao usar a instalação personalizada, outra conta pode ser especificada.| 

>[!IMPORTANT]
> Se você usar o Connect com um build de março de 2017 ou anterior, a senha da conta de serviço não deverá ser redefinida, pois o Windows destruirá as chaves de criptografia por motivos de segurança. Você não pode alterar a conta para nenhuma outra conta sem reinstalar o Azure AD Connect. Se você atualizar para uma compilação de 2017 de abril ou posterior, haverá suporte para alterar a senha na conta de serviço, mas não será possível alterar a conta usada. 

> [!IMPORTANT]
> Você só pode definir a conta de serviço na primeira instalação. Não há suporte para alterar a conta de serviço após a conclusão da instalação. Se você precisar alterar a senha da conta de serviço, isso é suportado e as instruções podem ser encontradas [aqui](how-to-connect-sync-change-serviceacct-pass.md).

Veja a seguir uma tabela das opções padrão, recomendadas e com suporte para a conta de serviço de sincronização. 

Legenda: 

- **Bold** indica a opção padrão e, na maioria dos casos, a opção recomendada. 
- *Itálico* indica a opção recomendada quando ela não é a opção padrão. 
- Não negrito – opção com suporte 
- Conta local – conta de usuário local no servidor 
- Conta do domínio – conta de usuário do domínio 
- sMSA – [conta de serviço gerenciado autônomo](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd548356(v=ws.10))
- gMSA – [conta de serviço gerenciado de grupo](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831782(v=ws.11)) 

 ||**LocalDB </br> Express**|**LocalDB/LocalSQL </br> personalizado**|**Personalizado do SQL remoto </br>**|
|-----|-----|-----|-----|
|**computador associado ao domínio**|**VSA**|**VSA**</br> *sMSA*</br> *gMSA*</br> Conta local</br> Conta do domínio| *gMSA* </br>Conta do domínio|
|Controlador de domínio| **sMSA**|**sMSA** </br>*gMSA*</br> Conta do domínio|*gMSA*</br>Conta do domínio| 

## <a name="virtual-service-account"></a>Conta de Serviço Virtual 

Uma conta de serviço virtual é um tipo especial de conta local gerenciada que não tem uma senha e é gerenciada automaticamente pelo Windows. 

 ![Conta de serviço virtual](media/concept-adsync-service-account/account-1.png)

A conta de serviço virtual destina-se a ser usada com cenários nos quais o mecanismo de sincronização e o SQL estão no mesmo servidor. Se você usar o SQL remoto, é recomendável usar uma conta de serviço gerenciado de grupo em vez disso. 

A conta de serviço virtual não pode ser usada em um controlador de domínio devido a problemas de [DPAPI (API de proteção de dados) do Windows](https://msdn.microsoft.com/library/ms995355.aspx) . 

## <a name="managed-service-account"></a>Conta de Serviço Gerenciado 

Se você usar um SQL Server remoto, será recomendável usar uma conta de serviço gerenciado de grupo. Para obter mais informações sobre como preparar seu Active Directory para conta de serviço gerenciado de grupo, consulte [visão geral de contas de serviço gerenciado de grupo](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831782(v=ws.11)). 

Para usar essa opção, na página [Instalar componentes necessários](how-to-connect-install-custom.md#install-required-components), selecione **Usar uma conta de serviço existente** e selecione **Conta de serviço gerenciado**. 

 ![conta de serviço gerenciado](media/concept-adsync-service-account/account-2.png)

Também há suporte para o uso de uma conta de serviço gerenciado autônomo. No entanto, eles só podem ser usados no computador local e não há nenhum benefício em usá-los na conta de serviço virtual padrão. 

### <a name="auto-generated-standalone-managed-service-account"></a>Conta de serviço gerenciado autônomo gerada automaticamente 

Se você instalar Azure AD Connect em um controlador de domínio, uma conta de serviço gerenciado autônomo será criada pelo assistente de instalação (a menos que você especifique a conta a ser usada nas configurações personalizadas). A conta é prefixada **ADSyncMSA_** e usada para que o serviço de sincronização real seja executado como. 

Essa conta é uma conta de domínio gerenciado que não tem uma senha e é gerenciada automaticamente pelo Windows. 

Essa conta destina-se a ser usada com cenários nos quais o mecanismo de sincronização e o SQL estão no controlador de domínio. 

## <a name="user-account"></a>Conta de Usuário 

Uma conta de serviço local é criada pelo assistente de instalação (a menos que você especifique a conta a ser usada em configurações personalizadas). A conta prefixada com AAD_ é usada com o serviço de sincronização real para ser executada como. Se você instalar o Azure AD Connect em um controlador de domínio, a conta é criada no domínio. A conta de serviço AAD_ deve estar localizada no domínio se: 
- Você usa um servidor remoto executando o SQL Server 
- você usa um proxy que exija autenticação 

 ![conta de usuário](media/concept-adsync-service-account/account-3.png)

A conta é criada com uma senha longa complexa que não expira. 

Essa conta é usada para armazenar senhas para as outras contas de forma segura. As senhas dessas outras contas são armazenadas criptografadas no banco de dados. As chaves privadas das chaves de criptografia são protegidas com a criptografia de chave secreta dos serviços de criptografia usando a DPAPI (API de Proteção de Dados) do Windows. 

Se você usar um SQL Server completo, a conta de serviço será o DBO do banco de dados criado para o mecanismo de sincronização. O serviço não funcionará conforme o esperado com qualquer outra permissão. Um logon do SQL também é criado. 

A conta também recebe permissão para arquivos, chaves do registro e outros objetos relacionados ao mecanismo de sincronização. 


## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre [Como integrar suas identidades locais ao Active Directory do Azure](whatis-hybrid-identity.md).
