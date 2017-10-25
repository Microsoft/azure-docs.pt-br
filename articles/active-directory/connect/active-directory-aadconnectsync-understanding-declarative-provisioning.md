---
title: "Azure AD Connect: Noções básicas sobre provisionamento declarativo | Microsoft Docs"
description: "Explica o modelo de configuração de provisionamento declarativo no Azure AD Connect."
services: active-directory
documentationcenter: 
author: andkjell
manager: femila
editor: 
ms.assetid: cfbb870d-be7d-47b3-ba01-9e78121f0067
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: billmath
ms.openlocfilehash: 7497ec2ca658c3790227c56ef1755d9a1cb74e0a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-ad-connect-sync-understanding-declarative-provisioning"></a>Sincronização do Azure AD Connect: noções básicas sobre expressões de provisionamento declarativo
Este tópico explica o modelo de configuração no Azure AD Connect. O modelo é chamado de Provisionamento Declarativo e permite que você altere uma configuração com facilidade. Muitos itens descritos neste tópico são avançados e não são necessários para a maioria dos cenários do cliente.

## <a name="overview"></a>Visão geral
O provisionamento declarativo está processando objetos provenientes um diretório de origem conectado e determina como o objeto e os atributos devem ser transformados de uma origem para um destino. Um objeto é processado em um pipeline de sincronização, e o pipeline é o mesmo para regras de entrada e saída. Uma regra de entrada é de um espaço de conector para o metaverso, e uma regra de saída é do metaverso para um espaço do conector.

![Pipeline de sincronização](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/sync1.png)  

O pipeline tem vários módulos diferentes. Cada um é responsável por um conceito na sincronização de objeto.

![Pipeline de sincronização](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/pipeline.png)  

