---
title: "Azure Active Directory B2C: Políticas personalizadas | Microsoft Docs"
description: "Um tópico sobre as políticas personalizadas do Azure Active Directory B2C"
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: krassk
editor: parakhj
ms.assetid: 1ff398a4-2079-4615-94f1-57de22c0aad6
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/04/2017
ms.author: parakhj
ms.openlocfilehash: 25dada7bc04449c6e527b94d97780d9aef1c33a9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-custom-policies"></a>Azure Active Directory B2C: Políticas personalizadas

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

## <a name="what-are-custom-policies"></a>O que são políticas personalizadas?

Políticas personalizadas são arquivos de configuração que definem o comportamento do locatário do Azure AD B2C. Enquanto as **políticas internas** são predefinidas no portal do Azure AD B2C para as tarefas mais comuns de identidade, as políticas personalizadas podem ser editadas por completo por um desenvolvedor de identidade para concluir um número quase ilimitado de tarefas. Continue lendo para determinar se as políticas personalizadas são ideais para você e seu cenário de identidade.

## <a name="comparing-built-in-policies-and-custom-policies"></a>Comparando políticas internas e personalizadas

| | Políticas internas | Políticas personalizadas |
|-|-------------------|-----------------|
|Usuários de destino | Todos os desenvolvedores de aplicativos com ou sem conhecimentos sobre identidade | Profissionais de identidade: integradores de sistemas, consultores e equipes internas de identidade. Estão familiarizados com fluxos do OpenIDConnect e têm conhecimento sobre a autenticação baseada em declarações e os provedores de identidade |
|Método de configuração | Portal do Azure com uma interface do usuário amigável | Editando arquivos XML diretamente e carregando-os no portal do Azure |
|Personalização da interface do usuário | Personalização completa da interface do usuário, incluindo suporte a HTML, CSS e javascript (exige um domínio personalizado)<br><br>Suporte a várias linguagens com cadeias de caracteres Personalizadas | Idêntico |
| Personalização de atributos | Atributos padrão e personalizados | Idêntico |
|Gerenciamento de tokens e sessões | Token personalizado e opções de várias sessões | Idêntico |
|Provedores de Identidade| **Hoje**: provedor social e local predefinido<br><br>**Futuro**: OIDC baseado em padrões, SAML, OAuth | **Hoje**: OIDC baseado em padrões, OAUTH, SAML<br><br>**Futuro**: WsFed |
|Tarefas de identidade (exemplos) | Inscrever-se ou entrar com contas locais e várias contas sociais<br><br>Redefinição de senha de autoatendimento<br><br>Edição de perfil<br><br>Cenários de Autenticação Multifator<br><br>Personalizar tokens e sessões<br><br>Acessar fluxos de token | Concluir as mesmas tarefas que as políticas internas usando provedores de identidade ou escopos personalizados<br><br>Provisionar um usuário em outro sistema no momento do registro<br><br>Enviar um email de boas-vindas usando seu próprio provedor de serviços de email<br><br>Usar um repositório de usuários fora do B2C<br><br>Validar as informações fornecidas pelo usuário com um sistema confiável por meio da API |

## <a name="policy-files"></a>Arquivos de política

Uma política personalizada é representada como um ou vários arquivos formatados em XML que se referenciam entre si em uma cadeia hierárquica. Os elementos XML definem: esquema de declarações, transformações de declarações, definições de conteúdo, provedores de declarações/perfis técnicos e etapas de orquestração de Userjourney, entre outros elementos.

Recomendamos o uso de três tipos de arquivos de política:

