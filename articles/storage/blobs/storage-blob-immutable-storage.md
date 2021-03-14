---
title: Armazenamento de blob imutável-armazenamento do Azure
description: O Armazenamento do Microsoft Azure oferece suporte WORM (Gravar uma vez, reutilizar frequentemente) para o armazenamento de Blob (objeto) que possibilita que os usuários armazenem dados em um estado não apagável e não modificável para um intervalo especificado.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 02/01/2021
ms.author: tamram
ms.reviewer: hux
ms.subservice: blobs
ms.openlocfilehash: 8d04d1bd758480ec33a7480e4045d28ed750f22e
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2021
ms.locfileid: "102430931"
---
# <a name="store-business-critical-blob-data-with-immutable-storage"></a>Armazenar dados de blob comercialmente críticos com armazenamento imutável

O armazenem imutável para armazenamento de blobs do Azure possibilita que os usuários armazenem objetos de dados críticos baseados em negócios em um estado WORM (Gravar uma vez, reutilizar frequentemente). Esse estado torna os dados não apagáveis e não modificáveis para um intervalo especificado pelo usuário. Durante o intervalo de retenção, os blobs podem ser criados e lidos, mas não modificados ou excluídos. O armazenamento imutável está disponível para contas de uso geral v1, de uso geral v2, BlobStorage e BlockBlobStorage em todas as regiões do Azure.

Para obter informações sobre como definir e limpar as isenções legais ou criar uma política de retenção baseada em tempo usando o portal do Azure, o PowerShell ou o CLI do Azure, consulte [definir e gerenciar políticas de imutabilidade para o armazenamento de BLOBs](storage-blob-immutability-policies-manage.md).

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="about-immutable-blob-storage"></a>Sobre o armazenamento de BLOBs imutável

O armazenamento imutável ajuda a organização de saúde, instituições financeiras e setores relacionados, &mdash; especialmente as organizações do revendedor &mdash; para armazenar dados com segurança. O armazenamento imutável também pode ser aproveitado em qualquer cenário para proteger dados críticos contra modificação ou exclusão.

Os aplicativos típicos incluem:

- **Conformidade regulatória**: o armazenamento imutável para o armazenamento de BLOBs do Azure ajuda as organizações a lidar com SEC 17a-4 (f), CFTC 1.31 (d), FINRA e outras regulamentações. Um White paper técnico da Cohasset Associates fornece detalhes sobre como o armazenamento imutável resolve esses requisitos regulatórios é baixável por meio do [portal de confiança do serviço da Microsoft](https://aka.ms/AzureWormStorage). O [central de confiabilidade do Azure](https://www.microsoft.com/trustcenter/compliance/compliance-overview) contém informações detalhadas sobre nossas certificações de conformidade.

- **Retenção segura de documentos**: o armazenamento imutável para o armazenamento de BLOBs do Azure garante que os dados não possam ser modificados ou excluídos por qualquer usuário, incluindo usuários com privilégios administrativos de conta.

- **Retenção legal**: armazenamento imutável para o armazenamento de BLOBs do Azure permite que os usuários armazenem informações confidenciais que são críticas para uso de litígio ou comercial em um estado de prova de adulteração até a duração desejada até que a suspensão seja removida. Esse recurso não é limitado apenas a casos de uso jurídico, mas também pode ser considerado como uma suspensão baseada em evento ou um bloqueio corporativo, em que a necessidade de proteger dados com base em gatilhos de evento ou política corporativa é necessária.

O armazenamento imutável oferece suporte aos seguintes recursos:

- **[Suporte à política de retenção baseada em tempo](#time-based-retention-policies)**: os usuários podem definir políticas para armazenar dados para um intervalo especificado. Quando uma política de retenção baseada em tempo é definida, os BLOBs podem ser criados e lidos, mas não modificados ou excluídos. Depois que o período de retenção tiver expirado, os BLOBs poderão ser excluídos, mas não substituídos.

- **[Suporte à política de manutenção legal](#legal-holds)**: se o intervalo de retenção não for conhecido, os usuários poderão definir as isenções legais para armazenar dados imutáveis até que a retenção legal seja apagada.  Quando uma política de manutenção legal é definida, os BLOBs podem ser criados e lidos, mas não modificados ou excluídos. Cada retenção legal é associado a uma marca alfanumérica definida pelo usuário (como uma ID de caso, um nome de evento, etc.) que é usada como uma cadeia de caracteres de identificador. 

- **Suporte para todos os níveis de blob**: as políticas do WORM são independentes da camada de armazenamento do Azure Blob e se aplicam a todas as camadas: quente, legal e arquivamento. Os usuários podem fazer a transição dos dados de suas cargas de trabalho para o nível de custo mais otimizado, mantendo a imutabilidade dos dados.

- **Configuração no nível do contêiner**: os usuários podem configurar políticas de retenção com base no tempo e marcas de retenção legal no nível do contêiner. Ao usar configurações simples no nível do contêiner, os usuários podem criar e bloquear políticas de retenção baseadas em tempo, estender intervalos de retenção, definir e limpar retenção legal, entre outros. Essas políticas se aplicam a todos os blobs no contêiner, existentes e novos. Para uma conta habilitada para o HNS, essas políticas também se aplicam a todos os diretórios em um contêiner.

- **Suporte ao log de auditoria**: cada contêiner inclui um log de auditoria de política. Ele mostra até sete comandos de retenção baseados em tempo para políticas de retenção baseadas em tempo bloqueadas e contém a ID de usuário, o tipo de comando, os carimbos de data/hora e o intervalo de retenção. Para retenções legais, o log contém as marcações de ID de usuário, tipo de comando, carimbos de data/hora e retenção legal. Esse log é retido durante o tempo de vida da política, de acordo com as diretrizes regulatórias da SEC 17a-4 (f). O [log de atividades do Azure](../../azure-monitor/essentials/platform-logs-overview.md) mostra um log mais abrangente de todas as atividades do plano de controle; Embora a habilitação dos [logs de recursos do Azure](../../azure-monitor/essentials/platform-logs-overview.md) retenha e mostre operações de plano de dados. É responsabilidade do usuário armazenar esses logs de forma persistente, conforme o necessário para regulamentações ou outros fins.

## <a name="how-it-works"></a>Como ele funciona

O armazenamento imutável para Armazenamento de Blobs do Azure é compatível com dois tipos de políticas WORM ou imutáveis: retenção baseada em tempo e retenções legais. Quando uma política de retenção baseada em tempo ou uma retenção legal é aplicada em um contêiner, todos os BLOBs existentes são movidos para um estado de WORM imutável em menos de 30 segundos. Todos os novos BLOBs que são carregados nesse contêiner de política protegida também serão movidos para um estado imutável. Depois que todos os BLOBs estiverem em um estado imutável, a política imutável será confirmada e quaisquer operações de substituição ou exclusão no contêiner imutável não serão permitidas. No caso de uma conta habilitada para o HNS, os BLOBs não podem ser renomeados ou movidos para um diretório diferente.

A exclusão da conta de armazenamento e de contêiner também não será permitida se houver BLOBs em um contêiner protegido por uma retenção legal ou uma política baseada em tempo bloqueada. Uma política de retenção legal se protegerá contra exclusão de BLOB, contêiner e conta de armazenamento. As políticas baseadas em tempo desbloqueadas e bloqueadas se protegerão contra a exclusão de BLOBs durante o tempo especificado. As políticas baseadas em tempo desbloqueadas e bloqueadas serão protegidas contra exclusão de contêiner somente se pelo menos um blob existir no contêiner. Somente um contêiner com política baseada em tempo *bloqueada* se protegerá contra exclusões da conta de armazenamento; contêineres com políticas baseadas em tempo desbloqueadas não oferecem proteção de exclusão de conta de armazenamento nem conformidade.

O diagrama a seguir mostra como as políticas de retenção baseadas em tempo e as ações legais impedem operações de gravação e exclusão enquanto elas estão em vigor.

:::image type="content" source="media/storage-blob-immutable-storage/worm-diagram.png" alt-text="Diagrama mostrando como as políticas de retenção e os controles legais impedem operações de gravação e exclusão":::

Para obter mais informações sobre como definir e bloquear políticas de retenção baseadas em tempo, consulte [definir e gerenciar políticas de imutabilidade para o armazenamento de BLOBs](storage-blob-immutability-policies-manage.md).

## <a name="time-based-retention-policies"></a>Políticas de retenção baseadas em tempo

> [!IMPORTANT]
> Uma política de retenção baseada em tempo deve estar *bloqueada* para que o blob esteja em um estado compatível imutável (gravação e exclusão protegida) para SEC 17a-4 (f) e outra conformidade regulatória. É recomendável que você bloqueie a política em um período de tempo razoável, normalmente menos de 24 horas. O estado inicial de uma política de retenção baseada em tempo é *desbloqueado*, permitindo que você teste o recurso e faça alterações na política antes de bloqueá-lo. Embora o estado *desbloqueado* forneça proteção contra imutabilidade, não é recomendável usar o estado *desbloqueado* para qualquer outra finalidade que não seja a avaliação de recursos de curto prazo. 

Quando uma política de retenção baseada em tempo é aplicada em um contêiner, todos os BLOBs no contêiner permanecerão no estado imutável durante o período de retenção *efetivo* . O período de retenção efetivo para BLOBs é igual à diferença entre o tempo de **criação** do blob e o intervalo de retenção especificado pelo usuário. Como os usuários podem estender o intervalo de retenção, o armazenamento imutável usa o valor mais recente do intervalo de retenção especificado pelo usuário para calcular o período de retenção efetivo.

Por exemplo, suponha que um usuário crie uma política de retenção baseada em tempo com um intervalo de retenção de cinco anos. Um blob existente nesse contêiner, _testblob1_, foi criado um ano atrás; Portanto, o período de retenção efetivo para _testblob1_ é de quatro anos. Quando um novo BLOB, _testblob2_, é carregado no contêiner, o período de retenção efetivo para o _testblob2_ é de cinco anos a partir do momento da criação.

Uma política de retenção baseada em tempo desbloqueada é recomendada apenas para teste de recursos e uma política deve ser bloqueada para ser compatível com a SEC 17a-4 (f) e outra conformidade regulatória. Quando uma política de retenção baseada em tempo é bloqueada, a política não pode ser removida e um máximo de cinco aumentos no período de retenção efetivo é permitido.

Os seguintes limites se aplicam às políticas de retenção:

- Para uma conta de armazenamento, o número máximo de contêineres com políticas imutáveis baseadas em tempo bloqueado é de 10.000.
- O intervalo de retenção mínimo é 1 dia. O máximo é de 146.000 dias (400 anos).
- Para um contêiner, o número máximo de edições para estender um intervalo de retenção para políticas imutáveis baseadas em tempo bloqueado é 5.
- Para um contêiner, um máximo de sete logs de auditoria de política de retenção baseadas em tempo são mantidos para uma política bloqueada.

### <a name="allow-protected-append-blobs-writes"></a>Permitir gravações de blobs de acréscimo protegidos

Os blobs de acréscimo são compostos de blocos de dados e otimizados para operações de acréscimo de dados exigidas pelos cenários de auditoria e registro em log. Por design, os blobs de acréscimo permitem apenas a adição de novos blocos ao final do blob. Independentemente da imutabilidade, a modificação ou a exclusão de blocos existentes em um blob de acréscimo não é permitida de maneira fundamental. Para saber mais sobre blobs de acréscimo, confira [sobre blobs de acréscimo](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-append-blobs).

Somente as políticas de retenção baseadas em tempo têm uma `allowProtectedAppendWrites` configuração que permite gravar novos blocos em um blob de acréscimo, mantendo a proteção contra imutabilidade e a conformidade. Se essa configuração estiver habilitada, você poderá criar um blob de acréscimo diretamente no contêiner protegido por política e continuar a adicionar novos blocos de dados ao final dos BLOBs de acréscimo existentes usando a API *AppendBlock* . Somente novos blocos podem ser adicionados e os blocos existentes não podem ser modificados ou excluídos. A proteção de imutabilidade de retenção de tempo ainda se aplica, impedindo a exclusão do blob de acréscimo até que o período de retenção efetivo tenha decorrido. A habilitação dessa configuração não afeta o comportamento de imutabilidade de blobs de blocos ou BLOBs de páginas.

Como essa configuração faz parte de uma política de retenção baseada em tempo, os blobs de acréscimo ainda permanecem no estado imutável pela duração do período de retenção *efetivo* . Como novos dados podem ser acrescentados além da criação inicial do blob de acréscimo, há uma pequena diferença na forma como o período de retenção é determinado. A retenção efetiva é a diferença entre a hora da **última modificação** do blob de acréscimo e o intervalo de retenção especificado pelo usuário. Da mesma forma, quando o intervalo de retenção é estendido, o armazenamento imutável usa o valor mais recente do intervalo de retenção especificado pelo usuário para calcular o período de retenção efetivo.

Por exemplo, suponha que um usuário crie uma política de retenção baseada em tempo com `allowProtectedAppendWrites` habilitado e um intervalo de retenção de 90 dias. Um blob de acréscimo, _logblob1_, é criado no contêiner hoje, novos logs continuam a ser adicionados ao blob de acréscimo pelos próximos 10 dias; Portanto, o período de retenção efetivo para o _logblob1_ é de 100 dias a partir de hoje (a hora de seu último acréscimo + 90 dias).

Políticas de retenção baseadas em tempo desbloqueadas permitem que a `allowProtectedAppendWrites` configuração seja habilitada e desabilitada a qualquer momento. Quando a política de retenção baseada em tempo estiver bloqueada, a `allowProtectedAppendWrites` configuração não poderá ser alterada.

As políticas de retenção legal não podem habilitar `allowProtectedAppendWrites` e qualquer retenção legal anulará a propriedade ' allowProtectedAppendWrites '. Se uma retenção legal for aplicada a uma política de retenção baseada em tempo com `allowProtectedAppendWrites` habilitada, a API *AppendBlock* falhará até que a retenção legal seja levantada.

## <a name="legal-holds"></a>Retenções legais

As isenções legais são suspensões temporárias que podem ser usadas para fins de investigação legal ou políticas de proteção geral. Cada política de retenção legal precisa ser associada a uma ou mais marcas. As marcas são usadas como um identificador nomeado, como uma ID de caso ou evento, para categorizar e descrever a finalidade da espera.

Um contêiner pode ter uma retenção legal e uma política de retenção baseada no tempo ao mesmo tempo. Todos os blobs nesse contêiner permanecem no estado imutável até que todas as retenções legais sejam apagadas, mesmo que o período de retenção efetivo tenha expirado. Por outro lado, um blob permanece em um estado imutável até que o período de retenção efetivo expire, mesmo que todas as retenções legais tenham sido apagadas.

Os seguintes limites se aplicam a isenções legais:

- Para uma conta de armazenamento, o número máximo de contêineres com uma configuração de retenção legal é 10.000.
- Para um contêiner, o número máximo de tags de retenção legal é 10.
- O comprimento mínimo de uma marca de retenção legal é de três caracteres alfanuméricos. O comprimento máximo é de 23 caracteres alfanuméricos.
- Para um contêiner, um máximo de 10 logs de auditoria de política de retenção legal são mantidos durante a política.

## <a name="scenarios"></a>Cenários

A tabela a seguir mostra os tipos de operações de armazenamento de BLOBs que estão desabilitadas para os diferentes cenários imutáveis. Para obter mais informações, consulte a documentação da [API REST do serviço blob do Azure](/rest/api/storageservices/blob-service-rest-api) .

| Cenário | Estado do blob | Operações de blob negadas | Proteção de contêiner e conta |
|--|--|--|--|
| O intervalo efetivo de retenção no blob ainda não expirou e/ou a retenção legal está definida | Imutável: protegido contra exclusão e gravação | Colocar blob<sup>1</sup>, colocar bloco<sup>1</sup>, colocar lista de blocos<sup>1</sup>, excluir contêiner, excluir BLOB, definir metadados de BLOB, colocar página, definir propriedades de BLOB, BLOB de instantâneo, BLOB de cópia incremental, bloco de acréscimo<sup>2</sup> | Exclusão de contêiner negada; Exclusão da conta de armazenamento negada |
| O intervalo de retenção efetivo no blob expirou e nenhuma retenção legal está definida | Protegido apenas contra gravação  (operações de exclusão são permitidas) | Colocar blob<sup>1</sup>, colocar bloco<sup>1</sup>, colocar lista de blocos<sup>1</sup>, definir metadados de BLOB, colocar página, definir propriedades de BLOB, BLOB de instantâneo, BLOB de cópia incremental, bloco de acréscimo<sup>2</sup> | Exclusão de contêiner negada se pelo menos 1 blob existir no contêiner protegido; Exclusão de conta de armazenamento negada somente para políticas baseadas em tempo *bloqueadas* |
| Nenhuma política de WORM aplicada (sem retenção baseada em tempo e sem marca de suspensão legal) | Mutável | Nenhum | Nenhum |

<sup>1</sup> o serviço blob permite que essas operações criem um novo BLOB uma vez. Todas as operações de substituição subsequentes em um caminho de blob existente em um contêiner imutável não são permitidas.

<sup>2</sup> o bloco Append só é permitido para políticas de retenção baseadas em tempo com a `allowProtectedAppendWrites` Propriedade habilitada. Para obter mais informações, consulte a seção [permitir gravações de blobs de anexação protegidas](#allow-protected-append-blobs-writes) .

> [!IMPORTANT]
> Algumas cargas de trabalho, como o [backup do SQL para URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url), criam um blob e, em seguida, adicionam a ele. Se o contêiner tiver uma política de retenção baseada em tempo ativa ou uma suspensão legal em vigor, esse padrão não terá sucesso.

## <a name="pricing"></a>Preços

Não há nenhum custo adicional para usar esse recurso. Os dados imutáveis são cobrados da mesma maneira que os dados mutáveis. Para obter detalhes de preços no armazenamento de BLOBs do Azure, consulte a [página de preços do armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="faq"></a>Perguntas frequentes

**Você pode fornecer documentação sobre a conformidade do WORM?**

Sim. Para documentar a conformidade, a Microsoft manteve uma importante empresa de avaliação independente especializada em gerenciamento de registros e governança de informações, Cohasset Associates, para avaliar o armazenamento de BLOBs imutável e sua conformidade com os requisitos específicos para o setor de serviços financeiros. Cohasset validou que o armazenamento de blob imutável, quando usado para manter BLOBs baseados em tempo em um estado de WORM, atende aos requisitos de armazenamento relevantes do CFTC Rule 1.31 (c)-(d), à regra de FINRA 4511 e à norma SEC 17a-4. A Microsoft direcionou esse conjunto de regras, pois representam as diretrizes mais prescritivas globalmente para retenção de registros para instituições financeiras. O relatório Cohasset está disponível na [central de confiabilidade do serviço da Microsoft](https://aka.ms/AzureWormStorage). Para solicitar uma carta de atestado da Microsoft sobre a conformidade de imutabilidade do WORM, entre em contato com o suporte do Azure.

**O recurso se aplica apenas a blobs de blocos e blobs de acréscimo, ou também a blobs de páginas?**

O armazenamento imutável pode ser usado com qualquer tipo de blob como definido no nível de contêiner, mas é recomendável usar o WORM para contêineres que armazenam principalmente blobs de bloco e blobs de acréscimo. Os BLOBs existentes em um contêiner serão protegidos por uma política de WORM definida recentemente. Mas todos os novos blobs de páginas precisam ser criados fora do contêiner do WORM e, em seguida, copiados no. Uma vez copiado em um contêiner de WORM, não são permitidas mais alterações em um blob de páginas. Definir uma política de WORM em um contêiner que armazena VHDs (BLOBs de páginas) para qualquer máquina virtual ativa é desencorajado, pois ele bloqueará o disco da VM. Recomendamos que você examine minuciosamente a documentação e teste seus cenários antes de bloquear as políticas baseadas em tempo.

**É necessário criar uma nova conta de armazenamento para usar esse recurso?**

Não, você pode usar o armazenamento imutável com todas as contas de uso geral v1, BlobStorage ou BlockBlobStorage para fins gerais existentes ou criados recentemente. As contas de armazenamento v1 de uso geral são suportadas, mas recomendamos a atualização para a versão de uso geral v2, de modo que você possa aproveitar mais recursos. Para obter informações sobre como atualizar uma conta de armazenamento v1 de uso geral existente, consulte [atualizar uma conta de armazenamento](../common/storage-account-upgrade.md).

**Posso aplicar uma política de retenção baseada em tempo e em retenção legal?**

Sim, um contêiner pode ter um controle legal e uma política de retenção baseada em tempo ao mesmo tempo; no entanto, a configuração ' allowProtectedAppendWrites ' não será aplicada até que a retenção legal seja apagada. Todos os blobs nesse contêiner permanecem no estado imutável até que todas as retenções legais sejam apagadas, mesmo que o período de retenção efetivo tenha expirado. Por outro lado, um blob permanece em um estado imutável até que o período de retenção efetivo expire, mesmo que todas as retenções legais tenham sido apagadas. 

**As políticas de retenção legal são apenas para procedimentos legais ou existem outros cenários de uso?**

Não, a retenção legal é apenas o termo geral usado para uma política de retenção não baseada em tempo. Ele não precisa ser usado apenas para procedimentos relacionados a litígios. As políticas de retenção legal são úteis para desabilitar a substituição e as exclusões para proteger dados importantes do WORM corporativo, em que o período de retenção é desconhecido. Você pode usá-lo como uma política corporativa para proteger suas cargas de trabalho de WORMs de missão crítica ou usá-las como uma política de preparo antes que um gatilho de evento personalizado exija o uso de uma política de retenção baseada em tempo. 

**Posso remover uma política de retenção baseada em tempo _bloqueada_ ou uma retenção legal?**

Somente políticas de retenção baseadas em tempo desbloqueadas podem ser removidas de um contêiner. Quando uma política de retenção baseada em tempo é bloqueada, ela não pode ser removida; somente as extensões de período de retenção efetivas são permitidas. As marcas de retenção legal podem ser excluídas. Quando todas as marcas legais são excluídas, a retenção legal é removida.

**O que acontecerá se eu tentar excluir um contêiner com uma política de retenção baseada em tempo ou uma retenção legal?**

A operação excluir contêiner falhará se pelo menos um blob existir no contêiner com uma política de retenção baseada em tempo bloqueada ou desbloqueada ou se o contêiner tiver uma suspensão legal. A operação excluir contêiner só terá sucesso se nenhum blob existir no contêiner e não houver nenhuma retenção legal. 

**O que acontecerá se eu tentar excluir uma conta de armazenamento com um contêiner que tenha uma política de retenção baseada em tempo ou uma retenção legal?**

A exclusão da conta de armazenamento falhará se houver pelo menos um contêiner com um conjunto de contenção legal ou uma política baseada em tempo **bloqueada** . Um contêiner com uma política baseada em tempo desbloqueada não protege contra a exclusão da conta de armazenamento. Você deve remover todas as isenções legais e excluir todos os contêineres **bloqueados** antes de poder excluir a conta de armazenamento. Para obter informações sobre exclusão de contêiner, consulte a pergunta anterior. Você também pode aplicar mais proteções de exclusão para sua conta de armazenamento com [bloqueios de Azure Resource Manager](../../azure-resource-manager/management/lock-resources.md).

**Posso mover os dados entre diferentes camadas de BLOB (quente, esporádico, arquivo morto) quando o blob está no estado imutável?**

Sim, você pode usar o comando definir camada de BLOB para mover dados entre as camadas de BLOB e, ao mesmo tempo, manter os dados no estado imutável em conformidade. O armazenamento imutável tem suporte nas camadas de blob frequente, esporádica e de arquivos.

**O que acontece se eu não conseguir pagar e meu intervalo de retenção não expirou?**

No caso de não pagamento, as políticas normais de retenção de dados serão aplicadas conforme estipulado nos termos e condições do seu contrato com a Microsoft. Para obter informações gerais, consulte [Gerenciamento de dados na Microsoft](https://www.microsoft.com/en-us/trust-center/privacy/data-management). 

**Há oferta de um período de avaliação ou de cortesia para apenas experimentar o recurso?**

Sim. Quando uma política de retenção baseada em tempo é criada pela primeira vez, ela está em um estado *desbloqueado* . Nesse estado, você pode fazer qualquer alteração desejada no intervalo de retenção, como aumentar ou diminuir e até excluir a política. Depois que a política é bloqueada, ela permanece bloqueada até que o intervalo de retenção expire. Essa política bloqueada impede a exclusão e a modificação no intervalo de retenção. É altamente recomendável que você use o *desbloqueada* apenas para fins de avaliação de estado e a política de bloqueio dentro de um período de 24 horas. Essas práticas ajudarão-lo a cumprir 17a-4(f) s e outros regulamentos.

**Posso usar a exclusão reversível juntamente com políticas de blob imutáveis?**

Sim, se os requisitos de conformidade permitirem que a exclusão reversível seja habilitada. A [exclusão reversível para o armazenamento de BLOBs do Azure](./soft-delete-blob-overview.md) aplica-se a todos os contêineres em uma conta de armazenamento, independentemente de uma política de retenção baseada em tempo ou em retenção É recomendável habilitar a exclusão reversível para proteção adicional antes de qualquer política de WORM imutável ser aplicada e confirmada.

**Para uma conta habilitada para o HNS, posso renomear ou mover um blob quando o blob estiver no estado imutável?**
Não, tanto o nome quanto a estrutura de diretório são considerados dados importantes no nível do contêiner que não podem ser modificados depois que a política imutável está em vigor. Renomear e mover estão disponíveis somente para contas habilitadas para o HNS em geral.

## <a name="next-steps"></a>Próximas etapas

- [Definir e gerenciar políticas de imutabilidade para o armazenamento de blobs](storage-blob-immutability-policies-manage.md)
- [Definir regras para camada e exclusão automática de dados de blob com o gerenciamento do ciclo de vida](storage-lifecycle-management-concepts.md)
- [Exclusão reversível para blobs do Armazenamento do Azure ](./soft-delete-blob-overview.md)
- [Proteger assinaturas, grupos de recursos e recursos com bloqueios de Azure Resource Manager](../../azure-resource-manager/management/lock-resources.md).
