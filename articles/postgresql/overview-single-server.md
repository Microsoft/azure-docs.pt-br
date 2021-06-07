---
title: Servidor Único do Banco de Dados do Azure para PostgreSQL
description: Fornece uma visão geral do Servidor Único do Banco de Dados do Azure para PostgreSQL.
author: sunilagarwal
ms.author: sunila
ms.custom: mvc
ms.service: postgresql
ms.topic: overview
ms.date: 09/21/2020
ms.openlocfilehash: 18d944cd2b3bfde1a0ee5fc53e83e4016b11b01f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100379209"
---
# <a name="azure-database-for-postgresql-single-server"></a>Servidor Único do Banco de Dados do Azure para PostgreSQL

O [Banco de Dados do Azure para PostgreSQL](./overview.md) desenvolvido com o PostgreSQL community edition está disponível em três modos de implantação:

- Servidor único
- Servidor Flexível (versão prévia)
- Hiperescala (Citus)

Neste artigo, forneceremos uma visão geral e introdução aos conceitos principais do modelo de implantação de servidor único. Para saber mais sobre o modo de implantação de servidor flexível, confira a [visão geral do servidor flexível](./flexible-server/overview.md) e a Visão Geral de Hiperescala (Citus), respectivamente.

## <a name="overview"></a>Visão geral

