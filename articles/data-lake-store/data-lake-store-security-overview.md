---
title: Visão geral de segurança do Azure Data Lake Storage Gen1 | Microsoft Docs
description: Saiba mais sobre os recursos de segurança do Azure Data Lake Storage Gen1, incluindo autenticação, autorização, isolamento de rede, proteção de dados e auditoria.
services: data-lake-store
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: twooley
ms.openlocfilehash: 240018381a3139a6378141d78514e43ae469de5d
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/14/2020
ms.locfileid: "92146302"
---
# <a name="security-in-azure-data-lake-storage-gen1"></a>Segurança no Azure Data Lake Storage Gen1

Muitas empresas estão tirando vantagem da análise de big data para ter ideias de negócios e ajudá-los a tomar decisões inteligentes. Uma organização pode ter um ambiente regulamentado e complexo, com um número crescente de usuários diferentes. É fundamental para uma empresa ter certeza de que os dados essenciais aos negócios são armazenados com mais segurança, com o nível correto de acesso concedido a usuários individuais. O Azure Data Lake Storage Gen1 foi criado para ajudar a atender a esses requisitos de segurança. Neste artigo, saiba mais sobre os recursos de segurança do Azure Data Lake Storage Gen1, incluindo:

* Autenticação
* Autorização
* Isolamento da rede
* Proteção de dados
* Auditoria

## <a name="authentication-and-identity-management"></a>Autenticação e gerenciamento das identidades

Autenticação é o processo pelo qual a identidade do usuário é verificada quando ele interage com o Azure Data Lake Storage Gen1 ou com qualquer serviço que se conecte ao Data Lake Storage Gen1. Para o Gerenciamento das identidades e a autenticação, o Azure Data Lake Storage Gen1 usa o [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md), uma solução na nuvem do gerenciamento de identidades e acessos completo que simplifica o gerenciamento de usuários e grupos.

Toda assinatura do Azure pode ser associada a uma instância do Azure Active Directory. Somente os usuários e identidades de serviço que estão definidos no serviço Azure Active Directory podem acessar sua conta do Azure Data Lake Storage Gen1, usando o portal do Azure, as ferramentas de linha de comando, ou aplicativos de cliente criados pela sua organização usando o SDK do Azure Data Lake Storage Gen1 SDK. As principais vantagens de usar o Azure Active Directory como um mecanismo de controle de acesso centralizado são:

* Gerenciamento de ciclo de vida de identidade simplificado. A identidade de um usuário ou um serviço (uma entidade principal do serviço) pode ser criada rapidamente e revogada simplesmente excluindo ou desabilitando a conta no diretório.
* Multi-Factor Authentication. [Multi-Factor Authentication](../active-directory/authentication/concept-mfa-howitworks.md) fornece uma camada adicional de segurança para os logons e as transações dos usuários.
* A autenticação de clientes por meio de um protocolo aberto padrão, como OAuth ou OpenID.
* Federação com serviços de diretório da empresa e provedores de identidade da nuvem.

## <a name="authorization-and-access-control"></a>Autenticação e controle de acessos

Depois que o Microsoft Azure Active Directory autentica um usuário para que ele possa acessar o Azure Data Lake Storage Gen1, controles de autorização acessam permissões para o Azure Data Lake Storage Gen1. O Data Lake Storage Gen1 separa autorização das atividades relacionadas à conta e relacionadas a dados da seguinte forma:

* [Controle de acesso baseado em função do Azure (RBAC do Azure)](../role-based-access-control/overview.md) para gerenciamento de conta
* ACL POSIX para acessar dados no repositório

### <a name="azure-rbac-for-account-management"></a>RBAC do Azure para gerenciamento de conta

Quatro funções básicas são definidas para o Azure Data Lake Storage Gen1 por padrão. As funções permitem operações diferentes em uma conta do Azure Data Lake Storage Gen1 usando o portal do Azure, os cmdlets do PowerShell e as APIs REST. As funções Proprietário e Colaborador podem realizar várias funções de administração na conta. Você pode atribuir a função Leitor para os usuários que só visualizam os dados de gerenciamento da conta.

![Funções do Azure](./media/data-lake-store-security-overview/rbac-roles.png "Funções do Azure")

Observe que, embora as funções sejam atribuídas para gerenciamento de conta, algumas funções afetam o acesso aos dados. Você precisa usar ACLs para controlar o acesso a operações que um usuário pode executar no sistema de arquivos. A tabela a seguir mostra um resumo dos direitos de gerenciamento e dos direitos de acesso de dados para as funções padrão.

