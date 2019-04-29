---
title: Logs do SDK de fala - serviços de fala
titleSuffix: Azure Cognitive Services
description: Habilite o log no SDK da fala.
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: amishu
ms.openlocfilehash: 75eaea22c4809eda78e54514961d13113b4a5f3a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60696844"
---
# <a name="enable-logging-in-the-speech-sdk"></a>Habilitar o registro no SDK de fala

Log de arquivo é um recurso opcional para o SDK de fala. Durante o desenvolvimento de registro em log fornece informações adicionais e diagnósticos de componentes de núcleo de Speeck SDK. Ele pode ser habilitado configurando a propriedade `Speech_LogFilename` em um objeto de configuração de fala para o local e o nome do arquivo de log. Registro em log será ativado globalmente depois que um reconhecedor é criado a partir dessa configuração e não pode ser desabilitado posteriormente. Você não pode alterar o nome de um arquivo de log durante um sessão de log de execução.

> [!NOTE]
> Registro em log está disponível em todas as linguagens de programação, com exceção de JavaScript de SDK com suporte do fala.

## <a name="sample"></a>Amostra

O nome do arquivo de log é especificado em um objeto de configuração. Levando a `SpeechConfig` como exemplo e supondo que você tenha criado uma instância chamada `config`:

```csharp
config.SetProperty(PropertyId.Speech_LogFilename, "LogfilePathAndName");
```

```java
config.setProperty(PropertyId.Speech_LogFilename, "LogfilePathAndName");
```

```C++
config->SetProperty(PropertyId::Speech_LogFilename, "LogfilePathAndName");
```

```Python
config.set_property(speechsdk.PropertyId.Speech_LogFilename, "LogfilePathAndName")
```

```objc
[config setPropertyTo:@"LogfilePathAndName" byId:SPXSpeechLogFilename];
```

Você pode criar um reconhecedor de objeto de configuração. Isso permitirá que o registro em log para todos os identificadores.

> [!NOTE]
> Se você criar um `SpeechSynthesizer` do objeto de configuração, isso não habilitará o registro em log. Se o log está habilitado no entanto, você também receberá o diagnóstico do `SpeechSynthesizer`.

## <a name="create-a-log-file-on-different-platforms"></a>Criar um arquivo de log em diferentes plataformas

Para Windows ou Linux, o arquivo de log pode ser em qualquer caminho que o usuário tem permissão de gravação. Permissões de gravação para o sistema de arquivos locais em outros sistemas operacionais podem ser limitadas ou restrita por padrão.

### <a name="universal-windows-platform-uwp"></a>UWP (Plataforma Universal do Windows)

Aplicativos UWP precisam ser locais de arquivos de log em um dos locais de dados do aplicativo (locais, móveis ou temporários). Um arquivo de log pode ser criado na pasta local do aplicativo:

```csharp
StorageFolder storageFolder = ApplicationData.Current.LocalFolder;
StorageFile logFile = await storageFolder.CreateFileAsync("logfile.txt", CreationCollisionOption.ReplaceExisting);
config.SetProperty(PropertyId.Speech_LogFilename, logFile.Path);
```

Mais sobre o acesso de arquivo permissão para aplicativos UWP está disponível [aqui](https://docs.microsoft.com/windows/uwp/files/file-access-permissions).

### <a name="android"></a>Android

Você pode salvar um arquivo de log para armazenamento interno, armazenamento externo ou o diretório de cache. Arquivos criados no armazenamento interno ou o diretório de cache são particulares ao aplicativo. É preferível para criar um arquivo de log no armazenamento externo.

```java
File dir = context.getExternalFilesDir(null);
File logFile = new File(dir, "logfile.txt");
config.setProperty(PropertyId.Speech_LogFilename, logFile.getAbsolutePath());
```

O código acima salvará um arquivo de log para o armazenamento externo na raiz de um diretório específico do aplicativo. Um usuário pode acessar o arquivo com o Gerenciador de arquivos (normalmente em `Android/data/ApplicationName/logfile.txt`). O arquivo será excluído quando o aplicativo é desinstalado.

Você também precisará solicitar `WRITE_EXTERNAL_STORAGE` permissão no arquivo de manifesto:

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android" package="...">
  ...
  <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
  ...
</manifest>
```

Mais sobre os dados e o arquivo de armazenamento para aplicativos Android está disponível [aqui](https://developer.android.com/guide/topics/data/data-storage.html).

#### <a name="ios"></a>iOS

Somente os diretórios dentro de proteção do aplicativo são acessíveis. Arquivos podem ser criados no diretórios temporários, biblioteca e documentos. Arquivos no diretório de documentos podem ser disponibilizados para um usuário. O trecho de código a seguir mostra a criação de um arquivo de log no diretório do documento do aplicativo:

```objc
NSString *filePath = [
  [NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES) firstObject]
    stringByAppendingPathComponent:@"logfile.txt"];
[speechConfig setPropertyTo:filePath byId:SPXSpeechLogFilename];
```

Para acessar um arquivo criado, adicione a abaixo propriedades para o `Info.plist` lista de propriedades do aplicativo:

```xml
<key>UIFileSharingEnabled</key>
<true/>
<key>LSSupportsOpeningDocumentsInPlace</key>
<true/>
```

Mais sobre iOS sistema de arquivos está disponível [aqui](https://developer.apple.com/library/archive/documentation/FileManagement/Conceptual/FileSystemProgrammingGuide/FileSystemOverview/FileSystemOverview.html).

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Explorar nossos exemplos no GitHub](https://aka.ms/csspeech/samples)

