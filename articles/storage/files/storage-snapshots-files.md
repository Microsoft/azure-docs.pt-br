---
title: Visão geral do instantâneo de compartilhamento dos Arquivos do Azure | Microsoft Docs
description: Um instantâneo de compartilhamento é uma versão somente leitura de um compartilhamento dos Arquivos do Azure feito em determinado momento, como uma maneira de fazer backup do compartilhamento.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 01/17/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 7305c6a5047bd09f6c9bda018748643ff81fe893
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63763486"
---
# <a name="overview-of-share-snapshots-for-azure-files"></a>Visão geral de instantâneos de compartilhamento para Arquivos do Azure 
Os Arquivos do Azure fornecem a funcionalidade de tirar instantâneos de compartilhamentos de arquivos. Os instantâneos de compartilhamento capturam o estado de compartilhamento naquele ponto no tempo. Neste artigo, descreveremos quais recursos os instantâneos de compartilhamento fornecem e como você pode aproveitá-los no seu caso de uso personalizado.

## <a name="when-to-use-share-snapshots"></a>Quando usar os instantâneos de compartilhamento

### <a name="protection-against-application-error-and-data-corruption"></a>Proteção contra corrupção de dados e erro do aplicativo
Os aplicativos que usam compartilhamentos dos Arquivos do Azure executam operações, como leitura, gravação, armazenamento, transmissão e processamento. Um aplicativo pode ser configurado incorretamente ou ter a introdução não intencional de um bug que gera a substituição acidental ou danos a alguns blocos. Para ajudar a se proteger contra esses cenários, você pode tirar um instantâneo de compartilhamento antes de implantar o novo código de aplicativo. Se um bug ou erro do aplicativo for introduzido com a nova implantação, você poderá voltar para uma versão anterior dos dados no compartilhamento de arquivos. 

### <a name="protection-against-accidental-deletions-or-unintended-changes"></a>Proteção contra exclusões acidentais ou alterações indesejadas
Imagine que você está trabalhando em um arquivo de texto em um compartilhamento de arquivos. Quando o arquivo de texto é fechado, você perde a capacidade de desfazer suas alterações. Nesses casos, será necessário recuperar uma versão anterior do arquivo. Você poderá usar instantâneos de compartilhamento para recuperar versões anteriores do arquivo se ele for renomeado ou excluído acidentalmente.

### <a name="general-backup-purposes"></a>Objetivos gerais de backup
Depois de criar um compartilhamento de arquivos, você pode criar periodicamente um instantâneo de compartilhamento do seu compartilhamento de arquivos para usá-lo no backup de dados. O instantâneo de compartilhamento, quando executado periodicamente, ajuda a manter versões anteriores dos dados que podem ser usadas em futuras auditorias exigidas ou na recuperação de desastre.

## <a name="capabilities"></a>Funcionalidades
O instantâneo de compartilhamento é uma cópia somente leitura dos dados em determinado momento. Você pode criar, excluir e gerenciar instantâneos usando a API REST. Os mesmos recursos também estão disponíveis na biblioteca de cliente, na CLI do Azure e no portal do Azure. 