* Origem, objeto de origem
* [Escopo](#scope)localiza todas as regras de sincronização que estão no escopo
* [Ingressar](#join)determina a relação entre o metaverso e o espaço do conector
* [Transformar](#transform)calcula como os atributos devem ser transformados e fluir
* [Precedência](#precedence)resolve conflitos de contribuições de atributo
* Destino, objeto de destino

## <a name="scope"></a>Escopo
O módulo de escopo está avaliando um objeto e determina as regras que estão no escopo e devem ser incluídas no processamento. Dependendo dos valores de atributos no objeto, regras de sincronização diferentes são avaliadas como estando no escopo. Por exemplo, um usuário desabilitado sem uma caixa de correio do Exchange tem regras diferentes das de um usuário habilitado com uma caixa de correio.  
![Escopo](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/scope1.png)  

O escopo é definido como grupos e cláusulas. As cláusulas estão dentro de um grupo. Um E lógico é usado entre todas as cláusulas em um grupo. Por exemplo, (department =TI E country = Dinamarca). Um OU lógico é usado entre grupos.

![Escopo](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/scope2.png)  
O escopo nesta imagem deve ser lido como (department = TI e country = Dinamarca) ou (country = Suécia). Se o grupo 1 ou 2 for avaliado como true, a regra está no escopo.

O módulo de escopo dá suporte às operações a seguir.

| Operação | Descrição |
| --- | --- |
| EQUAL, NOTEQUAL |Uma comparação de cadeia de caracteres que avalia se o valor é igual ao valor do atributo. Para atributos com valores múltiplos, confira ISIN e ISNOTIN. |
| LESSTHAN, LESSTHAN_OR_EQUAL |Uma comparação de cadeia de caracteres que avalia se o valor é menor do que o valor do atributo. |
| CONTAINS, NOTCONTAINS |Comparação de cadeia de caracteres que avalia se o valor pode ser encontrado em algum lugar no valor do atributo. |
| STARTSWITH, NOTSTARTSWITH |Comparação de cadeia de caracteres que avalia se o valor está no início do valor do atributo. |
| ENDSWITH, NOTENDSWITH |Comparação de cadeia de caracteres que avalia se o valor está no fim do valor do atributo. |
| GREATERTHAN, GREATERTHAN_OR_EQUAL |Comparação de cadeia de caracteres que avalia se o valor é maior do que o valor do atributo. |
| ISNULL, ISNOTNULL |Avalia se o atributo está ausente do objeto. Se o atributo não estiver presente e, portanto, for nulo, a regra está no escopo. |
| ISIN, ISNOTIN |Avalia se o valor está presente no atributo definido. Essa operação é a variação de valores múltiplos de EQUAL e NOTEQUAL. O atributo deve para ser um atributo com valores múltiplos e, se o valor puder ser encontrado em qualquer um dos valores de atributo, a regra está no escopo. |
| ISBITSET, ISNOTBITSET |Avalia se um bit específico está definido. Por exemplo, pode ser usado para avaliar os bits em userAccountControl para ver se um usuário está habilitado ou desabilitado. |
| ISMEMBEROF, ISNOTMEMBEROF |O valor deve conter um DN para um grupo no espaço do conector. Se o objeto for membro do grupo especificado, a regra está no escopo. |

## <a name="join"></a>Ingressar
O módulo de junção no pipeline de sincronização é responsável por localizar a relação entre o objeto de origem e um objeto de destino. Em uma regra de entrada, essa relação seria um objeto em um espaço de conector que localiza uma relação com um objeto no metaverso.  
![Junção entre cs e mv](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/join1.png)  
O objetivo é ver se já existe um objeto no metaverso, criado por outro Conector ao qual ele deve ser associado. Por exemplo, em uma floresta de recursos de conta, o usuário da floresta de contas deve ser unido ao usuário da floresta de recursos.

As junções são usadas principalmente em regras de entrada para unir os objetos de espaço do conector ao mesmo objeto do metaverso.

As junções são definidas como um ou mais grupos. Dentro de um grupo, há cláusulas. Um E lógico é usado entre todas as cláusulas em um grupo. Um OU lógico é usado entre grupos. Os grupos são processados na ordem de cima para baixo. Quando um grupo encontra uma correspondência exata com um objeto de destino, nenhuma outra regra de associação é avaliada. Se zero ou mais de um objeto for localizado, o processamento continuará para o próximo grupo de regras. Por esse motivo, as regras devem ser criadas na ordem com a mais explícita primeiro e a mais difusa no fim.  
![Definição de junção](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/join2.png)  
As junções nesta figura são processadas de cima para baixo. Primeiro, o pipeline de sincronização vê se há uma correspondência em employeeID. Caso contrário, a segunda regra vê se o nome da conta pode ser usado para unir os objetos. Se essa não for uma correspondência, a terceira e última regra será uma correspondência mais difusa usando o nome de usuário.

Se todas as regras de junção forem avaliadas e não houver exatamente uma correspondência, o **Tipo de Link** na página **Descrição** será usado. Se essa opção for definida como **Provisionar**, será criado um novo objeto de destino.  
![Provisionar ou ingressar](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/join3.png)  

Um objeto deve ter apenas uma única regra de sincronização com as regras de associação em escopo. Se houver várias regras de sincronização em que a associação esteja definida, ocorrerá um erro. A precedência não é usada para resolver conflitos de junção. Um objeto deve ter uma regra de junção no escopo para que os atributos fluam com a mesma direção de entrada/saída. Se for preciso que os atributos fluam como entrada e saída para o mesmo objeto, você deverá ter uma entrada e uma regra de sincronização de saída com a junção.

A junção de saída tem um comportamento especial quando tenta fornecer um objeto para um espaço de conector de destino. O atributo DN é usado para tentar primeiro uma junção inversa. Se já houver um objeto no espaço do conector de destino com o mesmo DN, os objetos serão unidos.

O módulo de junção é avaliado apenas uma vez quando uma nova regra de sincronização está no escopo. Quando um objeto é unido, ele não é desunido mesmo que os critérios de junção não sejam atendidos. Se você quiser desunir um objeto, a regra de sincronização que uniu os objetos deverá sair do escopo.

### <a name="metaverse-delete"></a>Exclusão de metaverso
Um objeto do metaverso permanecerá, desde que haja uma regra de sincronização no escopo com **Tipo de Link** definido como **Provisionar** ou **StickyJoin**. Um StickyJoin é usado quando um conector não tem permissão para provisionar um novo objeto para o metaverso, mas quando é unido, deve ser excluído na origem antes que o objeto do metaverso seja excluído.

Quando um objeto do metaverso é excluído, todos os objetos associados a uma regra de sincronização de saída marcada para **provisionar** são marcados para uma exclusão.

## <a name="transformations"></a>Transformações
As transformações são usadas para definir como os atributos devem fluir da origem para o destino. Os fluxos podem ter um dos seguintes **tipos de fluxo**: Direto, Constante ou Expressão. Um fluxo direto; flui um valor de atributo como está, sem transformações adicionais. Um valor constante define o valor especificado. Uma expressão usa a linguagem de expressão de provisionamento declarativo para expressar como a transformação deve ser. Os detalhes para a linguagem de expressão podem ser encontrados no tópico [Noções básicas sobre a linguagem de expressão de provisionamento declarativo](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md) .

![Provisionar ou ingressar](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/transformations1.png)  

A caixa de seleção **Aplicar uma vez** define que o atributo deve ser definido somente quando o objeto é criado inicialmente. Por exemplo, essa configuração pode ser usada para definir uma senha inicial para um novo objeto de usuário.

### <a name="merging-attribute-values"></a>Mesclando valores de atributo
Nos fluxos de atributo, há uma configuração para determinar se os atributos com vários valores devem ser mesclados de vários conectores diferentes. O valor padrão é **Update**, que indica que a regra de sincronização com precedência mais alta deve prevalecer.

![Tipos de mesclagem](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/mergetype.png)  

Também há **Merge** e **MergeCaseInsensitive**. Essas opções permitem mesclar valores de diferentes origens. Por exemplo, esses valores podem ser usados para mesclar o membro ou o atributo proxyAddresses de várias florestas diferentes. Quando você usa essa opção, todas as regras de sincronização no escopo de um objeto devem usar o mesmo tipo de mesclagem. Não é possível definir **Update** de um conector e **Merge** de outro. Se tentar, você receberá um erro.

A diferença entre **Merge** e **MergeCaseInsensitive** é como processar valores de atributo duplicados. O mecanismo de sincronização garante que os valores duplicados não sejam inseridos no atributo de destino. Com **MergeCaseInsensitive**, os valores duplicados com uma diferença apenas no caso não estarão presentes. Por exemplo, você não verá “SMTP:bob@contoso.com” e “smtp:bob@contoso.com” no atributo de destino. **Merge** está examinando apenas os valores exatos e múltiplos valores onde há apenas uma diferença no caso pode estar presente.

A opção **Replace** é igual a **Update**, mas não é usada.

### <a name="control-the-attribute-flow-process"></a>Controlar o processo de fluxo de atributos
Quando várias regras de sincronização de entrada são configuradas para contribuir com o mesmo atributo de metaverso, a precedência é usada para determinar o vencedor. A regra de sincronização com precedência mais alta (valor numérico menor) contribuirá com o valor. O mesmo ocorre com as regras de saída. A regra de sincronização com a precedência mais alta vence e contribui com o valor para o diretório conectado.

Em alguns casos, em vez de contribuir com um valor, a regra de sincronização deve determinar como as outras regras devem comportar-se. Há alguns literais especiais usados para esse caso.

Para as Regras de Sincronização de entrada, o literal **NULL** pode ser usado para indicar que o fluxo não tem valores para contribuir. Outra regra com menor precedência pode contribuir com um valor. Se nenhuma regra contribuiu com um valor, o atributo metaverso é removido. Para uma regra de saída, se **NULL** for o valor final depois do processamento de todas as regras de sincronização, o valor será removido no diretório conectado.

O literal **AuthoritativeNull** é semelhante a **NULL**, mas com a diferença de que nenhuma regra de precedência mais baixa pode contribuir com um valor.

Um fluxo de atributos também pode usar **IgnoreThisFlow**. É semelhante a NULL no sentido de que indica que não há nada para contribuir. A diferença é que ele não remove um valor já existente no destino. É como se o fluxo de atributos nunca tivesse existido lá.

Veja um exemplo:

Em *saída para AD – usuário Exchange híbrido* fluxo a seguir pode ser encontrado:  
`IIF([cloudSOAExchMailbox] = True,[cloudMSExchSafeSendersHash],IgnoreThisFlow)`  
esta expressão deve ser lida como: se a caixa de correio do usuário estiver localizada no Azure AD, então, flua o atributo do Azure AD para o AD. Caso contrário, não flua nada de volta para o Active Directory. Neste caso, ele manteria o valor existente no AD.

### <a name="importedvalue"></a>ImportedValue
A função ImportedValue é diferente de todas as outras funções, pois o nome do atributo deve ser colocado entre aspas, em vez de colchetes:   
`ImportedValue("proxyAddresses")`.

Geralmente, durante a sincronização, um atributo usa o valor esperado, mesmo que ele ainda não tenha sido exportado ou que um erro tenha sido recebido durante a exportação ("topo da torre"). Uma sincronização de entrada presumirá que um atributo que ainda não atingiu um diretório conectado finalmente o atingirá. Em alguns casos, é importante sincronizar apenas um valor que foi confirmado pelo diretório conectado ("holograma e torre de importação delta").

Um exemplo dessa função pode ser encontrado na Regra de Sincronização pronta para uso *Entrada do AD – Usuário Comum do Exchange*. No Exchange Híbrido, o valor adicionado pelo Exchange online só deve ser sincronizado quando confirmado que o valor foi exportado com êxito:   
`proxyAddresses` <- `RemoveDuplicates(Trim(ImportedValue("proxyAddresses")))`

## <a name="precedence"></a>Precedência
Quando várias regras de sincronização tentam contribuir com o mesmo valor de atributo para o destino, o valor de precedência é usado para determinar o vencedor. A regra com prioridade mais alta e o menor valor numérico vai contribuir com o atributo em um conflito.

![Tipos de mesclagem](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/precedence1.png)  

Essa classificação pode ser usada para definir fluxos de atributo mais precisos para um pequeno subconjunto de objetos. Por exemplo, as regras prontas para uso garantem que os atributos de uma conta habilitada (**Useraccountenabled**) tenham precedência sobre outras contas.

A precedência pode ser definida entre Conectores. Isso permite que os Conectores com dados melhores contribuam com valores primeiro.

### <a name="multiple-objects-from-the-same-connector-space"></a>Vários objetos do mesmo espaço do conector
Se você tiver vários objetos no mesmo espaço do conector associados ao mesmo objeto de metaverso, a precedência deverá ser ajustada. Se vários objetos estiverem no escopo da mesma regra de sincronização, o mecanismo de sincronização não poderá determinar a precedência. É ambíguo qual objeto de origem deve contribuir com o valor para o metaverso. Essa configuração é relatada como ambígua, mesmo se os atributos na origem tiverem o mesmo valor.  
![Vários objetos unidos ao mesmo objeto mv](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/multiple1.png)  

Para esse cenário, você precisa alterar o escopo das regras de sincronização para que os objetos de origem tenham regras de sincronização diferentes no escopo. Isso permite que você defina uma precedência diferente.  
![Vários objetos unidos ao mesmo objeto mv](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/multiple2.png)  

## <a name="next-steps"></a>Próximas etapas
* Leia mais sobre a linguagem de expressão em [Noções básicas sobre expressões de provisionamento declarativo](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md).
* Veja como o provisionamento declarativo está pronto para uso em [Noções básicas sobre a configuração padrão](active-directory-aadconnectsync-understanding-default-configuration.md).
* Veja como fazer uma alteração prática usando o provisionamento declarativo em [Como fazer uma alteração na configuração padrão](active-directory-aadconnectsync-change-the-configuration.md).
* Continue a ler sobre como usuários e contatos funcionam juntos em [Noções básicas sobre usuários e contatos](active-directory-aadconnectsync-understanding-users-and-contacts.md).

**Tópicos de visão geral**

* [Sincronização do Azure AD Connect: compreender e personalizar a sincronização](active-directory-aadconnectsync-whatis.md)
* [Integração de suas identidades locais com o Active Directory do Azure](active-directory-aadconnect.md)

**Tópicos de referência**

* [Azure AD Connect Sync: referência de funções](active-directory-aadconnectsync-functions-reference.md)
