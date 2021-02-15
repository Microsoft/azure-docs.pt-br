---
title: Visão geral do Microsoft Azure Stack Edge Pro | Microsoft Docs
description: Descreve o Azure Stack Edge Pro, uma solução de armazenamento que usa um dispositivo físico para transferência baseada em rede para o Microsoft Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 09/09/2020
ms.author: alkohli
ms.openlocfilehash: 51469c23574f55c102f0c6fb98e5aa5178b59a4a
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96455743"
---
# <a name="what-is-azure-stack-edge-pro-with-fpga"></a>O que é o Azure Stack Edge Pro com FPGA?

[!INCLUDE [data-box-edge-gateway-rename-note](../../includes/data-box-edge-gateway-rename-note.md)]

O Azure Stack Edge Pro com FPGA é um dispositivo de computação de borda habilitado para IA com funcionalidades de transferência de dados de rede. Este artigo fornece uma visão geral da solução, das vantagens, das principais funcionalidades e dos cenários do Azure Stack Edge Pro com FPGA em que é possível implantar esse dispositivo.

O Azure Stack Edge Pro com FPGA é uma solução de hardware como serviço. A Microsoft enviará a você um dispositivo gerenciado em nuvem com uma FPGA (matriz de porta programável no campo) interna que habilita a inferência de IA acelerada e tem todas as funcionalidades de um gateway de armazenamento. 

## <a name="use-cases"></a>Casos de uso

Aqui estão os vários cenários em que o Azure Stack Edge Pro pode ser usado para inferências rápidas de ML (Machine Learning) na borda e pré-processamento de dados, antes do envio para o Microsoft Azure.