| Funções | Direitos de gerenciamento | Direitos de acesso a dados | Explicação |
| --- | --- | --- | --- |
| Nenhuma função atribuída |Nenhum |Controlado pela ACL |Os usuários não podem usar o portal do Azure ou os cmdlets do Azure PowerShell para percorrer o Azure Data Lake Storage Gen1. O usuário pode usar apenas as ferramentas de linha de comando. |
| Proprietário |Todos |Todos |A função Proprietário é um superusuário. Essa função pode gerenciar tudo e tem acesso completo aos dados. |
| Leitor |Somente leitura |Controlado pela ACL |A função Leitor pode ver tudo sobre o gerenciamento da conta, como qual usuário é atribuído a qual função. A função Leitor não pode fazer alterações. |
| Colaborador |Tudo, exceto adicionar e remover as funções. |Controlado pela ACL |A função Colaborador pode gerenciar alguns aspectos de uma conta, como implantações e a criação e o gerenciamento de alertas. Um Colaborador não pode adicionar ou remover funções. |
| Administrador de Acesso do Usuário |Adicionar e remover funções |Controlado pela ACL |A função Administrador de Acesso do Usuário permite gerenciar o acesso do usuário às contas. |

Para obter instruções, confira [Atribuir usuários ou grupos de segurança às contas do Azure Data Lake Storage Gen1](data-lake-store-secure-data.md#assign-users-or-security-groups-to-data-lake-storage-gen1-accounts).

### <a name="using-acls-for-operations-on-file-systems"></a>Usando ACLs para as operações nos sistemas de arquivos

O Azure Data Lake Storage Gen1 é um Sistema de Arquivos Distribuído como o HDFS (Hadoop Distributed File System) e dá suporte a [ACLs POSIX](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html#ACLs_Access_Control_Lists). Ele controla permissões de leitura (r), gravação (w) e execução (x) para recursos da função Proprietário, para o grupo Proprietários e para outros usuários e grupos. No Azure Data Lake Storage Gen1 (versão atual), as ACLs podem ser habilitadas na pasta raiz, nas subpastas e nos arquivos individuais. Para obter mais informações sobre como as ACLs funcionam no contexto do Data Lake Storage Gen1, consulte [Controle de acesso no Azure Data Lake Storage Gen1](data-lake-store-access-control.md).

Recomendamos que você defina as ACLs para vários usuários usando [grupos de segurança](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). Adicione usuários a um grupo de segurança e atribua as ACLs de um arquivo ou pasta ao grupo de segurança. Isso é útil quando você desejar fornecer permissões atribuídas, uma vez que estará limitado a um máximo de 28 entradas para permissões atribuídas. Para saber mais sobre como proteger melhor os dados armazenados no Data Lake Storage Gen1 usando grupos de segurança do Azure Active Directory, confira [Atribuir usuários ou grupo de segurança, como ACLs, ao sistema de arquivos do Azure Data Lake Storage Gen1](data-lake-store-secure-data.md#filepermissions).

![Listar permissões de acesso](./media/data-lake-store-security-overview/adl.acl.2.png "Listar permissões de acesso")

## <a name="network-isolation"></a>Isolamento da rede

Use o Azure Data Lake Storage Gen1 para ajudar a controlar o acesso ao seu armazenamento de dados no nível da rede. Você pode habilitar o firewall e definir um intervalo de endereços IP para seus clientes confiáveis. Com um intervalo de endereços IP, somente os clientes que possuem um endereço IP no intervalo definido podem se conectar ao Azure Data Lake Storage Gen1.

![Configurações do firewall e acesso IP](./media/data-lake-store-security-overview/firewall-ip-access.png "Configurações de firewall e endereço IP")

As marcas de serviço de suporte de VNet (redes virtuais) do Azure para Data Lake Gen 1. Uma marca de serviço representa um grupo de prefixos de endereço IP de um determinado serviço do Azure. A Microsoft gerencia os prefixos de endereço englobados pela marca de serviço e atualiza automaticamente a marca de serviço em caso de alteração de endereços. Para obter mais informações, consulte [visão geral das marcas de serviço do Azure](../virtual-network/service-tags-overview.md).

## <a name="data-protection"></a>Proteção de dados

O Azure Data Lake Storage Gen1 protege seus dados em todo o ciclo de vida. Para os dados em trânsito, o Azure Data Lake Storage Gen1 use o protocolo Transport Layer Security (TLS 1.2) padrão da indústria para proteger os dados na rede.

![Criptografia em Data Lake Storage Gen1](./media/data-lake-store-security-overview/adls-encryption.png "Criptografia em Data Lake Storage Gen1")

O Azure Data Lake Storage Gen1 também fornece criptografia para dados armazenados na conta. Você pode optar por ter seus dados criptografados ou optar por nenhuma criptografia. Se você optar por criptografia, os dados armazenados no Azure Data Lake Storage Gen1 são criptografados antes do armazenamento em mídia persistente. Nesse caso, o Azure Data Lake Storage Gen1 criptografa os dados automaticamente antes da persistência e descriptografa os dados antes da recuperação, por isso é completamente transparente para o cliente que acessa os dados. Não há nenhuma alteração de código necessária no lado do cliente para criptografar/descriptografar dados.

Em relação ao gerenciamento de chaves, o Data Lake Storage Gen1 fornece dois modos para gerenciar suas chaves-mestras de criptografia (MEKs), que são necessárias para descriptografar os dados armazenados no Azure Data Lake Storage Gen1. Você também pode deixar o Azure Data Lake Storage Gen1 gerenciar as MEKs para você ou optar por manter a propriedade das MEKs usando sua conta do Azure Key Vault. Você pode especificar o modo de gerenciamento de chaves ao criar uma conta do Azure Data Lake Storage Gen1. Para saber mais sobre como fornecer configuração relacionada à criptografia, veja [Introdução ao Azure Data Lake Storage Gen1 usando o portal do Azure](data-lake-store-get-started-portal.md).

## <a name="activity-and-diagnostic-logs"></a>Logs de atividade e diagnóstico

Você pode usar os logs de atividade ou de diagnóstico, dependendo se estar procurando logs para atividades relacionadas ao gerenciamento ou para atividades relacionadas aos dados.

* As atividades relacionadas ao gerenciamento de conta usam as APIs do Azure Resource Manager e são exibidas no Portal do Azure por meio dos logs de atividade.
* As atividades relacionadas aos dados usam APIs REST WebHDFS e são exibidas no portal do Azure por meio dos logs de diagnóstico.

### <a name="activity-log"></a>Log de atividades

Para cumprir as normas, uma organização poderá exigir trilhas de auditoria adequadas das atividades de gerenciamento da conta se precisar examinar incidentes específicos. O Azure Data Lake Storage Gen1 tem monitoramento interno e registra todas as atividades de gerenciamento da conta.

Para trilhas de auditoria de gerenciamento de conta, exiba e escolha as colunas que deseja registrar em log. Você também pode exportar os logs de atividades para o Armazenamento do Microsoft Azure.

![Log de atividades](./media/data-lake-store-security-overview/activity-logs.png "Log de atividades")

Para obter mais informações sobre como trabalhar com os logs, consulte [Exibir logs de atividades para auditar ações em recursos](../azure-resource-manager/management/view-activity-logs.md).

### <a name="diagnostics-logs"></a>Logs de diagnóstico

Você pode habilitar a auditoria de acesso a dados e o log de diagnóstico no portal do Azure e enviar os logs para uma conta de armazenamento de BLOBs do Azure, um hub de eventos ou logs de Azure Monitor.

![Logs de diagnóstico](./media/data-lake-store-security-overview/diagnostic-logs.png "Logs de diagnóstico")

Para saber mais sobre como trabalhar com logs de diagnóstico com o Azure Data Lake Storage Gen1, consulte [Acessar logs de diagnóstico para o Azure Data Lake Storage Gen1](data-lake-store-diagnostic-logs.md).

## <a name="summary"></a>Resumo

Os clientes corporativos exigem uma plataforma de nuvem da análise de dados que seja segura e fácil de usar. O Azure Data Lake Store Gen1 é projetado para ajudar a endereçar esses requisitos com o gerenciamento da identidade e a autenticação usando a integração do Azure Active Directory, as autorizações baseadas em ACL, o isolamento de rede, a criptografia do dados em trânsito e em repouso e a auditoria.

Se você quer ver os novos recursos incluídos no Azure Data Lake Storage Gen1, envie seus comentários para o [Fórum UserVoice do Azure Data Lake Storage Gen1](https://feedback.azure.com/forums/327234-data-lake).

## <a name="see-also"></a>Consulte também

* [Visão Geral do Azure Data Lake Storage Gen1](data-lake-store-overview.md)
* [Introdução ao Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md)
* [Proteger dados no Armazenamento do Data Lake Gen1](data-lake-store-secure-data.md)