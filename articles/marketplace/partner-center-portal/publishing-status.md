---
title: Verificar o status de publicação de sua oferta do Marketplace comercial
description: Verifique o status da validação, certificação e visualização de etapas necessárias para publicar uma oferta por meio do Marketplace comerciais na Microsoft Partner Center.
author: mattwojo
manager: evansma
ms.author: mattwoj
ms.service: marketplace
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: ae4840877ae461244a76984ed8ac3edc82d57a38
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67653975"
---
# <a name="check-the-publishing-status-of-your-commercial-marketplace-offer"></a>Verificar o status de publicação de sua oferta do Marketplace comercial

Você pode exibir as atuais **status da publicação** na **visão geral da oferta** guia dos [portal comercial Marketplace](https://partner.microsoft.com/dashboard/commercial-marketplace/offers) no Partner Center.

Um dos indicadores de status a seguir deve ser exibido para cada oferta.

| **Status**    | **Descrição**  |
| :---------- | :-------------------|
| **Escalação** | Oferta foi criada, mas ele não está sendo publicado. |
| **Publicação em andamento** | Plano de oferta/está trabalhando para percorrer as etapas do processo de publicação. |
| **Atenção necessária** | Um problema crítico foi descoberto durante a certificação Microsoft ou qualquer uma das etapas de publicação. |
| **Visualização** | Oferta certificada pela Microsoft e agora aguarda uma verificação final pelo editor. Selecione entrar no ar para tornar a oferta ao vivo. |
| **Live** | Oferta estiver ativa no marketplace e pode ser vista e adquirida por clientes. |
| **Venda de parada pendente** | Publicador selecionado "parar a origem de venda" no plano ou oferta, mas a ação ainda não foi concluída. |
| **Não disponível no marketplace** | Um oferta/plano anteriormente publicada no marketplace foi removido. |

## <a name="automated-validation"></a>Validação automatizada

A primeira etapa no processo de publicação é um conjunto de validações automatizados. Cada etapa de validação corresponde a um recurso que você escolheu para habilitar na criação de sua oferta. Se esse recurso não foi habilitado, a validação avançará para a próxima etapa de publicação. Cada verificação de validação deve ser concluída antes que o status da publicação seja aprovado.

- **Configuração de fluxo de compra da oferta (< 10 min)**

Nesta etapa, podemos garantir que sua oferta pode ser atendida quando adquiriu pelos clientes por meio do portal do Azure. Esta etapa só é aplicável para ofertas que estão sendo vendidas por meio do Microsoft.

- **Testar a validação de dados da unidade (cerca de 5 min)**

Nesta etapa, podemos validar os dados fornecidos na unidade de teste de seção de configuração técnica da oferta. Funcionalidade de teste de unidade é testada e aprovada. Esta etapa só é aplicável para ofertas com um test drive habilitado.

- **Testar a unidade de provisionamento (aproximadamente 30 min)**

Nesta etapa, depois de validar os dados e a funcionalidade do seu test drive na etapa anterior, podemos implantar e replicar as instâncias do seu test drive para que eles estejam prontos para uso pelo cliente.  Esta etapa só é aplicável para ofertas com um test drive habilitado.

- **Levar a validação de gerenciamento e o registro (< 15 min)**

Nesta etapa, vamos confirmar que seu sistema de gerenciamento de cliente potencial pode receber clientes potenciais com base em detalhes fornecidos na configuração da oferta. Esta etapa só é aplicável para ofertas com gerenciamento de leads habilitado.

## <a name="certification"></a>Certificação

Antes de sua publicação, deverão ser certificadas ofertas enviadas para o Marketplace comercial no Partner Center. Enviado ofertas passar por testes rigorosos, alguns automatizada e manual, incluindo uma verificação em relação a outros a [políticas de certificação do Azure Marketplace](https://docs.microsoft.com/legal/marketplace/general-policies). Envios devem ser marcadas como qualificados para a certificação antes de continuar para a próxima etapa no fluxo de publicação da oferta.

### <a name="types-of-validation-that-take-place-during-certification"></a>Tipos de validação que ocorrem durante a certificação

Há três níveis de validação incluídos no processo de certificação para cada oferta enviado.

- Qualificação de negócios do publicador
- Validação de conteúdo
- Validação técnica

#### <a name="publisher-business-eligibility"></a>Qualificação de negócios do publicador

Cada tipo de oferta verifica um conjunto de critérios de qualificação de base que o publicador deve atender. Critérios de qualificação podem incluir status do MPN do Editor, competências mantidas, níveis de competência, etc.

#### <a name="content-validation"></a>Validação de conteúdo

Durante a validação de conteúdo, as informações inseridas ao criar sua oferta são verificadas para qualidade e a relevância. Essas verificações de revisar as entradas para o marketplace listando os detalhes de preço, disponibilidade, planos associados, etc. Para atender o AppSource listando os critérios de e/ou Azure Marketplace, podemos validará que sua oferta inclui:

- um título que descreve com precisão a oferta;
- descrições bem escritas que fornecem uma visão geral completa e a proposição de valor;
- capturas de tela de qualidade e que acompanha vídeos; e
- obter uma explicação de como a oferta utiliza ferramentas e plataformas da Microsoft.

Saiba mais sobre os critérios de validação de conteúdo, lendo a [geral de políticas de listagem](https://docs.microsoft.com/legal/marketplace/certification-policies#100-general-policies).

#### <a name="technical-validation"></a>Validação técnica

Durante a validação técnica, a oferta (pacote ou binário) passa por verificações a seguir.
- Examinado em busca de malware
- Chamadas de rede monitoradas
- Pacote analisado
- Varredura completa de funcionalidade de real da oferta

A oferta é testada em várias plataformas e versões para garantir que ele seja robusto.

Examine os detalhes de configuração específicos necessários para sua oferta na seção configurações técnicas deste documento.

### <a name="certification-failure-report"></a>Relatório de falhas de certificação

Após a conclusão da análise, se sua oferta passou a certificação, em seguida, ele passa para a próxima etapa no processo de publicação. Se sua oferta falha nas verificações de listagem, técnico ou política ou se você não está qualificado para enviar uma oferta desse tipo, um relatório de falhas de certificação é gerado e enviado por email para você.

Este relatório contém descrições de todas as políticas que falharam, juntamente com as notas de revisão. Examine este relatório de email, resolva os problemas, fazer atualizações para sua oferta onde necessário e envie novamente a oferta usando o [portal do Marketplace comercial](https://partner.microsoft.com/dashboard/commercial-marketplace/offers) no Partner Center. (Você pode reenviar a oferta como quantas vezes forem necessárias até que a passagem de certificação).

## <a name="preview-creation"></a>Criação de versão prévia

Durante o **criação de visualização** etapa, criamos uma versão de sua oferta acessível apenas para o público que você especificou na seção de visualização da sua oferta.

## <a name="publisher-approval"></a>Aprovação do publicador

Nesta etapa, você receberão um email com uma solicitação para que você possa revisar e aprovar sua visualização de oferta antes da etapa final de publicação.

Se você tiver selecionado para vender a sua oferta por meio da Microsoft, você poderá testar a aquisição e a implantação de sua oferta para garantir que ele atenda às suas necessidades durante esse estágio de aprovação de visualização. Sua oferta não ainda estará disponível no marketplace público. Depois de testar e aprovar essa visualização, você precisará selecionar **Go-Live** sobre o [ **visão geral da oferta** ](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) painel.

Se você quiser fazer alterações para a oferta durante esse estágio de visualização, você pode editar e reenviar para publicar uma nova visualização. Consulte o artigo [atualização existente marketplace oferece](#update-existing-marketplace-offers) para obter detalhes sobre as alterações mais.

Se sua oferta já está ativo e disponível ao público no marketplace, as atualizações feitas não entrarei em tempo real até que você selecione **Go-live** sobre o [ **visão geral da oferta** ](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) Painel de controle.

### <a name="publish-offer-to-the-public"></a>Publicar a oferta para o público

Entrar no Centro de parceiro e acessar a oferta. Você será redirecionado para o **visão geral da oferta** página. Na parte superior desta página, você verá uma opção para **entrar no ar**. Selecione **entrar no ar,** e depois de confirmar, a oferta começará a ser publicado para o público. Você receberá uma notificação por email quando a oferta estiver ativa.

## <a name="publish"></a>Publicar

Agora que você decidiu **entrar no ar** com sua oferta, tornando-o disponível no marketplace, há uma série de verificações de validação final que será escalonado para garantir que a oferta ao vivo está configurada como a versão prévia versão da oferta.

- **Configuração de fluxo de compra da oferta (> 10 min)**

Nesta etapa, podemos garantir que sua oferta pode ser atendida quando adquiriu pelos clientes por meio do portal do Azure. Esta etapa só é aplicável para ofertas que estão sendo vendidas por meio do Microsoft.

- **Testar a validação de dados da unidade (cerca de 5 min)**

Nesta etapa, podemos validar os dados fornecidos na unidade de teste de seção de configuração técnica da oferta. Funcionalidade de teste de unidade é testada e aprovada. Esta etapa só é aplicável para ofertas com um test drive habilitado.

- **Testar a unidade de provisionamento (aproximadamente 30 min)**

Nesta etapa, podemos implantar e replicar as instâncias do seu test drive para que eles estejam prontos para uso pelo cliente.  Esta etapa só é aplicável para ofertas com um test drive habilitado.

- **Levar a validação de gerenciamento e o registro (> 15 min)**

Nesta etapa, vamos confirmar que seu sistema de gerenciamento de cliente potencial pode receber clientes potenciais com base em detalhes fornecidos na configuração da oferta. Esta etapa só é aplicável para ofertas com gerenciamento de leads habilitado.

- **Publicar final (> 30 minutos)**

Nesta etapa, podemos garantir que sua oferta se torna disponível publicamente no marketplace.

## <a name="update-existing-marketplace-offers"></a>Atualizar as ofertas existentes do marketplace

Se você quiser fazer alterações em uma oferta que você publicou, será necessário primeiro atualizar a oferta existente e, em seguida, publicá-lo novamente.

## <a name="next-steps"></a>Próximas etapas

- [Atualizar uma oferta existente no mercado comercial](./update-existing-offer.md)
