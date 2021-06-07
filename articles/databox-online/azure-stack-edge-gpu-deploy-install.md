---
title: 'Tutorial de instalação: desempacotar, cabear e montar em rack um dispositivo físico de GPU do Azure Stack Edge Pro | Microsoft Docs'
description: O segundo tutorial sobre como instalar a GPU do Azure Stack Edge Pro envolve desempacotamento, montagem em rack e cabeamento do dispositivo físico.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 12/21/2020
ms.author: alkohli
ms.openlocfilehash: a415dba4bb4fd5b95e98ae7c104cae92db27f34c
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106060152"
---
# <a name="tutorial-install-azure-stack-edge-pro-with-gpu"></a>Tutorial: instalar o Azure Stack Edge Pro com GPU

Este tutorial descreve como instalar um dispositivo físico do Azure Stack Edge Pro com uma GPU. O procedimento de instalação envolve desempacotamento, montagem em rack e cabeamento do dispositivo. 

A instalação poderá levar cerca de duas horas para ser concluída.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Desempacotar o dispositivo
> * Montar o dispositivo em rack
> * Cabear o dispositivo

## <a name="prerequisites"></a>Pré-requisitos

Os pré-requisitos de instalação de um dispositivo físico são os seguintes:

### <a name="for-the-azure-stack-edge-resource"></a>Para o recurso do Azure Stack Edge

Antes de começar, verifique se:

* Você concluiu todas as etapas descritas em [Preparar a implantação do Azure Stack Edge Pro com GPU](azure-stack-edge-gpu-deploy-prep.md).
    * Você criou um recurso do Azure Stack Edge para implantar seu dispositivo.
    * Você já gerou a chave de ativação para ativar seu dispositivo com o recurso do Azure Stack Edge.

 
### <a name="for-the-azure-stack-edge-pro-physical-device"></a>Para o dispositivo físico do Azure Stack Edge Pro

Antes de implantar um dispositivo:

- Coloque o dispositivo em uma superfície de trabalho plana, estável e nivelada.
- Verifique se o local em que você pretende configurar tem:
    - Alimentação CA padrão de uma fonte independente.

        -OU-
    - Uma PDU (unidade de distribuição de energia) em rack com um UPS (no-break).
    - Um slot 1U disponível no rack em que você pretende montar o dispositivo.

### <a name="for-the-network-in-the-datacenter"></a>Para a rede no datacenter

Antes de começar:

