---
title: Log do SDK de fala – serviço de fala
titleSuffix: Azure Cognitive Services
description: Habilite o registro em log no SDK de fala.
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: amishu
ms.openlocfilehash: 31ff21e33860f75d91d01e80e3ee77bd7192f780
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68559479"
---
# <a name="enable-logging-in-the-speech-sdk"></a>Habilitar o registro em log no SDK de fala

O registro em log no arquivo é um recurso opcional para o SDK de fala. Durante o log de desenvolvimento fornece informações adicionais e diagnósticos dos principais componentes do SDK de fala. Ele pode ser habilitado definindo a propriedade `Speech_LogFilename` em um objeto de configuração de fala como o local e o nome do arquivo de log. O registro em log será ativado globalmente quando um reconhecedor for criado a partir dessa configuração e não poderá ser desabilitado posteriormente. Não é possível alterar o nome de um arquivo de log durante uma sessão de log em execução.

> [!NOTE]
> O registro em log está disponível desde o Speech SDK versão 1.4.0 em todas as linguagens de programação SDK de fala com suporte, com exceção do JavaScript.

## <a name="sample"></a>Amostra

O nome do arquivo de log é especificado em um objeto de configuração. Considerando o `SpeechConfig` como um exemplo e supondo que você criou uma instância chamada `config`:

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

Você pode criar um reconhecedor a partir do objeto de configuração. Isso habilitará o registro em log para todos os reconhecedores.

> [!NOTE]
> Se você criar um `SpeechSynthesizer` do objeto de configuração, ele não habilitará o registro em log. No entanto, se o `SpeechSynthesizer`registro em log estiver habilitado, você também receberá diagnósticos do.

## <a name="create-a-log-file-on-different-platforms"></a>Criar um arquivo de log em diferentes plataformas

Para Windows ou Linux, o arquivo de log pode estar em qualquer caminho para o qual o usuário tenha permissão de gravação. As permissões de gravação para locais do sistema de arquivos em outros sistemas operacionais podem ser limitadas ou restritas por padrão.

### <a name="universal-windows-platform-uwp"></a>UWP (Plataforma Universal do Windows)

Os aplicativos UWP precisam ser colocados nos arquivos de log em um dos locais de dados do aplicativo (local, móvel ou temporário). Um arquivo de log pode ser criado na pasta do aplicativo local:

```csharp
StorageFolder storageFolder = ApplicationData.Current.LocalFolder;
StorageFile logFile = await storageFolder.CreateFileAsync("logfile.txt", CreationCollisionOption.ReplaceExisting);
config.SetProperty(PropertyId.Speech_LogFilename, logFile.Path);
```

Mais informações sobre a permissão de acesso a arquivos para aplicativos UWP estão disponíveis [aqui](https://docs.microsoft.com/windows/uwp/files/file-access-permissions).

### <a name="android"></a>Android

Você pode salvar um arquivo de log em um armazenamento interno, armazenamento externo ou diretório de cache. Os arquivos criados no armazenamento interno ou no diretório de cache são privados para o aplicativo. É preferível criar um arquivo de log no armazenamento externo.

```java
File dir = context.getExternalFilesDir(null);
File logFile = new File(dir, "logfile.txt");
config.setProperty(PropertyId.Speech_LogFilename, logFile.getAbsolutePath());
```

O código acima salvará um arquivo de log no armazenamento externo na raiz de um diretório específico do aplicativo. Um usuário pode acessar o arquivo com o Gerenciador de arquivos (geralmente `Android/data/ApplicationName/logfile.txt`em). O arquivo será excluído quando o aplicativo for desinstalado.

Você também precisa solicitar `WRITE_EXTERNAL_STORAGE` permissão no arquivo de manifesto:

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android" package="...">
  ...
  <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
  ...
</manifest>
```

Mais informações sobre armazenamento de dados e de arquivos para aplicativos Android estão disponíveis [aqui](https://developer.android.com/guide/topics/data/data-storage.html).

#### <a name="ios"></a>iOS

Somente os diretórios dentro da área restrita do aplicativo estão acessíveis. Os arquivos podem ser criados nos documentos, na biblioteca e nos diretórios temporários. Os arquivos no diretório documentos podem ser disponibilizados para um usuário. O trecho de código a seguir mostra a criação de um arquivo de log no diretório de documentos do aplicativo:

```objc
NSString *filePath = [
  [NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES) firstObject]
    stringByAppendingPathComponent:@"logfile.txt"];
[speechConfig setPropertyTo:filePath byId:SPXSpeechLogFilename];
```

Para acessar um arquivo criado, adicione as propriedades abaixo à `Info.plist` lista de propriedades do aplicativo:

```xml
<key>UIFileSharingEnabled</key>
<true/>
<key>LSSupportsOpeningDocumentsInPlace</key>
<true/>
```

Mais sobre o sistema de arquivos do iOS está disponível [aqui](https://developer.apple.com/library/archive/documentation/FileManagement/Conceptual/FileSystemProgrammingGuide/FileSystemOverview/FileSystemOverview.html).

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Explorar nossos exemplos no GitHub](https://aka.ms/csspeech/samples)
