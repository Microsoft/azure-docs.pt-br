---
title: Visão geral do Microsoft Azure Stack Edge Pro R | Microsoft Docs
description: Descreve os dispositivos Azure Stack Edge Pro R, uma solução de armazenamento para aplicativos militares que usa um dispositivo físico para transferência baseada em rede para o Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 40fa1bc776c5cd457e57cf170f629b6cf92800fe
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104585988"
---
# <a name="what-is-the-azure-stack-edge-pro-r"></a>O que é o Azure Stack Edge Pro R?

O Azure Stack Edge Pro R é um dispositivo de computação de borda robusto projetado para uso em ambientes adversos. O Azure Stack Edge Pro R é entregue como uma solução de hardware como serviço. A Microsoft envia a você um dispositivo gerenciado em nuvem que funciona como um gateway de armazenamento de rede e tem uma GPU (unidade de processamento gráfico) interna que permite a inferência de IA acelerada.

Este artigo fornece uma visão geral da solução Azure Stack Edge Pro R, das principais funcionalidades dela e dos cenários nos quais é possível implantar esse dispositivo.


## <a name="key-capabilities"></a>Principais recursos

O Azure Stack Edge Pro R tem as seguintes funcionalidades:

|Funcionalidade |Descrição  |
|---------|---------|
|Hardware robusto| Hardware robusto de classe de servidor projetado para ambientes adversos. Dispositivo contido em um estojo de transporte portátil. |
|Gerenciado pela nuvem     |O dispositivo e o serviço são gerenciados por meio do portal do Azure.|
|Cargas de trabalho de computação de borda   |Permite a análise, o processamento e a filtragem de dados. Dá suporte a VMs e cargas de trabalho em contêineres.|
|Inferência de IA acelerada| Habilitado por uma GPU do Nvidia T4.|
|Acesso de dados     | Acesso direto a dados dos Blobs de Armazenamento do Microsoft Azure e Arquivos do Azure usando APIs de nuvem para processamento adicional de dados na nuvem. O cache local no dispositivo é usado para acesso rápido dos arquivos usados mais recentemente.|
|Modo desconectado| Opcionalmente, o dispositivo e o serviço podem ser gerenciados por meio do Azure Stack Hub. Implante, execute e gerencie aplicativos no modo offline. <br> O modo desconectado é compatível com cenários de upload offline.|
|Protocolos de transferência de arquivo com suporte     |Suporte aos protocolos SMB, NFS e REST padrão para ingestão de dados. <br> Para obter mais informações sobre as versões com suporte, acesse [Requisitos do sistema do Azure Stack Edge Pro R](azure-stack-edge-gpu-system-requirements.md).|
|Atualização dedados     | Capacidade de atualizar arquivos locais com a versão mais recente da nuvem.|
|Criptografia dupla    | O uso de unidades de criptografia automática fornece a primeira camada de criptografia. A VPN fornece a segunda camada de criptografia. Suporte ao BitLocker para criptografar dados localmente e proteger a transferência de dados para a nuvem por meio do *https*.|
|Limitação de largura de banda| Limitação para restringir o uso de largura de banda durante horários de pico.|

<!--|Scale out file server| Available as 1-node and 4-node cluster configurations|-->

## <a name="use-cases"></a>Casos de uso

Veja a seguir os vários cenários nos quais o Azure Stack Edge Pro R pode ser usado para inferências rápidas de ML (Machine Learning) na borda e pré-processamento de dados, antes do envio para o Azure.

