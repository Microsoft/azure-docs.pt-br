---
title: Visão geral do controle de acesso no Data Lake Storage Gen1 | Microsoft Docs
description: Compreenda como o controle de acesso funciona no Azure Data Lake Storage Gen1
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: d16f8c09-c954-40d3-afab-c86ffa8c353d
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: twooley
ms.openlocfilehash: 276e691351d852d6dcb0075d47bf33af6767fc10
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2019
ms.locfileid: "68226088"
---
# <a name="access-control-in-azure-data-lake-storage-gen1"></a>Controle de acesso no Azure Data Lake Storage Gen1

O Azure Data Lake Storage Gen1 implementa um modelo de controle de acesso que deriva do HDFS que, por sua vez, deriva do modelo de controle de acesso do POSIX. Este artigo resume as noções básicas do modelo de controle de acesso para o Data Lake Storage Gen1. 

## <a name="access-control-lists-on-files-and-folders"></a>Listas de controle de acesso em arquivos e pastas

Há dois tipos de listas de controle de acesso (ACLs), **ACLs de Acesso** e **ACLs Padrão**.

* **ACLs de Acesso**: Estas listas controlam o acesso a um objeto. Os arquivos e as pastas têm ACLs de Acesso.

* **ACLs padrão**: Um "modelo" de ACLs associado a uma pasta que determina as ACLs de Acesso para quaisquer itens derivados criados sob essa pasta. Os Arquivos não têm ACLs Padrão.


As ACLs de Acesso e as ACLs Padrão têm a mesma estrutura.



> [!NOTE]
> Alterar a ACL Padrão em um pai não afeta o a ACL de Acesso ou a ACL Padrão de itens filhos já existentes.
>
>

## <a name="permissions"></a>Permissões

As permissões em um objeto do sistema de arquivos são **Ler**, **Gravar** e **Executar** e podem ser usadas em arquivos e em pastas como mostrado na seguinte tabela:

|            |    Arquivo     |   Pasta |
|------------|-------------|----------|
| **Ler (R)** | Pode ler o conteúdo de um arquivo | Requer **Ler** e **Executar** para listar o conteúdo da pasta|
| **Gravar (W)** | Pode gravar ou anexar a um arquivo | Requer **Gravar** e **Executar** para criar itens filhos em uma pasta |
| **Executar (X)** | Não significa nada no contexto do Data Lake Storage Gen1 | Necessário para percorrer os itens filhos de uma pasta |

### <a name="short-forms-for-permissions"></a>Formatos abreviados para permissões

**RWX**é usado para indicar **Ler + Gravar + Executar**. Existe um formato numérico mais condensado na qual **Ler = 4**, **Gravar = 2** e **Executar = 1** e sua soma representa as permissões. Estes são alguns exemplos:

| Formato numérico | Formato curto |      O que significa     |
|--------------|------------|------------------------|
| 7            | `RWX`        | Ler + Gravar + Executar |
| 5            | `R-X`        | Ler + Executar         |
| 4            | `R--`        | Ler                   |
| 0            | `---`        | Nenhuma permissão         |


### <a name="permissions-do-not-inherit"></a>Não herdam permissões

No modelo de estilo POSIX usado pelo Data Lake Storage Gen1, as permissões para um item são armazenadas no próprio item. Em outras palavras, as permissões para um item não podem ser herdadas dos itens pais.

## <a name="common-scenarios-related-to-permissions"></a>Cenários comuns relacionados a permissões

A seguir estão alguns cenários comuns para ajudá-lo a compreender quais permissões são necessárias para executar determinadas operações em uma conta do Data Lake Storage Gen1.

| Operação | Objeto              |    /      | Seattle/   | Portland/   | Data.txt       |
|-----------|---------------------|-----------|------------|-------------|----------------|
| Ler      | Data.txt            |   `--X`   |   `--X`    |  `--X`      | `R--`          |
| Acrescentar a | Data.txt            |   `--X`   |   `--X`    |  `--X`      | `RW-`          |
| Excluir    | Data.txt            |   `--X`   |   `--X`    |  `-WX`      | `---`          |
| Criar    | Data.txt            |   `--X`   |   `--X`    |  `-WX`      | `---`          |
| Lista      | /                   |   `R-X`   |   `---`    |  `---`      | `---`          |
| Lista      | /Seattle/           |   `--X`   |   `R-X`    |  `---`      | `---`          |
| Lista      | /Seattle/Portland/  |   `--X`   |   `--X`    |  `R-X`      | `---`          |


> [!NOTE]
> Permissões de gravação não são necessárias no arquivo para excluí-lo, desde que as duas condições anteriores sejam verdadeiras.
>
>


## <a name="users-and-identities"></a>Usuários e identidades

Todos os arquivos e pastas têm permissões diferentes para estas identidades:

* O usuário proprietário
* O grupo proprietário
* Usuários nomeados
* Grupos nomeados
* Todos os outros usuários

