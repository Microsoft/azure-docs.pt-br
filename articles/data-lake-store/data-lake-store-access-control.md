---
title: "Visão geral do controle de acesso no Data Lake Store | Microsoft Docs"
description: Compreenda como o controle de acesso funciona no Azure Data Lake Store
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: d16f8c09-c954-40d3-afab-c86ffa8c353d
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/03/2017
ms.author: nitinme
ms.openlocfilehash: 43582ae805d560e72fb8d03ab8e0abee4c4325b7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="access-control-in-azure-data-lake-store"></a>Controle de acesso no Azure Data Lake Store

O Azure Data Lake Store implementa um modelo de controle de acesso que deriva do HDFS que, por sua vez, deriva do modelo de controle de acesso do POSIX. Este artigo resume as noções básicas do modelo de controle de acesso para o Data Lake Store. Para saber mais sobre o modelo de controle de acesso do HDFS, veja [Guia de permissões do HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html).

## <a name="access-control-lists-on-files-and-folders"></a>Listas de controle de acesso em arquivos e pastas

Há dois tipos de listas de controle de acesso (ACLs), **ACLs de Acesso** e **ACLs Padrão**.

* **ACLs de Acesso**: controlam o acesso a um objeto. Os arquivos e as pastas têm ACLs de Acesso.

* **ACLs Padrão**: um "modelo" de ACLs associadas a uma pasta que determinam as ACLs de Acesso para todos os itens filhos criados nessa pasta. Os Arquivos não têm ACLs Padrão.

![ACLs do Data Lake Store](./media/data-lake-store-access-control/data-lake-store-acls-1.png)

As ACLs de Acesso e as ACLs Padrão têm a mesma estrutura.

![ACLs do Data Lake Store](./media/data-lake-store-access-control/data-lake-store-acls-2.png)



> [!NOTE]
> Alterar a ACL Padrão em um pai não afeta o a ACL de Acesso ou a ACL Padrão de itens filhos já existentes.
>
>

## <a name="users-and-identities"></a>Usuários e identidades

Todos os arquivos e pastas têm permissões diferentes para estas identidades:

* O usuário proprietário do arquivo
* O grupo proprietário
* Usuários nomeados
* Grupos nomeados
* Todos os outros usuários

As identidades dos usuários e grupos são identidades do Azure AD (Azure Active Directory). Portanto, a menos que indicado em contrário, um "usuário," no contexto do Data Lake Store, pode significar um usuário do Azure AD ou um grupo de segurança do Azure AD.

## <a name="permissions"></a>Permissões

As permissões em um objeto do sistema de arquivos são **Ler**, **Gravar** e **Executar** e podem ser usadas em arquivos e em pastas como mostrado na seguinte tabela:

|            |    Arquivo     |   Pasta |
|------------|-------------|----------|
| **Ler (R)** | Pode ler o conteúdo de um arquivo | Requer **Ler** e **Executar** para listar o conteúdo da pasta|
| **Gravar (W)** | Pode gravar ou anexar a um arquivo | Requer **Gravar** e **Executar** para criar itens filhos em uma pasta |
| **Executar (X)** | Não significa nada no contexto do Data Lake Store | Necessário para percorrer os itens filhos de uma pasta |

### <a name="short-forms-for-permissions"></a>Formatos abreviados para permissões

**RWX**é usado para indicar **Ler + Gravar + Executar**. Existe um formato numérico mais condensado na qual **Ler = 4**, **Gravar = 2** e **Executar = 1** e sua soma representa as permissões. Estes são alguns exemplos:

| Formato numérico | Formato curto |      O que significa     |
|--------------|------------|------------------------|
| 7            | RWX        | Ler + Gravar + Executar |
| 5            | R-X        | Ler + Executar         |
| 4            | R--        | Ler                   |
| 0            | ---        | Nenhuma permissão         |


### <a name="permissions-do-not-inherit"></a>Não herdam permissões

No modelo de estilo POSIX usado pelo Data Lake Store, as permissões para um item são armazenadas no próprio item. Em outras palavras, as permissões para um item não podem ser herdadas dos itens pais.

## <a name="common-scenarios-related-to-permissions"></a>Cenários comuns relacionados a permissões

A seguir estão alguns cenários comuns para ajudá-lo a compreender quais permissões são necessárias para executar determinadas operações em uma conta do Data Lake Store.