Você pode exibir instantâneos de um compartilhamento usando a API REST e o SMB. Você pode recuperar a lista de versões do diretório ou arquivo e montar uma versão específica diretamente como uma unidade (disponível apenas no Windows - consulte [Limites](#limits)). 

Quando um instantâneo de compartilhamento é criado, ele pode ser lido, copiado ou excluído, mas não modificado. Você não pode copiar um instantâneo de compartilhamento inteiro para outra conta de armazenamento. Você deve fazer isto arquivo por arquivo, usando AzCopy ou outros mecanismos de cópias.

A capacidade de instantâneo de compartilhamento é fornecida no nível de compartilhamento de arquivo. A recuperação é fornecida no nível de arquivo individual, para permitir a restauração de arquivos individuais. Você pode restaurar um compartilhamento de arquivo completo usando o SMB, a API REST, o portal, a biblioteca de cliente ou ferramentas de PowerShell/CLI.

Um instantâneo de compartilhamento de um compartilhamento de arquivos é idêntico ao seu compartilhamento de arquivo de base. A única diferença é que um valor de **DateTime** é acrescentado ao URI do compartilhamento para indicar o horário em que o compartilhamento de instantâneo foi tirado. Por exemplo, se um URI de compartilhamento de arquivo for http://storagesample.core.file.windows.net/myshare, o URI do instantâneo será semelhante a:
```
http://storagesample.file.core.windows.net/myshare?snapshot=2011-03-09T01:42:34.9360000Z
```

Os instantâneos de compartilhamento persistem até que sejam excluídos deliberadamente. Um instantâneo de compartilhamento não pode sobreviver além do seu compartilhamento de arquivo base. Você pode enumerar os instantâneos associados ao compartilhamento de arquivos principal para acompanhar seus instantâneos atuais. 

Quando você cria um instantâneo de compartilhamento de um compartilhamento de arquivos, os arquivos que residem nas propriedades do sistema do compartilhamento são copiados para o instantâneo de compartilhamento com os mesmos valores. Os arquivos principais e os metadados do compartilhamento de arquivos também são copiados no instantâneo, a menos que você especifique metadados diferentes para o instantâneo ao criá-lo.

Você não pode excluir um compartilhamento que tenha instantâneos de compartilhamento sem excluir todos os seus instantâneos de compartilhamento primeiro.

## <a name="space-usage"></a>Uso de espaço 
Os instantâneos de compartilhamento são incrementais por natureza. Somente os dados que foram alterados depois que o instantâneo mais recente do compartilhamento é salvo. Isso minimiza o tempo necessário para criar o instantâneo de compartilhamento e economiza nos custos de armazenamento. As eventuais operações de gravação no objeto ou operação de atualização de metadados ou de propriedade são contadas como "conteúdo alterado" e serão armazenadas no instantâneo de compartilhamento. 

Para economizar espaço, você pode excluir o instantâneo de compartilhamento cujo período tenha tido uma variação maior.

Embora os instantâneos de compartilhamento sejam salvos incrementalmente, você precisa manter somente o instantâneo mais recente do compartilhamento para restaurá-lo. Quando você exclui um instantâneo de compartilhamento, somente os dados exclusivos desse instantâneo de compartilhamento são removidos. Os instantâneos ativos contêm todas as informações necessárias para procurar e restaurar seus dados (da hora em que o instantâneo de compartilhamento foi tirado) no local original ou em um local alternativo. Você pode restaurar no nível do item.

Instantâneos não contam em relação ao limite de compartilhamento de 5 TB. Não há nenhum limite para a quantidade total de espaço ocupado pelo instantâneo de compartilhamento. Os limites de conta de armazenamento ainda se aplicam.

## <a name="limits"></a>limites
O número máximo de instantâneos de compartilhamento que os Arquivos do Azure permitem atualmente é 200. Depois de 200 instantâneos de compartilhamento, os instantâneos mais antigos precisarão ser excluídos para criar novos instantâneos de compartilhamento. 

Não há nenhum limite de chamadas simultâneas para criar o instantâneo de compartilhamento. Não há nenhum limite de quantidade de espaço que os instantâneos de compartilhamento de determinado compartilhamento de arquivos pode consumir. 

Atualmente, não é possível montar instantâneos compartilhados no Linux. Isso ocorre porque o cliente SMB do Linux não tem suporte para instantâneos de montagem como o Windows.

## <a name="copying-data-back-to-a-share-from-share-snapshot"></a>Copiando dados para um compartilhamento de um instantâneo de compartilhamento
As operações de cópia que envolvem arquivos e instantâneos de compartilhamento seguem estas regras:

Você pode copiar arquivos individuais de um instantâneo de compartilhamento de arquivos em seu compartilhamento base ou em outro local. Você pode restaurar uma versão anterior de um arquivo ou restaurar o compartilhamento de arquivos completo copiando arquivo por arquivo do instantâneo de compartilhamento. O instantâneo de compartilhamento não será promovido a compartilhamento base. 

O instantâneo de compartilhamento permanece intacto após a cópia, mas o compartilhamento de arquivos base é substituído por uma cópia dos dados que estavam disponíveis no instantâneo de compartilhamento. Todos os arquivos restaurados contam como “conteúdo alterado”.

Você pode copiar um arquivo em um instantâneo de compartilhamento para um destino com um nome diferente. O arquivo de destino resultante é um arquivo gravável que não é um instantâneo de compartilhamento.

Quando um arquivo de destino é substituído por uma cópia, todos os instantâneos de compartilhamento associados ao arquivo de destino original permanecem intactos.

## <a name="general-best-practices"></a>Práticas recomendadas gerais 
Ao executar infraestrutura do Azure, automatize os backups para recuperação de dados sempre que possível. As ações automáticas são mais confiáveis do que os processos manuais, ajudando a melhorar a capacidade de recuperação e proteção dos dados. Você pode usar a API REST, o SDK do cliente ou scripts para automação.

Antes de implantar o agendador de instantâneos de compartilhamento, leve em conta cuidadosamente as configurações de retenção e a frequência dos instantâneos de compartilhamento para evitar incorrer em encargos desnecessários.

Compartilhamentos de instantâneos fornecem apenas a proteção no nível de arquivo. Compartilhamentos de instantâneos não impedem exclusões de digitação acidental em uma conta de armazenamento ou compartilhamento de arquivos. Para ajudar a proteger a conta de armazenamento de exclusões acidentais, você pode bloquear a conta de armazenamento ou o grupo de recursos.

## <a name="next-steps"></a>Próximos passos
- Trabalhar com instantâneos de compartilhamento em:
    - [PowerShell](storage-how-to-use-files-powershell.md)
    - [CLI](storage-how-to-use-files-cli.md)
    - [Windows](storage-how-to-use-files-windows.md#accessing-share-snapshots-from-windows)
    - [Perguntas frequentes sobre instantâneo de compartilhamento](storage-files-faq.md#share-snapshots)
