---
title: Criptografia em repouso do Azure Cosmos DB
description: Saiba como o Azure Cosmos DB fornece criptografia de dados em repouso e como ela é implementada.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: 07d5aa752d6613f6733a44c9b34e48f537eb67ae
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60889562"
---
# <a name="data-encryption-in-azure-cosmos-db"></a>Criptografia de dados do Azure Cosmos DB 

A criptografia em repouso é uma frase que normalmente se refere à criptografia de dados em dispositivos de armazenamento não volátil, como SSDs (unidades de estado sólido) e HDDs (discos rígidos). O Cosmos DB armazena seus bancos de dados principais em SSDs. Seus anexos de mídia e backups são armazenados no armazenamento de Blobs do Azure, do qual geralmente é feito backup por HDDs. Com o lançamento da criptografia em repouso no Cosmos DB, todos os bancos de dados, anexos de mídia e backups são criptografados. Os dados agora são criptografados em trânsito (pela rede) e em repouso (armazenamento não volátil), fornecendo criptografia de ponta a ponta.

Como um serviço de PaaS, o Cosmos DB é muito fácil de usar. Como todos os dados de usuário armazenados no Cosmos DB são criptografados em repouso e em transporte, você não precisa realizar ação alguma. Em outras palavras, a criptografia em repouso é "ativada" por padrão. Não existem controles para ativá-lo ou desativá-lo. Fornecemos esse recurso enquanto continuamos atendendo a nossos [SLAs de desempenho e disponibilidade](https://azure.microsoft.com/support/legal/sla/cosmos-db).

## <a name="implementation-of-encryption-at-rest-for-azure-cosmos-db"></a>Implementação de criptografia em repouso para Azure Cosmos DB

A criptografia em repouso é implementada usando várias tecnologias de segurança, incluindo sistemas seguros de armazenamento de chaves, redes criptografadas e APIs criptográficas. Os sistemas que descriptografam e processam dados precisam se comunicar com sistemas que gerenciam chaves. O diagrama mostra como o armazenamento de dados criptografados e o gerenciamento de chaves são separados. 

![Diagrama de design](./media/database-encryption-at-rest/design-diagram.png)

O fluxo básico de uma solicitação de usuário é o seguinte:
- A conta do banco de dados do usuário é preparada e as chaves de armazenamento são recuperadas por meio de uma solicitação ao Provedor de Recursos do Serviço de Gerenciamento.
- Um usuário cria uma conexão ao Cosmos DB por meio de HTTPS/transporte seguro. (Os SDKs abstraem os detalhes).
- O usuário envia um documento JSON para ser armazenado pela conexão segura criada anteriormente.
- O documento JSON é indexado, a menos que o usuário tenha desativado a indexação.
- O documento JSON e os dados de índice são gravados no armazenamento seguro.
- Periodicamente, os dados são lidos no armazenamento seguro e o backup é feito Repositório de Blobs Criptografados do Azure.

## <a name="frequently-asked-questions"></a>Perguntas frequentes

### <a name="q-how-much-more-does-azure-storage-cost-if-storage-service-encryption-is-enabled"></a>P: Quanto custa a mais o Armazenamento do Azure se a Criptografia do Serviço de Armazenamento for habilitada?
R: Não há nenhum custo adicional.

### <a name="q-who-manages-the-encryption-keys"></a>P: Quem gerencia as chaves de criptografia?
R: As chaves são gerenciadas pela Microsoft.

### <a name="q-how-often-are-encryption-keys-rotated"></a>P: Com que frequência as chaves de criptografia são trocadas?
R: A Microsoft tem um conjunto de diretrizes internas para rotação de chave de criptografia, que o Cosmos DB segue. As diretrizes específicas não são publicadas. A Microsoft publica o [SDL (Security Development Lifecycle)](https://www.microsoft.com/sdl/default.aspx), que é visto como um subconjunto de orientação interna e tem práticas recomendadas úteis para desenvolvedores.

### <a name="q-can-i-use-my-own-encryption-keys"></a>P: Posso usar minhas próprias chaves de criptografia?
R: O Cosmos DB é um serviço PaaS e trabalhamos intensamente para manter o serviço fácil de usar. Observamos que essa pergunta geralmente é feita como uma pergunta substituta para o cumprimento de um requisito de conformidade, como as normas PCI-DSS. Como parte da criação desse recurso, trabalhamos com auditores de conformidade para garantir que os clientes que usam o Cosmos DB atendam a seus requisitos sem a necessidade de gerenciar as próprias chaves.

### <a name="q-what-regions-have-encryption-turned-on"></a>P: Em quais regiões a criptografia está ativada?
R: A criptografia está ativada para todos os dados do usuário em todas as regiões do Azure Cosmos DB.

### <a name="q-does-encryption-affect-the-performance-latency-and-throughput-slas"></a>P: A criptografia afeta os SLAs de latência de desempenho e produtividade?
R: Não há nenhum impacto ou alteração nos SLAs de desempenho agora que a criptografia em repouso está habilitada em todas as contas novas e existentes. Você pode ler mais na página [SLA do Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db) para ver as garantias mais recentes.

### <a name="q-does-the-local-emulator-support-encryption-at-rest"></a>P: O emulador local dá suporte à criptografia em repouso?
R: O emulador é uma ferramenta de desenvolvimento/teste independente e não usa os serviços de gerenciamento de chaves utilizados pelo serviço gerenciado do Cosmos DB. Nossa recomendação é habilitar o BitLocker em unidades em que você está armazenando dados confidenciais de teste do emulador. O [emulador dá suporte à alteração do diretório de dados padrão](local-emulator.md), bem como ao uso de um local conhecido.

## <a name="next-steps"></a>Próximas etapas

Para obter uma visão geral da segurança do Cosmos DB e as últimas melhorias, confira [Segurança de banco de dados do Azure Cosmos DB](database-security.md).
Para obter mais informações sobre as certificações da Microsoft, confira a [Central de Confiabilidade do Azure](https://azure.microsoft.com/support/trust-center/).
