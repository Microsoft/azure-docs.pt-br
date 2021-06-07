---
title: Como usar os hubs de notificação do Azure com Java
description: Aprenda a usar Hubs de notificação do Azure de um back-end do Java.
services: notification-hubs
documentationcenter: ''
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 4c3f966d-0158-4a48-b949-9fa3666cb7e4
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: java
ms.devlang: java
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.custom: devx-track-java
ms.openlocfilehash: 09553f587916e8204541b36f259a450d72c1b270
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "87322934"
---
# <a name="how-to-use-notification-hubs-from-java"></a>Como usar os Hubs de notificação do Java

[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

Este tópico descreve os principais recursos do totalmente novo do Java SDK do Hub de notificação do Azure com suporte oficial.
Este é um projeto de software livre e você pode exibir todo o código do SDK no [SDK do Java].

Normalmente, você pode acessar todos os recursos dos Hubs de Notificação por meio de um back-end de Java/PHP/Ruby usando a interface REST do Hub de Notificação, conforme descrito no tópico do MSDN [APIs REST dos Hubs de Notificação](/previous-versions/azure/reference/dn223264(v=azure.100)). Esse SDK Java fornece um wrapper estreito em relação a essas interfaces REST em Java.

O SDK atualmente oferece suporte para:

* CRUD em Hubs de notificação
* CRUD nos registros
* Gerenciamento de instalação
* Os registros de importação/exportação
* Envio regular
* Envio agendado
* Operações assíncronas via NIO Java
* Plataformas com suporte: APNS (iOS), FCM (Android), WNS (aplicativos da Windows Store), MPNS (Windows Phone), ADM (Amazon Kindle Fire), Baidu (Android sem Google Services)

## <a name="sdk-usage"></a>Uso do SDK

### <a name="compile-and-build"></a>Compilar e criar

Usar [Maven]

Para construir:

```cmd
mvn package
```

## <a name="code"></a>Código

### <a name="notification-hub-cruds"></a>CRUDs do Hub de notificação

**Crie um Gerenciador de namespace:**

```java
NamespaceManager namespaceManager = new NamespaceManager("connection string")
```

**Crie o Hub de Notificação:**

```java
NotificationHubDescription hub = new NotificationHubDescription("hubname");
hub.setWindowsCredential(new WindowsCredential("sid","key"));
hub = namespaceManager.createNotificationHub(hub);
```

 OU

```java
hub = new NotificationHub("connection string", "hubname");
```

**Obter Hub de notificação:**

```java
hub = namespaceManager.getNotificationHub("hubname");
```

**Atualizar Hub de notificação:**

```java
hub.setMpnsCredential(new MpnsCredential("mpnscert", "mpnskey"));
hub = namespaceManager.updateNotificationHub(hub);
```

**Excluir Hub de notificação:**

```java
namespaceManager.deleteNotificationHub("hubname");
```

### <a name="registration-cruds"></a>CRUDs de registro

**Crie um cliente do Hub de notificação:**

```java
hub = new NotificationHub("connection string", "hubname");
```

**Crie o registro do Windows:**

```java
WindowsRegistration reg = new WindowsRegistration(new URI(CHANNELURI));
reg.getTags().add("myTag");
reg.getTags().add("myOtherTag");
hub.createRegistration(reg);
```

**Crie registro do iOS:**

```java
AppleRegistration reg = new AppleRegistration(DEVICETOKEN);
reg.getTags().add("myTag");
reg.getTags().add("myOtherTag");
hub.createRegistration(reg);
```

Da mesma forma, você pode criar registros para Android (FCM), Windows Phone (MPNS) e Kindle Fire (ADM).

**Crie registros de modelo:**

```java
WindowsTemplateRegistration reg = new WindowsTemplateRegistration(new URI(CHANNELURI), WNSBODYTEMPLATE);
reg.getHeaders().put("X-WNS-Type", "wns/toast");
hub.createRegistration(reg);
```

**Crie registros usando o padrão criar ID de registro + upsert**:

Remova duplicatas devido a todas as respostas perdidas se armazenar ids de registro no dispositivo:

```java
String id = hub.createRegistrationId();
WindowsRegistration reg = new WindowsRegistration(id, new URI(CHANNELURI));
hub.upsertRegistration(reg);
```

**Atualizar registros:**

```java
hub.updateRegistration(reg);
```

**Exclua os registros:**

```java
hub.deleteRegistration(regid);
```

**Registros de consulta:**

* **Obter um registro único:**

```java
hub.getRegistration(regid);
```

* **Obter todos os registros no hub:**

```java
hub.getRegistrations();
```

* **Obter registros com marca:**

```java
hub.getRegistrationsByTag("myTag");
```

* **Obter registros por canal:**

```java
hub.getRegistrationsByChannel("devicetoken");
```

Todas as consultas de coleção suportam tokens $top e de continuação.

### <a name="installation-api-usage"></a>Uso da API de instalação

A API de instalação é um mecanismo alternativo para gerenciamento de registro. Em vez de manter vários registros, o que não é simples e pode facilmente ser feito da maneira incorreta ou de modo ineficiente, agora é possível usar um objeto de instalação ÚNICA.

A instalação contém tudo o que você precisa: canal push (token de dispositivo), marcas, modelos, blocos secundários (para WNS e APNS). Você não precisa mais chamar o serviço para obter a ID, basta gerar GUID ou qualquer outro identificador, mantê-lo no dispositivo e enviar para o back-end com o canal push (token de dispositivo).

No back-end, você deve fazer somente uma única chamada para `CreateOrUpdateInstallation`; ele é totalmente idempotente, então fique à vontade para tentar novamente se necessário.

Como exemplo, para o Amazon Kindle Fire:

```java
Installation installation = new Installation("installation-id", NotificationPlatform.Adm, "adm-push-channel");
hub.createOrUpdateInstallation(installation);
```

Se você quiser atualizá-lo:

```java
installation.addTag("foo");
installation.addTemplate("template1", new InstallationTemplate("{\"data\":{\"key1\":\"$(value1)\"}}","tag-for-template1"));
installation.addTemplate("template2", new InstallationTemplate("{\"data\":{\"key2\":\"$(value2)\"}}","tag-for-template2"));
hub.createOrUpdateInstallation(installation);
```

Para cenários avançados, utilize a capacidade de atualização parcial, que lhe permite modificar apenas determinadas propriedades do objeto de instalação. A atualização parcial é o subconjunto de operações de Patch JSON, que você pode executar no objeto de instalação.

```java
PartialUpdateOperation addChannel = new PartialUpdateOperation(UpdateOperationType.Add, "/pushChannel", "adm-push-channel2");
PartialUpdateOperation addTag = new PartialUpdateOperation(UpdateOperationType.Add, "/tags", "bar");
PartialUpdateOperation replaceTemplate = new PartialUpdateOperation(UpdateOperationType.Replace, "/templates/template1", new InstallationTemplate("{\"data\":{\"key3\":\"$(value3)\"}}","tag-for-template1")).toJson());
hub.patchInstallation("installation-id", addChannel, addTag, replaceTemplate);
```

Excluir a instalação:

```java
hub.deleteInstallation(installation.getInstallationId());
```

`CreateOrUpdate`, `Patch` e `Delete` acabam sendo consistentes com `Get`. A operação solicitada só vai para a fila do sistema durante a chamada e é executada em segundo plano. Get não foi projetado para o cenário principal do runtime, mas apenas para depuração e solução de problemas, ele é totalmente restrito pelo serviço.

Fluxo de envio para instalações é o mesmo para os registros. Para direcionar a notificação para uma instalação particular – basta usar a marca “InstallationId:{desired-id}". Nesse caso, o código é:

```java
Notification n = Notification.createWindowsNotification("WNS body");
hub.sendNotification(n, "InstallationId:{installation-id}");
```

Para um dos vários modelos:

```java
Map<String, String> prop =  new HashMap<String, String>();
prop.put("value3", "some value");
Notification n = Notification.createTemplateNotification(prop);
hub.sendNotification(n, "InstallationId:{installation-id} && tag-for-template1");
```

### <a name="schedule-notifications-available-for-standard-tier"></a>Agendar notificações (disponível para a camada PADRÃO)

O mesmo que o envio regular, mas com um parâmetro adicional - scheduledTime que informa quando a notificação deve ser entregue. O serviço aceita qualquer ponto de tempo entre agora + 5 minutos e agora + 7 dias.

**Agendar uma notificação nativa do Windows:**

```java
Calendar c = Calendar.getInstance();
c.add(Calendar.DATE, 1);
Notification n = Notification.createWindowsNotification("WNS body");
hub.scheduleNotification(n, c.getTime());
```

### <a name="importexport-available-for-standard-tier"></a>Importação/exportação (disponível para a camada PADRÃO)

Você talvez precise executar a operação em massa com relação a registros. Geralmente é para integração com outro sistema ou uma correção maciça para atualizar as marcas. Não recomendamos usar o fluxo Get/Update se milhares de registros estiverem envolvidos. A funcionalidade de Importação/Exportação do sistema foi projetada para cobrir o cenário. Você dará acesso a um contêiner de blob em sua conta de armazenamento como uma fonte de dados de entrada e local de saída.

**Envie um trabalho de exportação:**

```java
NotificationHubJob job = new NotificationHubJob();
job.setJobType(NotificationHubJobType.ExportRegistrations);
job.setOutputContainerUri("container uri with SAS signature");
job = hub.submitNotificationHubJob(job);
```

**Envie um trabalho de importação:**

```java
NotificationHubJob job = new NotificationHubJob();
job.setJobType(NotificationHubJobType.ImportCreateRegistrations);
job.setImportFileUri("input file uri with SAS signature");
job.setOutputContainerUri("container uri with SAS signature");
job = hub.submitNotificationHubJob(job);
```

**Aguarde até um trabalho estar concluído:**

```java
while(true){
    Thread.sleep(1000);
    job = hub.getNotificationHubJob(job.getJobId());
    if(job.getJobStatus() == NotificationHubJobStatus.Completed)
        break;
}
```

**Obter todos os trabalhos:**

```java
List<NotificationHubJob> jobs = hub.getAllNotificationHubJobs();
```

**URI com assinatura SAS:**

 Essa URL é a URL de um arquivo de blob ou contêiner de blobs mais um conjunto de parâmetros, como permissões e tempo de término, mais a assinatura de todos esses itens feita usando a chave SAS da conta. O SDK Java do Armazenamento do Azure tem recursos avançados, incluindo a criação desses URIs. Como alternativa simples, dê uma olhada na classe de teste `ImportExportE2E` (da localização do GitHub) que tem implementações básicas e compactas do algoritmo de assinatura.

### <a name="send-notifications"></a>Enviar notificações

O objeto de notificação é simplesmente um corpo com cabeçalhos, alguns métodos de utilitário que ajudam na criação de objetos nativos e de modelo de notificações.

* **Windows Store e Windows Phone 8.1 (não Silverlight)**

```java
String toast = "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">Hello from Java!</text></binding></visual></toast>";
Notification n = Notification.createWindowsNotification(toast);
hub.sendNotification(n);
```

* **iOS**

```java
String alert = "{\"aps\":{\"alert\":\"Hello from Java!\"}}";
Notification n = Notification.createAppleNotification(alert);
hub.sendNotification(n);
```

* **Android**

```java
String message = "{\"data\":{\"msg\":\"Hello from Java!\"}}";
Notification n = Notification.createFcmNotification(message);
hub.sendNotification(n);
```

* **Silverlight para Windows Phone 8.0 e 8.1**

```java
String toast = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
            "<wp:Notification xmlns:wp=\"WPNotification\">" +
                "<wp:Toast>" +
                    "<wp:Text1>Hello from Java!</wp:Text1>" +
                "</wp:Toast> " +
            "</wp:Notification>";
Notification n = Notification.createMpnsNotification(toast);
hub.sendNotification(n);
```

* **Kindle Fire**

```java
String message = "{\"data\":{\"msg\":\"Hello from Java!\"}}";
Notification n = Notification.createAdmNotification(message);
hub.sendNotification(n);
```

* **Enviar para Marcas**
  
```java
Set<String> tags = new HashSet<String>();
tags.add("boo");
tags.add("foo");
hub.sendNotification(n, tags);
```

* **Enviar a expressão de marca**

```java
hub.sendNotification(n, "foo && ! bar");
```

* **Enviar notificação de modelo**

```java
Map<String, String> prop =  new HashMap<String, String>();
prop.put("prop1", "v1");
prop.put("prop2", "v2");
Notification n = Notification.createTemplateNotification(prop);
hub.sendNotification(n);
```

A execução do código Java agora deve produzir uma notificação que aparece no dispositivo de destino.

## <a name="next-steps"></a><a name="next-steps"></a>Próximas etapas

Este tópico mostrou como criar um cliente REST simples do Java para Hubs de Notificação. A partir daqui, você pode:

* Baixe o [SDK do Java]completo, que contém todo o código do SDK.
* Brincar com os exemplos:
  * [Introdução aos Hubs de Notificação]
  * [Enviar últimas notícias]
  * [Enviar últimas notícias localizadas]
  * [Enviar notificações aos usuários autenticados]
  * [Enviar notificações entre plataformas aos usuários autenticados]

[Java SDK]: https://github.com/Azure/azure-notificationhubs-java-backend
[Get started tutorial]: ./notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Introdução aos Hubs de Notificação]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Enviar últimas notícias]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[Enviar últimas notícias localizadas]: notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md
[Enviar notificações aos usuários autenticados]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Enviar notificações entre plataformas aos usuários autenticados]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Maven]: https://maven.apache.org/