As identidades dos usuários e grupos são identidades do Azure AD (Azure Active Directory). Portanto, a menos que indicado em contrário, um "usuário," no contexto do Data Lake Storage Gen1, pode significar um usuário do Azure AD ou um grupo de segurança do Azure AD.

### <a name="the-super-user"></a>O superusuário

Um superusuário é aquele que tem mais direitos que todos os usuários na conta do Data Lake Storage Gen1. Um superusuário:

* Tem Permissões RWX para **todos** os arquivos e pastas.
* Pode alterar as permissões em qualquer arquivo ou pasta.
* Pode alterar o usuário proprietário ou o grupo proprietário de qualquer arquivo ou pasta.

Todos os usuários que fazem parte da função **Proprietários** de uma conta do Data Lake Storage Gen1 são automaticamente superusuários.

### <a name="the-owning-user"></a>O usuário proprietário

O usuário que criou o item é automaticamente o usuário proprietário do item. Um usuário proprietário pode:

* Altere as permissões de um arquivo pertencente.
* Alterar o grupo proprietário de um arquivo pertencente, contanto que o usuário proprietário também seja membro do grupo de destino.

> [!NOTE]
> O usuário proprietário *não pode* alterar o usuário proprietário de um arquivo ou uma pasta. Somente superusuários podem alterar o usuário proprietário de um arquivo ou pasta.
>
>

### <a name="the-owning-group"></a>O grupo proprietário

**Informações**

Nas ACLs do POSIX, cada usuário está associado a um "grupo primário". Por exemplo, o usuário "alice" pode pertencer ao grupo "finanças". Alice pode pertencer também a vários grupos, mas um grupo será sempre designado como o grupo primário dela. No POSIX, quando Alice cria um arquivo, o grupo proprietário desse arquivo é definido como o grupo primário que, nesse caso, é "finanças". De modo contrário, o grupo proprietário se comporta de modo semelhante às permissões atribuídas para outros usuários/grupos.

Como não há "grupo primário" associado a usuários no Data Lake Storage Gen1, o grupo proprietário é atribuído conforme mostrado abaixo.

**Atribuindo o grupo proprietário de um novo arquivo ou pasta**

* **Caso 1**: A pasta raiz "/". Essa pasta é criada quando uma conta do Data Lake Storage Gen1 é criada. Nesse caso, o grupo proprietário é definido como um GUID de zeros.  Este valor não permite acesso.  Ele será um espaço reservado até ao momento em que um grupo for atribuído.
* **Caso 2** (Todos os outros casos): Quando um novo item é criado, o grupo proprietário é copiado da pasta pai.

**Alterando o grupo proprietário**

O grupo proprietário pode ser alterado por:
* Todos os superusuários.
* O usuário proprietário, se o usuário proprietário também for membro do grupo de destino.

> [!NOTE]
> O usuário proprietário *não pode* alterar as ACLs de um arquivo ou uma pasta.
>
> Para contas criadas em ou antes de setembro de 2018, o grupo proprietário foi definido para o usuário que criou a conta no caso da pasta raiz do **Caso 1**, acima.  Uma única conta de usuário não é válida para fornecer permissões por meio do grupo proprietário, portanto, nenhuma permissão é concedida por essa configuração padrão. Você pode atribuir essa permissão a um grupo de usuários válido.


## <a name="access-check-algorithm"></a>Algoritmo de verificação de acesso

O pseudocódigo a seguir representa o algoritmo de verificação de acesso das contas do Data Lake Storage Gen1.

```
def access_check( user, desired_perms, path ) : 
  # access_check returns true if user has the desired permissions on the path, false otherwise
  # user is the identity that wants to perform an operation on path
  # desired_perms is a simple integer with values from 0 to 7 ( R=4, W=2, X=1). User desires these permissions
  # path is the file or folder
  # Note: the "sticky bit" is not illustrated in this algorithm
  
# Handle super users.
  if (is_superuser(user)) :
    return True

  # Handle the owning user. Note that mask IS NOT used.
  entry = get_acl_entry( path, OWNER )
  if (user == entry.identity)
      return ( (desired_perms & entry.permissions) == desired_perms )

  # Handle the named users. Note that mask IS used.
  entries = get_acl_entries( path, NAMED_USER )
  for entry in entries:
      if (user == entry.identity ) :
          mask = get_mask( path )
          return ( (desired_perms & entry.permmissions & mask) == desired_perms)

  # Handle named groups and owning group
  member_count = 0
  perms = 0
  entries = get_acl_entries( path, NAMED_GROUP | OWNING_GROUP )
  for entry in entries:
    if (user_is_member_of_group(user, entry.identity)) :
      member_count += 1
      perms | =  entry.permissions
  if (member_count>0) :
    return ((desired_perms & perms & mask ) == desired_perms)
 
  # Handle other
  perms = get_perms_for_other(path)
  mask = get_mask( path )
  return ( (desired_perms & perms & mask ) == desired_perms)
```

