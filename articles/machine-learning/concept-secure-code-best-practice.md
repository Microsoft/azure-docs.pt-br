---
title: Práticas recomendadas de código seguro
titleSuffix: Azure Machine Learning
description: Saiba mais sobre possíveis ameaças à segurança que podem existir durante o desenvolvimento para Azure Machine Learning, mitigações e práticas recomendadas.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: cgronlun
author: cjgronlund
ms.date: 11/12/2019
ms.openlocfilehash: 37cb70bdbd1e3c87eeb994e0959c6214822d22ad
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93322972"
---
# <a name="secure-code-best-practices-with-azure-machine-learning"></a>Práticas recomendadas de código seguro com o Azure Machine Learning

No Azure Machine Learning, você pode carregar arquivos e conteúdo de qualquer fonte no Azure. O conteúdo em blocos de anotações ou scripts Jupyter que você carrega pode potencialmente ler dados de suas sessões, acessar dados em sua organização no Azure ou executar processos mal-intencionados em seu nome.

> [!IMPORTANT]
> Execute somente blocos de anotações ou scripts de fontes confiáveis. Por exemplo, onde você ou sua equipe de segurança revisou o bloco de anotações ou o script.

## <a name="potential-threats"></a>Ameaças potenciais

O desenvolvimento com o Azure Machine Learning geralmente envolve ambientes de desenvolvimento baseados na Web (notebooks & Azure ML Studio). Ao usar ambientes de desenvolvimento baseados na Web, as possíveis ameaças são:

* [XSS (Cross-Site Scripting)](https://owasp.org/www-community/attacks/xss/)

    * __Injeção de Dom__: esse tipo de ataque pode modificar a interface do usuário exibida no navegador. Por exemplo, alterando como o botão Executar se comporta em um Jupyter Notebook.
    * __Token de acesso/cookies__: os ataques de XSS também podem acessar o armazenamento local e cookies de navegador. O token de autenticação do Azure Active Directory (AAD) é armazenado no armazenamento local. Um ataque XSS pode usar esse token para fazer chamadas à API em seu nome e, em seguida, enviar os dados para um sistema externo ou uma API.

* [Falsificação de solicitação entre sites (CSRF)](https://owasp.org/www-community/attacks/csrf): esse ataque pode substituir a URL de uma imagem ou um link com a URL de um script mal-intencionado ou API. Quando a imagem é carregada ou o link clicado, é feita uma chamada para a URL.

## <a name="azure-ml-studio-notebooks"></a>Blocos de anotações do Azure ML Studio

O Azure Machine Learning Studio fornece uma experiência de notebook hospedada em seu navegador. As células em um bloco de anotações podem gerar documentos HTML ou fragmentos que contenham código mal-intencionado.  Quando a saída é renderizada, o código pode ser executado.

__Possíveis ameaças__:
* XSS (Cross-Site Scripting)
* CSRF (Solicitação Intersite Forjada)

__Atenuações fornecidas pelo Azure Machine Learning__:
* A __saída da célula de código__ está na área restrita em um iframe. O iframe impede que o script acesse o DOM pai, os cookies ou o armazenamento de sessão.
* O conteúdo da __célula de redução__ é limpo usando a biblioteca dompurify. Isso impede que scripts mal-intencionados sejam executados com células de redução sejam renderizados.
* A __URL da imagem__ e os links de __redução__ são enviados a um ponto de extremidade de propriedade da Microsoft, que verifica valores mal-intencionados. Se um valor mal-intencionado for detectado, o ponto de extremidade rejeitará a solicitação.

__Ações recomendadas__:
* Verifique se você confia no conteúdo dos arquivos antes de carregá-los no estúdio. Ao carregar, você deve reconhecer que está carregando arquivos confiáveis.
* Ao selecionar um link para abrir um aplicativo externo, você será solicitado a confiar no aplicativo.

## <a name="azure-ml-compute-instance"></a>Instância de computação do Azure ML

Azure Machine Learning instância de computação hospeda o __Jupyter__ e o __Jupyter Lab__. Ao usar qualquer um, as células em um bloco de anotações ou código no podem gerar documentos HTML ou fragmentos que contêm código mal-intencionado. Quando a saída é renderizada, o código pode ser executado. As mesmas ameaças também se aplicam ao usar o __RStudio__ hospedado em uma instância de computação.

__Possíveis ameaças__:
* XSS (Cross-Site Scripting)
* CSRF (Solicitação Intersite Forjada)

__Atenuações fornecidas pelo Azure Machine Learning__:
* Nenhum. O Jupyter e o Jupyter Lab são aplicativos de software livre hospedados na instância de computação Azure Machine Learning.

__Ações recomendadas__:
* Verifique se você confia no conteúdo dos arquivos antes de carregá-los no estúdio. Ao carregar, você deve reconhecer que está carregando arquivos confiáveis.

## <a name="report-security-issues-or-concerns"></a>Relatar problemas ou preocupações de segurança 

Azure Machine Learning está qualificado sob o programa Microsoft Azure Bounty. Para obter mais informações, visite  [https://www.microsoft.com/msrc/bounty-microsoft-azure](https://www.microsoft.com/msrc/bounty-microsoft-azure) .

## <a name="next-steps"></a>Próximas etapas

* [Segurança empresarial para o Azure Machine Learning](concept-enterprise-security.md)