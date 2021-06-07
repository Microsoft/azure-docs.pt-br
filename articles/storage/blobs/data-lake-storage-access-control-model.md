---
title: Modelo de controle de acesso para Azure Data Lake Storage Gen2 | Microsoft Docs
description: Saiba como configurar o acesso em nível de contêiner, diretório e arquivo em contas que têm um namespace hierárquico.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: normesta
ms.openlocfilehash: 9fa7f586db5a32640c16db5802b56d6481e8889d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102439282"
---
# <a name="access-control-model-in-azure-data-lake-storage-gen2"></a>Modelo de controle de acesso no Azure Data Lake Storage Gen2

O Data Lake Storage Gen2 dá suporte aos seguintes mecanismos de autorização:

- Autorização de chave compartilhada
- Autorização de assinatura de acesso compartilhado (SAS)
- Controle de acesso baseado em função (RBAC do Azure)
- ACL (listas de controle de acesso)

A [autorização de chave compartilhada e SAS](#shared-key-and-shared-access-signature-sas-authorization) concede acesso a um usuário (ou aplicativo) sem exigir que eles tenham uma identidade no Azure Active Directory (AD do Azure). Com essas duas formas de autenticação, o RBAC do Azure e as ACLs não têm nenhum efeito.

O RBAC e a ACL do Azure exigem que o usuário (ou aplicativo) tenha uma identidade no Azure AD.  O RBAC do Azure permite que você conceda acesso "granular" aos dados da conta de armazenamento, como acesso de leitura ou gravação a **todos** os dados em uma conta de armazenamento, enquanto as ACLs permitem conceder acesso "refinado", como acesso de gravação a um diretório ou arquivo específico.  

Este artigo se concentra no RBAC do Azure e nas ACLs, e como o sistema os avalia em conjunto para tomar decisões de autorização para recursos de conta de armazenamento.

<a id="role-based-access-control"></a>

## <a name="role-based-access-control-azure-rbac"></a>Controle de acesso baseado em função (RBAC do Azure)

O RBAC do Azure usa atribuições de função para aplicar conjuntos de permissões a [entidades de segurança](../../role-based-access-control/overview.md#security-principal). Uma entidade de segurança é um objeto que representa um usuário, grupo, entidade de serviço ou identidade gerenciada que é definida no Azure Active Directory (AD). Um conjunto de permissões pode dar a uma entidade de segurança um nível de acesso "de alta granularidade", como acesso de leitura ou gravação a **todos** os dados em uma conta de armazenamento ou a **todos** os dados em um contêiner. 

As funções a seguir permitem que uma entidade de segurança acesse dados em uma conta de armazenamento.

|Função|Descrição|
|--|--|
| [Proprietário de Dados do Blob de Armazenamento](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) | Acesso completo a dados e contêineres de armazenamento de BLOBs. Esse acesso permite que a entidade de segurança defina um item do proprietário e modifique as ACLs de todos os itens. |
| [Colaborador de dados de blob de armazenamento](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) | Ler, gravar e excluir o acesso a BLOBs e contêineres de armazenamento de BLOBs. Esse acesso não permite que a entidade de segurança defina a propriedade de um item, mas pode modificar a ACL de itens que pertencem à entidade de segurança. |
| [Leitor de Dados do Blob de Armazenamento](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader) | Ler e listar contêineres e blobs de armazenamento de BLOBs. |

Funções como [proprietário](../../role-based-access-control/built-in-roles.md#owner), [colaborador](../../role-based-access-control/built-in-roles.md#contributor), [leitor](../../role-based-access-control/built-in-roles.md#reader)e colaborador de [conta de armazenamento](../../role-based-access-control/built-in-roles.md#storage-account-contributor) permitem que uma entidade de segurança gerencie uma conta de armazenamento, mas não forneça acesso aos dados dentro dessa conta. No entanto, essas funções (exceto o **leitor**) podem obter acesso às chaves de armazenamento, que podem ser usadas em várias ferramentas de cliente para acessar os dados.

## <a name="access-control-lists-acls"></a>ACLs (listas de controle de acesso)

As ACLs oferecem a capacidade de aplicar o nível de acesso "mais detalhado" a diretórios e arquivos. Uma *ACL* é uma construção de permissão que contém uma série de *entradas de ACL*. Cada entrada de ACL associa a entidade de segurança a um nível de acesso.  Para saber mais, confira [ACLs (listas de controle de acesso) no Azure data Lake Storage Gen2](data-lake-storage-access-control.md).

## <a name="how-permissions-are-evaluated"></a>Como as permissões são avaliadas

Durante a autorização baseada em entidade de segurança, as permissões são avaliadas na seguinte ordem.

: uma: &nbsp; &nbsp; as atribuições de função do Azure são avaliadas primeiro e têm prioridade sobre quaisquer atribuições de ACL.

: dois: &nbsp; &nbsp; se a operação for totalmente autorizada com base na atribuição de função do Azure, as ACLs não serão avaliadas.

: três: &nbsp; &nbsp; se a operação não for totalmente autorizada, as ACLs serão avaliadas.

> [!div class="mx-imgBorder"]
> ![fluxo de permissão de armazenamento do data Lake](./media/control-access-permissions-data-lake-storage/data-lake-storage-permissions-flow.png)

Devido à maneira como as permissões de acesso são avaliadas pelo sistema, você **não pode** usar uma ACL para **restringir** o acesso que já foi concedido por uma atribuição de função. Isso ocorre porque o sistema avalia primeiro as atribuições de função do Azure e, se a atribuição conceder permissão de acesso suficiente, as ACLs serão ignoradas. 

O diagrama a seguir mostra o fluxo de permissão para três operações comuns: listar o conteúdo do diretório, ler um arquivo e gravar um arquivo.

> [!div class="mx-imgBorder"]
> ![exemplo de fluxo de permissão de armazenamento do data Lake](./media/control-access-permissions-data-lake-storage/data-lake-storage-permissions-example.png)

## <a name="permissions-table-combining-azure-rbac-and-acl"></a>Tabela de permissões: combinando o RBAC do Azure e a ACL

A tabela a seguir mostra como combinar funções do Azure e entradas de ACL para que uma entidade de segurança possa executar as operações listadas na coluna **operação** . Esta tabela mostra uma coluna que representa cada nível de uma hierarquia de diretório fictícia. Há uma coluna para o diretório raiz do contêiner ( `/` ), um subdiretório chamado **Oregon**, um subdiretório do diretório Oregon chamado **Portland** e um arquivo de texto no diretório de Portland chamado **Data.txt**. A exibição dessas colunas é uma pequena representação de [forma abreviada](data-lake-storage-access-control.md#short-forms-for-permissions) da entrada ACL necessária para conceder permissões. **N/A** (_não aplicável_) aparece na coluna se uma entrada de ACL não for necessária para executar a operação.

|    Operação             | Função do Azure atribuída               |    /        | Oregon/     | Portland/ | Data.txt |             
|--------------------------|----------------------------------|-------------|-------------|-----------|----------|
| Read Data.txt            |   Proprietário de Dados do Blob de Armazenamento        | N/D      | N/D      | N/D       | N/D    |  
|                          |   Colaborador de dados de blob de armazenamento  | N/D      | N/D      | N/D       | N/D    |
|                          |   Leitor de Dados do Blob de Armazenamento       | N/D      | N/D      | N/D       | N/D    |
|                          |   Nenhum                           | `--X`    | `--X`    | `--X`     | `R--`  |
| Append to Data.txt       |   Proprietário de Dados do Blob de Armazenamento        | N/D      | N/D      | N/D       | N/D    |
|                          |   Colaborador de dados de blob de armazenamento  | N/D      | N/D      | N/D       | N/D    |
|                          |   Leitor de Dados do Blob de Armazenamento       | `--X`    | `--X`    | `--X`     | `-W-`  |
|                          |   Nenhum                           | `--X`    | `--X`    | `--X`     | `RW-`  |
| Delete Data.txt          |   Proprietário de Dados do Blob de Armazenamento        | N/D      | N/D      | N/D       | N/D    |
|                          |   Colaborador de dados de blob de armazenamento  | N/D      | N/D      | N/D       | N/D    |
|                          |   Leitor de Dados do Blob de Armazenamento       | `--X`    | `--X`    | `-WX`     | N/D    |
|                          |   Nenhum                           | `--X`    | `--X`    | `-WX`     | N/D    |
| Create Data.txt          |   Proprietário de Dados do Blob de Armazenamento        | N/D      | N/D      | N/D       | N/D    |
|                          |   Colaborador de dados de blob de armazenamento  | N/D      | N/D      | N/D       | N/D    |
|                          |   Leitor de Dados do Blob de Armazenamento       | `--X`    | `--X`    | `-WX`     | N/D    |
|                          |   Nenhum                           | `--X`    | `--X`    | `-WX`     | N/D    |
| List /                   |   Proprietário de Dados do Blob de Armazenamento        | N/D      | N/D      | N/D       | N/D    |
|                          |   Colaborador de dados de blob de armazenamento  | N/D      | N/D      | N/D       | N/D    |
|                          |   Leitor de Dados do Blob de Armazenamento       | N/D      | N/D      | N/D       | N/D    |
|                          |   Nenhum                           | `R-X`    | N/D      | N/D       | N/D    |
| Lista /Oregon/            |   Proprietário de Dados do Blob de Armazenamento        | N/D      | N/D      | N/D       | N/D    |
|                          |   Colaborador de dados de blob de armazenamento  | N/D      | N/D      | N/D       | N/D    |
|                          |   Leitor de Dados do Blob de Armazenamento       | N/D      | N/D      | N/D       | N/D    |
|                          |   Nenhum                           | `--X`    | `R-X`    | N/D       | N/D    |
| Lista /Oregon/Portland/   |   Proprietário de Dados do Blob de Armazenamento        | N/D      | N/D      | N/D       | N/D    |
|                          |   Colaborador de dados de blob de armazenamento  | N/D      | N/D      | N/D       | N/D    |
|                          |   Leitor de Dados do Blob de Armazenamento       | N/D      | N/D      | N/D       | N/D    |
|                          |   Nenhum                           | `--X`    | `--X`    | `R-X`     | N/D    |


> [!NOTE] 
> Para exibir o conteúdo de um contêiner em Gerenciador de Armazenamento do Azure, as entidades de segurança devem [entrar no Gerenciador de armazenamento usando o Azure ad](../../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows#attach-to-an-individual-resource)e (no mínimo) ter acesso de leitura (R--) para a pasta raiz ( `\` ) de um contêiner. Esse nível de permissão fornece a eles a capacidade de listar o conteúdo da pasta raiz. Se não quiser que o conteúdo da pasta raiz fique visível, você poderá atribuí-los à função de [leitor](../../role-based-access-control/built-in-roles.md#reader) . Com essa função, eles poderão listar os contêineres na conta, mas não o conteúdo do contêiner. Você pode conceder acesso a diretórios e arquivos específicos usando ACLs.   

## <a name="security-groups"></a>Grupos de segurança

[!INCLUDE [Security groups](../../../includes/azure-storage-data-lake-groups.md)]

## <a name="limits-on-azure-role-assignments-and-acl-entries"></a>Limites em atribuições de função do Azure e entradas de ACL

Usando grupos, você tem menor probabilidade de exceder o número máximo de atribuições de função por assinatura e o número máximo de entradas de ACL por arquivo ou diretório. A tabela a seguir descreve esses limites.

[!INCLUDE [Security groups](../../../includes/azure-storage-data-lake-rbac-acl-limits.md)] 

## <a name="shared-key-and-shared-access-signature-sas-authorization"></a>Autorização de chave compartilhada e assinatura de acesso compartilhado (SAS)

O Azure Data Lake Storage Gen2 também dá suporte a métodos de [chave compartilhada](/rest/api/storageservices/authorize-with-shared-key) e [SAS](../common/storage-sas-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) para autenticação. Uma característica desses métodos de autenticação é que nenhuma identidade está associada ao chamador e, portanto, a autorização baseada em permissão de entidade de segurança não pode ser executada.

No caso da chave compartilhada, o chamador obtém efetivamente o acesso de "superusuário", o que significa acesso completo a todas as operações em todos os recursos, incluindo dados, configuração de proprietário e alteração de ACLs.

Os tokens SAS incluem permissões permitidas como parte do token. As permissões incluídas no token de SAS com eficiência são aplicadas a todas as decisões de autorização, mas nenhuma verificação ACL adicional é realizada.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre as listas de controle de acesso, consulte  [listas de controle de acesso (ACLs) em Azure data Lake Storage Gen2](data-lake-storage-access-control.md).