O Servidor Único é um serviço de banco de dados totalmente gerenciado com requisitos mínimos para personalizações do banco de dados. A plataforma de servidor único é projetada para administrar a maioria das funções de gerenciamento de banco de dados, como aplicação de patches, backups, alta disponibilidade, controle e segurança com mínima configuração do usuário. A arquitetura é otimizada para fornecer 99,99% de disponibilidade em uma única zona de disponibilidade. Ela dá suporte à versão da comunidade do PostgreSQL 9.5, 9.6, 10 e 11. O serviço está em disponibilidade geral em diversas [regiões do Azure](https://azure.microsoft.com/global-infrastructure/services/).

Servidores únicos são mais adequados para aplicativos nativos na nuvem projetados para lidar com aplicação de patch automatizada sem a necessidade de controle granular sobre o agendamento de aplicação de patch e definições de configuração de PostgreSQL personalizadas.

## <a name="high-availability"></a>Alta disponibilidade

O modelo de implantação de servidor único é otimizado para alta disponibilidade interna e elasticidade a um custo reduzido. A arquitetura separa a computação do armazenamento. O mecanismo de banco de dados é executado em um contêiner de computação proprietário, enquanto os arquivos de dados são residem no armazenamento do Azure. O armazenamento mantém três cópias síncronas localmente redundantes dos arquivos de banco de dados, garantindo a durabilidade dos dados.

Durante eventos de failover planejados ou não planejados, se o servidor ficar inativo, o serviço manterá a alta disponibilidade dos servidores usando o seguinte procedimento automatizado:

1. Um novo contêiner de computação é provisionado.
2. O armazenamento com arquivos de dados é mapeado para o novo contêiner.
3. O mecanismo de banco de dados do PostgreSQL é colocado online no novo contêiner de computação.
4. O serviço de gateway garante um failover transparente, garantindo que nenhuma alteração no lado do aplicativo seja exigida.
   
 :::image type="content" source="./media/overview/overview-azure-postgres-single-server.png" alt-text="Servidor Único do Banco de Dados do Azure para PostgreSQL":::

O tempo de failover típico varia de 60 a 120 segundos. O design nativo de nuvem do serviço de servidor único permite que ele dê suporte a 99,99% da disponibilidade, eliminando o custo de espera ativa/passiva.

O SLA (contrato de nível de serviço) de disponibilidade de 99,99% do Azure, que é líder do setor e é desenvolvido por uma rede global de datacenters gerenciados pela Microsoft, ajuda a manter os seus aplicativos em execução de modo ininterrupto.

## <a name="automated-patching"></a>Aplicação de patch automatizada

O serviço executa a aplicação automatizada de patch do hardware, do sistema operacional e do mecanismo de banco de dados subjacentes. A aplicação de patch inclui atualizações de segurança e software. Para o mecanismo do PostgreSQL, as atualizações de versão secundária são automáticas e incluídas como parte do ciclo de aplicação de patch. Não há nenhuma ação do usuário nem definições de configuração necessárias para a aplicação de patch. A frequência de aplicação de patch é gerenciada pelo serviço com base na importância do conteúdo. No geral, o serviço segue a agenda de lançamento mensal como parte do lançamento e da integração contínua. Os usuários podem assinar a [notificação de manutenção planejada]() para receber a notificação das próximas 72 horas de manutenção antes do evento.

## <a name="automatic-backups"></a>Backups automáticos

O serviço de servidor único cria backups de servidor automaticamente e os armazena no LRS (armazenamento com redundância local) ou no armazenamento com redundância geográfica configurado pelo usuário. Os backups podem ser usados para restaurar o servidor em qualquer ponto no tempo dentro do período de retenção de backup. O período de retenção de backup padrão é de sete dias. A retenção pode ser configurada opcionalmente em até 35 dias. Todos os backups são criptografados usando a criptografia AES de 256 bits. Confira [Backups](./concepts-backup.md) para obter detalhes.

## <a name="adjust-performance-and-scale-within-seconds"></a>Ajustar o desempenho e a escala em segundos

O serviço de servidor único está disponível em três níveis de SKU: Básico, Uso Geral e Otimizado para Memória. O nível Básico é mais adequado para o desenvolvimento de baixo custo e cargas de trabalho com baixa simultaneidade. O Uso Geral e o Otimizado para Memória são mais adequados para cargas de trabalho de produção que exigem alta simultaneidade, escala e desempenho previsível. Você pode criar seu primeiro aplicativo em um banco de dados pequeno por alguns dólares por mês e então ajustar a escala para atender às necessidades da sua solução. A escala do armazenamento está online e dá suporte ao aumento automático do armazenamento. A escalabilidade dinâmica permite que o banco de dados responda de forma transparente a mudanças rápidas nos requisitos de recursos. Você paga somente pelos recursos que consome. Veja [Tipos de preço]() para obter detalhes.

## <a name="enterprise-grade-security-compliance-and-governance"></a>Segurança, conformidade e governança de nível empresarial

O serviço de servidor único usa o módulo de criptografia validado por FIPS 140-2 para criptografia de armazenamento de dados em repouso. Os dados, incluindo backups, e os arquivos temporários criados durante a execução de consultas são criptografados. O serviço usa a criptografia AES de 256 bits incluída na criptografia de armazenamento do Azure, e as chaves são gerenciadas pelo sistema (padrão) ou [gerenciadas pelo cliente](). O serviço criptografa os dados em movimento com o protocolo SSL/TLS imposto por padrão. O serviço dá suporte às versões do TLS 1.2, 1.1 e 1.0 com uma capacidade de impor a [versão de TLS mínima]().

O serviço permite o acesso privado aos servidores usando o link privado e fornece o recurso Proteção avançada contra ameaças. A proteção avançada contra ameaças detecta atividades anômalas que indicam tentativas incomuns e potencialmente prejudiciais de acesso ou exploração dos bancos de dados.

Além da autenticação nativa, o serviço de servidor único dá suporte à autenticação do Azure Active Directory. A autenticação do Azure AD é um mecanismo de conexão com os servidores PostgreSQL usando identidades definidas e gerenciadas no Azure AD. Com a autenticação do Azure AD, é possível gerenciar as identidades de usuários do banco de dados e outros serviços do Azure em uma localização central, o que simplifica e centraliza o controle de acesso.

O [log de auditoria]() (em versão prévia) está disponível para rastrear toda a atividade no nível do banco de dados.

O serviço de servidor único está em conformidade com todas as certificações líderes do setor, como FedRAMP, HIPAA, PCI DSS. Visite a [Central de Confiabilidade do Azure]() para obter informações sobre a segurança da plataforma do Azure.

Para obter mais informações sobre os recursos de segurança do Banco de Dados do Azure para PostgreSQL, confira a [visão geral de segurança]().

## <a name="monitoring-and-alerting"></a>Monitoramento e alertas

O serviço de servidor único é equipado com recursos internos de monitoramento e alerta de desempenho. Todas as métricas do Azure têm uma frequência de um minuto e cada uma delas fornece 30 dias de histórico. É possível configurar alertas nas métricas. O serviço permite configurar logs de consulta lentos e vem com um recurso [Repositório de consultas](./concepts-query-store.md) diferenciado. O Repositório de Consultas simplifica a solução de problemas ajudando você a rapidamente localizar as consultas de execução mais longa e que consomem mais recursos. Usando essas ferramentas, você pode otimizar rapidamente suas cargas de trabalho e configurar seu servidor para ter o melhor desempenho. Confira [Monitoramento](./concepts-monitoring.md) para obter mais detalhes.

## <a name="migration"></a>Migração

O serviço executa a versão da comunidade do PostgreSQL. Isso permite a compatibilidade total do aplicativo e exige um custo de refatoração mínimo para migrar o aplicativo existente desenvolvido no mecanismo PostgreSQL para um serviço de servidor único. A migração para o servidor único pode ser executada usando uma das seguintes opções:

- **Despejo e restauração**: nas migrações offline, em que os usuários podem ter algum tempo de inatividade, realizar o despejo e a restauração com ferramentas da comunidade, como pg_dump e pg_restore, pode fornecer uma forma mais rápida de migração. Confira [Migrar usando despejo e restauração](./howto-migrate-using-dump-and-restore.md) para obter detalhes.
- **Serviço de Migração de Banco de Dados do Azure** – Para migrações integradas e simplificadas para o servidor único com um tempo de inatividade mínimo, você pode aproveitar o Serviço de Migração de Banco de Dados do Azure. Confira [DMS por meio do portal](../dms/tutorial-postgresql-azure-postgresql-online-portal.md) e [DMS por meio da CLI](../dms/tutorial-postgresql-azure-postgresql-online.md).

## <a name="contacts"></a>Contatos

Para perguntas ou sugestões sobre como trabalhar com o Banco de Dados do Azure para PostgreSQL, envie um email para a equipe do Banco de Dados do Azure para PostgreSQL ([@Ask Banco de Dados do Azure para PostgreSQL](mailto:AskAzureDBforPostgreSQL@service.microsoft.com)). Esse endereço de email não é um alias de suporte técnico.

Além disso, considere os seguintes pontos de contato, conforme apropriado:

- Para entrar em contato com o Suporte do Azure, [crie um tíquete no Portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Para corrigir um problema com sua conta, apresente uma [solicitação de suporte](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) no portal do Azure.
- Para fornecer comentários ou solicitar novos recursos, crie uma entrada por meio do [UserVoice](https://feedback.azure.com/forums/597976-azure-database-for-postgresql).

## <a name="next-steps"></a>Próximas etapas

Agora que você leu a introdução ao modo de implantação do servidor único do Banco de Dados do Azure para PostgreSQL, você está pronto para:
- Criar o seu primeiro servidor.