### <a name="permissions-needed-to-read-a-file"></a>Permissões necessárias para ler um arquivo

![ACLs do Data Lake Store](./media/data-lake-store-access-control/data-lake-store-acls-3.png)

* Para o arquivo a ser lido, o chamador precisa de permissões **Ler**.
* Para todas as pastas na estrutura de pastas que contêm o arquivo, o chamador precisa de permissões **Executar**.

### <a name="permissions-needed-to-append-to-a-file"></a>Permissões necessárias para anexar a um arquivo

![ACLs do Data Lake Store](./media/data-lake-store-access-control/data-lake-store-acls-4.png)

* Para o arquivo ao qual será anexado, o chamador precisa de permissões **Gravar**.
* Para todas as pastas que contêm o arquivo, o chamador precisa de permissões **Executar**.

### <a name="permissions-needed-to-delete-a-file"></a>Permissões necessárias para excluir um arquivo

![ACLs do Data Lake Store](./media/data-lake-store-access-control/data-lake-store-acls-5.png)

* Para a pasta pai, o chamador precisa de permissões **Gravar + Executar**.
* Para todas as outras pastas no caminho do arquivo, o chamador precisa de permissões **Executar**.



> [!NOTE]
> Permissões de gravação não são necessárias no arquivo para excluí-lo, desde que as duas condições anteriores sejam verdadeiras.
>
>

### <a name="permissions-needed-to-enumerate-a-folder"></a>Permissões necessárias para enumerar uma pasta

![ACLs do Data Lake Store](./media/data-lake-store-access-control/data-lake-store-acls-6.png)

* Para a pasta a ser enumerada, o chamador precisa de permissões **Ler + Executar**.
* Para todas as pastas do ancestral, o chamador precisa de permissões **Executar**.

## <a name="viewing-permissions-in-the-azure-portal"></a>Exibição de permissões no portal do Azure

Na folha **Data Explorer** da conta do Data Lake Store, clique em **Acesso** para ver as ACLs de um arquivo ou pasta. Clique em **Acesso** para ver as ACLs para a pasta **catálogo** sob a conta **mydatastore**.

![ACLs do Data Lake Store](./media/data-lake-store-access-control/data-lake-store-show-acls-1.png)

Nessa folha, a seção superior mostra uma visão geral das permissões que você tem. (Na captura de tela, o usuário é Paulo.) A seguir, as permissões de acesso são mostradas. Depois disso, da folha **Acesso**, clique em **Exibição Simples** para ver a exibição mais simples.

![ACLs do Data Lake Store](./media/data-lake-store-access-control/data-lake-store-show-acls-simple-view.png)

Clique em **Exibição Avançada** para ver a exibição mais avançada em que os conceitos de ACLs Padrão, máscara e superusuário são mostrados.

![ACLs do Data Lake Store](./media/data-lake-store-access-control/data-lake-store-show-acls-advance-view.png)

## <a name="the-super-user"></a>O superusuário

Um superusuário é aquele que tem mais direitos no Data Lake Store. Um superusuário:

* Tem Permissões RWX para **todos** os arquivos e pastas.
* Pode alterar as permissões em qualquer arquivo ou pasta.
* Pode alterar o usuário proprietário ou o grupo proprietário de qualquer arquivo ou pasta.

No Azure, uma conta do Data Lake Store tem diversas funções do Azure, incluindo:

* Proprietários
* Colaboradores
* Leitores

Todos na função **Proprietários** para uma conta do Data Lake Store se tornam automaticamente superusuários para essa conta. Para saber mais, confira [Controle de acesso baseado em função](../active-directory/role-based-access-control-configure.md).
Se você quiser criar uma função personalizada de RBAC (controle de acesso baseado em função) com permissões de superusuário, ela precisará ter as seguintes permissões:
- Microsoft.DataLakeStore/accounts/Superuser/action
- Microsoft.Authorization/roleAssignments/write


## <a name="the-owning-user"></a>O usuário proprietário

O usuário que criou o item é automaticamente o usuário proprietário do item. Um usuário proprietário pode:

* Altere as permissões de um arquivo pertencente.
* Alterar o grupo proprietário de um arquivo pertencente, contanto que o usuário proprietário também seja membro do grupo de destino.

