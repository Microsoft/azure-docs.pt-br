---
title: Visão geral do controle de acesso no Azure Data Lake Storage Gen2 | Microsoft Docs
description: Compreenda como o controle de acesso funciona no Azure Data Lake Storage Gen2
services: storage
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: 112d3b18df8205aac173eafb8f8e30ed6c32e048
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68249087"
---
# <a name="access-control-in-azure-data-lake-storage-gen2"></a>Controle de acesso no Azure Data Lake Storage Gen2

Azure Data Lake Storage Gen2 implementa um modelo de controle de acesso que dá suporte ao RBAC (controle de acesso baseado em função) do Azure e às ACLs (listas de controle de acesso) semelhantes a POSIX. Este artigo resume as noções básicas do modelo de controle de acesso para o Data Lake Storage Gen2.

<a id="azure-role-based-access-control-rbac" />

## <a name="role-based-access-control"></a>Controle de acesso baseado em função

O RBAC usa atribuições de função para aplicar efetivamente conjuntos de permissões a *entidades de segurança*. Uma *entidade de segurança* é um objeto que representa um usuário, grupo, entidade de serviço ou identidade gerenciada que é definida no Azure Active Directory (AD) que está solicitando acesso aos recursos do Azure.

Normalmente, esses recursos do Azure são restritos a recursos de nível superior (por exemplo: Contas de armazenamento do Azure). No caso do armazenamento do Azure e, consequentemente, Azure Data Lake Storage Gen2, esse mecanismo foi estendido para o recurso de contêiner (sistema de arquivos).

