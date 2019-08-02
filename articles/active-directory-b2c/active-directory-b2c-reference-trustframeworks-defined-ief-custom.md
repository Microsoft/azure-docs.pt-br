---
title: Referência – estruturas de confiança no Azure Active Directory B2C | Microsoft Docs
description: Um tópico sobre as políticas personalizadas do Azure Active Directory B2C e a Estrutura de Experiência de Identidade.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/04/2017
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: e96ddcb904bbda6c3123ffc9d3da50ff80823689
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/25/2019
ms.locfileid: "68500023"
---
# <a name="define-trust-frameworks-with-azure-ad-b2c-identity-experience-framework"></a>Definir estruturas confiáveis com a Estrutura de Experiência de Identidade do Azure AD B2C

As políticas personalizadas do Azure AD B2C (Azure Active Directory B2C) que usam a Identity Experience Framework fornecem um serviço centralizado à sua organização. Esse serviço reduz a complexidade da federação de identidade em uma grande comunidade de interesse. A complexidade é reduzida para uma relação de confiança única e uma troca única de metadados.

Azure AD B2C políticas personalizadas usam a estrutura de experiência de identidade para permitir que você responda às seguintes perguntas:

- Quais são as políticas jurídicas, de segurança, de privacidade e de proteção de dados que devem ser cumpridas?
- Quem são os contatos e quais são os processos para se tornar um participante autorizado?
- Quem são os provedores de informações de identidade autorizados (também conhecidos como "provedores de declarações") e o que eles oferecem?
- Quais são as terceiras partes confiáveis autorizadas (e, opcionalmente, quais são suas exigências)?
- Quais são os requisitos interoperabilidade técnica "durante a transmissão" para os participantes?
- Quais regras operacionais de "tempo de execução" que devem ser aplicadas para a troca de informações de identidade digital?

Para responder a todas essas perguntas, as políticas personalizadas do Azure AD B2C que usam a Identity Experience Framework, usam o constructo da TF (Estrutura Confiável). Vamos considerar esse constructo e o que ele oferece.

## <a name="understand-the-trust-framework-and-federation-management-foundation"></a>Noções básicas sobre o gerenciamento de federação e a Estrutura Confiável

A Estrutura Confiável é uma especificação escrita de identidade, segurança, privacidade e políticas de proteção de dados às quais os participantes de uma comunidade de interesse devem estar em conformidade.

A identidade federada fornece uma base para alcançar a garantia de identidade do usuário final em escala de Internet. Ao delegar o gerenciamento de identidade para terceiros, uma única identidade digital para um usuário final pode ser reutilizada com várias terceiras partes confiáveis.  

A garantia de identidade requer que os IdPs (provedores de identidade) e AtPs (provedores de atributos) obedeçam a políticas e práticas específicas de segurança, de privacidade e operacionais.  Se não puderem realizar inspeções diretas, as RPs (terceiras partes confiáveis) devem desenvolver relações de confiança com os IdPs e AtPs com os quais escolherem trabalhar.  

Conforme o número de consumidores e provedores de informações de identidade digital aumenta, torna-se difícil continuar gerenciamento de paridade dessas relações de confiança ou até mesmo a troca de paridade de metadados técnicos necessários para a conectividade de rede.  Os hubs de federação só alcançaram sucesso limitado na solução desses problemas.

### <a name="what-a-trust-framework-specification-defines"></a>O que uma especificação de Estrutura Confiável define
As TFs são a base do modelo de estrutura confiável da OIX (Open Identity Exchange), em que cada comunidade de interesse é governada por uma especificação determinada de TF. Tal especificação TF define:

- **As métricas de segurança e privacidade para a comunidade de interesse com a definição de:**
    - LOA (níveis de garantia) que são oferecidos/exigidos pelos participantes, ou seja, um conjunto ordenado de classificações de segurança para a autenticidade das informações de identidade digital.
    - LOP (Níveis de proteção) que são oferecidos/exigidos pelos participantes; por exemplo, um conjunto ordenado de classificações de confiança para a proteção das informações de identidade digital que são manipuladas por participantes da comunidade de interesse.

- **A descrição das informações de identidade digital que são oferecidas/exigidas pelos participantes**.

