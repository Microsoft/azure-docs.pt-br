---
title: Visão geral da política personalizada de Azure AD B2C | Microsoft Docs
description: Um tópico sobre as políticas personalizadas do Azure Active Directory B2C e a Estrutura de Experiência de Identidade.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/14/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: cb33e11af26d5f5a2676f5b236ac142179bdb550
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99592833"
---
# <a name="azure-ad-b2c-custom-policy-overview"></a>Visão geral da política personalizada Azure AD B2C

As políticas personalizadas são arquivos de configuração que definem o comportamento do locatário do Azure AD B2C (Azure Active Directory B2C). Embora os [fluxos de usuário](user-flow-overview.md) sejam predefinidos no portal de Azure ad B2C para as tarefas de identidade mais comuns, as políticas personalizadas podem ser totalmente editadas por um desenvolvedor de identidade para concluir várias tarefas diferentes.

Uma política personalizada é totalmente configurável e orientada por políticas. Uma política personalizada orquestra a relação de confiança entre entidades em formatos de protocolo padrão, como OpenID Connect, OAuth, SAML e alguns não padrão, por exemplo, trocas de declarações do sistema-para-sistema com base na API REST. A estrutura cria experiências com nome de usuário e em branco.

Uma política personalizada é representada como um ou vários arquivos formatados em XML que se referenciam entre si em uma cadeia hierárquica. Os elementos XML definem os blocos de construção, a interação com o usuário e outras partes e a lógica de negócios. 

## <a name="custom-policy-starter-pack"></a>Pacote de início de política personalizada

