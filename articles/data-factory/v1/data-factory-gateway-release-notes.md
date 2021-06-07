---
title: Notas de versão para o Gateway de Gerenciamento de Dados
description: Notas de versão do Gateway de Gerenciamento de Dados
author: nabhishek
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: abnarain
robots: noindex
ms.openlocfilehash: b5bf0c6d6996a67e8b0fec3ec51e31fef8eedf26
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100363909"
---
# <a name="release-notes-for-data-management-gateway"></a>Notas de versão para o Gateway de Gerenciamento de Dados
> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se estiver usando a versão atual do serviço do Data Factory, consulte [runtime de integração auto-hospedada na V2](../create-self-hosted-integration-runtime.md).

Um dos desafios da integração de dados moderna é mover dados entre o local e a nuvem. O Data Factory faz essa integração com o Gateway de Gerenciamento de Dados, que é um agente que você pode instalar localmente para habilitar a movimentação de dados híbridos.

Veja os artigos a seguir para obter informações detalhadas sobre o Gateway de Gerenciamento de Dados e como usá-lo:

*  [Gateway de gerenciamento de dados](data-factory-data-management-gateway.md)
*  [Mover dados entre o local e a nuvem usando a Azure Data Factory](data-factory-move-data-between-onprem-and-cloud.md)