- **Um arquivo BASE**, que contém a maioria das definições e para a qual o Azure fornece uma amostra completa.  Recomendamos fazer um número mínimo de alterações nesse arquivo para ajudar na solução de problemas e na manutenção de longo prazo das políticas
- **um arquivo de EXTensões** que contém as alterações de configuração exclusivas para seu locatário
- **um arquivo RP (Terceira Parte Confiável)** que é o único arquivo centrado na tarefa que é invocado diretamente pelo aplicativo ou serviço (também conhecido como Terceira Parte Confiável).  Leia o artigo sobre definições do arquivo de Política para obter mais informações.  Cada tarefa exclusiva exige seu próprio RP e, dependendo dos requisitos de identidade visual, o número pode ser “total de aplicativos x número total de casos de uso”.


As políticas internas do Azure AD B2C seguem o padrão de 3 arquivos descrito acima, mas o desenvolvedor vê apenas o arquivo RP (Terceira Parte Confiável), enquanto o portal faz alterações em segundo plano no arquivo de EXTensões.

## <a name="core-concepts-you-should-know-when-using-custom-policies"></a>Conceitos fundamentais que você deve saber ao usar políticas personalizadas

### <a name="azure-active-directory-b2c"></a>Active Directory B2C do Azure

Serviço de CIAM (gerenciamento de identidades e acessos do cliente) do Azure. O serviço inclui:

1. Um diretório de usuários na forma de um Azure Active Directory de uso especial acessível pelo Microsoft Graph e que mantém os dados de usuário de contas locais e contas federadas 
2. Acesso à **Estrutura de Experiência de Identidade** que orquestra a relação de confiança entre usuários e entidades e passa as declarações entre eles para concluir uma tarefa de gerenciamento de identidade/acesso 
3. Um STS (serviço de token de segurança) que emite tokens de ID, tokens de atualização e tokens de acesso (e declarações SAML equivalentes) e valida-os para proteger os recursos.

O Azure AD B2C interage com provedores de identidade, usuários, outros sistemas e com o diretório de usuários local em sequência para realizar uma tarefa de identidade (por exemplo, logon de um usuário, registro de um novo usuário, redefinição de uma senha). A plataforma subjacente que estabelece a relação de confiança de várias terceiras partes e executa essas etapas é chamada de Estrutura de Experiência de Identidade e uma política (também chamada de um percurso do usuário ou uma política de estrutura confiável) define explicitamente os atores, as ações, os protocolos e a sequência de etapas a serem concluídas.

### <a name="identity-experience-framework"></a>Identity Experience Framework

Uma plataforma do Azure totalmente configurável, orientada por política e baseada em nuvem que orquestra a confiança entre entidades (amplamente, Provedores de Declarações) nos formatos de protocolo padrão, como OpenIDConnect, OAuth, SAML, WSFed e alguns não padrão (por exemplo, trocas de declarações entre sistemas baseadas na API REST). O I2E cria experiências amigáveis ao usuário na lista de permissões que dão suporte a HTML, CSS e javascript.  Atualmente, a Estrutura de Experiência de Identidade está disponível apenas no contexto do serviço Azure AD B2C e é priorizada para tarefas relacionadas ao CIAM.

### <a name="built-in-policies"></a>Políticas internas

Arquivos de configuração predefinidos que direcionam o comportamento do Azure AD B2C para realizar as tarefas de identidade mais comumente usadas (ou seja, registro de usuário, entrar, redefinição de senha) e interagir com partes confiáveis cuja relação também é predefinida no Azure AD B2C (por exemplo, provedor de identidade do Facebook, LinkedIn, conta da Microsoft, contas do Google).  No futuro, as políticas internas também poderão fornecer personalização de provedores de identidade que costumam estar no realm empresarial, como Azure Active Directory Premium, Active Directory/ADFS, provedor de ID do Salesforce etc.


### <a name="custom-policies"></a>Políticas personalizadas

