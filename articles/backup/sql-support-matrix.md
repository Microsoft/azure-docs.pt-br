---
title: Matriz de suporte de backup do Azure para SQL Server Backup em VMs do Azure
description: Fornece um resumo das configurações de suporte e limitações ao fazer backup de SQL Server em VMs do Azure com o serviço de backup do Azure.
ms.topic: conceptual
ms.date: 03/05/2020
ms.custom: references_regions
ms.openlocfilehash: 78436981c515b95ccda763d8ac916738b4364953
ms.sourcegitcommit: f7084d3d80c4bc8e69b9eb05dfd30e8e195994d8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/22/2020
ms.locfileid: "97734786"
---
# <a name="support-matrix-for-sql-server-backup-in-azure-vms"></a>Matriz de suporte para backup SQL Server em VMs do Azure

Você pode usar o backup do Azure para fazer backup de bancos de dados SQL Server em VMs do Azure hospedadas na plataforma Microsoft Azure Cloud. Este artigo resume as configurações de suporte geral e as limitações para cenários e implantações de SQL Server Backup em VMs do Azure.

## <a name="scenario-support"></a>Suporte ao cenário

**Suporte** | **Detalhes**
--- | ---
**Implantações com suporte** | Há suporte para VMs do Azure no Marketplace do SQL e VMs que não são do Marketplace (do SQL Server instaladas manualmente).
**Regiões com suporte** | ASE (Sudeste da Austrália), AE (Leste da Austrália), Leste da Austrália, AC (Austrália Central), AC (Austrália Central 2) <br> Sul do Brasil (BRS)<br> Canadá Central (CNC), Leste do Canadá (CE)<br> Sudeste da Ásia (SEA), Leste da Ásia (EA) <br> Leste dos EUA (EUS), Leste dos EUA 2 (EUS2), Centro-Oeste dos EUA (WCUS), Oeste dos EUA (WUS); Oeste dos EUA 2 (WUS 2) Centro-Norte dos EUA (NCUS) EUA Central (CUS) Centro-Sul dos EUA (SCUS) <br> INC (Índia Central), INS (Sul da Índia), Oeste da Índia <br> Oeste do Japão (JPE), Leste do Japão (JPW) <br> Coreia Central (KRC), Sul da Coreia (KRS) <br> Norte da Europa (NE), Oeste da Europa <br> Sul do Reino Unido (UKS), Oeste do Reino Unido (UKW) <br> US Gov – Arizona, US Gov – Virgínia, US Gov – Texas, Região Central do US DoD, Leste do US DoD <br> Norte da Alemanha, Centro-oeste da Alemanha <br> Norte da Suíça, Oeste da Suíça <br> França Central <br> Leste da China, Leste da China 2, Norte da China, Norte da China 2
**Sistemas operacionais com suporte** | Windows Server 2019, Windows Server 2016, Windows Server 2012, Windows Server 2008 R2 SP1 <br/><br/> Não há suporte para Linux no momento.
**Versões do SQL Server com suporte** | SQL Server 2019, SQL Server 2017, conforme detalhado na [página Pesquisar ciclo de vida de produto](https://support.microsoft.com/lifecycle/search?alpha=SQL%20server%202017), SQL Server 2016 e SPs conforme detalhado na [página Pesquisar ciclo de vida do produto](https://support.microsoft.com/lifecycle/search?alpha=SQL%20server%202016%20service%20pack), SQL Server 2014, SQL Server 2012, SQL Server 2008 R2, SQL Server 2008 <br/><br/> Enterprise, Standard, Web, Developer e Express.<br><br>Não há suporte para versões expressas do BD local.
**Versões do .NET com suporte** | .NET Framework 4.5.2 ou posterior instalado na VM

## <a name="feature-considerations-and-limitations"></a>Considerações e limitações de recursos

|Configuração  |Limite máximo |
|---------|---------|
|Número de bancos de dados que podem ser protegidos em um servidor (e em um cofre)    |   2000      |
|Tamanho do banco de dados com suporte (Além disso, problemas de desempenho podem surgir)   |   2 TB      |
|Número de arquivos com suporte em um banco de dados    |   1000      |

>[!NOTE]
> [Baixe o planejador de recursos detalhado](https://download.microsoft.com/download/A/B/5/AB5D86F0-DCB7-4DC3-9872-6155C96DE500/SQL%20Server%20in%20Azure%20VM%20Backup%20Scale%20Calculator.xlsx) para calcular o número aproximado de bancos de dados protegidos que são recomendados por servidor com base nos recursos da VM, na largura de banda e na política de backup.

* O Backup do SQL Server pode ser configurado no portal do Azure ou **PowerShell**. Não há suporte para a CLI.
* A solução é compatível em ambos os tipos de [implantações](../azure-resource-manager/management/deployment-models.md) – VMs do Azure Resource Manager e VMs clássicas.
* Há suporte para todos os tipos de backup (completo/diferencial/log) e modelos de recuperação (simples/completo/bulk-logged).
* Para bancos de dados **somente leitura** : backups completos e somente cópia são os únicos tipos de backup com suporte.
* A compactação nativa do SQL tem suporte se habilitada explicitamente pelo usuário na política de backup. O backup do Azure substitui os padrões em nível de instância pela cláusula COMPRESSION/NO_COMPRESSION, dependendo do valor desse controle, conforme definido pelo usuário.
* Há suporte para backup de banco de dados habilitado para TDE. Para restaurar um banco de dados criptografado com TDE para outro SQL Server, primeiro você precisa [restaurar o certificado para o servidor de destino](/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server). A compactação de backup para bancos de dados habilitados para TDE para o SQL Server 2016 e versões mais recentes está disponível, mas com tamanho de transferência mais baixo, conforme explicado [aqui](https://techcommunity.microsoft.com/t5/sql-server/backup-compression-for-tde-enabled-databases-important-fixes-in/ba-p/385593).
* Não há suporte para operações de backup e restauração para bancos de dados de espelho e instantâneos do banco de dados.
* Não há suporte para SQL Server **FCI (instância de cluster de failover)** .
* Usar mais de uma solução de backup para fazer backup da instância SQL Server autônoma ou do grupo de disponibilidade AlwaysOn do SQL pode levar a uma falha de backup. Evite fazer isso. Fazer backup de dois nós de um grupo de disponibilidade individualmente com soluções iguais ou diferentes, também poderá levar à falha de backup.
* Quando os grupos de disponibilidade são configurados, os backups são obtidos dos diferentes nós com base em alguns fatores. O comportamento de backup para um grupo de disponibilidade está resumido abaixo.

### <a name="back-up-behavior-with-always-on-availability-groups"></a>Comportamento de backup com grupos de disponibilidade Always On

Recomendamos que o backup seja configurado em apenas um nó de um AG (grupo de disponibilidade). Sempre configure o backup na mesma região que o nó primário. Em outras palavras, você sempre precisa que o nó primário esteja presente na região em que você está configurando o backup. Se todos os nós do AG estiverem na mesma região em que o backup está configurado, não haverá nenhuma preocupação.

#### <a name="for-cross-region-ag"></a>Para grupos de disponibilidade entre regiões

* Independentemente da preferência de backup, os backups só serão executados a partir dos nós que estão na mesma região em que o backup está configurado. Isso ocorre porque não há suporte para backups entre regiões. Se você tiver apenas dois nós e o nó secundário estiver na outra região, os backups continuarão a ser executados a partir do nó primário (a menos que a preferência de backup seja ' somente secundária ').
* Se um nó passar por failover para uma região diferente daquela em que o backup está configurado, os backups falharão nos nós na região que passou por failover.

Dependendo da preferência de backup e os tipos de backups (completo/diferencial/log/somente cópia completos), os backups serão feitos em um nó específico (primário/secundário).

#### <a name="backup-preference-primary"></a>Preferência de backup: primária

**Tipo de backup** | **Nó**
--- | ---
Completo | Primário
Diferencial | Primário
Registro |  Primário
Completo somente de cópia |  Primário

#### <a name="backup-preference-secondary-only"></a>Preferência de backup: somente secundário

**Tipo de backup** | **Nó**
--- | ---
Completo | Primário
Diferencial | Primário
Registro |  Secundário
Completo somente de cópia |  Secundário

#### <a name="backup-preference-secondary"></a>Preferência de backup: secundária

**Tipo de backup** | **Nó**
--- | ---
Completo | Primário
Diferencial | Primário
Registro |  Secundário
Completo somente de cópia |  Secundário

#### <a name="no-backup-preference"></a>Nenhuma preferência de Backup

**Tipo de backup** | **Nó**
--- | ---
Completo | Primário
Diferencial | Primário
Registro |  Secundário
Completo somente de cópia |  Secundário

## <a name="next-steps"></a>Próximas etapas

Saiba como [fazer backup de um banco de dados SQL Server](backup-azure-sql-database.md) em execução em uma VM do Azure.