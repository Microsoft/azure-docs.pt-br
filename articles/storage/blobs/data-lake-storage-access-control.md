---
title: Listas de controle de acesso no Azure Data Lake Storage Gen2 | Microsoft Docs
description: Entenda como as listas de controle de acesso de ACLs semelhantes ao POSIX funcionam em Azure Data Lake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 10/16/2020
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: 2a1455c5956297a19d640146879f93b61d035139
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/14/2021
ms.locfileid: "98185896"
---
# <a name="access-control-lists-acls-in-azure-data-lake-storage-gen2"></a>ACLs (listas de controle de acesso) no Azure Data Lake Storage Gen2

Azure Data Lake Storage Gen2 implementa um modelo de controle de acesso que dá suporte ao controle de acesso baseado em função do Azure (RBAC do Azure) e às ACLs (listas de controle de acesso) semelhantes a POSIX. Este artigo descreve as listas de controle de acesso no Data Lake Storage Gen2. Para saber mais sobre como incorporar o RBAC do Azure junto com ACLs e como o sistema os avalia para tomar decisões de autorização, consulte [modelo de controle de acesso em Azure data Lake Storage Gen2](data-lake-storage-access-control-model.md).

<a id="access-control-lists-on-files-and-directories"></a>

## <a name="about-acls"></a>Sobre ACLs

