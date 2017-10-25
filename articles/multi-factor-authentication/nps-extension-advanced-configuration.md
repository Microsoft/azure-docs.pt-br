---
title: "Configurar a extensão NPS da MFA do Azure | Microsoft Docs"
description: "Depois de instalar a extensão NPS, siga estas etapas para configuração avançada como lista de permissões de IP e substituição de UPN."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/14/2017
ms.author: kgremban
ms.reviewer: yossib
ms.custom: it-pro
ms.openlocfilehash: ef922668f080b8f02f07c2f9724f5a98171fb754
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="advanced-configuration-options-for-the-nps-extension-for-multi-factor-authentication"></a>Opções de configuração avançada para a extensão NPS para autenticação multifator

A extensão NPS (servidor de políticas de rede) estende os recursos de Autenticação Multifator do Azure baseados em nuvem para sua infraestrutura local. Este artigo pressupõe que você já tenha a extensão instalada e agora deseje saber como personalizá-la para as suas necessidades. 

## <a name="alternate-login-id"></a>ID de logon alternativa

Uma vez que a extensão NPS conecta-se aos seus diretórios locais e de nuvem, você pode encontrar um problema em que seus UPNs (nomes de entidade de usuário) locais não correspondem aos nomes na nuvem. Para resolver esse problema, use as IDs de logon alternativas. 

Dentro da extensão do NPS, você pode designar um atributo do Active Directory a ser usado no lugar do UPN para a Autenticação Multifator do Azure. Isso permite que você proteja seus recursos locais com a verificação em duas etapas sem modificar seu UPNs locais. 

Para configurar as IDs de logon alternativo, vá para `HKLM\SOFTWARE\Microsoft\AzureMfa` e edite os valores de registro a seguir:

| Nome | Tipo | Valor padrão | Descrição |
| ---- | ---- | ------------- | ----------- |
| LDAP_ALTERNATE_LOGINID_ATTRIBUTE | string | Vazio | Designe o nome do atributo do Active Directory que você deseja usar, em vez do UPN. Esse atributo é usado como o atributo AlternateLoginId. Se esse valor de registro for definido como um [atributo válido do Active Directory](https://msdn.microsoft.com/library/ms675090.aspx) (por exemplo, email ou displayName), o valor do atributo será usado no lugar do UPN do usuário para autenticação. Se esse valor do registro estiver vazio ou não estiver configurado, AlternateLoginId estará desabilitado e o UPN do usuário será usado para autenticação. |
| LDAP_FORCE_GLOBAL_CATALOG | Booliano | Falso | Use esse sinalizador para forçar o uso do Catálogo Global para pesquisas LDAP ao procurar AlternateLoginId. Configure um controlador de domínio como um Catálogo Global, adicione o atributo AlternateLoginId ao Catálogo Global e, em seguida, habilite esse sinalizador. <br><br> Se LDAP_LOOKUP_FORESTS estiver configurado (não vazio), **esse sinalizador será imposto como true**, independentemente do valor da configuração do registro. Nesse caso, a extensão NPS exige que o Catálogo Global seja configurado com o atributo AlternateLoginId para cada floresta. |
| LDAP_LOOKUP_FORESTS | string | Vazio | Forneça uma lista separada por ponto e vírgula de florestas a pesquisar. Por exemplo, *contoso.com;foobar.com*. Se esse valor do registro estiver configurado, a extensão NPS pesquisará iterativamente em todas as florestas a ordem em que elas foram listadas e retornará o primeiro valor AlternateLoginId bem-sucedido. Se esse valor do registro não estiver configurado, a pesquisa de AlternateLoginId estará limitada ao domínio atual.|

Para solucionar problemas com IDs de logon alternativo, use as etapas recomendadas para [Erros de ID de logon alternativa](multi-factor-authentication-nps-errors.md#alternate-login-id-errors).

## <a name="ip-exceptions"></a>Exceções de IP

Se você precisar monitorar a disponibilidade do servidor, como se os balanceadores de carga verificam se os servidores estão em execução antes de enviarem cargas de trabalho, não desejará que essas verificações sejam bloqueadas por solicitações de verificação. Em vez disso, crie uma lista de endereços IP que você saiba que são usados por contas de serviço e desabilite os requisitos de Autenticação Multifator para essa lista. 

Para configurar uma lista de permissões de IP, vá para `HKLM\SOFTWARE\Microsoft\AzureMfa` e configure o seguinte valor de registro: 

| Nome | Tipo | Valor padrão | Descrição |
| ---- | ---- | ------------- | ----------- |
| IP_WHITELIST | string | Vazio | Forneça uma lista separada por ponto e vírgula de endereços IP. Inclua os endereços IP dos computadores dos quais as solicitações de serviço originam-se, como o servidor NAS/VPN. Os intervalos IP que são sub-redes não têm suporte. <br><br> Por exemplo, *10.0.0.1;10.0.0.2;10.0.0.3*.

Quando uma solicitação chega de um endereço IP existente na lista de permissões, a verificação em duas etapas é ignorada. A lista de permissões de IP é comparada ao endereço IP fornecido no atributo *ratNASIPAddress* da solicitação RADIUS. Se uma solicitação RADIUS chegar sem o atributo ratNASIPAddress, o seguinte aviso será registrado: "P_WHITE_LIST_WARNING::IP A lista de permissões está sendo ignorada, uma vez que o IP de origem está ausente na solicitação do RADIUS no atributo NasIpAddress."

## <a name="next-steps"></a>Próximas etapas

[Resolver mensagens de erro da extensão NPS da Autenticação Multifator do Azure](multi-factor-authentication-nps-errors.md)