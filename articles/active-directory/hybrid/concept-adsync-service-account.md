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
ms.date: 06/27/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8dddfb8426b769c06cb5b7494431b7eee34dbf9e
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94410888"
---
# <a name="adsync-service-account"></a>Conta do serviço ADSync
Azure AD Connect instala um serviço local que orquestra a sincronização entre Active Directory e Azure Active Directory.  O Microsoft Azure AD ADSync (serviço de sincronização sincronizada) é executado em um servidor no seu ambiente local.  As credenciais para o serviço são definidas por padrão nas instalações expressas, mas podem ser personalizadas para atender aos requisitos de segurança organizacional.  Essas credenciais não são usadas para se conectar às suas florestas ou Azure Active Directory locais.

Escolher a conta de serviço do ADSync é uma decisão de planejamento importante a ser feita antes da instalação do Azure AD Connect.  Qualquer tentativa de alterar as credenciais após a instalação resultará na falha na inicialização do serviço, na perda do acesso ao banco de dados de sincronização e na falha na autenticação com seus diretórios conectados (Azure e AD DS).  Nenhuma sincronização ocorrerá até que as credenciais originais sejam restauradas.

## <a name="the-default-adsync-service-account"></a>A conta de serviço ADSync padrão

Quando executado em um servidor membro, o serviço AdSync é executado no contexto de uma conta de serviço virtual (VSA).  Devido a uma limitação de produto, uma conta de serviço personalizada é criada quando instalada em um controlador de domínio.  Se a conta de serviço de configurações expressas não atender aos seus requisitos de segurança organizacional, implante Azure AD Connect escolhendo a opção personalizar.  Em seguida, escolha a opção de conta de serviço que atende aos requisitos da sua organização.

>[!NOTE]
>A conta de serviço padrão quando instalada em um controlador de domínio é do formato domínio \ AAD_InstallationIdentifier.  A senha para essa conta é gerada aleatoriamente e apresenta desafios significativos para recuperação e rotação de senha.  A Microsoft recomenda personalizar a conta de serviço durante a instalação inicial em um controlador de domínio para usar uma conta de serviço gerenciado de grupo ou autônoma (sMSA/gMSA)

|Azure AD Connect local|Conta de serviço criada|
|-----|-----|
|Servidor membro|NT SERVICE\ADSync|
|Controlador de Domínio|Domínio \ AAD_74dc30c01e80 (consulte a observação)|

## <a name="custom-adsync-service-accounts"></a>Contas de serviço do ADSync personalizado
A Microsoft recomenda executar o serviço ADSync no contexto de uma conta de serviço virtual ou de uma conta de serviço gerenciado de grupo ou autônomo.  O administrador de domínio também pode optar por criar uma conta de serviço provisionada para atender aos requisitos específicos de segurança organizacional.   Para personalizar a conta de serviço usada durante a instalação, escolha a opção personalizar na página Configurações expressas abaixo.   As seguintes opções estão disponíveis:

- conta padrão – Azure AD Connect provisionará a conta de serviço conforme descrito acima
- conta de serviço gerenciado – use um MSA autônomo ou de grupo provisionado pelo administrador
- conta de domínio – usar uma conta de serviço de domínio provisionada pelo administrador

![Captura de tela da página de configurações do Azure AD Connect Express com os botões de opção "Personalizar" ou "usar configurações expressas".](media/concept-adsync-service-account/adsync1.png)

![Captura de tela da página Azure AD Connect "instalar componentes necessários" com a opção de usar uma conta de serviço gerenciado existente selecionada.](media/concept-adsync-service-account/adsync2.png)

## <a name="diagnosing-adsync-service-account-changes"></a>Diagnosticando alterações de conta de serviço do ADSync
A alteração das credenciais do serviço ADSync após a instalação resultará na falha na inicialização do serviço, na perda do acesso ao banco de dados de sincronização e na falha na autenticação com seus diretórios conectados (Azure e AD DS).  A concessão de acesso ao banco de dados à nova conta de serviço ADSync é insuficiente para se recuperar desse problema. Nenhuma sincronização ocorrerá até que as credenciais originais sejam restauradas.

O serviço ADSync emitirá uma mensagem de nível de erro para o log de eventos quando não puder iniciar.  O conteúdo da mensagem irá variar dependendo se o banco de dados interno (LocalDB) ou o SQL completo está em uso.  Veja a seguir exemplos das entradas do log de eventos que podem estar presentes.

### <a name="example-1"></a>Exemplo 1

Não foi possível encontrar as chaves de criptografia do serviço AdSync e elas foram recriadas.  A sincronização não ocorrerá até que esse problema seja corrigido.

Solucionando esse problema, a Microsoft Azure AD chaves de criptografia de sincronização se tornarão inacessíveis se as credenciais de logon do serviço AdSync forem alteradas.  Se as credenciais tiverem sido alteradas, use o aplicativo de serviços para alterar a conta de logon de volta para o valor originalmente configurado (por exemplo, NT SERVICE\AdSync) e reinicie o serviço.  Isso irá restaurar imediatamente a operação correta do serviço AdSync.

Consulte o [artigo](./whatis-hybrid-identity.md) a seguir para obter mais informações.

### <a name="example-2"></a>Exemplo 2

O serviço não pôde ser iniciado porque não foi possível estabelecer uma conexão com o banco de dados local (LocalDB).

Solucionando esse problema, o serviço de sincronização de Microsoft Azure AD perderá a permissão para acessar o provedor de banco de dados local se as credenciais de logon do serviço AdSync forem alteradas.  Se as credenciais tiverem sido alteradas, use o aplicativo serviços para alterar a conta de logon de volta para o valor originalmente configurado (ex. NT SERVICE\AdSync) e reinicie o serviço.  Isso irá restaurar imediatamente a operação correta do serviço AdSync.

Consulte o [artigo](./whatis-hybrid-identity.md) a seguir para obter mais informações.

Detalhes adicionais as informações de erro a seguir foram retornadas pelo provedor:
 

``` 
OriginalError=0x80004005 OLEDB Provider error(s): 
Description  = 'Login timeout expired'
Failure Code = 0x80004005
Minor Number = 0 
Description  = 'A network-related or instance-specific error has occurred while establishing a connection to SQL Server. Server is not found or not accessible. Check if instance name is correct and if SQL Server is configured to allow remote connections. For more information see SQL Server Books Online.'
```
## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre [Como integrar suas identidades locais ao Active Directory do Azure](whatis-hybrid-identity.md).