---
title: "Azure AD Connect: solucionando erros durante a sincronização | Microsoft Docs"
description: "Explica como solucionar problemas de erros encontrados durante a sincronização com o Azure AD Connect."
services: active-directory
documentationcenter: 
author: karavar
manager: samueld
editor: curtand
ms.assetid: 2209d5ce-0a64-447b-be3a-6f06d47995f8
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: billmath
ms.openlocfilehash: 5a319de69c4e142414ab8f2be980a6576acbf8bb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshooting-errors-during-synchronization"></a>Solucionando erros durante a sincronização
Podem ocorrer erros quando os dados de identidade são sincronizados do AD DS (Active Directory do Windows Server) para o Azure AD (Azure Active Directory). Este artigo fornece uma visão geral dos diferentes tipos de erros de sincronização, alguns dos possíveis cenários que causam esses erros e possíveis maneiras de corrigi-los. Este artigo inclui os tipos de erro comuns e talvez não abranja todos os erros possíveis.

 Este artigo pressupõe que o leitor esteja familiarizado com os [conceitos de criação do Azure AD e Azure AD Connect](active-directory-aadconnect-design-concepts.md) subjacentes.

Com a versão mais recente do Azure AD Connect de \(agosto de 2016 ou superior\), um relatório de erros de sincronização está disponível na [Portal do Azure](https://aka.ms/aadconnecthealth) como parte do Azure AD Connect Health para sincronização.

A partir de 1º de setembro de 2016, o recurso [Duplicar a Resiliência do Atributo do Azure Active Directory](active-directory-aadconnectsyncservice-duplicate-attribute-resiliency.md) estará habilitado por padrão para todos os *novos* locatários do Azure Active Directory. Este recurso será habilitado automaticamente para locatários existentes nos próximos meses.

Azure AD Connect realiza 3 tipos de operações dos diretórios que mantém em sincronia: Importação, Sincronização e Exportação. Erros podem ocorrer em todas as operações. Este artigo se concentra principalmente em erros durante a Exportação para o Azure AD.

## <a name="errors-during-export-to-azure-ad"></a>Erros durante a Exportação para o Azure AD
A seção seguinte descreve os diferentes tipos de erros de sincronização que podem ocorrer durante a operação de exportação para o Azure AD usando o conector do Azure AD. Esse conector pode ser identificado pelo formato de nome, que é "contoso.*onmicrosoft.com*".
Erros durante a exportação para o Azure AD indicam que a operação \(adicionar, atualizar, excluir, etc.\) tentada pelo \(Mecanismo de Sincronização\) do Azure AD Connect no Azure Active Directory falhou.

![Visão geral de Erros de Exportação](./media/active-directory-aadconnect-troubleshoot-sync-errors/Export_Errors_Overview_01.png)

## <a name="data-mismatch-errors"></a>Erros de Incompatibilidade de Dados
### <a name="invalidsoftmatch"></a>InvalidSoftMatch
#### <a name="description"></a>Descrição
* Quando o \(mecanismo de sincronização\) do Azure AD Connect instrui o Azure Active Directory a adicionar ou atualizar objetos, o Azure AD faz a correspondência do objeto de entrada, usando o atributo **sourceAnchor**, ao atributo **immutableId** de objetos no Azure AD. Essa correspondência é chamada de **Correspondência Rígida**.
* Quando o Azure AD **não encontra** nenhum objeto em que o atributo **immutableId** corresponda ao atributo **sourceAnchor** do objeto de entrada, antes de provisionar um novo objeto, ele volta para usar os atributos ProxyAddresses e UserPrincipalName na tentativa de encontrar uma correspondência. Essa correspondência é chamada de **Correspondência Flexível**. A Correspondência Flexível foi projetada para corresponder objetos já presentes no Azure AD (que são originados no Azure AD) com os novos objetos que estão sendo adicionados/atualizados durante a sincronização e que representam a mesma entidade (usuários, grupos) local.
* O erro **InvalidSoftMatch** ocorre quando a correspondência rígida não encontra nenhum objeto correspondente **E** a correspondência flexível localiza um objeto correspondente, mas esse objeto tem um valor de *immutableId* diferente daquele no atributo *SourceAnchor* do objeto de entrada, sugerindo que o objeto correspondente foi sincronizado com outro objeto do Active Directory local.

Em outras palavras, para a correspondência flexível trabalhar, o objeto com o qual a correspondência flexível deve ser realizada não deve ter nenhum valor para o atributo *immutableId*. Se qualquer objeto com *immutableId* definido com um valor estiver falhando na correspondência rígida mas satisfazendo os critérios de correspondência flexível, a operação resultará em um erro de sincronização de InvalidSoftMatch.

O esquema do Azure Active Directory não permite que dois ou mais objetos tenham o mesmo valor para os atributos a seguir. \(Esta não é uma lista completa.\)

* ProxyAddresses
* UserPrincipalName
* onPremisesSecurityIdentifier
* ObjectId

> [!NOTE]
> O recurso [Duplicar a Resiliência do Atributo do Azure AD](active-directory-aadconnectsyncservice-duplicate-attribute-resiliency.md) também está sendo distribuído como o comportamento padrão do Azure Active Directory.  Isso reduzirá o número de erros de sincronização vistos pelo Azure AD Connect (assim como outros clientes de sincronização), tornando o Azure AD mais resiliente quanto à maneira que ele manipula atributos ProxyAddresses e UserPrincipalName duplicados presentes em ambientes do AD local. Este recurso não corrige os erros de duplicação. Assim, os dados ainda precisam ser corrigidos. No entanto, ele permite o provisionamento de novos objetos cujo provisionamento, caso contrário, fica bloqueado devido a valores duplicados no Azure AD. Isso também reduzirá o número de erros de sincronização retornados ao cliente de sincronização.
> Se esse recurso estiver habilitado para seu locatário, você não verá os erros de sincronização InvalidSoftMatch vistos durante o provisionamento de novos objetos.
>
>

#### <a name="example-scenarios-for-invalidsoftmatch"></a>Cenários de Exemplo para InvalidSoftMatch
1. Dois ou mais objetos com o mesmo valor do atributo ProxyAddresses existem no Active Directory local. Somente um deles está sendo provisionado no Azure AD.
2. Dois ou mais objetos com o mesmo valor de userPrincipalName existem no Active Directory local. Somente um deles está sendo provisionado no Azure AD.
3. Um objeto foi adicionado no Active Directory local com o mesmo valor do atributo ProxyAddresses encontrado em um objeto existente no Azure Active Directory. O objeto adicionado localmente não está sendo provisionado no Azure Active Directory.
4. Um objeto foi adicionado no Active Directory local com o mesmo valor do atributo userPrincipalName encontrado em uma conta no Azure Active Directory. O objeto não está sendo provisionado no Azure Active Directory.
5. Uma conta sincronizada foi movida da Floresta A para a Floresta B. O Azure AD Connect (mecanismo de sincronização) estava usando o atributo ObjectGUID para calcular o SourceAnchor. Após a mudança de floresta, o valor de SourceAnchor é diferente. O novo objeto (da floresta B) está falhando em sincronizar com o objeto existente no Azure AD.
6. Um objeto sincronizado foi excluído acidentalmente do Active Directory local e um novo objeto foi criado no Active Directory para a mesma entidade (por exemplo, um usuário) sem excluir a conta no Azure Active Directory. A nova conta falha em sincronizar com o objeto existente do Azure AD.
7. O Azure AD Connect foi desinstalado e reinstalado. Durante a reinstalação, um atributo diferente foi escolhido como o SourceAnchor. Todos os objetos que tinham sido sincronizados anteriormente interromperam a sincronização com o erro InvalidSoftMatch.

#### <a name="example-case"></a>Caso de exemplo:
1. **Bob Smith** é um usuário sincronizado no Azure Active Directory do Active Directory local de *contoso.com*
2. O **UserPrincipalName** de Bob Smith está definido como **bobs@contoso.com**.
3. **"abcdefghijklmnopqrstuv=="** é o **SourceAnchor** calculado pelo Azure AD Connect usando o **objectGUID** de Bob Smith do Active Directory local, que é a **immutableId** de Bob Smith no Azure Active Directory.
4. Bob também tem os seguintes valores para o atributo **proxyAddresses**:
   * smtp:bobs@contoso.com
   * smtp:bob.smith@contoso.com
   * **smtp:bob@contoso.com**
5. Um novo usuário, **Bob Taylor**, é adicionado ao Active Directory local.
6. O **UserPrincipalName** de Bob Taylor é definido como **bobt@contoso.com**.
7. **"abcdefghijkl0123456789==""** é o **sourceAnchor** calculado pelo Azure AD Connect usando o **objectGUID** de Bob Taylor no Active Directory local. O objeto de Bob Taylor ainda NÃO foi sincronizado com o Azure Active Directory.
8. Bob Taylor tem os valores a seguir para o atributo proxyAddresses
   * smtp:bobt@contoso.com
   * smtp:bob.taylor@contoso.com
   * **smtp:bob@contoso.com**
9. Durante a sincronização, o Azure AD Connect reconhecerá a adição de Bob Taylor no Active Directory local e pedirá ao Azure AD para fazer a mesma alteração.
10. O Azure AD executará primeiro a correspondência rígida. Ou seja, ele pesquisará se há qualquer objeto com a immutableId igual a "abcdefghijkl0123456789==". Correspondência de disco rígida falhará, pois nenhum outro objeto no Azure AD terá essa immutableId.
11. O Azure AD tentará então realizar uma correspondência flexível para Bob Taylor. Ou seja, ele pesquisará se há algum objeto com proxyAddresses igual aos três valores, inclusive smtp:bob@contoso.com
12. O Azure AD encontrará o objeto de Bob Smith que satisfaz os critérios de correspondência flexível. No entanto, esse objeto tem o valor de immutableId = "abcdefghijklmnopqrstuv==". Isso indica que esse objeto foi sincronizado de outro objeto do Active Directory local. Assim, o Azure AD não pode realizar uma correspondência flexível desses objetos e resulta em um erro de sincronização **InvalidSoftMatch**.

#### <a name="how-to-fix-invalidsoftmatch-error"></a>Como corrigir o erro InvalidSoftMatch
O motivo mais comum para o erro InvalidSoftMatch é que dois objetos com valores diferentes para \(immutableId\) de SourceAnchor tenham o mesmo valor para os atributos ProxyAddresses e/ou UserPrincipalName, que são usados durante o processo de correspondência flexível no Azure AD. Para corrigir a Correspondência Flexível Inválida

1. Identifique o valor duplicado de proxyAddresses, userPrincipalName ou outro o atributo que está causando o erro. Identifique também quais dois \(ou mais\) os objetos estão envolvidos no conflito. O relatório gerado pelo [Azure AD Connect Health para sincronização](https://aka.ms/aadchsyncerrors) pode ajudá-lo a identificar os dois objetos.
2. Identifique qual objeto deve continuar a ter o valor duplicado e qual não deve.
3. Remova o valor duplicado do objeto que NÃO deve ter esse valor. Observe que você deve fazer a alteração no diretório do qual o objeto foi originado. Em alguns casos, talvez seja necessário excluir um dos objetos em conflito.
4. Se você fez a alteração no AD local, permita que o Azure AD Connect sincronize essa alteração.

Observe que o Relatório de erros de sincronização no Azure AD Connect Health para a sincronização é atualizado a cada 30 minutos e inclui os erros da tentativa de sincronização mais recente.

> [!NOTE]
> ImmutableId, por definição, não deve ser alterada durante o tempo de vida do objeto. Se o Azure AD Connect não tiver sido configurado com alguns dos cenários da lista acima em mente, você poderá acabar em uma situação em que o Azure AD Connect calcula um valor diferente de SourceAnchor para o objeto do AD que representa a mesma entidade (mesmo grupo, usuário, contato, etc.) que tem um Objeto do Azure AD existente que você deseja continuar usando.
>
>

#### <a name="related-articles"></a>Artigos relacionados
* [Atributos duplicados ou inválidos impedem a sincronização de diretórios no Office 365](https://support.microsoft.com/en-us/kb/2647098)

### <a name="objecttypemismatch"></a>ObjectTypeMismatch
#### <a name="description"></a>Descrição
Quando o Azure AD tenta fazer a correspondência flexível entre dois objetos, é possível que dois objetos com "tipo de objeto" diferente (como Usuário, Grupo, Contato, etc.) tenham os mesmos valores para os atributos usados para executar a correspondência flexível. Como a eliminação de duplicação desses atributos não é permitida no Azure AD, a operação pode resultar no erro de sincronização "ObjectTypeMismatch".

#### <a name="example-scenarios-for-objecttypemismatch-error"></a>Cenários de exemplo para o erro ObjectTypeMismatch
* Um grupo de segurança habilitado para email é criado no Office 365. O administrador adiciona um novo usuário ou contato no AD local (que ainda não está sincronizado com o Azure AD) com o mesmo valor para o atributo ProxyAddresses que o utilizado no grupo do Office 365.

#### <a name="example-case"></a>Caso de exemplo
1. O administrador cria um novo grupo de segurança habilitado para email no Office 365 para o departamento fiscal e fornece um endereço de email como tax@contoso.com. Isso atribui o atributo ProxyAddresses para esse grupo com o valor de **smtp:tax@contoso.com**
2. Um novo usuário ingressa em Contoso.com e uma conta é criada para o usuário local com proxyAddress como **smtp:tax@contoso.com**
3. Quando o Azure AD Connect sincronizar a nova conta de usuário, ele receberá o erro "ObjectTypeMismatch".

#### <a name="how-to-fix-objecttypemismatch-error"></a>Como corrigir o erro ObjectTypeMismatch
O motivo mais comum para o erro ObjectTypeMismatch é que dois objetos de tipo diferente (usuário, grupo, contatos, etc.) tenham o mesmo valor para o atributo ProxyAddresses. Para corrigir o ObjectTypeMismatch:

1. Identifique o valor duplicado de proxyAddresses (ou outro atributo) que está causando o erro. Identifique também quais dois \(ou mais\) os objetos estão envolvidos no conflito. O relatório gerado pelo [Azure AD Connect Health para sincronização](https://aka.ms/aadchsyncerrors) pode ajudá-lo a identificar os dois objetos.
2. Identifique qual objeto deve continuar a ter o valor duplicado e qual não deve.
3. Remova o valor duplicado do objeto que NÃO deve ter esse valor. Observe que você deve fazer a alteração no diretório do qual o objeto foi originado. Em alguns casos, talvez seja necessário excluir um dos objetos em conflito.
4. Se você fez a alteração no AD local, permita que o Azure AD Connect sincronize essa alteração. O Relatório de erros de sincronização no Azure AD Connect Health para a sincronização é atualizado a cada 30 minutos e inclui os erros da tentativa de sincronização mais recente.

## <a name="duplicate-attributes"></a>Atributos Duplicados
### <a name="attributevaluemustbeunique"></a>AttributeValueMustBeUnique
#### <a name="description"></a>Descrição
O esquema do Azure Active Directory não permite que dois ou mais objetos tenham o mesmo valor para os atributos a seguir. Ou seja, cada objeto no Azure AD é forçado a ter um valor exclusivo desses atributos em uma determinada instância.

* ProxyAddresses
* UserPrincipalName

Se o Azure AD Connect tentar adicionar um novo objeto ou atualizar um objeto existente com um valor para os atributos acima que já estiver atribuído a outro objeto no Azure Active Directory, a operação resultará no erro de sincronização "AttributeValueMustBeUnique".

#### <a name="possible-scenarios"></a>Cenários Possíveis:
1. Um valor duplicado é atribuído a um objeto já sincronizado, que está em conflito com outro objeto sincronizado.

#### <a name="example-case"></a>Caso de exemplo:
1. **Bob Smith** é um usuário sincronizado no Azure Active Directory do Active Directory local de contoso.com
2. O **UserPrincipalName** local de Bob Smith está definido como **bobs@contoso.com**.
3. Bob também tem os seguintes valores para o atributo **proxyAddresses**:
   * smtp:bobs@contoso.com
   * smtp:bob.smith@contoso.com
   * **smtp:bob@contoso.com**
4. Um novo usuário, **Bob Taylor**, é adicionado ao Active Directory local.
5. O **UserPrincipalName** de Bob Taylor é definido como **bobt@contoso.com**.
6. **Bob Taylor** tem os valores a seguir para o atributo i **ProxyAddresses**. smtp:bobt@contoso.com ii. smtp:bob.taylor@contoso.com
7. O objeto de Bob Taylor foi sincronizado com êxito ao Azure AD.
8. O administrador decidiu atualizar o atributo **ProxyAddresses** de Bob Taylor com o seguinte valor: i. **smtp:bob@contoso.com**
9. O Azure AD tentará atualizar o objeto de Bob Taylor no Azure AD com o valor acima, mas essa operação falhará porque o valor de ProxyAddresses já está atribuído a Bob Smith, resultando em um erro "AttributeValueMustBeUnique".

#### <a name="how-to-fix-attributevaluemustbeunique-error"></a>Como corrigir o erro AttributeValueMustBeUnique
O motivo mais comum para o erro AttributeValueMustBeUnique é que dois objetos com valores diferentes para \(immutableId\) de SourceAnchor tenham o mesmo valor para os atributos ProxyAddresses e/ou UserPrincipalName. Para corrigir o erro AttributeValueMustBeUnique

1. Identifique o valor duplicado de proxyAddresses, userPrincipalName ou outro o atributo que está causando o erro. Identifique também quais dois \(ou mais\) os objetos estão envolvidos no conflito. O relatório gerado pelo [Azure AD Connect Health para sincronização](https://aka.ms/aadchsyncerrors) pode ajudá-lo a identificar os dois objetos.
2. Identifique qual objeto deve continuar a ter o valor duplicado e qual não deve.
3. Remova o valor duplicado do objeto que NÃO deve ter esse valor. Observe que você deve fazer a alteração no diretório do qual o objeto foi originado. Em alguns casos, talvez seja necessário excluir um dos objetos em conflito.
4. Se você fez a alteração no AD local, permita que o Azure AD Connect sincronize essa alteração para que o erro seja corrigido.

#### <a name="related-articles"></a>Artigos relacionados
-[Atributos duplicados ou inválidos impedem a sincronização de diretórios no Office 365](https://support.microsoft.com/en-us/kb/2647098)

## <a name="data-validation-failures"></a>Falha na Validação de Dados
### <a name="identitydatavalidationfailed"></a>IdentityDataValidationFailed
#### <a name="description"></a>Descrição
O Azure Active Directory impõe várias restrições nos dados antes de permitir que dados sejam gravados no diretório. Isso é para garantir que os usuários finais obtenham melhores experiências possíveis ao usar os aplicativos que dependem desses dados.

#### <a name="scenarios"></a>Cenários
a. O valor do atributo UserPrincipalName tem caracteres inválidos ou sem suporte.
b. O atributo UserPrincipalName não segue o formato necessário.

#### <a name="how-to-fix-identitydatavalidationfailed-error"></a>Como corrigir o erro IdentityDataValidationFailed
a. Certifique-se de que o atributo userPrincipalName tem caracteres com suporte e o formato necessário.

#### <a name="related-articles"></a>Artigos relacionados
* [Prepare to provision users through directory synchronization to Office 365](https://support.office.com/en-us/article/Prepare-to-provision-users-through-directory-synchronization-to-Office-365-01920974-9e6f-4331-a370-13aea4e82b3e) (Preparar para provisionar usuários por meio da sincronização de diretório para o Office 365)

### <a name="federateddomainchangeerror"></a>FederatedDomainChangeError
#### <a name="description"></a>Descrição
Esse é um caso específico que resulta em um erro de sincronização **"FederatedDomainChangeError"** quando o sufixo UserPrincipalName de um usuário é alterado de um domínio federado para outro.

#### <a name="scenarios"></a>Cenários
Para um usuário sincronizado, o sufixo UserPrincipalName foi alterado de um domínio federado para outro domínio federado local. Por exemplo, *UserPrincipalName = bob@contoso.com* foi alterado para *UserPrincipalName = bob@fabrikam.com*.

#### <a name="example"></a>Exemplo
1. Bob Smith, uma conta para Contoso.com, é adicionado como um novo usuário no Active Directory com o UserPrincipalName bob@contoso.com
2. Bob passa para uma divisão diferente de Contoso.com chamada Fabrikam.com e seu UserPrincipalName é alterado para bob@fabrikam.com
3. Tanto contoso.com quanto fabrikam.com são domínios federados com o Azure Active Directory.
4. O userPrincipalName de Bob não é atualizado e resulta em um erro de sincronização "FederatedDomainChangeError".

#### <a name="how-to-fix"></a>Como corrigir
Se o sufixo UserPrincipalName de um usuário tiver sido atualizado de bob@**contoso.com** para bob@**fabrikam.com**, em que ambos **contoso.com** e **fabrikam.com** são **domínios federados**, siga estas etapas para corrigir o erro de sincronização

1. Atualize o UserPrincipalName do usuário no Azure AD de bob@contoso.com para bob@contoso.onmicrosoft.com. Você pode usar o seguinte comando do PowerShell com o Módulo do PowerShell do Azure AD: `Set-MsolUserPrincipalName -UserPrincipalName bob@contoso.com -NewUserPrincipalName bob@contoso.onmicrosoft.com`
2. Permita que o próximo ciclo de sincronização tentar a sincronização. Dessa vez, a sincronização será bem-sucedida e atualizará o UserPrincipalName de Bob para bob@fabrikam.com conforme esperado.

#### <a name="related-articles"></a>Artigos relacionados
* [As alterações não são sincronizadas pela ferramenta de sincronização do Azure Active Directory depois que você altera o UPN de uma conta de usuário para usar um domínio federado diferente](https://support.microsoft.com/en-us/help/2669550/changes-aren-t-synced-by-the-azure-active-directory-sync-tool-after-you-change-the-upn-of-a-user-account-to-use-a-different-federated-domain)

## <a name="largeobject"></a>LargeObject
### <a name="description"></a>Descrição
Quando um atributo excede o limite de tamanho permitido, o limite de comprimento ou o limite de contagem definidos pelo esquema do Azure Active Directory, a operação de sincronização resultará no erro de sincronização **LargeObject** ou **ExceededAllowedLength**. Geralmente este erro ocorre para os atributos seguir

* userCertificate
* userSMIMECertificate
* thumbnailPhoto
* proxyAddresses

### <a name="possible-scenarios"></a>Cenários possíveis
1. O atributo userCertificate de Bob está armazenando certificados em excesso atribuídos a Bob. Eles podem incluir certificados mais antigos e expirados. O limite rígido é de 15 certificados. Para obter mais informações sobre como lidar com erros LargeObject com o atributo userCertificate, consulte o artigo [Tratamento de erros LargeObject causados po atributo userCertificate](active-directory-aadconnectsync-largeobjecterror-usercertificate.md).
2. O atributo userSMIMECertificate de Bob está armazenando certificados em excesso atribuídos a Bob. Eles podem incluir certificados mais antigos e expirados. O limite rígido é de 15 certificados.
3. O thumbnailPhoto de Bob, definido no Active Directory, é muito grande para ser sincronizado no Azure AD.
4. Durante a população automática do atributo ProxyAddresses no Active Directory, um objeto tem muitos ProxyAddresses atribuídos.

### <a name="how-to-fix"></a>Como corrigir
1. Certifique-se de que o atributo que está causando o erro esteja dentro do limite permitido.

## <a name="related-links"></a>Links relacionados
* [Localizar objetos do Active Directory no Centro Administrativo do Active Directory](https://technet.microsoft.com/library/dd560661.aspx)
* [Como consultar o Azure Active Directory para um objeto usando o Azure Active Directory PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx)