> [!NOTE]
> O usuário proprietário *não pode* alterar o usuário proprietário de outro arquivo pertencente. Somente superusuários podem alterar o usuário proprietário de um arquivo ou pasta.
>
>

## <a name="the-owning-group"></a>O grupo proprietário

Nas ACLs do POSIX, cada usuário está associado a um "grupo primário". Por exemplo, o usuário "alice" pode pertencer ao grupo "finanças". Alice pode pertencer também a vários grupos, mas um grupo será sempre designado como o grupo primário dela. No POSIX, quando Alice cria um arquivo, o grupo proprietário desse arquivo é definido como o grupo primário que, nesse caso, é "finanças".

Quando um novo item do sistema de arquivos é criado, o Data Lake Store atribui um valor para o grupo proprietário.

* **Caso 1**: a pasta raiz "/". Essa pasta é criada quando uma conta do Data Lake Store é criada. Nesse caso, o grupo proprietário é definido para o usuário que criou a conta.
* **Caso 2** (todos os outros casos): quando um novo item é criado, o grupo proprietário é copiado da pasta pai.

O grupo proprietário pode ser alterado por:
* Todos os superusuários.
* O usuário proprietário, se o usuário proprietário também for membro do grupo de destino.

## <a name="access-check-algorithm"></a>Algoritmo de verificação de acesso

A ilustração a seguir representa o algoritmo de verificação de acesso para contas do Data Lake Store.

![Algoritmo de ACLs do Data Lake Store](./media/data-lake-store-access-control/data-lake-store-acls-algorithm.png)


## <a name="the-mask-and-effective-permissions"></a>A máscara e as "permissões efetivas"

A **máscara** é um valor RWX usado para limitar o acesso para **usuários nomeados**, o **grupo proprietário** e os **grupos nomeados** durante a execução do algoritmo de verificação de acesso. Aqui estão os principais conceitos de mask.

* A máscara cria "permissões efetivas." Ou seja, ele modifica as permissões no momento da verificação de acesso.
* Mask pode ser editada diretamente pelo proprietário do arquivo e por todos os superusuários.
* A máscara pode remover permissões para criar a permissão efetiva. A mask *não pode* adicionar permissões à permissão efetiva.

Vejamos alguns exemplos. No exemplo a seguir, a máscara é definida como **RWX**, o que significa que a máscara não remove permissões. As permissões efetivas para o usuário nomeado, o grupo proprietário e o grupo nomeado não são alteradas durante a verificação de acesso.

![ACLs do Data Lake Store](./media/data-lake-store-access-control/data-lake-store-acls-mask-1.png)

No exemplo a seguir, a máscara é definida como **R-X**. Isso significa que ela **desativa as permissões Gravar** para o **usuário nomeado**, o **grupo proprietário** e o **grupo nomeado** no momento da verificação de acesso.

![ACLs do Data Lake Store](./media/data-lake-store-access-control/data-lake-store-acls-mask-2.png)

Para referência, é aqui onde mask para um arquivo ou pasta aparece no portal do Azure.

![ACLs do Data Lake Store](./media/data-lake-store-access-control/data-lake-store-show-acls-mask-view.png)

> [!NOTE]
> Para uma nova conta do Data Lake Store, a mask da ACL de Acesso e a ACL Padrão da pasta raiz ("/") têm RWX como padrão.
>
>

## <a name="permissions-on-new-files-and-folders"></a>Permissões em novos arquivos e pastas

Quando um novo arquivo ou pasta é criado em uma pasta existente, a ACL Padrão na pasta pai determina:

- ACL Padrão e ACL de Acesso de uma pasta filho.
- ACL de Acesso de um arquivo filho (os arquivos não têm uma ACL Padrão).

### <a name="the-access-acl-of-a-child-file-or-folder"></a>A ACL de acesso de uma pasta ou um arquivo filho

Quando uma pasta ou um arquivo filho é criada, a ACL Padrão do pai é copiada como a ACL de Acesso do arquivo ou da pasta filho. Além disso, se **outro** usuário tiver permissões RWX na ACL Padrão do pai, elas serão removidas da ACL de Acesso do item filho.

![ACLs do Data Lake Store](./media/data-lake-store-access-control/data-lake-store-acls-child-items-1.png)

