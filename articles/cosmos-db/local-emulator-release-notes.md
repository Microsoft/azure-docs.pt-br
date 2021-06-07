---
title: Download e notas sobre a versão do Emulador do Azure Cosmos DB
description: Obtenha as notas sobre a versão do Emulador do Azure Cosmos DB para diferentes versões e informações de download.
ms.service: cosmos-db
ms.topic: conceptual
author: milismsft
ms.author: adrianmi
ms.date: 09/21/2020
ms.openlocfilehash: 442fa1c76d4667a54f1c13b411b1c052c9a5b134
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105566635"
---
# <a name="azure-cosmos-db-emulator---release-notes-and-download-information"></a>Emulador do Azure Cosmos DB – Notas sobre a versão e informações de download
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Este artigo mostra as notas sobre a versão do Emulador do Azure Cosmos DB com uma lista de atualizações de recursos que foram feitas em cada versão. Também lista a versão mais recente do emulador para baixar e usar.

## <a name="download"></a>Baixar

| | Link |
|---------|---------|
|**Download do MSI**|[Centro de Download da Microsoft](https://aka.ms/cosmosdb-emulator)|
|**Introdução**|[Desenvolvimento local com o Emulador do Azure Cosmos DB](local-emulator.md)|

## <a name="release-notes"></a>Notas de versão

### <a name="21111-22-february-2021"></a>2.11.11 (22 de fevereiro de 2021)

 - Essa versão atualiza o conteúdo do Data Explorer local para a versão mais recente do portal do Azure.


### <a name="21110-5-january-2021"></a>2.11.10 (5 de janeiro de 2021)

 - Essa versão atualiza o conteúdo do Data Explorer local para a versão mais recente do portal do Azure e adiciona uma nova opção pública, "/ExportPemCert", que permite que o usuário do emulador exporte diretamente o certificado do emulador público como um arquivo .PEM.

### <a name="2119-3-december-2020"></a>2.11.9 (3 de dezembro de 2020)

 - Esta versão aborda dois problemas com a funcionalidade de Emulador do Azure Cosmos DB além da atualização de conteúdo geral que reflete os recursos e aprimoramentos mais recentes no Azure Cosmos DB:
 * Correção de um problema em que as solicitações de conteúdo de documento grande falhavam quando o modo direto e aplicativos cliente Java eram usados.
 * Correção de um problema de conectividade com o ponto de extremidade do MongoDB versão 3.6 quando era direcionado por aplicativos baseados em .NET.

### <a name="2118-6-november-2020"></a>2.11.8 (6 de novembro de 2020)

 - Esta versão inclui uma atualização para o Data Explorer do emulador do Cosmos e corrige um problema em que os clientes do TLS 1.3 tentavam abrir o Data Explorer.

### <a name="2116-6-october-2020"></a>2.11.6 (6 de outubro de 2020)

 - Esta versão trata de um problema relacionado à simultaneidade, quando vários contêineres podem ser criados ao mesmo tempo. Nesses casos, os dados do emulador ficam em um estado corrompido e as solicitações à API a seguir para o ponto de extremidade do emulador podem falhar com erros de "serviço indisponível", exigindo uma reinicialização e uma redefinição dos dados locais do emulador.

### <a name="2115-23-august-2020"></a>2.11.5 (23 de agosto de 2020)

Esta versão adiciona duas novas opções de inicialização do emulador Cosmos: 

* "/EnablePreview" – habilita a versão prévia dos recursos para o emulador. A versão prévia dos recursos que ainda estão em desenvolvimento e podem ser acessados via CI e gravação de exemplos.
* "/EnableAadAuthentication" – habilita o emulador para aceitar tokens personalizados do Azure Active Directory como uma alternativa às chaves primárias do Azure Cosmos. Este recurso ainda está em desenvolvimento. Atualmente, não há suporte para atribuições de função específicas e outras configurações relacionadas a permissões.

### <a name="2112-07-july-2020"></a>2.11.2 (07 de julho de 2020)

- Esta versão altera o modo de coleta dos rastreamentos ETL necessários na solução de problemas do emulador do Cosmos. As ferramentas do WPR (Runtime de Desempenho do Windows) agora são as ferramentas padrão para a captura de rastreamentos baseados em ETL, ao passo que a antiga captura baseada em LOGMAN foi preterida. Essa alteração é, em parte, necessária porque as últimas atualizações de segurança do Windows tiveram um impacto inesperado sobre o funcionamento do LOGMAN quando executado por meio do emulador do Cosmos.

### <a name="2111-10-june-2020"></a>2.11.1 (10 de junho de 2020)

- Esta versão corrige alguns bugs relacionados ao Data Explorer do emulador. Em determinados casos, ao usar o Data Explorer do emulador por meio de um navegador da Web, não será possível a conexão com o ponto de extremidade do emulador do Cosmos e todas as ações relacionadas, como a criação de um banco de dados ou de um contêiner, resultarão em erro. O segundo problema corrigido está relacionado à criação de um item usando um arquivo JSON com a ação de upload do Data Explorer.

### <a name="2110"></a>2.11.0

- Esta versão introduz o suporte para taxa de transferência provisionada com dimensionamento automático. Esses novos recursos incluem a capacidade de definir um nível máximo personalizado de taxa de transferência provisionada em unidades de solicitação (RU/s), habilitar o dimensionamento automático em bancos de dados e contêineres existentes e suporte programático por meio de SDKs do Azure Cosmos DB.
- Correção de um problema ao consultar um grande volume de documentos (mais de 1 GB), em que o emulador falha com o código de status de erro interno 500.

### <a name="292"></a>2.9.2

- Esta versão corrige um bug ao habilitar o suporte para o ponto de extremidade do MongoDb versão 3.2. Ela também adiciona suporte para gerar rastreamentos de ETL para fins de solução de problemas usando WPR em vez de LOGMAN.

### <a name="291"></a>2.9.1

- Essa versão corrige alguns problemas no suporte à API de consulta e restaura a compatibilidade com sistemas operacionais mais antigos, como o Windows Server 2012.

### <a name="290"></a>2.9.0

- Esta versão adiciona a opção para definir a consistência com o prefixo consistente e aumentar os limites máximos para usuários e permissões.

### <a name="272"></a>2.7.2

- Esta versão adiciona suporte ao servidor do MongoDB versão 3.6 para o Emulador Cosmos. Para iniciar um ponto de extremidade do MongoDB que tenha como alvo a versão 3.6 do serviço, inicie o emulador de uma linha de comando do administrador com a opção "/EnableMongoDBEndpoint=3.6".

### <a name="270"></a>2.7.0

- Esta versão corrige uma regressão que impediu os usuários de executar consultas na conta da API do SQL no emulador ao usar clientes .NET com base em .NET Core ou x86.

### <a name="246"></a>2.4.6

- Esta versão fornece paridade com os recursos do serviço Azure Cosmos disponíveis desde julho de 2019, com as exceções indicadas em [Desenvolvimento local com o Emulador do Azure Cosmos DB](local-emulator.md). Ela também corrige vários bugs relacionados ao desligamento do emulador quando invocada pela linha de comando e substituições de endereço IP interno para clientes do SDK que usam a conectividade de modo direto.

### <a name="243"></a>2.4.3

- Inicialização do serviço MongoDB desabilitada por padrão. Somente o ponto de extremidade do SQL está habilitado por padrão. O usuário deve iniciar o ponto de extremidade manualmente usando a opção de linha de comando /EnableMongoDbEndpoint" do emulador. Agora, é como todos os outros pontos de extremidade, como Gremlin, Cassandra e tabela.
- Corrigido um bug no emulador ao iniciar com /AllowNetworkAccess", em que os pontos de extremidade de Gremlin, Cassandra e tabela não processavam corretamente as solicitações de clientes externos.
- Adicione portas de conexão direta às configurações de regras de firewall.

### <a name="240"></a>2.4.0

- Corrigido um problema de falha do emulador ao iniciar quando aplicativos de monitoramento de rede, como cliente do pulso, estão presentes no computador host.
