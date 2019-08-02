---
title: Perguntas frequentes sobre a instância de gerenciado do banco de dados SQL | Microsoft Docs
description: Perguntas frequentes (FAQ) de instância gerenciada do banco de dados SQL
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab
manager: craigg
ms.date: 07/08/2019
ms.openlocfilehash: c3a070eb7e1435055b47b39985cf8cb0b182a514
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798075"
---
# <a name="sql-database-managed-instance-frequently-asked-questions-faq"></a>Perguntas frequentes (FAQ) de instância gerenciada do banco de dados SQL

Este artigo contém muitas das perguntas mais comuns sobre [instância gerenciada do banco de dados SQL](sql-database-managed-instance.md).

## <a name="where-can-i-find-a-list-of-features-that-are-supported-on-managed-instance"></a>Onde posso encontrar uma lista de recursos que têm suporte na instância gerenciada?

Para obter uma lista de recursos com suporte na instância gerenciada, consulte [banco de dados SQL Azure versus SQL Server](sql-database-features.md).

As diferenças na sintaxe e o comportamento entre a instância de banco de dados do SQL Azure gerenciado e o SQL Server no local, consulte [diferenças de T-SQL do SQL Server](sql-database-managed-instance-transact-sql-information.md).


## <a name="where-can-i-find-technical-characteristics-and-resource-limits-for-managed-instance"></a>Onde encontrar características técnicas e limites de recursos para a instância gerenciada?

