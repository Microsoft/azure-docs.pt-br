---
title: 'Azure AD Connect: Conta de serviço ADSync | Microsoft Docs'
description: Este tópico descreve a conta de serviço ADSync e fornece as práticas recomendadas sobre a conta.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 06/27/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f228da5afc5998d8fa59ce2d720cec4c9f955b67
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2019
ms.locfileid: "67478713"
---
# <a name="adsync-service-account"></a>Conta do serviço ADSync
Azure AD Connect instala um serviço local que coordena a sincronização entre o Active Directory e o Azure Active Directory.  O serviço de sincronização do Microsoft Azure AD Sync (ADSync) é executado em um servidor no seu ambiente local.  As credenciais para o serviço são definidas por padrão nas instalações Express, mas podem ser personalizadas para atender às suas necessidades de segurança da organização.  Essas credenciais não são usadas para se conectar ao seu florestas locais ou do Active Directory do Azure.

Conta de serviço escolhendo o serviço ADSync é uma decisão importante de planejamento para fazer antes de instalar o Azure AD Connect.  Qualquer tentativa alterar as credenciais após a instalação resultará em Falha ao iniciar, o serviço de perder o acesso ao banco de dados de sincronização e falhando em autenticar com seus diretórios conectados (do Azure e o AD DS).  Nenhuma sincronização ocorrerá até que as credenciais originais são restauradas.

## <a name="the-default-adsync-service-account"></a>A conta padrão do serviço ADSync

Quando executado em um servidor membro, o serviço AdSync é executado no contexto de uma conta de serviço Virtual (VSA).  Devido a uma limitação do produto, uma conta de serviço personalizado é criada quando instalado em um controlador de domínio.  Se a conta de serviço de configurações do Express não atender seus requisitos de segurança organizacional, implante o Azure AD Connect, escolhendo a opção Personalizar.  Em seguida, escolha a opção de conta de serviço que atende aos requisitos da sua organização.

>[!NOTE]
>A conta de serviço padrão quando instalado em um controlador de domínio está no formato Domain\AAD_InstallationIdentifier.  A senha para essa conta é gerada aleatoriamente e apresenta desafios significativos para a rotação de senha e de recuperação.  A Microsoft recomenda Personalizando a conta de serviço durante a instalação inicial em um controlador de domínio para usar um autônomo ou conta de serviço gerenciado de grupo (sMSA / gMSA)

|Local do Azure AD Connect|Conta de serviço criada|
|-----|-----|
|Servidor membro|NT SERVICE\ADSync|
|Domain Controller|Domain\AAD_74dc30c01e80 (consulte a observação)|

## <a name="custom-adsync-service-accounts"></a>Contas de serviço personalizadas do ADSync
A Microsoft recomenda executando o serviço ADSync do serviço no contexto de uma conta de serviço Virtual ou um autônomo ou conta de serviço gerenciado de grupo.  Seu administrador de domínio também pode optar por criar uma conta de serviço provisionada para atender às suas necessidades específicas de segurança organizacional.   Para personalizar a conta de serviço usada durante a instalação, escolha a opção de personalizar a página de configurações expressas abaixo.   As seguintes opções estão disponíveis:

- conta padrão – o Azure AD Connect provisionará a conta de serviço, conforme descrito acima
- gerenciado conta de serviço – use um autônomo ou provisionado pelo administrador MSA de grupo
- conta de domínio – uso provisionada pelo administrador de conta de serviço de um domínio

![](media/concept-adsync-service-account/adsync1.png)

![](media/concept-adsync-service-account/adsync2.png)

## <a name="diagnosing-adsync-service-account-changes"></a>Diagnosticar alterações de conta do serviço ADSync
Alterar as credenciais para o serviço ADSync após a instalação resultará em Falha ao iniciar, o serviço de perder o acesso ao banco de dados de sincronização e falhando em autenticar com seus diretórios conectados (do Azure e o AD DS).  Concedendo acesso de banco de dados para a nova conta de serviço ADSync é insuficiente para se recuperar desse problema. Nenhuma sincronização ocorrerá até que as credenciais originais são restauradas.

O serviço ADSync emitirá uma mensagem de nível de erro para o log de eventos quando não é possível iniciar.  O conteúdo da mensagem irá variar dependendo se o banco de dados interno (localdb) ou o SQL completo está em uso.  A seguir estão exemplos das entradas de log de eventos que podem estar presentes.

### <a name="example-1"></a>Exemplo 1

As chaves de criptografia do serviço AdSync não pôde ser encontrado e foram recriadas.  Sincronização não ocorrerá até que esse problema seja corrigido.

Solução de problemas este problema o Microsoft Azure AD Sync chaves de criptografia tornará inacessíveis se as credenciais de logon do serviço AdSync forem alteradas.  Se as credenciais foram alteradas, use o aplicativo de serviços para alterar a conta de logon para seu valor originalmente configurado (por exemplo NT SERVICE\AdSync) e reinicie o serviço.  A operação correta do serviço AdSync será restaurado imediatamente.

Consulte o seguinte [artigo](https://go.microsoft.com/fwlink/?linkid=2086764) para obter mais informações.

### <a name="example-2"></a>Exemplo 2

O serviço não pôde ser iniciado porque não foi possível estabelecer uma conexão para o banco de dados local (localdb).

Solucionando problemas do serviço este problema o Microsoft Azure AD Sync, permissão para acessar o provedor de banco de dados local se as credenciais de logon do serviço AdSync forem alteradas serão perdidas.  Se as credenciais tiverem sido alteradas, use o aplicativo de serviços para alterar a conta de logon para seu valor originalmente configurado (por exemplo NT SERVICE\AdSync) e reinicie o serviço.  A operação correta do serviço AdSync será restaurado imediatamente.

Consulte o seguinte [artigo](https://go.microsoft.com/fwlink/?linkid=2086764) para obter mais informações.

As seguintes informações de erro foi retornadas pelo provedor de detalhes adicionais:
 

``` 
OriginalError=0x80004005 OLEDB Provider error(s): 
Description  = 'Login timeout expired'
Failure Code = 0x80004005
Minor Number = 0 
Description  = 'A network-related or instance-specific error has occurred while establishing a connection to SQL Server. Server is not found or not accessible. Check if instance name is correct and if SQL Server is configured to allow remote connections. For more information see SQL Server Books Online.'
```
## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre [Como integrar suas identidades locais ao Active Directory do Azure](whatis-hybrid-identity.md).