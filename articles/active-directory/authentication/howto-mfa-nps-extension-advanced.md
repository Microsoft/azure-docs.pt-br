---
title: Configurar a extensão NPS do Azure AD MFA-Azure Active Directory
description: Depois de instalar a extensão NPS, use estas etapas para configuração avançada, como listas de IP permitidas e substituição de UPN.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/11/2018
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 695261ceae9d64be9395e6de082f97be04292078
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98745978"
---
# <a name="advanced-configuration-options-for-the-nps-extension-for-multi-factor-authentication"></a>Opções de configuração avançada para a extensão NPS para autenticação multifator

A extensão NPS (servidor de políticas de rede) estende seus recursos de autenticação multifator do Azure AD baseados em nuvem para sua infraestrutura local. Este artigo pressupõe que você já tenha a extensão instalada e agora deseje saber como personalizá-la para as suas necessidades.

## <a name="alternate-login-id"></a>ID de logon alternativa

Uma vez que a extensão NPS conecta-se aos seus diretórios locais e de nuvem, você pode encontrar um problema em que seus UPNs (nomes de entidade de usuário) locais não correspondem aos nomes na nuvem. Para resolver esse problema, use as IDs de logon alternativas. 

Na extensão do NPS, você pode designar um atributo Active Directory a ser usado no lugar do UPN para a autenticação multifator do Azure AD. Isso permite que você proteja seus recursos locais com a verificação em duas etapas sem modificar seu UPNs locais. 

Para configurar as IDs de logon alternativo, vá para `HKLM\SOFTWARE\Microsoft\AzureMfa` e edite os valores de registro a seguir:

| Nome | Type | Valor padrão | Descrição |
| ---- | ---- | ------------- | ----------- |
| LDAP_ALTERNATE_LOGINID_ATTRIBUTE | string | Vazio | Designe o nome do atributo do Active Directory que você deseja usar, em vez do UPN. Esse atributo é usado como o atributo AlternateLoginId. Se esse valor de registro for definido como um [atributo válido do Active Directory](/windows/win32/adschema/attributes-all) (por exemplo, email ou displayName), o valor do atributo será usado no lugar do UPN do usuário para autenticação. Se esse valor do registro estiver vazio ou não estiver configurado, AlternateLoginId estará desabilitado e o UPN do usuário será usado para autenticação. |
| LDAP_FORCE_GLOBAL_CATALOG | booleano | Falso | Use esse sinalizador para forçar o uso do Catálogo Global para pesquisas LDAP ao procurar AlternateLoginId. Configure um controlador de domínio como um Catálogo Global, adicione o atributo AlternateLoginId ao Catálogo Global e, em seguida, habilite esse sinalizador. <br><br> Se LDAP_LOOKUP_FORESTS estiver configurado (não vazio), **esse sinalizador será imposto como true**, independentemente do valor da configuração do registro. Nesse caso, a extensão NPS exige que o Catálogo Global seja configurado com o atributo AlternateLoginId para cada floresta. |
| LDAP_LOOKUP_FORESTS | string | Vazio | Forneça uma lista separada por ponto e vírgula de florestas a pesquisar. Por exemplo, *contoso.com;foobar.com*. Se esse valor do registro estiver configurado, a extensão NPS pesquisará iterativamente em todas as florestas a ordem em que elas foram listadas e retornará o primeiro valor AlternateLoginId bem-sucedido. Se esse valor do registro não estiver configurado, a pesquisa de AlternateLoginId estará limitada ao domínio atual.|

Para solucionar problemas com IDs de logon alternativo, use as etapas recomendadas para [Erros de ID de logon alternativa](howto-mfa-nps-extension-errors.md#alternate-login-id-errors).

## <a name="ip-exceptions"></a>Exceções de IP

Se você precisar monitorar a disponibilidade do servidor, como se os balanceadores de carga verificam se os servidores estão em execução antes de enviarem cargas de trabalho, não desejará que essas verificações sejam bloqueadas por solicitações de verificação. Em vez disso, crie uma lista de endereços IP que você saiba que são usados por contas de serviço e desabilite os requisitos de Autenticação Multifator para essa lista.

Para configurar uma lista de permissões de IP, vá para `HKLM\SOFTWARE\Microsoft\AzureMfa` e configure o seguinte valor de registro:

| Nome | Type | Valor padrão | Descrição |
| ---- | ---- | ------------- | ----------- |
| IP_WHITELIST | string | Vazio | Forneça uma lista separada por ponto e vírgula de endereços IP. Inclua os endereços IP dos computadores dos quais as solicitações de serviço originam-se, como o servidor NAS/VPN. Não há suporte para intervalos de IP e sub-redes. <br><br> Por exemplo, *10.0.0.1;10.0.0.2;10.0.0.3*.

> [!NOTE]
> Essa chave do registro não é criada por padrão pelo instalador e um erro aparece no log AuthZOptCh quando o serviço é reiniciado. Esse erro no log pode ser ignorado, mas se essa chave do registro for criada e deixada vazia se não for necessária, a mensagem de erro não será retornada.

Quando uma solicitação chega de um endereço IP que existe no, a `IP_WHITELIST` verificação em duas etapas é ignorada. A lista de IPs é comparada ao endereço IP que é fornecido no atributo *ratNASIPAddress* da solicitação RADIUS. Se uma solicitação RADIUS entrar sem o atributo ratNASIPAddress, um aviso será registrado: "IP_WHITE_LIST_WARNING:: a lista de permissões de IP está sendo ignorada, pois o IP de origem está ausente no atributo NasIpAddress de solicitação RADIUS.

## <a name="next-steps"></a>Próximas etapas

[Resolver mensagens de erro da extensão do NPS para a autenticação multifator do Azure AD](howto-mfa-nps-extension-errors.md)