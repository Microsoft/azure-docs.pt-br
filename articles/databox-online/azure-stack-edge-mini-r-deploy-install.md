---
title: Tutorial para instalar o dispositivo físico Azure Stack Edge Mini R | Microsoft Docs
description: O segundo tutorial sobre como instalar o Azure Stack Edge Mini R envolve como fazer o cabeamento do dispositivo físico para energia e rede.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/20/2020
ms.author: alkohli
ms.openlocfilehash: 8b154dabd6f672c6fdaf77c5f8d48f80fb40d5d8
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106060101"
---
# <a name="tutorial-install-azure-stack-edge-mini-r"></a>Tutorial: Instalar o Azure Stack Edge Mini R

Este tutorial descreve como instalar um dispositivo físico Azure Stack Edge Mini R. O procedimento de instalação envolve o cabeamento do dispositivo.

A instalação pode levar aproximadamente 30 minutos para ser concluída.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Inspecionar o dispositivo
> * Cabear o dispositivo

## <a name="prerequisites"></a>Pré-requisitos

Os pré-requisitos de instalação de um dispositivo físico são os seguintes:

### <a name="for-the-azure-stack-edge-resource"></a>Para o recurso do Azure Stack Edge

Antes de começar, verifique se:

* Você concluiu todas as etapas descritas em [Preparar a implantação do Azure Stack Edge Mini R](azure-stack-edge-mini-r-deploy-prep.md).
    * Você criou um recurso do Azure Stack Edge para implantar seu dispositivo.
    * Você já gerou a chave de ativação para ativar seu dispositivo com o recurso do Azure Stack Edge.

 
### <a name="for-the-azure-stack-edge-mini-r-physical-device"></a>Para o dispositivo físico Azure Stack Edge Mini R

Antes de implantar um dispositivo:

- Coloque o dispositivo em uma superfície de trabalho plana, estável e nivelada.
- Verifique se o local em que você pretende configurar tem:
    - Alimentação de CA padrão de uma fonte independente OU
    - Uma PDU (unidade de distribuição de energia) de rack. 
    

### <a name="for-the-network-in-the-datacenter"></a>Para a rede no datacenter

Antes de começar:

- Examine os requisitos de rede para implantar o Azure Stack Edge Mini R e configure a rede de datacenter de acordo com os requisitos. Para saber mais, veja [Requisitos de rede do Azure Stack Edge](azure-stack-edge-mini-r-system-requirements.md#networking-port-requirements).

- A largura de banda mínima da Internet deve ser de 20 Mbps para o funcionamento ideal do dispositivo. <!-- engg TBC -->


## <a name="inspect-the-device"></a>Inspecionar o dispositivo

Este dispositivo é fornecido como uma unidade. Conclua as etapas a seguir para desempacotar seu dispositivo.

1. Coloque a caixa em uma superfície plana e nivelada.
2. Inspecione a caixa do dispositivo para verificar se há danos. Abra a caixa e inspecione o dispositivo. Se a caixa ou o dispositivo parecer danificado, entre em contato com o Suporte da Microsoft para ajudar você a avaliar se o dispositivo está em bom funcionamento.
3. Após abrir a caixa, verifique se você tem:
    - Um dispositivo Azure Stack Edge Mini R portátil com amortecedores laterais anexados
    - Uma bateria e a tampa traseira anexada ao dispositivo. 
    - Um cabo de alimentação para conectar a bateria à fonte de alimentação 

Caso não tenha recebido todos os itens listados aqui, contate o suporte do Azure Stack Edge. A próxima etapa é cabear o dispositivo.


## <a name="cable-the-device"></a>Cabear o dispositivo

Os procedimentos a seguir explicam como cabear o dispositivo Azure Stack Edge para energia e rede.

Antes de começar o cabeamento do dispositivo, você precisará do seguinte:

- O dispositivo físico Azure Stack Edge Mini R no local de instalação.
- Um cabo de alimentação.
- Pelo menos um cabo de rede RJ-45 de 1 GbE para conectar-se à interface de gerenciamento. Há duas interfaces de rede de 1 GbE, uma de gerenciamento e uma de dados, no dispositivo.
- Um cabo de cobre SFP+ de 10 GbE para cada adaptador de rede de dados a ser configurado. Pelo menos um adaptador de rede de dados entre PORTA 3 ou PORTA 4 precisa estar conectado à Internet (com conectividade com o Azure).  
- Acesso a uma unidade de distribuição de energia (recomendado).

> [!NOTE]
> - Se você está conectando somente um adaptador de rede de dados, recomendamos que você use um adaptador de rede de 10 GbE, como a PORTA 3 ou a PORTA 4, para enviar dados para o Azure. 
> - Para melhor desempenho e para lidar com grandes volumes de dados, considere a possibilidade de se conectar a todas as portas de dados.
> - O dispositivo Azure Stack Edge deve estar conectado à rede de datacenter de modo que possa ingerir dados de servidores de fonte de dados. <!-- engg TBC -->

No dispositivo Azure Stack Edge:

- O painel frontal tem um adaptador para SSD. 

    - O dispositivo tem 1 disco SSD no slot. 
    - O dispositivo também tem um cartão CFx que serve como armazenamento para o disco do sistema operacional.
    
- O painel frontal tem adaptadores de rede e acesso à Wi-Fi.

    - Dois adaptadores de rede RJ 45 de 1 GbE. Elas são a PORTA 1 e a PORTA 2 na IU local do dispositivo.
    - Dois adaptadores de rede SFP+ de 10 GbE. Elas são a PORTA 3 e a PORTA 4 na IU local do dispositivo. 
    - Uma porta Wi-Fi com um transceptor Wi-Fi anexado.

- O painel frontal também tem um botão de energia. 

- O painel traseiro inclui uma bateria e uma tampa instaladas no dispositivo. 


Execute as etapas a seguir para cabear o dispositivo para obter energia e rede.

1. Identifique os diferentes componentes de rede e de armazenamento no plano frontal do dispositivo.

    ![Interfaces de rede e de armazenamento no dispositivo](./media/azure-stack-edge-mini-r-deploy-install/ports-front-plane.png)

2. Localize o botão de energia no canto inferior direito na parte frontal do dispositivo. 

    ![Plano frontal de um dispositivo com o botão de energia no dispositivo](./media/azure-stack-edge-mini-r-deploy-install/device-power-button.png)

3. A bateria está conectada ao plano traseiro do dispositivo. Identifique o segundo botão de energia localizado na bateria. 

    ![Plano frontal de um dispositivo com o botão de energia na bateria](./media/azure-stack-edge-mini-r-deploy-install/battery-power-button.png)


    Conecte uma extremidade do cabo de alimentação à bateria e a outra à tomada. 

    ![Conexão do cabo de alimentação](./media/azure-stack-edge-mini-r-deploy-install/power-cord-connector-1.png) 

    Para funcionar somente com a bateria (ou seja, com a bateria não conectada à fonte de alimentação), o interruptor de energia na frente e o interruptor da bateria devem estar na posição LIGADO. Quando a bateria está conectada a uma fonte de alimentação, somente o botão de energia na frente do dispositivo deve estar na posição LIGADO. 

4. Pressione o botão de energia na parte frontal para ligar o dispositivo. 
    
    > [!NOTE]
    > Para ligar ou desligar a alimentação do dispositivo, pressione novamente o botão preto na parte superior do botão de energia e coloque o botão de energia na posição LIGADO ou DESLIGADO. 

5. Se estiver configurando a Wi-Fi neste dispositivo, use o seguinte diagrama de cabeamento:

    ![Cabeamento para Wi-Fi](./media/azure-stack-edge-mini-r-deploy-install/wireless-cabled.png)  

    - Conecte a PORTA 1 do adaptador de rede de 1 GbE ao computador usado para configurar o dispositivo físico. A PORTA 1 é o adaptador de gerenciamento dedicado.


    Se estiver usando a configuração com fio para o dispositivo, use o seguinte diagrama:
     
    ![Cabeamento para conexão com fio](./media/azure-stack-edge-mini-r-deploy-install/wired-cabled.png)     
    - Conecte a PORTA 1 do adaptador de rede de 1 GbE ao computador usado para configurar o dispositivo físico. A PORTA 1 é o adaptador de gerenciamento dedicado.
    - Conecte uma ou mais PORTA 2, PORTA 3, PORTA 4 à rede do datacenter/Internet.
    
        - Se estiver se conectando na PORTA 2, use o cabo de rede RJ-45.
        - Para os adaptadores de rede de 10 GbE, use os cabos de cobre SFP+.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você viu tópicos do Azure Stack Edge, como:

> [!div class="checklist"]
> * Desempacotar o dispositivo
> * Cabear o dispositivo

Vá para o próximo tutorial para aprender a conectar, configurar e ativar seu dispositivo.

> [!div class="nextstepaction"]
> [Conectar e configurar o Edge Azure Stack Edge](./azure-stack-edge-mini-r-deploy-connect.md)
