---
title: Visão geral do Microsoft Azure Data Box Gateway | Microsoft Docs
description: Descreve o Azure Data Box Gateway, uma solução de virtualização que permite transferir dados para o Azure
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: overview
ms.date: 07/16/2019
ms.author: alkohli
ms.openlocfilehash: 1b749df7c5b3badbc6e7eccd885cb953ab3d0afa
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68277379"
---
# <a name="what-is-azure-data-box-gateway"></a>O que é o Azure Data Box Gateway?

O Azure Data Box Gateway é uma solução de armazenamento que lhe permite enviar dados diretamente para o Azure. Este artigo fornece uma visão geral da solução, dos benefícios, das principais funcionalidades e dos cenários do Azure Data Box Gateway em que é possível implantar esse dispositivo.

O Data Box Gateway é um dispositivo virtual baseado em uma máquina virtual provisionada em seu ambiente virtualizado ou hipervisor. O dispositivo virtual reside em seu local e você grava dados nele usando os protocolos NFS e SMB. Em seguida, o dispositivo transfere seus dados para o blob de blocos ou blob de páginas do Azure ou Arquivos do Azure.

## <a name="use-cases"></a>Casos de uso

O Data Box Gateway pode ser usado para transferir dados para a nuvem, como arquivamento na nuvem, recuperação de desastre ou caso haja a necessidade de processar seus dados em escala de nuvem. Veja a seguir os vários cenários em que o Data Box Gateway pode ser usado para a transferência de dados.

- **Arquivamento na nuvem** – Copie centenas de TBs de dados para o armazenamento do Azure usando o Data Box Gateway de uma maneira segura e eficiente. Os dados podem ser ingeridos uma vez ou continuamente para cenários de arquivamento.

- **Ingestão de dados contínua** – ingira dados continuamente no dispositivo para copiá-los para a nuvem, independentemente de seu tamanho. Conforme os dados são gravados no dispositivo de gateway, o dispositivo carrega os dados no Armazenamento do Azure.  

- **Transferência em massa inicial seguida por transferência incremental** – use o Data Box para a transferência em massa em um modo offline (semente inicial) e o Data Box Gateway para transferências incrementais (feed contínuo) na rede.

Para obter mais informações, acesse [casos de uso do Azure Data Box Gateway](data-box-gateway-use-cases.md).

## <a name="benefits"></a>Benefícios

O Data Box Gateway tem os seguintes benefícios:

- **Transferência de dados fácil** – Facilita a movimentação de dados para dentro e fora do Azure, tanto quanto trabalhar em um compartilhamento de rede local.  
- **Alto desempenho** – Acaba com a complicação do transporte de dados de rede com transferências de alto desempenho para e do Azure.
- **Acesso rápido e altas taxas de ingestão de dados durante o horário comercial** – o Data Box Gateway tem um cache local que você define como o tamanho da capacidade local quando o dispositivo virtual é provisionado. O tamanho do disco de dados deve ser especificado de acordo com os [requisitos mínimos do dispositivo virtual](data-box-gateway-system-requirements.md#specifications-for-the-virtual-device). O cache local oferece os seguintes benefícios:
    - O cache local permite a ingestão de dados a uma alta taxa. Quando a alta quantidade de dados é ingerida durante o horário comercial de pico, o cache pode manter os dados e carregá-los para a nuvem.
    - O cache local permite acesso de leitura rápido até um determinado limite. Até que o dispositivo esteja 50-60% cheio, todas as leituras dele são acessadas do cache, criando-as mais rapidamente. Depois que o espaço usado no dispositivo ultrapassa esse limite, o dispositivo começa a remover arquivos locais.
 
- **Uso de largura de banda limitado** – Os dados podem ser gravados no Azure, mesmo quando a rede tem uma restrição para limitar o uso durante o horário comercial de pico.  

## <a name="key-capabilities"></a>Principais recursos

O Data Box Gateway tem as seguintes funcionalidades:

|Recurso |DESCRIÇÃO  |
|---------|---------|
|Velocidade     | Largura de banda e transferência de dados totalmente automatizada e altamente otimizada.|
|Protocolos com suporte     | Suporte para os protocolos SMB e NFS padrão para ingestão de dados. <br> Para obter mais informações sobre as versões com suporte, acesse [Data Box Gateway system requirements](data-box-gateway-system-requirements.md) (Requisitos do sistema do Gateway do Data Box).|
|Acesso de dados     | Depois que os dados enviados pelo dispositivo estiverem na nuvem, eles poderão ser modificados acessando diretamente as APIs de nuvem.|
|Acesso rápido     | Cache local no dispositivo para acesso rápido dos arquivos usados mais recentemente.|
|Upload offline     | O modo desconectado é compatível com cenários de upload offline.|
|Atualização dedados     | Capacidade de atualizar arquivos locais com a versão mais recente da nuvem.|
|Criptografia    | Suporte ao BitLocker para criptografar dados localmente e proteger a transferência de dados para a nuvem por meio do *https*       |
|Resiliência     | Resiliência de rede interna        |


## <a name="specifications"></a>Especificações

O dispositivo virtual Data Box Gateway tem as seguintes especificações:

| Especificações                                          | DESCRIÇÃO              |
|---------------------------------------------------------|--------------------------|
| Processadores virtuais (núcleos)   | Mínimo de 4 |
| Memória  |Mínimo de 8 GB|
| Disponibilidade|Nó único|
| Discos|Disco do sistema operacional: 250 GB <br> Disco de dados: Mínimo de 2 TB, com provisionamento dinâmico e deve ser ativado por SSDs|
| Interfaces de rede |1 ou mais adaptadores de rede virtual|
| Protocolos de compartilhamento de arquivo nativo|SMB e NFS  |
| Segurança|Autenticação para desbloquear o acesso ao dispositivo e aos dados <br> Dados em trânsito criptografados usando a criptografia AES de 256 bits|
| Gerenciamento|Interface do usuário da Web local – configuração inicial, diagnóstico e gerenciamento de energia do dispositivo <br> Portal do Azure – gerenciamento diário de dispositivos de Data Box Gateway       |

## <a name="components"></a>Componentes

A solução Data Box Gateway é composta pelo recurso de Data Box Gateway, dispositivo virtual de Data Box Gateway e uma interface do usuário da Web local.

- **Dispositivo virtual de Data Box Gateway** – um dispositivo baseado em uma máquina virtual provisionada em seu ambiente virtualizado ou hipervisor e que permite que você envie dados para o Azure.
    
- **Recurso de Data Box Gateway** – um recurso no portal do Azure que permite gerenciar um dispositivo de Data Box Gateway de uma interface da Web que você pode acessar de diferentes localizações geográficas. Use o recurso Data Box Gateway para exibir e gerenciar dispositivos, compartilhamentos, usuários e alertas. Para obter mais informações, confira como [Gerenciar usando o portal do Azure](data-box-gateway-manage-shares.md).

- **IU da Web local do Data Box** – use a IU da Web local para executar diagnósticos, desligar e reiniciar o dispositivo, gerar um pacote de suporte ou contatar o Suporte da Microsoft para registrar uma solicitação de serviço. Para obter mais informações, confira como [Gerenciar usando a IU da Web local](data-box-gateway-manage-access-power-connectivity-mode.md).

## <a name="region-availability"></a>Disponibilidade de região

O dispositivo físico Data Box Gateway, o recurso do Azure e a conta de armazenamento de destino para a qual os dados são transferidos não precisam estar todos na mesma região.

- **Disponibilidade de recursos** – para obter uma lista de todas as regiões em que o recurso do Data Box Edge está disponível, vá para [Produtos do Azure disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=databox). O Data Box Gateway também pode ser implantado na Nuvem do Azure Governamental. Para obter mais informações, confira [O que é o Azure Governamental?](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome).

- **Contas de Armazenamento de destino** – as contas de armazenamento que armazenam os dados estão disponíveis em todas as regiões do Azure.

    As regiões em que as contas de armazenamento armazenam os dados do Data Box devem estar localizadas perto do local em que o dispositivo está localizado para render desempenho ideal. Uma conta de armazenamento localizada longe do dispositivo resulta em longas latências de desempenho mais lentos.


## <a name="next-steps"></a>Próximas etapas

- Examine os [requisitos do sistema do Data Box Gateway](data-box-gateway-system-requirements.md).
- Entenda os [limites do Data Box Gateway](data-box-gateway-limits.md).
- Implante o [Azure Data Box Gateway](data-box-gateway-deploy-prep.md) no portal do Azure.

