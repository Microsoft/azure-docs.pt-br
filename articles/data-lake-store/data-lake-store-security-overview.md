---
title: "Visão geral da segurança no Data Lake Store | Microsoft Docs"
description: "Entender como o Azure Data Lake Store é um repositório de big data seguro"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: ebd5b2ac-c5cc-46d4-9cfd-1a1ee70024c2
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/28/2017
ms.author: nitinme
ms.openlocfilehash: e72dd7e84ce3961274cf312649cc679abc576aae
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="security-in-azure-data-lake-store"></a>Segurança no Armazenamento do Azure Data Lake
Muitas empresas estão tirando vantagem da análise de big data para ter ideias de negócios e ajudá-los a tomar decisões inteligentes. Uma organização pode ter um ambiente regulamentado e complexo, com um número crescente de usuários diferentes. É fundamental para uma empresa ter certeza de que os dados essenciais aos negócios são armazenados com mais segurança, com o nível correto de acesso concedido a usuários individuais. O Azure Data Lake Store foi criado para ajudar a atender a esses requisitos de segurança. Neste artigo, saiba mais sobre os recursos de segurança do Data Lake Store, incluindo:

* Autenticação
* Autorização
* Isolamento da rede
* Proteção de dados
* Auditoria

## <a name="authentication-and-identity-management"></a>Autenticação e gerenciamento das identidades
Autenticação é o processo pelo qual a identidade do usuário é verificada quando ele interage com o Data Lake Store ou com qualquer serviço que se conecte ao Data Lake Store. Para o Gerenciamento das identidades e a autenticação, o Data Lake Store usa o [Azure Active Directory](../active-directory/active-directory-whatis.md), uma solução na nuvem do gerenciamento de identidades e acessos completo que simplifica o gerenciamento de usuários e grupos.

Toda assinatura do Azure pode ser associada a uma instância do Azure Active Directory. Somente os usuários e identidades de serviço que estão definidos no serviço Azure Active Directory podem acessar sua conta do Data Lake Store, usando o portal do Azure, as ferramentas de linha de comando, ou aplicativos de cliente criados pela sua organização usando o SDK do Azure Data Lake Store. As principais vantagens de usar o Azure Active Directory como um mecanismo de controle de acesso centralizado são:

* Gerenciamento de ciclo de vida de identidade simplificado. A identidade de um usuário ou um serviço (uma entidade principal do serviço) pode ser criada rapidamente e revogada simplesmente excluindo ou desabilitando a conta no diretório.
* Multi-Factor Authentication. [Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md) fornece uma camada adicional de segurança para os logons e as transações dos usuários.
* A autenticação de clientes por meio de um protocolo aberto padrão, como OAuth ou OpenID.
* Federação com serviços de diretório da empresa e provedores de identidade da nuvem.

## <a name="authorization-and-access-control"></a>Autenticação e controle de acessos
Depois que o Azure Active Directory autentica um usuário para que ele possa acessar o Azure Data Lake Store, controles de autorização acessam permissões para o Data Lake Store. O Data Lake Store separa autorização das atividades relacionadas à conta e relacionadas a dados da seguinte forma:

* [Controle de acesso baseado em função](../active-directory/role-based-access-control-what-is.md) ) fornecido pelo Azure para gerenciamento de contas
* ACL POSIX para acessar dados no repositório

### <a name="rbac-for-account-management"></a>RBAC para o gerenciamento da conta
Quatro funções básicas são definidas para o Data Lake Store por padrão. As funções permitem operações diferentes em uma conta do Data Lake Store usando o portal do Azure, os cmdlets do PowerShell e as APIs REST. As funções Proprietário e Colaborador podem realizar várias funções de administração na conta. Você pode atribuir a função Leitor para os usuários que só interagem com dados.

![Funções RBAC](./media/data-lake-store-security-overview/rbac-roles.png "Funções RBAC")

Observe que, embora as funções sejam atribuídas para gerenciamento de conta, algumas funções afetam o acesso aos dados. Você precisa usar ACLs para controlar o acesso a operações que um usuário pode executar no sistema de arquivos. A tabela a seguir mostra um resumo dos direitos de gerenciamento e dos direitos de acesso de dados para as funções padrão.