- Examine os requisitos de rede para implantar o Azure Stack Edge Pro e configure a rede de datacenter de acordo com os requisitos. Para saber mais, veja [Requisitos de rede do Azure Stack Edge Pro](azure-stack-edge-system-requirements.md#networking-port-requirements).

- Garanta que a largura de banda mínima da Internet seja de 20 Mbps para o funcionamento ideal do dispositivo.


## <a name="unpack-the-device"></a>Desempacotar o dispositivo

Este dispositivo é fornecido em uma única caixa. Conclua as etapas a seguir para desempacotar seu dispositivo. 

1. Coloque a caixa em uma superfície plana e nivelada.
2. Inspecione a caixa e a espuma da embalagem para verificar se não há amassados, cortes, danos por água ou qualquer outro dano visível. Se a caixa ou a embalagem estiver gravemente danificada, não a abra. Contate o Suporte da Microsoft para receber ajuda e saber se o dispositivo está em boas condições de funcionamento.
3. Abra a caixa. Depois de abrir a caixa, verifique se você tem estes itens:
    - Um compartimento de dispositivo Azure Stack Edge Pro
    - Dois cabos de alimentação
    - Um conjunto de kit de trilho
    - Um livreto Informações sobre Regulamentação, Segurança e Meio-ambiente

Caso não tenha recebido todos os itens listados aqui, [Entre em contato com Suporte da Microsoft](azure-stack-edge-contact-microsoft-support.md). A próxima etapa é montar o dispositivo em rack.


## <a name="rack-the-device"></a>Montar o dispositivo em rack

O dispositivo precisa ser instalado em um rack padrão de 19 polegadas. Use o procedimento a seguir para montar o dispositivo em um rack padrão de 19 polegadas.

> [!IMPORTANT]
> Os dispositivos Azure Stack Edge Pro devem ser montados em rack para a operação apropriada.


### <a name="prerequisites"></a>Pré-requisitos

- Antes de começar, leia as instruções de segurança no livreto Informações sobre Regulamentação, Segurança e Meio-ambiente. Esse livreto foi enviado com o dispositivo.
- Comece a instalar os trilhos no espaço alocado mais próximo à parte inferior do compartimento de rack.
- Para a configuração de montagem do trilho com ferramentas:
    -  Você precisa fornecer oito parafusos: 10-32, 12-24, M5 ou M6. O diâmetro da cabeça dos parafusos precisa ser inferior a 10 mm (0,4").
    -  Você precisa de uma chave de fenda de ponta chata.

### <a name="identify-the-rail-kit-contents"></a>Identificar o conteúdo do kit de trilho

Localize os componentes para instalação do conjunto do kit de trilho:
- Dois conjuntos de trilho deslizante A7 Dell ReadyRails II
- Duas tiras com fixador de contato

    ![Identificar o conteúdo do kit de trilho](./media/azure-stack-edge-deploy-install/identify-rail-kit-contents.png)

### <a name="install-and-remove-tool-less-rails-square-hole-or-round-hole-racks"></a>Instale e remova os trilhos sem ferramentas (racks de orifício quadrado ou redondo)

> [!TIP]
> Essa opção é sem ferramenta porque não exige ferramentas para instalar e remover os trilhos no quadrado não rosqueado ou furos redondos no rack.

1. Posicione as extremidades esquerda e direita do trilho rotuladas **FRENTE** voltadas para dentro e vire cada extremidade até encaixá-las nos orifícios no lado dianteiro das bordas verticais do rack.
2. Alinhe cada extremidade nos orifícios inferior e superior dos espaços em U desejados.
3. Encaixe a extremidade traseira do trilho até que ela se ajuste totalmente na borda vertical do rack e a trava se encaixe no lugar. Repita essas etapas para posicionar e encaixar a extremidade dianteira da borda vertical do rack.
4. Para remover os trilhos, aperte o botão de liberação da trava no ponto médio da extremidade e remova cada trilho.

    ![Instalar e remover os trilhos sem ferramentas](./media/azure-stack-edge-deploy-install/installing-removing-tool-less-rails.png)

### <a name="install-and-remove-tooled-rails-threaded-hole-racks"></a>Instalar e remover os trilhos com ferramentas (racks com orifício rosqueado)

> [!TIP]
> Essa opção é com ferramentas porque requer uma ferramenta (_uma chave de fenda de ponta chata_) para instalar e remover os trilhos redondo rosqueados nos racks.

1. Remova os pinos dos suportes de montagem dianteiro e traseiro usando uma chave de fenda com ponta achatada.
2. Puxe e gire os subconjuntos de trava de trilho para removê-los dos suportes de montagem.
3. Fixe os trilhos de montagem esquerdo e direito nas bordas verticais dianteiras do rack usando dois pares de parafusos.
4. Deslize os suportes traseiros esquerdo e direito para frente em direção às bordas verticais traseiras do rack e fixe-os usando dois pares de parafusos.

    ![Instalar e remover trilhos com ferramentas 2](./media/azure-stack-edge-deploy-install/installing-removing-tooled-rails.png)

### <a name="install-the-system-in-a-rack"></a>Instalar o sistema em um rack

1. Puxe os trilhos deslizantes internos para fora do rack até eles fiquem travados.
2. Localize o suporte do trilho traseiro em cada lado do sistema e abaixe-os até os slots J traseiros sobre os conjuntos deslizantes. Gire o sistema para baixo até todos os suportes do trilho serem encaixados nos slots J.
3. Empurre o sistema para dentro até as alavancas de trava se encaixarem.
4. Pressione os botões de trava de liberação deslizante em ambos os trilhos e deslize o sistema no rack.

    ![Instalar o sistema em um rack](./media/azure-stack-edge-deploy-install/installing-system-rack.png)

### <a name="remove-the-system-from-the-rack"></a>Remover o sistema do rack

1. Localize as alavancas de trava nas laterais dos trilhos internos.
2. Destrave cada alavanca girando-a até a posição de liberação.
3. Segure as laterais do sistema com firmeza e puxe-o para frente até os suportes do trilho ficarem na frente dos slots J. Levante o sistema para cima e longe do rack e coloque-o em uma superfície nivelada.

    ![Remover o sistema do rack](./media/azure-stack-edge-deploy-install/removing-system-rack.png)

### <a name="engage-and-release-the-slam-latch"></a>Encaixar e liberar a trava do tipo slam

> [!NOTE]
> Para sistemas não equipados com travas do tipo slam, fixe o sistema usando parafusos, conforme descrito na etapa 3 deste procedimento.

1. Na parte dianteira, localize a trava do tipo slam em uma das laterais do sistema.
2. As travas fecham automaticamente conforme o sistema é empurrado para o rack e são liberadas quando puxadas para cima.
3. Para fixar o sistema de remessa no rack ou para outros ambientes instáveis, localize o parafuso de montagem rígida sob cada trava e aperte cada parafuso com uma chave Phillips nº 2.

    ![Encaixar e liberar a trava do tipo slam](./media/azure-stack-edge-deploy-install/engaging-releasing-slam-latch.png)


## <a name="cable-the-device"></a>Cabear o dispositivo

Passe os cabos e conecte o dispositivo. Os procedimentos a seguir explicam como cabear o dispositivo Azure Stack Edge Pro para energia e rede.

Antes de executar o cabeamento do dispositivo, será necessário obter o seguinte:

- Seu dispositivo físico Azure Stack Edge Pro, desempacotado e montado em rack.
- Dois cabos de alimentação.
- Pelo menos um cabo de rede RJ-45 de 1 GbE para conectar-se à interface de gerenciamento. Há duas interfaces de rede de 1 GbE, uma de gerenciamento e uma de dados, no dispositivo.
- Um cabo de cobre SFP+ de 25 GbE para cada interface de rede de dados a ser configurado. Pelo menos um adaptador de rede de dados da PORTA 2, PORTA 3, PORTA 4, PORTA 5 ou PORTA 6 precisa estar conectado à Internet (com conectividade com o Azure).  
- Acesso a duas unidades de distribuição de energia (recomendado).
- Pelo menos um comutador de rede de 1 GbE para conectar um adaptador de rede de 1 GbE à Internet para os dados. A IU da Web local não estará acessível caso o comutador conectado não tenha pelo menos 1 GbE. Caso esteja usando uma interface de 10/25 GbE para os dados, será necessário obter um comutador de 10 GbE ou 25 GbE.

> [!NOTE]
> - Se você está conectando somente um adaptador de rede de dados, recomendamos que você use um adaptador de rede de 25/10 GbE, como a PORTA 3, a PORTA 4, a PORTA 5 ou a PORTA 6, para enviar dados para o Azure. 
> - Para melhor desempenho e para lidar com grandes volumes de dados, considere a possibilidade de se conectar a todas as portas de dados.
> - O dispositivo Azure Stack Edge Pro deve estar conectado à rede de datacenter de modo que possa ingerir dados de servidores de fonte de dados.

No dispositivo Azure Stack Edge Pro:

- O painel frontal tem unidades de disco e um botão de energia.

    - Há 10 slots de discos na parte frontal do seu dispositivo.
    - O slot 0 tem uma unidade SATA de 240 GB usada como um disco do sistema operacional. O slot 1 está vazio e os slots 2 a 6 são SSDs NVMe usados como discos de dados. Os slots 7 a 9 também estão vazios.
- O backplane inclui PSUs (unidades de alimentação) redundantes.
- O backplane tem seis adaptadores de rede:

    - Duas interfaces de 1 Gbps.
    - Quatro interfaces de 25 Gbps que também podem servir como interfaces de 10 Gbps.
    - Um controlador BMC.

- O backplane tem duas placas de rede correspondentes às seis portas:

    - **Adaptador personalizado `Qlogic` Cavium 25G NDC da Microsoft** – Porta 1 a 4.
    - **Adaptador de rede de quatro canais Mellanox dual port 25G ConnectX-4** – porta 5 e porta 6.

Para obter uma lista completa de cabos, comutadores e transceptores compatíveis com essas placas de rede, acesse:

- [Matriz de interoperabilidade do adaptador `Qlogic` Cavium 25G NDC](https://www.marvell.com/documents/xalflardzafh32cfvi0z/).
- [Produtos compatíveis com o adaptador de rede de quatro canais Mellanox dual port 25G ConnectX-4](https://docs.mellanox.com/display/ConnectX4LxFirmwarev14271016/Firmware+Compatible+Products).  

 
Execute as etapas a seguir para cabear o dispositivo para obter energia e rede.

1. Identifique as várias portas no backplane do dispositivo. Você pode ter recebido um dos dispositivos a seguir da fábrica, dependendo do número de GPUs em seu dispositivo.

    - Dispositivo com dois slots PCI (Peripheral Component Interconnect) e uma GPU

        ![Backplane de um dispositivo cabeado](./media/azure-stack-edge-gpu-deploy-install/ase-two-pci-slots.png)

    - Dispositivo com três slots PCI e uma GPU

        ![Plano traseiro de um dispositivo cabeado 2](./media/azure-stack-edge-gpu-deploy-install/ase-three-pci-slots-one-gpu.png)

    - Dispositivo com três slots PCI e duas GPUs

        ![Plano traseiro de um dispositivo cabeado 3](./media/azure-stack-edge-gpu-deploy-install/ase-three-pci-slots-two-gpu.png)

2. Localize os slots do disco e o botão de energia na parte frontal do dispositivo.

    ![Plano frontal de um dispositivo](./media/azure-stack-edge-gpu-deploy-install/ase-gpu-device-front-plane-labeled.png)

3. Conecte os cabos de energia a cada uma das PSUs no compartimento. Para garantir a alta disponibilidade, instale e conecte as duas PSUs a diferentes fontes de alimentação.
4. Ligue os cabos de alimentação às PDUs (unidades de distribuição de energia) do rack. Verifique se as duas PSUs usam fontes de energia separadas.
5. Pressione o botão de energia para ligar o dispositivo.
6. Conecte a PORTA 1 do adaptador de rede de 1 GbE ao computador usado para configurar o dispositivo físico. A PORTA 1 serve como interface de gerenciamento.
    
    > [!NOTE]
    > Se você conectar o computador diretamente ao seu dispositivo (sem passar por um comutador), use um cabo cruzado ou um adaptador USB Ethernet.

7. Conecte uma ou mais das portas (PORTA 2, PORTA 3, PORTA 4, PORTA 5 ou PORTA 6) ao rede do datacenter/Internet.

    - Se estiver conectando a PORTA 2, use o cabo de rede RJ-45 de 1-GbE.
    - Para os adaptadores de rede de 10/25 GbE, use a fibra ou os cabos de cobre SFP+. Se estiver usando fibra, use um adaptador óptico para SFP.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você viu tópicos do Azure Stack Edge Pro, como:

> [!div class="checklist"]
> * Desempacotar o dispositivo
> * Montar o dispositivo em rack
> * Cabear o dispositivo

Vá para o próximo tutorial para aprender a se conectar ao seu dispositivo.

> [!div class="nextstepaction"]
> [Conectar o Azure Stack Edge Pro](./azure-stack-edge-gpu-deploy-connect.md)
