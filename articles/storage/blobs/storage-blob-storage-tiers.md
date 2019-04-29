---
title: Camadas quente, fria e de outros níveis de acesso para blobs – armazenamento do Azure
description: Camadas quente, fria e arquivar as camadas de acesso para contas de armazenamento do Azure.
services: storage
author: mhopkins-msft
ms.service: storage
ms.topic: conceptual
ms.date: 03/23/2019
ms.author: mhopkins
ms.subservice: blobs
ms.openlocfilehash: 9be62e09fbf65081f166c89f5358cdb6677c22c6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61426792"
---
# <a name="azure-blob-storage-hot-cool-and-archive-access-tiers"></a>O armazenamento de BLOBs do Azure: quente, frio e arquivar as camadas de acesso

Armazenamento do Azure oferece as camadas de acesso diferentes, que permitem que você armazene dados de objeto de blob da maneira mais econômica. As camadas de acesso disponíveis incluem:

- **Hot** - otimizado para armazenar dados acessados com frequência.
- **Legal** - otimizado para armazenar dados acessados raramente e armazenados pelo menos 30 dias.
- **Arquivo morto** - otimizado para armazenar dados acessados raramente e armazenados pelo menos 180 dias com os requisitos de latência flexível (na ordem de horas).

As seguintes considerações se aplicam para as camadas de acesso diferentes:

- A camada de acesso de arquivo morto está disponível apenas no nível do blob e não no nível de conta de armazenamento.
- Os dados na camada de acesso esporádico podem tolerar disponibilidade um pouco menor, mas ainda exigem alta durabilidade e características de acesso de tempo e a taxa de transferência semelhantes de dados ativos. Para dados estáticos, um contrato de nível de serviço de disponibilidade um pouco menor (SLA) e os mais altos de acesso os custos em comparação comparados dados mais acessados são compensações aceitáveis para reduzir os custos de armazenamento.
- O armazenamento de arquivos está offline e oferece os custos de armazenamento mais baixos, mas também os custos de acesso mais altos.
- Apenas as camadas de acesso quente e frio podem ser definidas no nível da conta.
- Quente, fria e arquivo morto camadas podem ser definido no nível do objeto.

Os dados armazenados na nuvem aumentam em ritmo exponencial. Para gerenciar os custos de suas necessidades cada vez maiores de armazenamento, é útil organizar seus dados com base em atributos como frequência de acesso e período de retenção planejado para otimizar os custos. Dados armazenados na nuvem podem ser diferentes em termos de como ele tem gerados, processados e acessados durante seu ciclo de vida. Alguns dados são ativamente acessados e modificados durante seu ciclo de vida. Alguns dados são acessados com frequência no início do seu tempo de vida, mas esse acesso cai drasticamente à medida que os dados envelhecem. Alguns dados permanecem ociosos na nuvem e é raramente, ou nunca, acessados depois que ele é armazenado.

Cada um desses cenários de acesso de dados se beneficia de uma camada de acesso diferentes que é otimizada para um padrão de acesso específico. Com quente, fria e as camadas de acesso de arquivo morto, endereços de armazenamento de BLOBs do Azure essa necessidade de camadas de acesso diferenciado com modelos de preços separados.

## <a name="storage-accounts-that-support-tiering"></a>Contas de armazenamento que dão suporte a camadas

Você pode apenas seus dados de armazenamento de objeto para quente, fria em camadas ou arquivar no armazenamento de BLOBs e de uso geral v2 (GPv2) de contas. Contas de Uso geral v1 (GPv1) não dão suporte a camadas. No entanto, você pode converter facilmente contas de armazenamento de Blob ou de GPv1 existentes para contas de GPv2 por meio de um processo em um único clique no portal do Azure. O GPv2 fornece uma nova estrutura de preços para blobs, arquivos e as filas e acesso a uma variedade de outros novos recursos de armazenamento. Além disso, alguns novos recursos e cortes de preços serão oferecidos apenas em contas de GPv2. Portanto, você deve avaliar usar contas de GPv2, mas apenas usá-los depois de revisar os preços de todos os serviços. Algumas cargas de trabalho podem ser mais caras em GPv2 do que em GPv1. Para saber mais, confira [Visão geral da conta de armazenamento do Azure](../common/storage-account-overview.md).