Você pode associar uma [entidade de segurança](../../role-based-access-control/overview.md#security-principal) a um nível de acesso para arquivos e diretórios. Essas associações são capturadas em uma *ACL (lista de controle de acesso)*. Cada arquivo e diretório na sua conta de armazenamento tem uma lista de controle de acesso. Quando uma entidade de segurança tenta uma operação em um arquivo ou diretório, uma verificação de ACL determina se a entidade de segurança (usuário, grupo, entidade de serviço ou identidade gerenciada) tem o nível de permissão correto para executar a operação.

> [!NOTE]
> As ACLs se aplicam somente a entidades de segurança no mesmo locatário e não se aplicam a usuários que usam a autenticação de token de chave compartilhada ou de assinatura de acesso compartilhado (SAS). Isso porque nenhuma identidade está associada ao chamador e, portanto, a autorização baseada em permissão de entidade de segurança não pode ser executada.  

## <a name="how-to-set-acls"></a>Como definir ACLs

Para definir permissões de nível de arquivo e diretório, consulte qualquer um dos seguintes artigos:

| Ambiente | Artigo |
|--------|-----------|
|Gerenciador de Armazenamento do Azure |[Use o Gerenciador de Armazenamento do Azure para gerenciar diretórios, arquivos e ACLs no Azure Data Lake Storage Gen2](data-lake-storage-explorer.md#managing-access)|
|.NET |[Use o .NET para gerenciar diretórios, arquivos e ACLs no Azure Data Lake Storage Gen2](data-lake-storage-directory-file-acl-dotnet.md#manage-access-control-lists-acls)|
|Java|[Use o Java para gerenciar diretórios, arquivos e ACLs no Azure Data Lake Storage Gen2](data-lake-storage-directory-file-acl-java.md#manage-access-control-lists-acls)|
|Python|[Use o Python para gerenciar diretórios, arquivos e ACLs no Azure Data Lake Storage Gen2](data-lake-storage-directory-file-acl-python.md#manage-access-control-lists-acls)|
|PowerShell|[Use o PowerShell para gerenciar diretórios, arquivos e ACLs no Azure Data Lake Storage Gen2](data-lake-storage-directory-file-acl-powershell.md#manage-access-control-lists-acls)|
|CLI do Azure|[Usar a CLI do Azure para gerenciar diretórios, arquivos e ACLs no Azure Data Lake Storage Gen2](data-lake-storage-directory-file-acl-cli.md#manage-access-control-lists-acls)|
|API REST |[Caminho-atualizar](/rest/api/storageservices/datalakestoragegen2/path/update)|

> [!IMPORTANT]
> Se a entidade de segurança for uma entidade de *serviço* , é importante usar a ID de objeto da entidade de serviço e não a ID de objeto do registro do aplicativo relacionado. Para obter a ID de objeto da entidade de serviço, abra o CLI do Azure e, em seguida, use este comando: `az ad sp show --id <Your App ID> --query objectId` . Certifique-se de substituir o `<Your App ID>` espaço reservado pela ID do aplicativo do registro do aplicativo.

## <a name="types-of-acls"></a>Tipos de ACLs

Há dois tipos de listas de controle de acesso: *ACLs de acesso* e *ACLs padrão*.

ACLs de acesso controlam o acesso a um objeto. Arquivos e diretórios têm ambos ACLs de acesso.

As ACLs padrão são modelos de ACLs associadas a um diretório que determinam as ACLs de acesso para qualquer item filho que são criados nesse diretório. Arquivos não têm ACLs padrão.

As ACLs de Acesso e as ACLs Padrão têm a mesma estrutura.

> [!NOTE]
> Alterar a ACL Padrão em um pai não afeta o a ACL de Acesso ou a ACL Padrão de itens filhos já existentes.

## <a name="levels-of-permission"></a>Níveis de permissão

As permissões em diretórios e arquivos em um contêiner são **ler**, **gravar** e **executar** e podem ser usadas em arquivos e diretórios, conforme mostrado na tabela a seguir:

|            |    Arquivo     |   Diretório |
|------------|-------------|----------|
| **Ler (R)** | Pode ler o conteúdo de um arquivo | Requer **Ler** e **Executar** para listar o conteúdo do diretório |
| **Gravar (W)** | Pode gravar ou anexar a um arquivo | Requer **Gravar** e **Executar** para criar itens filhos em um diretório |
| **Executar (X)** | Não significa nada no contexto do Azure Data Lake Storage Gen2 | Necessário para percorrer os itens filhos de um diretório |

> [!NOTE]
> Se você estiver concedendo permissões usando somente ACLs (sem RBAC do Azure), para conceder uma entidade de segurança de acesso de leitura ou de gravação a um arquivo, você precisará fornecer permissões de **execução** da entidade de segurança para a pasta raiz do contêiner e para cada pasta na hierarquia de pastas que levam ao arquivo.

### <a name="short-forms-for-permissions"></a>Formatos abreviados para permissões

**RWX** é usado para indicar **Ler + Gravar + Executar**. Existe um formato numérico mais condensado na qual **Ler = 4**, **Gravar = 2** e **Executar = 1** e sua soma representa as permissões. Estes são alguns exemplos:

| Formato numérico | Formato curto |      O que significa     |
|--------------|------------|------------------------|
| 7            | `RWX`        | Ler + Gravar + Executar |
| 5            | `R-X`        | Ler + Executar         |
| 4            | `R--`        | Ler                   |
| 0            | `---`        | Nenhuma permissão         |

### <a name="permissions-inheritance"></a>Herança de permissões

No modelo de estilo POSIX usado pelo Data Lake Storage Gen2, as permissões para um item são armazenadas no próprio item. Em outras palavras, as permissões para um item não podem ser herdadas dos itens pai se as permissões são definidas depois que o item filho já foi criado. As permissões são herdadas somente se as permissões padrão tiverem sido definidas nos itens pai antes dos itens filho terem sido criados.

## <a name="common-scenarios-related-to-acl-permissions"></a>Cenários comuns relacionados a permissões de ACL

A tabela a seguir mostra as entradas de ACL necessárias para habilitar uma entidade de segurança para executar as operações listadas na coluna **operação** . 

Esta tabela mostra uma coluna que representa cada nível de uma hierarquia de diretório fictícia. Há uma coluna para o diretório raiz do contêiner ( `\` ), um subdiretório chamado **Oregon**, um subdiretório do diretório Oregon chamado **Portland** e um arquivo de texto no diretório de Portland chamado **Data.txt**. 

> [!IMPORTANT]
> Esta tabela pressupõe que você está usando **somente** ACLs sem nenhuma atribuição de função do Azure. Para ver uma tabela semelhante que combina o RBAC do Azure junto com ACLs, consulte [tabela de permissões: combinando o RBAC e a ACL do Azure](data-lake-storage-access-control-model.md#permissions-table-combining-azure-rbac-and-acl).

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

## <a name="users-and-identities"></a>Usuários e identidades

Todos os arquivos e diretórios têm permissões diferentes para estas identidades:

- O usuário proprietário
- O grupo proprietário
- Usuários nomeados
- Grupos nomeados
- Todas as entidades de serviço
- Identidades gerenciadas nomeadas
- Todos os outros usuários

As identidades dos usuários e grupos são identidades do Azure AD (Azure Active Directory). Então, salvo indicação em contrário, um *usuário*, no contexto de data Lake Storage Gen2, pode se referir a um usuário do Azure AD, entidade de serviço, identidade gerenciada ou grupo de segurança.

### <a name="the-owning-user"></a>O usuário proprietário

O usuário que criou o item é automaticamente o usuário proprietário do item. Um usuário proprietário pode:

* Altere as permissões de um arquivo pertencente.
* Alterar o grupo proprietário de um arquivo pertencente, contanto que o usuário proprietário também seja membro do grupo de destino.

> [!NOTE]
> O usuário proprietário *não pode* alterar o usuário proprietário de um arquivo ou diretório. Somente superusuários podem alterar o usuário proprietário de um arquivo ou diretório.

### <a name="the-owning-group"></a>O grupo proprietário

Nas ACLs de POSIX, cada usuário é associado a um *grupo primário*. Por exemplo, o usuário "Alice" pode pertencer ao grupo "Finance". Alice também pode pertencer a vários grupos, mas um grupo é sempre designado como seu grupo primário. No POSIX, quando Alice cria um arquivo, o grupo proprietário desse arquivo é definido como o grupo primário que, nesse caso, é "finanças". De modo contrário, o grupo proprietário se comporta de modo semelhante às permissões atribuídas para outros usuários/grupos.

#### <a name="assigning-the-owning-group-for-a-new-file-or-directory"></a>Atribuindo o grupo proprietário de um novo arquivo ou diretório

* **Caso 1**: o diretório raiz "/". Esse diretório é criado quando um contêiner de Data Lake Storage Gen2 é criado. Nesse caso, o grupo proprietário é definido para o usuário que criou o contêiner se ele foi feito usando o OAuth. Se o contêiner for criado usando chave compartilhada, uma SAS de conta ou uma SAS de serviço, o proprietário e o grupo proprietário serão definidos como **$superuser**.
* **Caso 2** (todos os outros casos): quando um novo item é criado, o grupo proprietário é copiado do diretório pai.

#### <a name="changing-the-owning-group"></a>Alterando o grupo proprietário

O grupo proprietário pode ser alterado por:
* Todos os superusuários.
* O usuário proprietário, se o usuário proprietário também for membro do grupo de destino.

> [!NOTE]
> O usuário proprietário não pode alterar as ACLs de um arquivo ou um diretório.  Embora o grupo proprietário esteja definido como o usuário que criou a conta no caso do diretório raiz, **caso 1** acima, uma única conta de usuário não é válida para fornecer permissões por meio do grupo proprietário. Você pode atribuir essa permissão a um grupo de usuários válido, se for aplicável.

## <a name="access-check-algorithm"></a>Algoritmo de verificação de acesso

O pseudocódigo a seguir representa o algoritmo de verificação de acesso para contas de armazenamento.

```console
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
  mask = get_mask( path )
  for entry in entries:
    if (user_is_member_of_group(user, entry.identity)) :
        if ((desired_perms & entry.permissions & mask) == desired_perms)
            return True 
        
  # Handle other
  perms = get_perms_for_other(path)
  mask = get_mask( path )
  return ( (desired_perms & perms & mask ) == desired_perms)
```

### <a name="the-mask"></a>A máscara

Conforme ilustrado no algoritmo de verificação de acesso, a máscara limita o acesso para usuários nomeados, o grupo proprietário e grupos nomeados.  

Para um novo contêiner Data Lake Storage Gen2, a máscara para a ACL de acesso do diretório raiz ("/") usa como padrão **750** para diretórios e **640** para arquivos. A tabela a seguir mostra a notação simbólica desses níveis de permissão.

|Entidade|Diretórios|Arquivos|
|--|--|--|
|Usuário proprietário|`rwx`|`r-w`|
|Grupo proprietário|`r-x`|`r--`|
|Outro|`---`|`---`|

Arquivos não recebem o bit X uma vez que é irrelevante para arquivos em um sistema de armazenamento somente. 

A máscara pode ser especificada em uma base por chamada. Isso permite que diferentes sistemas de consumo, como clusters, ter diferentes máscaras eficazes para suas operações de arquivo. Se uma máscara for especificada em uma determinada solicitação, ela substitui completamente a máscara padrão.

### <a name="the-sticky-bit"></a>O sticky bit

O bit adesivo é um recurso mais avançado de um contêiner POSIX. No contexto do Azure Data Lake Storage Gen2, é improvável que o sticky bit seja necessário. Em resumo, se o sticky bit estiver habilitado em um diretório, um item filho apenas poderá ser excluído ou renomeado pelo usuário proprietário do item filho.

O bit adesivo não é mostrado na portal do Azure.

## <a name="default-permissions-on-new-files-and-directories"></a>Permissões padrão em novos arquivos e diretórios

Quando um novo arquivo ou pasta é criado em um diretório existente, a ACL Padrão no diretório pai determina:

- ACL padrão de um diretório filho e ACL de acesso.
- ACL de Acesso de um arquivo filho (os arquivos não têm uma ACL Padrão).

### <a name="umask"></a>umask

Ao criar um arquivo ou diretório, o umask é usado para modificar como as ACLs padrão são definidas no item filho. umask é um valor de 9 bits nas pastas pai que contém um valor RWX para o **usuário proprietário**, o **grupo proprietário** e **outros**.

O umask do Azure Data Lake Storage Gen2 é um valor constante definido como 007. Esse valor é convertido em:

| Componente umask     | Formato numérico | Formato curto | Significado |
|---------------------|--------------|------------|---------|
| umask.owning_user   |    0         |   `---`      | Para o usuário proprietário, copia a ACL Padrão do pai para a ACL de Acesso do filho | 
| umask.owning_group  |    0         |   `---`      | Para o grupo proprietário, copie a ACL Padrão do pai para a ACL de Acesso do filho | 
| umask.other         |    7         |   `RWX`      | Para outros, remova todas as permissões na ACL de Acesso do filho |

O valor umask usado por Azure Data Lake Storage Gen2 efetivamente significa que o valor de **outro** nunca é transmitido por padrão em novos filhos, a menos que uma ACL padrão seja definida no diretório pai. Nesse caso, o umask é efetivamente ignorado e as permissões definidas pela ACL padrão são aplicadas ao item filho. 

O pseudocódigo a seguir mostra como o umask é aplicado ao criar as ACLs de um item filho.

```console
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

## <a name="faq"></a>Perguntas frequentes

### <a name="do-i-have-to-enable-support-for-acls"></a>É necessário habilitar o suporte para ACLs?

Não. O controle de acesso por meio de ACLs é habilitado para uma conta de armazenamento, desde que o recurso de namespace hierárquico (HNS) esteja ativado.

Se o HNS for desativado, as regras de autorização do RBAC do Azure ainda serão aplicadas.

### <a name="what-is-the-best-way-to-apply-acls"></a>Qual é a melhor maneira de aplicar ACLs?

[!INCLUDE [Security groups](../../../includes/azure-storage-data-lake-groups.md)] 

### <a name="how-are-azure-rbac-and-acl-permissions-evaluated"></a>Como as permissões de RBAC e ACL do Azure são avaliadas?

Para saber como o sistema avalia o RBAC e as ACLs do Azure juntos para tomar decisões de autorização para recursos de conta de armazenamento, consulte [como as permissões são avaliadas](data-lake-storage-access-control-model.md#how-permissions-are-evaluated).

### <a name="what-are-the-limits-for-azure-role-assignments-and-acl-entries"></a>Quais são os limites para as atribuições de função do Azure e as entradas de ACL?

A tabela a seguir fornece uma exibição resumida dos limites a serem considerados ao usar o RBAC do Azure para gerenciar permissões "de alta granularidade" (permissões que se aplicam a contêineres ou contas de armazenamento) e usando ACLs para gerenciar permissões "refinadas" (permissões que se aplicam a arquivos e diretórios). Use grupos de segurança para atribuições de ACL. Usando grupos, você tem menor probabilidade de exceder o número máximo de atribuições de função por assinatura e o número máximo de entradas de ACl por arquivo ou diretório. 

[!INCLUDE [Security groups](../../../includes/azure-storage-data-lake-rbac-acl-limits.md)] 

### <a name="does-data-lake-storage-gen2-support-inheritance-of-azure-rbac"></a>Data Lake Storage Gen2 dá suporte à herança do RBAC do Azure?

As atribuições de função do Azure herdam. As atribuições fluem de assinatura, grupo de recursos e recursos da conta de armazenamento até o recurso de contêiner.

### <a name="does-data-lake-storage-gen2-support-inheritance-of-acls"></a>O Azure Data Lake Storage Gen2 dá suporte à herança de ACLs?

As ACLs padrão podem ser usadas para definir ACLs para novos subdiretórios filho e arquivos criados no diretório pai. Para atualizar ACLs para itens filho existentes, você precisará adicionar, atualizar ou remover ACLs recursivamente para a hierarquia de diretório desejada. Para obter mais informações, consulte [definir listas de controle de acesso (ACLs) recursivamente para Azure data Lake Storage Gen2](recursive-access-control-lists.md). 

### <a name="which-permissions-are-required-to-recursively-delete-a-directory-and-its-contents"></a>Quais são as permissões necessárias para excluir recursivamente um diretório e seu conteúdo?

- O chamador tem permissões de ' superusuário ',

Ou

- A pasta diretório deve ter permissões Gravar + Executar.
- O diretório a ser excluído, e todas as pastas nela, exige permissões Ler + Gravar + Executar.

> [!NOTE]
> Você não precisa de permissões de gravação para excluir arquivos em diretórios. Além disso, a pasta raiz "/" nunca poderá ser excluída.

### <a name="who-is-the-owner-of-a-file-or-directory"></a>Quem é o proprietário de um arquivo ou diretório?

O criador de um arquivo ou diretório se torna o proprietário. No caso do diretório raiz, essa é a identidade do usuário que criou o contêiner.

### <a name="which-group-is-set-as-the-owning-group-of-a-file-or-directory-at-creation"></a>Qual grupo é definido como grupo proprietário de um arquivo ou diretório na criação?

O grupo pertencente é copiado do grupo proprietário do diretório pai na qual o novo arquivo ou diretório é criado.

### <a name="i-am-the-owning-user-of-a-file-but-i-dont-have-the-rwx-permissions-i-need-what-do-i-do"></a>Sou o usuário proprietário de um arquivo, mas não tenho as permissões de RWX de que preciso. O que devo fazer?

O usuário proprietário pode alterar as permissões do arquivo para atribuir a sim mesmo quaisquer permissões RWX necessárias.

### <a name="why-do-i-sometimes-see-guids-in-acls"></a>Por que, às vezes, vejo GUIDs nas ACLs?

Um GUID será mostrado se a entrada representa um usuário e esse usuário não existir mais no Microsoft Azure Active Directory. Geralmente isso acontece se o usuário tiver deixado a empresa ou se sua conta tiver sido excluída no Azure AD. Além disso, as entidades de serviço e grupos de segurança não tem um nome Principal de Usuário (UPN) para identificá-lo e então, são representados por seu atributo de identificação de objeto (um guid).

### <a name="how-do-i-set-acls-correctly-for-a-service-principal"></a>Como fazer definir ACLs corretamente para uma entidade de serviço?

Quando você define ACLs para entidades de serviço, é importante usar a OID (ID de objeto) da entidade de *serviço* para o registro do aplicativo que você criou. É importante observar que os aplicativos registrados têm uma entidade de serviço separada no locatário específico do Azure AD. Os aplicativos registrados têm um OID que é visível no portal do Azure, mas a *entidade de serviço* tem outro OID (diferente).

Para obter o OID para a entidade de serviço que corresponde a um registro de aplicativo, você pode usar o `az ad sp show` comando. Especifique a ID do aplicativo como o parâmetro. Veja um exemplo de como obter o OID para a entidade de serviço que corresponde a um registro de aplicativo com ID do aplicativo = 18218b12-1895-43e9-ad80-6e8fc1ea88ce. Execute o seguinte comando na CLI do Azure:

```azurecli
az ad sp show --id 18218b12-1895-43e9-ad80-6e8fc1ea88ce --query objectId
```

O OID será exibido.

Quando você tiver o OID correto para a entidade de serviço, vá para a página Gerenciador de Armazenamento **gerenciar acesso** para adicionar o OID e atribuir as permissões apropriadas para o OID. É necessário que você selecione **Salvar**.

### <a name="can-i-set-the-acl-of-a-container"></a>Posso definir a ACL de um contêiner?

Não. Um contêiner não tem uma ACL. No entanto, você pode definir a ACL do diretório raiz do contêiner. Cada contêiner tem um diretório raiz e compartilha o mesmo nome que o contêiner. Por exemplo, se o contêiner for nomeado `my-container` , o diretório raiz será nomeado `myContainer/` . 

A API REST do armazenamento do Azure contém uma operação denominada [set container ACL](/rest/api/storageservices/set-container-acl), mas essa operação não pode ser usada para definir a ACL de um contêiner ou o diretório raiz de um contêiner. Em vez disso, essa operação é usada para indicar se os BLOBs em um contêiner [podem ser acessados publicamente](anonymous-read-access-configure.md). 

### <a name="where-can-i-learn-more-about-posix-access-control-model"></a>Onde posso saber mais sobre o modelo de controle de acesso do POSIX?

* [Listas de controle de acesso POSIX no Linux](https://www.linux.com/news/posix-acls-linux)
* [Guia de permissão do HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html)
* [PERGUNTAS FREQUENTES SOBRE O POSIX](https://www.opengroup.org/austin/papers/posix_faq.html)
* [POSIX 1003.1 2008](https://standards.ieee.org/findstds/standard/1003.1-2008.html)
* [POSIX 1003.1 2013](https://pubs.opengroup.org/onlinepubs/9699919799.2013edition/)
* [POSIX 1003.1 2016](https://pubs.opengroup.org/onlinepubs/9699919799.2016edition/)
* [POSIX ACL no Ubuntu](https://help.ubuntu.com/community/FilePermissionsACLs)
* [ACL usando listas de controle de acesso no Linux](https://bencane.com/2012/05/27/acl-using-access-control-lists-on-linux/)

## <a name="see-also"></a>Confira também

- [Modelo de controle de acesso no Azure Data Lake Storage Gen2](data-lake-storage-access-control-model.md)
