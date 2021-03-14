---
title: Desenvolver aplicativos seguros no Microsoft Azure
description: Este artigo aborda as melhores práticas a serem consideradas durante as fases de implementação e verificação do seu projeto de aplicativo Web.
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/12/2019
ms.topic: article
ms.service: security
ms.subservice: security-develop
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 52c93bef4529f27ad38677f17209e7b48e997368
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102548437"
---
# <a name="develop-secure-applications-on-azure"></a>Desenvolver aplicativos seguros no Azure
Neste artigo, apresentamos as atividades de segurança e os controles a serem considerados ao desenvolver aplicativos para a nuvem. Abordamos as perguntas e os conceitos de segurança a serem considerados durante as fases de implementação e verificação do ciclo de vida de desenvolvimento do [Microsoft SDL (Security Development Lifecycle)](/previous-versions/windows/desktop/cc307891(v=msdn.10)). O objetivo é ajudá-lo a definir atividades e serviços do Azure que você possa usar para desenvolver um aplicativo mais seguro.

As seguintes fases do SDL são abordadas neste artigo:

- Implementação
- Verificação

## <a name="implementation"></a>Implementação
O foco da fase de implementação é estabelecer as práticas recomendadas para prevenção antecipada, detecção e remoção problemas de segurança do código.
Imagine que seu aplicativo venha a ser usado de maneiras que você não pretendia. Isso ajuda você a se proteger contra o uso indevido acidental ou intencional do aplicativo.

### <a name="perform-code-reviews"></a>Executar revisões de código

Antes de fazer verificar o código, faça [revisões de código](/azure/devops/learn/devops-at-microsoft/code-reviews-not-primarily-finding-bugs) para aumentar sua qualidade geral e reduzir o risco de criar bugs. Você pode usar o [Visual Studio](/azure/devops/repos/tfvc/get-code-reviewed-vs) para gerenciar o processo de revisão de código.

### <a name="perform-static-code-analysis"></a>Executar análise de código estático

