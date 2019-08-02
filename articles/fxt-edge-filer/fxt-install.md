---
title: Tutorial sobre como instalar um dispositivo físico do Azure FXT Edge Filer | Microsoft Docs
description: Como desempacotar, montar no rack e gerenciar os cabos do componente de dispositivo físico do cache de armazenamento híbrido do Microsoft Azure FXT Edge Filer
services: ''
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 07/01/2019
ms.author: v-erkell
ms.openlocfilehash: ed9eca88e5ccc386b25acb95fa729a3cfb95cbd0
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67543430"
---
# <a name="tutorial-install-azure-fxt-edge-filer"></a>Tutorial: Instalar o Azure FXT Edge Filer 

Neste tutorial, você verá como instalar um nó de hardware do cache de armazenamento híbrido do Azure FXT Edge Filer. É necessário instalar ao menos três nós de hardware para criar um cluster do Azure FXT Edge Filer.

No procedimento de instalação, é preciso desempacotar, montar em rack e anexar o CMA (braço de gerenciamento de cabos) e o painel frontal. Outro tutorial explica como anexar os cabos de rede e conectar a energia. 

É necessária aproximadamente uma hora para instalar um nó do Azure FXT Edge Filer. 

Este tutorial inclui as seguintes etapas de instalação: 

> [!div class="checklist"]
> * Desempacotar o dispositivo
> * Montar o dispositivo em um rack
> * Instalar o painel frontal (opcional)

## <a name="installation-prerequisites"></a>Pré-requisitos da instalação 

Antes de começar, verifique se o data center e o rack a serem usados têm estes recursos:

