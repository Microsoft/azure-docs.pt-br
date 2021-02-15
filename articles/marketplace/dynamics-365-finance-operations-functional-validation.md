---
title: Validação funcional de uma oferta de finanças e operações do AppSource Dynamics 365 no Azure Marketplace.
description: Como validar de forma funcional uma oferta de finanças e operações do Dynamics 365 no Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: emuench
ms.author: navits
ms.date: 07/17/2020
ms.openlocfilehash: b9685081c0beacd745a83067b9d9876384933377
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93131234"
---
# <a name="appsource-dynamics-365-finance-and-operations-functional-validation"></a>Validação funcional de operações e finanças do AppSource Dynamics 365

Para concluir uma primeira publicação no [Partner Center](https://partner.microsoft.com/dashboard/home), as ofertas para finanças e operações do Dynamics 365 exigem duas validações funcionais:

- Carregue um vídeo de demonstração do ambiente do Dynamics 365 que mostra a funcionalidade básica.
- Apresente capturas de tela que demonstram o ambiente do LCS ( [serviços de ciclo de vida](https://lcs.dynamics.com/) ) da solução.

> [!NOTE]
> As publicações de rescertificação subsequentes não exigem demonstração. Para saber mais, consulte o [documento de política do AppSource](/legal/marketplace/certification-policies#1440-dynamics-365-finance-ops).

## <a name="how-to-validate"></a>Como validar

Há duas opções de validação funcional:

- Mantenha uma chamada de conferência de 30 minutos conosco durante o horário de expediente (hora oficial do Pacífico) para demonstrar e registrar o ambiente e a solução do [LCS](https://lcs.dynamics.com/) , ou
- No Partner Center, vá para [Commercial Marketplace](https://partner.microsoft.com/dashboard/commercial-marketplace/overview)  >  **visão geral** do Marketplace comercial e carregue uma URL de vídeo de demonstração e capturas de tela do LCS na guia conteúdo suplementar da oferta.

A equipe de certificação da Microsoft revisa o vídeo e os arquivos e, em seguida, aprova a solução ou envia um email para você sobre as próximas etapas.

### <a name="option-1-30-minute-conference-call"></a>Opção de chamada de conferência de 1:30 minutos

Para agendar uma chamada de revisão final, entre em contato [appsourceCRM@microsoft.com](mailto:appsourceCRM@microsoft.com) com o nome da sua oferta e alguns slots de tempo potenciais entre 8h e 17h hora do Pacífico.

### <a name="option-2-upload-a-demo-video-and-lcs-screenshots"></a>Opção 2: carregar um vídeo de demonstração e capturas de tela do LCS

1. Grave um vídeo e carregue o endereço para o site de Hospedagem de sua escolha. Siga estas diretrizes:

    - Visível pela equipe de certificação da Microsoft.
    - Menos de 20 minutos de comprimento.
    - Inclui até três destaques de funcionalidade principal de sua solução no ambiente do Dynamics 365.

    > [!NOTE]
    > É aceitável usar um vídeo de marketing existente se ele atender às diretrizes.

2. Use as seguintes capturas de tela do ambiente do [LCS](https://lcs.dynamics.com/) que correspondem à oferta ou à solução que você deseja publicar. Eles devem ser claros o suficiente para que a equipe de certificação Leia o texto. Salve as capturas de tela como arquivos JPG. Você pode fornecer [appSourceCRM@microsoft.com](mailto:appSourceCRM@microsoft.com) permissão ao seu ambiente do LCS para que possamos verificar a configuração no lugar de fornecer capturas de tela.

    1. Acesse a biblioteca de projetos do **LCS**  >  **Business Process Modeler**  >  **Project library** . Faça capturas de tela de todas as etapas do processo. Inclua os **diagramas** e as colunas **revisadas** , conforme mostrado aqui:

       :::image type="content" source="media/dynamics-365-finance-operations/project-library.png" alt-text="Mostra a janela da biblioteca do projeto.":::

      2. Vá para gerenciamento de soluções do **LCS**  >  **Solution Management**  >  **pacote de solução de teste** . Faça capturas de tela que incluem a visão geral do pacote e o conteúdo mostrado nestes exemplos:

    | Campo | Imagem <img src="" width="400px">|
    | --- | --- |
    | Visão geral do pacote | [![Captura de tela que mostra a janela "visão geral do pacote".](media/dynamics-365-finance-operations/package-overview-45.png)](media/dynamics-365-finance-operations/package-overview.png#lightbox) |
    | <ul><li>Aprovadores de solução</li></ul> | [![Tela de visão geral do pacote](media/dynamics-365-finance-operations/solution-approvers-45.png)](media/dynamics-365-finance-operations/solution-approvers.png#lightbox) |
    | Conteúdo do pacote<ul><li>Modelo</li><li>Pacote de implantação de software</li></ul> | [![Tela de conteúdo do pacote um](media/dynamics-365-finance-operations/package-contents-1-45.png)](media/dynamics-365-finance-operations/package-contents-1.png#lightbox) |
    | <ul><li>Configuração do Windows</li><li>Backup de banco de dados</li></ul><br>Os artefatos não são necessários na seção de **configuração do Windows** . | [![Tela de conteúdo do pacote dois](media/dynamics-365-finance-operations/package-contents-2-45.png)](media/dynamics-365-finance-operations/package-contents-2.png#lightbox) |
    | <ul><li>Power BI modelo de relatório</li><li>Artefato de BPM</li></ul><br>Os artefatos não são necessários na seção **Power bi** . | [![Tela de conteúdo do pacote três](media/dynamics-365-finance-operations/package-contents-3-45.png)](media/dynamics-365-finance-operations/package-contents-3.png#lightbox) |
    | <ul><li>Processar pacote de dados</li><li>Contrato de licença da solução e política de privacidade</li></ul><br>As seções **configuração do Windows** e modelo de relatório de **Power bi** são opcionais a serem incluídas para ofertas de finanças e operações. | [![Tela de conteúdo do pacote quatro](media/dynamics-365-finance-operations/package-contents-4-45.png)](media/dynamics-365-finance-operations/package-contents-4.png#lightbox) |

    Para saber mais sobre cada seção do portal do LCS, consulte o [Guia do usuário do LCS](/dynamics365/fin-ops-core/dev-itpro/lifecycle-services/lcs-user-guide).

3. Carregar no Partner Center.

    1. Crie um documento de texto que inclua o endereço de vídeo de demonstração e capturas de tela, ou salve as capturas de tela como arquivos JPG separados.
    2. Adicione o texto e todos os arquivos JPG a um arquivo. zip no [mercado comercial](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) no Partner Center na guia **conteúdo complementar** de sua oferta de finanças e operações.

    [![Mostra a janela da biblioteca do projeto](media/dynamics-365-finance-operations/supplemental-content.png)](media/dynamics-365-finance-operations/supplemental-content.png#lightbox)

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como criar uma oferta, confira: [criar uma oferta do Dynamics 365 for Operations](./partner-center-portal/create-new-operations-offer.md).