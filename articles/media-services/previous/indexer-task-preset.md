---
title: Predefinição de tarefa para o Azure Media Indexer
description: Este tópico fornece uma visão geral da predefinição de tarefa para o indexador de mídia dos serviços de mídia do Azure.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: c054c0aa8c58894f63f8ce8432e8d7a9e1275639
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/11/2021
ms.locfileid: "103012217"
---
# <a name="task-preset-for-azure-media-indexer"></a>Predefinição de tarefa para o Azure Media Indexer

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Azure Media Indexer é um Processador de Mídia que você usa para executar as seguintes tarefas: tornar arquivos de mídia e conteúdo pesquisáveis, gerar palavras-chave e faixas de legenda oculta, indexar arquivos de ativo que fazem parte do seu ativo.

Este tópico descreve as tarefas predefinidas que você precisa passar para seu trabalho de indexação. Para um exemplo completo, consulte [Indexar arquivos de mídia com o Azure Media Indexer](media-services-index-content.md).

## <a name="azure-media-indexer-configuration-xml"></a>XML de configuração do Azure Media Indexer

A tabela a seguir explica os elementos e atributos do XML de configuração.

|Nome|Exigir|Descrição|
|---|---|---|
|Entrada|true|Arquivos do ativo que você deseja indexar.<br/>O Azure Media Indexer dá suporte aos seguintes formatos de arquivo de mídia: MP4, MOV, WMV, MP3, M4A, WMA, AAC, WAV. <br/><br/>Você pode especificar os nomes de arquivo no atributo **name** ou **list** do elemento **input** (conforme mostrado abaixo). Se você não especificar qual arquivo de ativo indexar, o arquivo primário será escolhido. Se nenhum arquivo de ativo primário for definido, o primeiro arquivo no ativo de entrada será indexado.<br/><br/>Para especificar explicitamente o nome de arquivo do ativo, faça isto:<br/>```<input name="TestFile.wmv" />```<br/><br/>Você também pode indexar vários arquivos de ativo ao mesmo tempo (até 10 arquivos). Para fazer isso:<br/>– Crie um arquivo de texto (arquivo de manifesto) e dê a ele uma extensão .lst.<br/>– Adicione uma lista de todos os nomes de arquivo de ativo em seu ativo de entrada para esse arquivo de manifesto.<br/>– Adicione (carregue) o arquivo de manifesto ao ativo.<br/>– Especifique o nome do arquivo de manifesto no atributo list da entrada.<br/>```<input list="input.lst">```<br/><br/>**Observação:** Se você adicionar mais de 10 arquivos ao arquivo de manifesto, o trabalho de indexação falhará com o código de erro 2006.|
|metadata|false|Metadados para os arquivos de ativo especificados.<br/>```<metadata key="..." value="..." />```<br/><br/>Você pode fornecer valores para chaves predefinidas. <br/><br/>No momento, há suporte para as seguintes chaves:<br/><br/>**título** e **descrição** – usados para atualizar o modelo de idioma para aumentar a precisão do reconhecimento de fala.<br/>```<metadata key="title" value="[Title of the media file]" /><metadata key="description" value="[Description of the media file]" />```<br/><br/>**nome de usuário** e **senha** – usados para autenticação ao baixar arquivos da Internet via http ou https.<br/>```<metadata key="username" value="[UserName]" /><metadata key="password" value="[Password]" />```<br/>Os valores de nome de usuário e senha aplicam-se a todas as URLs de mídia no manifesto de entrada.|
|recursos<br/><br/> Adicionado na versão 1.2. Atualmente, o único recurso com suporte é o reconhecimento de fala (“ASR”).|false|O recurso de Reconhecimento de Fala tem as seguintes chaves de configurações:<br/><br/>Linguagem:<br/>– O idioma natural a ser reconhecido no arquivo multimídia.<br/>– Inglês, espanhol<br/><br/>CaptionFormats:<br/>– uma lista separada por pontos e vírgulas dos formatos de legenda de saída desejados (se houver)<br/>-ttml; webvtt<br/><br/><br/>GenerateKeywords:<br/>– Um sinalizador booliano que especifica se um arquivo XML de palavras-chave é necessário ou não.<br/>- True; False.|

## <a name="azure-media-indexer-configuration-xml-example"></a>Exemplo de XML de configuração do Azure Media Indexer

``` 
<?xml version="1.0" encoding="utf-8"?>  
<configuration version="2.0">  
  <input>  
    <metadata key="title" value="[Title of the media file]" />  
    <metadata key="description" value="[Description of the media file]" />  
  </input>  
  <settings>  
  </settings>  
  
  <features>  
    <feature name="ASR">    
      <settings>  
        <add key="Language" value="English"/>  
        <add key="GenerateKeywords" value ="true" />  
      </settings>  
    </feature>  
  </features>  
  
</configuration>  
```
  
## <a name="next-steps"></a>Próximas etapas

Consulte [Como indexar arquivos de mídia com o Azure Media Indexer](media-services-index-content.md).

