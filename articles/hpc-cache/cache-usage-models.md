---
title: Modelos de uso do cache HPC do Azure
description: Descreve os diferentes modelos de uso de cache e como escolher entre eles para definir o cache somente leitura ou de leitura/gravação e controlar outras configurações de cache
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 03/15/2021
ms.author: v-erkel
ms.openlocfilehash: 3ad252520ca0cf7acdb3c84ef1da87c8076f3172
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104775707"
---
# <a name="understand-cache-usage-models"></a>Entender os modelos de uso de cache

Os modelos de uso de cache permitem que você personalize como o cache HPC do Azure armazena arquivos para acelerar seu fluxo de trabalho.

## <a name="basic-file-caching-concepts"></a>Conceitos básicos de cache de arquivo

O cache de arquivos é como o cache HPC do Azure acelera as solicitações do cliente. Ele usa estas práticas básicas:

* **Cache de leitura** – o cache HPC do Azure mantém uma cópia dos arquivos que os clientes solicitam do sistema de armazenamento. Na próxima vez que um cliente solicitar o mesmo arquivo, o cache do HPC poderá fornecer a versão em seu cache em vez de ter que obtê-la novamente do sistema de armazenamento back-end.

* **Cache de gravação** – opcionalmente, o cache HPC do Azure pode armazenar uma cópia de todos os arquivos alterados enviados dos computadores cliente. Se vários clientes fizerem alterações no mesmo arquivo em um curto período, o cache poderá coletar todas as alterações no cache em vez de ter que gravar cada alteração individualmente no sistema de armazenamento de back-end.

  Após um período de tempo especificado sem alterações, o cache move o arquivo para o sistema de armazenamento de longo prazo.

  Se o cache de gravação estiver desabilitado, o cache não armazenará o arquivo alterado e o gravará imediatamente no sistema de armazenamento de back-end.

* **Atraso de write-back** -para um cache com cache de gravação ativado, o atraso de write-back é a quantidade de tempo que o cache espera por alterações de arquivo adicionais antes de copiar o arquivo para o sistema de armazenamento de back-end.

* **Verificação de back-end** – a configuração de verificação de back-end determina com que frequência o cache compara sua cópia local de um arquivo com a versão remota no sistema de armazenamento de back-end. Se a cópia de back-end for mais recente do que a cópia armazenada em cache, o cache buscará a cópia remota e a armazenará para solicitações futuras.

  A configuração de verificação de back-end mostra quando o cache compara *automaticamente* seus arquivos com os arquivos de origem no armazenamento remoto. No entanto, você pode forçar o cache do Azure HPC a comparar arquivos executando uma operação de diretório que inclui uma solicitação readdirplus. Readdirplus é uma API padrão do NFS (também chamada de leitura estendida) que retorna os metadados do diretório, o que faz com que o cache compare e atualize arquivos.

Os modelos de uso incorporados ao cache HPC do Azure têm valores diferentes para essas configurações, para que você possa escolher a melhor combinação para sua situação.

## <a name="choose-the-right-usage-model-for-your-workflow"></a>Escolha o modelo de uso certo para seu fluxo de trabalho

Você deve escolher um modelo de uso para cada destino de armazenamento montado em NFS que você usa. Os destinos do armazenamento de BLOBs do Azure têm um modelo de uso interno que não pode ser personalizado.

Os modelos de uso do cache HPC permitem que você escolha como balancear a resposta rápida com o risco de obter dados obsoletos. Se você quiser otimizar a velocidade de leitura de arquivos, talvez não se importa se os arquivos no cache são verificados em relação aos arquivos de back-end. Por outro lado, se você quiser ter certeza de que os arquivos estão sempre atualizados com o armazenamento remoto, escolha um modelo que verifica com frequência.

Estas são as opções de modelo de uso:

