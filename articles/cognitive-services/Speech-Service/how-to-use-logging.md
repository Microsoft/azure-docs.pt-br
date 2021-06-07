---
title: Log do SDK de fala – Serviço de fala
titleSuffix: Azure Cognitive Services
description: Aprenda a habilitar o registro em log no SDK de fala (C++, C#, Python, Objective-C, Java).
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: amishu
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 32715ad1a01366d7d56e6fa8129151b15c315e1d
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106504168"
---
# <a name="enable-logging-in-the-speech-sdk"></a>Habilitar o registro em log no SDK de fala

O registro em log em arquivo é um recurso opcional do SDK de fala. Durante o desenvolvimento, o registro em log traz mais informações e diagnósticos sobre os principais componentes do SDK de fala. Para habilitá-lo, defina a propriedade `Speech_LogFilename` em um objeto de configuração de fala como a localização e o nome do arquivo de log. O registro em log é realizado por uma classe estática na biblioteca nativa do SDK de fala. Você pode habilitar o registro em log para qualquer instância do reconhecedor ou sintetizador do SDK de fala. Todas as instâncias do mesmo processo gravam entradas de log no mesmo arquivo de log.

> [!NOTE]
> O registro em log está disponível desde a versão 1.4.0 do SDK de fala em todas as linguagens de programação com suporte, exceto JavaScript.

## <a name="sample"></a>Amostra

O nome do arquivo de log é especificado em um objeto de configuração. Considere o `SpeechConfig` como exemplo e suponha que você criou uma instância chamada `config`:

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

Você pode criar um reconhecedor usando o objeto de configuração. Isso habilitará o registro em log para todos os reconhecedores.

> [!NOTE]
> Se você criar um `SpeechSynthesizer` usando o objeto de configuração, o registro em log não será habilitado. No entanto, se o registro em log estiver habilitado, você também receberá diagnósticos do `SpeechSynthesizer`.

## <a name="create-a-log-file-on-different-platforms"></a>Criar arquivos de log em diferentes plataformas

No Windows ou Linux, o arquivo de log pode estar em qualquer caminho no qual o usuário tenha permissão de gravação. Em outros sistemas operacionais, talvez haja limites ou restrições padrão às permissões de gravação em locais do sistema de arquivos.

### <a name="universal-windows-platform-uwp"></a>Plataforma Universal do Windows (UWP)

Os aplicativos UWP precisam colocar os arquivos de log em um dos locais de dados do aplicativo (local, móvel ou temporário). É possível criar um arquivo de log na pasta local do aplicativo:

```csharp
StorageFolder storageFolder = ApplicationData.Current.LocalFolder;
StorageFile logFile = await storageFolder.CreateFileAsync("logfile.txt", CreationCollisionOption.ReplaceExisting);
config.SetProperty(PropertyId.Speech_LogFilename, logFile.Path);
```

Veja mais informações sobre a permissão de acesso a arquivos dos aplicativos UWP [aqui](/windows/uwp/files/file-access-permissions).

### <a name="android"></a>Android

Você pode salvar um arquivo de log no armazenamento interno, armazenamento externo ou diretório de cache. Os arquivos criados no armazenamento interno ou no diretório de cache são privados para o aplicativo. Recomendamos criar um arquivo de log no armazenamento externo.

```java
File dir = context.getExternalFilesDir(null);
File logFile = new File(dir, "logfile.txt");
config.setProperty(PropertyId.Speech_LogFilename, logFile.getAbsolutePath());
```

O código acima salva um arquivo de log no armazenamento externo, na raiz de um diretório específico do aplicativo. O usuário pode acessar o arquivo com o gerenciador de arquivos (geralmente em `Android/data/ApplicationName/logfile.txt`). O arquivo é excluído quando o aplicativo é desinstalado.

Você também precisa solicitar a permissão `WRITE_EXTERNAL_STORAGE` no arquivo de manifesto:

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android" package="...">
  ...
  <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
  ...
</manifest>
```

Veja mais informações sobre o armazenamento de dados e arquivos de aplicativos Android [aqui](https://developer.android.com/guide/topics/data/data-storage.html).

#### <a name="ios"></a>iOS

Somente os diretórios dentro da área restrita do aplicativo são acessíveis. É possível criar arquivos nos diretórios de documentos, da biblioteca ou temporário. Os arquivos no diretório de documentos podem ser disponibilizados ao usuário. O snippet de código a seguir mostra a criação de um arquivo de log no diretório de documentos do aplicativo:

```objc
NSString *filePath = [
  [NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES) firstObject]
    stringByAppendingPathComponent:@"logfile.txt"];
[speechConfig setPropertyTo:filePath byId:SPXSpeechLogFilename];
```

Para acessar o arquivo criado, adicione as propriedades abaixo à lista de propriedades `Info.plist` do aplicativo:

```xml
<key>UIFileSharingEnabled</key>
<true/>
<key>LSSupportsOpeningDocumentsInPlace</key>
<true/>
```

Veja mais informações sobre o sistema de arquivos do iOS [aqui](https://developer.apple.com/library/archive/documentation/FileManagement/Conceptual/FileSystemProgrammingGuide/FileSystemOverview/FileSystemOverview.html).

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Explorar nossos exemplos no GitHub](https://aka.ms/csspeech/samples)