### <a name="the-mask"></a>A máscara

Conforme ilustrado no algoritmo de verificação de acesso, a máscara limita o acesso para **usuários nomeados**, o **grupo proprietário** e **grupos nomeados**.  

> [!NOTE]
> Para uma nova conta do Data Lake Storage Gen1, a mask da ACL de Acesso da pasta raiz ("/") tem RWX como padrão.
>
>

### <a name="the-sticky-bit"></a>O sticky bit

O sticky bit é um recurso mais avançado de um sistema de arquivos POSIX. No contexto do Data Lake Storage Gen1, é improvável que o sticky bit seja necessário. Em resumo, se o sticky bit estiver habilitado em uma pasta, um item filho apenas poderá ser excluído ou renomeado pelo usuário proprietário do item filho.

O sticky bit não é mostrado no portal do Azure.

## <a name="default-permissions-on-new-files-and-folders"></a>Permissões padrão em novos arquivos e pastas

Quando um novo arquivo ou pasta é criado em uma pasta existente, a ACL Padrão na pasta pai determina:

- ACL Padrão e ACL de Acesso de uma pasta filho.
- ACL de Acesso de um arquivo filho (os arquivos não têm uma ACL Padrão).

### <a name="umask"></a>umask

Ao criar um arquivo ou uma pasta, o umask é usado para modificar como as ACLs padrão são definidas no item filho. umask é um valor de 9 bits em pastas pai que contém um valor de RWX para **usuário proprietário**, **grupo proprietário**e **outros**.

O umask para Azure Data Lake Storage Gen1 é um valor constante definido como 007. Esse valor é convertido em

| Componente umask     | Formato numérico | Formato curto | Significado |
|---------------------|--------------|------------|---------|
| umask.owning_user   |    0         |   `---`      | Para o usuário proprietário, copia a ACL Padrão do pai para a ACL de Acesso do filho | 
| umask.owning_group  |    0         |   `---`      | Para o grupo proprietário, copia a ACL Padrão do pai para a ACL de Acesso do filho | 
| umask.other         |    7         |   `RWX`      | Para outros, remova todas as permissões na ACL de Acesso do filho |

O valor de umask usado pelo Azure Data Lake Storage Gen1 significa efetivamente que o valor dos outros nunca é transmitido por padrão em novos filhos, independentemente do que a ACL Padrão indica. 

O pseudocódigo a seguir mostra como o umask é aplicado ao criar as ACLs de um item filho.

```
def set_default_acls_for_new_child(parent, child):
    child.acls = []
    for entry in parent.acls :
        new_entry = None
        if (entry.type == OWNING_USER) :
            new_entry = entry.clone(perms = entry.perms & (~umask.owning_user))
        elif (entry.type == OWNING_GROUP) :
            new_entry = entry.clone(perms = entry.perms & (~umask.owning_group))
        elif (entry.type == OTHER) :
            new_entry = entry.clone(perms = entry.perms & (~umask.other))
        else :
            new_entry = entry.clone(perms = entry.perms )
        child_acls.add( new_entry )
```

## <a name="common-questions-about-acls-in-data-lake-storage-gen1"></a>Perguntas comuns sobre ACLs no Data Lake Storage Gen1

### <a name="do-i-have-to-enable-support-for-acls"></a>É necessário habilitar o suporte para ACLs?

Nº O controle de acesso via ACLs está sempre ativado para uma conta do Data Lake Storage Gen1.

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

### <a name="does-data-lake-storage-gen1-support-inheritance-of-acls"></a>O Data Lake Storage Gen1 dá suporte à herança de ACLs?

Não, mas ACLs padrão pode ser usado para definir as ACLs de arquivos filho e recentemente criado na pasta pai da pasta.  

### <a name="where-can-i-learn-more-about-posix-access-control-model"></a>Onde posso saber mais sobre o modelo de controle de acesso do POSIX?

* [Listas de controle de acesso POSIX no Linux](https://www.linux.com/news/posix-acls-linux)
* [Guia de permissão do HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html)
* [PERGUNTAS FREQUENTES SOBRE O POSIX](https://www.opengroup.org/austin/papers/posix_faq.html)
* [POSIX 1003.1 2008](https://standards.ieee.org/findstds/standard/1003.1-2008.html)
* [POSIX 1003.1 2013](https://pubs.opengroup.org/onlinepubs/9699919799.2013edition/)
* [POSIX 1003.1 2016](https://pubs.opengroup.org/onlinepubs/9699919799.2016edition/)
* [POSIX ACL no Ubuntu](https://help.ubuntu.com/community/FilePermissionsACLs)
* [ACL usando listas de controle de acesso no Linux](https://bencane.com/2012/05/27/acl-using-access-control-lists-on-linux/)

## <a name="see-also"></a>Consulte também

* [Visão Geral do Azure Data Lake Storage Gen1](data-lake-store-overview.md)