## <a name="current-version"></a>VERSÃO ATUAL 
Não mantemos mais as Notas de versão aqui. Obtenha as notas de versão mais recentes [aqui](https://go.microsoft.com/fwlink/?linkid=853077)




## <a name="earlier-versions"></a>Versões anteriores
## <a name="21063477"></a>2.10.6347.7
### <a name="enhancements-"></a>Melhorias-
- Você pode adicionar entradas DNS para permitir o barramento de serviço em vez de permitir todos os endereços IP do Azure do seu firewall (se necessário). Você pode encontrar a respectiva entrada DNS em portal do Azure (Data Factory-> ' criar e implantar '-> ' gateways '-> "perurls" (em JSON)
- O conector HDFS agora dá suporte ao certificado público autoassinado, permitindo que você ignore a validação TLS.
- Corrigido: problema com gateway offline durante a atualização (devido à distorção do relógio)


## <a name="2963132"></a>2.9.6313.2
### <a name="enhancements-"></a>Melhorias-
-    Você pode adicionar entradas DNS para permitir o barramento de serviço em vez de permitir todos os endereços IP do Azure do seu firewall (se necessário). Mais detalhes aqui.
-    Agora você pode copiar dados de/para um único blob de blocos de até 4,75 TB, que é o tamanho máximo com suporte no blob de blocos. (o limite anterior era de 195 GB).
-    Correção: problema de memória insuficiente ao descompactar vários arquivos pequenos durante a atividade de cópia.
-    Corrigido: problema de índice fora do intervalo ao copiar do BD de documento para SQL Server com o recurso Idempotência.
-    Corrigido: o script de limpeza do SQL não funciona com SQL Server do assistente de cópia.
-    Correção: um nome de coluna com espaço no final não funcionava na atividade de cópia.

## <a name="28662833"></a>2.8.66283.3
### <a name="enhancements-"></a>Melhorias-
- Correção: problema com credenciais ausentes na reinicialização do computador do gateway.
- Correção: problema com o registro durante a restauração do gateway usando um arquivo de backup.


## <a name="2762401"></a>2.7.6240.1
### <a name="enhancements-"></a>Melhorias-
- Correção: leitura incorreta do valor nulo Decimal do Oracle como fonte.

## <a name="2661922"></a>2.6.6192.2
### <a name="whats-new"></a>Novidades
- Os clientes podem fornecer comentários sobre a experiência de registro de gateway.
- Suporte a um novo formato de compactação: ZIP (Deflate)

### <a name="enhancements-"></a>Melhorias-
- Melhoria de desempenho para o coletor Oracle, fonte HDFS.
- Correção de bug para atualização automática do gateway, capacidade de processamento paralelo do gateway.


## <a name="2561641"></a>2.5.6164.1
### <a name="enhancements"></a>Aprimoramentos
- Experiência de registro de gateway melhor e mais robusta – agora você pode acompanhar o status do progresso durante o processo de registro de gateway, o que torna a experiência de registro mais dinâmica.
- Melhoria no Processo de Restauração de Gateway – você ainda poderá recuperar o gateway, mesmo que não tenha o arquivo de backup do gateway com essa atualização. Isso exigiria a redefinição das credenciais de Serviço Vinculado no Portal.
- Correção de bug.

## <a name="2461511"></a>2.4.6151.1

### <a name="whats-new"></a>Novidades

- Agora você pode armazenar credenciais de fonte de dados localmente. As credenciais são criptografadas. As credenciais da fonte de dados podem ser recuperadas e restauradas usando o arquivo de backup que pode ser exportado do Gateway existente, tudo localmente.

### <a name="enhancements-"></a>Melhorias-

- Experiência de registro de Gateway melhorada e mais robusta.
- Suporte à detecção automática da configuração de QuoteChar para formato de Texto no assistente de cópia e melhoria da precisão de detecção do formato geral.

## <a name="2361002"></a>2.3.6100.2

- Suporte à detecção automática de firstRowAsHeader e SkipLineCount no assistente de cópia para arquivos de texto em HDFS e no sistema de arquivos local.
- Melhoria da estabilidade da conexão de rede entre o gateway e o Barramento de Serviço
- Algumas correções de bug


## <a name="2260721"></a>2.2.6072.1

*  Permite a configuração do proxy HTTP para o gateway usando o Gerenciador de Configurações do Gateway. Se configurado, o Blob do Azure, a Tabela do Azure, o Azure Data Lake e o DocumentDB serão acessados por meio do proxy HTTP.
*  Dá suporte ao tratamento de cabeçalho para TextFormat na cópia de dados dentro e fora do Blob do Azure, Azure Data Lake Store, Sistema de Arquivos Local e HDFS local.
*  Dá suporte à cópia de dados do Blob de Acréscimos e Blob de Páginas com o Blob de Blocos já com suporte.
*  Introduz um novo status de gateway **Online (Limitado)**, que indica se a funcionalidade principal do gateway funciona, com exceção do suporte à operação interativa do Assistente de Cópia.
*  Aprimora a robustez do registro de gateway usando a chave do Registro.

## <a name="216040"></a>2.1.6040.

*  O driver DB2 agora está incluído no pacote de instalação do gateway. Você não precisa instalá-lo separadamente.
*  O driver DB2 agora oferece suporte ao z/OS e DB2 para i (AS/400) junto com as plataformas já suportadas (Linux, Unix e Windows).
*  Dá suporte ao uso do Azure Cosmos DB como uma origem ou um destino para armazenamentos de dados locais
*  Dá suporte à cópia dos dados de/para o armazenamento de blobs quente/frio junto com a conta de armazenamento de uso geral já com suporte.
*  Permite que você se conecte a SQL Server por meio de gateway com privilégios de logon remoto.  

## <a name="2060131"></a>2.0.6013.1

*  Você pode selecionar o idioma/cultura a ser usada por um gateway durante a instalação manual.

*  Quando o gateway não funcionar conforme o esperado, você poderá optar por enviar logs de gateway dos últimos sete dias à Microsoft para facilitar a solução do problema. Se gateway não estiver conectado ao serviço de nuvem, você poderá optar por salvar e arquivar logs de gateway.  

*  Aprimoramentos na interface do usuário para o gerenciador de configuração de gateway:

    *  Torne o status do gateway mais visível na guia Página Inicial.

    *  Controles reorganizados e simplificados.

    *  Você pode copiar dados de um armazenamento usando a [ferramenta de cópia sem código](data-factory-copy-data-wizard-tutorial.md). Confira [Cópia em Etapas](data-factory-copy-activity-performance.md#staged-copy) para obter detalhes gerais sobre esse recurso.
*  Você pode usar Gerenciamento de Dados gateway para entrada de dados diretamente de um banco de SQL Server no Azure Machine Learning.

*  Aprimoramentos de desempenho

    * Melhore o desempenho de exibição de Esquema/Visualização no SQL Server na ferramenta de cópia sem código.

## <a name="11259531"></a>1.12.5953.1

*  Correções de bug

## <a name="11159181"></a>1.11.5918.1

*  O tamanho máximo do log de eventos do gateway foi aumentado de 1 MB para 40 MB.

*  Uma caixa de diálogo de aviso é exibida caso uma reinicialização seja necessária durante a atualização automática do gateway. Você pode optar por reiniciar logo em seguida ou mais tarde.

*  Em caso de falha da atualização automática, o instalador do gateway recupera a atualização automática três vezes no máximo.

*  Aprimoramentos de desempenho

    * Melhora no desempenho do carregamento de grandes tabelas de servidor local no cenário de cópia sem código.

*  Correções de bug

## <a name="11058921"></a>1.10.5892.1

*  Aprimoramentos de desempenho

*  Correções de bug

## <a name="1958652"></a>1.9.5865.2

*  Capacidade de atualização automática zero touch
*  Novo ícone de bandeja com indicadores de status do gateway
*  Capacidade de "atualizar agora" do cliente
*  Capacidade de definir o horário da agenda de atualização
*  Script do PowerShell para ativar/desativar a atualização automática
*  Suporte para o formato JSON  
*  Aprimoramentos de desempenho
*  Correções de bug

## <a name="1858221"></a>1.8.5822.1

*  Melhorar a experiência de solução de problemas
*  Aprimoramentos de desempenho
*  Correções de bug

### <a name="1757951"></a>1.7.5795.1

*  Aprimoramentos de desempenho
*  Correções de bug

### <a name="1757641"></a>1.7.5764.1

*  Aprimoramentos de desempenho
*  Correções de bug

### <a name="1657351"></a>1.6.5735.1

*  Suporte à fonte/coletor do HDFS local
*  Aprimoramentos de desempenho
*  Correções de bug

### <a name="1656961"></a>1.6.5696.1

*  Aprimoramentos de desempenho
*  Correções de bug

### <a name="1656761"></a>1.6.5676.1

*  Suporte a ferramentas de diagnóstico no Gerenciador de Configurações
*  Suporte a colunas de tabela para fontes de dados tabulares do Azure Data Factory
*  Suporte para o Azure Synapse Analytics para Azure Data Factory
*  Suporte Reclusivo em BlobSource e FileSource para o Azure Data Factory
*  Suporte a CopyBehavior – MergeFiles, PreserveHierarchy e FlattenHierarchy em BlobSink e FileSink com Cópia Binária para o Azure Data Factory
*  Suporte a relatórios de andamento de Atividade de Cópia do Azure Data Factory
*  Suporte a Validação de Conectividade de Fonte de Dados para Azure Data Factory
*  Correções de bug

### <a name="1656721"></a>1.6.5672.1

*  Suporte a nome de tabela para  fonte de dados ODBC para o Azure Data Factory
*  Aprimoramentos de desempenho
*  Correções de bug

### <a name="1656581"></a>1.6.5658.1

*  Suporte a Coletor de Arquivo para o Azure Data Factory
*  Suporte à preservação de hierarquia na cópia binária para o Azure Data Factory
*  Suporte a Idempotência de Atividade de Cópia para o Azure Data Factory
*  Correções de bug

### <a name="1656401"></a>1.6.5640.1

*  Suporte a três ou mais fontes de dados para o Azure Data Factory (ODBC, OData, HDFS)
*  Suporte ao caractere de aspas no analisador de csv para o Azure Data Factory
*  Suporte à compactação (BZip2)
*  Correções de bug

### <a name="1556121"></a>1.5.5612.1

*  Suporte a cinco bancos de dados relacionais do Azure Data Factory (MySQL, PostgreSQL, DB2, Teradata e Sybase)
*  Suporte à compactação (Gzip e Deflate)
*  Aprimoramentos de desempenho
*  Correções de bug

### <a name="1455491"></a>1.4.5549.1

*  Adicionar suporte a fonte de dados Oracle para o Azure Data Factory
*  Aprimoramentos de desempenho
*  Correções de bug

### <a name="1454921"></a>1.4.5492.1

*  Binário unificado que dá suporte aos serviços Microsoft Azure Data Factory e Office 365 Power BI
*  Refinar a interface do usuário de Configuração e o processo de registro
*  Azure Data Factory – suporte a Ingresso e Egresso do Azure para fonte de dados do SQL Server

### <a name="1253031"></a>1.2.5303.1

*  Correção do problema de tempo limite para dar suporte a conexões de fontes de dados mais demoradas.

### <a name="1155268"></a>1.1.5526.8

*  Requer o .NET Framework 4.5.1 como pré-requisito durante a instalação.

### <a name="1051442"></a>1.0.5144.2

*  Nenhuma alteração que afeta os cenários do Azure Data Factory.