- **As políticas técnicas para a produção e o consumo de informações de identidade digital e, portanto, para medir LOA e LOP. Essas políticas escritas normalmente incluem as seguintes categorias de políticas:**
    - Políticas de verificação de identidade, por exemplo: *Qual é o nível de verificação das informações de uma pessoa?*
    - Políticas de segurança, por exemplo: *Qual é o nível de proteção de confidencialidade e integridade das informações?*
    - Políticas de privacidade, por exemplo: *Qual controle um usuário tem sobre as próprias PII (informações de identificação pessoal)* ?
    - Políticas de sustentabilidade, por exemplo: *Se um provedor encerra as operações, como funciona a continuidade e a proteção das PII?*

- **Os perfis técnicos para produção e consumo de informações de identidade digital. Esses perfis incluem:**
    - Interfaces de escopo para as quais as informações de identidade digital estão disponíveis em um LOA específico.
    - Requisitos técnicos para a interoperabilidade durante a transmissão.

- **As descrições das várias funções que os participantes da comunidade podem realizar e as qualificações necessárias para cumprir essas funções.**

Dessa forma, uma especificação TF controla como as informações de identidade são trocadas entre os participantes da comunidade relevante: partes confiantes, identidade, provedores de atributos e verificadores de atributos.

Uma especificação de TF é um ou vários documentos que servem como uma referência para o controle da comunidade de interesse que regula a instrução de declaração e o consumo de informações de identidade digital dentro da comunidade. É um conjunto documentado de políticas e procedimentos projetados para estabelecer confiança em identidades digitais que são usadas para transações online entre membros de uma comunidade de interesse.  

Em outras palavras, uma especificação de TF define as regras para a criação de um ecossistema de identidade federada viável para uma comunidade.

Atualmente há um acordo amplo a respeito do benefício dessa abordagem. Não há dúvidas de que as especificações da estrutura confiável facilitam o desenvolvimento de ecossistemas de identidade digital com segurança, garantia e características de privacidade verificáveis, de modo que possam ser reutilizados em várias comunidades de interesse.

Por esse motivo, as políticas personalizadas do Azure AD B2C que usam a Identity Experience Framework usam a especificação como a base de sua representação de dados para uma TF a fim de facilitar a interoperabilidade.  

As políticas personalizadas do Azure AD B2C que aproveitam a Estrutura de Experiência de Identidade representam uma especificação de TF como uma combinação de dados humanos e dados legíveis por computador. Algumas seções desse modelo (normalmente seções que são mais orientadas para a governança) são representadas como referências à documentação publicada de política de privacidade e segurança, junto com os procedimentos relacionados (se houver). Outras seções descrevem em detalhes os metadados de configuração e as regras de tempo de execução que facilitam a automação operacional.

## <a name="understand-trust-framework-policies"></a>Entender as políticas de estrutura confiável

Em termos de implementação, a especificação de TF consiste em um conjunto de políticas que permitem o controle completo sobre comportamentos de identidade e experiências.  As políticas personalizadas do Azure AD B2C que aproveitam a Estrutura de Experiência de Identidade permitem que você escreva e crie sua própria TF por meio dessas políticas declarativas que podem definir e configurar:

- A referência ou as referências de documento que definem o ecossistema de identidade federada da comunidade que se relaciona com a TF. Eles são links para a documentação do TF. As regras de "tempo de execução" operacionais (predefinidas), ou os percursos do usuário que automatizam e/ou controlam a troca e uso das declarações. Esses percursos do usuário são associados a um LOA (e um LOP). Portanto, uma política pode ter percursos do usuário com diversos LOAs (e LOPs).

- Os provedores de atributos e de identidade, ou os provedores de declarações na comunidade relevante e os perfis técnicos que eles suportam junto com a capacitação de LOA/LOP (fora de banda) relacionadas a eles.

- A integração com verificadores de atributo ou com provedores de declarações.

- Partes confiáveis da comunidade (por inferência).

- Os metadados para estabelecer comunicações de rede entre os participantes. Esses metadados com os perfis técnicos são usados durante uma transação para inserir interoperabilidade "durante a transmissão" entre a terceira parte confiável e outros participantes da comunidade.

- A conversão de protocolo, se houver (por exemplo, SAML 2.0, OAuth2, Web Services Federation e OpenID Connect).

- Os requisitos de autenticação.

