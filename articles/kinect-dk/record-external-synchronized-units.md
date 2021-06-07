---
title: Usar o gravador de Kinect do Azure com dispositivos sincronizados externos
description: Saiba como registrar dados de dispositivos configurados para sincronização externa usando o gravador de Kinect do Azure.
author: tesych
ms.author: tesych
ms.reviewer: jawirth
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Kinect, sensor, visualizador, sincronização externa, atraso da fase, profundidade, RGB, câmera, cabo de áudio, gravador
ms.openlocfilehash: 052f6f1ac9f90e764de25d1d4d1b25b3d50a848d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "85276334"
---
# <a name="use-azure-kinect-recorder-with-external-synchronized-devices"></a>Usar o gravador de Kinect do Azure com dispositivos sincronizados externos

Este artigo fornece orientação sobre como o [gravador de Kinect do Azure](azure-kinect-recorder.md) pode registrar dispositivos configurados para sincronização externa de dados.

## <a name="prerequisites"></a>Pré-requisitos

- [Configure várias unidades do Azure Kinect DK para sincronização externa](https://support.microsoft.com/help/4494429).

## <a name="external-synchronization-constraints"></a>Restrições de sincronização externa

- O dispositivo mestre não pode ter a sincronização em cabo conectado.
- O dispositivo mestre deve transmitir a câmera RGB para habilitar a sincronização.
- Todas as unidades devem usar a mesma configuração de câmera (taxa de quadros e resolução).
- Todas as unidades devem executar o mesmo firmware de dispositivo (instruções de[atualização de firmware](update-device-firmware.md) ).
- Todos os dispositivos subordinados devem ser iniciados antes do dispositivo mestre.
- O mesmo valor de exposição deve ser definido em todos os dispositivos.
- A configuração *mestre de atraso* de cada subordinado é relativa ao dispositivo mestre.

## <a name="record-when-each-unit-has-a-host-pc"></a>Registrar quando cada unidade tiver um PC host

No exemplo a seguir, cada dispositivo tem seu próprio PC host dedicado.
É recomendável conectar dispositivos a PCs dedicados para evitar problemas com largura de banda USB e uso de CPU/GPU.

### <a name="subordinate-1"></a>Subordinado-1

1. Configurar o gravador para a primeira unidade

      `k4arecorder.exe --external-sync sub -e -8 -r 5 -l 10 sub1.mkv`

2. O dispositivo começa a esperar

    ```console
    Device serial number: 000011590212
    Device version: Rel; C: 1.5.78; D: 1.5.60[6109.6109]; A: 1.5.13
    Device started
    [subordinate mode] Waiting for signal from master
    ```

### <a name="subordinate-2"></a>Subordinado-2

1. Configurar o gravador para a segunda unidade

    `k4arecorder.exe --external-sync sub -e -8 -r 5 -l 10 sub2.mkv`

2. O dispositivo começa a esperar

    ```console
    Device serial number: 000011590212
    Device version: Rel; C: 1.5.78; D: 1.5.60[6109.6109]; A: 1.5.13
    Device started
    [subordinate mode] Waiting for signal from master
    ```

### <a name="master"></a>Principal

1. Iniciar gravação no mestre

    `>k4arecorder.exe --external-sync master -e -8 -r 5 -l 10 master.mkv`

2. Aguardar até a gravação terminar

## <a name="recording-when-multiple-units-connected-to-single-host-pc"></a>Gravando quando várias unidades estão conectadas a um único PC host

Você pode ter vários DKs do Azure Kinect conectados a um único PC host. No entanto, isso pode ser muito exigente para a largura de banda USB e a computação do host. Para reduzir a demanda:

- Conecte cada dispositivo ao próprio controlador de host USB.
- Ter uma GPU poderosa que pode manipular o mecanismo de profundidade para cada dispositivo.
- Registre apenas os sensores necessários e use a taxa de quadros inferior.

Sempre inicie dispositivos subordinados primeiro e o mestre por último.

## <a name="subordinate-1"></a>Subordinado-1

1. Iniciar gravador em subordinado

    `>k4arecorder.exe --device 1 --external-sync subordinate --imu OFF -e -8 -r 5 -l 5 output-2.mkv`

2. O dispositivo entra em estado de espera

## <a name="master"></a>Principal

1. Iniciar dispositivo mestre

    `>k4arecorder.exe --device 0 --external-sync master --imu OFF -e -8 -r 5 -l 5 output-1.mkv`

2. Aguardar gravação para concluir

## <a name="playing-recording"></a>Executando gravação

Você pode usar o [Azure Kinect Viewer](azure-kinect-viewer.md) para reproduzir a gravação.



## <a name="tips"></a>Dicas

- Use a exposição manual para registrar câmeras sincronizadas. A exposição automática da câmera RGB pode afetar a sincronização de tempo.
- A reinicialização do dispositivo subordinado fará com que a sincronização seja perdida.
- Alguns [modos de câmera](hardware-specification.md#depth-camera-supported-operating-modes) dão suporte a 15 fps máx. Recomendamos que você não misture modos/taxas de quadros entre dispositivos
- Conectar várias unidades a um único PC pode facilmente saturar a largura de banda USB, considere o uso de um PC host separado por dispositivo. Preste atenção também na computação de CPU/GPU.
- Desabilite o microfone e o IMU se eles não forem necessários para melhorar a confiabilidade.

Para problemas, consulte [solução de problemas](troubleshooting.md)

## <a name="see-also"></a>Confira também

- [Configurar a sincronização externa](https://support.microsoft.com/help/4494429/sync-multiple-devices)
- [Gravador do Azure Kinect](azure-kinect-recorder.md) para configurações do gravador e informações adicionais.
- O [Azure Kinect Viewer](azure-kinect-viewer.md) para executar gravações ou definir propriedades de câmera RGB não estão disponíveis por meio do gravador.
- [Ferramenta de firmware Kinect do Azure](azure-kinect-firmware-tool.md) para atualizar o firmware do dispositivo.