As características de geração de hardware disponíveis, consulte [diferenças técnicas nas gerações de hardware](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics).
Para as camadas de serviço disponíveis e suas características, consulte [técnicas diferenças entre as camadas de serviço](sql-database-managed-instance-resource-limits.md#service-tier-characteristics).

## <a name="where-can-i-find-known-issues-and-bugs"></a>Onde encontrar os problemas conhecidos e bugs?

Para bugs e problemas conhecidos, consulte [alterações de comportamento](sql-database-managed-instance-transact-sql-information.md#Changes) e [problemas conhecidos](sql-database-managed-instance-transact-sql-information.md#Issues).


## <a name="can-a-managed-instance-have-the-same-name-as-on-premises-sql-server"></a>Uma instância gerenciada pode ter o mesmo nome que o SQL Server no local?

A instância gerenciada deve ter um nome que termina com *database.windows.net*. Para usar outra zona DNS em vez do padrão, por exemplo, **mi outro nome**. contoso.com: 
- Use CliConfig para definir um alias (a ferramenta é apenas um wrapper de configurações do registro, portanto, isso também poderia ser feito usando a diretiva de grupo ou script).
- Use *CNAME* com *TrustServerCertificate = true* opção.


## <a name="how-can-i-move-database-from-managed-instance-back-to-sql-server-or-azure-sql-database"></a>Como posso mover banco de dados de instância gerenciada volta ao SQL Server ou banco de dados SQL?

Você pode [exportar o banco de dados para o bacpac](sql-database-export.md) e, em seguida [importar o arquivo bacpac]( sql-database-import.md). Essa abordagem é recomendada se seu banco de dados for menor que 100 GB.

Replicação transacional pode ser usada se todas as tabelas no banco de dados tiverem chaves primárias.

Nativo `COPY_ONLY` os backups de instância gerenciada não podem ser restaurados para o SQL Server porque a instância gerenciada tem uma versão mais recente do banco de dados em comparação ao SQL Server.

## <a name="how-can-i-migrate-my-instance-database-to-a-single-azure-sql-database"></a>Como posso migrar meu banco de dados de instância para um único banco de dados SQL?

Uma opção é [exportar o banco de dados para um bacpac](sql-database-export.md) e, em seguida [importar o arquivo bacpac]( sql-database-import.md). 

Isso é a abordagem recomendada se seu banco de dados for menor que 100 GB. Replicação transacional pode ser usada se todas as tabelas no banco de dados tiverem chaves primárias.

## <a name="how-do-i-choose-between-gen-4-and-gen-5-hardware-generation-for-managed-instance"></a>Como escolher entre Gen 4 e Gen 5 geração de hardware para a instância gerenciada?

Isso depende de sua carga de trabalho conforme alguns geração de hardware é melhor para determinados tipos de cargas de trabalho que o outro. Embora o assunto do desempenho é bastante complexo para simplificar, as seguintes diferenças entre as gerações de hardware que afetam o desempenho da carga de trabalho:
- A Gen 4 fornece um melhor suporte de computação como ele se baseia nos processadores físicos, versus Gen 5 baseado em vCore processadores. Isso pode ser mais vantajoso para cargas de trabalho com uso intensivo de computação.
- Dá suporte a Gen 5 acelerada rede resultando em uma melhor largura de banda de e/s para o armazenamento remoto. Isso pode ser vantajoso para cargas de trabalho com uso intensivo de e/s nas camadas de serviço de uso geral. Gen 5 usa discos locais mais rápidos do SSD em comparação comparados Gen 4. Isso pode ser vantajoso para cargas de trabalho com uso intensivo de e/s nas camadas de serviço críticos de negócios.

Os clientes são aconselhados a testar o desempenho de cargas de trabalho reais destinadas a produção antes de ficarem ativos para determinar qual hardware geração funcionará melhor no seu caso.

## <a name="can-i-switch-my-managed-instance-hardware-generation-between-gen-4-and-gen-5-online"></a>Posso mudar minha instância gerenciada geração do hardware entre Gen 4 e 5 online? 

É possível se ambas as gerações de hardware estão disponíveis na mesma região em que sua instância gerenciada é provisionada comutação online automatizada entre gerações de hardware. Nesse caso, você tem uma opção na seção de camada de preço no portal do Azure para alternar entre gerações de hardware.

Isso é um long executando a operação como a nova instância gerenciada será ser provisionada automaticamente no back-end e os bancos de dados transferidos entre a instância antiga e nova. Esse processo será perfeito para os clientes.

Se ambas as gerações de hardware não são suportadas na mesma região, alterando a geração de hardware é possível, mas deve ser feita manualmente. Isso exige que você provisionar uma nova instância da região em que a geração de hardware desejado está disponível e manualmente fazendo backup e restauração de dados entre a instância antiga e nova.


## <a name="how-do-i-tune-performance-of-my-managed-instance"></a>Como ajustar o desempenho de minha instância gerenciada? 

Uso geral de instância gerenciada usa o armazenamento remoto devido à qual o tamanho dos arquivos de dados e de log é importante para desempenho. Para ajustar o desempenho de nível de serviço de uso geral, siga as instruções nesta postagem no blog.

Para cargas de trabalho intensivas de e/s considere usar o hardware Ger 5, em vez de usar Gen 4 para cargas de trabalho com uso intensivo de computação. Para obter mais informações, consulte a seção de perguntas Frequentes sobre como escolher entre gerações de hardware.

Se sua carga de trabalho consiste em muitas transações pequenas, considere a possibilidade do tipo de conexão de proxy para o modo de redirecionamento.

## <a name="what-is-the-maximum-storage-size-for-managed-instance"></a>O que é o tamanho máximo de armazenamento para a instância gerenciada? 

A camada de serviço selecionado (uso geral ou comercialmente crítico) depende do tamanho do armazenamento para a instância gerenciada. Para limitações de armazenamento uma dessas camadas de serviço, consulte [característica da camada de serviço](sql-database-service-tiers-general-purpose-business-critical.md).

## <a name="is-the-backup-storage-deducted-from-my-managed-instance-storage"></a>O armazenamento de backup é deduzido do meu armazenamento de instância gerenciada? 

Não, o armazenamento de backup não será deduzido do seu espaço de armazenamento de instância gerenciada. O armazenamento de backup é independente do espaço de armazenamento de instância e não é limitado em tamanho. Armazenamento de backup é limitado pelo tempo para reter o backup de seus bancos de dados instância, configuráveis de 7 a 35 dias. Para obter detalhes, consulte [backups automatizados](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups).
  
## <a name="how-can-i-set-inbound-nsg-rules-on-management-ports"></a>Como configurar regras de NSG de entrada nas portas de gerenciamento?

O recurso de firewall interno configura o firewall do Windows em todas as máquinas virtuais no cluster para permitir conexões de entrada de intervalos de IP associadas somente a máquinas de implantação do gerenciamento da Microsoft e estações de trabalho de administração seguras efetivamente impedindo invasões por meio da camada de rede.

Aqui está a quais portas são usadas para:

Portas 9000 e 9003 são usadas pela infraestrutura do Service Fabric. Função primária do Service Fabric é manter o cluster virtual íntegro e manter o estado de meta em termos de número de réplicas do componente.

Portas 1438, 1440 e 1452 são usadas pelo agente do nó. Agente do nó é um aplicativo que é executado dentro do cluster e é usado pelo plano de controle para executar comandos de gerenciamento.

O firewall interno na camada de rede, além de comunicação também é protegida com certificados.
  
Para obter mais informações e como verificar o firewall interno, consulte [banco de dados SQL gerenciados firewall interno da instância](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md).


## <a name="how-can-i-mitigate-networking-risks"></a>Como reduzir os riscos de rede? 

Para atenuar quaisquer riscos de rede, os clientes são recomendados para aplicar um conjunto de configurações de segurança e controles:

- Ative na criptografia de dados transparente (TDE) de todos os bancos de dados.
- Ative o logoff em tempo de execução CLR (Common Language). Isso é recomendado local também.
- Use apenas a contas do Azure AD.
- Acesso a SQL MI com conta com baixos privilégios DBA.
- Configure o acesso de jumpbox JiT para a conta sysadmin.
- Ative a auditoria do SQL e integrá-lo com mecanismos de alerta.
- Ative a detecção de ameaças do suite ATS.


## <a name="where-can-i-find-use-cases-and-resulting-cost-savings-with-managed-instance"></a>Onde encontrar as economias de custos resultantes e casos de uso com a instância gerenciada?

Estudos de caso de instância gerenciada:

- [Komatsu](http://customers.microsoft.com/story/komatsu-australia-manufacturing-azure)
- [powerdetails](http://customers.microsoft.com/story/powerdetails-partner-professional-services-azure-sql-database-managed-instance)
- [Allscripts](http://customers.microsoft.com/story/allscripts-partner-professional-services-azure)   
Para obter uma melhor compreensão sobre os benefícios, custos e riscos associados à implantação de instância gerenciada do banco de dados SQL, também há estudo da Forrester um: [Total Economic Impact of MI](https://azure.microsoft.com/resources/forrester-tei-sql-database-managed-instance).


## <a name="can-i-do-dns-refresh"></a>Posso fazer a atualização DNS? 
  
Atualmente, não fornecemos um recurso para atualizar a configuração do servidor DNS para a instância gerenciada.

Configuração de DNS, eventualmente, é atualizada:

- Quando a concessão de DHCP expira.
- Na atualização da plataforma.

Como alternativa, fazer o downgrade de instância gerenciada a ser vCore 4 e atualizá-lo novamente mais tarde. Isso tem um efeito colateral de atualizar a configuração do DNS.


## <a name="can-a-managed-instance-have-a-static-ip-address"></a>Uma instância gerenciada pode ter um endereço IP estático?

Em situações raras mas necessárias, é necessário fazer uma migração online de uma instância gerenciada para um novo cluster virtual. Se necessário, essa migração é devido a alterações na nossa pilha de tecnologia para melhorar a segurança e confiabilidade do serviço. Migrando para um novo resultar de cluster virtual em alterar o endereço IP que é mapeado para o nome do host de instância gerenciada. O serviço de instância gerenciada não de declaração de suporte de endereço IP estático e reserva o direito de alterá-lo sem aviso prévio como parte da manutenção regular ciclos.

Por esse motivo, nós não recomendamos depender de imutabilidade do endereço IP como ele pode fazer com que o tempo de inatividade desnecessário.


## <a name="can-i-change-the-time-zone-for-an-existing-managed-instance"></a>Posso alterar o fuso horário para uma instância gerenciada existente?

Configuração de fuso horário pode ser definida quando uma instância gerenciada é provisionada pela primeira vez. Não há suporte para alterar o fuso horário da instância gerenciada existente. Para obter detalhes, consulte [limitações de fuso horário](sql-database-managed-instance-timezone.md#limitations).

Soluções alternativas incluem a criação de uma nova instância gerenciada com o fuso horário apropriado e, em seguida, execute um backup manual e restauração ou o que é recomendável executar uma [entre instâncias point-in-time restore](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/07/cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance/).


## <a name="how-do-i-resolve-performance-issues-with-my-managed-instance"></a>Como resolver problemas de desempenho com a instância gerenciada

Para obter uma comparação de desempenho entre a instância gerenciada e SQL Server, é um bom ponto de partida [práticas recomendadas para a comparação de desempenho entre a instância do SQL Azure gerenciado e o SQL Server](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/The-best-practices-for-performance-comparison-between-Azure-SQL/ba-p/683210).

Carregamentos de dados costumam ser mais lentos em uma instância gerenciada que no SQL Server devido ao modelo de recuperação completa obrigatório e [limites](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics) em produtividade de gravação de log de transações. Às vezes, isso pode ser contornado, carregamento de dados transitórios em tempdb, em vez de banco de dados de usuário ou usando o columnstore clusterizado, ou tabelas com otimização de memória.


## <a name="can-i-restore-my-encrypted-database-to-managed-instance"></a>Pode restaurar meu banco de dados criptografado para a instância gerenciada?

Sim, você não precisará descriptografar o banco de dados para que seja possível restaurá-lo para a instância gerenciada. Você precisa fornecer uma certificado/chave usada como um protetor de chave de criptografia no sistema de origem para a instância gerenciada para ser capaz de ler dados do arquivo de backup criptografado. Há duas maneiras possíveis de fazer isso:

- Carregue o protetor de certificado para a instância gerenciada. Isso pode ser feito apenas usando o PowerShell. O script de exemplo descreve o processo inteiro.
- Carregue o protetor de chave assimétrico para AKV Azure Key Vault () e apontar a instância gerenciada para ele. Essa abordagem é semelhante ao caso de uso do bring-your-proprietário-key (BYOK) TDE que também usa a integração de AKV para armazenar a chave de criptografia. Se você quiser apenas a chave carregada no AKV disponível para a instância gerenciada para a restauração de bancos de dados criptografados sem, na verdade, usando a chave como um protetor de chave de criptografia, siga as instruções para configurar o BYOK TDE e não marque a caixa de seleção Verifique a chave selecionada o protetor de TDE padrão.

Depois que você disponibiliza a criptografia protetor para a instância gerenciada, você pode continuar com o procedimento de restauração do banco de dados padrão.

## <a name="how-can-i-migrate-from-azure-sql-database-single-or-elastic-pool-to-managed-instance"></a>Como posso migrar de pool único ou Elástico de banco de dados SQL para a instância gerenciada? 

Gerenciado instância oferece os mesmos níveis de desempenho por tamanho de computação e armazenamento que outras opções de implantação de banco de dados SQL. Se você deseja consolidar dados em uma única instância ou simplesmente precisar de um recurso com suporte exclusivamente na instância gerenciada, você pode migrar seus dados usando a funcionalidade de importação/exportação (BACPAC).