A [análise de código estático](https://owasp.org/www-community/controls/Static_Code_Analysis) (também conhecida como *análise de código-fonte*) geralmente é executada como parte de uma revisão de código. A análise de código estático normalmente refere-se à execução de ferramentas de análise de código estático para encontrar possíveis vulnerabilidades no código que não está em execução por meio de técnicas como [verificação de contaminação](https://en.wikipedia.org/wiki/Taint_checking) e [análise de fluxo de dados](https://en.wikipedia.org/wiki/Data-flow_analysis).

O Azure Marketplace oferece [ferramentas de desenvolvedor](https://azuremarketplace.microsoft.com/marketplace/apps/category/developer-tools?page=1&search=code%20review) que fazem análise de código estático e auxiliam nas revisões de código.

### <a name="validate-and-sanitize-every-input-for-your-application"></a>Validar e limpar todas as entradas do aplicativo

Trate todas as entradas como não confiáveis para proteger seu aplicativo das vulnerabilidades mais comuns de aplicativos Web. Os dados não confiáveis são um veículo para ataques de injeção. A entrada para o seu aplicativo inclui parâmetros na URL, entradas do usuário, dados do banco de dados ou de uma API e tudo o que for transmitido que um usuário possa manipular. Um aplicativo deve [validar](https://owasp.org/www-project-proactive-controls/v3/en/c5-validate-inputs) que os dados estão sintática e semanticamente válidos antes de usar os dados de alguma forma (incluindo exibi-los ao usuário).

Valide a entrada no início do fluxo de dados para ter certeza de que apenas os dados formados corretamente entrarão no fluxo de trabalho. Você não quer que dados malformados persistam em seu banco de dados ou criem um problema em um componente downstream.

Inclusão na lista e allowlisting são duas abordagens gerais para executar a validação da sintaxe de entrada:

  - Inclusão na lista tenta verificar se uma determinada entrada do usuário não contém conteúdo "conhecido como mal-intencionado".

  - O Allowlisting tenta verificar se uma determinada entrada do usuário corresponde a um conjunto de entradas "boas conhecidas". A allowlisting baseada em caracteres é uma forma de allowlisting em que um aplicativo verifica se a entrada do usuário contém apenas caracteres "bons" ou se a entrada corresponde a um formato conhecido.
    Por exemplo, isso pode envolver a verificação de que um nome de usuário contém apenas caracteres alfanuméricos ou que contém exatamente dois números.

Allowlisting é a abordagem preferida para a criação de software seguro.
O inclusão na lista está propenso a erros porque é impossível considerar uma lista completa de entradas potencialmente inadequadas.

Faça isso no servidor, não no lado do cliente (ou no servidor e no lado do cliente).

### <a name="verify-your-applications-outputs"></a>Verificar as saídas do aplicativo

Qualquer saída que você apresente visualmente ou dentro de um documento sempre deve ser codificada e com escape. O [Escape](https://owasp.org/www-community/Injection_Theory#Escaping_.28aka_Output_Encoding.29), também conhecido como *codificação de saída*, é usado para garantir que os dados não confiáveis não sejam um veículo para um ataque de injeção. O escape, combinado com a validação de dados, oferece defesas em camadas para aumentar a segurança do sistema como um todo.

O escape faz com que tudo seja exibido como *saída.* O escape também permite que o intérprete saiba que os dados não devem ser executados, e isso impede o sucesso dos ataques. Essa é outra técnica de ataque comum chamada *XSS* (Script entre sites).

Se você estiver usando uma estrutura da Web de terceiros, poderá verificar suas opções de codificação de saída em sites usando a [folha de referências de prevenção de OWASP XSS](https://github.com/OWASP/CheatSheetSeries/blob/master/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.md).

### <a name="use-parameterized-queries-when-you-contact-the-database"></a>Usar consultas parametrizadas quando entrar em contato com o banco de dados

Nunca crie uma consulta de banco de dados embutida "em tempo real" em seu código e envie-a diretamente para o banco de dados. O código mal-intencionado inserido em seu aplicativo poderia fazer com que o banco de dados fosse roubado, apagado ou modificado. Seu aplicativo também pode ser usado para executar comandos mal-intencionados no sistema operacional que hospeda seu banco de dados.

Use consultas parametrizadas ou procedimentos armazenados. Ao usar consultas parametrizadas, você poderá invocar o procedimento de seu código com segurança e transmiti-lo a uma cadeia de caracteres sem se preocupar com ele ser tratado como parte da instrução de consulta.

### <a name="remove-standard-server-headers"></a>Remover cabeçalhos de servidor padrão

Cabeçalhos como Server, X-Powered-By e X-AspNet-Version revelam informações sobre o servidor e as tecnologias subjacentes. Recomendamos suprimir esses cabeçalhos para evitar a impressão digital do aplicativo.
Confira [Removendo cabeçalhos de servidor padrão nos sites do Azure](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/).

### <a name="segregate-your-production-data"></a>Separar os dados de produção

Seus dados de produção, ou dados "reais", não devem ser usados para desenvolvimento, teste ou outra finalidade além da pretendida pela empresa. Um conjunto de dados mascarado ([anonimizado](https://en.wikipedia.org/wiki/Data_anonymization)) deve ser usado em todo o desenvolvimento e teste.

Isso significa que menos pessoas têm acesso aos dados reais, o que reduz a superfície de ataque. Isso também significa que menos funcionários veem dados pessoais, o que elimina uma possível violação de confidencialidade.

### <a name="implement-a-strong-password-policy"></a>Implementar uma política de senha forte

Para se defender contra ataques de força bruta e adivinhação baseada em dicionário, você precisará implementar uma política de senha forte para fazer com que os usuários criem uma senha complexa (por exemplo, tamanho mínimo de 12 caracteres e exigência de caracteres alfanuméricos e especiais).

Você pode usar uma estrutura de identidade para criar e impor políticas de senha. O Azure AD B2C ajuda no gerenciamento de senhas com a oferta de [políticas internas](../../active-directory-b2c/tutorial-create-user-flows.md#create-a-password-reset-user-flow), [redefinição de senha self-service](../../active-directory-b2c/user-flow-self-service-password-reset.md) e muito mais.

Para se proteger contra ataques em contas padrão, verifique se todas as chaves e senhas são substituíveis e se são geradas ou substituídas depois que você instala recursos.

Se o aplicativo precisar gerar senhas automaticamente, verifique se as senhas geradas são aleatórias e se têm alta entropia.

### <a name="validate-file-uploads"></a>Validar uploads de arquivos

Se seu aplicativo permitir [uploads de arquivos](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload), considere as precauções que você pode tomar nessa atividade arriscada. A primeira etapa em muitos ataques é inserir código mal-intencionado em um sistema que está sob ataque. O uso de um upload de arquivo ajuda o invasor a realizar a primeira etapa. O OWASP oferece soluções para validar um arquivo e garantir a segurança do que você está carregando.

A proteção antimalware ajuda a identificar e remover vírus, spyware e outros softwares mal-intencionados. Você pode instalar o [ Microsoft Antimalware](../fundamentals/antimalware.md) ou uma solução de proteção de pontos de extremidade do parceiro da Microsoft ([Trend Micro](https://www.trendmicro.com/azure/), [Broadcom](https://www.broadcom.com/products), [McAfee](https://www.mcafee.com/us/products.aspx), [Windows Defender](/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-in-windows-10) e [Endpoint Protection](/configmgr/protect/deploy-use/endpoint-protection)).

O [Microsoft Antimalware](../fundamentals/antimalware.md) inclui recursos como proteção em tempo real, verificação agendada, correção de malware, atualizações de assinatura, atualizações de mecanismo, relatórios de exemplos e coleção de eventos de exclusão. Você pode integrar o Antimalware da Microsoft e as soluções de parceiros com a [Central de Segurança do Azure](../../security-center/security-center-partner-integration.md) para facilidade de implantação e detecções internas (alertas e incidentes).

### <a name="dont-cache-sensitive-content"></a>Não armazenar conteúdo confidencial em cache

Não armazenar conteúdo confidencial no navegador. Os navegadores podem armazenar informações em seus caches e históricos. Os arquivos armazenados em cache são salvos em uma pasta, como a pasta Arquivos de Internet Temporários no caso do Internet Explorer. Quando essas páginas são consultadas novamente, o navegador as exibe por meio do cache. Se informações confidenciais (endereço, detalhes do cartão de crédito, número do seguro social, nome de usuário) forem exibidas para o usuário, elas poderão ser armazenadas no cache do navegador e ser recuperadas no exame do cache do navegador ou simplesmente com o botão **Voltar** do navegador.

## <a name="verification"></a>Verificação
A fase de verificação envolve um esforço abrangente para fazer com que o código atenda aos princípios de segurança e privacidade que foram estabelecidos nas fases anteriores.

### <a name="find-and-fix-vulnerabilities-in-your-application-dependencies"></a>Localizar e corrigir vulnerabilidades em suas dependências de aplicativo

Você examina seu aplicativo e as bibliotecas dependentes para identificar os componentes vulneráveis conhecidos. Os produtos que estão disponíveis para realizar essa verificação incluem [Verificação de Dependência do OWASP](https://www.owasp.org/index.php/OWASP_Dependency_Check), [Snyk](https://snyk.io/) e [Black Duck](https://www.blackducksoftware.com/).

A verificação de vulnerabilidades da plataforma [Tinfoil Security](https://www.tinfoilsecurity.com/) está disponível para Aplicativos Web do Serviço de Aplicativo do Azure. [O exame do Tinfoil Security por meio do Serviço de Aplicativo](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/) oferece aos desenvolvedores e administradores um meio rápido, integrado e econômico de descobrir e endereçar vulnerabilidades antes que um ator mal-intencionado possa tirar proveito delas.

> [!NOTE]
> Você também pode [Integrar o Tinfoil Security ao Azure AD](../../active-directory/saas-apps/tinfoil-security-tutorial.md). A integração do Tinfoil Security com o Azure AD oferece os seguintes benefícios:
>  - No Azure AD, você pode controlar quem tem acesso ao Tinfoil Security.
>  - Você pode permitir que seus usuários entrem automaticamente no Tinfoil Security (logon único) com a conta do Azure AD.
>  - Você pode gerenciar suas contas em um único local central, o portal do Azure.

### <a name="test-your-application-in-an-operating-state"></a>Testar o aplicativo em um estado operacional

O DAST (teste de segurança do aplicativo dinâmico) é um processo de teste de um aplicativo em um estado operacional para encontrar vulnerabilidades de segurança. As ferramentas DAST analisam programas enquanto estão em execução para encontrar vulnerabilidades de segurança, como corrupção de memória, configuração de servidor inseguro, script entre sites, problemas de privilégio de usuário, injeção de SQL e outras preocupações de segurança críticas.

O DAST é diferente do SAST (teste de segurança do aplicativo estático). As ferramentas de SAST analisam o código-fonte ou as versões compiladas do código quando ele não está em execução para encontrar falhas de segurança.

Faça o DAST preferencialmente com a assistência de um profissional de segurança (um [testador de penetração](../fundamentals/pen-testing.md) ou avaliador de vulnerabilidade). Se um profissional de segurança não estiver disponível, você poderá fazer o DAST por conta própria com um scanner de proxy da Web e algum treinamento. Conecte um scanner DAST logo no início para não introduzir problemas de segurança óbvios em seu código. Consulte o site [OWASP](https://owasp.org/www-community/Vulnerability_Scanning_Tools) para obter uma lista de scanners de vulnerabilidade de aplicativos Web.

### <a name="perform-fuzz-testing"></a>Fazer teste de fuzzing

No [teste de fuzzing](https://cloudblogs.microsoft.com/microsoftsecure/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/), você induz a falha do programa com a apresentação deliberada de dados incorretos ou aleatórios a um aplicativo. A indução em falha do programa ajuda a revelar possíveis problemas de segurança antes do lançamento do aplicativo.

A [Detecção de Riscos de Segurança](https://www.microsoft.com/en-us/security-risk-detection/) é o serviço de teste de fuzzing exclusivo da Microsoft para localizar bugs críticos de segurança no software.

### <a name="conduct-attack-surface-review"></a>Fazer o exame da superfície de ataque

O exame da superfície de ataque após a conclusão do código ajuda a garantir que todas as alterações no design ou na implementação em um aplicativo ou sistema tenham sido consideradas. Ele ajuda a garantir que todos os novos vetores de ataque criados como resultado das alterações, incluindo modelos de ameaça, tenham sido examinados e atenuados.

Você pode criar uma imagem da superfície de ataque examinando o aplicativo. A Microsoft oferece uma ferramenta de análise da superfície de ataque chamada [Analisador de Superfície de Ataque](https://www.microsoft.com/download/details.aspx?id=58105). Você pode escolher entre vários serviços ou ferramentas de teste dinâmico e exame de vulnerabilidade, incluindo o [Projeto OWASP Zed Attack Proxy](https://www.owasp.org/index.php/OWASP_Zed_Attack_Proxy_Project), o [Arachni](http://arachni-scanner.com/), o [Skipfish](https://code.google.com/p/skipfish/)e o [w3af](http://w3af.sourceforge.net/). Essas ferramentas de verificação rastreiam seu aplicativo e mapeiam as partes do aplicativo que podem ser acessadas pela Web. Você também pode pesquisar [ferramentas de desenvolvedor semelhantes](https://azuremarketplace.microsoft.com/marketplace/apps/category/developer-tools?page=1) no Azure Marketplace.

### <a name="perform-security-penetration-testing"></a>Executar teste de penetração de segurança

Verificar se seu aplicativo é seguro é tão importante quanto testar qualquer outra funcionalidade. Torne os [testes de penetração](../fundamentals/pen-testing.md) uma parte padrão do processo de build e de implantação. Agende testes de segurança e verificações de vulnerabilidade regulares em aplicativos implantados e monitore em busca de ataques e de portas e pontos de extremidade abertos.

### <a name="run-security-verification-tests"></a>Executar testes de verificação de segurança

O [AzSK](https://azsk.azurewebsites.net/index.html) (Secure DevOps Kit para Azure) contém SVTs para vários serviços da plataforma Azure. Execute esses SVTs periodicamente para ter certeza de que sua assinatura do Azure e os diferentes recursos que compõem seu aplicativo estão em um estado seguro. Você também pode automatizar esses testes usando o recurso de extensões de CI/CD (integração contínua/implantação contínua) do AzSK, que torna o SVTs disponível como extensão do Visual Studio.

## <a name="next-steps"></a>Próximas etapas
Nos artigos a seguir, recomendamos os controles de segurança e as atividades que podem ajudá-lo a projetar e implantar aplicativos seguros.

- [Criar aplicativos seguros](secure-design.md)
- [Implantar aplicativos seguros](secure-deploy.md)