Na maioria dos cenários, as informações anteriores são tudo o que você precisa saber sobre como a ACL de Acesso de um item filho é determinada. No entanto, se você estiver familiarizado com sistemas POSIX e quiser entender com detalhes como essa transformação é obtida, veja a seção [A função umask na criação da ACL de Acesso para arquivos e pastas novos](#umasks-role-in-creating-the-access-acl-for-new-files-and-folders) posteriormente neste artigo.


### <a name="a-child-folders-default-acl"></a>ACL Padrão de uma pasta filho

Quando uma pasta filho é criada em uma pasta pai, a ACL Padrão da pasta pai é copiado como está, para a ACL Padrão da pasta filho.

![ACLs do Data Lake Store](./media/data-lake-store-access-control/data-lake-store-acls-child-items-2.png)

## <a name="advanced-topics-for-understanding-acls-in-data-lake-store"></a>Tópicos avançados para compreender as ACLs no Data Lake Store

A seguir, alguns tópicos avançados para ajudar você a entender como as ACLs são determinadas para arquivos ou pastas do Data Lake Store.

### <a name="umasks-role-in-creating-the-access-acl-for-new-files-and-folders"></a>A função umask na criação da ACL de Acesso para arquivos e pastas novos

Em um sistema compatível com POSIX, o conceito geral é que umask é um valor de 9 bits na pasta pai usado para transformar a permissão para **usuário proprietário**, **grupo proprietário** e **outros** na ACL de Acesso de um novo arquivo ou pasta filho. Os bits de uma umask identificam quais bits devem ser desativados na ACL de Acesso do item filho. Dessa forma, é usado para impedir a propagação de permissões para o **usuário proprietário**, o **grupo proprietário** e **outros**.

Em um sistema HDFS, umask normalmente é uma opção de configuração de todo o site que é controlada pelos administradores. O Data Lake Store usa uma **umask de toda a conta** que não pode ser alterada. A tabela a seguir mostra o unmask para o Data Lake Store.

| Grupo de usuários  | Configuração | Efeito na ACL de Acesso de um novo item filho |
|------------ |---------|---------------------------------------|
| usuário proprietário | ---     | Sem efeito                             |
| grupo proprietário| ---     | Sem efeito                             |
| outro       | RWX     | Remover Ler + Gravar + Executar         |

A ilustração a seguir mostra esta umask em ação. O efeito líquido é remover **Ler + Gravar + Executar** para **outro** usuário. Como a umask não especificou bits para o **usuário proprietário** e o **grupo proprietário**, essas permissões não são transformadas.

![ACLs do Data Lake Store](./media/data-lake-store-access-control/data-lake-store-acls-umask.png)

### <a name="the-sticky-bit"></a>O sticky bit

O sticky bit é um recurso mais avançado de um sistema de arquivos POSIX. No contexto do Data Lake Store, é improvável que o sticky bit seja necessário.

A tabela a seguir mostra como o bit adesivo funciona no Data Lake Store.

| Grupo de usuários         | Arquivo    | Pasta |
|--------------------|---------|-------------------------|
| Sticky bit **DESATIVADO** | Sem efeito   | Sem efeito.           |
| Sticky bit **ATIVADO**  | Sem efeito   | Impede que alguém, exceto os **superusuários** e o **usuário proprietário** de um item filho, exclua ou renomeie o item filho.               |

O sticky bit não é mostrado no portal do Azure.

## <a name="common-questions-about-acls-in-data-lake-store"></a>Perguntas comuns sobre ACLs no Data Lake Store

Aqui estão algumas perguntas que surgem com frequência sobre ACLs no Data Lake Store.

### <a name="do-i-have-to-enable-support-for-acls"></a>É necessário habilitar o suporte para ACLs?

Não. O controle de acesso via ACLs está sempre ativado para uma conta do Data Lake Store.

### <a name="which-permissions-are-required-to-recursively-delete-a-folder-and-its-contents"></a>Quais são as permissões necessárias para excluir recursivamente uma pasta e seu conteúdo?

* A pasta pai deve ter permissões **Gravar + Executar**.
* A pasta a ser excluída, e todas as pastas nela, exige permissões **Ler + Gravar + Executar**.

> [!NOTE]
> Você não precisa de permissões de gravação para excluir arquivos em pastas. Além disso, a pasta raiz "/" **nunca** poderá ser excluída.
>
>

### <a name="who-is-the-owner-of-a-file-or-folder"></a>Quem é o proprietário de um arquivo ou pasta?

O criador de um arquivo ou pasta se torna o proprietário.

### <a name="which-group-is-set-as-the-owning-group-of-a-file-or-folder-at-creation"></a>Qual grupo é definido como o grupo proprietário de um arquivo ou pasta na criação?

O grupo pertencente é copiado do grupo proprietário da pasta pai na qual o novo arquivo ou pasta é criado.

### <a name="i-am-the-owning-user-of-a-file-but-i-dont-have-the-rwx-permissions-i-need-what-do-i-do"></a>Eu sou o usuário proprietário de um arquivo, mas não tenho as permissões RWX de que necessito. O que devo fazer?

O usuário proprietário pode alterar as permissões do arquivo para atribuir a sim mesmo quaisquer permissões RWX necessárias.

### <a name="when-i-look-at-acls-in-the-azure-portal-i-see-user-names-but-through-apis-i-see-guids-why-is-that"></a>Quando examino as ACLs no portal do Azure eu vejo nomes de usuário, mas por meio de APIs eu vejo GUIDs, por quê?

As entradas nas ACLs são armazenadas como GUIDs que correspondem aos usuários no Azure AD. As APIs retornam os GUIDs como estão. O portal do Azure tenta tornar as ACLs mais fáceis de usar, convertendo os GUIDs em nomes amigáveis quando possível.

### <a name="why-do-i-sometimes-see-guids-in-the-acls-when-im-using-the-azure-portal"></a>Por que, às vezes, vejo GUIDs nas ACLs ao usar o portal do Azure?

Um GUID é mostrado quando o usuário não existe mais no Azure AD. Geralmente isso acontece se o usuário tiver deixado a empresa ou se sua conta tiver sido excluída no Azure AD.

### <a name="does-data-lake-store-support-inheritance-of-acls"></a>O Data Lake Store dá suporte à herança de ACLs?

Não.

### <a name="what-is-the-difference-between-mask-and-umask"></a>Qual é a diferença entre mask e umask?

| mask | umask|
|------|------|
| A propriedade **mask** está disponível em todos os arquivos e pastas. | **umask** é uma propriedade da conta do Data Lake Store. Portanto, há apenas uma única umask no Data Lake Store.    |
| A propriedade mask em um arquivo ou pasta pode ser alterada pelo usuário proprietário ou pelo grupo proprietário de um arquivo ou por um superusuário. | A propriedade umask não pode ser modificada por qualquer usuário, até mesmo por um superusuário. É um valor constante, inalterável.|
| A propriedade mask é usada durante o algoritmo de verificação de acesso em tempo de execução para determinar se um usuário tem o direito de executar a operação em um arquivo ou pasta. A função de mask é criar "permissões efetivas" no momento da verificação de acesso. | A umask nunca é usada durante a verificação de acesso. A umask é usada para determinar o a ACL de Acesso de novos itens filho de uma pasta. |
| Mask é um valor RWX de 3 bits que se aplica ao usuário nomeado, ao grupo nomeado e ao usuário proprietário no momento da verificação de acesso.| Umask é um valor de 9 bits que se aplica ao usuário proprietário, ao grupo proprietário e **outros** de um novo filho.|

### <a name="where-can-i-learn-more-about-posix-access-control-model"></a>Onde posso saber mais sobre o modelo de controle de acesso do POSIX?

* [Listas de controle de acesso POSIX no Linux](http://www.vanemery.com/Linux/ACL/POSIX_ACL_on_Linux.html)

* [Guia de permissão do HDFS](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html)

* [PERGUNTAS FREQUENTES SOBRE O POSIX](http://www.opengroup.org/austin/papers/posix_faq.html)

* [POSIX 1003.1 2008](http://standards.ieee.org/findstds/standard/1003.1-2008.html)

* [POSIX 1003.1 2013](http://pubs.opengroup.org/onlinepubs/9699919799.2013edition/)

* [POSIX 1003.1 2016](http://pubs.opengroup.org/onlinepubs/9699919799.2016edition/)

* [POSIX ACL no Ubuntu](https://help.ubuntu.com/community/FilePermissionsACLs)

* [ACL usando listas de controle de acesso no Linux](http://bencane.com/2012/05/27/acl-using-access-control-lists-on-linux/)

## <a name="see-also"></a>Consulte também

* [Visão geral do Repositório Azure Data Lake](data-lake-store-overview.md)
