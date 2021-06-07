---
title: O que é Instância Gerenciada de SQL do Azure?
description: Saiba mais sobre como a Instância Gerenciada de SQL do Azure fornece quase 100% de compatibilidade com o mecanismo de banco de dados do SQL Server (Edição Enterprise)
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: overview
author: bonova
ms.author: bonova
ms.reviewer: sstein, vanto
ms.date: 01/14/2021
ms.openlocfilehash: 5d49a5b57ff4b59005461f2bb13451822723b039
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105644989"
---
# <a name="what-is-azure-sql-managed-instance"></a>O que é Instância Gerenciada de SQL do Azure?
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

A Instância Gerenciada de SQL do Azure é o serviço de banco de dados de nuvem inteligente e escalonável que combina a compatibilidade mais ampla de mecanismo de banco de dados do SQL Server com todos os benefícios de uma plataforma como serviço evergreen e totalmente gerenciada. A Instância Gerenciada de SQL tem quase 100% de compatibilidade com o mecanismo de banco de dados do SQL Server (Edição Enterprise) mais recente, fornecendo uma implementação de [VNet (rede virtual)](../../virtual-network/virtual-networks-overview.md) nativa que resolve preocupações comuns de segurança e um [modelo de negócios](https://azure.microsoft.com/pricing/details/sql-database/) favorável para clientes existentes do SQL Server. A Instância Gerenciada de SQL permite que os clientes do SQL Server existentes façam lift-and-shift dos aplicativos locais para a nuvem com alterações mínimas do banco de dados e aplicativo. Ao mesmo tempo, a Instância Gerenciada de SQL preserva todos os recursos de PaaS (aplicação automática de patches e atualizações de versão, [backups automatizados](../database/automated-backups-overview.md), [alta disponibilidade](../database/high-availability-sla.md)), reduzindo drasticamente a sobrecarga de gerenciamento e TCO.

Se você não está familiarizado com a Instância Gerenciada de SQL do Azure, confira o vídeo *Instância Gerenciada de SQL do Azure* da nossa [série detalhada de vídeos do SQL do Azure](https://channel9.msdn.com/Series/Azure-SQL-for-Beginners?WT.mc_id=azuresql4beg_azuresql-ch9-niner):
> [!VIDEO https://channel9.msdn.com/Series/Azure-SQL-for-Beginners/Azure-SQL-Managed-Instance-Overview-6-of-61/player]

> [!IMPORTANT]
> Para obter uma lista de regiões em que a Instância Gerenciada de SQL está disponível no momento, confira [Regiões com suporte](resource-limits.md#supported-regions).

O seguinte diagrama apresenta os principais recursos da Instância Gerenciada de SQL:

![Principais recursos](./media/sql-managed-instance-paas-overview/key-features.png)

A Instância Gerenciada de SQL do Azure foi desenvolvida para clientes que desejam migrar um grande número de aplicativos do ambiente local ou IaaS, autocompilados ou fornecidos pelo ISV para o ambiente de nuvem de PaaS totalmente gerenciado, com o menor esforço de migração possível. Usando o [Serviço de Migração de Dados do Azure](../../dms/tutorial-sql-server-to-managed-instance.md#create-an-azure-database-migration-service-instance) totalmente automatizado, os clientes podem realizar lift-and-shift da instância do SQL Server existente para a Instância Gerenciada de SQL, que oferece compatibilidade com o SQL Server e isolamento completo de instâncias do cliente com suporte nativo VNet. Para obter mais informações sobre opções e ferramentas de migração, confira [Visão geral da migração: SQL Server para a Instância Gerenciada de SQL do Azure](../migration-guides/managed-instance/sql-server-to-managed-instance-overview.md).</br> Com o Software Assurance, você pode trocar suas licenças existentes por tarifas com desconto em uma Instância Gerenciada de SQL usando o [Benefício Híbrido do Azure para SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/). A Instância Gerenciada de SQL é o melhor destino da migração na nuvem para instâncias do SQL Server que exigem alta segurança e uma superfície de programação avançada.

> [!TIP]
> Como podemos aprimorar o SQL do Azure? [Responda à pesquisa](https://microsoft.qualtrics.com/jfe/form/SV_ePOznHhP4gDKfGu?channel=456).

## <a name="key-features-and-capabilities"></a>Principais recursos e capacidades

A Instância Gerenciada de SQL combina os melhores recursos disponíveis no Banco de Dados SQL do Azure e no mecanismo de banco de dados do SQL Server.

> [!IMPORTANT]
> Uma Instância Gerenciada de SQL é executada com todos os recursos da versão mais recente do SQL Server, incluindo operações online, correções de plano automático e outros aprimoramentos de desempenho corporativo. Uma comparação dos recursos disponíveis é explicada em [Comparação de recursos: Instância Gerenciada de SQL do Azure e SQL Server](../database/features-comparison.md).

| **Benefícios de PaaS** | **Continuidade de negócios** |
| --- | --- |
|Sem gerenciamento e compra de hardware <br>Sem sobrecarga de gerenciamento para gerenciar infraestrutura subjacente <br>Rápido provisionamento e dimensionamento de serviço <br>Aplicação de patch automatizado e atualização da versão <br>Integração com outros serviços de dados PaaS |99,99% do SLA de tempo de atividade  <br>[Alta disponibilidade](../database/high-availability-sla.md) interna <br>Dados protegidos com [backups automatizados](../database/automated-backups-overview.md) <br>Período de retenção de backup configurável pelo cliente <br>[Backups](/sql/t-sql/statements/backup-transact-sql?preserve-view=true&view=azuresqldb-mi-current) iniciados pelo usuário <br>Capacidade de [restauração pontual do banco de dados](../database/recovery-using-backups.md#point-in-time-restore) |
|**Segurança e conformidade** | **Gerenciamento**|
|Ambiente isolado ([Integração de VNet](connectivity-architecture-overview.md), serviço de locatário único, computação e armazenamento dedicados) <br>[Transparent data encryption (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)<br>[Autenticação do Azure AD (Azure Active Directory)](../database/authentication-aad-overview.md), suporte de logon único <br> [Entidades de segurança do servidor do Azure AD (logons)](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current&preserve-view=true) <br>Cumpre os padrões de conformidade assim como o Banco de Dados SQL do Azure <br>[Auditoria do SQL](auditing-configure.md) <br>[Proteção Avançada contra Ameaças](threat-detection-configure.md) |API do Azure Resource Manager para automatizar o dimensionamento e provisionamento do serviço <br>Funcionalidade do Portal do Azure para dimensionamento e provisionamento manual do serviço <br>Serviço de Migração de Dados

> [!IMPORTANT]
> A Instância Gerenciada de SQL do Azure foi certificada em relação a vários padrões de conformidade. Para saber mais, confira as [Ofertas de Conformidade do Microsoft Azure](https://servicetrust.microsoft.com/ViewPage/MSComplianceGuideV3?command=Download&downloadType=Document&downloadId=44bbae63-bf4d-4e3b-9d3d-c96fb25ec363&tab=7027ead0-3d6b-11e9-b9e1-290b1eb4cdeb&docTab=7027ead0-3d6b-11e9-b9e1-290b1eb4cdeb_FAQ_and_White_Papers), em que é possível encontrar a lista mais atual de certificações de conformidade da Instância Gerenciada de SQL, listadas em **Banco de Dados SQL**.

Os principais recursos da Instância Gerenciada de SQL são mostrados na seguinte tabela:

|Recurso | Descrição|
|---|---|
| Build/versão do SQL Server | Mecanismo de banco de dados do SQL Server (estável mais recente) |
| Backups automatizados gerenciados | Sim |
| Métricas e monitoramento de banco de dados e instância interna | Sim |
| Aplicação automática de patches de software | Sim |
| Os recursos mais recentes do mecanismo de banco de dados | Sim |
| Número de arquivos de dados (LINHAS) por banco de dados | Vários |
| Número de arquivos de log (LOG) por banco de dados | 1 |
| VNet - Implantação do Azure Resource Manager | Sim |
| VNet - Modelo de implantação clássico | Não |
| Suporte do Portal | Sim|
| Serviço de Integração Integrado (SSIS) | Não - o SSIS faz parte da [PaaS do Azure Data Factory](../../data-factory/tutorial-deploy-ssis-packages-azure.md) |
| Serviço de análise interna (SSAS) | Não - SSAS é separado [PaaS](../../analysis-services/analysis-services-overview.md) |
| Serviço interno de relatório (SSRS) | Não. Use [Relatórios paginados do Power BI](/power-bi/paginated-reports/paginated-reports-report-builder-power-bi) ou hospede o SSRS em uma VM do Azure. Embora a Instância Gerenciada de SQL não possa executar o SSRS como serviço, ela pode hospedar [bancos de dados de catálogo SSRS](/sql/reporting-services/install-windows/ssrs-report-server-create-a-report-server-database#database-server-version-requirements) para um servidor de relatórios instalado na Máquina Virtual do Azure, usando a autenticação do SQL Server. |
|||

## <a name="vcore-based-purchasing-model"></a>Modelo de compra baseado em vCore

O [modelo de compra baseado em vCore](../database/service-tiers-vcore.md) para a Instância Gerenciada de SQL oferece flexibilidade, controle, transparência e uma forma direta de traduzir os requisitos de carga de trabalho local para a nuvem. Este modelo permite que você altere computação, memória e armazenamento com base em suas necessidades de carga de trabalho. O modelo vCore também é elegível para economias de até 55% com o [Benefício Híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) para SQL Server.

No modelo vCore, você pode escolher entre gerações de hardware.

- As CPUs lógicas **Gen4** são baseadas em processadores Intel&reg; E5-2673 v3 (Haswell) de 2.4 GHz, SSD conectado, núcleos físicos, 7 GB de RAM por núcleo e tamanhos da computação entre 8 e 24 vCores.
- As CPUs lógicas **Gen5** são baseadas em processadores Intel&reg; E5-2673 v4 (Broadwell) de 2.3 GHz, Intel&reg; SP-8160 (Skylake) e Intel&reg; 8272CL (Cascade Lake) de 2.5 GHz, SSD NVMe rápido, núcleo lógico hyper-threaded e tamanhos da computação entre 4 e 80 núcleos.

Encontre mais informações sobre a diferença entre gerações de hardware em [limites de recursos da Instância Gerenciada de SQL](resource-limits.md#hardware-generation-characteristics).

## <a name="service-tiers"></a>Camadas de serviço

A Instância Gerenciada de SQL está disponível em duas camadas de serviço:

- **Uso geral**: projetada para aplicativos com desempenho típico e requisitos de latência de E/S.
- **Comercialmente crítico**: projetada para aplicativos com baixos requisitos de latência de E/S e impacto mínimo das operações de manutenção subjacentes na carga de trabalho.

Ambas as camadas de serviço garantem 99,99% de disponibilidade e permitem que você selecione de maneira independente o tamanho do armazenamento e a capacidade de computação. Para obter mais informações sobre a arquitetura de alta disponibilidade da Instância Gerenciada de SQL do Azure, confira [Alta disponibilidade e Instância Gerenciada de SQL do Azure](../database/high-availability-sla.md).

### <a name="general-purpose-service-tier"></a>Camada de serviço de Uso Geral

A seguinte lista descreve a principal característica da camada de serviço de Uso Geral:

- Criada para a maioria dos aplicativos de negócios com requisitos de desempenho típicos
- Armazenamento de Blobs do Azure de alto desempenho (8 TB)
- [Alta disponibilidade](../database/high-availability-sla.md#basic-standard-and-general-purpose-service-tier-locally-redundant-availability) interna baseada no Armazenamento de Blobs do Azure confiável e no [Azure Service Fabric](../../service-fabric/service-fabric-overview.md)

Para obter mais informações, confira [Camada de armazenamento na Camada de Uso Geral](https://medium.com/azure-sqldb-managed-instance/file-layout-in-general-purpose-azure-sql-managed-instance-cf21fff9c76c) e [Melhores práticas e considerações de desempenho de armazenamento para a Instância Gerenciada de SQL (Uso Geral)](/archive/blogs/sqlcat/storage-performance-best-practices-and-considerations-for-azure-sql-db-managed-instance-general-purpose).

Encontre mais informações sobre a diferença entre camadas de serviço em [limites de recursos da Instância Gerenciada de SQL](resource-limits.md#service-tier-characteristics).

### <a name="business-critical-service-tier"></a>Camada de serviço comercialmente crítica

A camada de serviço Comercialmente Crítico foi criada para aplicativos com altos requisitos de E/S. Oferece maior resiliência a falhas usando várias réplicas isoladas.

A lista a seguir apresenta as características principais da camada de serviço Comercialmente Crítico:

- Projetada para aplicativos de negócios com requisitos de alta disponibilidade e desempenho mais alto
- Vem com armazenamento SSD local super rápido (até 1 TB no Gen 4 e até 4 TB no Gen 5)
- [Alta disponibilidade](../database/high-availability-sla.md#premium-and-business-critical-service-tier-locally-redundant-availability) interna com base nos [Grupos de Disponibilidade Always On](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) e no [Azure Service Fabric](../../service-fabric/service-fabric-overview.md)
- [Réplica de banco de dados somente leitura](../database/read-scale-out.md) adicional integrada que pode ser usada para relatórios e outras cargas de trabalho somente leitura
- [OLTP In-Memory](../in-memory-oltp-overview.md) que pode ser usado para carga de trabalho com requisitos de alto desempenho  

Encontre mais informações sobre as diferenças entre camadas de serviço em [limites de recursos da Instância Gerenciada de SQL](resource-limits.md#service-tier-characteristics).

## <a name="management-operations"></a>Operações de gerenciamento

A Instância Gerenciada de SQL do Azure fornece operações de gerenciamento que você poderá usar para implantar automaticamente novas instâncias gerenciadas, atualizar propriedades de instância e excluir instâncias quando não forem mais necessárias. A explicação detalhada das operações de gerenciamento pode ser encontrada na página de [visão geral de operações de gerenciamento de instância gerenciada](management-operations-overview.md).

## <a name="advanced-security-and-compliance"></a>Segurança e conformidade avançadas

A Instância Gerenciada de SQL vem com recursos de segurança avançados fornecidos pela plataforma do Azure e pelo mecanismo de banco de dados do SQL Server.

### <a name="security-isolation"></a>Isolamento de segurança

A Instância Gerenciada de SQL fornece isolamento de segurança adicional de outros locatários na plataforma do Azure. O isolamento de segurança inclui:

- [Implementação de rede virtual nativa](connectivity-architecture-overview.md) e conectividade com seu ambiente local usando o Azure ExpressRoute ou o Gateway de VPN.
- Em uma implantação padrão, o ponto de extremidade do SQL é exposto apenas por meio de um endereço IP privado, permitindo conectividade segura de redes híbridas ou privadas do Azure.
- Locatário único com infraestrutura subjacente dedicada (computação, armazenamento).

O diagrama a seguir descreve várias opções de conectividade para seus aplicativos:

![Alta disponibilidade](./media/sql-managed-instance-paas-overview/application-deployment-topologies.png)  

Para obter mais detalhes sobre a integração VNet e a imposição de políticas de rede no nível da sub-rede, confira [Arquitetura de VNet para instâncias gerenciadas](connectivity-architecture-overview.md) e [Conectar seu aplicativo à instância gerenciada](connect-application-instance.md).

> [!IMPORTANT]
> Coloque várias instâncias gerenciadas na mesma sub-rede, onde quer que seja permitido pelos seus requisitos de segurança, porque isso trará benefícios adicionais. A colocação das instâncias na mesma sub-rede simplificará significativamente a manutenção de infraestrutura de rede e reduzirá o tempo de provisionamento da instância, pois uma longa duração do provisionamento está associada ao custo de implantação da primeira instância gerenciada em uma sub-rede.

### <a name="security-features"></a>Recursos de segurança

A Instância Gerenciada de SQL do Azure oferece um conjunto de recursos de segurança avançados que podem ser usados para proteger seus dados.

- A [auditoria da Instância Gerenciada de SQL](auditing-configure.md) rastreia eventos de banco de dados e os grava em um arquivo de log de auditoria colocado na conta de armazenamento do Azure. A auditoria pode ajudar você a manter uma conformidade regulatória, a entender a atividade do banco de dados e a obter informações sobre discrepâncias e anomalias que poderiam indicar preocupações de negócios ou suspeitas de violações de segurança.
- Criptografia de dados em movimento – a Instância Gerenciada de SQL protege os dados, fornecendo criptografia para dados em movimento usando o protocolo TLS. Além do protocolo TLS, a Instância Gerenciada de SQL oferece proteção de dados confidenciais em trânsito, em repouso e durante o processamento de consulta com [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine). O Always Encrypted oferece segurança de dados com relação a violações que envolvem o roubo de dados críticos. Por exemplo, com o Always Encrypted, números de cartão de crédito são armazenados sempre criptografados no banco de dados, mesmo durante a consulta de processamento, permitindo a descriptografia no ponto de uso por pessoal autorizado ou aplicativos que precisam processar os dados.
- A [Proteção Avançada contra Ameaças](threat-detection-configure.md) complementa a [auditoria](auditing-configure.md) fornecendo uma camada adicional de inteligência de segurança compilada ao serviço que detecta tentativas incomuns e potencialmente perigosas de acessar ou explorar bancos de dados. Você é alertado sobre atividades suspeitas, vulnerabilidades potenciais, ataques de injeção de SQL, bem como padrões de acesso do banco de dados anormais. Os alertas da Proteção Avançada contra Ameaças podem ser visualizados no [Centro de Segurança do Azure](https://azure.microsoft.com/services/security-center/). Eles fornecem detalhes de atividades suspeitas e recomendam ação de como investigar e atenuar a ameaça.  
- [Dynamic data masking](/sql/relational-databases/security/dynamic-data-masking) limits sensitive data exposure by masking it to non-privileged users. A máscara de dados dinâmicos ajuda a impedir o acesso não autorizado a dados confidenciais, permitindo que você designe a quantidade de dados confidenciais a ser revelada, com impacto mínimo sobre a camada de aplicativo. Ele é um recurso de segurança baseado em políticas que oculta os dados confidenciais no conjunto de resultados de uma consulta em relação aos campos do banco de dados designados, sem que os dados no banco de dados sejam alterados.
- A [RLS](/sql/relational-databases/security/row-level-security) (segurança em nível de linha) permite controlar o acesso às linhas em uma tabela de banco de dados com base nas características do usuário que executa uma consulta (por exemplo, uma associação de grupo ou um contexto de execução). A RLS simplifica o design e codificação de segurança em seu aplicativo. Ela permite implementar restrições de acesso à linha de dados. Por exemplo, garantindo que os funcionários possam acessar apenas as linhas de dados pertinentes ao seu departamento ou restringindo o acesso a dados apenas para dados relevantes.
- A [TDE (Transparent Data Encryption)](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) criptografa arquivos de dados da Instância Gerenciada de SQL, conhecidos como dados de criptografia em repouso. A TDE realiza a criptografia e a descriptografia de E/S em tempo real dos arquivos de log e de dados. A criptografia usa uma chave de criptografia de banco de dados (DEK), que é armazenada no registro de inicialização do banco de dados para disponibilidade durante a recuperação. Você pode proteger todos os seus bancos de dados na instância gerenciada com a transparent data encryption. A TDE é a tecnologia de criptografia em repouso comprovada no SQL Server exigida por vários padrões de conformidade para proteger contra roubo de mídia de armazenamento.

Há suporte para a migração de um banco de dados criptografado para a Instância Gerenciada de SQL por meio do Serviço de Migração de Banco de Dados do Azure ou uma restauração nativa. Se você planeja migrar um banco de dados criptografado usando a restauração nativa, a migração do certificado TDE existente da instância do SQL Server para a Instância Gerenciada de SQL é uma etapa necessária. Para obter mais informações sobre as opções de migração, confira [Migração do SQL Server para a Instância Gerenciada de SQL](migrate-to-instance-from-sql-server.md).

## <a name="azure-active-directory-integration"></a>Integração do Microsoft Azure Active Directory

A Instância Gerenciada de SQL dá suporte a logons do mecanismo de banco de dados do SQL Server e logons integrados ao Azure AD. As entidades de servidor do Azure AD (logons) (**versão prévia pública**) são uma versão de nuvem do Azure de logons de banco de dados locais que você está usando no ambiente local. As entidades de servidor do Azure AD (logons) permitem que você especifique usuários e grupos de seu locatário do Azure AD como verdadeiras entidades de segurança com escopo de instância, capazes de executar qualquer operação no nível da instância, incluindo consultas entre bancos de dados dentro da mesma instância gerenciada.

Uma nova sintaxe é introduzida para criar entidades de servidor do Azure AD (logons) **DE UM PROVEDOR EXTERNO**. Para obter mais informações sobre a sintaxe, confira [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current&preserve-view=true) e examine o artigo [Provisionar um administrador do Azure Active Directory para a Instância Gerenciada de SQL](../database/authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance).

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Integração do Azure Active Directory e autenticação multifator

A Instância Gerenciada de SQL permite gerenciar centralmente as identidades de usuários do banco de dados e de outros serviços da Microsoft com a [integração do Azure Active Directory](../database/authentication-aad-overview.md). Esse recurso simplifica o gerenciamento de permissão e aprimora a segurança. O Azure Active Directory é compatível com a [autenticação multifator](../database/authentication-mfa-ssms-configure.md) para aumentar a segurança de aplicativos e dados enquanto dá suporte a um processo de logon único.

### <a name="authentication"></a>Autenticação

A autenticação de Instância Gerenciada de SQL refere-se a como os usuários comprovam a identidade deles ao conectarem-se ao banco de dados. A Instância Gerenciada de SQL dá suporte a dois tipos de autenticação:  

- **Autenticação do SQL**:

  Este método de autenticação usa um nome de usuário e senha.
- **Autenticação do Active Directory do Azure**:

  Esse método de autenticação usa identidades gerenciadas pelo Azure Active Directory e tem suporte para domínios gerenciados e integrados. Use autenticação do Active Directory (segurança integrada) [sempre que possível](/sql/relational-databases/security/choose-an-authentication-mode).

### <a name="authorization"></a>Autorização

Autorização refere-se ao que um usuário pode fazer em um banco de dados na Instância Gerenciada de SQL do Azure e é controlado pelas associações de uma função de banco de dados e permissões no nível do objeto da conta de usuário. Uma Instância Gerenciada de SQL tem as mesmas funcionalidades de autorização que o SQL Server 2017.

## <a name="database-migration"></a>Migração de banco de dados

A Instância Gerenciada de SQL direciona cenários de usuários com migração de banco de dados em massa de implementações de bancos de dados locais ou IaaS. A Instância Gerenciada de SQL dá suporte a várias opções de migração de banco de dados que são discutidas nos guias de migração. Confira [Visão geral da migração: SQL Server para a Instância Gerenciada de SQL do Azure](../migration-guides/managed-instance/sql-server-to-managed-instance-overview.md) para obter mais informações.

### <a name="backup-and-restore"></a>Backup e restauração  

A abordagem de migração aproveita backups do SQL para o Armazenamento de Blobs do Azure. Os backups armazenados em um blob de armazenamento do Azure podem ser restaurados diretamente na instância gerenciada usando o [comando T-SQL RESTORE](/sql/t-sql/statements/restore-statements-transact-sql?preserve-view=true&view=azuresqldb-mi-current).

- Para obter um início rápido mostrando como restaurar a Wide World Importers – Arquivo de backup do banco de dados padrão, consulte [Restaurar um arquivo de backup para uma instância gerenciada](restore-sample-database-quickstart.md). Este guia de início rápido mostra que você precisa carregar um arquivo de backup para o Armazenamento de blobs do Azure e o proteger usando uma chave de SAS (assinatura de acesso compartilhado).
- Para obter informações sobre restauração de URL, consulte [Restauração nativa de URL](../migration-guides/managed-instance/sql-server-to-managed-instance-guide.md#backup-and-restore).

> [!IMPORTANT]
> Os backups de uma instância gerenciada só podem ser restaurados para outra instância gerenciada. Eles não podem ser restaurados para uma instância do SQL Server ou para o Banco de Dados SQL do Azure.

### <a name="database-migration-service"></a>Serviço de Migração de Banco de Dados

O Serviço de Migração de Banco de Dados do Azure é um serviço totalmente gerenciado projetado para permitir migrações perfeitas de várias fontes de banco de dados para plataformas de dados do Azure com um tempo de inatividade mínimo. Esse serviço simplifica as tarefas necessárias para mover bancos de dados do SQL Server e de terceiros existentes para o Banco de Dados SQL do Azure, a Instância Gerenciada de SQL do Azure e o SQL Server na VM do Azure. Confira [Como migrar o banco de dados local para a Instância Gerenciada de SQL usando o Serviço de Migração de Banco de Dados](../../dms/tutorial-sql-server-to-managed-instance.md).

## <a name="sql-features-supported"></a>Recursos do SQL com suporte

A Instância Gerenciada de SQL visa entregar aproximadamente 100% da compatibilidade de área de superfície com a última versão do SQL Server por meio de um plano de lançamento em etapas. Para ver uma lista de recursos e comparação, confira [Comparação de recursos da Instância Gerenciada de SQL](../database/features-comparison.md). Para ver uma lista de diferenças de T-SQL na Instância Gerenciada de SQL em comparação com o SQL Server, confira [Diferenças de T-SQL da Instância Gerenciada de SQL do SQL Server](transact-sql-tsql-differences-sql-server.md).

A Instância Gerenciada de SQL dá suporte à compatibilidade com versões anteriores para bancos de dados do SQL Server 2008. Há suporte para a migração direta dos servidores de banco de dados do SQL Server 2005, e o nível de compatibilidade para bancos de dados do SQL Server 2005 migrados foi atualizado para o SQL Server 2008.
  
O seguinte diagrama apresenta a compatibilidade da área de superfície na Instância Gerenciada de SQL:  

![compatibilidade da área de superfície](./media/sql-managed-instance-paas-overview/migration.png)

### <a name="key-differences-between-sql-server-on-premises-and-sql-managed-instance"></a>Principais diferenças entre o SQL Server local e a Instância Gerenciada de SQL

A Instância Gerenciada de SQL se beneficia de estar sempre atualizada na nuvem, o que significa que alguns recursos no SQL Server podem estar obsoletos, desativados ou ter alternativas. Há casos específicos em que as ferramentas precisam reconhecer que um recurso particular funciona de maneira ligeiramente diferente ou que o serviço está sendo executado em um ambiente que não é totalmente controlado.

Algumas das principais diferenças:

- Alta disponibilidade é criada e pré-configurada usando uma tecnologia semelhante a [grupos de disponibilidade Always On](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server).
- Há apenas backups automatizados e restauração pontual. Os clientes podem iniciar backups `copy-only` que não interferem na cadeia de backup automático.
- Não há suporte para especificar caminhos físicos completos, de modo que todos os cenários correspondentes precisam ter suporte de maneira diferente: RESTORE DB não dá suporte a WITH MOVE, CREATE DB não permite caminhos físicos, BULK INSERT funciona somente com blobs do Azure etc.
- A Instância Gerenciada de SQL dá suporte à [autenticação do Azure AD](../database/authentication-aad-overview.md) como uma alternativa de nuvem à autenticação do Windows.
- A Instância Gerenciada de SQL gerencia automaticamente o grupo de arquivos XTP e os arquivos para bancos de dados que contêm objetos OLTP in-memory.
- A Instância Gerenciada de SQL dá suporte ao SSIS (SQL Server Integration Services) e pode hospedar o catálogo do SSIS (SSISDB) que armazena pacotes SSIS, mas eles são executados em um Azure-SSIS IR (Integration Runtime) gerenciado no Azure Data Factory. Confira [Criar Azure-SSIS IR em um Data Factory](../../data-factory/create-azure-ssis-integration-runtime.md). Para comparar os recursos do SSIS, confira [Comparar o Banco de Dados SQL com a Instância Gerenciada de SQL](../../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-sql-database-and-sql-managed-instance).

### <a name="administration-features"></a>Recursos de administração

A Instância Gerenciada de SQL permite que os administradores do sistema gastem menos tempo em tarefas administrativas porque o serviço as realiza para você ou as simplifica bastante. Por exemplo, [instalação do RDBMS/SO e aplicação de patch](../database/high-availability-sla.md), [redimensionamento de instância dinâmica e configuração](../database/single-database-scale.md), [backups](../database/automated-backups-overview.md), [replicação de banco de dados](replication-between-two-instances-configure-tutorial.md) (incluindo bancos de dados do sistema), [configuração de alta disponibilidade](../database/high-availability-sla.md) e configuração de fluxos de dados de [monitoramento de desempenho e integridade](../../azure-monitor/insights/azure-sql.md).

Para obter mais informações, confira [uma lista de recursos da Instância Gerenciada de SQL com suporte e sem suporte](../database/features-comparison.md) e [diferenças de T-SQL entre a Instância Gerenciada de SQL e o SQL Server](transact-sql-tsql-differences-sql-server.md).

### <a name="programmatically-identify-a-managed-instance"></a>Identificar programaticamente uma instância gerenciada

A tabela a seguir mostra várias propriedades, acessíveis por meio do Transact-SQL, que você pode usar para detectar se o aplicativo está trabalhando com a Instância Gerenciada de SQL e recuperar propriedades importantes.

|Propriedade|Valor|Comentário|
|---|---|---|
|`@@VERSION`|Microsoft SQL Azure (RTM) - 12.0.2000.8 07/03/2018 Copyright (C) 2018 Microsoft Corporation.|Esse valor é o mesmo que no Banco de Dados SQL. Isso **não** indica o mecanismo SQL versão 12 (SQL Server 2014). A Instância Gerenciada de SQL sempre executa a versão mais recente do mecanismo SQL estável, que é igual ou superior à versão RTM mais recente disponível do SQL Server.  |
|`SERVERPROPERTY ('Edition')`|SQL Azure|Esse valor é o mesmo que no Banco de Dados SQL.|
|`SERVERPROPERTY('EngineEdition')`|8|Esse valor identifica exclusivamente a instância gerenciada.|
|`@@SERVERNAME`, `SERVERPROPERTY ('ServerName')`|Nome DNS da instância completo no seguinte formato:`<instanceName>`.`<dnsPrefix>`.database.windows.net, em que `<instanceName>` é o nome fornecido pelo cliente, enquanto `<dnsPrefix>` é a parte gerada automaticamente do nome, garantindo exclusividade de nome DNS global ("wcus17662feb9ce98", por exemplo)|Exemplo: my-managed-instance.wcus17662feb9ce98.database.windows.net|

## <a name="next-steps"></a>Próximas etapas

- Para saber como criar sua primeira instância gerenciada, confira o [Guia de início rápido](instance-create-quickstart.md).
- Para obter uma lista de recursos e de comparação, consulte [Recursos comuns do SQL](../database/features-comparison.md).
- Para obter mais informações sobre a configuração de VNet, confira [Configuração de VNet de Instância Gerenciada de SQL](connectivity-architecture-overview.md).
- Para obter um início rápido que cria uma instância gerenciada e restaura um banco de dados de um arquivo de backup, confira [Criar uma instância gerenciada](instance-create-quickstart.md).
- Para obter um tutorial sobre como usar o Serviço de Migração de Banco de Dados do Azure para migração, confira [Migração de Instância Gerenciada de SQL usando o Serviço de Migração de Banco de Dados](../../dms/tutorial-sql-server-to-managed-instance.md).
- Para obter o monitoramento avançado do desempenho de banco de dados da Instância Gerenciada de SQL com inteligência de solução de problemas interna, confira [Monitorar a Instância Gerenciada de SQL do Azure usando a Análise de SQL do Azure](../../azure-monitor/insights/azure-sql.md).
- Para saber mais sobre preços, confira [Preços do Banco de Dados SQL](https://azure.microsoft.com/pricing/details/sql-database/managed/).