Para saber como atribuir funções a entidades de segurança no escopo da sua conta de armazenamento, consulte [conceder acesso ao blob do Azure e dados de fila com RBAC no portal do Azure](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

### <a name="the-impact-of-role-assignments-on-file-and-directory-level-access-control-lists"></a>O impacto das atribuições de função em listas de controle de acesso de nível de arquivo e diretório

Embora o uso de atribuições de função RBAC seja um mecanismo poderoso para controlar as permissões de acesso, trata-se de um mecanismo muito mais esparso em relação às ACLs. A granularidade menor para o RBAC está no nível de sistema de arquivos e isso será avaliado em uma prioridade mais alta que as ACLs. Portanto, se você atribuir uma função a uma entidade de segurança no escopo de um sistema de arquivos, essa entidade de segurança terá o nível de autorização associado a essa função para todos os diretórios e arquivos nesse sistema de arquivos, independentemente das atribuições de ACL.

Quando uma entidade de segurança recebe permissões de dados RBAC por meio de uma [função interna](https://docs.microsoft.com/azure/storage/common/storage-auth-aad?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#built-in-rbac-roles-for-blobs-and-queues)ou por meio de uma função personalizada, essas permissões são avaliadas primeiro após a autorização de uma solicitação. Se a operação solicitada for autorizada pelas atribuições de RBAC da entidade de segurança, a autorização será imediatamente resolvida e nenhuma verificação de ACL adicional será executada. Como alternativa, se a entidade de segurança não tiver uma atribuição de RBAC ou se a operação da solicitação não corresponder à permissão atribuída, as verificações de ACL serão executadas para determinar se a entidade de segurança está autorizada a executar a operação solicitada.

> [!NOTE]
> Se a entidade de segurança tiver sido atribuída a atribuição de função interna do proprietário de dados do blob de armazenamento, a entidade de  segurança será considerada um superusuário e terá acesso completo a todas as operações de mutação, incluindo a definição do proprietário de um diretório ou arquivo, bem como ACLs para diretórios e arquivos para os quais eles não são o proprietário. O acesso de superusuário é a única maneira autorizada para alterar o proprietário de um recurso.

## <a name="shared-key-and-shared-access-signature-sas-authentication"></a>Autenticação de chave compartilhada e SAS (assinatura de acesso compartilhado)

O Azure Data Lake Storage Gen2 dá suporte a métodos de chave compartilhada e SAS para autenticação. Uma característica desses métodos de autenticação é que nenhuma identidade está associada ao chamador e, portanto, a autorização baseada em permissão de entidade de segurança não pode ser executada.

No caso de chave compartilhada, o chamador efetivamente obtém acesso de “superusuário”, o que significa acesso completo a todas as operações em todos os recursos, incluindo a definição de proprietário e alterando ACLs.

Os tokens SAS incluem permissões permitidas como parte do token. As permissões incluídas no token de SAS com eficiência são aplicadas a todas as decisões de autorização, mas nenhuma verificação ACL adicional é realizada.

## <a name="access-control-lists-on-files-and-directories"></a>Listas de controle de acesso em arquivos e diretórios

Você pode associar uma entidade de segurança a um nível de acesso para arquivos e diretórios. Essas associações são capturadas em uma *ACL (lista de controle de acesso)* . Cada arquivo e diretório na sua conta de armazenamento tem uma lista de controle de acesso.

Se você atribuiu uma função a uma entidade de segurança no nível da conta de armazenamento, poderá usar listas de controle de acesso para conceder a essa entidade de segurança acesso elevado a arquivos e diretórios específicos.

Você não pode usar listas de controle de acesso para fornecer um nível de acesso inferior a um nível concedido por uma atribuição de função. Por exemplo, se você atribuir a função de [colaborador de dados de blob de armazenamento](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor) a uma entidade de segurança, não poderá usar listas de controle de acesso para impedir que a entidade de segurança grave em um diretório.

### <a name="set-file-and-directory-level-permissions-by-using-access-control-lists"></a>Definir permissões de nível de arquivo e diretório usando listas de controle de acesso

Para definir permissões de nível de arquivo e diretório, consulte qualquer um dos seguintes artigos:

|Se você quiser usar essa ferramenta:    |Consulte este artigo:    |
|--------|-----------|
|Gerenciador de Armazenamento do Azure    |[Definir permissões no nível do arquivo e do diretório usando o Gerenciador de Armazenamento do Azure com o Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-how-to-set-permissions-storage-explorer)|
|API REST    |[Caminho-atualizar](https://docs.microsoft.com/rest/api/storageservices/datalakestoragegen2/path/update)|

> [!IMPORTANT]
> Se a entidade de segurança for uma entidade de *serviço* , é importante usar a ID de objeto da entidade de serviço e não a ID de objeto do registro do aplicativo relacionado. Para obter a ID de objeto da entidade de serviço, abra o CLI do Azure e, em seguida, `az ad sp show --id <Your App ID> --query objectId`Use este comando:. Certifique-se de substituir `<Your App ID>` o espaço reservado pela ID do aplicativo do registro do aplicativo.

### <a name="types-of-access-control-lists"></a>Tipos de listas de controle de acesso

Há dois tipos de listas de controle de acesso: *ACLs de acesso* e *ACLs padrão*.

ACLs de acesso controlam o acesso a um objeto. Arquivos e diretórios têm ambos ACLs de acesso.

As ACLs padrão são modelos de ACLs associadas a um diretório que determinam as ACLs de acesso para qualquer item filho que são criados nesse diretório. Arquivos não têm ACLs padrão.

As ACLs de Acesso e as ACLs Padrão têm a mesma estrutura.

> [!NOTE]
> Alterar a ACL Padrão em um pai não afeta o a ACL de Acesso ou a ACL Padrão de itens filhos já existentes.

### <a name="levels-of-permission"></a>Níveis de permissão

As permissões em um objeto do sistema de arquivos são **Ler**, **Gravar** e **Executar** e podem ser usadas em arquivos e em diretórios como mostrado na seguinte tabela:

|            |    Arquivo     |   Diretório |
|------------|-------------|----------|
| **Ler (R)** | Pode ler o conteúdo de um arquivo | Requer **Ler** e **Executar** para listar o conteúdo do diretório |
| **Gravar (W)** | Pode gravar ou anexar a um arquivo | Requer **Gravar** e **Executar** para criar itens filhos em um diretório |
| **Executar (X)** | Não significa nada no contexto do Azure Data Lake Storage Gen2 | Necessário para percorrer os itens filhos de um diretório |

> [!NOTE]
> Se você estiver concedendo permissões usando somente ACLs (sem RBAC), para conceder a uma entidade de serviço acesso de leitura ou gravação a um arquivo, você precisará conceder permissões de **execução** da entidade de serviço ao sistema de arquivos e a cada pasta na hierarquia de pastas que levar ao arquivo.

#### <a name="short-forms-for-permissions"></a>Formatos abreviados para permissões

**RWX**é usado para indicar **Ler + Gravar + Executar**. Existe um formato numérico mais condensado na qual **Ler = 4**, **Gravar = 2** e **Executar = 1** e sua soma representa as permissões. Estes são alguns exemplos:

| Formato numérico | Formato curto |      O que significa     |
|--------------|------------|------------------------|
| 7            | `RWX`        | Ler + Gravar + Executar |
| 5            | `R-X`        | Ler + Executar         |
| 4            | `R--`        | Ler                   |
| 0            | `---`        | Nenhuma permissão         |

#### <a name="permissions-inheritance"></a>Herança de permissões

No modelo de estilo POSIX usado pelo Data Lake Storage Gen2, as permissões para um item são armazenadas no próprio item. Em outras palavras, as permissões para um item não podem ser herdadas dos itens pai se as permissões são definidas depois que o item filho já foi criado. As permissões são herdadas somente se as permissões padrão tiverem sido definidas nos itens pai antes dos itens filho terem sido criados.

### <a name="common-scenarios-related-to-permissions"></a>Cenários comuns relacionados a permissões

A tabela a seguir lista alguns cenários comuns para ajudá-lo a entender quais permissões são necessárias para executar determinadas operações em uma conta de armazenamento.

|    Operação             |    /    | Oregon/ | Portland/ | Data.txt     |
|--------------------------|---------|----------|-----------|--------------|
| Read Data.txt            |   `--X`   |   `--X`    |  `--X`      | `R--`          |
| Append to Data.txt       |   `--X`   |   `--X`    |  `--X`      | `RW-`          |
| Delete Data.txt          |   `--X`   |   `--X`    |  `-WX`      | `---`          |
| Create Data.txt          |   `--X`   |   `--X`    |  `-WX`      | `---`          |
| List /                   |   `R-X`   |   `---`    |  `---`      | `---`          |
| Lista /Oregon/           |   `--X`   |   `R-X`    |  `---`      | `---`          |
| Lista /Oregon/Portland/  |   `--X`   |   `--X`    |  `R-X`      | `---`          |

> [!NOTE]
> Permissões de gravação não são necessárias no arquivo para excluí-lo, desde que as duas condições anteriores sejam verdadeiras.

### <a name="users-and-identities"></a>Usuários e identidades

Todos os arquivos e diretórios têm permissões diferentes para estas identidades:

- O usuário proprietário
- O grupo proprietário
- Usuários nomeados
- Grupos nomeados
- Todas as entidades de serviço
- Identidades gerenciadas nomeadas
- Todos os outros usuários

As identidades dos usuários e grupos são identidades do Azure AD (Azure Active Directory). Então, salvo indicação em contrário, um *usuário*, no contexto de data Lake Storage Gen2, pode se referir a um usuário do Azure AD, entidade de serviço, identidade gerenciada ou grupo de segurança.

#### <a name="the-owning-user"></a>O usuário proprietário

O usuário que criou o item é automaticamente o usuário proprietário do item. Um usuário proprietário pode:

* Altere as permissões de um arquivo pertencente.
* Alterar o grupo proprietário de um arquivo pertencente, contanto que o usuário proprietário também seja membro do grupo de destino.

> [!NOTE]
> O usuário proprietário *não pode* alterar o usuário proprietário de um arquivo ou diretório. Somente superusuários podem alterar o usuário proprietário de um arquivo ou diretório.

#### <a name="the-owning-group"></a>O grupo proprietário

Nas ACLs do POSIX, cada usuário está associado a um *grupo primário*. Por exemplo, o usuário "Alice" pode pertencer ao grupo "Finance". Alice também pode pertencer a vários grupos, mas um grupo é sempre designado como seu grupo primário. No POSIX, quando Alice cria um arquivo, o grupo proprietário desse arquivo é definido como o grupo primário que, nesse caso, é "finanças". De modo contrário, o grupo proprietário se comporta de modo semelhante às permissões atribuídas para outros usuários/grupos.

##### <a name="assigning-the-owning-group-for-a-new-file-or-directory"></a>Atribuindo o grupo proprietário de um novo arquivo ou diretório

* **Caso 1**: Diretório Raiz "/". Esse diretório é criado quando um sistema de arquivos do Azure Data Lake Storage Gen2. Nesse caso, o grupo proprietário é definido para o usuário que criou o sistema de arquivo se tivesse sido feito usando o OAuth. Se o sistema de arquivos for criado usando chave compartilhada, uma SAS de conta ou uma SAS de serviço, o proprietário e o grupo proprietário serão definidos como **$superuser**.
* **Caso 2** (Todos os outros casos): Quando um novo item é criado, o grupo proprietário é copiado da pasta pai.

##### <a name="changing-the-owning-group"></a>Alterando o grupo proprietário

O grupo proprietário pode ser alterado por:
* Todos os superusuários.
* O usuário proprietário, se o usuário proprietário também for membro do grupo de destino.

> [!NOTE]
> O usuário proprietário não pode alterar as ACLs de um arquivo ou um diretório.  Embora o grupo proprietário esteja definido como o usuário que criou a conta no caso do diretório raiz, **caso 1** acima, uma única conta de usuário não é válida para fornecer permissões por meio do grupo proprietário. Você pode atribuir essa permissão a um grupo de usuários válido, se for aplicável.

### <a name="access-check-algorithm"></a>Algoritmo de verificação de acesso

O pseudocódigo a seguir representa o algoritmo de verificação de acesso para contas de armazenamento.

```
def access_check( user, desired_perms, path ) : 
  # access_check returns true if user has the desired permissions on the path, false otherwise
  # user is the identity that wants to perform an operation on path
  # desired_perms is a simple integer with values from 0 to 7 ( R=4, W=2, X=1). User desires these permissions
  # path is the file or directory
  # Note: the "sticky bit" isn't illustrated in this algorithm
  
# Handle super users.
  if (is_superuser(user)) :
    return True

# Handle the owning user. Note that mask isn't used.
entry = get_acl_entry( path, OWNER )
if (user == entry.identity)
    return ( (desired_perms & entry.permissions) == desired_perms )

# Handle the named users. Note that mask IS used.
entries = get_acl_entries( path, NAMED_USER )
for entry in entries:
    if (user == entry.identity ) :
        mask = get_mask( path )
        return ( (desired_perms & entry.permissions & mask) == desired_perms)

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

#### <a name="the-mask"></a>A máscara

Conforme ilustrado no algoritmo de verificação de acesso, a máscara limita o acesso para usuários nomeados, o grupo proprietário e grupos nomeados.  

> [!NOTE]
> Para um novo sistema de arquivos do Azure Data Lake Storage Gen2, o padrão da máscara de acesso ACL do diretório raiz ("/") é 750 para diretórios e 640 para arquivos. Arquivos não recebem o bit X uma vez que é irrelevante para arquivos em um sistema de armazenamento somente.
>
> A máscara pode ser especificada em uma base por chamada. Isso permite que diferentes sistemas de consumo, como clusters, ter diferentes máscaras eficazes para suas operações de arquivo. Se uma máscara for especificada em uma determinada solicitação, ela substitui completamente a máscara padrão.

#### <a name="the-sticky-bit"></a>O sticky bit

O sticky bit é um recurso mais avançado de um sistema de arquivos POSIX. No contexto do Azure Data Lake Storage Gen2, é improvável que o sticky bit seja necessário. Em resumo, se o sticky bit estiver habilitado em um diretório, um item filho apenas poderá ser excluído ou renomeado pelo usuário proprietário do item filho.

O bit adesivo não é mostrado na portal do Azure.

### <a name="default-permissions-on-new-files-and-directories"></a>Permissões padrão em novos arquivos e diretórios

Quando um novo arquivo ou pasta é criado em um diretório existente, a ACL Padrão no diretório pai determina:

- ACL Padrão e ACL de Acesso de um diretório filho.
- ACL de Acesso de um arquivo filho (os arquivos não têm uma ACL Padrão).

#### <a name="umask"></a>umask

Ao criar um arquivo ou diretório, o umask é usado para modificar como as ACLs padrão são definidas no item filho. umask é um valor de 9 bits nas pastas pai que contém um valor RWX para o **usuário proprietário**, o **grupo proprietário** e **outros**.

O umask do Azure Data Lake Storage Gen2 é um valor constante definido como 007. Esse valor é convertido em:

| Componente umask     | Formato numérico | Formato curto | Significado |
|---------------------|--------------|------------|---------|
| umask.owning_user   |    0         |   `---`      | Para o usuário proprietário, copia a ACL Padrão do pai para a ACL de Acesso do filho | 
| umask.owning_group  |    0         |   `---`      | Para o grupo proprietário, copie a ACL Padrão do pai para a ACL de Acesso do filho | 
| umask.other         |    7         |   `RWX`      | Para outros, remova todas as permissões na ACL de Acesso do filho |

O valor de umask usado pelo Azure Data Lake Storage Gen2 significa efetivamente que o valor dos **outros** nunca é transmitido por padrão em novos filhos, independentemente do que a ACL Padrão indica. 

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

## <a name="common-questions-about-acls-in-data-lake-storage-gen2"></a>Perguntas comuns sobre ACLs no Data Lake Storage Gen2

### <a name="do-i-have-to-enable-support-for-acls"></a>É necessário habilitar o suporte para ACLs?

Nº O controle de acesso por meio de ACLs é habilitado para uma conta de armazenamento, desde que o recurso de namespace hierárquico (HNS) esteja ativado.

Se HNS estiver desativado, as regras de autorização do RBAC do Azure ainda se aplicam.

### <a name="what-is-the-best-way-to-apply-acls"></a>Qual é a melhor maneira de aplicar ACLs?

Sempre use grupos de segurança do Microsoft Azure Active Directory como o principal atribuído em ACLs. Resista a oportunidade de atribuir diretamente a usuários individuais ou entidades de serviço. Usar essa estrutura permitirá que você adicione e remova usuários ou entidades de serviço sem a necessidade de reaplicar as ACLs para uma estrutura de diretório inteiro. ) Em vez disso, você só precisa adicionar ou removê-lo do grupo de segurança do Azure Active Directory. Tenha em mente que as ACLs não são herdadas e então reaplicar as ACLs é necessário atualizar a ACL em cada arquivo e o subdiretório. 

### <a name="which-permissions-are-required-to-recursively-delete-a-directory-and-its-contents"></a>Quais são as permissões necessárias para excluir recursivamente um diretório e seu conteúdo?

- O chamador tem permissões de “superusuário”,

Ou

- A pasta diretório deve ter permissões Gravar + Executar.
- O diretório a ser excluído, e todas as pastas nela, exige permissões Ler + Gravar + Executar.

> [!NOTE]
> Você não precisa de permissões de gravação para excluir arquivos em diretórios. Além disso, a pasta raiz "/" nunca poderá ser excluída.

### <a name="who-is-the-owner-of-a-file-or-directory"></a>Quem é o proprietário de um arquivo ou diretório?

O criador de um arquivo ou diretório se torna o proprietário. No caso do diretório raiz, esta é a identidade do usuário que criou o sistema de arquivos.

### <a name="which-group-is-set-as-the-owning-group-of-a-file-or-directory-at-creation"></a>Qual grupo é definido como grupo proprietário de um arquivo ou diretório na criação?

O grupo pertencente é copiado do grupo proprietário do diretório pai na qual o novo arquivo ou diretório é criado.

### <a name="i-am-the-owning-user-of-a-file-but-i-dont-have-the-rwx-permissions-i-need-what-do-i-do"></a>Eu sou o usuário proprietário de um arquivo, mas não tenho as permissões RWX de que necessito. O que devo fazer?

O usuário proprietário pode alterar as permissões do arquivo para atribuir a sim mesmo quaisquer permissões RWX necessárias.

### <a name="why-do-i-sometimes-see-guids-in-acls"></a>Por que, às vezes, vejo GUIDs nas ACLs?

Um GUID será mostrado se a entrada representa um usuário e esse usuário não existir mais no Microsoft Azure Active Directory. Geralmente isso acontece se o usuário tiver deixado a empresa ou se sua conta tiver sido excluída no Azure AD. Além disso, as entidades de serviço e grupos de segurança não tem um nome Principal de Usuário (UPN) para identificá-lo e então, são representados por seu atributo de identificação de objeto (um guid).

### <a name="how-do-i-set-acls-correctly-for-a-service-principal"></a>Como fazer definir ACLs corretamente para uma entidade de serviço?

Quando você define ACLs para entidades de serviço, é importante usar a OID (ID de objeto) da entidade de *serviço* para o registro do aplicativo que você criou. É importante observar que os aplicativos registrados têm uma entidade de serviço separada no locatário específico do Azure AD. Os aplicativos registrados têm um OID que é visível no portal do Azure, mas a *entidade de serviço* tem outro OID (diferente).

Para obter o OID para a entidade de serviço que corresponde a um registro de aplicativo, você pode `az ad sp show` usar o comando. Especifique a ID do aplicativo como o parâmetro. Veja um exemplo de como obter o OID para a entidade de serviço que corresponde a um registro de aplicativo com ID do aplicativo = 18218b12-1895-43e9-ad80-6e8fc1ea88ce. Execute o seguinte comando na CLI do Azure:

```
$ az ad sp show --id 18218b12-1895-43e9-ad80-6e8fc1ea88ce --query objectId
<<OID will be displayed>>
```

Quando você tiver o OID correto para a entidade de serviço, vá para a página Gerenciador de Armazenamento **gerenciar acesso** para adicionar o OID e atribuir as permissões apropriadas para o OID. Certifique-se de selecionar **salvar**.

### <a name="does-data-lake-storage-gen2-support-inheritance-of-acls"></a>O Azure Data Lake Storage Gen2 dá suporte à herança de ACLs?

Herda as atribuições de Azure RBAC. Atribuições de fluxo de assinatura, grupo de recursos e recursos da conta de armazenamento para baixo até o recurso do sistema de arquivos.

ACLS não herdam. Porém, as ACLs padrão pode ser usadas para definir as ACLs de subdiretórios filho e arquivos criados no diretório pai. 

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

* [Visão geral do Azure Data Lake Storage Gen2](../blobs/data-lake-storage-introduction.md)