- **Inferência com o Azure Machine Learning** – Com o Azure Stack Edge Pro R, é possível executar modelos de ML para obter resultados rápidos que podem ser tratados antes que os dados sejam enviados para a nuvem. O conjunto de dados completo pode ser transferido para continuar a treinar novamente e melhorar os modelos de ML. Para obter mais informações sobre como usar os modelos de aceleração de hardware do Azure ML no dispositivo Azure Stack Edge Pro R, confira [Implantar modelos de aceleração de hardware do Azure ML no Azure Stack Edge Pro R](../machine-learning/how-to-deploy-fpga-web-service.md#deploy-to-a-local-edge-server).

- **Pré-processar dados** – transforme dados antes de enviá-los para o Azure para criar um conjunto de dados mais acionável. O pré-processamento pode ser usado para:

    - Agregar dados.
    - Modifique os dados, por exemplo, para remover dados pessoais.
    - Subconjunto dados para otimizar o armazenamento e a largura de banda ou para análise posterior.
    - Analisar e reagir a Eventos de IoT.

- **Transferir dados pela rede para o Azure** – Use o Azure Stack Edge Pro R para transferir dados de maneira fácil e rápida para o Azure para habilitar ainda mais a computação e a análise ou para fins de arquivamento.

## <a name="components"></a>Componentes

A solução Azure Stack Edge Pro R inclui um recurso do Azure Stack Edge, o dispositivo físico robusto Azure Stack Edge Pro R e uma IU da Web local.

- **Dispositivo físico Azure Stack Edge Pro R** – um dispositivo de computação e armazenamento de um nó contido em um estojo de transporte robusto. Um no-break opcional também está disponível.

    ![O dispositivo Azure Stack Edge Pro R de um nó](media/azure-stack-edge-pro-r-overview/device-image-1.png)

- **Recurso do Azure Stack Edge** – Um recurso no portal do Azure com o qual você pode gerenciar um dispositivo robusto Azure Stack Edge Pro R em uma interface da Web que pode ser acessada em diferentes localizações geográficas. Use o recurso Azure Stack Edge para criar e gerenciar recursos, exibir e gerenciar dispositivos e alertas, e gerenciar compartilhamentos.  

- **IU da Web local do Azure Stack Edge Pro R** – uma interface do usuário local baseada em navegador em seu dispositivo Azure Stack Edge Pro R destinada principalmente à configuração inicial do dispositivo. Use a IU da Web local também para executar diagnósticos, desligar e reiniciar o dispositivo Azure Stack Edge Pro, exibir logs de cópia e contatar o Suporte da Microsoft para apresentar uma solicitação de serviço.


## <a name="region-availability"></a>Disponibilidade de região

O dispositivo físico Azure Stack Edge Pro R, o recurso do Azure e a conta de armazenamento de destino para a qual os dados são transferidos não precisam estar na mesma região.

- **Disponibilidade de recursos** – Para obter uma lista de todas as regiões em que o recurso Azure Stack Edge está disponível, vá para [Produtos do Microsoft Azure disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). 

- **Disponibilidade do dispositivo** – para obter uma lista de todos os países em que o dispositivo do Azure Stack Edge Pro R está disponível, acesse a seção **Disponibilidade** na guia **Azure Stack Edge Pro R** para [Preço do Azure Stack Edge Pro R](https://azure.microsoft.com/pricing/details/azure-stack/edge/#azureStackEdgeProR).

- **Contas de Armazenamento de destino** – as contas de armazenamento que armazenam os dados estão disponíveis em todas as regiões do Azure. Para obter o desempenho ideal, as regiões nas quais as contas de armazenamento guardam dados do Azure Stack Edge Pro R devem estar próximas do local em que o dispositivo está localizado. Uma conta de armazenamento localizada longe do dispositivo resulta em longas latências de desempenho mais lentos.

O serviço Azure Stack Edge é um serviço não regional. Para obter mais informações, confira [Regiões e Zonas de Disponibilidade no Azure](https://docs.microsoft.com/azure/availability-zones/az-overview). O serviço Azure Stack Edge não tem dependência de nenhuma região específica do Azure, tornando-o resiliente a interrupções em todas as zonas e regiões.

## <a name="next-steps"></a>Próximas etapas

- Examinar os [requisitos do sistema do Azure Stack Edge Pro R](azure-stack-edge-gpu-system-requirements.md).
<!--- Understand the [Azure Stack Edge Pro R limits](azure-stack-edge-limits.md).-->
