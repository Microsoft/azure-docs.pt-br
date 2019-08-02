---
title: Desenvolver aplicativos seguros no Microsoft Azure
description: Este artigo discute as práticas recomendadas a serem considerados durante as fases de implementação e a verificação do seu projeto de aplicativo web.
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/12/2019
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: f4add4bf07178aa616e86f8a64b313630466824f
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67653275"
---
# <a name="develop-secure-applications-on-azure"></a>Desenvolver aplicativos seguros no Azure
Neste artigo, apresentamos as atividades de segurança e controles a serem considerados ao desenvolver aplicativos para a nuvem. Perguntas de segurança e conceitos a serem considerados durante as fases de implementação e a verificação da Microsoft [Security Development Lifecycle (SDL)](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) são abordados. O objetivo é ajudá-lo a definir as atividades e serviços do Azure que você pode usar para desenvolver um aplicativo mais seguro.

As seguintes fases SDL são abordadas neste artigo:

- Implementação
- Verificação

## <a name="implementation"></a>Implementação
É o foco da fase de implementação para estabelecer as práticas recomendadas para prevenção antecipada e detectar e remover os problemas de segurança do código.
Suponha que seu aplicativo será usado de maneiras que você não pretendia que seja usada. Isso ajuda você a se proteger contra uso indevido acidental ou intencional de seu aplicativo.

### <a name="perform-code-reviews"></a>Executar revisões de código