- A orquestração multifator, se houver.

- Um esquema compartilhado para todas as declarações que estão disponíveis e os mapeamentos para os participantes de uma comunidade de interesse.

- Todas as transformações de declarações, junto com a minimização de dados possíveis nesse contexto, para sustentar a troca e o uso das declarações.

- A associação e criptografia.

- O armazenamento das declarações.

### <a name="understand-claims"></a>Entender as declarações

> [!NOTE]
> Nos referimos, coletivamente, a todos os possíveis tipos de informações de identidade que poderão ser trocadas como "declarações": declarações sobre a credencial de autenticação do usuário final, análise de identidade, dispositivo de comunicação, localização física, atributos de identificação pessoal e assim por diante.  
>
> Usamos o termo "declarações" – em vez de "atributos" – pois, no caso de transações online, esses artefatos de dados não são fatos que podem ser verificados diretamente pela terceira parte confiável. Em vez disso, eles são instruções de declaração ou declarações sobre fatos para os quais a terceira parte confiável deve desenvolver confiança suficiente para conceder a transação solicitada pelo usuário final.  
>
> Nós também usamos o termo "declarações" porque as políticas personalizadas do Azure AD B2C que usam a Identity Experience Framework foram projetadas para simplificar a troca de todos os tipos de informações de identidade digital de maneira consistente, independentemente se o protocolo subjacente foi definido para autenticação de usuário ou recuperação de atributo.  Da mesma forma, usamos o termo "provedores de declarações" para nos referirmos coletivamente aos provedores de identidade, provedores de atributo e verificadores de atributo quando não queremos fazer distinção entre suas funções específicas.   

Assim, eles controlam como as informações de identidade são trocadas entre uma terceira parte confiável, identidade, provedores de atributos e verificadores de atributos. Elas controlam quais identidades e provedores de atributos são necessários para autenticação de uma terceira parte confiável. Eles devem ser considerados como uma DSL (Linguagem Específica de Domínio), ou seja, uma linguagem de computador especializada para um domínio do aplicativo específico com herança, instruções *if*, polimorfismo.

Essas políticas constituem a parte legível por computador do constructo da TF nas políticas personalizadas do Azure AD B2C que aproveitam a Estrutura de Experiência de Identidade. Elas incluem todos os detalhes operacionais, incluindo metadados e perfis técnicos de provedores de declarações, definições de esquema de declarações, funções de transformação de declarações e jornadas do usuário que são preenchidas para facilitar a automação e a orquestração operacional.  

Elas são consideradas *documentos vivos* porque há uma boa chance de que seu conteúdo seja alterado ao longo do tempo, no que diz respeito aos participantes ativos declarados nas políticas. Também há a possibilidade de que os termos e condições para se tornar um participante possam ser alterados.  

A configuração de federação e manutenção são amplamente simplificadas pela reconfiguração e confiança contínua de conectividade já que os verificadores/provedores de declarações ingressam ou saem (representados pela comunidade) do conjunto de políticas.

A interoperabilidade é outro desafio significativo. Os verificadores/provedores de declarações adicionais devem ser integrados, porque é provável que as terceiras partes confiáveis não ofereçam suporte a todos os protocolos necessários. As políticas personalizadas do Azure AD B2C resolvem esse problema oferecendo suporte aos protocolos padrão do setor e aplicando percursos do usuário específicos para transpor solicitações quando as terceiras partes confiáveis e provedores de atributos não derem suporte ao mesmo protocolo.  

Os percursos do usuário incluem perfis de protocolo e metadados que são usados para inserir interoperabilidade "durante a transmissão" entre a terceira parte confiável e outros participantes. Também há regras de tempo de execução operacional que são aplicadas a mensagens de solicitação/resposta de informações de identidade para impor a conformidade com políticas publicadas como parte da especificação da TF. A ideia de jornadas do usuário é fundamental para a personalização da experiência do cliente. Ela também esclarece como o sistema funciona no nível do protocolo.

Com essa base, portais e aplicativos de terceira parte confiável podem, dependendo de seu contexto, invocar as políticas personalizadas do Azure AD B2C que aproveitam a Estrutura de Experiência de Identidade ao passar o nome de uma política específica e obter exatamente o comportamento e a troca de informações desejadas sem qualquer problema, confusão ou risco.