Armazenamento de BLOBs e GPv2 expõem de contas do **camada de acesso** atributo no nível da conta. Este atributo permite que você especifique a camada de acesso padrão como frequente ou esporádica para qualquer blob na conta de armazenamento que não tenha uma camada explícita definida no nível do objeto. Para objetos com a camada definida no nível do objeto, a camada da conta não será aplicada. Camada de arquivo pode ser aplicada apenas no nível do objeto. Você pode alternar entre as camadas de acesso a qualquer momento.

## <a name="premium-performance-block-blob-storage"></a>Armazenamento de blob de bloco de desempenho Premium

O armazenamento de blob de bloco de desempenho Premium disponibiliza dados acessados com frequência por meio de hardware de alto desempenho. Os dados nesse nível de desempenho são armazenados em unidades de estado sólido (SSDs), que são otimizadas para consistente e baixa latência. Os SSDs fornecem taxas mais altas de transacionais e de taxa de transferência em comparação comparada discos rígidos tradicionais.

O armazenamento de blob de bloco de desempenho Premium é ideal para cargas de trabalho que exigem tempos de resposta rápida e consistente. Ele é ideal para cargas de trabalho que executam muitas transações pequenas, como a captura de dados de telemetria, mensagens e transformação de dados. Dados que envolva os usuários finais, como edição de vídeo interativo, conteúdo web estático e transações on-line também são bons candidatos.

O armazenamento de blob de bloco de desempenho Premium está disponível somente via o tipo de conta de armazenamento de blob de bloco e não oferece suporte a disposição em camadas para frequente, esporádico ou arquivo as camadas de acesso.

## <a name="hot-access-tier"></a>Camada de acesso quente

A camada de acesso frequente tem custos de armazenamento mais altos que as camadas fria e arquivo, mas os custos de acesso a mais baixos. Cenários de uso de exemplo para a camada de acesso quente incluem:

- Dados que estão em uso ativo ou devem ser acessados (lidos e gravados) com frequência.
- Dados preparados para processamento e eventual migração para a camada de acesso esporádico.

## <a name="cool-access-tier"></a>Camada de acesso frio

A camada de acesso esporádico tem custos de armazenamento mais baixos e custos mais altos de acesso, comparados ao armazenamento frequente. Essa camada destina-se aos dados que permanecerão na camada esporádica por pelo menos 30 dias. Cenários de uso de exemplo para a camada de acesso esporádico incluem:

- Conjuntos de dados de recuperação de desastre e de backup de curto prazo.
- Conteúdo de mídia mais antigo que não é mais exibido frequentemente, mas ainda deve estar disponível imediatamente quando acessado.
- Grandes conjuntos de dados que precisam ser armazenados de forma econômica enquanto mais dados estão sendo obtidos para processamento futuro. (*por exemplo*, armazenamento de longo prazo de dados científicos; dados brutos de telemetria de uma instalação de manufatura)

## <a name="archive-access-tier"></a>Camada de acesso ao arquivo

A camada de acesso de arquivo morto tem o menor custo de armazenamento e custos mais altos de recuperação dados em comparação com as camadas quentes e fria. Essa camada destina-se aos dados que podem tolerar várias horas de latência de recuperação e permanecerão na camada de arquivos por pelo menos 180 dias.

Embora seja um blob no armazenamento de arquivos, os dados de blob está offline e não podem ser lido, copiado, substituído ou modificado. Você não pode tirar instantâneos de um blob no armazenamento de arquivos. No entanto, os metadados de blob permanecem online e disponíveis, permitindo que você liste o blob e suas propriedades. Para blobs em arquivo, as únicas operações válidas são GetBlobProperties, GetBlobMetadata, ListBlobs, SetBlobTier e DeleteBlob.

