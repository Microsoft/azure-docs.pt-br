---
title: Notas de versão do SDK do leitor de imersão
titleSuffix: Azure Cognitive Services
description: Saiba mais sobre as novidades no SDK de JavaScript do leitor de imersão.
services: cognitive-services
author: dylankil
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: reference
ms.date: 10/12/2020
ms.author: dylankil
ms.openlocfilehash: 6b041916c6fa446732e95d49391d9ead63eb1b17
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93133468"
---
# <a name="immersive-reader-javascript-sdk-release-notes"></a>Notas de versão do SDK do JavaScript do leitor de imersão

## <a name="version-110"></a>Version 1.1.0

Esta versão contém novos recursos, correções de vulnerabilidades de segurança, correções de bugs, atualizações de exemplos de código e opções de configuração.

#### <a name="new-features"></a>Novos recursos

* Habilitar o salvamento e o carregamento de preferências do usuário em diferentes navegadores e dispositivos
* Habilitar a configuração de opções de exibição padrão
* Adicionar opção para definir o idioma de tradução, habilitar a tradução automática e habilitar a tradução de documentos ao iniciar o leitor de imersão
* Adicionar suporte para configuração de leitura em voz alta via opções
* Adicionar a capacidade de desabilitar a primeira experiência de execução
* Adicionar ImmersiveReaderView para UWP

#### <a name="improvements"></a>Aprimoramentos

* Atualizar o HTML de exemplo de código do Android para trabalhar com o SDK mais recente
* Atualizar resposta de inicialização para retornar o número de caracteres processados
* Atualizar exemplos de código para usar o v 1.1.0
* Não permitir que launchAsync seja chamado quando já estiver sendo carregado
* Verificar conteúdo inválido ignorando mensagens em que os dados não são uma cadeia de caracteres
* Encapsular chamada para Window em uma cláusula If para verificar o suporte do navegador da promessa

#### <a name="fixes"></a>Correções

* Corrija o dependabot removendo yarn. Lock de gitignore
* Corrigir vulnerabilidade de segurança atualizando Pug para v 3.0.0 no início rápido-exemplo de código NodeJS
* Corrigir várias vulnerabilidades de segurança atualizando os pacotes Jest e TypeScript
* Corrigir uma vulnerabilidade de segurança atualizando Microsoft. IdentityModel. clients. ActiveDirectory para v 5.2.0

<br>

## <a name="version-100"></a>Versão 1.0.0

Esta versão contém alterações significativas, novos recursos, aprimoramentos de exemplo de código e correções de bugs.

#### <a name="breaking-changes"></a>Alterações de quebra

* Exigir token e subdomínio do Azure AD e os tokens usados em versões anteriores são substituídos.
* Defina CookiePolicy como desabilitado. A retenção de preferências do usuário é desabilitada por padrão. O leitor é iniciado com as configurações padrão a cada vez, a menos que CookiePolicy esteja definido como habilitado.

#### <a name="new-features"></a>Novos recursos

* Adicionar suporte para habilitar ou desabilitar cookies
* Adicionar exemplo de código de início rápido do Android Kotlin
* Adicionar exemplo de código de início rápido do Android Java
* Adicionar Node.js exemplo de código de início rápido

#### <a name="improvements"></a>Aprimoramentos

* Atualizar Node.js README.md avançado
* Alterar o exemplo de código Python de avançado para início rápido
* Mover amostra de código do iOS Swift para JS/exemplos
* Atualizar exemplos de código para usar v 1.0.0

#### <a name="fixes"></a>Correções

* Correção para o exemplo de código avançado Node.js
* Adicionar arquivos ausentes para Advanced-Csharp-Multiple-Resources
* Remover en-US de hiperlinks

<br>

## <a name="version-003"></a>0.0.3 da versão

Esta versão contém novos recursos, melhorias em exemplos de código, correções de vulnerabilidades de segurança e correções de bugs.

#### <a name="new-features"></a>Novos recursos

* Adicionar exemplo de código Swift do iOS
* Adicione um exemplo de código avançado em C# demonstrando o uso de vários recursos 
* Adicionar suporte para desabilitar o recurso de alternância de tela inteira
* Adicionar suporte para ocultar o botão de saída do aplicativo do leitor de imersão
* Adicionar uma função de retorno de chamada que pode ser usada pelo aplicativo host ao sair do leitor de imersão
* Atualizar exemplos de código para usar a autenticação Azure Active Directory

#### <a name="improvements"></a>Aprimoramentos

* Atualizar exemplo de código avançado do C# para incluir documento do Word
* Atualizar exemplos de código para usar v 0.0.3

#### <a name="fixes"></a>Correções

* Atualizar o lodash para a versão 4.17.14 para corrigir a vulnerabilidade de segurança
* Atualizar a biblioteca MSAL do C# para corrigir a vulnerabilidade de segurança
* Atualizar o mescla para a versão 1.3.2 para corrigir a vulnerabilidade de segurança
* Atualizar Jest, webpack e webpack-CLI que estavam usando versões vulneráveis de Set-Value e mescla-Deep para corrigir vulnerabilidade de segurança

<br>

## <a name="version-002"></a>0.0.2 da versão

Esta versão contém novos recursos, melhorias em exemplos de código, correções de vulnerabilidades de segurança e correções de bugs.

#### <a name="new-features"></a>Novos recursos

* Adicionar exemplo de código avançado do Python
* Adicionar exemplo de código de início rápido de Java
* Adicionar exemplo de código simples

#### <a name="improvements"></a>Aprimoramentos

* Renomear resourceName para cogSvcsSubdomain
* Mover segredos do código e usar variáveis de ambiente
* Atualizar exemplos de código para usar v 0.0.2

#### <a name="fixes"></a>Correções

* Corrigir bugs de acessibilidade do botão leitor imersiva
* Corrigir a rolagem quebrada
* Atualizar o pacote Handlebars para a versão 4.1.2 para corrigir a vulnerabilidade de segurança
* Corrige bugs em testes de unidade do SDK
* Corrige bugs de compatibilidade com JavaScript Internet Explorer 11
* Atualiza as URLs do SDK

<br>

## <a name="version-001"></a>0.0.1 da versão

A versão inicial do SDK do JavaScript do leitor de imersão.

* Adicionar SDK de JavaScript do leitor de imersão
* Adicionar suporte para especificar o idioma da interface do usuário
* Adicionar um tempo limite para determinar quando a função launchAsync deve falhar com um erro de tempo limite
* Adicionar suporte para especificar o índice z do iframe do leitor de imersão
* Adicionar suporte para usar uma marca WebView em vez de um iframe, para compatibilidade com aplicativos Chrome
* Adicionar testes de unidade do SDK
* Adicionar Node.js exemplo de código avançado
* Adicionar exemplo de código avançado em C#
* Adicionar exemplo de código de início rápido do C#
* Adicionar configuração de pacote, yarn e outros arquivos de compilação
* Adicionar arquivos de configuração do git
* Adicionar arquivos README.md a exemplos de código e SDK
* Adicionar licença MIT
* Adicionar instruções de colaborador
* Adicionar botão de ícone estático ativos SVG

## <a name="next-steps"></a>Próximas etapas

Introdução à Leitura Avançada:

* Leia a [Referência da biblioteca de clientes de Leitura Avançada](./reference.md)
* Explore a [Biblioteca de clientes de Leitura Avançada no GitHub](https://github.com/microsoft/immersive-reader-sdk)