- **Inferência com o Azure Machine Learning** – com o Azure Stack Edge Pro, é possível executar modelos de ML para obter resultados rápidos que podem ser tratados, antes que os dados sejam enviados para a nuvem. O conjunto de dados completo pode ser transferido para continuar a treinar novamente e melhorar os modelos de ML. Para obter mais informações sobre como usar os modelos de aceleração de hardware do Azure ML no dispositivo Azure Stack Edge Pro, confira [Implantar modelos de aceleração de hardware do Azure ML no Azure Stack Edge Pro](../machine-learning/how-to-deploy-fpga-web-service.md#deploy-to-a-local-edge-server).

- **Pré-processar dados** – transforme dados antes de enviá-los para o Azure para criar um conjunto de dados mais acionável. O pré-processamento pode ser usado para: 

    - Agregar dados.
    - Modifique os dados, por exemplo, para remover dados pessoais.
    - Subconjunto dados para otimizar o armazenamento e a largura de banda ou para análise posterior.
    - Analisar e reagir a Eventos de IoT. 

- **Transferir dados pela rede para o Microsoft Azure** – Use o Azure Stack Edge Pro para transferir dados de maneira fácil e rápida para o Microsoft Azure para habilitar ainda mais computação e análise ou para fins de arquivamento. 

## <a name="key-capabilities"></a>Principais recursos

O Azure Stack Edge Pro tem as seguintes funcionalidades:

|Recurso |Descrição  |
|---------|---------|
|Inferência de IA acelerada| Habilitada pela FPGA interna.|
|Computação       |Permite a análise, o processamento e a filtragem de dados.|
|Alto desempenho | Computação de alto desempenho e transferências de dados.|
|Acesso de dados     | Acesso direto a dados dos Blobs de Armazenamento do Microsoft Azure e Arquivos do Azure usando APIs de nuvem para processamento adicional de dados na nuvem. O cache local no dispositivo é usado para acesso rápido dos arquivos usados mais recentemente.|
|Gerenciado pela nuvem     |O dispositivo e o serviço são gerenciados por meio do portal do Azure.  |
|Upload offline     | O modo desconectado é compatível com cenários de upload offline.|
|Protocolos com suporte     | Suporte para os protocolos SMB e NFS padrão para ingestão de dados. <br> Para obter mais informações sobre as versões compatíveis, acesse [Requisitos do sistema do Azure Stack Edge Pro](azure-stack-edge-system-requirements.md).|
|Atualização dedados     | Capacidade de atualizar arquivos locais com a versão mais recente da nuvem.|
|Criptografia    | Suporte ao BitLocker para criptografar dados localmente e proteger a transferência de dados para a nuvem por meio do *https*.|
|Limitação de largura de banda| Limitação para restringir o uso de largura de banda durante horários de pico.|
|ExpressRoute | Adição de segurança por meio do ExpressRoute. Use a configuração de emparelhamento, na qual o tráfego de dispositivos locais para os pontos de extremidade de armazenamento em nuvem viaja pelo ExpressRoute. Para obter mais informações, confira [Visão geral do ExpressRoute](../expressroute/expressroute-introduction.md).

## <a name="components"></a>Componentes

A solução Azure Stack Edge Pro inclui o recurso Azure Stack Edge, o dispositivo físico Azure Stack Edge Pro e uma IU da Web local.

* **Dispositivo físico Azure Stack Edge Pro** – Um servidor montado em rack de 1U fornecido pela Microsoft que pode ser configurado para enviar dados ao Azure.
    
* **Recurso Azure Stack Edge** – Um recurso no portal do Azure com o qual você pode gerenciar um dispositivo Azure Stack Edge Pro em uma interface da Web que pode ser acessada em diferentes localizações geográficas. Use o recurso Azure Stack Edge para criar e gerenciar recursos, exibir e gerenciar dispositivos e alertas, e gerenciar compartilhamentos.  

    <!--![The Azure Stack Edge service in Azure portal](media/data-box-overview/data-box-Edge-service1.png)-->

    Para saber mais, vá para o tópico [Criar uma ordem para o dispositivo Azure Stack Edge Pro](azure-stack-edge-deploy-prep.md#create-a-new-resource).

* **IU da Web local do Azure Stack Edge Pro** – Use a interface do usuário da Web local para executar diagnósticos, desligar e reiniciar o dispositivo Azure Stack Edge Pro, exibir logs de cópia e contatar o Suporte da Microsoft para apresentar uma solicitação de serviço.

    <!--![The Azure Stack Edge Pro local web UI](media/data-box-Edge-overview/data-box-Edge-local-web-ui.png)-->

    Para saber mais sobre como usar a interface do usuário baseada na Web, veja o tópico [Usar a interface do usuário baseada na Web para administrar o Azure Stack Edge Pro](azure-stack-edge-manage-access-power-connectivity-mode.md).

## <a name="region-availability"></a>Disponibilidade de região

O dispositivo físico Azure Stack Edge Pro, o recurso do Microsoft Azure e a conta de armazenamento de destino para a qual os dados são transferidos não precisam estar na mesma região.

- **Disponibilidade de recursos**: para obter uma lista de todas as regiões em que o recurso Azure Stack Edge está disponível, confira [Produtos do Azure disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). O Azure Stack Edge Pro também pode ser implantado na Nuvem do Azure Government. Para obter mais informações, confira [O que é o Azure Governamental?](../azure-government/documentation-government-welcome.md).
    
- **Contas de Armazenamento de destino** – as contas de armazenamento que armazenam os dados estão disponíveis em todas as regiões do Azure. Para obter o desempenho ideal, as regiões em que as contas de armazenamento armazenam dados do Azure Stack Edge Pro devem estar próximas de onde o dispositivo está localizado. Uma conta de armazenamento localizada longe do dispositivo resulta em longas latências de desempenho mais lentos.

## <a name="next-steps"></a>Próximas etapas

- Examinar os [requisitos do sistema do Azure Stack Edge Pro](azure-stack-edge-system-requirements.md).
- Entender os [limites do Azure Stack Edge Pro](azure-stack-edge-limits.md).
- Implantar o [Azure Stack Edge Pro](azure-stack-edge-deploy-prep.md) no portal do Azure.