Cenários de uso de exemplo para a camada de acesso de arquivo morto incluem:

- Backup de longo prazo, backup secundário e conjuntos de dados de arquivamento 
- Dados originais (brutos) que devem ser preservados, mesmo após serem processados em formato utilizável final. (*por exemplo*, arquivos de mídia brutos após transcodificação em outros formatos)
- Dados de conformidade e arquivamento que precisam ser armazenados por um longo tempo e quase nunca são acessados. (*Por exemplo*, filmagens de câmeras de segurança, X raios/ressonâncias magnéticas para as organizações de saúde, gravações de áudio e transcrições de cliente chamadas para serviços financeiros)

### <a name="blob-rehydration"></a>Reidratação de blob

Para ler dados no armazenamento de arquivo morto, primeiro você deve alterar a camada do blob para quente ou fria. Esse processo é conhecido como reidratação e pode levar até 15 horas para ser concluído. Tamanhos grandes de blob são recomendados para otimizar o desempenho. Reidratar vários blobs pequenos simultaneamente pode inserir um tempo adicional.

Durante a reidratação, você pode marcar a propriedade de blob **Status do arquivo** para confirmar se a camada foi alterada. O status exibe "reidratação pendentes para camada quente" ou "reidratação pendente para camada fria" dependendo da camada de destino. Após a conclusão, a propriedade status do arquivo é removida, e a propriedade de blob de **Nível de Acesso** reflete a nova camada frequente ou esporádica.  

## <a name="blob-level-tiering"></a>Camada no nível do blob

As camadas no nível do blob permitem que você altere a camada de seus dados no nível de objeto usando uma única operação chamada [Definir Nível de Blob](/rest/api/storageservices/set-blob-tier). Você pode alterar facilmente o nível de acesso de um blob entre as camadas quente, fria ou de arquivo morto como alteração de padrões de uso, sem a necessidade de mover dados entre contas. Todas as alterações de camadas acontecem imediatamente. No entanto, reidratar um blob dos arquivos pode levar várias horas.

A hora da última alteração na camada de blob é exposta por meio da propriedade do blob **Acessar Hora de Alteração da Camada**. Se um blob estiver na camada de arquivo, ele não pode ser substituído, portanto, o carregamento do mesmo blob não é permitido neste cenário. Você pode substituir um blob em uma camada quente ou fria, nesse caso o novo blob herda a camada do blob que foi substituído.

Blobs em todas as camadas de acesso três podem coexistir na mesma conta. Qualquer blob que não tenha uma camada atribuída explicitamente herda a camada da configuração de nível de acesso da conta. Se a camada de acesso for inferida da conta, você verá a propriedade de blob **Camada de Acesso Inferida** definida como "true", e a propriedade de blob **Camada de Acesso** corresponde à camada da conta. No portal do Azure, a camada de acesso inferida a propriedade é exibida com a camada de acesso de blob (por exemplo, **frequente (inferido)** ou **esporádico (inferido)**).

> [!NOTE]
> O armazenamento de arquivos e as camadas no nível do blob só oferecem suporte aos blobs de bloco. Também não é possível alterar a camada de um blob de blocos que tenha instantâneos.

> [!NOTE]
> Dados armazenados em uma conta de armazenamento de blob de bloco no momento, não podem ser disposto em camadas para frequente, esporádica ou de arquivo usando [definir nível de Blob](/rest/api/storageservices/set-blob-tier) ou usando o gerenciamento de ciclo de vida de armazenamento de BLOBs do Azure.
> Para mover dados, você deve copiar de forma síncrona blobs da conta de armazenamento de blob de bloco para a camada de acesso quente em uma conta diferente usando o [colocar bloco da API de URL](/rest/api/storageservices/put-block-from-url) ou uma versão do AzCopy que dá suporte a essa API.
> A API *Colocar bloco pela URL* copia dados sincronicamente no servidor, o que significa que a chamada é concluída apenas depois que todos os dados são movidos do local do servidor de origem para o local de destino.

### <a name="blob-lifecycle-management"></a>Gerenciamento de ciclo de vida de blob

