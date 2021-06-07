---
title: Introdução à Atualização de Dispositivo para o Hub IoT do Azure | Microsoft Docs
description: A Atualização de Dispositivo para o Hub IoT é um serviço que permite que você implante as atualizações OTA (por satélite) nos dispositivos IoT.
author: vimeht
ms.author: vimeht
ms.date: 2/11/2021
ms.topic: overview
ms.service: iot-hub-device-update
ms.openlocfilehash: 95cecd66f4d0164594eb4e8da9efe765b5833e5e
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106120768"
---
# <a name="device-update-for-iot-hub-preview-overview"></a>Visão geral da Atualização de Dispositivo para o Hub IoT (versão prévia)

A Atualização de Dispositivo para o Hub IoT é um serviço que permite que você implante as atualizações OTA (por satélite) nos dispositivos IoT.

À medida que as organizações procuram viabilizar ainda mais a produtividade e a eficiência operacional, as soluções de Internet das Coisas (IoT) continuam a ser cada vez mais adotadas. Isso torna imprescindível que os dispositivos que compõem essas soluções sejam criados em uma base de confiabilidade e segurança e sejam fáceis de se conectar e gerenciar em grande escala. A Atualização de Dispositivo para o Hub IoT é uma plataforma de ponta a ponta que os clientes podem usar para publicar, distribuir e gerenciar atualizações por satélite para tudo, desde pequenos sensores até dispositivos de nível de gateway. 

Para entender os benefícios completos da transformação digital habilitada para IoT, os clientes precisam dessa capacidade de operar, manter e atualizar dispositivos em escala. Conheça os benefícios da implementação da Atualização de Dispositivo para o Hub IoT, os quais incluem a capacidade de responder rapidamente às ameaças de segurança e implantar novos recursos para alcançar os objetivos comerciais, sem incorrer em custos adicionais de desenvolvimento e manutenção da criação de suas próprias plataformas de atualização.

## <a name="support-for-a-wide-range-of-iot-devices"></a>Suporte para uma ampla gama de dispositivos IoT


