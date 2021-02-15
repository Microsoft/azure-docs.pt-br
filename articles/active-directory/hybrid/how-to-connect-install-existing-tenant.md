---
title: 'Azure AD Connect: Quando você já tiver o Azure AD | Microsoft Docs'
description: Este tópico descreve como usar o Connect quando você tem um locatário existente do Azure AD.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 04/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 68251270b6273f5a07391138e5c7210f1c46ba5a
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2020
ms.locfileid: "93420522"
---
# <a name="azure-ad-connect-when-you-have-an-existing-tenant"></a>Azure AD Connect: quando você tem um locatário existente
A maioria dos tópicos sobre como usar o Azure AD Connect pressupõe que você inicie com um novo locatário do Azure AD e que não exista nenhum usuário nem outros objetos. Mas, se você tiver começado com um locatário do Azure AD, o preencheu com usuários e outros objetos, e agora deseja usar o Connect, este tópico é para você.

## <a name="the-basics"></a>Noções básicas
Um objeto no Azure AD é controlado na nuvem (Azure AD) ou no local. Em um único objeto, você não pode gerenciar alguns atributos locais e outros atributos no Azure AD. Cada objeto tem um sinalizador que indica onde o objeto é gerenciado.

Você pode gerenciar alguns usuários locais e outros na nuvem. Um cenário comum para essa configuração é uma organização com uma combinação de funcionários de contabilidade e vendedores. Os funcionários de contabilidade têm uma conta local do AD, mas os vendedores não, eles têm uma conta no Azure AD. Você deve gerenciar alguns usuários locais e outros no Azure AD.

Se você começar a gerenciar usuários no Azure AD que também estão no AD local e, mais tarde, desejar usar o Connect, existem outros problemas que devem ser considerados.

## <a name="sync-with-existing-users-in-azure-ad"></a>Sincronia com usuários existentes no Azure AD
Quando você instala o Azure AD Connect e inicia a sincronização, o serviço de sincronização do AD do Azure (no Azure AD) faz uma verificação em cada novo objeto e tenta encontrar um objeto existente para fazer a correspondência. Existem três atributos usados para esse processo: **userPrincipalName** , **proxyAddresses** e **sourceAnchor**/**immutableID**. Uma correspondência entre **userPrincipalName** e **proxyAddresses** é conhecida como uma **correspondência flexível**. Uma correspondência com **sourceAnchor** é conhecida como **correspondência rígida**. Para o atributo **proxyAddresses** , apenas o valor com **SMTP:** , que é o email principal, é usado para a avaliação.

A correspondência é avaliada apenas para objetos novos provenientes do Connect. Se você alterar um objeto existente para que ele corresponda a um desses atributos, ocorrerá um erro.

Se o Azure AD encontrar um objeto cujos valores de atributo são os mesmos para um objeto proveniente do Connect e que já esteja presente no Azure AD, o objeto no Azure AD será controlado pelo Connect. O objeto anteriormente gerenciado por nuvem é sinalizado como gerenciado no local. Todos os atributos no Azure AD com um valor no AD local são substituídos pelo valor local.

> [!WARNING]
> Como todos os atributos no Azure AD serão substituídos pelo valor local, verifique se os dados no local estão corretos. Por exemplo, se você tiver apenas endereços de email gerenciados no Microsoft 365 e não for mantido atualizado no AD DS local, perderá os valores no Azure AD/Microsoft 365 não presentes no AD DS.

> [!IMPORTANT]
> Caso use a sincronização de senha, que é sempre usada pelas configurações expressas, a senha no Azure AD será substituída pela a senha do AD local. Se os usuários são usados para gerenciar senhas diferentes, informe-os de que eles devem usar a senha local de quando você instalou o Connect.

A seção anterior e o aviso devem ser considerados no planejamento. Se existem muitas alterações no Azure AD que não refletem no AD DS local, planeje como popular o AD DS com os valores atualizados antes de sincronizar seus objetos com o Azure AD Connect.

Se você correspondeu os objetos com uma correspondência flexível, o **sourceAnchor** será adicionado ao objeto no Azure AD para que uma correspondência rígida possa ser usada posteriormente.

>[!IMPORTANT]
> A Microsoft não recomenda a sincronização de contas de locais com contas administrativas pré-existentes no Azure Active Directory.

### <a name="hard-match-vs-soft-match"></a>Correspondência rígida x correspondência flexível
Para uma nova instalação do Connect, não há nenhuma diferença prática entre uma correspondência rígida e uma correspondência flexível. A diferença está em uma situação de recuperação de desastre. Se você tiver perdido o seu servidor com o Azure AD Connect, reinstale uma nova instância para não perder os dados. Um objeto com um sourceAnchor é enviado para o Connect durante a instalação inicial. Então a correspondência poderá ser avaliada pelo cliente (Azure AD Connect), que é muito mais rápido do que fazer o mesmo no Azure AD. Uma correspondência rígida é avaliada pelo Connect e pelo Azure AD. Uma correspondência flexível é avaliada apenas pelo Azure AD.

### <a name="other-objects-than-users"></a>Outros objetos que não são usuários
Para grupos habilitados para email e contatos, você pode fazer uma correspondência dinâmica com base no proxyAddresses. Correspondência fixa não se aplica, já que só é possível atualizar o sourceAnchor/immutableID (usando o PowerShell) em Usuários. Para grupos que não estão habilitados para email, no momento não há suporte para correspondência dinâmica nem fixa.

### <a name="admin-role-considerations"></a>Considerações de função de administrador
Para impedir que usuários locais não confiáveis façam a correspondência com um usuário de nuvem que tenha qualquer função de administrador, Azure AD Connect não corresponderá a objetos de usuário locais com objetos que têm uma função de administrador. Isso é por padrão. Para solucionar esse comportamento, você pode fazer o seguinte:

1.  Remova as funções de diretório do objeto de usuário somente em nuvem.
2.  Se houvesse uma falha na tentativa de sincronização do usuário, exclua o objeto em quarentena na nuvem.
3.  Disparar uma sincronização.
4.  Opcionalmente, adicione as funções de diretório de volta ao objeto de usuário na nuvem assim que a correspondência tiver ocorrido.



## <a name="create-a-new-on-premises-active-directory-from-data-in-azure-ad"></a>Criação de um Active Directory local novo a partir dos dados no Azure AD
Alguns clientes começam com uma solução somente em nuvem com o Azure AD e não têm um AD local. Mais tarde, eles desejam consumir recursos locais e criar um AD local com base nos dados do Azure AD. O Azure AD Connect não pode ajudá-lo nesse cenário. Ele não cria os usuários locais e não tem capacidade de definir a senha local igual à do Azure AD.

Se oferecer suporte a LOBs (aplicativos de linha de negócios) é o único motivo para adicionar o AD local, considere usar o [Azure AD Domain Services](../../active-directory-domain-services/index.yml).

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre [Como integrar suas identidades locais ao Active Directory do Azure](whatis-hybrid-identity.md).
