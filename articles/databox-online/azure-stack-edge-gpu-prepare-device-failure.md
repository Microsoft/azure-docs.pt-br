---
title: Preparar a falha do dispositivo pro Edge Azure Stack
description: Descreve como obter uma substituição para um dispositivo Azure Stack Edge pro com falha.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 60469dc834c28bd8dbc73a1883ea01b06797c01f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102442971"
---
# <a name="prepare-for-an-azure-stack-edge-pro-gpu-device-failure"></a>Preparar-se para uma falha de dispositivo do Azure Stack Edge pro GPU

[!INCLUDE [applies-to-GPU-and-pro-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-sku.md)]

Este artigo ajuda você a se preparar para uma falha de dispositivo, detalhando como salvar e fazer backup da configuração e dos dados do dispositivo em seu dispositivo Azure Stack Edge pro GPU. 

O artigo não inclui etapas para fazer backup de contêineres de kubernetes e IoT implantados em seu dispositivo Azure Stack Edge pro GPU. 

## <a name="understand-device-failures"></a>Entender falhas do dispositivo

Seu dispositivo de GPU pro Azure Stack Edge pode apresentar dois tipos de falhas de hardware.

- Falhas tolerável que exigem a substituição de um componente de hardware. Essas falhas permitirão que você opere o dispositivo em um estado degradado. Exemplos dessas falhas incluem uma única unidade de fonte de energia (PSU) com falha ou um único disco com falha no dispositivo. Em cada um desses casos, o dispositivo pode continuar a operar. Entre em contato com Suporte da Microsoft assim que possível para substituir os componentes com falha.

- Falhas não tolerável que exigem que você substitua todo o dispositivo-por exemplo, quando dois discos falham em seu dispositivo. Nesses casos, entre em contato com Suporte da Microsoft imediatamente. Depois de determinar que a substituição de um dispositivo é necessária, o suporte ajudará na substituição de seu dispositivo de Azure Stack Edge.

Para se preparar para falhas não tolerantes, você precisa fazer o backup dos seguintes itens em seu dispositivo:

- Informações sobre a configuração do dispositivo
- Dados em compartilhamentos locais de borda e compartilhamentos de nuvem de borda
- Arquivos e pastas associados às VMs em execução no seu dispositivo


## <a name="back-up-device-configuration"></a>Fazer backup da configuração do dispositivo

Durante a configuração inicial do dispositivo, é importante manter uma cópia das informações de configuração do dispositivo, conforme descrito na [lista de verificação de implantação](azure-stack-edge-gpu-deploy-checklist.md). Durante a recuperação, essas informações de configuração serão usadas para aplicar ao novo dispositivo de substituição. 

## <a name="protect-device-data"></a>Proteger os dados do dispositivo

Os dados do dispositivo podem ser de um dos seguintes tipos:

- Dados em compartilhamentos de nuvem do Edge
- Dados em compartilhamentos locais
- Arquivos e pastas em VMs

As seções a seguir discutem as etapas e recomendações para proteger cada um desses tipos de dados em seu dispositivo.

## <a name="protect-data-in-edge-cloud-shares"></a>Proteger dados em compartilhamentos de nuvem de borda

Você pode criar compartilhamentos de nuvem de borda que os dados de camada do seu dispositivo para o Azure. Dependendo da largura de banda da rede disponível, configure os modelos de largura de banda em seu dispositivo para minimizar qualquer perda de dados se ocorrer uma falha não tolerável.

> [!IMPORTANT]
> Se o dispositivo tiver uma falha não tolerável, os dados locais que não estiverem em camadas do dispositivo para o Azure poderão ser perdidos. 

## <a name="protect-data-in-edge-local-shares"></a>Proteger dados em compartilhamentos locais de borda

Se você estiver implantando kubernetes ou IoT Edge, configure o para salvar os dados do aplicativo no dispositivo localmente e não Sincronize com o armazenamento do Azure. Os dados são armazenados em um compartilhamento no dispositivo. Você pode achar importante fazer backup dos dados nesses compartilhamentos.

As soluções de proteção de dados de terceiros a seguir podem fornecer uma solução de backup para os dados nos compartilhamentos SMB locais ou NFS. 

| Software de terceiros           | Referência à solução                               |
|--------------------------------|---------------------------------------------------------|
| Cohesity                       | [https://www.cohesity.com/solution/cloud/azure/](https://www.cohesity.com/solution/cloud/azure/) <br> Para obter detalhes, entre em contato com Cohesity.          |
| Commvault                      | [https://www.commvault.com/azure](https://www.commvault.com/azure) <br> Para obter detalhes, entre em contato com Commvault.          |
| Veritas                        | [http://veritas.com/azure](http://veritas.com/azure) <br> Para obter detalhes, entre em contato com Veritas.   |
| Veeam                          | [https://www.veeam.com/kb4041](https://www.veeam.com/kb4041) <br> Para obter detalhes, entre em contato com Veeam. |


## <a name="protect-files-and-folders-on-vms"></a>Proteger arquivos e pastas em VMs

O Azure Stack Edge funciona com o backup do Azure e outras soluções de proteção de dados de terceiros para fornecer uma solução de backup para proteger os dados contidos nas VMs implantadas no dispositivo. A tabela a seguir lista referências a soluções disponíveis das quais você pode escolher.


| Soluções de backup        | SO com suporte   | Referência                                                                |
|-------------------------|----------------|--------------------------------------------------------------------------|
| Agente de Serviços de Recuperação do Microsoft Azure (MARS) para o backup do Azure | Windows        | [Sobre o agente do MARS](../backup/backup-azure-about-mars.md)    |
| Cohesity                | Windows, Linux | [Resumo da solução de Microsoft Azure integração, backup & recuperação](https://www.cohesity.com/solution/cloud/azure) <br>Para obter detalhes, entre em contato com Cohesity.                          |
| Commvault               | Windows, Linux | [https://www.commvault.com/azure](https://www.commvault.com/azure) <br>Para obter detalhes, entre em contato com Commvault.                          |
| Veritas                 | Windows, Linux | [https://vox.veritas.com/t5/Protection/Protecting-Azure-Stack-Edge-with-NetBackup/ba-p/883370](https://vox.veritas.com/t5/Protection/Protecting-Azure-Stack-Edge-with-NetBackup/ba-p/883370) <br> Para obter detalhes, entre em contato com Veritas.                    |
| Veeam                   | Windows, Linux | [https://www.veeam.com/kb4041](https://www.veeam.com/kb4041) <br> Para obter detalhes, entre em contato com Veeam. |


## <a name="next-steps"></a>Próximas etapas

- Saiba como se [recuperar de um dispositivo de GPU pro de Azure Stack de borda com falha](azure-stack-edge-gpu-recover-device-failure.md).