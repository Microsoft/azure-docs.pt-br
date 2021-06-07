---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/04/2019
ms.author: alkohli
ms.openlocfilehash: 3312d1ec7c2535e103cf8959599c0d4c3014f520
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "86218231"
---
1. [Conecte-se à interface do PowerShell](#connect-to-the-powershell-interface).
2. Use o `Get-HcsApplianceInfo` para obter as informações do seu dispositivo.

    O exemplo a seguir mostra o uso desse cmdlet:

    ```
    [10.100.10.10]: PS>Get-HcsApplianceInfo
    
    Id                            : b2044bdb-56fd-4561-a90b-407b2a67bdfc
    FriendlyName                  : DBE-NBSVFQR94S6
    Name                          : DBE-NBSVFQR94S6
    SerialNumber                  : HCS-NBSVFQR94S6
    DeviceId                      : 40d7288d-cd28-481d-a1ea-87ba9e71ca6b
    Model                         : Virtual
    FriendlySoftwareVersion       : Data Box Gateway 1902
    HcsVersion                    : 1.4.771.324
    IsClustered                   : False
    IsVirtual                     : True
    LocalCapacityInMb             : 1964992
    SystemState                   : Initialized
    SystemStatus                  : Normal
    Type                          : DataBoxGateway
    CloudReadRateBytesPerSec      : 0
    CloudWriteRateBytesPerSec     : 0
    IsInitialPasswordSet          : True
    FriendlySoftwareVersionNumber : 1902
    UploadPolicy                  : All
    DataDiskResiliencySettingName : Simple
    ApplianceTypeFriendlyName     : Data Box Gateway
    IsRegistered                  : False
    ```

    Aqui está uma tabela que resume algumas das informações importantes do dispositivo:

    | Parâmetro | Descrição |
    |-----------|-------------|
    | FriendlyName                   | O nome amigável do dispositivo, conforme configurado por meio da interface do usuário da Web local durante a implantação do dispositivo. O nome amigável padrão é o número de série do dispositivo.  |
    | SerialNumber                   | O número de série do dispositivo é um número exclusivo atribuído na fábrica.                                                                             |
    | Modelo                          | O modelo para seu Azure Stack borda ou dispositivo de Gateway do Data Box. O modelo é físico para Azure Stack borda e virtual para Gateway do Data Box.                   |
    | FriendlySoftwareVersion        | A cadeia de caracteres amigável que corresponde à versão do software do dispositivo. Para um sistema executando a visualização, a versão de software amigável seria Data Box Edge 1902. |
    | HcsVersion                     | A versão do software HCS em execução no seu dispositivo. Por exemplo, a versão do software HCS correspondente ao Data Box Edge 1902 é 1.4.771.324.            |
    | LocalCapacityInMb              | A capacidade local total do dispositivo em megabits.                                                                                                        |
    | IsRegistered                   | Esse valor indica se o dispositivo está ativado com o serviço.                                                                                         |