Gerenciamento de ciclo de vida de armazenamento de blob oferece uma política avançada, baseada em regra, que você pode usar para fazer a transição de seus dados para a camada de acesso melhor e expirar os dados no final do ciclo de vida. Ver [gerenciar o ciclo de vida de armazenamento de BLOBs do Azure](storage-lifecycle-management-concepts.md) para saber mais.  

### <a name="blob-level-tiering-billing"></a>Cobrança da camada no nível do blob

Quando um blob é movido para uma camada mais esporádica (frequente -> esporádico, frequente -> arquivos ou esporádico -> arquivos), a operação é cobrada como uma operação de gravação na camada de destino, em que os encargos pela operação de gravação (por 10.000) e gravação de dados (por GB) da camada de destino são aplicados.

Quando um blob é movido para uma camada mais frequente (arquivo -> esporádico, arquivo -> frequente ou esporádico -> frequente), a operação é cobrada como uma leitura da camada de origem, em que se aplicam a operação de leitura (por 10.000) e os encargos de recuperação (por GB) de dados da camada de origem. A tabela a seguir resume como as alterações na camada são cobradas.

| | **Encargos de gravação (operação + acesso)** | **Encargos de leitura (operação + acesso)**
| ---- | ----- | ----- |
| **Direção de SetBlobTier** | frequente -> esporádico, frequente -> arquivo morto, legal -> arquivo morto | arquivo -> esporádico, arquivo -> frequente, esporádico -> frequente

Caso altere a camada de conta de frequente para esporádico, você será cobrado por operações de gravação (por 10.000) para todos os blobs sem uma camada de conjunto apenas em contas de GPv2. Não há nenhum encargo para essa alteração em contas de Armazenamento de Blobs. Você será cobrado por operações de leitura (por 10.000) e por recuperação de dados (por GB) caso altere a conta de Armazenamento de Blobs ou de GPv2 de esporádica para frequente. Encargos de exclusão antecipada para qualquer blob tirado das camadas esporádica ou de arquivo também podem incorrer.

### <a name="cool-and-archive-early-deletion"></a>Exclusão antecipada esporádica e de arquivo

Além dos encargos por GB e por mês, qualquer blob que seja colocado na camada esporádica (apenas para contas de GPv2) está sujeito a um período inicial de exclusão moderada de 30 dias, e qualquer blob que seja colocado na camada de arquivo está sujeito a um período de exclusão antecipada do arquivo de 180 dias. A cobrança é proporcional. Por exemplo, se um blob for movido para o arquivo e depois for excluído ou movido para a camada frequente após 45 dias, será cobrada um valor pela exclusão antecipada equivalente a 135 dias (180 menos 45) de armazenamento de blob no arquivo.

