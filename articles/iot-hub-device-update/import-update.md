---
title: Como importar uma nova atualização | Microsoft Docs
description: Guia de How-To para importar uma nova atualização para a atualização de dispositivo do Hub IoT para o Hub IoT.
author: andrewbrownmsft
ms.author: andbrown
ms.date: 2/11/2021
ms.topic: how-to
ms.service: iot-hub-device-update
ms.openlocfilehash: 3644f26f989fec05ee76afd9f930c31b25234c7f
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105608520"
---
# <a name="import-new-update"></a>Importar nova atualização
Saiba como importar uma nova atualização para a atualização de dispositivo para o Hub IoT. Se você ainda não tiver feito isso, não se esqueça de se familiarizar com os conceitos básicos de [importação](import-concepts.md).

## <a name="prerequisites"></a>Pré-requisitos

* [Acesso a um hub IOT com a atualização de dispositivo para o Hub IOT habilitado](create-device-update-account.md). 
* Um dispositivo IoT (ou simulador) provisionado para atualização de dispositivo no Hub IoT.
   * Se estiver usando um dispositivo real, você precisará de um arquivo de imagem de atualização para atualização de imagem ou [arquivo de manifesto apt](device-update-apt-manifest.md) para atualização de pacote.
* [PowerShell 5](/powershell/scripting/install/installing-powershell) ou posterior (inclui instalações do Linux, MacOS e Windows)
* Navegadores compatíveis:
  * [Microsoft Edge](https://www.microsoft.com/edge)
  * Google Chrome

> [!NOTE]
> Alguns dados enviados para esse serviço podem ser processados em uma região fora da região em que essa instância foi criada.

## <a name="create-device-update-import-manifest"></a>Criar manifesto de importação de atualização de dispositivo

1. Verifique se o arquivo de imagem de atualização ou o arquivo de manifesto APT está localizado em um diretório acessível do PowerShell.

2. Crie um arquivo de texto chamado **AduUpdate. psm1** no diretório em que está localizado o arquivo de imagem de atualização ou o nome de manifesto do apt. Em seguida, abra o cmdlet do PowerShell [AduUpdate. psm1](https://github.com/Azure/iot-hub-device-update/tree/main/tools/AduCmdlets) , copie o conteúdo para o arquivo de texto e salve o arquivo de texto.

3. No PowerShell, navegue até o diretório em que você criou o cmdlet do PowerShell na etapa 2. Em seguida, execute:

    ```powershell
    Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process
    Import-Module .\AduUpdate.psm1
    ```

4. Execute os comandos a seguir substituindo os valores de parâmetro de exemplo para gerar um manifesto de importação, um arquivo JSON que descreve a atualização:
    ```powershell
    $compat = New-AduUpdateCompatibility -DeviceManufacturer 'deviceManufacturer' -DeviceModel 'deviceModel'

    $importManifest = New-AduImportManifest -Provider 'updateProvider' -Name 'updateName' -Version 'updateVersion' `
                                            -UpdateType 'updateType' -InstalledCriteria 'installedCriteria' `
                                            -Compatibility $compat -Files 'updateFilePath(s)'

    $importManifest | Out-File '.\importManifest.json' -Encoding UTF8
    ```

    Para referência rápida, aqui estão alguns exemplos de valores para os parâmetros acima. Você também pode exibir o [esquema de manifesto de importação](import-schema.md) completo para obter mais detalhes.

    | Parâmetro | Descrição |
    | --------- | ----------- |
    | deviceManufacturer | Fabricante do dispositivo ao qual a atualização é compatível, por exemplo, contoso. Deve corresponder à [Propriedade do dispositivo](./device-update-plug-and-play.md#device-properties)do _fabricante_ .
    | deviceModel | Modelo do dispositivo ao qual a atualização é compatível, por exemplo, torradeira. Deve corresponder à [Propriedade do dispositivo](./device-update-plug-and-play.md#device-properties)de _modelo_ .
    | updateprovider | Entidade que está criando ou diretamente responsável pela atualização. Em geral, ele será um nome de empresa.
    | updatename | Identificador para uma classe de atualizações. A classe pode ser qualquer coisa que você escolher. Em geral, ele será um nome de dispositivo ou modelo.
    | updateVersion | Número de versão que distingue essa atualização de outras que têm o mesmo nome e provedor. Não corresponde a uma versão de um componente de software individual no dispositivo (mas pode se você escolher).
    | updateType | <ul><li>Especificar `microsoft/swupdate:1` para atualização de imagem</li><li>Especificar `microsoft/apt:1` para a atualização do pacote</li></ul>
    | installedCriteria | <ul><li>Especificar o valor de SWVersion para o `microsoft/swupdate:1` tipo de atualização</li><li>Especifique o valor recomendado para o `microsoft/apt:1` tipo de atualização.
    | updateFilePath (s) | Caminho para os arquivos de atualização no computador


## <a name="review-generated-import-manifest"></a>Examinar o manifesto de importação gerado

Exemplo:
```json
{
  "updateId": {
    "provider": "Microsoft",
    "name": "Toaster",
    "version": "2.0"
  },
  "updateType": "microsoft/swupdate:1",
  "installedCriteria": "5",
  "compatibility": [
    {
      "deviceManufacturer": "Fabrikam",
      "deviceModel": "Toaster"
    },
    {
      "deviceManufacturer": "Contoso",
      "deviceModel": "Toaster"
    }
  ],
  "files": [
    {
      "filename": "file1.json",
      "sizeInBytes": 7,
      "hashes": {
        "sha256": "K2mn97qWmKSaSaM9SFdhC0QIEJ/wluXV7CoTlM8zMUo="
      }
    },
    {
      "filename": "file2.zip",
      "sizeInBytes": 11,
      "hashes": {
        "sha256": "gbG9pxCr9RMH2Pv57vBxKjm89uhUstD06wvQSioLMgU="
      }
    }
  ],
  "createdDateTime": "2020-10-08T03:32:52.477Z",
  "manifestVersion": "2.0"
}
```

## <a name="import-update"></a>Importar atualização

[!NOTE]
As instruções a seguir mostram como importar uma atualização por meio da interface do usuário do portal do Azure. Você também pode usar a [atualização do dispositivo para APIs do Hub IOT](https://github.com/Azure/iot-hub-device-update/tree/main/docs/publish-api-reference) para importar uma atualização. 

1. Faça logon no [portal do Azure](https://portal.azure.com) e navegue até o Hub IOT com a atualização do dispositivo.

2. No lado esquerdo da página, selecione "atualizações de dispositivo" em "gerenciamento de dispositivo automático".

   :::image type="content" source="media/import-update/import-updates-3.png" alt-text="Importar atualizações" lightbox="media/import-update/import-updates-3.png":::

3. Você verá várias guias na parte superior da tela. Selecione a guia Atualizações.

   :::image type="content" source="media/import-update/updates-tab.png" alt-text="Atualizações" lightbox="media/import-update/updates-tab.png":::

4. Selecione "+ importar nova atualização" abaixo do cabeçalho "pronto para implantar".

   :::image type="content" source="media/import-update/import-new-update-2.png" alt-text="Importar nova atualização" lightbox="media/import-update/import-new-update-2.png":::

5. Selecione o ícone de pasta ou caixa de texto em "Selecionar um arquivo de manifesto de importação". Uma caixa de diálogo de seletor de arquivos aparecerá. Selecione o manifesto de importação criado anteriormente usando o cmdlet do PowerShell. Em seguida, selecione o ícone de pasta ou caixa de texto em "Selecionar um ou mais arquivos de atualização". Uma caixa de diálogo de seletor de arquivos aparecerá. Selecione o arquivo (s) de atualização.

   :::image type="content" source="media/import-update/select-update-files.png" alt-text="Selecionar arquivos de atualização" lightbox="media/import-update/select-update-files.png":::

6. Selecione o ícone de pasta ou a caixa de texto em "Selecionar um contêiner de armazenamento". Em seguida, selecione a conta de armazenamento apropriada. O contêiner de armazenamento é usado para preparar temporariamente os arquivos de atualização.

   :::image type="content" source="media/import-update/storage-account.png" alt-text="Conta de armazenamento" lightbox="media/import-update/storage-account.png":::

7. Caso já tenha criado um contêiner, você pode reutilizá-lo. (Caso contrário, selecione "+ Contêiner" para criar um novo contêiner de armazenamento para atualizações.)  Selecione o contêiner que você deseja usar e clique em "Selecionar".

   :::image type="content" source="media/import-update/container.png" alt-text="Selecionar contêiner" lightbox="media/import-update/container.png":::

8. Selecione "Enviar" para iniciar o processo de importação.

   :::image type="content" source="media/import-update/publish-update.png" alt-text="Publicar atualização" lightbox="media/import-update/publish-update.png":::

9. O processo de importação é iniciado e a tela alterna para a seção "importar histórico". Selecione "atualizar" para exibir o progresso até que o processo de importação seja concluído (dependendo do tamanho da atualização, isso pode ser concluído em alguns minutos, mas pode levar mais tempo).

   :::image type="content" source="media/import-update/update-publishing-sequence-2.png" alt-text="Atualizar sequenciamento de importação" lightbox="media/import-update/update-publishing-sequence-2.png":::

10. Quando a coluna Status indicar que a importação foi bem-sucedida, selecione o cabeçalho "Pronto para implantar". Agora a atualização importada deve estar presente na lista.

   :::image type="content" source="media/import-update/update-ready.png" alt-text="Status do Trabalho" lightbox="media/import-update/update-ready.png":::

## <a name="next-steps"></a>Próximas etapas

[Criar grupos](create-update-group.md)

[Saiba mais sobre os conceitos de importação](import-concepts.md)