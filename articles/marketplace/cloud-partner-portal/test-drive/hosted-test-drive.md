---
title: Test drive hospedado | Microsoft Docs
description: Como configurar um manter um test drive hospedado do Marketplace
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Ricardo.Villalobos
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 5057c0c781cb9ec60ecde7dd3f4bf96089b902df
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60559782"
---
# <a name="hosted-test-drive"></a>Test drive hospedado

Um test drive hospedado elimina a complexidade da instalação porque a Microsoft instala e mantém o serviço que executa o provisionamento e o desprovisionamento do usuário do test drive. Este artigo é para editores que já têm ou estando criando uma oferta no AppSource e desejam oferecer a um test drive hospedado, que se conecte a uma instância do Dynamics 365 for Customer Engagement, do Dynamics 365 para Finanças ou do Dynamics 365 Business Central.

## <a name="how-to-publish-a-test-drive"></a>Como publicar um Test Drive

Navegue até uma oferta existente ou crie uma oferta.

Selecione a opção de test drive no menu lateral.

Selecione \'Sim\' para a opção \'Habilitar um test drive\'.

Forneça os seguintes campos na seção \'Detalhes\'.

- **Descrição**: Fornece uma visão geral do seu Test Drive. Esse texto será mostrado ao usuário enquanto o test drive está sendo provisionado. Esse campo dá suporte a HTML, caso você queira fornecer conteúdo formatado.
- **Manual do usuário**: Carregue um manual do usuário detalhada (arquivo de. PDF do tipo) que ajuda os usuários do Test Drive entender como usar o seu aplicativo.
- **Vídeo do Test Drive demonstração**: Opcionalmente, carrega um vídeo que demonstra o seu aplicativo.

Conceda permissão ao AppSource para provisionar e desprovisionar usuários do test drive em seu locatário usando as instruções localizadas [aqui](https://github.com/Microsoft/AppSource/blob/patch-1/Microsoft%20Hosted%20Test%20Drive/Setup-your-Azure-subscription-for-Dynamics365-Microsoft-Hosted-Test-Drives.md).

Nesta etapa, você gerará os valores da \'ID do Aplicativo Azure AD\' e da \'Chave do Aplicativo Azure AD\' mencionados abaixo.

Forneça os seguintes campos na seção \'Configurações Técnicas\':

- **Tipo de teste de unidade**: Escolha \'Microsoft Hosted (exemplo do Dynamics 365 for Customer Engagement)' opção. Isso indica que a Microsoft hospedará e manterá o serviço que executa o provisionamento e o desprovisionamento de usuários do test drive.
- **Simultânea máxima Test Drives**: Defina esse campo como o número de usuários simultâneos que podem ter uma unidade de teste ativa em qualquer determinado momento. Cada usuário consumirá uma licença do Dynamics enquanto seu test drive estiver ativo, portanto, verifique se você tem pelo menos esse número de licenças do Dynamics disponíveis para usuários do test drive. O valor recomendado é de 3 a 5.
- **Teste de unidade de duração (horas)**: Defina esse campo como o número de horas que os usuários do para que Test Drive ficará ativo. Depois desse número de horas, o usuário será desprovisionado do locatário. O valor recomendado é de 2 a 24 horas, dependendo da complexidade do aplicativo. O usuário sempre poderá solicitar outro test drive quando atingir o tempo limite e desejar acessar o test drive novamente.
- **URL da instância**: Forneça uma URL que o usuário de teste de unidade será inicialmente para onde navegar quando os usuários iniciarem o Test Drive. Normalmente, essa é a URL da instância do Dynamics 365 que contém o aplicativo e os dados de exemplo instalados. Valor de exemplo: https:\//testdrive.crm.dynamics.com
- **ID de locatário do Azure AD**: Forneça a ID do locatário do Azure para sua instância do Dynamics 365. Para recuperar esse valor, faça logon no portal do Azure e navegue até o \'Azure Active Directory\' -\> Selecione Propriedades na folha de menu −\> Copiar a ID do Diretório. Valor de exemplo: 72f988bf-86f1-41af-91ab-2d7cd0111234
- **A ID de aplicativo do Azure AD**: ID do aplicativo do Azure AD criado na etapa 7. \ valor de exemplo: 53852862-a2ae-4e43-9461-faa49650a096
- **Chave de aplicativo do Azure AD**: Secreta para o aplicativo do AD do Azure criado na etapa 7. \ valor de exemplo: IJUgaIOfq9b9LbUjeQmzNBW4VGn6grr1l/n3aMrnfdk=
- **Nome do locatário do Azure AD**: Forneça o nome do locatário do Azure para sua instância do Dynamics 365. Use o formato \<tenantname.\>onmicrosoft.com. Valor de exemplo: testdrive.onmicrosoft.com
- **URL da API da Web da instância**: Forneça a URL da API da Web para sua instância do Dynamics 365. Você pode recuperar esse valor ao fazer logon na instância do Microsoft Dynamics 365 e navegar para Configuração –\> Personalização –\> Recursos para Desenvolvedores –\> API Web da Instância (copie esta URL). Valor de exemplo: https:\//testdrive.crm.dynamics.com/api/data/v9.0 
- **Nome da função**: Forneça o nome da função de segurança de Dynamics 365 personalizado criado para o teste de unidade. Essa é a função que será atribuída aos usuários durante o test drive. Valor de exemplo: testdriverole

## <a name="next-steps"></a>Próximas etapas

Quando estiver pronto **publique** sua oferta, depois que seu aplicativo tiver sido aprovado a certificação, você terá uma **visualização** de sua oferta. Inicie um test drive na interface do usuário e verifique se os test drives estão sendo executados corretamente. Depois que você se sentir confortável com a sua oferta de visualização, agora é hora de **entrar no ar!**
