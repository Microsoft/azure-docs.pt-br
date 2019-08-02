---
title: 'Sincronização do Azure AD Connect: noções básicas sobre a configuração padrão | Microsoft Docs'
description: Este artigo descreve a configuração padrão na sincronização do Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ed876f22-6892-4b9d-acbe-6a2d112f1cd1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: bfaf3cc9b113ff10766f7a17bd7bf09ffa619a8e
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2019
ms.locfileid: "68227430"
---
# <a name="azure-ad-connect-sync-understanding-the-default-configuration"></a>Sincronização do Azure AD Connect: Noções básicas sobre a configuração padrão
Este artigo explica as regras da configuração pronta para uso. Ele documenta as regras e como elas afetarão a configuração. Ele também o orienta durante a configuração padrão da sincronização do Azure AD Connect. O objetivo é que o leitor compreenda como o modelo de configuração, chamado de provisionamento declarativo, está funcionando em um exemplo do mundo real. Este artigo pressupõe que você já instalou e configurou a sincronização do Azure AD Connect usando o assistente de instalação.

Para entender os detalhes do modelo de configuração, leia [Noções básicas do provisionamento declarativo](concept-azure-ad-connect-sync-declarative-provisioning.md).

## <a name="out-of-box-rules-from-on-premises-to-azure-ad"></a>Regras prontas para uso do local ao AD do Azure
As expressões a seguir podem ser encontradas na configuração pronta para uso.

### <a name="user-out-of-box-rules"></a>Regras prontas para uso do usuário
Essas regras também são aplicadas ao tipo de objeto iNetOrgPerson.

Um objeto de usuário deve atender ao seguinte para ser sincronizado:

* Deve ter um sourceAnchor.
* Depois de o objeto ser criado no Azure AD, o sourceAnchor não poderá ser alterado. Se o valor for alterado no local, o objeto interromperá a sincronização até que o sourceAnchor seja alterado para seu valor anterior.
* Deve ter o atributo accountEnabled (userAccountControl) preenchido. Com um local no Active Directory, esse atributo sempre está presente e preenchido.

Os seguintes objetos de usuário **não** estão sincronizados ao AD do Azure:

* `IsPresent([isCriticalSystemObject])`. Certifique-se de vários objetos prontos para uso no Active Directory, como a conta de administrador interna, não sejam sincronizados.
* `IsPresent([sAMAccountName]) = False`. Certifique-se de que os objetos de usuário sem o atributo sAMAccountName não estejam sincronizados. Esse caso ocorre na prática apenas em um domínio atualizado do NT4.
* `Left([sAMAccountName], 4) = "AAD_"`, `Left([sAMAccountName], 5) = "MSOL_"`. Não sincronize a conta de serviço usada pelo Azure AD Connect sync e suas versões anteriores.
* Não sincronize contas do Exchange que não funcionem no Exchange Online.
  * `[sAMAccountName] = "SUPPORT_388945a0"`
  * `Left([mailNickname], 14) = "SystemMailbox{"`
  * `(Left([mailNickname], 4) = "CAS_" && (InStr([mailNickname], "}") > 0))`
  * `(Left([sAMAccountName], 4) = "CAS_" && (InStr([sAMAccountName], "}")> 0))`
* Não sincronize objetos que não funcionem no Exchange Online.
  `CBool(IIF(IsPresent([msExchRecipientTypeDetails]),BitAnd([msExchRecipientTypeDetails],&H21C07000) > 0,NULL))`  
  Essa máscara de bit (& H21C07000) filtraria os objetos a seguir:
  * Pasta pública habilitada para Email (em versão prévia desde a versão 1.1.524.0)
  * Caixa de correio do Atendedor do sistema
  * Caixa de correio do banco de dados de correio (caixa de correio do sistema)
  * Grupo de segurança universal (não se aplica a um usuário, mas está presente por motivos herdados)
  * Grupo não universal (não se aplica a um usuário, mas está presente por motivos herdados)
  * Plano de caixa de correio
  * Caixa de correio de descoberta
