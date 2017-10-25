---
title: Armazenamento quente, frio e de arquivo morto para blobs | Microsoft Docs
description: Armazenamento quente, frio e de arquivo morto para contas de armazenamento de Blobs do Azure.
services: storage
documentationcenter: 
author: michaelhauss
manager: vamshik
editor: tysonn
ms.assetid: eb33ed4f-1b17-4fd6-82e2-8d5372800eef
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/05/2017
ms.author: mihauss
ms.openlocfilehash: 544b11d74a926fe62b8ceca51570ce9d2ee7e6e7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-blob-storage-hot-cool-and-archive-preview-storage-tiers"></a>Armazenamento de Blobs do Azure: Camadas de armazenamento quente, fria e de arquivo morto (visualização)

## <a name="overview"></a>Visão geral

O Armazenamento do Azure oferece três camadas de armazenamento para o armazenamento de objetos de Blobs, para que você possa armazenar os dados de uma maneira mais econômica, dependendo de como você os utiliza. A **camada de armazenamento dinâmica** do Azure é otimizada para armazenar dados acessados com frequência. A **camada de armazenamento fria** do Azure é otimizada para armazenar dados acessados com menos frequência e armazenados por pelo menos um mês. A [camada de armazenamento de arquivo morto (visualização)](https://azure.microsoft.com/blog/announcing-the-public-preview-of-azure-archive-blob-storage-and-blob-level-tiering) é otimizada para armazenar dados acessados raramente e armazenados por pelo menos seis meses com os requisitos de latência flexível (na ordem de horas). A camada de armazenamento de *arquivo morto* só pode ser usada no nível de blob e não na conta de armazenamento inteira. Os dados na camada de armazenamento estático podem tolerar uma disponibilidade um pouco menor, mas ainda exigem alta durabilidade e tempo de acesso e taxa de transferência semelhantes aos de dados ativos. Para os dados inativos e de arquivo morto, um SLA com disponibilidade um pouco menor e custos mais altos de acesso são compensações aceitáveis, em troca de custos de armazenamento muito menores.

Hoje, os dados armazenados na nuvem estão aumentando em um ritmo exponencial. Para gerenciar os custos de suas necessidades cada vez maiores de armazenamento, é útil organizar seus dados com base em atributos como frequência de acesso e período de retenção planejado. Os dados armazenados na nuvem podem ser diferentes em relação ao modo como são gerados, processados e acessados durante seu tempo de vida. Alguns dados são ativamente acessados e modificados durante seu ciclo de vida. Alguns dados são acessados com frequência no início do seu tempo de vida, mas esse acesso cai drasticamente à medida que os dados envelhecem. Alguns dados permanecem ociosos na nuvem e raramente, ou nunca, são acessados após serem armazenados.

Cada um desses cenários de acesso a dados se beneficia de uma camada diferenciada de armazenamento, otimizada para um padrão de acesso específico. Com as camadas de armazenamento quente, fria e de arquivo morto, o Armazenamento de Blobs do Azure atende a essa necessidade através de camadas de armazenamento diferenciadas com modelos de preços separados.

## <a name="blob-storage-accounts"></a>Contas de armazenamento de Blobs

**Contas de armazenamento de Blobs** são contas de armazenamento especializadas para armazenar dados não estruturados como blobs (objetos) no Armazenamento do Azure. Com contas de armazenamento de Blobs, agora você pode escolher entre as camadas de armazenamento quente e fria no nível de conta, ou camadas quente, fria e de arquivo morto, com base nos padrões de acesso. Armazene os dados inativos acessados raramente com o menor custo de armazenamento, os dados inativos acessados com menos frequência a um custo de armazenamento menor do que o armazenamento quente e armazene os dados ativos acessados com frequência com o menor custo de acesso. As contas de armazenamento de Blobs são semelhantes a contas de armazenamento de uso geral existentes e compartilham todos os excelentes recursos de durabilidade, disponibilidade, escalabilidade e desempenho que você usa atualmente, incluindo 100% de consistência de API para blobs de blocos e blobs de acréscimo.

> [!NOTE]
> As contas de armazenamento de blobs oferecem suporte apenas aos blobs de bloco e aos blobs de acréscimo, e não aos blobs de página.

As contas de armazenamento de Blobs expõem o atributo de **Camada de Acesso**, que permite especificar a camada de armazenamento como **Dinâmica** ou **Estática**, dependendo dos dados armazenados na conta. Se houver uma alteração no padrão de uso dos dados, você também poderá alternar entre as camadas de armazenamento a qualquer momento. A camada de arquivo morto (visualização) só pode ser aplicada no nível do blob.

> [!NOTE]
> A alteração da camada de armazenamento pode resultar em cobranças adicionais. Consulte a seção [Preços e cobrança](#pricing-and-billing) para obter mais detalhes.

### <a name="hot-access-tier"></a>Camada de acesso quente

Os cenários de uso de exemplo para a camada de armazenamento dinâmico incluem:

* Dados que estão em uso ativo ou devem ser acessados (lidos e gravados) com frequência.
* Dados preparados para processamento e eventual migração para a camada de armazenamento estático.

### <a name="cool-access-tier"></a>Camada de acesso frio

Os cenários de uso de exemplo para a camada de armazenamento estático incluem:

* Conjuntos de dados de recuperação de desastre e de backup de curto prazo.
* Conteúdo de mídia mais antigo que não é mais exibido frequentemente, mas ainda deve estar disponível imediatamente quando acessado.
* Grandes conjuntos de dados que precisam ser armazenados de forma econômica enquanto mais dados estão sendo obtidos para processamento futuro. (*por exemplo*, armazenamento de longo prazo de dados científicos; dados brutos de telemetria de uma instalação de manufatura)

### <a name="archive-access-tier-preview"></a>Camada de acesso de arquivo morto (visualização)

O [Armazenamento de arquivo morto](https://azure.microsoft.com/blog/announcing-the-public-preview-of-azure-archive-blob-storage-and-blob-level-tiering) tem o menor custo de armazenamento e os custos mais altos de recuperação de dados comparados com o armazenamento quente e frio.

Enquanto um blob estiver no armazenamento de arquivo morto, ele não pode ser lido, copiado, substituído ou modificado. Você também não pode tirar instantâneos de um blob no armazenamento de arquivo morto. No entanto, você pode usar as operações existentes para excluir, listar, obter propriedades/metadados de blob ou alterar a camada do seu blob. Para ler dados no armazenamento de arquivo morto, primeiro você deve alterar a camada do blob para quente ou fria. Esse processo é conhecido como reidratação e pode levar até 15 horas para ser concluído para blobs com menos de 50 GB. O tempo adicional necessário para blobs maiores varia de acordo com o limite de taxa de transferência do blob.

Durante a reidratação, você pode marcar a propriedade de blob "status do arquivo morto" para confirmar se a camada foi alterada. O status exibe "reidratação pendentes para camada quente" ou "reidratação pendente para camada fria" dependendo da camada de destino. Após a conclusão, a propriedade de blob "status do arquivo morto" será removida e a propriedade de blob de "nível de acesso" reflete a camada quente ou fria.  

Os cenários de uso de exemplo para a camada de armazenamento de arquivo morto incluem:

* Conjuntos de dados de recuperação de desastre, arquivo morto e backup de longo prazo
* Dados originais (brutos) que devem ser preservados, mesmo após serem processados em formato utilizável final. (*por exemplo*, arquivos de mídia brutos após transcodificação em outros formatos)
* Dados de conformidade e arquivamento que precisam ser armazenados por um longo tempo e quase nunca são acessados. (*por exemplo*, filmagens de câmeras de segurança, raios X/ressonâncias magnéticas antigos para organizações de serviços de saúde, gravações de áudio e transcrições de chamadas de clientes para serviços financeiros)

### <a name="recommendations"></a>Recomendações

Confira [Sobre contas de armazenamento do Azure](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) para saber mais sobre contas de armazenamento.

Para aplicativos que exigem apenas armazenamento de blobs de bloco ou acréscimo, é recomendável usar contas de armazenamento de Blobs para aproveitar o modelo de preços diferenciado do armazenamento em camadas. No entanto, compreendemos que talvez isso não seja possível em determinadas circunstâncias em que o uso de contas de armazenamento de finalidade geral seria a melhor solução, como:

* Você precisa usar tabelas, filas ou arquivos e deseja que seus blobs sejam armazenados na mesma conta de armazenamento. Observe que armazená-los na mesma conta não oferece vantagens técnicas, exceto ter as mesmas chaves compartilhadas.

* Você ainda precisa usar o modelo de implantação Clássico. As contas de armazenamento de Blobs só estão disponíveis por meio do modelo de implantação do Azure Resource Manager.

* Você precisa usar blobs de página. As contas de armazenamento de blobs não dão suporte a blobs de página. Geralmente, é recomendável usar blobs de bloco, a menos que você tenha uma necessidade específica para blobs de página.

* Você usa uma versão da [API REST dos Serviços de Armazenamento](https://msdn.microsoft.com/library/azure/dd894041.aspx) que é anterior a 2014-02-14 ou uma biblioteca de cliente com uma versão inferior a 4.x e não pode atualizar o aplicativo.

> [!NOTE]
> Contas de armazenamento de Blobs atualmente têm suporte em todas as regiões do Azure.
 

## <a name="blob-level-tiering-feature-preview"></a>Recurso de camadas de nível de blob (visualização)

As camadas de nível de blob agora permitem que você altere a camada de seus dados no nível de objeto usando uma única operação chamada [Definir Nível de Blob](/rest/api/storageservices/set-blob-tier). Você pode alterar facilmente o nível de acesso de um blob entre as camadas quente, fria ou de arquivo morto como alteração de padrões de uso, sem a necessidade de mover dados entre contas. Todas as alterações de camada acontecem imediatamente, exceto quando um blob está sendo submetido a uma reidratação a partir do arquivo morto. Blobs em todos os três camadas de armazenamento podem coexistir na mesma conta. Qualquer blob que não tenha uma camada atribuída explicitamente herda a camada da configuração de nível de acesso da conta.

Para usar esses recursos em visualização, siga as instruções em [Comunicado do blog de Camadas de Nível de Blob e de Arquivo Morto do Azure](https://azure.microsoft.com/blog/announcing-the-public-preview-of-azure-archive-blob-storage-and-blob-level-tiering).

A seguir está uma de algumas restrições aplicáveis durante a visualização de camadas de nível de blob:

* Somente novas contas de armazenamento Blobs criadas no Leste dos EUA 2 após um registro de visualização bem-sucedido são compatíveis com armazenamento de arquivo morto.

* Somente novas contas de armazenamento de Blobs criadas em regiões públicas após um registro de visualização bem-sucedido são compatíveis com camadas de nível de blob.

* Há suporte para o armazenamento em camadas de nível de blob e de arquivo morto somente no armazenamento [LRS] (../ common/storage-redundancy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#locally-redundant-storage). Haverá suporte [GRS](../common/storage-redundancy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#geo-redundant-storage) e [RA-GRS](../common/storage-redundancy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#read-access-geo-redundant-storage) no futuro.

* Não é possível alterar a camada de um blob com instantâneos.

* Você não pode copiar ou tirar um instantâneo de um blob no armazenamento de arquivo morto.

## <a name="comparison-of-the-storage-tiers"></a>Comparação entre camadas de armazenamento

A tabela a seguir mostra uma comparação entre as camadas de armazenamento quente e fria. A camada de nível de blob de arquivo morto não está na visualização, portanto não há nenhum SLA para ela.

| | **Camada de armazenamento quente** | **Camada de armazenamento fria** |
| ---- | ----- | ----- |
| **Disponibilidade** | 99,9% | 99% |
| **Disponibilidade** <br> **(Leituras de RA-GRS)**| 99,99% | 99,9% |
| **Encargos de uso** | Custos de armazenamento maiores, custos de acesso e de transações menores | Custos de armazenamento menores, custos de acesso e de transações maiores |
| **Tamanho mínimo do objeto** | N/D | N/D |
| **Duração mínima de armazenamento** | N/D | N/D |
| **Latência** <br> **(Tempo até o primeiro byte)** | milissegundos | milissegundos |
| **Escalabilidade e metas de desempenho** | Igual ao das contas de armazenamento de finalidade geral | Igual ao das contas de armazenamento de finalidade geral |

> [!NOTE]
> As contas de armazenamento de Blobs dão suporte às mesmas metas de desempenho e escalabilidade que as contas de armazenamento de finalidade geral. Confira [Metas de desempenho e de escalabilidade do Armazenamento do Azure](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) para saber mais.


## <a name="pricing-and-billing"></a>Preços e cobrança
As contas de armazenamento de Blobs usam um modelo de preços para o armazenamento de blobs com base na camada de armazenamento. Ao se usar uma conta de armazenamento de Blobs, as seguintes considerações de cobranças são aplicáveis:

* **Custos de armazenamento**: além da quantidade de dados armazenados, o custo de armazenamento de dados varia de acordo com a camada de armazenamento. O custo por gigabyte é menor para a camada de armazenamento estático do que para a camada de armazenamento dinâmico.

* **Custos de acesso a dados**: Para dados na camada de armazenamento fria, será cobrado um encargo de acesso a dados por gigabyte para leituras e gravações.

* **Custos de transação**: há um encargo por transação para ambas as camadas. No entanto, o custo por transação para a camada de armazenamento estático é maior do que para a camada de armazenamento dinâmico.

* **Custos de transferência de dados de replicação geográfica**: isso só se aplica a contas com replicação geográfica configurada, incluindo GRS e RA-GRS. A transferência de dados de replicação geográfica acarreta um encargo por gigabyte.

* **Custos de transferência de dados de saída**: transferências de dados de saída (dados que são transferidos para fora de uma região do Azure) acarretam a cobrança por uso de largura de banda por gigabyte, de forma consistente com as contas de armazenamento de finalidade geral.

* **Alteração da camada de armazenamento**: A alteração da camada de armazenamento de fria para quente acarretará um encargo igual à leitura de todos os dados existentes na conta de armazenamento para todas as transições. Por outro lado, a alteração de camada quente para fria será gratuita.

> [!NOTE]
> Para saber mais sobre o modelo de preços para contas de armazenamento de Blobs, confira a página [Preços de Armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/). Para saber mais sobre os encargos de transferência de dados de saída, confira a página [Detalhes de preços de transferências de dados](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="quickstart"></a>Início rápido

Nesta seção, demonstramos os seguintes cenários usando o portal do Azure:

* Como criar uma conta de armazenamento de Blobs.
* Como gerenciar uma conta de armazenamento de Blobs.

Não é possível definir o nível de acesso para arquivo morto nos exemplos a seguir, porque essa configuração se aplica à conta de armazenamento inteira. Arquivo morto só pode ser definido em um blob específico.

### <a name="create-a-blob-storage-account-using-the-azure-portal"></a>Criar uma conta de armazenamento de Blobs usando o portal do Azure

1. Entre no [Portal do Azure](https://portal.azure.com).

2. No menu Hub, selecione **Novo** > **Dados + Armazenamento** > **Conta de armazenamento**.

3. Insira um nome para a conta de armazenamento.
   
    Esse nome deve ser exclusivo globalmente; ele é usado como parte da URL utilizada para acessar os objetos na conta de armazenamento.  

4. Selecione **Gerenciador de Recursos** como o modelo de implantação.
   
    O armazenamento em camadas só pode ser usado com as contas de armazenamento do Gerenciador de Recursos; é o modelo de implantação recomendado para os novos recursos. Para obter mais informações, verifique [Visão geral do Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md).  

5. Na lista suspensa Tipo de Conta, selecione **Armazenamento de Blobs**.
   
    É onde você seleciona o tipo de conta de armazenamento. O armazenamento em camadas não está disponível no armazenamento geral; só está disponível na conta do tipo Armazenamento de Blobs.     
   
    Observe que quando você seleciona essa opção, a camada de desempenho é definida para Standard. O armazenamento em camadas não está disponível com o nível de desempenho Premium.

6. Selecione a opção de replicação para a conta de armazenamento: **LRS**, **GRS** ou **RA-GRS**. O padrão é **RA-GRS**.
   
    LRS = armazenamento com redundância local; GRS = armazenamento com redundância geográfica (2 regiões); RA-GRS é o armazenamento com redundância geográfica e acesso de leitura (2 regiões com acesso de leitura para o segundo).
   
    Para obter mais detalhes sobre as opções de replicação do Armazenamento do Azure, verifique a [Replicação do Armazenamento do Azure](../common/storage-redundancy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

7. Selecione a camada de armazenamento adequada para suas necessidades: defina a **Camada de acesso** como **Estática** ou **Dinâmica**. O padrão é **Dinâmica**. 

8. Selecione a assinatura na qual você deseja criar a nova conta de armazenamento.

9. Especifique um novo grupo de recursos ou selecione um grupo de recursos existente. Para saber mais sobre grupos de recursos, confira [Visão geral do Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md).

10. Selecione a região de sua conta de armazenamento.

11. Clique em **Criar** para criar a conta de armazenamento.

### <a name="change-the-storage-tier-of-a-blob-storage-account-using-the-azure-portal"></a>Alterar a camada de armazenamento em uma conta de armazenamento de Blobs usando o portal do Azure

1. Entre no [Portal do Azure](https://portal.azure.com).

2. Para navegar até sua conta de armazenamento, selecione Todos os Recursos e sua conta de armazenamento.

3. Na folha Configurações, clique em **Configuração** para exibir e/ou alterar a configuração da conta.

4. Selecione a camada de armazenamento adequada para suas necessidades: defina a **Camada de acesso** como **Fria** ou **Quente**.

5. Clique em Salvar na parte superior da folha.

> [!NOTE]
> A alteração da camada de armazenamento pode resultar em cobranças adicionais. Consulte a seção [Preços e Cobrança](#pricing-and-billing) para obter mais detalhes.


## <a name="evaluating-and-migrating-to-blob-storage-accounts"></a>Avaliando e migrando para contas de armazenamento de Blobs
O objetivo desta seção é ajudar os usuários a fazer uma transição sem problemas para usar contas de armazenamento de Blobs. Há dois cenários de usuário:

* Você tem uma conta existente de armazenamento para uso geral e deseja avaliar uma alteração para uma conta de armazenamento de Blobs com a camada de armazenamento adequada.
* Você optou por usar uma conta de armazenamento de Blobs ou já tem uma e deseja avaliar se deve usar a camada de armazenamento dinâmica ou estática.

Em ambos os casos, a tarefa prioritária é estimar o custo de armazenar e acessar os dados armazenados em uma conta de armazenamento de Blobs e comparar isso com seus custos atuais.

## <a name="evaluating-blob-storage-account-tiers"></a>Avaliando camadas de conta de armazenamento de Blobs

Para estimar o custo de armazenar e acessar dados armazenados em uma conta de armazenamento de Blobs, você precisa avaliar seu padrão de uso existente ou estimar seu padrão de uso. Em geral, você precisa saber:

* Consumo de armazenamento: quanto dados são armazenados e como isso é alterado mensalmente?

* Padrão de acesso de armazenamento: quantos dados são lidos e gravados na conta (incluindo novos dados)? Quantas transações são usadas para acesso a dados e que tipo de transações são elas?

## <a name="monitoring-existing-storage-accounts"></a>Monitorando contas de armazenamento existentes

Para monitorar suas contas de armazenamento existentes e reunir esses dados, você poderá fazer uso da Análise de Armazenamento do Azure, que executa logs e fornece dados de métrica para uma conta de armazenamento. A Análise de Armazenamento pode armazenar métricas que incluem estatísticas de transação agregadas e dados de capacidade em relação a solicitações ao serviço de armazenamento de Blobs para contas de armazenamento para fins gerais e contas de armazenamento de Blobs. Esses dados são armazenados em tabelas conhecidas na mesma conta de armazenamento.

Para obter mais detalhes, consulte [Sobre métricas de análise de armazenamento](https://msdn.microsoft.com/library/azure/hh343258.aspx) e [Esquema de tabela de métricas da análise de armazenamento](https://msdn.microsoft.com/library/azure/hh343264.aspx)

> [!NOTE]
> As contas de armazenamento de Blobs expõem o ponto de extremidade de serviço de tabela apenas para armazenar e acessar dados de métricas dessa conta.

Para monitorar o consumo de armazenamento do serviço de armazenamento de Blobs, você precisa habilitar as métricas de capacidade.
Com esse recurso habilitado, os dados de capacidade são gravados diariamente para o serviço Blob de uma conta de armazenamento e registrados como uma entrada de tabela gravada na tabela *$MetricsCapacityBlob* dentro da mesma conta de armazenamento.

Para monitorar o padrão de acesso a dados para o serviço de armazenamento de Blobs, você precisa habilitar as métricas de transações por hora no nível da API. Com esse recurso habilitado, as transações por API são agregadas a cada hora e registradas como uma entrada de tabela que é gravada na tabela *$MetricsHourPrimaryTransactionsBlob* dentro da mesma conta de armazenamento. A tabela *$MetricsHourSecondaryTransactionsBlob* registra as transações para o ponto de extremidade secundário ao usar contas de armazenamento RA-GRS.

> [!NOTE]
> Caso você tenha uma conta de armazenamento para uso geral na qual armazenou discos de máquina virtual e blobs de páginas junto com dados de blob de bloqueio e anexação, esse processo de previsão não se aplica. Isso ocorre porque você não tem nenhuma maneira de distinguir métricas de capacidade e de transação com base apenas no tipo de blob somente para blobs de acréscimo e bloqueio que podem ser migrados para uma conta de armazenamento de Blobs.

Para obter uma boa aproximação do consumo de dados e do padrão de acesso, recomendamos escolher um período de retenção para as métricas que representam seu uso regular e extrapolar. Uma opção é manter os dados de métricas por sete dias e coletar os dados de cada semana, para analisá-las no final do mês. Outra opção é manter os dados de métricas dos últimos 30 dias e coletar e analisar os dados ao fim do período de 30 dias.

Para obter detalhes sobre como habilitar, coletar e exibir dados de métricas, consulte [Habilitando métricas do Armazenamento do Azure e exibindo dados de métricas](../common/storage-enable-and-view-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

> [!NOTE]
> O armazenamento, acesso e download de dados de análise também serão cobrados, assim como os dados de usuário comuns.

### <a name="utilizing-usage-metrics-to-estimate-costs"></a>Utilizando métricas de uso para estimar custos

### <a name="storage-costs"></a>Custos de armazenamento

A última entrada na tabela de métricas de capacidade *$MetricsCapacityBlob* com a chave de linha *'data'* mostra a capacidade de armazenamento consumida por dados do usuário. A última entrada na tabela de métricas de capacidade *$MetricsCapacityBlob* com a chave de linha *'analytics'* mostra a capacidade de armazenamento consumida por logs de análise.

Essa capacidade total consumida tanto por dados de usuário quanto logs de análise (se habilitado) pode ser usada para estimar o custo de armazenar dados na conta de armazenamento. O mesmo método também pode ser usado para estimar os custos de armazenamento de blobs de anexação e bloqueio nas contas de armazenamento de uso geral.

### <a name="transaction-costs"></a>Custos de transação

A soma de *'TotalBillableRequests'* de todas as entradas para uma API na tabela de métricas de transação indica a quantidade total de transações para essa API em particular. *Por exemplo*, o número total de transações *'GetBlob'* em um determinado período pode ser calculado pela soma do total de solicitações faturáveis para todas as entradas com a chave de linha *'user;GetBlob'*.

Para estimar os custos de transação para contas de armazenamento de Blobs, você precisa dividir as transações em três grupos, já que elas possuem preços diferentes.

* Transações de gravação, como *'PutBlob'*, *'PutBlock'*, *'PutBlockList'*, *'AppendBlock'*, *'ListBlobs'*, *'ListContainers'*, *'CreateContainer'*, *'SnapshotBlob'* e *'CopyBlob'*.
* Transações de exclusão, como *'DeleteBlob'* e *'DeleteContainer'*.
* Todas as outras transações.

Para estimar os custos de transação para contas de armazenamento de uso geral, você precisa agregar todas as transações, independentemente da operação/API.

### <a name="data-access-and-geo-replication-data-transfer-costs"></a>Custos de transferência de dados de acesso aos dados e de replicação geográfica

Embora a análise de armazenamento não forneça a quantidade de dados lida e gravada em uma conta de armazenamento, ela pode ser estimada aproximadamente examinando a tabela de métricas de transação. A soma de *'TotalIngress'* de todas as entradas para uma API na tabela de métricas de transação indica a quantidade total de dados de entrada em bytes para essa API em particular. Da mesma forma, a soma de *'TotalEgress'* indica a quantidade total de dados de saída, em bytes.

Para estimar os custos de acesso de dados para as contas de armazenamento de Blobs, você precisa dividir as transações em dois grupos. 

* A quantidade de dados recuperados da conta de armazenamento pode ser estimada observando a soma de *'TotalEgress'* basicamente para as operações *'GetBlob'* e *'CopyBlob'*.

* A quantidade de dados gravados na conta de armazenamento pode ser estimada observando a soma de *'TotalIngress'* basicamente para as operações *'PutBlob'*, *'PutBlock'*, *'CopyBlob'* e *'AppendBlock'*.

O custo da transferência de dados de replicação geográfica para contas de armazenamento de Blobs também pode ser calculado usando a estimativa de quantidade de dados gravados ao usar uma conta de armazenamento GRS ou RA-GRS.

> [!NOTE]
> Para obter um exemplo mais detalhado de como calcular os custos para usar a camada de armazenamento quente ou fria, dê uma olhada nas Perguntas frequentes intituladas *“O que são as camadas de acesso quente e fria e como devo determinar qual delas usar?”* na [Página de preços de armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/).
 
## <a name="migrating-existing-data"></a>Migração de dados existentes

Uma conta de Armazenamento de Blobs é especializada no armazenamento exclusivo de blobs de bloco e de acréscimo. As contas de armazenamento de uso geral existentes, que permitem o armazenamento de tabelas, filas, arquivos e discos não podem ser convertidas em contas de Armazenamento de Blobs. Para usar as camadas de armazenamento, é necessário criar novas contas de Armazenamento de Blobs e migrar os dados existentes para as contas recém-criadas.

Você pode usar os métodos a seguir para migrar os dados existentes para as contas de armazenamento de Blobs a partir dos dispositivos de armazenamento locais, provedores de armazenamento de nuvem de terceiros ou de suas contas de armazenamento geral existentes no Azure:

### <a name="azcopy"></a>AzCopy

O AzCopy é um utilitário de linha de comando do Windows desenvolvido para cópia de dados de alto desempenho para dentro e para fora do Armazenamento do Azure. Você pode usar o AzCopy para copiar dados para sua conta de armazenamento de Blobs de suas contas de armazenamento de finalidade geral existentes ou carregar dados do dispositivo de armazenamento local para sua conta de armazenamento de Blobs.

Para obter mais detalhes, consulte [Transferir dados com o Utilitário da Linha de Comando AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

### <a name="data-movement-library"></a>Biblioteca de movimentação de dados

A biblioteca de movimentação de dados do Armazenamento do Azure para .NET baseia-se na estrutura de movimentação de dados de núcleo que habilita o AzCopy. A biblioteca foi projetada para operações de transferência de dados de alto desempenho, confiáveis e fáceis, de forma semelhante ao AzCopy. Isso permite que você aproveite todos os benefícios dos recursos fornecidos pelo AzCopy em seu aplicativo de forma nativa, sem a necessidade de lidar com a execução e o monitoramento de instâncias externas do AzCopy.

Para obter mais detalhes, consulte [Biblioteca de Movimentação dos Dados do Armazenamento do Azure para .Net](https://github.com/Azure/azure-storage-net-data-movement)

### <a name="rest-api-or-client-library"></a>API REST ou biblioteca de cliente

Você pode criar um aplicativo personalizado para migrar os dados para uma conta de armazenamento de Blobs usando uma das bibliotecas de cliente do Azure ou a API REST dos serviços de armazenamento do Azure. O Armazenamento do Azure fornece bibliotecas de cliente avançadas para várias linguagens e plataformas, como .NET, Java, C++, Node.JS, PHP, Ruby e Python. As bibliotecas de cliente oferecem recursos avançados, como lógica de recuperação, registro em log e carregamentos paralelos. Você também pode desenvolver diretamente na API REST, que pode ser chamada por qualquer linguagem que faça solicitações HTTP/HTTPS.

Para obter mais detalhes, consulte [Introdução ao armazenamento de Blobs do Azure](storage-dotnet-how-to-use-blobs.md).

> [!NOTE]
> Blobs criptografados usando metadados relacionados à criptografia de armazenamento no lado do cliente armazenados com o blob. É absolutamente essencial que qualquer mecanismo de cópia assegure que os metadados de blob, e especialmente os metadados relacionados à criptografia, sejam preservados. Se você copiar os blobs sem esses metadados, o conteúdo do blob não pode ser recuperado novamente. Para obter mais detalhes sobre os metadados relacionados à criptografia, confira [Criptografia no Lado do Cliente do Armazenamento do Azure](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).
 
## <a name="faq"></a>Perguntas frequentes

1. **As contas de armazenamento existentes ainda estão disponíveis?**
   
    Sim, as contas de armazenamento existentes ainda estão disponíveis, e seus preços ou funcionalidade não foram alterados.  Elas não têm a capacidade de escolher uma camada de armazenamento e não terão recursos de camadas no futuro.

2. **Por que e quando devo começar a usar contas de armazenamento de Blobs?**
   
    As contas de armazenamento de Blobs são especializadas em armazenamento de blobs e nos permitem introduzir novos recursos centrados em blobs. Futuramente, as contas de armazenamento de Blobs serão a maneira recomendada para armazenar blobs, à medida que recursos futuros, como o armazenamento hierárquico e em camadas, forem introduzidos com base nesse tipo de conta. No entanto, você pode decidir quando deseja migrar com base nas necessidades de negócios.

3. **Posso converter minha conta de armazenamento existente em uma conta de armazenamento de Blobs?**
   
    Não. Uma conta de armazenamento de Blobs é um tipo diferente de conta de armazenamento e você precisa criar uma nova e migrar os dados, conforme explicado anteriormente.

4. **Posso armazenar objetos em ambas as camadas de armazenamento na mesma conta?**
   
    O atributo *'Access Tier'* indica o valor da camada de armazenamento definida no nível de conta e se aplica a todos os objetos na respectiva conta. No entanto, o recurso de camada de nível de blob (visualização) permitirá que você defina o nível de acesso em blobs específicos, e isso substituirá a configuração de camada de acesso da conta. 

5. **Posso alterar a camada de armazenamento da minha conta de armazenamento de Blobs?**
   
    Sim. Você pode alterar a camada de armazenamento definindo o atributo *'Access Tier'* na conta de armazenamento. A alteração da camada de armazenamento é aplicada a todos os objetos armazenados na conta. A alteração da camada de armazenamento de quente para fria não incorrerá em encargos, mas a alteração de fria para quente incorrerá em um custo por GB para a leitura de todos os dados na conta.

6. **Com que frequência posso alterar a camada de armazenamento em minha conta de armazenamento de Blobs?**
   
    Embora não imponhamos uma limitação à frequência com que a camada de armazenamento pode ser alterada, lembre-se de que a alteração da camada de armazenamento de estática para dinâmica incorrerá em encargos significativos. Não recomendamos alterar a camada de armazenamento com frequência.

7. **Os blobs na camada de armazenamento fria se comportam de forma diferente daqueles na camada de armazenamento quente?**
   
    Os Blobs na camada de armazenamento dinâmico têm a mesmo latência que os blobs nas contas de armazenamento de uso geral. Os Blobs na camada de armazenamento estático têm uma latência parecida (em milissegundos) que os blobs nas contas de armazenamento de uso geral.
   
    Os Blobs na camada de armazenamento fria têm um SLA (acordo de nível de serviço) com disponibilidade ligeiramente menor do que os blobs armazenados na camada de armazenamento quente. Para obter mais detalhes, confira [SLA para armazenamento](https://azure.microsoft.com/support/legal/sla/storage).

8. **Posso armazenar os blobs de página e discos de máquinas virtuais em contas de armazenamento de Blob?**
   
    As contas de armazenamento de blobs oferecem suporte apenas aos blobs de bloco e aos blobs de acréscimo, e não aos blobs de página. Os discos de máquina virtual do Azure têm o suporte de blobs de página e, como resultado, as contas de armazenamento de blobs não podem ser usadas para armazenar os discos da máquina virtual. No entanto, é possível armazenar backups dos discos de máquina virtual como blobs de bloco em uma conta de Armazenamento de Blobs.

9. **Preciso alterar meus aplicativos existentes para usar contas de armazenamento de Blobs?**
   
    Contas de Armazenamento de Blobs são 100% consistentes com API com contas de armazenamento de uso geral para blobs de bloco e de acréscimo. Contanto que o aplicativo esteja usando blobs de bloco ou blobs de acréscimo e você esteja usando a versão 2014-02-14 da [API REST dos Serviços de Armazenamento](https://msdn.microsoft.com/library/azure/dd894041.aspx) ou mais recente, o aplicativo deverá funcionar. Se estiver usando uma versão mais antiga do protocolo, você precisa atualizar o aplicativo para usar a nova versão, para que funcione perfeitamente com os dois tipos de contas de armazenamento. Em geral, é sempre recomendável usar a última versão, independentemente do tipo de conta de armazenamento que você usa.

10. **Haverá uma alteração na experiência do usuário?**
    
    As contas de Armazenamento de Blobs são bastante parecidas com as contas de armazenamento de finalidade geral para armazenamento de blobs de bloco e de acréscimo, e oferecem suporte a todos os principais recursos do Armazenamento do Azure, incluindo alta durabilidade e disponibilidade, escalabilidade, desempenho e segurança. Além dos recursos e restrições específicos de contas de armazenamento de Blobs e suas camadas de armazenamento, explicados acima, todo o resto permanece o mesmo.

## <a name="next-steps"></a>Próximas etapas

### <a name="evaluate-blob-storage-accounts"></a>Avaliar as contas de armazenamento de Blobs

[Verificar a disponibilidade das contas de armazenamento de blobs por região](https://azure.microsoft.com/regions/#services)

[Avaliar o uso de suas contas de armazenamento atuais, habilitando as métricas do Armazenamento do Azure](../common/storage-enable-and-view-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

[Verificar preços do armazenamento de blobs por região](https://azure.microsoft.com/pricing/details/storage/)

[Verificar os preços de transferências de dados](https://azure.microsoft.com/pricing/details/data-transfers/)

### <a name="start-using-blob-storage-accounts"></a>Começar a usar as contas de Armazenamento de Blob

[Introdução ao Armazenamento de Blobs do Azure](storage-dotnet-how-to-use-blobs.md)

[Movendo dados para dentro e para fora do Armazenamento do Azure](../common/storage-moving-data.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

[Transferir dados com o Utilitário de Linha de Comando AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

[Navegue e explore suas contas de armazenamento](http://storageexplorer.com/)