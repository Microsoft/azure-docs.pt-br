---
title: Programa Azure Certified Device – Tutorial – Como criar seu projeto
description: Guia de criação de um projeto no portal do Azure Certified Device
author: nkuntjoro
ms.author: nikuntjo
ms.service: certification
ms.topic: tutorial
ms.date: 03/01/2021
ms.custom: template-tutorial
ms.openlocfilehash: 11d72847209a2e706d4aa32d38af1b2c8af3dfa0
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107313990"
---
# <a name="tutorial-create-your-project"></a>Tutorial: Criar seu projeto

Parabéns por escolher certificar seu dispositivo por meio do programa Azure Certified Device! Agora você escolheu o programa de certificação apropriado para seu dispositivo e está pronto para começar a usar o portal.

Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> * Entrar no [portal do Azure Certified Device](https://certify.azure.com/)
> * Criar um projeto de certificação para seu dispositivo
> * Especificar os detalhes básicos do dispositivo do seu projeto

## <a name="prerequisites"></a>Pré-requisitos

- Você precisará ter uma [conta corporativa ou de estudante válida do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis).
- Você precisará ter uma conta verificada do MPN (Microsoft Partner Network). Caso você não tenha uma conta do MPN, [ingresse na rede do parceiro](https://partner.microsoft.com/) antes de começar.

## <a name="signing-into-the-azure-certified-device-portal"></a>Como entrar no portal do Azure Certified Device

Para começar, você precisa entrar no portal, no qual fornecerá as informações do seu dispositivo, concluirá o teste de certificação e gerenciará as publicações de dispositivo para o catálogo do Azure Certified Device.

1. Acesse o [portal do Azure Certified Device](https://certify.azure.com).
1. Selecione `Company profile` no lado esquerdo e atualize as informações do fabricante.
   ![Seção Perfil da empresa](./media/images/company-profile.png)
1. Aceite o contrato do programa para iniciar o projeto.

## <a name="creating-your-project-on-the-portal"></a>Como criar seu projeto no portal

Agora que você está configurado no portal, inicie o processo de certificação. Primeiro, você precisa criar um projeto para o seu dispositivo.

1. Na tela inicial, selecione `Create new project`. Isso abrirá uma janela para adicionar informações básicas do dispositivo na próxima seção.

 ![Imagem do botão Criar projeto](./media/images/create-new-project.png)

## <a name="identifying-basic-device-information"></a>Como identificar as informações básicas do dispositivo

Em seguida, você precisa fornecer informações básicas do dispositivo. Você poderá editar essas informações posteriormente.

1. Preencha os campos solicitados na seção `Basics`. Veja a tabela abaixo para obter esclarecimentos sobre os campos **obrigatórios**:

    | Campos                  | Descrição                                                                                                                         |
    |------------------------|-------------------------------------------------------------------------------------------------------------------------------------|
    | Nome do projeto           | Nome interno que não será visível no catálogo do Azure Certified Device                                                        |
    | Nome do dispositivo            | Nome público do dispositivo                                                                                                |
    | Tipo de dispositivo            | Especificação do produto finalizado ou do kit de desenvolvedor pronto para a solução.     Para obter mais informações sobre a terminologia, confira [Glossário de certificação](./resources-glossary.md).                                                                     |
    | Classe de dispositivo           | Gateway, sensor ou outro.  Para obter mais informações sobre a terminologia, confira [Glossário de certificação](./resources-glossary.md).                                                                    |
    | URL do código-fonte do dispositivo | Necessário se você estiver certificando um kit de desenvolvimento pronto para a solução, de outro modo, opcional. A URL precisa ser para uma localização do GitHub para o código do dispositivo. |
1. Selecione o botão `Next` para prosseguir para a guia `Certifications`.

    ![Imagem do formulário Criar projeto, guia Certificações](./media/images/create-new-project-certificationswindow.png)

1. Especifique quais certificações você deseja obter para seu dispositivo.
1. Selecione `Create` e o novo projeto será salvo e ficará visível na home page do portal.

    ![Imagem da tabela do projeto](./media/images/project-table.png)

1. Selecione o nome do projeto na tabela. Isso iniciará a página de resumo do projeto, na qual você pode adicionar e ver outros detalhes sobre seu dispositivo.

    ![Imagem da página de detalhes da réplica](./media/images/device-details-section.png)

## <a name="next-steps"></a>Próximas etapas

Agora você está pronto para adicionar detalhes do dispositivo e testá-lo usando nosso serviço de certificação. Avance para o próximo artigo para saber como editar os detalhes do dispositivo.
> [!div class="nextstepaction"]
> [Tutorial: Como adicionar detalhes do dispositivo](tutorial-02-adding-device-details.md)