A Atualização de Dispositivo para o Hub IoT foi desenvolvida para oferecer implantação de atualização otimizada e operações simplificadas por meio da integração com o [Hub IOT do Azure](https://azure.microsoft.com/en-us/services/iot-hub/). Essa integração facilita a adoção da Atualização de Dispositivo em qualquer solução existente. Fornece uma solução hospedada na nuvem para conectar virtualmente qualquer dispositivo. A Atualização de Dispositivo dá suporte a uma ampla gama de sistemas operacionais de IoT, incluindo Linux e [Azure RTOS](https://azure.microsoft.com/en-us/services/rtos/) (sistema operacional em tempo real), além de ser extensível por meio de software livre. Estamos desenvolvendo a Atualização de Dispositivo para ofertas do Hub IoT em conjunto com nossos parceiros de semicondutor, incluindo a STMicroelectronics, a NXP, a Renesas e a Microchip. Confira os [exemplos](https://github.com/azure-rtos/samples/tree/PublicPreview/ADU) dos principais quadros de avaliação de semicondutores, que incluem os guias de introdução para saber como configurar, criar e implantar as atualizações OTA (over-the-air) para dispositivos da classe MCU. 

Um binário do Simulador de Agente da Atualização de dispositivo e as imagens Yocto de referência do Raspberry Pi são fornecidos.
A Atualização de Dispositivo para o Hub IoT também dá suporte à Atualização de Dispositivos Azure IoT Edge. Um Agente de Atualização de Dispositivo é fornecido para a plataforma Ubuntu Server 18.04 AMD64. A Atualização de Dispositivo para o Hub IoT também fornece código aberto, se você não estiver executando uma das plataformas acima. Você pode portar o agente para a distribuição que está executando.

A Atualização de Dispositivo funciona com o IoT Plug and Play e pode gerenciar qualquer dispositivo que dê suporte às interfaces PnP necessárias. Para obter mais informações, confira [Atualização de Dispositivo para o Hub IoT e IoT Plug and Play](device-update-plug-and-play.md).

## <a name="support-for-a-wide-range-of-update-artifacts"></a>Suporte para uma ampla gama de artefatos de atualização

A Atualização de Dispositivo para o Hub IoT é compatível com duas formas de atualizações – baseadas em imagem e em pacote.

As atualizações baseadas em pacote são atualizações de destino que alteram apenas um componente ou aplicativo específico no dispositivo. Isso leva a um menor consumo de largura de banda e ajuda a reduzir o tempo para baixar e instalar a atualização. As atualizações de pacote geralmente permitem menos tempo de inatividade dos dispositivos ao aplicar uma atualização e evita a sobrecarga de criação de imagens.

As atualizações de imagem fornecem um nível mais alto de confiança no estado final do dispositivo. Normalmente, é mais fácil replicar os resultados de uma atualização de imagem entre um ambiente de pré-produção e um ambiente de produção, já que não são apresentados os mesmos desafios que os pacotes e suas dependências.
Devido à natureza atômica, também é possível adotar facilmente um modelo de failover A/B.

Não há resposta correta e você pode escolher de maneira diferente com base nos casos de uso específicos. A Atualização de Dispositivo para o Hub IoT dá suporte à forma de atualização de imagem e pacote, permitindo que você escolha o modelo de atualização correto para o ambiente do dispositivo.

## <a name="flexible-features-for-updating-devices"></a>Recursos flexíveis para a atualização de dispositivos

A Atualização de Dispositivo para os recursos do Hub IoT fornece uma experiência eficaz e flexível, incluindo:

* UX de gerenciamento de atualizações integrado com o Hub IoT do Azure
* Distribuição de atualização gradativa por meio de controles de agrupamento de dispositivos e de agendamento de atualizações
* APIs programáticas para viabilizar as experiências de automação e portal personalizado
* Visão geral das exibições de conformidade e status de atualizações entre as frotas de dispositivos heterogêneos
* Suporte para atualizações de dispositivos resilientes (A/B) para fornecer reversão perfeita
* Controles de acesso baseado em função e assinatura disponíveis por meio do portal do Azure.com
* Cache de conteúdo local e suporte de borda aninhada para viabilizar a atualização de dispositivos desconectados na nuvem
* Ferramentas detalhadas de relatório e gerenciamento de atualizações 

Com os controles de gerenciamento e implantação da Atualização de Dispositivo para o Hub IoT, os usuários podem maximizar a produtividade e economizar um tempo valioso. A Atualização de Dispositivo para o Hub IoT inclui a capacidade de agrupar dispositivos e especificar para quais dispositivos uma atualização deve ser implantada. Os usuários também podem exibir o status das implantações de atualização e verificar se cada dispositivo aplicou as atualizações com sucesso.

Quando ocorre uma falha de atualização, a Atualização de Dispositivo para o Hub IoT também permite que os usuários identifiquem os dispositivos que não aplicaram a atualização, além de conferir os detalhes relacionados à falha. A capacidade de identificar quais dispositivos não foram atualizados significa a economia de inúmeras horas de trabalho manual tentando identificar a origem.

### <a name="best-in-class-security-at-global-scale"></a>A melhor segurança da categoria em escala global

O Microsoft Azure dá suporte a mais de um bilhão de dispositivos IoT em todo o mundo – um número que está crescendo rapidamente dia após dia. A Atualização de Dispositivo para o Hub IoT se baseia nessa experiência e na confiabilidade comprovada demonstrada pela plataforma Windows Update, para que os dispositivos possam ser atualizados continuamente em uma escala global.

A Atualização de Dispositivo para o Hub IoT usa uma segurança abrangente da nuvem para a borda desenvolvida para o Microsoft Azure para que os clientes não precisem perder tempo descobrindo como criá-la do zero.


## <a name="device-update-workflows"></a>Fluxos de trabalho da Atualização de Dispositivo

A funcionalidade de Atualização de Dispositivo pode ser dividida em três áreas: Integração do Agente, Importação e Gerenciamento.

### <a name="device-update-agent"></a>Agente de Atualização de Dispositivo

Quando um comando de atualização é recebido em um dispositivo, ele executa a fase solicitada de atualização (Download, Instalação e Aplicação). Durante cada fase, o status é retornado para a Atualização de Dispositivo por meio do Hub IoT para que você possa exibir o status atual de uma implantação. Se não houver atualizações em andamento, o status retornará como "ocioso". Uma implantação pode ser cancelada a qualquer momento.

:::image type="content" source="media/understand-device-update/client-agent-workflow.png" alt-text="Diagrama do fluxo de trabalho do agente de Atualização de Dispositivo." lightbox="media/understand-device-update/client-agent-workflow.png":::

[Saiba mais](device-update-agent-overview.md) sobre o agente de Atualização de Dispositivo. 

### <a name="importing"></a>Importação

A importação é como as atualizações são ingeridas na Atualização de Dispositivo para que possam ser implantadas em dispositivos. A Atualização de Dispositivo dá suporte à distribuição de uma única atualização por dispositivo. Isso a torna ideal para atualizações de imagem completa que atualizam uma partição de sistema operacional inteira de uma vez ou um Manifesto de apt que descreve todos os pacotes que você deseja atualizar no dispositivo. Para importar atualizações para a Atualização de Dispositivo, primeiro crie um manifesto de importação que descreva a atualização e, em seguida, carregue os arquivos de atualização e o manifesto de importação em um local acessível pela Internet. Depois disso, você pode usar o portal do Azure ou a [API REST da Atualização de Dispositivo](https://docs.microsoft.com/rest/api/deviceupdate/) para iniciar o processo assíncrono de importação de atualização. A Atualização de Dispositivo carrega, processa e disponibiliza os arquivos para distribuição para os dispositivos IoT.

Para conteúdo confidencial, proteja o download usando uma SAS (assinatura de acesso compartilhado), como uma SAS ad hoc para o Armazenamento de Blobs do Azure. [Saiba mais sobre a SAS](../storage/common/storage-sas-overview.md)

:::image type="content" source="media/understand-device-update/import-update.png" alt-text="Diagrama do fluxo de trabalho de importação da Atualização de Dispositivo para o Hub IoT." lightbox="media/understand-device-update/import-update.png":::

[Saiba mais](import-concepts.md) sobre a importação de atualizações. 

### <a name="grouping-and-deployment"></a>Agrupamento e implantação

Depois de importar uma atualização, você pode exibir as atualizações compatíveis para os dispositivos e as classes de dispositivo.

A Atualização de Dispositivo dá suporte ao conceito de **Grupos** por meio de marcas no Hub IoT. A implantação de uma atualização em um grupo de teste primeiro é uma boa maneira de reduzir o risco de problemas durante uma distribuição de produção.

Na Atualização de Dispositivo, as implantações são uma maneira de conectar o conteúdo certo a um conjunto específico de dispositivos compatíveis. A Atualização de Dispositivo articula o processo de envio de comandos para cada dispositivo, instruindo-os a baixar e instalar as atualizações e retomar o status.

:::image type="content" source="media/understand-device-update/manage-deploy-updates.png" alt-text="Diagrama do fluxo de trabalho de agrupamento e implantação da Atualização de Dispositivo para o Hub IoT." lightbox="media/understand-device-update/manage-deploy-updates.png":::

[Saiba mais](device-update-compliance.md) sobre os conceitos de implantação

[Saiba mais](device-update-groups.md) sobre os grupos de Atualização de Dispositivo


## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Criar conta e instância de Atualização de Dispositivo](create-device-update-account.md)