Antes de fazer check-in do código, conduzir [revisões de código](https://docs.microsoft.com/azure/devops/learn/devops-at-microsoft/code-reviews-not-primarily-finding-bugs) para aumentar a qualidade geral do código e reduzir o risco de criar bugs. Você pode usar [Visual Studio](https://docs.microsoft.com/azure/devops/repos/tfvc/get-code-reviewed-vs?view=vsts) para gerenciar o processo de revisão de código.

### <a name="perform-static-code-analysis"></a>Executar análise de código estático

[Análise de código estático](https://www.owasp.org/index.php/Static_Code_Analysis) (também conhecido como *análise de código de origem*) normalmente é executada como parte de uma revisão de código. Análise de código estático normalmente se refere à execução de código estático para encontrar vulnerabilidades potenciais no código de não execução usando técnicas como as ferramentas de análise [prejudicar verificando](https://en.wikipedia.org/wiki/Taint_checking) e [análise de fluxo de dados](https://en.wikipedia.org/wiki/Data-flow_analysis).

O Azure Marketplace oferece [ferramentas de desenvolvedor](https://azuremarketplace.microsoft.com/marketplace/apps/category/developer-tools?page=1&search=code%20review) que executar a análise de código estático e ajudar com revisões de código.

### <a name="validate-and-sanitize-every-input-for-your-application"></a>Validar e limpar a cada entrada para seu aplicativo

Trate todas as entradas como não confiável para proteger seu aplicativo contra as vulnerabilidades mais comuns do aplicativo web. Dados não confiáveis são um veículo para ataques de injeção. A entrada para seu aplicativo inclui os parâmetros na URL de entrada do usuário, dados do banco de dados ou de uma API, e tudo o que é passado em que um usuário poderia potencialmente manipular. Um aplicativo deve [validar](https://www.owasp.org/index.php/OWASP_Proactive_Controls_2016#4:_Validate_All_Inputs) que os dados são sintaticamente e semanticamente válidos antes do aplicativo usa os dados de qualquer forma (incluindo exibi-la de volta para o usuário).

Valide a entrada no início no fluxo de dados para garantir que apenas os dados corretamente formados insere o fluxo de trabalho. Você não deseja dados malformados persistir no banco de dados ou disparar um mau funcionamento em um componente downstream.

Blacklisting e lista de exceções são as duas abordagens gerais para executar a validação de sintaxe de entrada:

  - Lista de bloqueios tenta verificar que uma entrada do usuário fornecido não contém conteúdo "conhecido como mal-intencionado".

  - Lista de permissões de tentativas verificar uma entrada de determinado usuário corresponde a um conjunto de entradas "boas". Lista branca baseada em caractere é um formulário de lista de permissões em que um aplicativo verifica se a entrada do usuário contém apenas "bom" caracteres conhecidos ou entrada corresponde a um formato conhecido.
    Por exemplo, isso pode envolver a verificação de que um nome de usuário contém apenas caracteres alfanuméricos ou que ele contém exatamente dois números.

Lista de permissões é a abordagem preferencial para a criação de um software seguro.
Lista de bloqueios é propensa a erros, porque é impossível pensar em uma lista completa de entrada potencialmente ruim.

Fazer esse trabalho no servidor, não no lado do cliente (ou no servidor e no lado do cliente).

### <a name="verify-your-applications-outputs"></a>Verifique se as saídas de seu aplicativo

Todas as saídas que você apresente visualmente ou dentro de um documento sempre devem ser codificada e de escape. [O escape](https://www.owasp.org/index.php/Injection_Theory#Escaping_.28aka_Output_Encoding.29), também conhecido como *codificação de saída*, é usado para ajudar a garantir que os dados não confiáveis não não um veículo para um ataque de injeção. O escape, combinados com a validação de dados, fornece defesas em camadas para aumentar a segurança do sistema como um todo.

O escape de torna-se de que tudo é exibido como *saída.* Escape também permite que o interpretador Saiba que os dados não se destina a ser executado, e isso impede ataques de trabalho. Isso é outra técnica de ataque comum chamada *scripts entre sites* (XSS).

Se você estiver usando uma estrutura da web de terceiros, você pode verificar as opções de codificação de saída nos sites usando o [roteiro de prevenção de XSS OWASP](https://github.com/OWASP/CheatSheetSeries/blob/master/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.md).

### <a name="use-parameterized-queries-when-you-contact-the-database"></a>Use consultas parametrizadas, quando você entre em contato com o banco de dados

Nunca crie uma consulta de banco de dados embutidos "em um piscar de olhos" em seu código e enviá-lo diretamente para o banco de dados. Código mal-intencionado inserido no seu aplicativo pode causar o seu banco de dados a ser roubado, apagados ou modificada. Seu aplicativo também pode ser usado para executar comandos do sistema de operacional mal-intencionado no sistema operacional que hospeda seu banco de dados.

Em vez disso, use consultas parametrizadas ou procedimentos armazenados. Quando você usa consultas parametrizadas, você pode invocar o procedimento do seu código com segurança e passá-lo em uma cadeia de caracteres sem se preocupar que ele será tratado como parte da instrução de consulta.

### <a name="remove-standard-server-headers"></a>Remover cabeçalhos de servidor padrão

Cabeçalhos como Server, X-Powered-By, e X-AspNet-Version revelam informações sobre o servidor e as tecnologias subjacentes. É recomendável que você suprimir esses cabeçalhos para evitar a impressão digital do aplicativo.
Ver [Removendo cabeçalhos de servidor padrão nos sites do Azure](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/).

### <a name="segregate-your-production-data"></a>Separar seus dados de produção

Seus dados de produção ou dados "real", não devem ser usados para desenvolvimento, teste ou qualquer outra finalidade que não se destina a empresa. Um mascarado ([anonimizado](https://en.wikipedia.org/wiki/Data_anonymization)) conjunto de dados deve ser usado para todo o desenvolvimento e teste.

Isso significa que menos pessoas têm acesso a seus dados reais, o que reduz a superfície de ataque. Isso também significa que menos funcionários consulte dados pessoais, eliminando uma potencial violação confidencialidade.

### <a name="implement-a-strong-password-policy"></a>Implementar uma política de senha forte

Para fornecer proteção contra força bruta e baseada em dicionário adivinhar, você deve implementar uma política de senha forte para garantir que os usuários criem uma senha complexa (por exemplo, comprimento mínimo de 12 caracteres e exigir caracteres especiais e alfanuméricos).

Você pode usar uma estrutura de identidade para criar e impor políticas de senha. B2C do AD do Azure ajuda você com o gerenciamento de senhas, fornecendo [políticas internas](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-user-flows#create-a-password-reset-user-flow), [de redefinição de senha de autoatendimento](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-sspr)e muito mais.

Para se proteger contra ataques em contas padrão, verifique se todas as chaves e senhas são substituíveis e que eles são gerados ou substituídos após a instalação de recursos.

Se o aplicativo deve gerar senhas automaticamente, certifique-se de que as senhas geradas são aleatórias e que eles têm alta entropia.

### <a name="validate-file-uploads"></a>Validar os carregamentos de arquivos

Se seu aplicativo permite [carregamentos de arquivo](https://www.owasp.org/index.php/Unrestricted_File_Upload), considere as precauções que você pode tomar para esta atividade arriscada. A primeira etapa em muitos ataques é obter algum código mal-intencionado em um sistema que está sob ataque. O uso de um upload de arquivo ajuda o invasor fazer isso. OWASP oferece soluções para validar um arquivo para garantir que o arquivo que você está carregando é seguro.

Proteção antimalware ajuda a identificar e remover vírus, spyware e outros softwares mal-intencionados. Você pode instalar o [Antimalware da Microsoft](https://docs.microsoft.com/azure/security/azure-security-antimalware) ou uma solução de proteção de pontos de extremidade do parceiro da Microsoft ([Trend Micro](https://www.trendmicro.com/azure/), [Symantec](https://www.symantec.com/products), [McAfee](https://www.mcafee.com/us/products.aspx), [Windows Defender](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-in-windows-10) e [System Center Endpoint Protection](https://docs.microsoft.com/sccm/protect/deploy-use/endpoint-protection)).

[O Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) inclui recursos como a proteção em tempo real, verificação agendada, remediação de malware, atualizações de assinatura, atualizações do mecanismo, exemplos de relatório e coleta de eventos de exclusão. Você pode integrar o Antimalware da Microsoft e as soluções de parceiros com a [Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-partner-integration) para facilidade de implantação e detecções internas (alertas e incidentes).

### <a name="dont-cache-sensitive-content"></a>Não armazenar em cache conteúdo confidencial

Não armazenar em cache conteúdo confidencial no navegador. Os navegadores podem armazenar informações de armazenamento em cache e o histórico. Arquivos armazenados em cache são armazenados em uma pasta como a pasta de arquivos temporários da Internet, no caso do Internet Explorer. Quando essas páginas são chamadas novamente, o navegador exibe as páginas do seu cache. Se informações confidenciais (endereço, detalhes de cartão de crédito, número do seguro Social, nome de usuário) são exibidas ao usuário, as informações podem ser armazenadas no cache do navegador e ser recuperável, examinando o cache do navegador ou, simplesmente pressionando o navegador  **Volta** botão.

## <a name="verification"></a>Verificação
A fase de verificação envolve um esforço abrangente para garantir que o código atenda os princípios de segurança e privacidade estabelecidas nas fases anteriores.

### <a name="find-and-fix-vulnerabilities-in-your-application-dependencies"></a>Encontre e corrija vulnerabilidades em suas dependências de aplicativo

Você digitalizar seu aplicativo e suas bibliotecas dependentes para identificar todos os componentes vulneráveis conhecidos. Os produtos que estão disponíveis para executar essa verificação incluem [OWASP Dependency Check](https://www.owasp.org/index.php/OWASP_Dependency_Check),[Snyk](https://snyk.io/), e [Black Duck](https://www.blackducksoftware.com/).

Verificação de vulnerabilidade multifuncionais [Tinfoil Security](https://www.tinfoilsecurity.com/) está disponível para aplicativos de Web do serviço de aplicativo do Azure. [Verificação do tinfoil Security por meio do serviço de aplicativo](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/) oferece aos desenvolvedores e administradores um meio rápido, integrado e econômico de descobrir e resolver vulnerabilidades antes que um ator mal-intencionado pode tirar proveito deles.

> [!NOTE]
> Você também pode [integrar o Tinfoil Security com o Azure AD](https://docs.microsoft.com/azure/active-directory/saas-apps/tinfoil-security-tutorial). A integração do Tinfoil Security com o Azure AD oferece os seguintes benefícios:
>  - No Azure AD, você pode controlar quem tem acesso ao Tinfoil Security.
>  - Seus usuários podem ser automaticamente conectados ao Tinfoil Security (logon único) usando suas contas do AD do Azure.
>  - Você pode gerenciar suas contas em um único local central, o portal do Azure.

### <a name="test-your-application-in-an-operating-state"></a>Teste seu aplicativo em um estado operacional

Testes de segurança de aplicativo dinâmico (DAST) é um processo de testar o aplicativo em um estado operacional para encontrar vulnerabilidades de segurança. Ferramentas DAST analisam programas enquanto elas estiverem em execução para encontrar vulnerabilidades de segurança, como corrupção de memória, configuração do servidor inseguro, scripts intersites, questões de privilégio de usuário, injeção de SQL e outras preocupações de segurança críticas.

DAST é diferente de (SAST) de testes de segurança de aplicativo estático. Ferramentas SAST analisar o código-fonte ou compilado versões do código quando o código não está em execução para encontrar falhas de segurança.

Executar DAST, preferencialmente com o auxílio de um profissional de segurança (um [Testador de penetração](https://docs.microsoft.com/azure/security/azure-security-pen-testing) ou assessor de vulnerabilidade). Se um profissional de segurança não estiver disponível, você pode executar DAST por conta própria com algum treinamento e de um scanner de proxy da web. Conecte um scanner DAST desde o início para garantir que você não introduzem problemas de segurança óbvias em seu código. Consulte a [OWASP](https://www.owasp.org/index.php/Category:Vulnerability_Scanning_Tools) site para obter uma lista dos scanners de vulnerabilidade do aplicativo web.

### <a name="perform-fuzz-testing"></a>Executar o teste de difusão

Na [teste difuso](https://cloudblogs.microsoft.com/microsoftsecure/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/), induzir a falha do programa introduzindo deliberadamente malformados ou aleatórios de dados a um aplicativo. Induzir a falha do programa ajuda a revelar problemas potenciais de segurança antes que o aplicativo seja liberado.

[Detecção de riscos de segurança](https://docs.microsoft.com/security-risk-detection/) é o Microsoft exclusivo teste difuso serviço para localizar bugs críticos de segurança em software.

### <a name="conduct-attack-surface-review"></a>Conduzir a revisão da superfície de ataque

Após a conclusão de código ajuda a garantir que qualquer design ou na implementação muda para um aplicativo ou sistema foram considerado, revisando a superfície de ataque. Ele ajuda a garantir que quaisquer novos vetores de ataque que foram criados como resultado das alterações, incluindo modelos de ameaças, foi revisado e reduzido.

Você pode criar uma imagem da superfície de ataque por meio da verificação do aplicativo. A Microsoft oferece uma ferramenta de análise da superfície de ataque chamada [Attack Surface Analyzer](https://www.microsoft.com/download/details.aspx?id=24487). Você pode escolher entre muitos testes dinâmicos comercial e ferramentas ou serviços, incluindo a verificação de vulnerabilidade [projeto de Proxy de ataque do OWASP zada](https://www.owasp.org/index.php/OWASP_Zed_Attack_Proxy_Project), [Arachni](http://arachni-scanner.com/), [Skipfish](https://code.google.com/p/skipfish/), e [w3af](http://w3af.sourceforge.net/). Essas ferramentas de verificação rastrear seu aplicativo e mapeiam as partes do aplicativo que são acessíveis pela web. Você também pode pesquisar no Azure Marketplace para semelhante [ferramentas de desenvolvedor](https://azuremarketplace.microsoft.com/marketplace/apps/category/developer-tools?page=1).

### <a name="perform-security-penetration-testing"></a>Executar testes de penetração de segurança

Garantir que seu aplicativo é seguro é tão importante quanto testar qualquer outra funcionalidade. Tornar [testes de penetração](https://docs.microsoft.com/azure/security/azure-security-pen-testing) padrão, parte do processo de compilação e implantação. Agende testes de segurança regular e verificação de vulnerabilidades nos aplicativos implantados e monitorar ataques, os pontos de extremidade e portas abertas.

### <a name="run-security-verification-tests"></a>Executar testes de verificação de segurança

[Proteger o Kit de DevOps do Azure](https://azsk.azurewebsites.net/index.html) (AzSK) contém SVTs para vários serviços da plataforma Azure. Você executa esses SVTs periodicamente para garantir que sua assinatura do Azure e os diferentes recursos que compõem seu aplicativo estão em um estado seguro. Você também pode automatizar esses testes usando o recurso de extensões de CI/CD (implantação) / integração contínua do AzSK, o que torna SVTs disponível como uma extensão do Visual Studio.

## <a name="next-steps"></a>Próximas etapas
Os artigos a seguir, é recomendável que os controles de segurança e as atividades que podem ajudá-lo a projetar e implantar aplicativos seguros.

- [Design de aplicativos seguros](secure-design.md)
- [Implantar aplicativos seguros](secure-deploy.md)
