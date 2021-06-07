---
title: Tutorial para instalar o dispositivo físico do Azure Stack Edge Pro R | Microsoft Docs
description: O segundo tutorial sobre como instalar o Azure Stack Edge Pro R envolve como fazer o cabeamento do dispositivo físico para energia e rede.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/18/2020
ms.author: alkohli
ms.openlocfilehash: c751c1d9caa06973171d35d5e6bd1f945f9d7a77
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106059897"
---
# <a name="tutorial-install-azure-stack-edge-pro-r"></a>Tutorial: Instalar o Azure Stack Edge Pro R

Este tutorial descreve como instalar um dispositivo físico do Azure Stack Edge Pro R. O procedimento de instalação envolve o cabeamento do dispositivo.

A instalação pode levar aproximadamente 30 minutos para ser concluída.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Inspecionar o dispositivo
> * Cabear o dispositivo

## <a name="prerequisites"></a>Pré-requisitos

Os pré-requisitos de instalação de um dispositivo físico são os seguintes:

### <a name="for-the-azure-stack-edge-resource"></a>Para o recurso do Azure Stack Edge

Antes de começar, verifique se:

* Você concluiu todas as etapas descritas em [Preparar a implantação do Azure Stack Edge Pro R](azure-stack-edge-pro-r-deploy-prep.md).
    * Você criou um recurso do Azure Stack Edge para implantar seu dispositivo.
    * Você já gerou a chave de ativação para ativar seu dispositivo com o recurso do Azure Stack Edge.

 
### <a name="for-the-azure-stack-edge-pro-r-physical-device"></a>Para o dispositivo físico do Azure Stack Edge Pro R

Antes de implantar um dispositivo:

- Coloque o dispositivo em uma superfície de trabalho plana, estável e nivelada.
- Verifique se o local em que você pretende configurar tem:
    - Alimentação CA padrão de uma fonte independente

        -OU-
    - Uma PDU (unidade de distribuição de energia) de rack. O dispositivo acompanha um no-break
    

### <a name="for-the-network-in-the-datacenter"></a>Para a rede no datacenter

Antes de começar:

- Examine os requisitos de rede para implantar o Azure Stack Edge Pro R e configure a rede de datacenter de acordo com os requisitos. Para saber mais, confira [Requisitos de rede do Azure Stack Edge Pro R](azure-stack-edge-pro-r-system-requirements.md#networking-port-requirements).

- Garanta que a largura de banda mínima da Internet seja de 20 Mbps para o funcionamento ideal do dispositivo.


## <a name="inspect-the-device"></a>Inspecionar o dispositivo

Este dispositivo é fornecido como uma unidade. Conclua as etapas a seguir para desempacotar seu dispositivo.

1. Coloque a caixa em uma superfície plana e nivelada.
2. Inspecione a caixa do dispositivo para verificar se há danos. Abra a caixa e inspecione o dispositivo. Se a caixa ou o dispositivo parecer danificado, entre em contato com o Suporte da Microsoft para ajudar você a avaliar se o dispositivo está em bom funcionamento.
3. Após abrir a caixa, verifique se você tem:
    - Um compartimento de dispositivo Azure Stack Edge Pro R
    - Um no-break
    - Dois cabos de alimentação curtos para conectar o dispositivo ao no-break
    - Um cabo de alimentação para conectar o no-break à fonte de alimentação

Caso não tenha recebido todos os itens listados aqui, entre em contato com o suporte do Azure Stack Edge Pro R. A próxima etapa é cabear o dispositivo.


## <a name="cable-the-device"></a>Cabear o dispositivo

Os procedimentos a seguir explicam como cabear o dispositivo Azure Stack Edge Pro R para energia e rede.

Antes de começar o cabeamento do dispositivo, você precisará do seguinte:

- Seu dispositivo físico Azure Stack Edge Pro R no local de instalação.
- Um cabo de alimentação.
- Pelo menos um cabo de rede RJ-45 de 1 GbE para conectar-se à interface de gerenciamento. Há duas interfaces de rede de 1 GbE, uma de gerenciamento e uma de dados, no dispositivo.
- Um cabo de cobre SFP+ de 10/25 GbE para cada adaptador de rede de dados a ser configurado. Pelo menos um adaptador de rede de dados entre PORTA 3 ou PORTA 4 precisa estar conectado à Internet (com conectividade com o Azure).  
- Acesso a uma unidade de distribuição de energia (recomendado).

> [!NOTE]
> - Se você está conectando somente um adaptador de rede de dados, recomendamos que você use um adaptador de rede de 25/10 GbE, como a PORTA 3 ou a PORTA 4, para enviar dados para o Azure. 
> - Para melhor desempenho e para lidar com grandes volumes de dados, considere a possibilidade de se conectar a todas as portas de dados.
> - O dispositivo Azure Stack Edge Pro R deve estar conectado à rede de datacenter de modo que possa ingerir dados de servidores de fonte de dados.

No dispositivo Azure Stack Edge Pro R:

- O painel frontal tem unidades de disco e um botão de energia.

    - Há oito slots de discos na parte frontal do seu dispositivo.
    - O dispositivo também tem discos SATA 2 X M.2 que funcionam como discos do sistema operacional. 

- O backplane inclui PSUs (unidades de alimentação) redundantes.
- O backplane tem quatro adaptadores de rede:

    - Duas interfaces de 1 Gbps.
    - Dois interfaces de 25 Gbps que também podem servir como interfaces de 10 Gbps.
    - Um controlador BMC.

<!--- The back plane has two network cards corresponding to the 4 ports:

    - QLogic FastLinQ 41264
    - QLogic FastLinQ 41262

For a full list of supported cables, switches, and transceivers for these network cards, go to [Cavium FastlinQ 41000 Series Interoperability Matrix](https://www.marvell.com/documents/xalflardzafh32cfvi0z/).-->
 
Execute as etapas a seguir para cabear o dispositivo para obter energia e rede.

1. Identifique as várias portas no backplane do dispositivo.

    ![Backplane de um dispositivo cabeado](./media/azure-stack-edge-pro-r-deploy-install/backplane-cabled.png)

2. Localize os slots do disco e o botão de energia na parte frontal do dispositivo.

    ![Plano frontal de um dispositivo](./media/azure-stack-edge-pro-r-deploy-install/device-front-plane-labeled-1.png)

3. Conecte uma extremidade do cabo de alimentação ao no-break. Anexe a outra extremidade do cabo de alimentação às PDUs (unidades de distribuição de energia) do rack. 
5. Pressione o botão de energia para ligar o dispositivo.
6. Conecte a PORTA 1 do adaptador de rede de 1 GbE ao computador usado para configurar o dispositivo físico. A PORTA 1 é o adaptador de gerenciamento dedicado.
7. Conecte uma ou mais PORTA 2, PORTA 3, PORTA 4 à rede do datacenter/Internet.

    - Se estiver se conectando na PORTA 2, use o cabo de rede RJ-45.
    - Para os adaptadores de rede de 10/25 GbE, use os cabos de cobre SFP+.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você viu tópicos do Azure Stack Edge Pro R, como:

> [!div class="checklist"]
> * Desempacotar o dispositivo
> * Cabear o dispositivo

Vá para o próximo tutorial para aprender a se conectar ao seu dispositivo.

> [!div class="nextstepaction"]
> [Conectar-se ao Azure Stack Edge Pro R](./azure-stack-edge-pro-r-deploy-connect.md)
