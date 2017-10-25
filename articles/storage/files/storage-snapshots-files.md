---
title: "Visão geral do instantâneo de compartilhamento dos Arquivos do Azure | Microsoft Docs"
description: "O instantâneo de compartilhamento dos Arquivos do Azure é uma versão somente leitura de um compartilhamento dos Arquivos do Azure feito em determinado momento. Quando um instantâneo de compartilhamento é criado, ele pode ser lido, copiado ou excluído, mas não modificado. Os instantâneos de compartilhamento fornecem uma maneira de fazer backup do compartilhamento da maneira como ele aparece em um momento específico."
services: storage
documentationcenter: .net
author: renash
manager: aungoo
editor: tysonn
ms.assetid: edabe3ee-688b-41e0-b34f-613ac9c3fdfd
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2017
ms.author: renash
ms.openlocfilehash: 70b7fec318b527c6c67bd281892c10814712bbc6
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-files-share-snapshot-preview-overview"></a>Visão geral do instantâneo de compartilhamento dos Arquivos do Azure
Os Arquivos do Azure fornecem a funcionalidade de tirar instantâneos de compartilhamentos de arquivos. Os instantâneos de compartilhamento (versão prévia) capturam o estado de compartilhamento no momento. Neste artigo, descreveremos quais recursos os instantâneos de compartilhamento fornecem e como você pode aproveitá-los no seu caso de uso personalizado.


## <a name="when-to-use-share-snapshots"></a>Quando usar os instantâneos de compartilhamento

### <a name="protection-against-application-error-and-data-corruption"></a>Proteção contra corrupção de dados e erro do aplicativo
-------------------------------------------------------------------------

Os aplicativos que usam compartilhamentos dos Arquivos do Azure executam várias operações, como leitura, gravação, armazenamento, transmissão ou processamento. Um aplicativo pode ser configurado incorretamente ou ter a introdução não intencional de um bug que gera a substituição ou danos a alguns blocos. Para proteger contra esses cenários, você pode tirar um instantâneo de compartilhamento antes de implantar o novo código de aplicativo e, se um erro de bug ou aplicativo for introduzido com a nova implantação, você poderá voltar para uma versão anterior dos dados que residem no compartilhamento de arquivos. 

### <a name="protection-against-accidental-deletions-or-unintended-changes"></a>Proteção contra exclusões acidentais ou alterações indesejadas
----------------------------------------------------------------------------------------------

Imagine que um usuário está trabalhando em um arquivo de texto que reside em um compartilhamento de arquivos. Quando o arquivo de texto é fechado, perdemos a capacidade de desfazer nossas alterações. Nesses casos, será necessário recuperar uma versão anterior do arquivo. Os instantâneos de compartilhamento permitem recuperar versões anteriores do arquivo quando ele é renomeado ou excluído acidentalmente.

### <a name="general-backup-purposes"></a>Objetivos gerais de backup
---------------------------

Depois de criar um compartilhamento dos Arquivos do Azure, você pode criar periodicamente um instantâneo de compartilhamento do seu compartilhamento dos Arquivos do Azure para usá-lo no backup de dados. O instantâneo de compartilhamento, quando executado periodicamente, ajuda a manter versões anteriores dos dados que podem ser usadas em futuras auditorias exigidas ou na recuperação de desastre.

## <a name="share-snapshot-capabilities"></a>Funcionalidades do instantâneo de compartilhamento

O instantâneo de compartilhamento é uma cópia somente leitura dos dados em determinado momento. Você pode criar, excluir e gerenciar instantâneos usando a API REST. Os mesmos recursos também estão disponíveis na biblioteca de cliente, na CLI e no portal do Azure. A integração do PowerShell para o instantâneo de compartilhamento também estará disponível em breve. Você pode exibir instantâneos de um compartilhamento usando a API REST e o SMB. Os clientes podem recuperar a lista de versões de arquivo ou diretório e também podem montar uma versão específica diretamente como uma unidade. Quando um instantâneo de compartilhamento é criado, ele pode ser lido, copiado ou excluído, mas não modificado. Você não pode copiar um instantâneo de compartilhamento inteiro para outra conta de armazenamento. É necessário fazer isso usando o azcopy ou outro mecanismo de cópias.

A funcionalidade de instantâneo de compartilhamento é fornecida no nível do compartilhamento de arquivo, enquanto que a recuperação é fornecida no nível do arquivo individual para permitir a restauração de arquivos individuais. Você pode restaurar um compartilhamento de arquivo completo usando o SMB, a API REST, o portal, a biblioteca de cliente ou ferramentas do PowerShell/da CLI.

Um instantâneo de compartilhamento de um compartilhamento de arquivos é idêntico ao seu compartilhamento de arquivo base, exceto pelo fato de o URI de compartilhamento ter um valor **DateTime** acrescentado ao URI de compartilhamento para indicar o horário em que o instantâneo de compartilhamento foi tirado. Por exemplo, se um URI de compartilhamento de arquivos for http://storagesample.core.file.windows.net/myshare, o URI do instantâneo de compartilhamento é semelhante a
```
http://storagesample.core.file.windows.net/myshare?snapshot=2011-03-09T01:42:34.9360000Z.
```

