---
title: Programa Azure Certified Device – Tutorial – Como adicionar detalhes do dispositivo
description: Um guia passo a passo para adicionar detalhes do dispositivo ao seu projeto no portal do Azure Certified Device
author: nkuntjoro
ms.author: nikuntjo
ms.service: certification
ms.topic: tutorial
ms.date: 03/02/2021
ms.custom: template-tutorial
ms.openlocfilehash: f4f3d045a2530fa54d22bec789918454cba80097
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107310420"
---
# <a name="tutorial-add-device-details"></a>Tutorial: Como adicionar detalhes do dispositivo

Agora você criou o projeto para seu dispositivo e está pronto para começar o processo de certificação! Primeiro, vamos adicionar os detalhes do seu dispositivo. Eles incluirão especificações técnicas que seus clientes poderão ver no catálogo do Azure Certified Device e os detalhes de marketing que usarão para comprar depois de tomar uma decisão.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Adicionar detalhes do dispositivo usando os recursos de componentes e dependências
> * Carregar um guia de introdução para seu dispositivo
> * Especificar detalhes de marketing para os clientes comprarem seu dispositivo
> * Opcionalmente, identificar certificações do setor

## <a name="prerequisites"></a>Pré-requisitos

* Você deve estar conectado e ter um projeto para seu dispositivo criado no [portal do Azure Certified Device](https://certify.azure.com). Para obter mais informações, veja o [tutorial](tutorial-01-creating-your-project.md).
* Você deve ter um guia de introdução para seu dispositivo no formato PDF. Fornecemos vários modelos de introdução para uso, dependendo do programa de certificação e do idioma de sua preferência. Os modelos estão disponíveis em nosso local do GitHub de [modelos de introdução](https://aka.ms/GSTemplate "Modelos de introdução").

## <a name="adding-technical-device-details"></a>Como adicionar detalhes técnicos do dispositivo

A primeira seção da página do seu projeto, chamada "Detalhes do dispositivo de entrada", permite que você forneça informações sobre as principais funcionalidades de hardware do seu dispositivo, como nome, descrição, processador, sistema operacional, opções de conectividade, interfaces de hardware, protocolos do setor, dimensões físicas e muito mais. Embora muitos dos campos sejam opcionais, a maioria dessas informações será disponibilizada para clientes potenciais no catálogo do Azure Certified Device se você optar por publicar seu dispositivo depois que ele tiver sido certificado.

1. Clique em `Add` na seção 'Detalhes do dispositivo de entrada' na página de resumo do projeto para abrir a seção detalhes do dispositivo. Você verá cinco seções a serem concluídas.

![Imagem da página de detalhes da réplica](./media/images/device-details-menu.png)

2. Examine as informações fornecidas anteriormente quando você criou o projeto na guia `Basics`.
1. Examine as certificações que você está aplicando ao seu dispositivo na guia `Certifications`.
1. Abra a guia `Product details` e selecione pelo menos um sistema operacional.
1. Adicione **pelo menos** um componente discreto que descreva seu dispositivo. Você pode ver diretrizes adicionais sobre o uso do componente [aqui](how-to-using-the-components-feature.md).
1. Clique em `Save`. Você poderá editar o dispositivo do componente e adicionar mais detalhes avançados.
1. Lista detalhes adicionais do dispositivo não capturados pelos detalhes do componente em `Additional product details`.
1. Se você marcou `Other` em qualquer um dos campos do componente ou tiver uma circunstância especial que gostaria de sinalizar com a equipe de certificação do Azure, deixe um comentário de esclarecimento na seção `Comments for reviewer`.
1. Use a guia `Dependencies` para listar as dependências se o dispositivo exigir hardware ou serviços adicionais para enviar dados ao Azure. Você pode exibir diretrizes adicionais sobre dependências de listagem [aqui](how-to-indirectly-connected-devices.md).
1. Quando estiver satisfeito com as informações fornecidas, você poderá usar a guia `Review` para uma visão geral somente leitura do conjunto completo de detalhes do dispositivo que foram inseridos.
1. Clique em `Project summary` na parte superior da página para retornar à página de resumo.

![Página Examinar detalhes do projeto](./media/images/sample-device-details.png)

## <a name="uploading-a-get-started-guide"></a>Como carregar um guia de introdução

O guia de introdução é um documento PDF para simplificar a instalação, a configuração e o gerenciamento do seu produto. A finalidade dele é simplificar para os clientes a conexão e o suporte a dispositivos no Azure usando seu dispositivo. Como parte do processo de certificação, exigimos que nossos parceiros forneçam **um** guia de introdução para o programa de certificação mais relevante.

1. Verifique se você forneceu todas as informações solicitadas em seu PDF do guia de introdução de acordo com os [modelos](https://aka.ms/GSTemplate) fornecidos. O modelo que você usa deve ser determinado pelo selo de certificação para o qual você está se inscrevendo. (Por exemplo, um dispositivo IoT Plug and Play usará o modelo de IoT Plug and Play. Os dispositivos que se aplicam *somente* à certificação de linha de base do Azure Certified Device usarão o modelo do Azure Certified Device.)
1. Clique em `Add` na seção do guia 'Introdução' na página de resumo do projeto.

![Imagem do botão GSG](./media/images/gsg-menu.png)

2. Clique em 'Escolher arquivo' para carregar o PDF.
1. Examine o documento na visualização para formatação.
1. Salve seu upload clicando no botão 'Salvar'.
1. Clique em `Project summary` na parte superior da página para retornar à página de resumo.

## <a name="providing-marketing-details"></a>Como fornecer detalhes de marketing

Nessa área, você fornecerá informações de marketing prontas para o cliente para seu dispositivo. Esses campos serão demonstrados no catálogo do Azure Certified Device se você optar por publicar seu dispositivo certificado.

1. Clique em `Add` na seção 'Adicionar detalhes de marketing' para abrir a página de detalhes de marketing.

![Imagem da seção de detalhes de marketing](./media/images/marketing-details.png)

1. Carregue uma foto do produto no formato JPEG ou PNG que será usada no catálogo.
1. Escreva uma breve descrição do dispositivo que será exibida na página descrição do produto do catálogo.
1. Indique a disponibilidade geográfica do seu dispositivo.
1. Forneça um link para a página de marketing do fabricante deste dispositivo. O link deve direcionar para um site que fornece informações adicionais sobre o dispositivo.
    > [!Note]
    > Verifique se todas as URLs fornecidas são válidas ou estarão ativas no momento da aprovação após a publicação.*

1. Indique até três setores-alvo para os quais o dispositivo é otimizado.
1. Forneça informações para até cinco distribuidores do seu dispositivo. Isso pode incluir o próprio site do fabricante.

    > [!Note]
    > Se nenhuma URL de página de produto do distribuidor for fornecida, o botão `Shop` no catálogo será padronizado com o link fornecido para a `Distributor page`, que pode não ser específico para o dispositivo. O ideal é que a URL do distribuidor direcione a uma página específica na qual um cliente pode comprar um dispositivo, mas isso não é obrigatório. Se o distribuidor for o mesmo que o fabricante, essa URL poderá ser a mesma que a página de marketing do fabricante.*

1. Clique em `Save` para confirmar suas informações.
1. Clique em `Project summary` na parte superior da página para retornar à página de resumo.

## <a name="declaring-additional-industry-certifications"></a>Como declarar certificações adicionais do setor

Também é possível promover certificações adicionais do setor que você pode ter recebido para seu dispositivo. Essas certificações podem ajudar a fornecer maior clareza no uso pretendido do seu dispositivo e poderão ser pesquisadas no catálogo do Azure Certified Device.

1. Clique em `Add` na seção 'Fornecer certificações do setor'.
1. Clique em `Add a certification` para selecionar em uma lista de programas comuns de certificação do setor. Se o produto tiver atingido uma certificação que não está em nossa lista, você poderá especificar um valor de cadeia de caracteres personalizada selecionando `Other (please specify)`.
1. Opcionalmente, forneça uma descrição ou observações para o revisor. No entanto, essas observações não estarão disponíveis publicamente para exibição no catálogo.
1. Clique em `Save` para confirmar suas informações.
1. Clique em `Project summary` na parte superior da página para retornar à página de resumo.

## <a name="next-steps"></a>Próximas etapas

Agora você concluiu o processo de descrever seu dispositivo! Isso ajudará a equipe de revisão do Azure Certified Device e seu cliente a entender melhor seu produto. Quando estiver satisfeito com as informações fornecidas, você estará pronto para passar para a fase de teste do processo de certificação.
> [!div class="nextstepaction"]
> [Tutorial: Como testar seu dispositivo](tutorial-03-testing-your-device.md)