Azure AD B2C pacote de [início](custom-policy-get-started.md#get-the-starter-pack) de política personalizada vem com várias políticas predefinidas para ajudá-lo a ir rapidamente. Cada um desses pacotes de início contém o menor número de perfis de técnicos e percurso do usuário necessários para alcançar os cenários descritos:

- **LocalAccounts** – permite o uso apenas de contas locais.
- **SocialAccounts** – permite o uso apenas de contas sociais (ou federadas).
- **SocialAndLocalAccounts** – permite o uso de contas locais e de contas sociais. A maioria dos nossos exemplos referem-se a essa política.
- **SocialAndLocalAccountsWithMFA** – permite opções de autenticação locais, sociais e multifator.

## <a name="understanding-the-basics"></a>Noções básicas sobre noções básicas 

### <a name="claims"></a>Declarações

Uma declaração fornece armazenamento temporário de dados durante uma execução de política do Azure AD B2C. Ele pode armazenar informações sobre o usuário, como nome, sobrenome ou qualquer outra declaração obtida do usuário ou de outros sistemas (trocas de declarações). O [esquema de declarações](claimsschema.md) é o lugar em que você declara suas declarações. 

Quando a política é executada, o Azure AD B2C envia e recebe declarações de e para partes internas e externas e, em seguida, envia um subconjunto dessas declarações para seu aplicativo de terceira parte confiável como parte do token. As declarações são usadas das seguintes maneiras: 

- Uma declaração é salva, lida ou atualizada no objeto de usuário do diretório.
- Uma declaração é recebida de um provedor de identidade externo.
- As declarações são enviadas ou recebidas usando um serviço de API REST personalizado.
- Os dados são coletados como declarações do usuário durante os fluxos de inscrição ou edição de perfil.

### <a name="manipulating-your-claims"></a>Manipulando suas declarações

As [transformações de declarações](claimstransformations.md) são funções predefinidas que podem ser usadas para converter uma determinada declaração em outra, avaliar uma declaração ou definir um valor de declaração. Por exemplo, adicionar um item a uma coleção de cadeias de caracteres, alterar o caso de uma cadeia de caracteres ou avaliar uma declaração de data e hora. Uma transformação de declarações especifica um método de transformação. 

### <a name="customize-and-localize-your-ui"></a>Personalizar e localizar sua interface do usuário

Quando você quiser coletar informações de seus usuários apresentando uma página em seu navegador da Web, use o [perfil técnico autodeclarado](self-asserted-technical-profile.md). Você pode editar seu perfil técnico autodeclarado para [adicionar declarações e personalizar a entrada do usuário](./configure-user-input.md).

Para [Personalizar a interface do usuário](customize-ui-with-html.md) para seu perfil técnico autodeclarado, ESPECIFIQUE uma URL no elemento de [definição de conteúdo](contentdefinitions.md) com conteúdo HTML personalizado. No perfil técnico autodeclarado, você aponta para essa ID de definição de conteúdo.

Para personalizar cadeias de caracteres específicas do idioma, use o elemento de [localização](localization.md) . Uma definição de conteúdo pode conter uma referência de [localização](localization.md) que especifica uma lista de recursos localizados a serem carregados. O Azure AD B2C mescla os elementos da interface do usuário com o conteúdo HTML carregado da URL e exibe a página ao usuário. 

## <a name="relying-party-policy-overview"></a>Visão geral da política de terceira parte confiável

Um aplicativo de terceira parte confiável, que no protocolo SAML é conhecido como um provedor de serviços, chama a política de terceira parte [confiável](relyingparty.md) para executar um percurso de usuário específico. A política de terceira parte confiável especifica a jornada do usuário a ser executada e a lista de declarações que o token inclui. 

![Diagrama mostrando o fluxo de execução da política](./media/custom-policy-trust-frameworks/custom-policy-execution.png)

Todos os aplicativos de terceira parte confiável que usam a mesma política receberão as mesmas declarações de token e o usuário passará pela mesma jornada do usuário.

### <a name="user-journeys"></a>Percursos do usuário

As [viagens do usuário](userjourneys.md) permitem que você defina a lógica de negócios com o caminho pelo qual o usuário seguirá para obter acesso ao seu aplicativo. O usuário é levado pelo percurso do usuário para recuperar as declarações que devem ser apresentadas ao seu aplicativo. Uma jornada do usuário é criada a partir de uma sequência de [etapas de orquestração](userjourneys.md#orchestrationsteps). Um usuário deve acessar a última etapa para adquirir um token. 

As instruções a seguir descrevem como você pode adicionar etapas de orquestração à política de [inicialização de conta social e local](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/SocialAndLocalAccounts) . Aqui está um exemplo de uma chamada à API REST que foi adicionada.

![jornada do usuário personalizado](media/custom-policy-trust-frameworks/user-journey-flow.png)


### <a name="orchestration-steps"></a>Etapas de orquestração

A etapa de orquestração faz referência a um método que implementa sua finalidade ou funcionalidade pretendida. Esse método é chamado de [perfil técnico](technicalprofiles.md). Quando o percurso do usuário precisa de ramificação para representar melhor a lógica de negócios, a etapa de orquestração faz referência à [sub-rotina](subjourneys.md). Uma sub-rotina contém seu próprio conjunto de etapas de orquestração.

Um usuário deve acessar a última etapa de orquestração no percurso do usuário para adquirir um token. Mas os usuários talvez não precisem percorrer todas as etapas de orquestração. As etapas de orquestração podem ser executadas condicionalmente com base nas [pré-condições](userjourneys.md#preconditions) definidas na etapa de orquestração. 

Após a conclusão de uma etapa de orquestração, Azure AD B2C armazena as declarações de saída na **bolsa de declarações**. As declarações no recipiente de declarações podem ser utilizadas por outras etapas de orquestração na jornada do usuário.

O diagrama a seguir mostra como as etapas de orquestração da jornada do usuário podem acessar o recipiente de declarações.

![Jornada do usuário Azure AD B2C](media/custom-policy-trust-frameworks/user-journey-diagram.png)

### <a name="technical-profile"></a>Perfil técnico

Um perfil técnico fornece uma interface para se comunicar com diferentes tipos de partes. Um percurso do usuário combina a chamada de perfis técnicos por meio de etapas de orquestração para definir sua lógica de negócios.

Todos os tipos de perfis técnicos compartilham o mesmo conceito. Você envia declarações de entrada, executa a transformação de declarações e comunica-se com a parte configurada. Depois que o processo for concluído, o perfil técnico retornará as declarações de saída para o recipiente de declarações. Para obter mais informações, consulte [visão geral de perfis técnicos](technicalprofiles.md).

### <a name="validation-technical-profile"></a>Perfil técnico de validação

Quando um usuário interage com a interface do usuário, talvez você queira validar os dados coletados. Para interagir com o usuário, um [perfil técnico autodeclarado](self-asserted-technical-profile.md) deve ser usado.

Para validar a entrada do usuário, um [perfil técnico de validação](validation-technical-profile.md) é chamado do perfil técnico autodeclarado. Um perfil técnico de validação é um método para chamar qualquer perfil técnico não interativo. Nesse caso, o perfil técnico pode retornar declarações de saída ou uma mensagem de erro. A mensagem de erro é renderizada para o usuário na tela, permitindo que o usuário tente novamente.

O diagrama a seguir ilustra como o Azure AD B2C usa um perfil técnico de validação para validar as credenciais do usuário.

![Diagrama de perfil técnico de validação](media/custom-policy-trust-frameworks/validation-technical-profile.png)

## <a name="inheritance-model"></a>Modelo de herança

Cada pacote de início inclui os seguintes arquivos:

- Um arquivo de **Base** que contém a maioria das definições. Para ajudar na solução de problemas e na manutenção de longo prazo de suas políticas, tente minimizar o número de alterações feitas nesse arquivo.
- Um arquivo de **Extensões** que contém as alterações de configuração exclusivas para seu locatário. Esse arquivo de política é derivado do arquivo de Base. Use esse arquivo para adicionar novas funcionalidades ou substituir a funcionalidade existente. Por exemplo, use esse arquivo para federar com novos provedores de identidade.
- Um arquivo **RP (Terceira Parte Confiável)** que é o único arquivo centrado em tarefa invocado diretamente pelo aplicativo de terceira parte confiável, como seus aplicativos Web, móveis ou da área de trabalho. Cada tarefa exclusiva, como inscrição, entrada, redefinição de senha ou edição de perfil, requer seu próprio arquivo de política de terceira parte confiável. Esse arquivo de política é derivado do arquivo de extensões.

O modelo de herança é assim:

- A política filho em qualquer nível pode herdar de política pai e estendê-la adicionando novos elementos.
- Para cenários mais complexos, você pode adicionar mais níveis de herança (até 10 no total).
- Você pode adicionar mais políticas de terceira parte confiável. Por exemplo, exclua minha conta, altere um número de telefone, política de terceira parte confiável SAML e muito mais.

O diagrama a seguir mostra a relação entre os arquivos de política e os aplicativos de terceira parte confiável.

![Diagrama mostrando o modelo de herança de política de estrutura confiável](media/custom-policy-trust-frameworks/policies.png)


## <a name="guidance-and-best-practices"></a>Diretrizes e melhores práticas

### <a name="best-practices"></a>Práticas recomendadas

Dentro de uma Azure AD B2C política personalizada, você pode integrar sua própria lógica de negócios para criar as experiências do usuário necessárias e estender a funcionalidade do serviço. Temos um conjunto de práticas recomendadas e recomendações para começar.

- Crie sua lógica dentro da **política de extensão** ou da política de terceira parte **confiável**. Você pode adicionar novos elementos, que substituirão a política base referenciando a mesma ID. Isso permitirá que você Escale horizontalmente seu projeto enquanto torna mais fácil atualizar a política base posteriormente, se a Microsoft lançar novos pacotes de início.
- Na **política de base**, é altamente recomendável evitar fazer qualquer alteração. Quando necessário, faça comentários onde as alterações são feitas.
- Quando você estiver substituindo um elemento, como metadados de perfil técnico, evite copiar o perfil técnico inteiro da política de base. Em vez disso, copie apenas a seção necessária do elemento. Consulte [desabilitar a verificação de email](./disable-email-verification.md) para obter um exemplo de como fazer a alteração.
- Para reduzir a duplicação de perfis técnicos, onde a funcionalidade principal é compartilhada, use a [inclusão de perfil técnico](technicalprofiles.md#include-technical-profile).
- Evite gravar no diretório do AD do Azure durante a entrada, o que pode levar à limitação de problemas.
- Se sua política tiver dependências externas, como APIs REST, verifique se elas estão altamente disponíveis.
- Para uma melhor experiência de usuário, verifique se seus modelos HTML personalizados são implantados globalmente usando a [distribuição de conteúdo online](../cdn/index.yml). A CDN (rede de distribuição de conteúdo) do Azure permite reduzir os tempos de carregamento, economizar largura de banda e melhorar a velocidade de resposta.
- Se você quiser fazer uma alteração no percurso do usuário, copie todo o percurso do usuário da política de base para a política de extensão. Forneça uma ID de jornada do usuário exclusiva para o percurso do usuário que você copiou. Em seguida, na política de terceira parte [confiável](relyingparty.md), altere o elemento de [jornada do usuário padrão](relyingparty.md#defaultuserjourney) para apontar para o novo percurso do usuário.

## <a name="troubleshooting"></a>Solução de problemas

Ao desenvolver com políticas de Azure AD B2C, você pode encontrar erros ou exceções ao executar a jornada do usuário. O pode ser investigado usando Application Insights.

- Integre Application Insights com Azure AD B2C para [diagnosticar exceções](troubleshoot-with-application-insights.md).
- A [extensão de Azure ad B2C para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=AzureADB2CTools.aadb2c) pode ajudá-lo [a acessar e visualizar os logs](https://github.com/azure-ad-b2c/vscode-extension/blob/master/src/help/app-insights.md) com base em um nome de política e hora.
- O erro mais comum na configuração de políticas personalizadas é XML com erro de formatação. Use a [validação de esquema XML](troubleshoot-custom-policies.md) para identificar erros antes de carregar o arquivo XML.

## <a name="continuous-integration"></a>Integração contínua

Usando um pipeline de CI/CD (integração e entrega contínuas) que você configura no Azure Pipelines, você pode [incluir suas Azure ad B2C políticas personalizadas em sua distribuição de software](deploy-custom-policies-devops.md) e automação de controle de código. Conforme você implanta em diferentes ambientes de Azure AD B2C, por exemplo, desenvolvimento, teste e produção, recomendamos que você remova processos manuais e execute testes automatizados usando Azure Pipelines.

## <a name="prepare-your-environment"></a>Prepare o seu ambiente

Você começa a usar Azure AD B2C política personalizada:

1. [Criar um locatário do Azure AD B2C](tutorial-create-tenant.md)
1. [Registre um aplicativo Web](tutorial-register-applications.md) usando o portal do Azure para que você possa testar sua política.
1. Adicione as [chaves de política](custom-policy-get-started.md#add-signing-and-encryption-keys) necessárias e [registre os aplicativos da estrutura de experiência de identidade](custom-policy-get-started.md#register-identity-experience-framework-applications).
1. [Obtenha o pacote de início do Azure ad B2C Policy](custom-policy-get-started.md#get-the-starter-pack) e carregue-o em seu locatário. 
1. Depois de carregar o pacote de início, [teste sua política de inscrição ou de entrada](custom-policy-get-started.md#test-the-custom-policy).
1. Recomendamos que você baixe e instale o [Visual Studio Code](https://code.visualstudio.com/) (vs Code). Visual Studio Code é um editor de código-fonte leve, mas poderoso, que é executado em sua área de trabalho e está disponível para Windows, macOS e Linux. Com o VS Code, você pode navegar rapidamente e editar seus arquivos XML da política personalizada Azure AD B2C instalando a [extensão Azure ad B2C para vs Code](https://marketplace.visualstudio.com/items?itemName=AzureADB2CTools.aadb2c)
 
## <a name="next-steps"></a>Próximas etapas

Depois de configurar e testar sua política de Azure AD B2C, você pode começar a personalizar sua política. Siga os artigos a seguir para saber como:

- [Adicione declarações e personalize a entrada do usuário](./configure-user-input.md) usando políticas personalizadas. Saiba como definir uma declaração e adicionar uma declaração à interface do usuário, personalizando alguns dos perfis técnicos do pacote inicial.
- [Personalize a interface do usuário](customize-ui-with-html.md) do seu aplicativo usando uma política personalizada. Saiba como criar seu próprio conteúdo HTML e personalizar a definição de conteúdo.
- [Localize a interface do usuário](./language-customization.md) do seu aplicativo usando uma política personalizada. Saiba como configurar a lista de idiomas com suporte e fornecer rótulos específicos do idioma, adicionando o elemento recursos localizados.
- Durante o desenvolvimento e o teste de sua política, você pode [desabilitar a verificação de email](./disable-email-verification.md). Saiba como substituir os metadados de um perfil técnico.
- [Configure a entrada com uma conta do Google](./identity-provider-google.md) usando políticas personalizadas. Saiba como criar um novo provedor de declarações com o perfil técnico do OAuth2. Em seguida, personalize a jornada do usuário para incluir a opção de entrada do Google.
- Para diagnosticar problemas com suas políticas personalizadas, você pode [coletar logs de Azure Active Directory B2C com Application insights](troubleshoot-with-application-insights.md). Saiba como adicionar novos perfis técnicos e configurar sua política de terceira parte confiável.