Você poderá calcular a exclusão antecipada usando a propriedade de blob, **creation-time**, se não tiverem ocorrido alterações à camada de acesso. Caso contrário, você pode usar a camada de acesso foi modificada pela última vez para esporádica ou de arquivo, exibindo a propriedade de blob: **camada de acesso-hora alteração**. Para obter mais informações sobre as propriedades de blob, confira [Obter Propriedades de Blob](https://docs.microsoft.com/rest/api/storageservices/get-blob-properties).

## <a name="comparing-block-blob-storage-options"></a>Comparando opções de armazenamento de blob de bloco

As camadas de acesso a seguinte tabela mostra uma comparação entre o armazenamento de blob de bloco de desempenho premium e frequente, esporádico e arquivo.

|                                           | **Desempenho Premium** | **Camada quente** | **Camada fria** | **Camada de arquivo morto**
| ----------------------------------------- | ---------------- | ------------ | ----- | ----- |
| **Disponibilidade**                          | 99,9%            | 99,9%        | 99% | N/D |
| **Disponibilidade** <br> **(Leituras de RA-GRS)**  | N/D              | 99,99%       | 99,9% | N/D |
| **Encargos de uso**                         | Custos de armazenamento maiores, menores custos e os custos de transação | Custos de armazenamento maiores, custos de acesso e de transações menores | Custos de armazenamento menores, custos de acesso e de transações maiores | Custos de armazenamento mais baixos, custos de acesso e de transações mais altos |
| **Tamanho mínimo do objeto**                   | N/D | N/D | N/D | N/D |
| **Duração mínima de armazenamento**              | N/D | N/D | 30 dias (somente GPv2) | 180 dias
| **Latência** <br> **(Tempo até o primeiro byte)** | Milissegundos de dígito único | milissegundos | milissegundos | < 15 horas

> [!NOTE]
> Para destinos de escalabilidade e desempenho, consulte [metas de desempenho e escalabilidade do armazenamento do Azure para contas de armazenamento](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

## <a name="quickstart-scenarios"></a>Cenários de início rápido

Nesta seção, os cenários a seguir são demonstrados usando o Portal do Azure:

- Como alterar a camada de acesso de conta padrão de uma conta de Armazenamento de Blobs ou de GPv2.
- Como alterar a camada de um blob em uma conta de Armazenamento de Blobs ou de GPv2.

### <a name="change-the-default-account-access-tier-of-a-gpv2-or-blob-storage-account"></a>Alterar a camada de acesso de conta padrão de uma conta de Armazenamento de Blobs ou de GPv2

1. Entre no [Portal do Azure](https://portal.azure.com).

1. Para navegar até sua conta de armazenamento, selecione Todos os Recursos e sua conta de armazenamento.

1. Na folha Configurações, clique em **Configuração** para exibir e/ou alterar a configuração da conta.

1. Selecione a camada de acesso correto para suas necessidades: Defina a **Camada de acesso** como **Esporádico** ou **Frequente**.

1. Clique em **Salvar** na parte superior da folha.

### <a name="change-the-tier-of-a-blob-in-a-gpv2-or-blob-storage-account"></a>Alterar a camada de um blob em uma conta de armazenamento de BLOBs ou de GPv2

1. Entre no [Portal do Azure](https://portal.azure.com).

1. Para navegar até seu blob em sua conta de armazenamento, selecione **todos os recursos**, selecione sua conta de armazenamento, selecione seu contêiner e, em seguida, selecione o blob.

1. No **propriedades de Blob** folha, selecione o **camada de acesso** menu suspenso para selecionar o **quente**, **legal**, ou **arquivo morto**  camada de acesso.

1. Clique em **Salvar** na parte superior da folha.

## <a name="pricing-and-billing"></a>Preços e cobrança

Todas as contas de armazenamento usam um modelo de preços para o Armazenamento de Blobs com base na camada de cada blob. Tenha em mente as seguintes considerações de cobrança:

- **Custos de armazenamento**: Além da quantidade de dados armazenados, o custo de armazenamento de dados varia, dependendo da camada de acesso. O custo por gigabyte diminui conforme a camada fica mais esporádica.
- **Custos de acesso a dados**: os encargos de acesso a dados aumentam conforme a camada fica mais esporádica. Para dados na camada de acesso esporádico e de arquivos, você é cobrado um encargo de acesso a dados por gigabyte para leituras.
- **Custos de transação**: há um encargo por transação para todas as camadas que aumenta à medida que a camada fica mais esporádica.
- **Custos de transferência de dados com replicação geográfica**: isso só se aplica a contas com replicação geográfica configurada, incluindo GRS e RA-GRS. A transferência de dados de replicação geográfica acarreta um encargo por gigabyte.
- **Custos de transferência de dados de saída**: transferências de dados de saída (dados que são transferidos para fora de uma região do Azure) acarretam a cobrança por uso de largura de banda por gigabyte, de forma consistente com as contas de armazenamento de finalidade geral.
- **A alteração da camada de acesso**: Alteração da camada de acesso de conta de fria para quente acarretará um encargo igual à leitura de todos os dados existentes na conta de armazenamento. No entanto, a alteração da camada de acesso à conta de frequente para esporádico gera uma cobrança igual à gravação de todos os dados na camada de acesso esporádico (somente contas GPv2).

> [!NOTE]
> Para saber mais informações sobre preços para contas de Armazenamento de Blobs, confira a página [Preços de Armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/). Para saber mais informações sobre os encargos de transferência de dados de saída, confira a página [Detalhes de preços de transferências de dados](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="faq"></a>Perguntas frequentes

**Devo usar contas de Armazenamento de Blobs ou de GPv2 se quiser colocar meus dados em camadas?**

Recomendamos que você use o GPv2 em vez de contas de Armazenamento de Blobs para camadas. O GPv2 dá suporte a todos os recursos aos quais as contas de Armazenamento de Blobs dão suporte e muito mais. O preço entre o Armazenamento de Blobs e o GPv2 são quase idênticos, mas alguns novos recursos e cortes de preços só estarão disponíveis em contas de GPv2. Contas de GPv1 não dão suporte a camadas.

A estrutura de preços entre contas de GPv1 e GPv2 é diferente, e os clientes devem avaliar cuidadosamente antes de decidir usar contas de GPv2. Você pode converter facilmente uma conta existente de Armazenamento de Blobs ou de GPv1 em GPv2 por meio de um processo simples de um clique. Para saber mais, confira [Visão geral da conta de armazenamento do Azure](../common/storage-account-overview.md).

**Posso armazenar objetos em todas as três (frequente, esporádica e arquivo) as camadas na mesma conta de acesso?**

Sim. O atributo **Camada de Acesso** definido no nível de conta é a camada padrão aplicada a todos os objetos na conta sem uma camada de conjunto explícita. No entanto, camadas no nível do blob permitem que você defina a camada de acesso no nível de objeto, independentemente da configuração de camada de acesso da conta. Os BLOBs em qualquer uma das camadas de acesso de três (frequente, esporádica ou arquivo morto) podem existir na mesma conta.

**Posso alterar a camada de acesso padrão da minha conta de armazenamento de BLOBs ou de GPv2?**

Sim, você pode alterar a camada de acesso padrão definindo a **camada de acesso** atributo na conta de armazenamento. A alteração da camada de acesso se aplica a todos os objetos armazenados na conta que não têm uma camada explícita definida. Alternar a camada de acesso de frequente para esporádica incorre em operações de gravação (por 10.000) para todos os blobs sem uma camada de conjunto em apenas para contas de GPv2 e alternar de esporádico para frequente incorre em operações de leitura (por 10.000) e os encargos de recuperação de dados (por GB) para todos os blobs no armazenamento de Blob e contas de GPv2.

**Posso configurar minha camada de acesso de conta padrão para arquivar?**

Não. Apenas as camadas de acesso quente e frio podem ser definidas como a camada de acesso de conta padrão. A camada arquivo só pode ser definida no nível do objeto.

**Em quais regiões são frequente, esporádica em arquivar as camadas de acesso disponíveis no?**

As camadas de acesso quente e frio junto com a camada no nível do blob estão disponíveis em todas as regiões. O armazenamento de arquivo estará disponível, inicialmente, apenas em regiões selecionadas. Para obter uma lista completa, consulte [Produtos do Azure por região](https://azure.microsoft.com/regions/services/).

**Os blobs na camada de acesso fria se comportam de forma diferente na camada de acesso quente?**

Os BLOBs na camada de acesso quente têm a mesmo latência que os blobs nas contas de armazenamento de GPv1, GPv2 e BLOBs. Os BLOBs na camada de acesso esporádico têm uma latência parecida (em milissegundos) que os blobs nas contas de armazenamento de GPv1, GPv2 e BLOBs. Os BLOBs na camada de acesso de arquivo têm várias horas de latência em contas de armazenamento de GPv1, GPv2 e BLOBs.

Os BLOBs na camada de acesso fria têm um nível ligeiramente menor disponibilidade serviço (SLA) que os blobs armazenados na camada de acesso quente. Para obter mais informações, veja [SLA para armazenamento](https://azure.microsoft.com/support/legal/sla/storage/v1_2/).

**As operações entre as camadas frequente, esporádica e de arquivo são a mesma?**

Sim. Todas as operações entre frequente e esporádica são 100% consistentes. Todas as operações válidas de arquivo, incluindo excluir, listar, obter propriedades/metadados de blob e definir a camada de blob são 100% consistentes com frequente e esporádica. Um blob não pode ser lido ou modificado enquanto estiver na camada de arquivo.

**Ao reidratar um blob da camada de arquivo para a camada frequente ou esporádica, como vou saber quando a reidratação estiver concluída?**

Durante a reidratação, você pode usar a operação de obtenção de propriedades de blob para sondar o atributo **Status de Arquivo** para confirmar quando a alteração de camada estiver concluída. O status exibe "reidratação pendentes para camada quente" ou "reidratação pendente para camada fria" dependendo da camada de destino. Após a conclusão, a propriedade status do arquivo é removida, e a propriedade de blob de **Nível de Acesso** reflete a nova camada frequente ou esporádica.  

**Depois de definir o nível de um blob, quando vai começar a ser cobrada a taxa apropriada?**

Cada blob é sempre cobrado de acordo com a camada indicada pelo propriedade **Camada de Acesso** do blob. Quando você define uma nova camada para um blob, a propriedade **Camada de acesso** reflete imediatamente a nova camada para todas as transições. No entanto, reidratar um blob da camada dos arquivos para uma camada frequente ou esporádica pode levar várias horas. Nesse caso, são cobradas tarifas de arquivos até a conclusão da reidratação, ponto no qual a propriedade **Camada de Acesso** reflete a nova camada. Nesse ponto, você é cobrado por esse blob na taxa do armazenamento frequente ou esporádico.

**Como determino se vou incorrer um custo de exclusão antecipada ao excluir ou tirar um blob da camada esporádica ou de arquivo?**

Qualquer blob que for excluído ou tirado da camada esporádica (somente para contas de GPv2) ou de arquivo morto antes de 30 e 180 dias respectivamente incorrerá em uma carga de exclusão antecipada proporcional. É possível determinar quanto tempo um blob está na camada esporádica ou de arquivos verificando a propriedade **Hora da Alteração da Camada de Acesso** do blob, que fornece um selo da última alteração da camada. Para obter mais informações, consulte [exclusão antecipada esporádica e de arquivo](#cool-and-archive-early-deletion).

**Quais ferramentas e SDKs do Azure dão suporte a armazenamento de camada no nível do blob e de arquivo?**

As ferramentas do portal do Azure, do PowerShell e da CLI e as bibliotecas de cliente .NET, Java, Python e Node.js, todas dão suporte ao armazenamento de camada no nível do blob e de arquivo.  

**Qual a quantidade de dados que posso armazenar nas camadas frequente, esporádica ou de arquivo?**

Armazenamento de dados e outros limites são definidos no nível da conta e não por camada de acesso. Portanto, é possível optar por usar todo o seu limite em uma camada ou em todas as três camadas. Para saber mais, confira [Metas de desempenho e escalabilidade do Armazenamento do Azure](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="next-steps"></a>Próximas etapas

### <a name="evaluate-hot-cool-and-archive-in-gpv2-blob-storage-accounts"></a>Avaliar os mais acessados, os menos acessados e os arquivados em contas do Armazenamento de Blobs GPv2

[Verificar a disponibilidade de frequente, esporádica e de arquivo por região](https://azure.microsoft.com/regions/#services)

[Gerenciar o ciclo de vida de armazenamento de BLOBs do Azure](storage-lifecycle-management-concepts.md)

[Avaliar o uso de suas contas de armazenamento atuais, habilitando as métricas do Armazenamento do Azure](../common/storage-enable-and-view-metrics.md)

[Verificar o preço de frequente, esporádica e de arquivo em contas de Armazenamento de Blobs e GPv2 por região](https://azure.microsoft.com/pricing/details/storage/)

[Verificar os preços de transferências de dados](https://azure.microsoft.com/pricing/details/data-transfers/)
