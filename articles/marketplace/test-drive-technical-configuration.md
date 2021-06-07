---
title: Testar a configuração técnica, Microsoft Commercial Marketplace
description: Saiba mais sobre as unidades de teste. As unidades de teste permitem que novos clientes test drive sua oferta antes de confirmar a compra.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 08/13/2019
author: trkeya
ms.author: trkeya
ms.openlocfilehash: 553eba3898a5ea42e5d478603e35e82c68abcab1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96462903"
---
# <a name="test-drive-technical-configuration"></a>Configuração técnica de test drive

A opção test drive no Microsoft Commercial Marketplace permite que você configure um tour prático e autoguiado dos principais recursos do produto. Com um test drive, novos clientes podem experimentar sua oferta antes de confirmar para comprá-la. Para saber mais, consulte [O que é um test drive?](what-is-test-drive.md)

Se você não quiser mais fornecer um test drive para sua oferta, retorne à página de **instalação da oferta** e desmarque a caixa de seleção **habilitar Test Drive** . Nem todos os tipos de oferta têm um test drive disponível.

## <a name="azure-resource-manager-test-drive"></a>Azure Resource Manager test drive

Essa é a única opção test drive para a máquina virtual ou ofertas de aplicativo do Azure e requer uma configuração bastante detalhada. Leia as seções abaixo para [Ver os detalhes da assinatura de implantação](#deployment-subscription-details) e as [listagens do Test Drive](#test-drive-listings)e, em seguida, continue com o tópico separado para [Azure Resource Manager configuração de Test Drive](azure-resource-manager-test-drive.md).

## <a name="hosted-test-drive"></a>test drive hospedado

A Microsoft pode remover a complexidade de configurar um test drive hospedando e mantendo o provisionamento e o desprovisionamento de serviços. A configuração para esse tipo de test drive é a mesma, independentemente de o test drive ter como alvo um cliente do Dynamics 365 ou um público de operações do Dynamics 365.

- **Máximo de unidades de test drive simultâneas** (obrigatório) – defina o número máximo de clientes que podem usar seu test drive simultaneamente. Cada usuário simultâneo consumirá uma licença do Dynamics 365 enquanto o test drive estiver ativo, portanto, verifique se você tem licenças suficientes disponíveis para dar suporte ao limite máximo definido. O valor recomendado é 3-5.

- **Duração do teste de unidade** (obrigatório) – Insira o número de horas que o Test Drive permanecerá ativo para cada cliente. Após esse período, a sessão será encerrada e não consumirá mais uma de suas licenças. Recomendamos um valor de 2 a 24 horas, dependendo da complexidade da oferta. Essa duração só pode ser definida em horas inteiras (por exemplo, "2 horas" é válida; "1,5 horas" não é). O usuário poderá solicitar uma nova sessão se atingir o tempo limite e desejar acessar o test drive novamente.

- **URL da instância** (obrigatório) – a URL em que o cliente começará o test drive dele. Normalmente, a URL da instância do Dynamics 365 executando o aplicativo com os dados de exemplo instalados (por exemplo, `https://testdrive.crm.dynamics.com`).

- **URL da API Web da instância** (necessária) – recupere a URL da API da Web para sua instância do Dynamics 365 fazendo logon em sua conta de Microsoft 365 e navegando até **configurações**  >  **personalização**  >  **recursos do desenvolvedor**  >  **instância API Web (URL raiz do serviço)**, copie a URL encontrada aqui (por exemplo, `https://testdrive.crm.dynamics.com/api/data/v9.0` ).

- **Nome da função** (obrigatório) – forneça o nome da função de segurança que você definiu em seu test drive personalizado do Dynamics 365, o qual será atribuído ao usuário durante o test drive (por exemplo, test-drive-role).

Para obter ajuda sobre como configurar seu ambiente do Dynamics 365 para test drive e conceder permissão AppSource para provisionar e desprovisionar test drive usuários em seu locatário, siga [estas instruções](./test-drive-azure-subscription-setup.md).

Para obter instruções passo a passo sobre como listar e configurar sua unidade de teste hospedada, visite a página [configuração detalhada para o Test Drive hospedado](./test-drive-hosted-detailed-config.md) .

## <a name="logic-app-test-drive"></a>test drive de aplicativo lógico

Esse tipo de test drive não é hospedado pela Microsoft. Use-o para se conectar a uma oferta do Dynamics 365 ou a outro recurso personalizado, que abrange uma variedade de arquiteturas de solução complexas. Para obter mais informações sobre como configurar test drives de Aplicativo Lógico, visite [Operations](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md) e [Customer Engagement](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app) no GitHub.

- **Região** (obrigatório, lista suspensa de seleção única) – atualmente, há 26 regiões compatíveis com o Azure onde seu test drive pode ser disponibilizado. Os recursos para seu aplicativo lógico serão implantados na região que você selecionar. Se o Aplicativo Lógico tiver recursos personalizados armazenados em uma região específica, verifique se essa região está selecionada aqui. A melhor maneira de implantar de forma integral o Aplicativo Lógico localmente em sua assinatura do Azure no portal e verificar se ele funciona antes de gravá-lo aqui.

- **Máximo de unidades de test drive simultâneas** (obrigatório) – defina o número máximo de clientes que podem usar seu test drive simultaneamente. Essas unidades de teste já estão implantadas, permitindo que os clientes as acessem instantaneamente sem esperar por uma implantação.

- **Duração do test drive** (obrigatório) – insira o período durante o qual o test drive permanecerá ativo, em número de horas. O test drive é encerrado automaticamente após o término desse período de tempo.

- **Nome do grupo de recursos do Azure** (obrigatório) – Insira o nome do [grupo de recursos do Azure](../azure-resource-manager/management/overview.md#resource-groups), em que seu aplicativo lógico Test Drive é salvo.

- **Nome do Aplicativo Lógico do Azure** (obrigatório) – insira o nome do Aplicativo Lógico que atribui o test drive ao usuário. Esse Aplicativo Lógico precisa ser salvo no grupo de recursos do Azure mencionado acima.

- **Desprovisionar nome do aplicativo lógico** (obrigatório) – Insira o nome do aplicativo lógico que desprovisiona a Test Drive quando o cliente é concluído. Esse Aplicativo Lógico precisa ser salvo no grupo de recursos do Azure mencionado acima.

## <a name="power-bi-test-drive"></a>Test Drive do Power BI

Os produtos que desejam demonstrar um visual interativo do Power BI podem usar um link incorporado para compartilhar um dashboard personalizado como o test drive deles, sem necessidade de nenhuma configuração técnica adicional. Tudo o que você precisa fazer aqui é carregar sua URL de Power BI inserida.

Para obter mais informações sobre como configurar aplicativos Power BI, consulte [o que são aplicativos Power bi?](/power-bi/service-template-apps-overview)

## <a name="deployment-subscription-details"></a>Detalhes da assinatura de implantação

Para permitir que a Microsoft implante o test drive em seu nome, crie e forneça uma assinatura do Azure separada e exclusiva (não é obrigatória para unidades de teste do Power BI).

- **ID da assinatura do Azure** (necessário para Azure Resource Manager e Aplicativos Lógicos) – insira a ID da assinatura para permitir acesso aos serviços de conta do Azure para cobrança e relatório de uso de recursos. Recomendamos que você considere [criar uma assinatura separada do Azure](../cost-management-billing/manage/create-subscription.md) para usar em test drives, caso ainda não tenha uma. Você pode encontrar sua ID da assinatura do Azure entrando no [portal do Azure](https://portal.azure.com/) e navegando até a guia **Assinaturas** no menu do lado esquerdo. A seleção dessa guia exibirá sua ID da assinatura (por exemplo, "a83645ac-1234-5ab6-6789-1h234g764ghty").

- **ID de locatário do Azure ad** (obrigatório) – Insira sua ID de [locatário](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)do Azure Active Directory (AD)). Para localizar essa ID, entre no [portal do Azure](https://portal.azure.com/), selecione a guia Active Directory no menu à esquerda, selecione **Propriedades** e, em seguida, procure o número da **ID do diretório** listado (por exemplo, 50c464d3-4930-494c-963c-1e951d15360e). Você também pode procurar a ID do locatário da sua organização usando seu endereço de nome de domínio em [https://www.whatismytenantid.com](https://www.whatismytenantid.com).

- **Nome do locatário do Azure AD** (necessário para o Dynamics 365) – insira seu nome do Azure AD (Active Directory). Para localizar esse nome, entre no [portal do Azure](https://portal.azure.com/), no canto superior direito, o nome do locatário estará listado abaixo do seu nome de conta.

- **ID do aplicativo do Azure ad** (obrigatório) – Insira sua ID do [aplicativo](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)Azure Active Directory (AD)). Para localizar essa ID, entre no [portal do Azure](https://portal.azure.com/), selecione a guia Active Directory no menu à esquerda, selecione **registros de aplicativo** e procure o número de **ID do aplicativo** listado (como `50c464d3-4930-494c-963c-1e951d15360e` ).

- **Segredo do cliente do aplicativo do Azure ad** (obrigatório) – Insira o [segredo do cliente](../active-directory/develop/howto-create-service-principal-portal.md#option-2-create-a-new-application-secret)do aplicativo do Azure AD). Para encontrar esse valor, entre no [portal do Azure](https://portal.azure.com/). Selecione a guia **Azure Active Directory** no menu à esquerda, selecione **registros de aplicativo** e selecione seu aplicativo Test Drive. Em seguida, **Selecione certificados e segredos**, selecione **novo segredo do cliente**, insira uma descrição, selecione **nunca** em **expirar** e escolha **Adicionar**. Certifique-se de anotar o valor. Não navegue para fora da página antes de copiar o valor.

## <a name="test-drive-listings"></a>Testar listagens de unidades

A opção **Test Drive listagens** encontrada na guia **Test Drive** no Partner Center exibe os idiomas (e os mercados) em que o Test Drive está disponível, atualmente inglês (Estados Unidos) é o único local disponível. Além disso, essa página exibe o status da listagem específica a um idioma e a data/hora em que ela foi adicionada. Você precisará definir os detalhes da test drive (descrição, manual do usuário, vídeos, etc.) para cada idioma/mercado.

- **Descrição** (obrigatório): descreva sua Test Drive, o que será demonstrado, os objetivos para o usuário experimentar, os recursos a serem explorados e todas as informações relevantes para ajudar o usuário a determinar se deseja adquirir sua oferta. Até 3.000 caracteres de texto podem ser inseridos neste campo.

- **Informações de acesso** (necessárias para unidades de teste de Azure Resource Manager e lógica): explique o que um cliente precisa saber para acessar e usar esse Test Drive. Faça um passo a passo para um cenário de uso de sua oferta e exatamente o que o cliente deve saber para acessar recursos durante o test drive. Até 10.000 caracteres de texto podem ser inseridos neste campo.

- **Manual do usuário** (obrigatório): um passo a passo detalhado de sua experiência de Test Drive. O manual do usuário precisa abranger exatamente o que você deseja que os clientes ganhem com a experiência de fazer o test drive e deve servir como uma referência para quaisquer perguntas que eles possam ter. O arquivo precisa estar no formato PDF e ser nomeado (máximo de 255 caracteres) após o upload.

- **Vídeos: adicionar vídeos** (opcional): Vídeos hospedados em outro lugar podem ser referenciados aqui com uma imagem de link e miniatura (533 x 324 pixels) para que um cliente possa exibir um passo a passo de informações para ajudá-los a entender melhor os Test Drive, incluindo como usar com êxito os recursos de sua oferta e entender os cenários que destacam seus benefícios.
  - **Nome** (obrigatório)
  - **URL** (somente YouTube ou Vimeo; obrigatório)
  - **Miniatura** (533 x 324 pixels) – a imagem deve estar no formato png.

Se estiver criando seu test drive no Partner Center, selecione **salvar rascunho** antes de continuar.

Para obter instruções passo a passo sobre como listar e configurar sua unidade de teste hospedada, visite a página [configuração detalhada para o Test Drive hospedado](./test-drive-hosted-detailed-config.md) .

## <a name="additional-resources"></a>Recursos adicionais

## <a name="next-steps"></a>Próximas etapas

- [Práticas recomendadas do Test Drive](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Visão geral](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) (PDF; Verifique se o bloqueador de pop-ups está desativado)
- [Atualizar uma oferta existente no marketplace comercial](partner-center-portal/update-existing-offer.md)