Arquivos de configuração que definem o comportamento da Estrutura de Experiência de Identidade no seu locatário do Azure AD B2C. Uma política personalizada está acessível como um ou vários arquivos XML (consulte definições de Arquivos de Política) que são executados pela Estrutura de Experiência de Identidade quando invocados por uma terceira parte confiável (por exemplo, um aplicativo). As políticas personalizadas podem ser editadas diretamente por um desenvolvedor de identidade para realizar um número quase ilimitado de tarefas. Os desenvolvedores que configuram as políticas personalizadas devem definir as relações de confiança em detalhes para incluir pontos de extremidade de metadados, definições exatas de troca de declarações e configurar segredos, chaves e certificados, conforme necessário, para cada provedor de identidade.

## <a name="policy-file-definitions-for-identity-experience-framework-trustframeworks"></a>Definições de arquivo de política para Trustframeworks da Estrutura de Experiência de Identidade

### <a name="policy-files"></a>Arquivos de Política

Uma política personalizada é representada como um ou vários arquivos formatados em XML que se referenciam entre si em uma cadeia hierárquica. Os elementos XML definem: esquema de declarações, transformações de declarações, definições de conteúdo, provedores de declarações/perfis técnicos e etapas de orquestração de percurso do usuário, entre outros elementos.  Recomendamos o uso de três tipos de arquivos de política:

- **Um arquivo BASE** contém a maioria das definições e para o qual o Azure fornece uma amostra completa.  Recomendamos fazer um número mínimo de alterações nesse arquivo para ajudar na solução de problemas e na manutenção de longo prazo das políticas
- **um arquivo de EXTensões** que contém as alterações de configuração exclusivas para seu locatário
- **um arquivo RP (Terceira Parte Confiável)** é o arquivo único centrado na tarefa invocado diretamente pelo aplicativo ou serviço (também conhecido como Terceira Parte Confiável).  Leia o artigo sobre definições do arquivo de Política para obter mais informações.  Cada tarefa exclusiva exige seu próprio RP e, dependendo dos requisitos de identidade visual, o número pode ser “total de aplicativos x número total de casos de uso”.

![Tipos de arquivo de política](media/active-directory-b2c-overview-custom/active-directory-b2c-overview-custom-policy-files.png)

| Tipo de arquivo de política | Nome de arquivo de exemplo | Uso recomendado | É herdado de |
|---------------------|--------------------|-----------------|---------------|
| BASE |TrustFrameworkBase.xml<br><br>Mytenant.onmicrosoft.com-B2C-1A_BASE1.xml | Inclui o esquema principal de declarações, transformações de declarações, provedores de declarações e percursos do usuário configurados pela Microsoft<br><br>Fazer alterações mínimas nesse arquivo | Nenhum |
| Extensão (EXT) | TrustFrameworkExtensions.xml<br><br>Mytenant.onmicrosoft.com-B2C-1A_EXT.xml | Consolidar as alterações no arquivo BASE aqui<br><br>Provedores de declarações modificados<br><br>Percursos do usuário modificados<br><br>Suas próprias definições de esquema personalizadas | Arquivo BASE |
| RP (Terceira Parte Confiável) | B2C_1A_sign_up_sign_in.xml| Altere as configurações de forma e sessão do token aqui| Arquivo Extensions(EXT) |

### <a name="inheritance-model"></a>Modelo de herança

Quando um aplicativo chamar o arquivo de Política da RP, a Estrutura de Experiência de Identidade no B2C adicionará todos os elementos de BASE, depois, de EXTENSÕES e, por fim, do arquivo de política da RP para montar a política atual em vigor.  Elementos do mesmo tipo e com o mesmo nome no arquivo RP substituirão aqueles nas EXTENSÕES e as EXTENSÕES substituem a BASE.

As **políticas internas** do Azure AD B2C seguem o padrão de 3 arquivos descrito acima, mas o desenvolvedor vê apenas o arquivo RP (Terceira Parte Confiável), enquanto o portal faz alterações em segundo plano no arquivo de EXTensões.  Todo o Azure AD B2C compartilha um arquivo de política BASE que está sob o controle da equipe do Azure B2C e é atualizado com frequência.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Introdução às políticas personalizadas](active-directory-b2c-get-started-custom.md)
