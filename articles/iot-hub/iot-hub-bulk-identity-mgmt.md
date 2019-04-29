---
title: Importar e exportar identidades de dispositivo do Hub IoT do Azure | Microsoft Docs
description: Como usar o SDK do serviço IoT do Azure para executar operações em massa no Registro de identidade para importar e exportar identidades de dispositivo. As operações de importação permitem criar, atualizar e excluir as identidades de dispositivo em massa.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/03/2017
ms.author: robinsh
ms.openlocfilehash: 274b77644326cbf73696aae77b48afcbc63aa4c2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61322625"
---
# <a name="import-and-export-iot-hub-device-identities-in-bulk"></a>Importar e exportar identidades de dispositivo do IoT Hub em massa

Cada hub IoT tem um registro de identidade que você pode usar para criar recursos por dispositivo no serviço. O registro de identidade também permite que você controle o acesso aos pontos de extremidade voltados para o dispositivo. Este artigo descreve como importar e exportar identidades de dispositivo em massa bidirecionalmente em um registro de identidade.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

As operações de importação e exportação ocorrem no contexto de *Trabalhos* , que permitem aos usuários executar operações de serviço em massa em um Hub IoT.

A classe **RegistryManager** inclui os métodos **ExportDevicesAsync** e **ImportDevicesAsync** que usam a estrutura **Job**. Esses métodos permitem exportar, importar e sincronizar todo o registro de identidade de um Hub IoT.

Este tópico discute o uso da classe **RegistryManager** e do sistema de **trabalho** para executar importações e exportações em massa de dispositivos para e do registro de identidade de um hub IoT. Você também pode usar o Serviço de Provisionamento de Dispositivos no Hub IoT do Azure para habilitar o provisionamento sem toque e Just-In-Time para um ou mais hubs IoT sem a necessidade de intervenção humana. Para obter mais informações, consulte a [documentação do serviço de provisionamento](/azure/iot-dps).


## <a name="what-are-jobs"></a>O que são trabalhos?

As operações de registro de identidade usam o sistema de **Trabalho** quando a operação:

* Tem um tempo de execução potencialmente longo em comparação com as operações de tempo de execução padrão.

* Retorna uma grande quantidade de dados para o usuário.

Em vez de ter uma única chamada à API aguardando ou bloqueando o resultado da operação, a operação cria de modo assíncrono um **Trabalho** para o Hub IoT. Então, a operação retorna imediatamente um objeto **JobProperties**.

O snippet de código de C# a seguir mostra como criar um trabalho de exportação:

```csharp
// Call an export job on the IoT Hub to retrieve all devices
JobProperties exportJob = await 
  registryManager.ExportDevicesAsync(containerSasUri, false);
```

> [!NOTE]
> Para usar a classe **RegistryManager** no código C#, adicione o pacote NuGet **Microsoft.Azure.Devices** ao projeto. A classe **RegistryManager** está no namespace **Microsoft.Azure.Devices**.

Você pode usar a classe **RegistryManager** para consultar o estado do **Job** usando os metadados de **JobProperties** retornados. Para criar uma instância da classe **RegistryManager**, use o método **CreateFromConnectionString**.

```csharp
RegistryManager registryManager =
  RegistryManager.CreateFromConnectionString("{your IoT Hub connection string}");
```

Para encontrar a cadeia de conexão do para seu Hub IoT no Portal do Azure:

- Navegue até o seu Hub IoT.

- Selecione **Políticas de acesso compartilhado**.

- Selecione uma política, levando em conta as permissões necessárias.

- Copie a cadeia de conexão do painel no lado direito da tela.

O snippet de código de C# a seguir mostra como verificar a cada cinco segundos para ver se o trabalho finalizou a execução:

```csharp
// Wait until job is finished
while(true)
{
  exportJob = await registryManager.GetJobAsync(exportJob.JobId);
  if (exportJob.Status == JobStatus.Completed || 
      exportJob.Status == JobStatus.Failed ||
      exportJob.Status == JobStatus.Cancelled)
  {
    // Job has finished executing
    break;
  }

  await Task.Delay(TimeSpan.FromSeconds(5));
}
```

## <a name="export-devices"></a>Exportar dispositivos

