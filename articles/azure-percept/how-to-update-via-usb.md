---
title: Atualizar seu Percept do Azure DK em uma conexão USB
description: Saiba como atualizar o Azure Percept DK em uma conexão USB
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/18/2021
ms.custom: template-how-to
ms.openlocfilehash: cd6e4e62123b4d4b927cf385aaf64a066eecc1e0
ms.sourcegitcommit: a67b972d655a5a2d5e909faa2ea0911912f6a828
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104887743"
---
# <a name="how-to-update-azure-percept-dk-over-a-usb-connection"></a>Como atualizar o Azure Percept DK em uma conexão USB

Embora o uso de atualizações OTA (over-the-Air) seja o melhor método para manter o firmware e o sistema operacional do seu kit de desenvolvimento atualizados, há cenários em que é necessário atualizar (ou "piscar") o kit de desenvolvimento em uma conexão USB:

- Uma atualização OTA não é possível devido a conectividade ou outros problemas técnicos
- O dispositivo precisa ser reiniciado de volta para seu estado de fábrica

Este guia mostrará como atualizar com êxito o sistema operacional e o firmware do seu kit de desenvolvimento em uma conexão USB.

> [!WARNING]
> Atualizar seu kit de desenvolvimento sobre USB excluirá todos os dados existentes no dispositivo, incluindo modelos e contêineres de ia.
>
> Siga todas as instruções na ordem. Ignorar etapas pode colocar seu kit de desenvolvimento em um estado inutilizável.

## <a name="prerequisites"></a>Pré-requisitos

- Um Percept do Azure DK
- Um computador host baseado em Windows, Linux ou OS X com Wi-Fi recurso e uma porta USB-C ou USB-A disponível
- Um USB-C para USB-um cabo (opcional, vendido separadamente)
- Um logon SSH, criado durante a [experiência de instalação do Azure PERCEPT DK](./quickstart-percept-dk-set-up.md)

## <a name="download-software-tools-and-update-files"></a>Baixar ferramentas de software e arquivos de atualização

1. [Ferramenta NXP uuu](https://github.com/NXPmicro/mfgtools/releases). Baixe o arquivo de uuu.exe de **versão mais recente** (para Windows) ou o arquivo uuu (para Linux) na guia **ativos** .

1. [7-zip](https://www.7-zip.org/). Este software será usado para extrair o arquivo de imagem bruto de seu arquivo compactado XZ. Baixe e instale o arquivo. exe apropriado.

1. [Baixe os arquivos de atualização](https://go.microsoft.com/fwlink/?linkid=2155734).

1. Verifique se todos os três artefatos de compilação estão presentes:
    - Azure-Percept-DK-*&lt; número &gt; de versão*. Raw. XZ
    - Fast-hab-FW. Raw
    - emmc_full.txt

## <a name="set-up-your-environment"></a>Configure seu ambiente

1. Crie uma pasta/diretório no computador host em um local que seja fácil de acessar por meio da linha de comando.

1. Copie a ferramenta UUU (**uuu.exe** ou **uuu**) para a nova pasta.

1. Extraia o arquivo **. Raw de *&lt; número &gt; de versão* do Azure-Percept-DK-Version** do arquivo compactado, clicando com o botão direito do mouse em **Azure-Percept-DK-*&lt; &gt; version number*. Raw. XZ** e selecionando **7-zip** &gt; **Extract aqui**.

1. Mova o arquivo **. Raw de *&lt; &gt; versão* do Azure-Percept-DK-Version** extraído, **Fast-hab-FW. Raw** e **emmc_full.txt** para a pasta que contém a ferramenta uuu.

## <a name="update-your-device"></a>Atualizar seu dispositivo

1. [SSH em seu kit de desenvolvimento](./how-to-ssh-into-percept-dk.md).

1. Em seguida, abra um prompt de comando do Windows (**Iniciar**  >  **cmd**) ou um terminal do Linux e navegue até a pasta onde os arquivos de atualização e a ferramenta uuu estão armazenados. Digite o seguinte comando no prompt de comando ou no terminal para preparar o computador para receber um dispositivo que seja atualizado:

    - Windows:

        ```console
        uuu -b emmc_full.txt fast-hab-fw.raw Azure-Percept-DK-<version number>.raw 
        ```

    - Linux:

        ```bash
        sudo ./uuu -b emmc_full.txt fast-hab-fw.raw Azure-Percept-DK-<version number>.raw
        ```

1. Desconecte o dispositivo Azure Percept Vision da porta USB-C da placa da operadora.

1. Conecte o cabo USB-C fornecido à porta USB-C da placa da portadora e à porta USB-C do computador host. Se o computador tiver apenas uma porta USB-A, conecte um USB-C a USB-A (vendido separadamente) à placa-portadora e ao computador host.

1. No prompt do cliente SSH, insira os seguintes comandos:

    1. Defina o dispositivo para o modo de atualização USB:

        ```bash
        sudo flagutil    -wBfRequestUsbFlash    -v1
        ```

    1. Reinicialize o dispositivo. A instalação da atualização será iniciada.

        ```bash
        sudo reboot -f
        ```

1. Navegue de volta para o outro prompt de comando ou terminal. Quando a atualização for concluída, você verá uma mensagem com ```Success 1    Failure 0``` :

    > [!NOTE]
    > Após a atualização, o dispositivo será redefinido para as configurações de fábrica e você perderá sua conexão de Wi-Fi e o logon SSH.

1. Quando a atualização for concluída, desligue a placa da operadora. Desconecte o cabo USB do PC.  

## <a name="next-steps"></a>Próximas etapas

Trabalhe com a [experiência de instalação do Azure PERCEPT DK](./quickstart-percept-dk-set-up.md) para reconfigurar seu dispositivo.