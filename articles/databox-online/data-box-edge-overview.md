---
title: Visão geral do Microsoft Azure Data Box Edge | Microsoft Docs
description: Descreve o Azure Data Box Edge, uma solução de armazenamento que usa um dispositivo físico para transferência baseada em rede para o Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 07/17/2019
ms.author: alkohli
ms.openlocfilehash: 69580f956b603423ef302353953a45ad5d00391e
ms.sourcegitcommit: 770b060438122f090ab90d81e3ff2f023455213b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/17/2019
ms.locfileid: "68305429"
---
# <a name="what-is-azure-data-box-edge"></a>O que é o Azure Data Box Edge? 

O Azure Data Box Edge é um dispositivo de computação de borda habilitado para IA com funcionalidades de transferência de dados de rede. Este artigo fornece uma visão geral da solução, dos benefícios, das principais funcionalidades e dos cenários do Data Box Edge em que é possível implantar esse dispositivo. 

O Data Box Edge é uma solução de hardware como serviço. A Microsoft envia a você um dispositivo gerenciado por nuvem com uma FPGA (matriz de porta programável no campo) interna que habilita inferência de IA acelerada e tem todos os recursos de um gateway de armazenamento. 

## <a name="use-cases"></a>Casos de uso

Aqui estão os vários cenários em que o Data Box Edge pode ser usado para inferências rápidas de ML (Machine Learning) na borda e pré-processamento de dados antes de enviá-los para o Azure.

- **Inferência com o Azure Machine Learning** – com o Data Box Edge, é possível executar modelos de ML para obter resultados rápidos que podem ser tratados antes que os dados sejam enviados para a nuvem. O conjunto de dados completo pode ser transferido para continuar a treinar novamente e melhorar os modelos de ML. Para obter mais informações sobre como usar os Modelos de Aceleração de Hardware do Azure ML no dispositivo Data Box Edge, confira [Implantar Modelos de Aceleração de Hardware do Azure ML no Data Box Edge](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-fpga-web-service#deploy-to-a-local-edge-server).

- **Pré-processar dados** – transforme dados antes de enviá-los para o Azure para criar um conjunto de dados mais acionável. O pré-processamento pode ser usado para: 

    - Agregar dados.
    - Modifique os dados, por exemplo, para remover dados pessoais.
    - Subconjunto dados para otimizar o armazenamento e a largura de banda ou para análise posterior.
    - Analisar e reagir a Eventos de IoT. 

- **Transferir dados pela rede para o Azure** – Use o Data Box Edge para transferir dados de maneira rápida e fácil para o Azure para habilitar ainda mais computação e análise ou para fins de arquivamento. 


## <a name="key-capabilities"></a>Principais recursos

O Data Box Edge tem as seguintes funcionalidades:

|Recurso |DESCRIÇÃO  |
|---------|---------|
|Inferência de IA acelerada| Habilitada pela FPGA interna.|
|Computação       |Permite a análise, o processamento e a filtragem de dados.|
|Alto desempenho | Computação de alto desempenho e transferências de dados.|
|Acesso de dados     | Acesso direto a dados dos Blobs de Armazenamento do Microsoft Azure e Arquivos do Azure usando APIs de nuvem para processamento adicional de dados na nuvem. O cache local no dispositivo é usado para acesso rápido dos arquivos usados mais recentemente.|
|Gerenciado pela nuvem     |O dispositivo e o serviço são gerenciados por meio do portal do Azure.  |
|Upload offline     | O modo desconectado é compatível com cenários de upload offline.|
|Protocolos com suporte     | Suporte para os protocolos SMB e NFS padrão para ingestão de dados. <br> Para obter mais informações sobre as versões com suporte, acesse [Data Box Edge system requirements](data-box-edge-system-requirements.md) (Requisitos do sistema do Data Box Edge).|
|Atualização dedados     | Capacidade de atualizar arquivos locais com a versão mais recente da nuvem.|
|Criptografia    | Suporte ao BitLocker para criptografar dados localmente e proteger a transferência de dados para a nuvem por meio do *https*.|
|Limitação de largura de banda| Limitação para restringir o uso de largura de banda durante horários de pico.|


## <a name="components"></a>Componentes

A solução Data Box Edge é composta pelo recurso do Data Box Edge, dispositivo físico Data Box Edge e uma interface do usuário da Web local.

* **Dispositivo físico Data Box Edge** – Um servidor montado em rack de 1U fornecido pela Microsoft que pode ser configurado para enviar dados ao Azure. 
    
* **Recurso do Data Box Edge** – um recurso no portal do Azure que permite a você gerenciar um dispositivo Data Box Edge em uma interface da Web que pode ser acessada em diferentes localizações geográficas. Use o recurso do Data Box Edge para criar e gerenciar recursos, exibir e gerenciar dispositivos e alertas e gerenciar compartilhamentos.  

    <!--![The Data Box Edge service in Azure portal](media/data-box-overview/data-box-Edge-service1.png)-->

    Para saber mais, acesse [Criar uma ordem para seu dispositivo Data Box Edge](data-box-edge-deploy-prep.md#create-a-new-resource).

* **Interface do usuário da Web local do Data Box** – Use a interface do usuário da Web local para executar diagnósticos, desligar e reiniciar o dispositivo Data Box Edge, exibir logs de cópia e contatar o Suporte da Microsoft para apresentar uma solicitação de serviço.

    <!--![The Data Box Edge local web UI](media/data-box-Edge-overview/data-box-Edge-local-web-ui.png)-->

    Para obter informações sobre como usar a interface do usuário baseada na Web, acesse [Use the web-based UI to administer your Data Box](data-box-edge-manage-access-power-connectivity-mode.md) (Usar a interface do usuário baseada na Web para administrar seu Data Box).


## <a name="region-availability"></a>Disponibilidade de região

O dispositivo físico Data Box Edge, o recurso do Azure e a conta de armazenamento de destino para a qual os dados são transferidos não precisam estar na mesma região.

- **Disponibilidade de recursos** – para obter uma lista de todas as regiões em que o recurso do Data Box Edge está disponível, vá para [Produtos do Azure disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). O Data Box Edge também pode ser implantado na Nuvem do Azure Governamental. Para obter mais informações, confira [O que é o Azure Governamental?](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome).
    
- **Contas de Armazenamento de destino** – as contas de armazenamento que armazenam os dados estão disponíveis em todas as regiões do Azure. As regiões em que as contas de armazenamento armazenam dados do Data Box Edge devem estar localizadas perto do local em que o dispositivo está para apresentar o desempenho ideal. Uma conta de armazenamento localizada longe do dispositivo resulta em longas latências de desempenho mais lentos. 


## <a name="next-steps"></a>Próximas etapas

- Examine os [Requisitos de sistema do Data Box Edge](data-box-edge-system-requirements.md).
- Entenda os [limites do Data Box Edge](data-box-edge-limits.md).
- Implante o [Azure Data Box Edge](data-box-edge-deploy-prep.md) no portal do Azure.