Use o método **ExportDevicesAsync** para exportar todo o registro de identidade de um Hub IoT para um contêiner de blobs do [Armazenamento do Azure](../storage/index.yml) usando uma [Assinatura de Acesso Compartilhado](../storage/common/storage-security-guide.md#data-plane-security).

Esse método permite criar backups confiáveis das informações do dispositivo em um contêiner de blobs controlado por você.

O método **ExportDevicesAsync** exige dois parâmetros:

* Uma *cadeia de caracteres* que contenha um URI de um contêiner de blobs. Esse URI deve conter um token SAS que conceda acesso de gravação ao contêiner. O trabalho cria um blob de blocos nesse contêiner para armazenar os dados do dispositivo de exportação serializados. O token SAS deve incluir estas permissões:

   ```csharp
   SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Read 
     | SharedAccessBlobPermissions.Delete
   ```

* Um *booliano* que indica se você deseja excluir chaves de autenticação dos dados de exportação. Se for **false**, as chaves de autenticação serão incluídas na saída de exportação. Caso contrário, as chaves serão exportadas como **null**.

O seguinte snippet de código em C# mostra como iniciar um trabalho de exportação que inclui chaves de autenticação de dispositivo nos dados de exportação e, em seguida, pesquisar se houve a conclusão:

```csharp
// Call an export job on the IoT Hub to retrieve all devices
JobProperties exportJob = 
  await registryManager.ExportDevicesAsync(containerSasUri, false);

// Wait until job is finished
while(true)
{
    exportJob = await registryManager.GetJobAsync(exportJob.JobId);
    if (exportJob.Status == JobStatus.Completed || 
        exportJob.Status == JobStatus.Failed ||
        exportJob.Status == JobStatus.Cancelled)
    {
    // Job has finished executing
    break;
    }

    await Task.Delay(TimeSpan.FromSeconds(5));
}
```

O trabalho armazena sua saída no contêiner de blobs fornecido como um blob de blocos com o nome **devices.txt**. Os dados da saída consistem em dados de dispositivo serializados JSON, com um dispositivo por linha.

O exemplo a seguir mostra os dados de saída:

```json
{"id":"Device1","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device2","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device3","eTag":"MA==","status":"disabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device4","eTag":"MA==","status":"disabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device5","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
```

Se um dispositivo tiver dados gêmeos, os dados gêmeos também serão exportados junto com os dados do dispositivo. O exemplo a seguir mostra esse formato. Todos os dados da linha "twinETag" até o final são dados gêmeos.

```json
{
   "id":"export-6d84f075-0",
   "eTag":"MQ==",
   "status":"enabled",
   "statusReason":"firstUpdate",
   "authentication":null,
   "twinETag":"AAAAAAAAAAI=",
   "tags":{
      "Location":"LivingRoom"
   },
   "properties":{
      "desired":{
         "Thermostat":{
            "Temperature":75.1,
            "Unit":"F"
         },
         "$metadata":{
            "$lastUpdated":"2017-03-09T18:30:52.3167248Z",
            "$lastUpdatedVersion":2,
            "Thermostat":{
               "$lastUpdated":"2017-03-09T18:30:52.3167248Z",
               "$lastUpdatedVersion":2,
               "Temperature":{
                  "$lastUpdated":"2017-03-09T18:30:52.3167248Z",
                  "$lastUpdatedVersion":2
               },
               "Unit":{
                  "$lastUpdated":"2017-03-09T18:30:52.3167248Z",
                  "$lastUpdatedVersion":2
               }
            }
         },
         "$version":2
      },
      "reported":{
         "$metadata":{
            "$lastUpdated":"2017-03-09T18:30:51.1309437Z"
         },
         "$version":1
      }
   }
}
```

Se precisar de acesso a esses dados no código, você poderá desserializar facilmente esses dados usando a classe **ExportImportDevice** . O seguinte snippet de código de C# mostra como ler informações do dispositivo que foram anteriormente exportadas para um blob de blocos:

```csharp
var exportedDevices = new List<ExportImportDevice>();

using (var streamReader = new StreamReader(await blob.OpenReadAsync(AccessCondition.GenerateIfExistsCondition(), null, null), Encoding.UTF8))
{
  while (streamReader.Peek() != -1)
  {
    string line = await streamReader.ReadLineAsync();
    var device = JsonConvert.DeserializeObject<ExportImportDevice>(line);
    exportedDevices.Add(device);
  }
}
```

## <a name="import-devices"></a>Importar dispositivos

O método **ImportDevicesAsync** na classe **RegistryManager** permite que você execute operações em massa de importação e sincronização em um registro de identidade do Hub IoT. Assim como ocorre com o método **ExportDevicesAsync**, o método **ImportDevicesAsync** usa a estrutura **Job**.

Tenha cuidado ao usar o método **ImportDevicesAsync** porque, além de provisionar novos dispositivos no registro de identidade, ele também pode atualizar e excluir os dispositivos existentes.

> [!WARNING]
> Uma operação de importação não pode ser desfeita. Sempre faça backup de seus dados existentes usando o método **ExportDevicesAsync** em outro contêiner de blobs antes de fazer alterações em massa no registro de identidade.

O método **ImportDevicesAsync** usa dois parâmetros:

* Uma *cadeia de caracteres* que contém um URI de um contêiner de blobs do [Armazenamento do Azure](../storage/index.yml) como *entrada* para o trabalho. Esse URI deve conter um token SAS que conceda acesso de leitura ao contêiner. Esse contêiner deve conter um blob com o nome **devices.txt** que contém os dados de dispositivo serializados a serem importados no registro de identidade. Os dados de importação devem conter informações do dispositivo no mesmo formato JSON usado pelo trabalho **ExportImportDevice** ao criar um blob **devices.txt**. O token SAS deve incluir estas permissões:

   ```csharp
   SharedAccessBlobPermissions.Read
   ```

* Uma *cadeia de caracteres* que contém um URI de um contêiner de blobs do [Armazenamento do Azure](https://azure.microsoft.com/documentation/services/storage/) como *saída* do trabalho. O trabalho cria um blob de blocos nesse contêiner para armazenar quaisquer informações de erro do **Trabalho**de importação concluído. O token SAS deve incluir estas permissões:

   ```csharp
   SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Read 
     | SharedAccessBlobPermissions.Delete
   ```

> [!NOTE]
> Os dois parâmetros podem apontar para o mesmo contêiner de blobs. Os parâmetros separados simplesmente permitem mais controle sobre seus dados, pois o contêiner de saída requer permissões adicionais.

O snippet de código de C# a seguir mostra como iniciar um trabalho de importação:

```csharp
JobProperties importJob = 
   await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);
```

Esse método também pode ser usado para importar os dados para o dispositivo gêmeo. O formato para a entrada de dados é o mesmo mostrado na seção **ExportDevicesAsync**. Dessa forma, você pode reimportar os dados exportados. O **$metadata** é opcional.

## <a name="import-behavior"></a>Comportamento de importação

É possível usar o método **ImportDevicesAsync** para executar as seguintes operações em massa no registro de identidade:

* Registro em massa de novos dispositivos
* Exclusões em massa dos dispositivos existentes
* Alterações de status em massa (habilitar ou desabilitar dispositivos)
* Atribuição em massa de novas chaves de autenticação de dispositivo
* Nova geração automática em massa de chaves de autenticação de dispositivo
* Atualização em massa de dados gêmeos

É possível executar qualquer combinação das operações anteriores em uma única chamada **ImportDevicesAsync** . Por exemplo, é possível registrar novos dispositivos e excluir ou atualizar dispositivos existentes ao mesmo tempo. Quando usado com o método **ExportDevicesAsync** , é possível migrar completamente todos os seus dispositivos de um hub IoT para outro.

Se o arquivo de importação incluir metadados gêmeos, esses metadados substituirão os metadados gêmeos existentes. Se o arquivo de importação não inclui metadados gêmeos, somente os metadados `lastUpdateTime` são atualizados usando a hora atual.

Use a propriedade opcional **importMode** nos dados de serialização de importação para cada dispositivo para controlar o processo de importação por dispositivo. A propriedade **importMode** tem as seguintes opções:

| importMode | DESCRIÇÃO |
| --- | --- |
| **createOrUpdate** |Se não existir um dispositivo com a especificada **ID**, foi registrado recentemente. <br/>Se o dispositivo já existir, as informações existentes serão substituídas pelos dados de entrada fornecidos sem considerar o valor de **ETag** . <br> Opcionalmente, o usuário pode especificar dados gêmeos junto com os dados do dispositivo. A etag do gêmeo, se especificada, será processada independentemente da etag do dispositivo. Se houver uma incompatibilidade com a etag do gêmeo existente, um erro será registrado no arquivo de log. |
| **create** |Se não existir um dispositivo com a especificada **ID**, foi registrado recentemente. <br/>Se o dispositivo já existir, um erro será gravado no arquivo de log. <br> Opcionalmente, o usuário pode especificar dados gêmeos junto com os dados do dispositivo. A etag do gêmeo, se especificada, será processada independentemente da etag do dispositivo. Se houver uma incompatibilidade com a etag do gêmeo existente, um erro será registrado no arquivo de log. |
| **atualizar** |Se já existir um dispositivo com o especificado **identificação**, informações existentes serão substituídas pelos dados de entrada fornecidos sem considerar para o **ETag** valor. <br/>Se o dispositivo não existir, um erro será gravado no arquivo de log. |
| **updateIfMatchETag** |Se já existir um dispositivo com o especificado **identificação**, informações existentes serão substituídas pelos dados de entrada fornecidos somente se houver um **ETag** corresponder. <br/>Se o dispositivo não existir, um erro será gravado no arquivo de log. <br/>Se não houver uma correspondência de **ETag** , um erro será gravado no arquivo de log. |
| **createOrUpdateIfMatchETag** |Se não existir um dispositivo com a especificada **ID**, foi registrado recentemente. <br/>Se o dispositivo já existir, as informações existentes serão substituídas pelos dados de entrada fornecidos somente se houver uma correspondência de **ETag** . <br/>Se não houver uma correspondência de **ETag** , um erro será gravado no arquivo de log. <br> Opcionalmente, o usuário pode especificar dados gêmeos junto com os dados do dispositivo. A etag do gêmeo, se especificada, será processada independentemente da etag do dispositivo. Se houver uma incompatibilidade com a etag do gêmeo existente, um erro será registrado no arquivo de log. |
| **delete** |Se já existir um dispositivo com o especificado **identificação**, ele é excluído sem considerar a **ETag** valor. <br/>Se o dispositivo não existir, um erro será gravado no arquivo de log. |
| **deleteIfMatchETag** |Se já existir um dispositivo com o especificado **identificação**, ele é excluído somente se houver um **ETag** corresponder. Se o dispositivo não existir, um erro será gravado no arquivo de log. <br/>Se não houver uma correspondência de ETag, um erro será gravado no arquivo de log. |

> [!NOTE]
> Se os dados de serialização não definirem explicitamente um sinalizador **importMode** para um dispositivo, eles usarão **createOrUpdate** como padrão durante a operação de importação.

## <a name="import-devices-example--bulk-device-provisioning"></a>Exemplo de importação de dispositivos – provisionamento de dispositivo em massa

O seguinte exemplo de código C# ilustra como gerar várias identidades de dispositivo que:

* Inclua as chaves de autenticação.
* Grave essas informações de dispositivo em um blob de blocos.
* Importe os dispositivos para o registro de identidade.

```csharp
// Provision 1,000 more devices
var serializedDevices = new List<string>();

for (var i = 0; i < 1000; i++)
{
  // Create a new ExportImportDevice
  // CryptoKeyGenerator is in the Microsoft.Azure.Devices.Common namespace
  var deviceToAdd = new ExportImportDevice()
  {
    Id = Guid.NewGuid().ToString(),
    Status = DeviceStatus.Enabled,
    Authentication = new AuthenticationMechanism()
    {
      SymmetricKey = new SymmetricKey()
      {
        PrimaryKey = CryptoKeyGenerator.GenerateKey(32),
        SecondaryKey = CryptoKeyGenerator.GenerateKey(32)
      }
    },
    ImportMode = ImportMode.Create
  };

  // Add device to the list
  serializedDevices.Add(JsonConvert.SerializeObject(deviceToAdd));
}

// Write the list to the blob
var sb = new StringBuilder();
serializedDevices.ForEach(serializedDevice => sb.AppendLine(serializedDevice));
await blob.DeleteIfExistsAsync();

using (CloudBlobStream stream = await blob.OpenWriteAsync())
{
  byte[] bytes = Encoding.UTF8.GetBytes(sb.ToString());
  for (var i = 0; i < bytes.Length; i += 500)
  {
    int length = Math.Min(bytes.Length - i, 500);
    await stream.WriteAsync(bytes, i, length);
  }
}

// Call import using the blob to add new devices
// Log information related to the job is written to the same container
// This normally takes 1 minute per 100 devices
JobProperties importJob =
   await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);

// Wait until job is finished
while(true)
{
  importJob = await registryManager.GetJobAsync(importJob.JobId);
  if (importJob.Status == JobStatus.Completed || 
      importJob.Status == JobStatus.Failed ||
      importJob.Status == JobStatus.Cancelled)
  {
    // Job has finished executing
    break;
  }

  await Task.Delay(TimeSpan.FromSeconds(5));
}
```

## <a name="import-devices-example--bulk-deletion"></a>Exemplo de importação de dispositivos – exclusão em massa

O exemplo de código a seguir mostra como excluir os dispositivos que você adicionou usando o exemplo de código anterior:

```csharp
// Step 1: Update each device's ImportMode to be Delete
sb = new StringBuilder();
serializedDevices.ForEach(serializedDevice =>
{
  // Deserialize back to an ExportImportDevice
  var device = JsonConvert.DeserializeObject<ExportImportDevice>(serializedDevice);

  // Update property
  device.ImportMode = ImportMode.Delete;

  // Re-serialize
  sb.AppendLine(JsonConvert.SerializeObject(device));
});

// Step 2: Write the new import data back to the block blob
await blob.DeleteIfExistsAsync();
using (CloudBlobStream stream = await blob.OpenWriteAsync())
{
  byte[] bytes = Encoding.UTF8.GetBytes(sb.ToString());
  for (var i = 0; i < bytes.Length; i += 500)
  {
    int length = Math.Min(bytes.Length - i, 500);
    await stream.WriteAsync(bytes, i, length);
  }
}

// Step 3: Call import using the same blob to delete all devices
importJob = await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);

// Wait until job is finished
while(true)
{
  importJob = await registryManager.GetJobAsync(importJob.JobId);
  if (importJob.Status == JobStatus.Completed || 
      importJob.Status == JobStatus.Failed ||
      importJob.Status == JobStatus.Cancelled)
  {
    // Job has finished executing
    break;
  }

  await Task.Delay(TimeSpan.FromSeconds(5));
}
```

## <a name="get-the-container-sas-uri"></a>Obter o URI de SAS do contêiner

O exemplo de código a seguir mostra como gerar um [URI SAS](../storage/blobs/storage-dotnet-shared-access-signature-part-2.md) com as permissões de leitura, gravação e exclusão para um contêiner de blobs:

```csharp
static string GetContainerSasUri(CloudBlobContainer container)
{
  // Set the expiry time and permissions for the container.
  // In this case no start time is specified, so the
  // shared access signature becomes valid immediately.
  var sasConstraints = new SharedAccessBlobPolicy();
  sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
  sasConstraints.Permissions = 
    SharedAccessBlobPermissions.Write | 
    SharedAccessBlobPermissions.Read | 
    SharedAccessBlobPermissions.Delete;

  // Generate the shared access signature on the container,
  // setting the constraints directly on the signature.
  string sasContainerToken = container.GetSharedAccessSignature(sasConstraints);

  // Return the URI string for the container,
  // including the SAS token.
  return container.Uri + sasContainerToken;
}
```

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a realizar operações em massa no registro de identidade em um Hub IoT. Para saber mais sobre o gerenciamento do Hub IoT do Azure, siga estes links:

* [Métricas do IoT Hub](iot-hub-metrics.md)
* [Monitoramento de operações](iot-hub-operations-monitoring.md)

Para explorar melhor as funcionalidades do Hub IoT, consulte:

* [Guia do desenvolvedor do Hub IoT](iot-hub-devguide.md)
* [Implantando o AI em dispositivos de borda com o Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)

Para explorar usando o Serviço de Provisionamento de Dispositivos do Hub IoT para habilitar o provisionamento sem toque e Just-In-Time, consulte: 

* [Serviço de Provisionamento de Dispositivos no Hub IoT do Azure](/azure/iot-dps)
