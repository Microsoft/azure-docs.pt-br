---
title: Pool de SQL sem servidor
description: Saiba mais sobre o pool de SQL sem servidor no Azure Synapse Analytics.
services: synapse analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: e1a0f4f22411e506cab3b54e955a4cdc16986fe2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101667500"
---
# <a name="serverless-sql-pool-in-azure-synapse-analytics"></a>Pool de SQL sem servidor no Azure Synapse Analytics 

Todo workspace do Azure Synapse Analytics é fornecido com pontos de extremidade do pool de SQL sem servidor que você pode usar para consultar dados no lake.

O pool de SQL sem servidor é um serviço de consulta dos dados no data lake. Ele permite que você acesse os dados por meio das seguintes funcionalidades:
 
- Uma sintaxe de T-SQL familiar para consultar dados no local sem a necessidade de copiar nem carregar dados em um repositório especializado. 
- Conectividade integrada por meio da interface T-SQL que oferece uma ampla variedade de ferramentas de consulta ad hoc e de business intelligence, incluindo os drivers mais populares. 

O pool de SQL sem servidor é um sistema de processamento de dados distribuído, criado para funções computacionais e de dados em grande escala. O pool de SQL sem servidor permite que você analise Big Data em segundos ou minutos, dependendo da carga de trabalho. Graças à tolerância interna a falhas de execução de consulta, o sistema oferece confiabilidade e taxas de êxito altas, até mesmo para consultas de longa execução envolvendo grandes conjuntos de dados.

O pool de SQL sem servidor é sem servidor, portanto, não há infraestrutura a ser instalada nem clusters a serem mantidos. Um ponto de extremidade padrão para esse serviço é fornecido em todos os workspaces do Azure Synapse, de modo que você pode iniciar a consulta de dados assim que o workspace é criado. 

Não há nenhum encargo referente a recursos reservados, você é cobrado apenas pelos dados processados pelas consultas que executa, portanto, esse é verdadeiramente um modelo de pagamento conforme o uso.  

Se você usar o Apache Spark para o Azure Synapse no seu pipeline de dados para preparação, limpeza ou enriquecimento de dados, poderá [consultar as tabelas externas do Spark](develop-storage-files-spark-tables.md) que criou no processo diretamente do pool de SQL sem servidor. Use o [Link Privado](../security/how-to-connect-to-workspace-with-private-links.md) para trazer o ponto de extremidade do pool de SQL sem servidor para dentro da [VNet do workspace gerenciado](../security/synapse-workspace-managed-vnet.md).  

## <a name="serverless-sql-pool-benefits"></a>Benefícios do pool de SQL sem servidor

Se você precisar explorar dados no data lake, obter insights desses dados ou otimizar o pipeline de transformação de dados existente, poderá se beneficiar do uso do pool de SQL sem servidor. Ele é adequado para os seguintes cenários:

- Descoberta básica e exploração – tire conclusões rapidamente sobre os dados em vários formatos (Parquet, CSV, JSON) no data lake, permitindo que você planeje a melhor maneira de extrair insights deles.
- Data warehouse lógico – forneça uma abstração relacional sobre dados brutos ou díspares sem realocá-los nem transformá-los, permitindo a exibição sempre atualizada dos dados.
- Transformação de dados – maneira simples, escalonável e de alto desempenho para transformar dados no Lake usando o T-SQL, de modo que ele pode ser alimentado para BI e outras ferramentas ou então carregado em um armazenamento de dados relacional (bancos de dados SQL do Synapse, Banco de Dados SQL do Azure etc).

Diferentes funções profissionais podem se beneficiar do pool de SQL sem servidor:

- Os engenheiros de dados podem explorar o lake, transformar e preparar dados usando esse serviço e simplificar os próprios pipelines de transformação de dados. Para obter mais informações, confira este [tutorial](tutorial-data-analyst.md).
- Os cientistas de dados podem tirar conclusões rapidamente com base no conteúdo e na estrutura dos dados no lake, graças a recursos como o OPENROWSET e a inferência automática de esquemas.
- Os Analistas de Dados podem [explorar dados e tabelas externas do Spark](develop-storage-files-spark-tables.md) criadas por Cientistas de Dados ou Engenheiros de Dados usando uma linguagem T-SQL familiar ou as ferramentas favoritas deles, que podem se conectar ao pool de SQL sem servidor.
- Os profissionais de BI podem rapidamente [criar relatórios do Power BI com base nos dados no lake](tutorial-connect-power-bi-desktop.md) e em tabelas do Spark.