* Um slot de 1U disponível no rack em que você pretende montar o dispositivo.
* Fonte de alimentação CA e sistemas de resfriamento que atendem às necessidades do Azure FXT Edge Filer. Leia [Especificações térmicas e de energia](fxt-specs.md#power-and-thermal-specifications) para ajudar a planejar e dimensionar a instalação.  

  > [!NOTE] 
  > Para aproveitar ao máximo as duas PSUs (unidades de fonte de alimentação), use as unidades de distribuição de energia em dois circuitos ao conectar com a alimentação CA. Leia [Conectar os cabos de alimentação](fxt-network-power.md#connect-power-cables) para saber detalhes.  

## <a name="unpack-the-hardware-node"></a>Desempacotar o nó de hardware 

Cada nó do Azure FXT Edge Filer é enviado em uma caixa diferente. Conclua estas etapas para desempacotar um dispositivo.

1. Coloque a caixa em uma superfície plana e nivelada.

2. Inspecione a caixa e a espuma da embalagem para verificar se não há amassados, cortes, danos por água ou qualquer outro dano visível. Se a caixa ou a embalagem estiver gravemente danificada, não a abra. Contate o Suporte da Microsoft para receber ajuda e saber se o dispositivo está em boas condições de funcionamento.

3. Abra a caixa. Verifique se os seguintes itens estão presentes:
   * Um dispositivo do FXT de compartimento único
   * Dois cabos de alimentação
   * Um painel frontal e a chave
   * Um conjunto de kit de trilho
   * Um CMA (braço de gerenciamento de cabos)
   * Livreto de instruções sobre a instalação do CMA
   * Livreto de instruções sobre a instalação do rack
   * Livreto "Informações sobre regulamentação, segurança e meio ambiente"

Caso não tenha recebido todos os itens listados aqui, entre em contato com o fornecedor de dispositivos para receber ajuda. 

Verifique se o dispositivo teve tempo suficiente para atingir temperatura ambiente antes de instalá-lo ou ativá-lo. Se você perceber condensação em qualquer peça do dispositivo, aguarde ao menos 24 antes de instalá-lo.

A próxima etapa é montar o dispositivo em rack.

## <a name="rack-the-device"></a>Montar o dispositivo em rack

O dispositivo do Azure FXT Edge Filer deve ser instalado em um rack padrão de 19 polegadas. 

O cache de armazenamento híbrido do Azure FXT Edge Filer tem três ou mais dispositivos do Azure FXT Edge Filer. Repita as etapas de instalação do rack para cada dispositivo do sistema. 

### <a name="rack-install-prerequisites"></a>Pré-requisitos de instalação do rack

* Antes de começar, leia as instruções de segurança no livreto "Informações sobre regulamentação, segurança e meio ambiente" enviado com o dispositivo.

  > [!NOTE]
  > Sempre use duas pessoas para levantar o nó, até ao instalá-lo ou removê-lo do rack. 

* Identifique o tipo de instalação em trilho usado com o rack do equipamento. 
  * Para racks de ajuste com orifícios quadrados ou redondos, siga as instruções de trilho sem ferramentas.
  * Para racks com orifício rosqueado, siga as instruções de trilho com ferramenta. 
  
    Para a configuração de montagem do trilho com ferramentas, você precisará fornecer oito parafusos: nº 10-32, nº 12-24, nº M5 ou nº M6. O diâmetro da cabeça dos parafusos precisa ser inferior a 10 mm (0,4").

### <a name="identify-the-rail-kit-contents"></a>Identificar o conteúdo do kit de trilho

Localize os componentes para instalação do conjunto do kit de trilho:

1. Dois conjuntos de trilho deslizante A7 Dell ReadyRails II (1)
1. Duas tiras com fixador de contato (2)

![Desenho numerado do conteúdo do kit de trilho](media/fxt-install/identify-rail-kit-contents-400.png)

### <a name="rail-assembly---tool-less-rails-square-hole-or-round-hole-racks"></a>Conjunto de trilhos – trilhos sem ferramentas (racks de orifício quadrado ou redondo)

Para racks de ajuste com orifícios quadrados ou redondos, siga este procedimento para montar e instalar os trilhos. 

1. Posicione as extremidades dos trilhos direito e esquerdo com o rótulo **FRENTE** voltados para dentro. Posicione cada extremidade de modo que se encaixem nos orifícios da parte frontal dos flanges do rack vertical. (1)

2. Alinhe cada extremidade com os orifícios superior e inferior do rack no espaço em que deseja montá-lo. (2)

3. Encaixe a extremidade traseira do trilho até que ela se ajuste totalmente na borda vertical do rack e a trava se encaixe no lugar. Repita essas etapas para posicionar e encaixar a extremidade dianteira da borda vertical do rack. (3)

> [!TIP]
> Para remover os trilhos, aperte o botão de liberação da trava no ponto médio da extremidade (4) e remova cada trilho.

![Diagrama de instalação e remoção de trilhos sem ferramentas, com as etapas numeradas](media/fxt-install/installing-removing-tool-less-rails-400.png)

### <a name="rail-assembly---tooled-rails-threaded-hole-racks"></a>Conjunto do trilho – trilhos com ferramentas (racks com orifício rosqueado)

Para racks com orifícios rosqueados, siga este procedimento para montar e instalar os trilhos.

1. Remova os pinos dos suportes de montagem dianteiro e traseiro usando uma chave de fenda com ponta achatada. (1)
1. Puxe e gire os subconjuntos de trava de trilho para removê-los dos suportes de montagem. (2)
1. Fixe os trilhos de montagem esquerdo e direito nas bordas verticais dianteiras do rack usando dois pares de parafusos. (3)
1. Deslize os suportes traseiros esquerdo e direito para frente em direção às bordas verticais traseiras do rack e fixe-os usando dois pares de parafusos. (4)

![Diagrama de instalação e remoção de trilhos com ferramentas, com as etapas numeradas](media/fxt-install/installing-removing-tooled-rails-400.png)

### <a name="install-the-system-in-the-rack"></a>Instalar o sistema em um rack

Siga estas etapas para montar o dispositivo do Azure FXT Edge Filer no rack.

1. Puxe os trilhos deslizantes internos para fora do rack até eles fiquem travados. (1)
1. Localize o suporte do trilho traseiro em cada lado do dispositivo e abaixe-os até os slots J traseiros sobre os conjuntos deslizantes. (2) 
1. Gire o dispositivo para baixo até todos os suportes do trilho serem encaixados nos slots J. (3)
1. Empurre o dispositivo para dentro até as alavancas de trava se encaixarem.
1. Pressione os botões de trava de liberação deslizante em ambos os trilhos (4) e deslize o dispositivo no rack.

![Diagrama "Instalar o sistema em um rack", com as etapas numeradas](media/fxt-install/installing-system-rack-400.png)

### <a name="remove-the-system-from-the-rack"></a>Remover o sistema do rack

Para remover o dispositivo do rack, siga este procedimento. 

1. Localize as alavancas de trava nas laterais dos trilhos internos (1).
2. Destrave cada alavanca girando-a até a posição de liberação (2).
3. Segure as laterais do sistema com firmeza e puxe-o para frente até os suportes do trilho ficarem na frente dos slots J. Levante o sistema para cima e longe do rack e coloque-o em uma superfície nivelada (3).

![Ilustração da remoção de um sistema do rack, com as etapas numeradas](media/fxt-install/removing-system-rack-400.png)

### <a name="engage-the-slam-latch"></a>Fechar a trava do tipo slam

1. Na parte dianteira, localize a trava do tipo slam (1) em uma das laterais do sistema.
2. As travas fecham automaticamente conforme o sistema é empurrado para o rack. 

Para liberar as travas ao remover o sistema, puxe-as para cima (2).

São fornecidos parafusos de montagem rígida opcionais para proteger o sistema do rack no envio ou em outros ambientes instáveis. Localize os parafusos em cada trava e aperte-os com um chave Phillips nº 2 (3).

![Ilustração numerada de como fechar e abrir a trava do tipo slam](media/fxt-install/engaging-releasing-slam-latch-400.png)

### <a name="install-the-cable-management-arm"></a>Instalar o braço de gerenciamento de cabos 

Um CMA (braço de gerenciamento de cabos) opcional é fornecido com o FXT Edge Filer. No pacote, há instruções para instalá-lo. 

1. Desempacotar e identificar os componentes do kit do braço de gerenciamento de cabos:
   * Bandeja do CMA (1)
   * CMA (2)
   * Braçadeiras do cabo de náilon (3)
   * Suporte de fixação do CMA (4)
   * Cabo do indicador de status (5) 

   > [!TIP] 
   > Para proteger o CMA para envio no rack, enrole as abraçadeiras nas cestas e na bandeja e feche-as firmemente. Ao proteger o CMA dessa maneira, você também protege o sistema em ambientes instáveis.

   ![Ilustração das peças do CMA](media/fxt-install/cma-kit-400.png)

2. Instale a bandeja do CMA.

   A bandeja do CMA é um apoio e atua como um retentor para o CMA. 

   1. Alinhe e conecte cada lado da bandeja aos suportes receptores das bordas internas dos trilhos. 
   1. Empurre a bandeja para frente até ela se encaixar. (1)
   1. Para remover a bandeja, aperte os botões de liberação da trava em direção ao centro e puxe a bandeja para fora dos suportes receptores (2).

   ![Ilustração da instalação da bandeja do CMA](media/fxt-install/cma-tray-install-400.png)

3. Instale os suportes de fixação do CMA. 

   > [!NOTE]
   >
   > * É possível fixar o CMA no trilho de montagem direito ou esquerdo, de acordo com o modo como você pretende rotear os cabos do sistema. 
   > * Para sua conveniência, monte o CMA no lado oposto às fontes de alimentação (lado A). Se estiver montado no lado B, o CMA precisará estar desconectado para remover a fonte de alimentação externa. 
   > * Sempre remova a bandeja antes de retirar as fontes de alimentação. 

   ![Ilustração da instalação do suporte do CMA](media/fxt-install/cma-bracket-l-r-install-400.png)

   1. Escolha o suporte de fixação correto do CMA para o lado em que você quer montá-lo (lado B ou lado A).
   1. Instale o suporte de fixação do CMA com o lado A ou B correspondente marcado na parte posterior do trilho deslizante.
   1. Alinhe os orifícios sobre o suporte com os pinos no trilho deslizante. Empurre o suporte para baixo até encaixar. 

4. Instalar o CMA.

   1. Na parte posterior do sistema, encaixe a trava da parte frontal do CMA no suporte interno do conjunto deslizante até a trava fechar (1). 
   1. Encaixe a outra trava na extremidade do suporte externo até a trava fechar (2). 
   1. Para remover o CMA, abra ambas as travas pressionando os botões de liberação do CMA na parte superior das caixas das travas interna e externa (3).

   ![Ilustração da instalação do CMA principal](media/fxt-install/cma-install-400.png)

   É possível girar o CMA para retirá-lo do sistema caso precise acessá-lo ou fazer manutenção. Na extremidade articulada, levante o CMA da bandeja para removê-lo (1). Após removê-lo da bandeja, gire o CMA para retirá-lo do sistema (2).

   ![Ilustração do CMA girado para fora para o manutenção](media/fxt-install/cma-swing-over-tray-400.png)

## <a name="install-the-front-bezel-optional"></a>Instalar o painel frontal (opcional)

Nesta seção, você aprenderá a instalar e remover o painel frontal (tampa) do hardware do Azure FXT Edge Filer. 

Para instalar o painel frontal: 

1. Localize e remova a chave do painel, que é fornecida no pacote do painel frontal. 
1. Alinhe o painel com a parte frontal do chassi e insira os pinos no lado direito do painel, nos orifícios do flange de montagem do rack direito no nó. 
1. Encaixe a extremidade esquerda do painel no chassi. Pressione o painel até o botão no lado esquerdo encaixar.
1. Trave o painel com a chave.

Para remover o painel frontal: 
1. Destrave o painel com a chave.
1. Pressione o botão de liberação no lado esquerdo e puxe a extremidade esquerda do painel para fora do chassi.
1. Desencaixe a extremidade direita e remova o painel.
   
   ![Imagem mostrando o botão de liberação à esquerda do painel e como removê-lo puxando para fora no lado esquerdo](media/fxt-install/remove-bezel-edited-600.png)

## <a name="next-steps"></a>Próximas etapas

Após desempacotar e montar o dispositivo no rack, continue a instalação fixando os cabos de rede e conectando a alimentação CA ao Azure FXT Edge Filer.

> [!div class="nextstepaction"]
> [Cabear portas de rede e fornecer energia](fxt-network-power.md)