| Funções | Direitos de gerenciamento | Direitos de acesso a dados | Explicação |
| --- | --- | --- | --- |
| Nenhuma função atribuída |Nenhum |Controlado pela ACL |Os usuários não podem usar o portal do Azure ou os cmdlets do Azure PowerShell para percorrer o Data Lake Store. O usuário pode usar apenas as ferramentas de linha de comando. |
| Proprietário |Todos |Todos |A função Proprietário é um superusuário. Essa função pode gerenciar tudo e tem acesso completo aos dados. |
| Leitor |Somente leitura |Controlado pela ACL |A função Leitor pode ver tudo sobre o gerenciamento da conta, como qual usuário é atribuído a qual função. A função Leitor não pode fazer alterações. |
| Colaborador |Tudo, exceto adicionar e remover as funções. |Controlado pela ACL |A função Colaborador pode gerenciar alguns aspectos de uma conta, como implantações e a criação e o gerenciamento de alertas. Um Colaborador não pode adicionar ou remover funções. |
| Administrador de Acesso do Usuário |Adicionar e remover funções |Controlado pela ACL |A função Administrador de Acesso do Usuário permite gerenciar o acesso do usuário às contas. |

Para obter instruções, confira [Atribuir usuários ou grupos de segurança às contas do Azure Data Lake Store](data-lake-store-secure-data.md#assign-users-or-security-groups-to-azure-data-lake-store-accounts).

### <a name="using-acls-for-operations-on-file-systems"></a>Usando ACLs para as operações nos sistemas de arquivos
O Data Lake Store é um sistema de arquivos hierárquico, como o HDFS (Hadoop Distributed File System) e dá suporte a [ACLs POSIX](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html#ACLs_Access_Control_Lists). Ele controla permissões de leitura (r), gravação (w) e execução (x) para recursos da função Proprietário, para o grupo Proprietários e para outros usuários e grupos. Na Visualização Pública do Data Lake Store (versão atual), as ACLs podem ser habilitadas na pasta raiz, nas subpastas e nos arquivos individuais. Para saber mais sobre como funcionam as ACLs no contexto do Data Lake Store, veja [Controle de acesso no Data Lake ](data-lake-store-access-control.md).

Recomendamos que você defina as ACLs para vários usuários usando [grupos de segurança](../active-directory/active-directory-accessmanagement-manage-groups.md). Adicione usuários a um grupo de segurança e atribua as ACLs de um arquivo ou pasta ao grupo de segurança. Isso é útil quando você deseja fornecer acesso personalizado, já que você está limitado à adição de no máximo nove entradas de acesso personalizado. Para saber mais sobre como proteger melhor os dados armazenados no Data Lake Store usando grupos de segurança do Azure Active Directory, confira [Atribuir usuários ou grupo de segurança, como ACLs, ao sistema de arquivos do Azure Data Lake Store](data-lake-store-secure-data.md#filepermissions).

![Lista de acesso padrão e personalizado](./media/data-lake-store-security-overview/adl.acl.2.png "lista de acesso padrão e personalizado")

## <a name="network-isolation"></a>Isolamento da rede
Use o Data Lake Store para ajudar a controlar o acesso ao seu armazenamento de dados no nível da rede. Você pode habilitar o firewall e definir um intervalo de endereços IP para seus clientes confiáveis. Com um intervalo de endereços IP, somente os clientes que possuem um endereço IP no intervalo definido podem se conectar ao Data Lake Store.

![Configurações de firewall e acesso IP](./media/data-lake-store-security-overview/firewall-ip-access.png "Endereço IP e configurações de firewall")

## <a name="data-protection"></a>Proteção de dados
O Azure Data Lake Store protege seus dados em todo o ciclo de vida. Para os dados em trânsito, o Data Lake Store usa o protocolo TLS (Transport Layer Security) padrão da indústria para proteger os dados na rede.

![Criptografia no Data Lake Store](./media/data-lake-store-security-overview/adls-encryption.png "Criptografia no Data Lake Store")

O Data Lake Store também fornece criptografia para os dados armazenados na conta. Você pode optar por ter seus dados criptografados ou optar por nenhuma criptografia. Se você optar por criptografia, os dados armazenados no Data Lake Store são criptografados antes do armazenamento em mídia persistente. Nesse caso, o Data Lake Store criptografa os dados automaticamente antes da persistência e descriptografa os dados antes da recuperação, por isso é completamente transparente para o cliente que acessa os dados. Não há nenhuma alteração de código necessária no lado do cliente para criptografar/descriptografar dados.

Em relação ao gerenciamento de chaves, o Data Lake Store fornece dois modos para gerenciar suas chaves-mestras de criptografia (MEKs), que são necessárias para descriptografar os dados armazenados no Data Lake Store. Você também pode deixar o Data Lake Store gerenciar as MEKs para você ou optar por manter a propriedade das MEKs usando sua conta do Cofre de Chaves do Azure. Você pode especificar o modo de gerenciamento de chaves ao criar uma conta do Data Lake Store. Para saber mais sobre como fornecer configuração relacionada à criptografia, veja [Introdução ao Azure Data Lake Store usando o Portal do Azure](data-lake-store-get-started-portal.md).

## <a name="auditing-and-diagnostic-logs"></a>Logs de auditoria e diagnóstico
Você pode usar os logs de auditoria ou de diagnóstico, dependendo de estar procurando logs para atividades relacionadas ao gerenciamento ou para atividades relacionadas aos dados.

* As atividades relacionadas ao gerenciamento usam as APIs do Azure Resource Manager e são exibidas no portal do Azure por meio dos logs de auditoria.
* As atividades relacionadas aos dados usam APIs REST WebHDFS e são exibidas no portal do Azure por meio dos logs de diagnóstico.

### <a name="auditing-logs"></a>Logs de auditoria
Para cumprir as normas, uma organização poderá exigir trilhas de auditoria adequadas se precisar examinar incidentes específicos. O Data Lake Store tem auditoria e monitoramento internos e registra todas as atividades de gerenciamento da conta.

Para trilhas de auditoria de gerenciamento de conta, exiba e escolha as colunas que deseja registrar em log. Você também pode exportar os logs de auditoria para o Armazenamento do Azure.

![Logs de auditoria](./media/data-lake-store-security-overview/audit-logs.png "Logs de auditoria")

### <a name="diagnostic-logs"></a>Logs de diagnóstico
Você pode definir as trilhas de auditoria de acesso aos dados no portal do Azure (em Configurações de Diagnóstico) e criar uma conta de armazenamento de blobs do Azure onde os logs serão armazenados.

![Logs de diagnóstico](./media/data-lake-store-security-overview/diagnostic-logs.png "Logs de diagnóstico")

Depois de configurar as definições de diagnóstico, você pode exibir os logs na guia **Logs de Diagnóstico** .

Para saber mais sobre como trabalhar com logs de diagnóstico com o Azure Data Lake Store, consulte [Acessar logs de diagnóstico para o Data Lake Store](data-lake-store-diagnostic-logs.md).

## <a name="summary"></a>Resumo
Os clientes corporativos exigem uma plataforma de nuvem da análise de dados que seja segura e fácil de usar. O Azure Data Lake Store é projetado para ajudar a endereçar esses requisitos com o gerenciamento da identidade e a autenticação usando a integração do Azure Active Directory, as autorizações baseadas em ACL, o isolamento de rede, a criptografia do dados em trânsito e em repouso (no futuro) e a auditoria.

Se você quer ver os novos recursos incluídos no Data Lake Store, envie seus comentários para o [Fórum Uservoice do Data Lake Store](https://feedback.azure.com/forums/327234-data-lake).

## <a name="see-also"></a>Confira também
* [Visão geral do repositório Azure Data Lake](data-lake-store-overview.md)
* [Introdução ao Data Lake Store](data-lake-store-get-started-portal.md)
* [Proteger dados no Repositório Data Lake](data-lake-store-secure-data.md)

