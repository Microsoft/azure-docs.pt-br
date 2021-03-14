---
title: Atualizar seu Percept do Azure DK em uma conexão USB
description: Saiba como atualizar o Azure Percept DK em uma conexão USB
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/18/2021
ms.custom: template-how-to
ms.openlocfilehash: 7f5e5e4da9fea671fc85a55fc8cc191fa14b720f
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101661749"
---
# <a name="how-to-update-azure-percept-dk-over-a-usb-connection"></a>Como atualizar o Azure Percept DK em uma conexão USB

Siga este guia para saber como executar uma atualização USB para a placa transportadora do Azure Percept DK.

## <a name="prerequisites"></a>Pré-requisitos
- Computador host com uma porta USB-C ou USB-A disponível.
- Placa de operadora do Azure Percept DK (Kit de desenvolvimento) e USB-C fornecido para cabo USB-C. Se o computador host tiver uma porta USB-A, mas não uma porta USB-C, você poderá usar um cabo USB-C para USB-A (vendido separadamente).
- Instalar [a](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html) saída (acesso de administrador necessário).
- Instale a ferramenta NXP UUU. Baixe o arquivo de uuu.exe de [versão mais recente](https://github.com/NXPmicro/mfgtools/releases) (para Windows) ou o arquivo uuu (para Linux) na guia ativos.
- [Instale o 7-zip](https://www.7-zip.org/). Este software será usado para extrair o arquivo de imagem bruto de seu arquivo compactado XZ. Baixe e instale o arquivo. exe apropriado.

## <a name="steps"></a>Etapas
1.  Baixe os [três arquivos de atualização USB](https://go.microsoft.com/fwlink/?linkid=2155734)a seguir:
    - pe101-UEFI-***&lt; número &gt; de versão***. Raw. XZ
    - emmc_full.txt
    - Fast-hab-FW. Raw
 
1. Extraia para pe101-UEFI-***&lt; número &gt; de versão**. Raw do número * _&lt; &gt; de versão_ do pe101-UEFI compactado * *. arquivo RAW. XZ. Não sabe como extrair? Baixe e instale o 7-zip, clique com o botão direito do mouse no arquivo de imagem **. XZ** e selecione a extração de 7-zip &gt; aqui.

1. Copie os três arquivos a seguir para a pasta que contém a ferramenta UUU:
    - Extraiu pe101-UEFI-***&lt; version &gt;***. Raw File (da etapa 2).
    - emmc_full.txt (da etapa 1).
    - Fast-hab-FW. Raw (da etapa 1).
 
1. Ligue o kit de desenvolvimento.
1. [Conectar-se ao kit de desenvolvimento por SSH](./how-to-ssh-into-percept-dk.md)
1. Abra um prompt de comando do Windows (iniciar &gt; cmd) ou um terminal do Linux e navegue até a pasta onde os arquivos de atualização estão armazenados. Execute o seguinte comando para iniciar a atualização:
    - Windows: ```uuu -b emmc_full.txt fast-hab-fw.raw pe101-uefi-<version number>.raw```
    - Linux: ```sudo ./uuu -b emmc_full.txt fast-hab-fw.raw pe101-uefi-<version number>.raw```
    
Depois de executar esses comandos, você poderá ver uma mensagem informando "aguardando o dispositivo..." no prompt de comando. Isso é esperado e você deve prosseguir para a próxima etapa.
    
1. Conecte a placa de portadora do kit de desenvolvimento ao computador host por meio de um cabo USB. Sempre conecte-se da porta de placas de transporte USB-C para a porta USB-c ou USB-a do computador host (USB-C para USB-um cabo vendido separadamente), dependendo de quais portas estão disponíveis. 
 
1. No terminal SSH/de saída, insira os seguintes comandos para definir o kit de desenvolvimento no modo USB e, em seguida, para reinicializar o kit de desenvolvimento.
    - ```flagutil    -wBfRequestUsbFlash    -v1```
    - ```reboot -f```
 
1. Você pode receber uma indicação de que o computador host reconhece o dispositivo e o processo de atualização será iniciado automaticamente. Navegue de volta para o prompt de comando para ver o status. O processo levará até dez minutos e, quando a atualização for bem-sucedida, você verá uma mensagem dizendo "êxito 1 falha 0"
 
1. Quando a atualização for concluída, desligue a placa da operadora. Desconecte o cabo USB do PC.  Conecte o módulo do Azure Percept Vision de volta à placa da operadora usando o cabo USB.

1. Ligue novamente a placa operadora.

## <a name="next-steps"></a>Próximas etapas

Seu kit de desenvolvimento agora foi atualizado com êxito. Você pode continuar o desenvolvimento e a operação com seu devkit.