## <a name="how-to-start-using-serverless-sql-pool"></a>Como começar a usar o pool de SQL sem servidor

O ponto de extremidade do pool de SQL sem servidor é fornecido em todos os workspaces do Azure Synapse. Você pode criar um workspace e começar a consultar dados instantaneamente usando ferramentas com as quais você está familiarizado.

## <a name="client-tools"></a>Ferramentas de cliente

O pool de SQL sem servidor permite que as ferramentas existentes de consulta ad hoc e de business intelligence do SQL aproveitem o data lake. Já que ele fornece uma sintaxe de T-SQL familiar, qualquer ferramenta capaz de estabelecer ofertas de conexão TDS pode [se conectar ao SQL do Synapse e consultá-lo](connect-overview.md) sob demanda. Você pode se conectar com Azure Data Studio e executar consultas ad hoc ou conectar-se com Power BI para obter insights em questão de minutos.

## <a name="t-sql-support"></a>Suporte para T-SQL

O pool de SQL sem servidor oferece uma área da superfície de consulta T-SQL que é ligeiramente aprimorada/estendida em alguns aspectos para permitir experiências relacionadas à consulta de dados semiestruturados e não estruturados. Além disso, alguns aspectos da linguagem T-SQL não têm suporte devido ao design do pool de SQL sem servidor. Por exemplo, atualmente, a funcionalidade DML não é compatível.

- A carga de trabalho pode ser organizada usando conceitos familiares:
- Bancos de dados: o ponto de extremidade do pool de SQL sem servidor pode ter vários bancos de dados.
- Esquemas: dentro de um banco de dados, pode haver um ou vários grupos de propriedade de objeto denominados esquemas.
- Exibições
- Recursos externos: fontes de dados, formatos de arquivo e tabelas

A segurança pode ser imposta usando:

- Logons e usuários
- Credenciais para controlar o acesso a contas de armazenamento
- Concessão, negação e revogação de permissões por nível de objeto
- Integração do Microsoft Azure Active Directory

T-SQL compatível:

- A área de superfície de [SELECT](/sql/t-sql/queries/select-transact-sql?view=azure-sqldw-latest&preserve-view=true) completa é compatível, incluindo a maioria das funções SQL
- CETAS – CREATE EXTERNAL TABLE AS SELECT
- Instruções DDL relacionadas somente a exibições e segurança

O pool de SQL sem servidor não tem armazenamento local, somente objetos de metadados são armazenados em bancos de dados. Portanto, o T-SQL relacionado aos seguintes conceitos não tem suporte:

- Tabelas
- Gatilhos
- Exibições materializadas
- Instruções DDL diferentes daquelas relacionadas a exibições e segurança
- Instruções DML

### <a name="extensions"></a>Extensões

Para permitir uma experiência tranquila para a consulta no local de dados que residem em arquivos no data lake, o pool de SQL sem servidor estende a função [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql?view=azure-sqldw-latest&preserve-view=true) existente adicionando as seguintes funcionalidades:

[Consultar vários arquivos ou pastas](query-data-storage.md#query-multiple-files-or-folders)

[Formato de arquivo PARQUET](query-data-storage.md#query-parquet-files)

[Opções adicionais para trabalhar com texto delimitado (terminador de campo, terminador de linha, caractere de escape)](query-data-storage.md#query-csv-files)

[Ler um subconjunto escolhido de colunas](query-data-storage.md#read-a-chosen-subset-of-columns)

[Inferência de esquema](query-data-storage.md#schema-inference)

[Função filename](query-data-storage.md#filename-function)

[Função filepath](query-data-storage.md#filepath-function)

[Trabalhar com tipos complexos e estruturas de dados aninhadas ou repetidas](query-data-storage.md#work-with-complex-types-and-nested-or-repeated-data-structures)

## <a name="security"></a>Segurança

O pool de SQL sem servidor oferece mecanismos para proteger o acesso aos seus dados.

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Integração do Azure Active Directory e autenticação multifator

O pool de SQL sem servidor permite gerenciar centralmente as identidades de usuário do banco de dados e de outros serviços da Microsoft com a [integração do Azure Active Directory](../../azure-sql/database/authentication-aad-configure.md). Esse recurso simplifica o gerenciamento de permissão e aprimora a segurança. O Azure AD (Azure Active Directory) é compatível com [MFA](../../azure-sql/database/authentication-mfa-ssms-configure.md) (autenticação multifator) para aumentar a segurança de aplicativos e dados e dá suporte a um processo de logon único.

#### <a name="authentication"></a>Autenticação

A autenticação do pool de SQL sem servidor refere-se a como os usuários comprovam a identidade ao conectarem-se ao ponto de extremidade. Os tipos de autenticação abaixo são compatíveis:

- **Autenticação do SQL**

  Este método de autenticação usa um nome de usuário e senha.

- **Autenticação do Active Directory do Azure**:

  Esse método de autenticação usa identidades gerenciadas pelo Azure Active Directory. Para usuários do Azure AD, a autenticação multifator pode ser habilitada. Use autenticação do Active Directory (segurança integrada) [sempre que possível](/sql/relational-databases/security/choose-an-authentication-mode?view=azure-sqldw-latest&preserve-view=true).

#### <a name="authorization"></a>Autorização

Autorização refere-se ao que um usuário pode fazer em um banco de dados do pool de SQL sem servidor e ela é controlada pelas associações a uma função de banco de dados e pelas permissões no nível de objeto da conta de usuário.

Se a autenticação do SQL for usada, o usuário do SQL existirá somente no pool de SQL sem servidor e o escopo das permissões será delimitado aos objetos no pool de SQL sem servidor. O acesso a objetos protegíveis em outros serviços (como o Armazenamento do Azure) não pode ser concedido ao usuário do SQL diretamente, pois ele só existe no escopo do pool de SQL sem servidor. O usuário do SQL precisa usar um dos [tipos de autorização compatíveis](develop-storage-files-storage-access-control.md#supported-storage-authorization-types) para acessar os arquivos.

Se a autenticação do Azure AD for usada, um usuário poderá entrar no pool de SQL sem servidor e em outros serviços, como o Armazenamento do Azure, e poderá conceder permissões ao usuário do Azure AD.

### <a name="access-to-storage-accounts"></a>Acesso às contas de armazenamento

Um usuário que fez logon no serviço de pool de SQL sem servidor precisa estar autorizado a acessar e consultar os arquivos no Armazenamento do Azure. O pool de SQL sem servidor dá suporte aos seguintes tipos de autorização:

- A **SAS (Assinatura de Acesso Compartilhado)** fornece acesso delegado aos recursos em uma conta de armazenamento. Com uma SAS, você pode conceder aos clientes acesso aos recursos na conta de armazenamento, sem compartilhar as chaves de conta. Uma SAS oferece controle granular sobre o tipo de acesso que você concede aos clientes que têm uma SAS: intervalo de validade, permissões concedidas, intervalo de endereços IP aceitáveis e protocolo aceitável (HTTPS/HTTP).

- A **Identidade do Usuário**, também conhecida como "passagem", é um tipo de autorização em que a identidade do usuário do Azure AD que fez logon no pool de SQL sem servidor é usada para autorizar o acesso aos dados. Antes de acessar os dados, o administrador do Armazenamento do Azure deve conceder permissões ao usuário do Azure AD a fim de acessar os dados. Esse tipo de autorização usa o usuário do Azure AD que fez logon no pool de SQL sem servidor, portanto, ela não é compatível com tipos de usuário do SQL.

## <a name="next-steps"></a>Próximas etapas
Informações adicionais sobre conexão de ponto de extremidade e consulta de arquivos podem ser encontradas nos seguintes artigos: 
- [Conectar-se ao seu ponto de extremidade](connect-overview.md)
- [Consultar seus arquivos](develop-storage-files-overview.md)