* `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. Não sincronize nenhum objetos vítima de replicação.

As regras de atributos a seguir se aplicam:

* `sourceAnchor <- IIF([msExchRecipientTypeDetails]=2,NULL,..)`. O atributo sourceAnchor não vem de uma caixa de correio vinculada. Supõe-se que, se uma caixa de correio vinculada tiver sido encontrada, a conta real será adicionada posteriormente.
* Atributos relacionados ao Exchange só são sincronizados se o atributo **mailNickName** tiver um valor.
* Quando há várias florestas, os atributos são consumidos na seguinte ordem:
  1. Atributos relacionados a entrada (por exemplo, userPrincipalName) são a contribuição da floresta com uma conta habilitada.
  2. Atributos que podem ser encontrados em uma GAL (Lista de Endereços Global) do Exchange são a contribuição da floresta com uma caixa de correio do Exchange.
  3. Se não for possível localizar nenhuma caixa de correio, esses atributos poderão vir de qualquer floresta.
  4. Atributos relacionados ao Exchange (atributos técnicos não visíveis na GAL) são a contribuição da floresta em que `mailNickname ISNOTNULL`.
  5. Se houver várias florestas que atendam a uma dessas regras, a ordem de criação (data/hora) dos conectores (florestas) será usada para determinar qual floresta contribuirá com os atributos. A primeira floresta conectada será a primeira floresta a ser sincronizada. 

### <a name="contact-out-of-box-rules"></a>Regras prontas para uso de contato
Um objeto de contato deve atender ao seguinte para ser sincronizado:

* O contato deve ser habilitado para email. É verificado com as seguintes regras:
  * `IsPresent([proxyAddresses]) = True)`. O atributo proxyAddresses deve ser preenchido.
  * Um endereço de email principal pode ser encontrado no atributo proxyAddresses ou no atributo de email. A presença de uma \@ é usada para verificar se o conteúdo é um endereço de email. Uma dessas duas regras deve ser avaliada como True.
    * `(Contains([proxyAddresses], "SMTP:") > 0) && (InStr(Item([proxyAddresses], Contains([proxyAddresses], "SMTP:")), "@") > 0))`. Existe uma entrada com "SMTP:" e, se houver, uma \@ pode ser encontrado na cadeia de caracteres?
    * `(IsPresent([mail]) = True && (InStr([mail], "@") > 0)`. O atributo de email está preenchido e, se estiver, uma \@ pode ser encontrado na cadeia de caracteres?

Os seguintes objetos de contato **não** estão sincronizados ao AD do Azure:

* `IsPresent([isCriticalSystemObject])`. Certifique-se de que nenhum objeto de contato marcado como crítico seja sincronizado. Não deve ser nenhum com uma configuração padrão.
* `((InStr([displayName], "(MSOL)") > 0) && (CBool([msExchHideFromAddressLists])))`.
* `(Left([mailNickname], 4) = "CAS_" && (InStr([mailNickname], "}") > 0))`. Esses objetos não funcionam no Exchange Online.
* `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. Não sincronize nenhum objetos vítima de replicação.

### <a name="group-out-of-box-rules"></a>Grupo prontas para uso de grupo
Um objeto de grupo deve atender ao seguinte para ser sincronizado:

* Deve ter menos de 50 mil membros. Essa contagem é o número de membros do grupo local.
  * Se tiver mais membros antes de a sincronização iniciar pela primeira vez, o grupo não será sincronizado.
  * Se o número de membros crescer desde quando ele foi inicialmente criado, quando ele atingir 50 mil membros, a sincronização será interrompida até que a contagem de associação seja inferior a 50 mil novamente.
  * Observação: o Microsoft Azure Active Directory também impõe a contagem de 50 mil associações. Você não pode sincronizar grupos com mais membros mesmo que modifique ou remova essa regra.
* Se o grupo for um **Grupo de Distribuição**, ele também deverá ser habilitado para email. Consulte [Regras prontas para uso de contato](#contact-out-of-box-rules) para ver como essa regra é aplicada.

Os seguintes objetos de grupo **não** estão sincronizados ao AD do Azure:

* `IsPresent([isCriticalSystemObject])`. Certifique-se de vários objetos prontos para uso no Active Directory, como o grupo de administrador interno, não sejam sincronizados.
* `[sAMAccountName] = "MSOL_AD_Sync_RichCoexistence"`. Grupo herdado usado pelo DirSync.
* `BitAnd([msExchRecipientTypeDetails],&amp;H40000000)`. Grupo de funções.
* `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. Não sincronize nenhum objetos vítima de replicação.

### <a name="foreignsecurityprincipal-out-of-box-rules"></a>Regras prontas para uso de ForeignSecurityPrincipal
FSPs são associados a "qualquer" objeto (\*) no metaverso. Na verdade, essa junção ocorre apenas para usuários e grupos de segurança. Essa configuração garante que as associações entre florestas sejam resolvidas e representadas corretamente no Azure AD.

### <a name="computer-out-of-box-rules"></a>Regras prontas para uso de computador
Um objeto de computador deve atender ao seguinte para ser sincronizado:

* `userCertificate ISNOTNULL`. Somente os computadores Windows 10 populam esse atributo. Todos os objetos de computador com um valor nesse atributo serão sincronizados.

## <a name="understanding-the-out-of-box-rules-scenario"></a>Noções básicas sobre o cenário de regras prontas para uso
Neste exemplo, estamos usando uma implantação com uma floresta de contas (A), uma floresta de recursos (R) e um diretório do Azure AD.

![Imagem com a descrição do cenário](./media/concept-azure-ad-connect-sync-default-configuration/scenario.png)

Nessa configuração, pressupomos que haja uma conta habilitada na floresta de contas e uma conta desabilitada na floresta de recursos com uma caixa de correio vinculada.

Nosso objetivo com a configuração padrão é:

* Atributos relacionados à entrada são sincronizados com a floresta com a conta habilitada.
* Atributos que podem ser encontrados na GAL (Lista de Endereços Global) são sincronizados da floresta com a caixa de correio. Se nenhuma caixa de correio for encontrada, nenhuma outra floresta será usada.
* Se uma caixa de correio vinculada for encontrada, a conta habilitada vinculada deverá encontrada para que o objeto seja exportado para o AD do Azure.

### <a name="synchronization-rule-editor"></a>Editor de Regra de Sincronização
O SRE (Editor de Regras de Sincronização) pode exibir e alterar a configuração, e um atalho para ele pode ser encontrado no menu Iniciar.

![Ícone do Editor de Regras de Sincronização](./media/concept-azure-ad-connect-sync-default-configuration/sre.png)

O SRE é uma ferramenta do kit de recursos e é instalado com a sincronização do Azure AD Connect. Para iniciá-lo, você deve ser um membro do grupo ADSyncAdmins. Quando ele é iniciado, você vê algo assim:

![Regras de Sincronização Entrada](./media/concept-azure-ad-connect-sync-default-configuration/syncrulesinbound.png)

Nesse painel, você vê todas as regras de sincronização criadas para sua configuração. Cada linha na tabela é uma regra de sincronização. À esquerda, em Tipos de Regra, estão listados os dois tipos diferentes: Entrada e Saída. Entrada e saída é da exibição do metaverso. Você se concentrará principalmente nas regras de entrada nesta visão geral. A lista atual de Regras de Sincronização depende do esquema detectado no AD. Na figura acima, a conta de florestas (fabrikamonline.com) não tem serviços, como o Exchange e o Lync, e nenhuma regra de sincronização foi criada para esses serviços. No entanto, na floresta de recursos (res.fabrikamonline.com), você encontra Regras de Sincronização para esses serviços. O conteúdo das regras é diferente dependendo da versão detectada. Por exemplo, em uma implantação com o Exchange 2013, há mais fluxos de atributo configurados do que no Exchange 2010/2007.

### <a name="synchronization-rule"></a>Regra de Sincronização
Uma regra de sincronização é um objeto de configuração com um conjunto de atributos que fluem quando uma condição é atendida. Ela também é usado para descrever como um objeto em um espaço de conector está relacionado a um objeto no metaverso, conhecido como **associação** ou **correspondência**. As Regras de Sincronização têm um valor de precedência indicando como elas se relacionam entre si. Uma regra de sincronização com um valor numérico mais baixo tem maior precedência e, em um conflito de fluxo de atributo, a precedência mais alta vence na resolução de conflitos.

Por exemplo, examinaremos a Regra de Sincronização **Entrada do AD – usuário AccountEnabled**. Marque essa linha no SRE e selecione **Editar**.

Como essa regra é pronta para uso, você receberá um aviso quando abrir a regra. Você não deve fazer qualquer [alteração em regras prontas para uso](how-to-connect-sync-best-practices-changing-default-configuration.md). Portanto, é indagado sobre suas intenções. Nesse caso, você só deseja exibir a regra. Selecione **Não**.

![Aviso de Regras de Sincronização](./media/concept-azure-ad-connect-sync-default-configuration/warningeditrule.png)

Uma Regra de Sincronização tem quatro seções de configuração: Descrição, Filtro de escopo, Regras de junção e Transformações.

#### <a name="description"></a>DESCRIÇÃO
A primeira seção fornece informações básicas, como nome e descrição.

![Guia Descrição no Editor de regras de sincronização](./media/concept-azure-ad-connect-sync-default-configuration/syncruledescription.png)

Você também encontra informações sobre a qual sistema conectado essa regra está relacionada, a qual objeto de tipo no sistema conectado ela se aplica e o tipo de objeto do metaverso. O tipo de objeto do metaverso é sempre pessoa, independentemente de o tipo de objeto de origem ser usuário, iNetOrgPerson ou contato. O tipo de objeto do metaverso nunca deve ser alterado. Portanto, ele é criado como um tipo genérico. O Tipo de Link pode ser definido como Associar, StickyJoin ou Provisionar. Essa configuração funciona em conjunto com a seção de regras de junção e é abordada mais tarde.

Você também pode ver que essa regra de sincronização é usada para a sincronização de senha. Se um usuário estiver no escopo dessa regra de sincronização, a senha será sincronizada do local para a nuvem (supondo que você tenha habilitado o recurso de sincronização de senha).

#### <a name="scoping-filter"></a>Filtro de escopo
A seção Filtro de Escopo é usada para configurar quando uma Regra de Sincronização deve ser aplicada. Como o nome da Regra de Sincronização que você está vendo indica que ela só deve ser aplicada para usuários habilitados, o escopo está configurado para que o atributo do AD **userAccountControl** não precise ter o bit 2 definido. Quando o mecanismo de sincronização encontra um usuário no AD, ele se aplica a essa regra de sincronização quando **userAccountControl** é definido como o valor decimal 512 (usuário normal habilitado). A regra não é aplicável quando o usuário tem **userAccountControl** definido como 514 (usuário normal desabilitado).

![Guia Escopo no Editor de regras de sincronização](./media/concept-azure-ad-connect-sync-default-configuration/syncrulescopingfilter.png)

O filtro de escopo tem Grupos e Cláusulas que podem ser aninhados. Todas as cláusulas dentro de um grupo devem ser satisfeitas para que uma Regra de Sincronização seja aplicada. Quando vários grupos são definidos, pelo menos um grupo deve ser satisfeito para que a regra seja aplicada. Ou seja, um OR lógico é avaliado entre grupos e um AND lógico é avaliado dentro de um grupo. Um exemplo dessa configuração pode ser encontrado na regra de sincronização de saída **Saída para AAD – Associação no grupo**. Há vários grupos de filtro de sincronização, por exemplo, um para grupos de segurança (`securityEnabled EQUAL True`) e outro para grupos de distribuição (`securityEnabled EQUAL False`).

![Guia Escopo no Editor de regras de sincronização](./media/concept-azure-ad-connect-sync-default-configuration/syncrulescopingfilterout.png)

Essa regra é usada para definir quais grupos devem ser provisionados ao Azure AD. Grupos de Distribuição devem ser habilitados para email para serem sincronizados com o Azure AD, mas, para os grupos de segurança, o email não é obrigatório.

#### <a name="join-rules"></a>Regras de associação
A terceira seção é usada para configurar como os objetos no espaço do conector se relacionam aos objetos no metaverso. A regra que você viu anteriormente não tem qualquer configuração para Regras de Associação. Então, você vai examinar **Entrada do AD – Associar Usuário**.

![Guia Regras de junção no Editor de regras de sincronização](./media/concept-azure-ad-connect-sync-default-configuration/syncrulejoinrules.png)

O conteúdo das regras de associação depende da opção correspondente selecionada no assistente de instalação. Para uma regra de entrada, a avaliação se inicia com um objeto no espaço do conector de origem, e cada grupo de regras de associação é avaliado em sequência. Se um objeto de origem for avaliado para corresponder exatamente a um objeto no metaverso usando uma das regras de associação, os objetos serão associados. Se todas as regras forem avaliadas e não houver correspondência, o Tipo de Link na página de descrição será usado. Se essa configuração for definida como **Provisionar**, um novo objeto será criado no destino, o metaverso. Provisionar um novo objeto para o metaverso também é conhecido como **projetar** um objeto para o metaverso.

As regras de associação são avaliadas apenas uma vez. Quando um objeto de espaço do conector e um objeto do metaverso são associados, eles permanecem associados enquanto o escopo da Regra de Sincronização ainda for satisfeito.

Ao serem avaliadas Regras de Sincronização, apenas uma Regra de Sincronização com as regras de associação definidas deve estar no escopo. Se forem encontradas várias Regras de Sincronização com regras de associação para um objeto, um erro será gerado. Por esse motivo, a prática recomendada é ter apenas uma Regra de Sincronização com associação definida quando várias Regras de Sincronização estão no escopo de um objeto. Na configuração pronta para uso para a sincronização do Azure AD Connect, essas regras podem ser encontradas examinando-se o nome para localizar aquelas com a palavra **Join** no fim do nome. Uma Regra de Sincronização sem regras de associação definidas aplicará os fluxos de atributo se outra Regra de Sincronização tiver associado os objetos ou provisionado um novo objeto no destino.

Se você examinar a figura acima, poderá ver que a regra está tentando associar **objectSID** a **msExchMasterAccountSid** (Exchange) e a **msRTCSIP OriginatorSid (Lync)** , que é o que esperamos em uma topologia de floresta de recurso de conta. Você encontrará a mesma regra em todas as florestas. Pressupõe-se que cada floresta possa ser uma conta ou um recurso de floresta. Essa configuração também funcionará se você tiver contas que residam em uma única floresta e não precisem ser unidas.

#### <a name="transformations"></a>Transformações
A seção de transformação define todos os fluxos de atributo que serão aplicados ao objeto de destino quando os objetos forem associados e o filtro de escopo for satisfeito. Voltando à Regra de Sincronização **Entrada do AD – usuário AccountEnabled** , você encontra as seguintes transformações:

![Guia Transformações no Editor de regras de sincronização](./media/concept-azure-ad-connect-sync-default-configuration/syncruletransformations.png)

Para colocar essa configuração em contexto, em uma implantação de floresta de Recurso de Conta, espera-se encontrar uma conta habilitada da floresta de contas e uma conta desabilitada na floresta de recursos com as configurações do Exchange e do Lync. A regra de sincronização que você está examinando contém os atributos necessários para entrada, e esses atributos devem fluir da floresta em que há uma conta habilitada. Todos esses fluxos de atributo são colocados juntos em uma Regra de Sincronização.

Uma transformação pode ter diferentes tipos: Constante, Direta e Expressão.

* Um fluxo constante sempre flui um valor codificado. Nesse caso, ele sempre define o valor **True** no atributo de metaverso chamado **accountEnabled**.
* Um fluxo direto sempre flui o valor do atributo na fonte para o atributo de destino como está.
* O terceiro tipo de fluxo é Expressão e permite configurações mais avançadas.

A linguagem de expressão é VBA (Visual Basic for Applications). Portanto, pessoas com experiência no Microsoft Office ou VBScript reconhecerão o formato. Os atributos são colocados entre colchetes, [attributeName]. Os nomes de função e de atributo diferenciam maiúsculas de minúsculas, mas o Editor de Regras de Sincronização avaliará as expressões e fornecerá um aviso se a expressão não for válida. Todas as expressões são expressas em uma única linha com funções aninhadas. Para mostrar a capacidade da linguagem de configuração, aqui está o fluxo para pwdLastSet, mas com comentários adicionais inseridos:

```
// If-then-else
IIF(
// (The evaluation for IIF) Is the attribute pwdLastSet present in AD?
IsPresent([pwdLastSet]),
// (The True part of IIF) If it is, then from right to left, convert the AD time format to a .NET datetime, change it to the time format used by Azure AD, and finally convert it to a string.
CStr(FormatDateTime(DateFromNum([pwdLastSet]),"yyyyMMddHHmmss.0Z")),
// (The False part of IIF) Nothing to contribute
NULL
)
```

Veja [Noções básicas sobre expressões de provisionamento declarativo](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md) para obter mais informações sobre a linguagem de expressão para fluxos de atributo.

### <a name="precedence"></a>Precedência
Agora você analisou algumas Regras de Sincronização individuais, mas as regras trabalham juntas na configuração. Em alguns casos, um valor de atributo vêm de várias regras de sincronização para o mesmo atributo de destino. Nesse caso, precedência do atributo é usada para determinar qual atributo vence. Como exemplo, examine o atributo sourceAnchor. É um atributo importante para se conseguir entrar no AD do Azure. Você pode encontrar um fluxo de atributo para esse atributo em duas diferentes regras de sincronização, **Entrada do AD – usuário AccountEnabled** e **Entrada do AD – usuário comum**. Devido à precedência de Regra de Sincronização, o atributo sourceAnchor terá a contribuição da floresta com uma conta habilitada primeiro se houver vários objetos associados ao objeto metaverso. Se nenhuma conta estiver habilitada, o mecanismo de sincronização usará a Regra de Sincronização para capturar tudo **Entrada do AD – Usuário Comum**. Essa configuração garante que, até mesmo para contas desabilitadas, ainda haja um sourceAnchor.

![Regras de Sincronização Entrada](./media/concept-azure-ad-connect-sync-default-configuration/syncrulesinbound.png)

A precedência de Regras de Sincronização é definida em grupos pelo assistente de instalação. Todas as regras em um grupo têm o mesmo nome, mas elas são conectadas a diferentes diretórios conectados. O assistente de instalação dá à regra **Entrada do AD – Associação de Usuário** maior precedência e itera por todos os diretórios do AD conectados. Em seguida, ele continua com os próximos grupos de regras em uma ordem predefinida. Dentro de um grupo, as regras são adicionadas na ordem em que os conectores foram adicionados ao assistente. Se outro conector for adicionado usando o assistente, as Regras de Sincronização serão reordenadas e as novas regras do conector serão inseridas por último em cada grupo.

### <a name="putting-it-all-together"></a>Juntando as peças
Agora sabemos o suficiente sobre Regras de Sincronização para poder entender como a configuração funciona com diferentes Regras de Sincronização. Se você observar um usuário e os atributos que contribuíram para o metaverso, as regras são aplicadas na seguinte ordem:

| Nome | Comentário |
|:--- |:--- |
| Entrada do AD – Associação de Usuário |Regra para associar objetos de espaço conector com metaverso. |
| Entrada do AD – UserAccount habilitada |Os atributos necessários para entrar no Azure AD e no Office 365. Queremos esses atributos da conta habilitada. |
| Entrada do AD – usuário comum do Exchange |Atributos encontrados na Lista de Endereços Global. Vamos supor que a qualidade dos dados é melhor na floresta em que achamos a caixa de correio do usuário. |
| Entrada do AD – usuário comum |Atributos encontrados na Lista de Endereços Global. No caso de não encontramos uma caixa de correio, outros objetos associados podem contribuir com o valor do atributo. |
| Entrada do AD – usuário do Exchange |Existe somente se o Exchange foi detectado. Flui todos os atributos do Exchange de infraestrutura. |
| Entrada do AD – usuário Lync |Existe somente se o Lync foi detectado. Flui todos os atributos do Lync de infraestrutura. |

## <a name="next-steps"></a>Próximas etapas
* Leia mais sobre o modelo de configuração em [Noções básicas do provisionamento declarativo](concept-azure-ad-connect-sync-declarative-provisioning.md).
* Leia mais sobre a linguagem de expressão em [Noções básicas sobre expressões de provisionamento declarativo](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md).
* Continue lendo sobre como a configuração pronta para uso funciona em [Noções básicas sobre usuários e contatos](concept-azure-ad-connect-sync-user-and-contacts.md)
* Veja como fazer uma alteração prática usando o provisionamento declarativo em [Como fazer uma alteração na configuração padrão](how-to-connect-sync-change-the-configuration.md).

**Tópicos de visão geral**

* [Sincronização do Azure AD Connect: Compreender e personalizar a sincronização](how-to-connect-sync-whatis.md)
* [Integração de suas identidades locais com o Active Directory do Azure](whatis-hybrid-identity.md)

