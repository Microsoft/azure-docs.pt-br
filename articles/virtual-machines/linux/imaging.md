---
title: Visão geral da criação de imagens do Linux para o Azure
description: Como trazer suas imagens de VM do Linux ou criar imagens para uso no Azure.
author: danielsollondon
ms.service: virtual-machines
ms.subservice: imaging
ms.collection: linux
ms.topic: overview
ms.workload: infrastructure
ms.date: 06/22/2020
ms.author: danis
ms.reviewer: cynthn
ms.openlocfilehash: a77c2bc69418b821933c0b62674500f7a32e40a5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102565199"
---
# <a name="bringing-and-creating-linux-images-in-azure"></a>Trazer e criar imagens do Linux no Azure

Esta visão geral aborda os conceitos básicos sobre a geração de imagens e como criar e usar com êxito imagens do Linux no Azure. Antes de trazer uma imagem personalizada para o Azure, você precisa estar ciente dos tipos e das opções disponíveis para você.

Este artigo percorrerá os pontos e requisitos de decisão de imagem e explicará os principais conceitos, para que você possa acompanhar e ser capaz de criar as próprias imagens personalizadas de acordo com sua especificação.

## <a name="difference-between-managed-disks-and-images"></a>Diferença entre discos gerenciados e imagens


O Azure permite que você coloque um VHD na plataforma ou use como um [Disco Gerenciado](../faq-for-disks.md#managed-disks) ou como uma origem para uma imagem. 

Os discos gerenciados do Azure são VHDs únicos. É possível usar um VHD existente e criar um disco gerenciado com base nele ou criar um disco gerenciado vazio do zero. É possível criar VMs com base em discos gerenciados anexando o disco à VM, mas você só pode usar um VHD com uma VM. Não é possível modificar nenhuma propriedade do sistema operacional; o Azure tentará ligar a VM e inicializar usando esse disco. 

As imagens do Azure podem ser compostas por vários discos do sistema operacional e de dados. Quando você usa uma imagem gerenciada para criar uma VM, a plataforma faz uma cópia da imagem e a usa para criar a VM, de modo que a imagem gerenciada dá suporte à reutilização da mesma imagem para várias VMs. O Azure também fornece funcionalidades avançadas de gerenciamento para imagens, como replicação global, e controle de versão por meio da [Galeria de Imagens Compartilhadas](../shared-image-galleries.md). 



## <a name="generalized-and-specialized"></a>Generalizados e especializados

O Azure oferece dois tipos de imagem principais: generalizados e especializados. Os termos “generalizados” e “especializados” são termos originalmente do Windows, que foram migrados para o Azure. Esses tipos definem como a plataforma tratará a VM quando ela for ativada. Os dois tipos têm vantagens e desvantagens e pré-requisitos. Antes de começar, é necessário saber qual tipo de imagem será necessário. Veja abaixo um resumo dos cenários e do tipo que você precisará escolher:

| Cenário      | Tipo de Imagem  | Opções de armazenamento |
| ------------- |:-------------:| :-------------:| 
| Criar uma imagem que possa ser configurada para uso por várias VMs e eu possa definir o nome do host, adicionar um usuário administrador e executar outras tarefas durante a primeira inicialização. | Generalizada | Galeria de Imagens Compartilhadas ou imagens gerenciadas autônomas |
| Criar uma imagem com base em um instantâneo de VM ou um backup | Especializada |Galeria de Imagens Compartilhadas ou um disco gerenciado |
| Crie rapidamente uma imagem que não precise de nenhuma configuração para criar várias VMs |Especializada |Galeria de imagens compartilhadas |


### <a name="generalized-images"></a>Imagens generalizada

Uma imagem generalizada é uma imagem que requer que a instalação seja concluída na primeira inicialização. Por exemplo, na primeira inicialização, você define o nome do host, o usuário administrador e outras configurações específicas da VM. Isso é útil quando você deseja que a imagem seja reutilizada várias vezes e quando você deseja passar parâmetros durante a criação. Se a imagem generalizada contiver o agente do Azure, o agente processará os parâmetros e informará à plataforma que a configuração inicial foi concluída. Esse processo chama-se [provisionamento](./provisioning.md). 

O provisionamento requer que um provisionador esteja incluído na imagem. Há dois provisionadores:
- [Agente Linux do Azure](../extensions/agent-linux.md)
- [cloud-init](./using-cloud-init.md)

Estes são [pré-requisitos](./create-upload-generic.md) para criar uma imagem.


### <a name="specialized-images"></a>Imagens especializadas
Essas são imagens que estão completamente configuradas e não exigem parâmetros especiais e de VM; a plataforma só ativará a VM e você precisará que o identificador seja único dentro da VM, como definir um nome de host, para evitar conflitos de DNS na mesma VNET. 

No entanto, os agentes de provisionamento não são necessários para essas imagens, mas talvez seja interessante ter funcionalidades de manipulação de extensão. É possível instalar o Agente do Linux, mas desabilitar a opção de provisionamento. Embora você não precise de um agente de provisionamento, a imagem deve atender aos [pré-requisitos](./create-upload-generic.md) para Imagens do Azure.


## <a name="image-storage-options"></a>Opções de armazenamento de imagens
Ao trazer sua imagem do Linux, você tem duas opções:

- Imagens gerenciadas para criação de VM simples em um ambiente de desenvolvimento e teste.
- [Galeria de Imagens Compartilhadas](../shared-image-galleries.md) para criar e compartilhar imagens em escala.


### <a name="managed-images"></a>Imagens gerenciadas

As imagens gerenciadas podem ser usadas para criar várias VMs, mas elas têm muitas limitações. As imagens gerenciadas só podem ser criadas com base em uma origem generalizada (VM ou VHD). Elas só podem ser usadas para criar VMs na mesma região e não podem ser compartilhados entre assinaturas e locatários.

As imagens gerenciadas podem ser usadas para ambientes de desenvolvimento e teste, em que são necessárias algumas imagens generalizadas simples para usar em uma região e assinatura. 

### <a name="azure-shared-image-gallery-sig"></a>SIG (Galeria de Imagens Compartilhadas) do Azure

As [Galerias de Imagens Compartilhadas](../shared-image-galleries.md) são recomendadas para criar, gerenciar e compartilhar imagens em escala. A galeria de imagens compartilhadas ajuda você a criar a estrutura e a organização em torno das suas imagens gerenciadas.  

- Suporte para imagens generalizadas e especializadas.
- Suporte para imagens da geração 1 e 2.
- Replicação global de imagens.
- Agrupamento e controle de versão de imagens para facilitar o gerenciamento.
- Imagens altamente disponíveis com ZRS (Armazenamento com Redundância de Zona) em regiões que dão suporte a Zonas de Disponibilidade. O ZRS oferece maior resiliência contra falhas em zonas.
- Compartilhamento entre assinaturas e até mesmo entre locatários do AD (Active Directory) usando o Azure RBAC.
- Dimensionamento das suas implantações com réplicas de imagem em cada região.

Em um alto nível, você cria um SIG e ele é composto por:
- Definições de imagem – são contêineres que armazenam grupos de imagens.
- Versões de imagem – essas são as imagens reais



## <a name="hyper-v-generation"></a>Geração do Hyper-V

O Azure dá suporte ao Hyper-V geração 1 (Gen1) e à geração 2 (Gen2); o Gen2 é a última geração e oferece funcionalidade adicional em relação ao Gen1. Por exemplo: maior memória, Intel SGX (Intel com Software Guard Extensions) e vPMEM (memória persistente virtualizada). As VMs de geração 2 em execução no local têm alguns recursos que ainda não têm suporte no Azure. Para obter mais informações, confira a seção Recursos e funcionalidades. Para obter mais informações, veja este [artigo](../generation-2.md). Crie imagens Gen2 se você precisar de funcionalidade adicional.

Se você ainda precisar criar sua imagem, verifique se ela atende aos [pré-requisitos de imagem](./create-upload-generic.md) e carregue no Azure. Requisitos específicos de distribuição:


- [Distribuições com base em CentOS](create-upload-centos.md)
- [Debian Linux](debian-create-upload-vhd.md)
- [Flatcar Container Linux](flatcar-create-upload-vhd.md)
- [Oracle Linux](oracle-create-upload-vhd.md)
- [Red Hat Enterprise Linux](redhat-create-upload-vhd.md)
- [SLES e openSUSE](suse-create-upload-vhd.md)
- [Ubuntu](create-upload-ubuntu.md)


## <a name="next-steps"></a>Próximas etapas

Saiba como criar uma [Galeria de Imagens Compartilhadas](tutorial-custom-images.md).