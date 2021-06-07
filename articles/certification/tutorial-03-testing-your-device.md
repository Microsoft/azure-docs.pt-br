---
title: Programa Azure Certified Device – Tutorial – Como testar seu dispositivo
description: Um guia passo a passo para testar seu dispositivo com o serviço AICS no portal do Azure Certified Device
author: nkuntjoro
ms.author: nikuntjo
ms.service: certification
ms.topic: tutorial
ms.date: 03/02/2021
ms.custom: template-tutorial
ms.openlocfilehash: ef82d44ef44189c0430ba9789baf3279fbe49a9c
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107310947"
---
# <a name="tutorial-test-and-submit-your-device"></a>Tutorial: Testar e enviar seu dispositivo

A próxima fase principal do processo de certificação (embora ela possa ser concluída antes da adição dos detalhes do dispositivo) envolve o teste do seu dispositivo. Por meio do portal, você usará o AICS (Serviço de Certificação IoT do Azure) para demonstrar o desempenho do seu dispositivo de acordo com nossos requisitos de certificação. Depois de passar na fase de teste com êxito, você enviará seu dispositivo para revisão final e aprovação da equipe de Certificação do Azure.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Conectar seu dispositivo ao Hub IoT usando o DPS (Serviço de Provisionamento de Dispositivos)
> * Testar seu dispositivo de acordo com os programas de certificação selecionados
> * Enviar seu dispositivo para revisão da equipe de Certificação do Azure

## <a name="prerequisites"></a>Pré-requisitos

- Você deve estar conectado e ter um projeto para seu dispositivo criado no [portal do Azure Certified Device](https://certify.azure.com). Para obter mais informações, veja o [tutorial](tutorial-01-creating-your-project.md).
- (Opcional) Aconselhamos que você prepare seu dispositivo e verifique manualmente o desempenho dele de acordo com os requisitos de certificação. Isso porque, se você quiser fazer um novo teste com outro código de dispositivo ou programa de certificação, precisará criar um projeto.

## <a name="connecting-your-device-using-dps"></a>Como conectar seu dispositivo usando o DPS

Todos os dispositivos certificados devem demonstrar a capacidade de se conectarem ao Hub IoT usando o DPS. As etapas a seguir explicam como conectar seu dispositivo com êxito para teste no portal.

1. Para iniciar a fase de teste, selecione o link `Connect & test` na página de resumo do projeto:  

    ![Link para Conectar e testar](./media/images/connect-and-test-link.png)

1. Dependendo das certificações selecionadas, você verá os testes necessários na página 'Conectar e testar'. Examine-os para garantir que esteja se candidatando no programa de certificação correto.  

    ![Página Conectar e testar](./media/images/connect-and-test.png)

1. Conecte seu dispositivo ao Hub IoT usando o DPS (Serviço de Provisionamento de Dispositivos). O DPS dá suporte a opções de conectividade de chaves simétricas, certificação X.509 e um TPM (Trusted Platform Module). Isso é necessário para todas as certificações.

    - *Para obter mais informações sobre como conectar seu dispositivo ao Hub IoT do Azure com o DPS, acesse [Visão geral do provisionamento de dispositivos](../iot-dps/about-iot-dps.md "Visão geral do Serviço de Provisionamento de Dispositivos").*
    
1. Se estiver usando chaves simétricas, você precisará configurar o DPS com o escopo de ID do DPS, a identificação do dispositivo, a chave de autenticação e o ponto de extremidade do DPS fornecidos. Caso contrário, precisará fornecer o certificado X.509 ou a chave de endosso.

1. Depois de configurar seu dispositivo com o DPS, confirme a conexão clicando no botão `Connect` na parte inferior da página. Após a conexão bem-sucedida, prossiga para a fase de teste clicando no botão `Next`.  

    ![Conectar e testar conectado](./media/images/connected.png)

## <a name="testing-your-device"></a>Como testar seu dispositivo

Depois de conectar o dispositivo ao AICS com êxito, você estará pronto para executar os testes de certificação específicos do programa de certificação para o qual está se candidatando.

1. **Para a certificação do Azure Certified Device**: na guia 'Selecionar funcionalidade do dispositivo', você examinará e selecionará quais testes deseja executar no dispositivo.
1. **Para a certificação do IoT Plug and Play**: examine atentamente os parâmetros que serão verificados durante o teste declarado no modelo de dispositivo.
1. **Para a certificação do Gerenciado por Borda**: não são necessárias etapas adicionais além da demonstração da conectividade.
1. Depois de concluir as preparações necessárias para o programa de certificação especificado, selecione `Next` para prosseguir para a fase de 'Teste'.
1. Escolha `Run tests` na página para começar a executar o AICS com o seu dispositivo.
1. Depois de receber uma notificação informando que você passou nos testes, selecione `Finish` para voltar à página de resumo.

![Êxito no teste](./media/images/test-pass.png)

7. Caso tenha outras dúvidas ou precise obter assistência para solução de problemas com o AICS, acesse nosso guia de solução de problemas.

> [!NOTE]
> Embora você possa concluir o processo de certificação online do IoT Plug and Play e do Gerenciado por Borda sem precisar enviar seu dispositivo para revisão manual, talvez seja contatado por um membro da equipe do Azure Certified Device para uma validação adicional do dispositivo além do que é testado por meio do nosso serviço de automação.

## <a name="submitting-your-device-for-review"></a>Como enviar seu dispositivo para revisão

Depois de preencher todos os campos obrigatórios da seção 'Detalhes do dispositivo' e passar com êxito no teste automatizado do processo 'Conectar e testar', você poderá notificar a equipe do Azure Certified Device de que você está pronto para a revisão de certificação.

1. selecione `Submit for review` na página de resumo do projeto:  

    ![Link para Examinar e certificar](./media/images/review-and-certify.png)

1. Confirme seu envio na janela pop-up. Depois que um dispositivo for enviado, todos os detalhes dele serão somente leitura até que a edição seja solicitada. (Confira [Como editar as informações do dispositivo após a publicação](./how-to-edit-published-device.md).)  

    ![Caixa de diálogo Iniciar revisão de certificação](./media/images/start-certification-review.png)

1. Depois que o projeto for enviado, a página de resumo do projeto indicará que o projeto está `Under Certification Review` pela equipe de Certificação do Azure:  

    ![Em revisão](./media/images/review-and-certify-under-review.png)

1. No prazo de 5 a 7 dias úteis, espere uma resposta por email da equipe de Certificação do Azure para o endereço fornecido no seu perfil de empresa em relação ao status do envio do seu dispositivo.

    - Envio aprovado  
        Depois que o seu projeto for examinado e aprovado, você receberá um email. O email incluirá um conjunto de arquivos, incluindo o selo do Azure Certified Device, as diretrizes de uso do selo e outras informações sobre como ampliar a mensagem de que o seu dispositivo está certificado. Parabéns!

    - Envio pendente  
        Caso seu projeto não seja aprovado, você poderá fazer alterações nos detalhes do projeto e reenviar o dispositivo para a certificação quando estiver pronto. Um email será enviado com informações sobre o motivo da não aprovação do projeto e as etapas necessárias para reenviá-lo para certificação.

## <a name="next-steps"></a>Próximas etapas

Parabéns! Seu dispositivo passou em todos os testes com êxito e foi aprovado por meio do programa Azure Certified Device. Agora, você pode publicá-lo em nosso catálogo do Azure Certified Device, em que os clientes poderão comprar seus produtos com total confiança no desempenho dele com o Azure.
> [!div class="nextstepaction"]
> [Tutorial: Como publicar seu dispositivo](tutorial-04-publishing-your-device.md)