Os instantâneos de compartilhamento persistem até que sejam excluídos deliberadamente. Um instantâneo de compartilhamento não pode sobreviver além do seu compartilhamento de arquivo base. Você pode enumerar os instantâneos associados ao compartilhamento de arquivos principal para acompanhar seus instantâneos atuais. Quando você cria um instantâneo de compartilhamento de um compartilhamento de arquivos, os arquivos que residem nas propriedades do sistema do compartilhamento são copiados para o instantâneo de compartilhamento com os mesmos valores. Os arquivos principais e os metadados do compartilhamento de arquivos também são copiados no instantâneo, a menos que você especifique metadados diferentes para o instantâneo ao criá-lo.

Você não pode excluir um compartilhamento que tenha instantâneos de compartilhamento sem excluir todos os seus instantâneos de compartilhamento primeiro.


## <a name="share-snapshot-space-usage"></a>Uso de espaço do instantâneo de compartilhamento 

Os instantâneos de compartilhamento são incrementais por natureza, o que significa que somente os dados que foram alterados após o instantâneo de compartilhamento mais recente são salvos. Isso minimiza o tempo necessário para criar o instantâneo de compartilhamento e economiza nos custos de armazenamento. As eventuais operações de gravação para o objeto ou de atualização de metadados ou de propriedade são contadas como "conteúdo alterado" e serão armazenadas no instantâneo de compartilhamento. 

Para economizar espaço, você pode excluir o instantâneo de compartilhamento cujo período tenha tido uma variação maior.

Embora os instantâneos de compartilhamento sejam salvos incrementalmente, o processo de exclusão do instantâneo de compartilhamento foi projetado para que você precise manter somente o instantâneo mais recente do compartilhamento para restaurá-lo. Quando você exclui um instantâneo de compartilhamento, somente os dados exclusivos desse instantâneo de compartilhamento são removidos. Os instantâneos ativos contêm todas as informações necessárias para procurar e restaurar seus dados (da hora em que o instantâneo de compartilhamento foi tirado) no local original ou em um local alternativo. A restauração pode ser feita no nível do item.

Os instantâneos não são contados no limite de compartilhamento de 5 TB. Não há nenhum limite para a quantidade total de espaço ocupado pelo instantâneo de compartilhamento. Os limites de conta de armazenamento ainda se aplicam.

## <a name="limits"></a>limites

O número máximo de instantâneos de compartilhamento que os Arquivos do Azure permitem atualmente é 200. Depois de 200 instantâneos de compartilhamento, os mais antigos precisarão ser excluídos para criar novos instantâneos de compartilhamento. Não há nenhum limite de chamadas simultâneas para criar o instantâneo de compartilhamento.
Não há nenhum limite de quantidade de espaço que os instantâneos de compartilhamento de determinado compartilhamento de arquivos pode consumir. 

## <a name="copy-data-back-to-a-share-from-share-snapshot"></a>Copiar dados para um compartilhamento de um instantâneo de compartilhamento

As operações de cópia que envolvem arquivos e instantâneos de compartilhamento seguem estas regras:

Você pode copiar arquivos individuais de um instantâneo de compartilhamento de arquivos em seu compartilhamento base ou em outro local. Você pode restaurar uma versão anterior de um arquivo ou restaurar o compartilhamento de arquivos completo copiando arquivo por arquivo do instantâneo de compartilhamento. O instantâneo de compartilhamento não será promovido a compartilhamento base. O instantâneo de compartilhamento permanece intacto após a cópia, mas o compartilhamento de arquivos base é substituído por uma cópia dos dados que estavam disponíveis no instantâneo de compartilhamento. Todos os arquivos restaurados contam como “conteúdo alterado”.

Você pode copiar um arquivo em um instantâneo de compartilhamento para um destino com um nome diferente. O arquivo de destino resultante é um arquivo gravável que não é um instantâneo de compartilhamento.

Quando um arquivo de destino é substituído por uma cópia, todos os instantâneos de compartilhamento associados ao arquivo de destino original permanecem intactos.

## <a name="general-best-practices"></a>Melhores práticas gerais 

Ao executar infraestrutura do Azure, automatize os backups para recuperação de dados sempre que possível. As ações automáticas são mais confiáveis do que os processos manuais, ajudando a melhorar a capacidade de recuperação e proteção dos dados. Você pode usar a API REST, o SDK do cliente ou scripts para automação.

Leve em conta cuidadosamente as configurações de retenção e a frequência dos instantâneos de compartilhamento antes de implantar o agendador de instantâneos de compartilhamento para evitar incorrer em encargos de instantâneos de compartilhamento desnecessariamente.

Os instantâneos de compartilhamento fornecem apenas proteção no nível do arquivo e nenhuma exclusão acidental na conta de armazenamento ou no compartilhamento de arquivos está protegida pelos instantâneos de compartilhamento. Você pode bloquear a conta de armazenamento ou o grupo de recursos para proteger a conta de armazenamento de exclusões acidentais.

## <a name="next-steps"></a>Próximas etapas
* [Trabalhar com instantâneos de compartilhamento dos Arquivos do Azure](storage-how-to-use-files-snapshots.md)
* [Perguntas frequentes sobre instantâneo de compartilhamento](storage-files-faq.md)