* **Ler gravações pesadas e frequentes** – Use essa opção se você quiser acelerar o acesso de leitura para arquivos que são estáticos ou raramente alterados.

  Essa opção armazena em cache as leituras do cliente, mas não armazena em cache as gravações. Ele passa gravações para o armazenamento de back-end imediatamente.
  
  Os arquivos armazenados no cache não são automaticamente comparados aos arquivos no volume de armazenamento NFS. (Leia a descrição da verificação de back-end acima para saber como compará-las manualmente.)

  Não use essa opção se houver um risco de que um arquivo possa ser modificado diretamente no sistema de armazenamento sem primeiro gravá-lo no cache. Se isso acontecer, a versão em cache do arquivo estará fora de sincronia com o arquivo de back-end.

* **Mais de 15% de gravações** – essa opção acelera o desempenho de leitura e gravação. Ao usar essa opção, todos os clientes devem acessar arquivos por meio do cache HPC do Azure em vez de montar o armazenamento de back-end diretamente. Os arquivos armazenados em cache terão alterações recentes que ainda não foram copiadas para o back-end.

  Nesse modelo de uso, os arquivos no cache só são verificados em relação aos arquivos no armazenamento de back-end a cada oito horas. Pressupõe-se que a versão em cache do arquivo seja mais atual. Um arquivo modificado no cache é gravado no sistema de armazenamento de back-end depois que ele está no cache por 20 minutos<!-- an hour --> sem alterações adicionais.

* **Os clientes gravam no destino NFS, ignorando o cache** -escolha esta opção se algum cliente em seu fluxo de trabalho gravar dados diretamente no sistema de armazenamento sem primeiro gravar no cache ou se você quiser otimizar a consistência dos dados. Os arquivos que os clientes solicitam são armazenados em cache (leituras), mas quaisquer alterações nesses arquivos do cliente (gravações) não são armazenadas em cache. Eles são passados diretamente para o sistema de armazenamento de back-end.

  Com esse modelo de uso, os arquivos no cache são verificados frequentemente em relação às versões de back-end para atualizações-a cada 30 segundos. Essa verificação permite que os arquivos sejam alterados fora do cache enquanto mantêm a consistência dos dados.

  > [!TIP]
  > Esses três primeiros modelos de uso básico podem ser usados para lidar com a maioria dos fluxos de trabalho de cache do Azure HPC. As próximas opções são para cenários menos comuns.

* **Mais de 15% de gravações, verificando o servidor de backup em busca de alterações a cada 30 segundos** e **maior que 15% de gravações, verificando o servidor de backup em busca de alterações a cada 60 segundos** -essas opções são criadas para fluxos de trabalho onde você deseja acelerar as leituras e gravações, mas há uma chance de que outro usuário grave diretamente no sistema de armazenamento de back-end Por exemplo, se vários conjuntos de clientes estiverem trabalhando nos mesmos arquivos de locais diferentes, esses modelos de uso podem fazer sentido balancear a necessidade de acesso rápido a arquivos com baixa tolerância a um conteúdo obsoleto da origem.

* **Mais de 15% de gravações, write-back para o servidor a cada 30 segundos** -essa opção foi projetada para o cenário em que vários clientes estão modificando ativamente os mesmos arquivos ou se alguns clientes acessam o armazenamento de back-end diretamente em vez de montar o cache.

  As gravações de back-end frequentes afetam o desempenho do cache, portanto, você deve considerar usar o modelo de uso **maior que 15% de gravação** se houver um baixo risco de conflito de arquivo – por exemplo, se você souber que clientes diferentes estão trabalhando em diferentes áreas do mesmo conjunto de arquivos.

* **Leia pesado, verificando o servidor de backup a cada 3 horas** -essa opção prioriza leituras rápidas no lado do cliente, mas também atualiza arquivos armazenados em cache do sistema de armazenamento de back-end regularmente, diferentemente do modelo de uso de **gravações pesadas e pouco frequentes de leitura** .

Esta tabela resume as diferenças do modelo de uso:

[!INCLUDE [usage-models-table.md](includes/usage-models-table.md)]

Se você tiver dúvidas sobre o melhor modelo de uso para o fluxo de trabalho do cache HPC do Azure, fale com seu representante do Azure ou abra uma solicitação de suporte para obter ajuda.

## <a name="next-steps"></a>Próximas etapas

* [Adicionar destinos de armazenamento](hpc-cache-add-storage.md) ao cache HPC do Azure
