---
title: Visão geral do registro da API de Detecção Facial
titleSuffix: Azure Cognitive Services
description: Saiba mais sobre o processo de registro de Detecção Facial para registrar os usuários em um serviço de reconhecimento facial.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: overview
ms.date: 11/17/2020
ms.author: pafarley
ms.openlocfilehash: ac5106aa661cb2baea31ee15d57e9c6fac8c7192
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96350323"
---
# <a name="face-api-enrollment"></a>Registro da API de Detecção Facial

Para usar a API de Detecção Facial de Serviços Cognitivos para verificação ou identificação facial, você precisará registrar os rostos em um **LargePersonGroup**. Esta análise aprofundada demonstra as melhores práticas da coleta de consentimento significativo dos usuários, bem como um exemplo de lógica para criar registros de alta qualidade que otimizarão a precisão do reconhecimento.  

## <a name="meaningful-consent"></a>Consentimento significativo 

Uma das principais finalidades de um aplicativo de registro para o reconhecimento facial é dar aos usuários a oportunidade de fornecer consentimento para o uso de imagens do próprio rosto para finalidades específicas, como o acesso a um site de trabalho. Como as tecnologias de reconhecimento facial podem ser interpretadas como coletoras de dados pessoais confidenciais, é especialmente importante solicitar o consentimento de uma forma que seja transparente e respeitosa. O consentimento é significativo para os usuários quando os capacita a tomar a decisão que acreditam ser melhor para eles.   

Com base na pesquisa de usuários da Microsoft, nos princípios de IA Responsável da Microsoft e em [pesquisas externas](ftp://ftp.cs.washington.edu/tr/2000/12/UW-CSE-00-12-02.pdf), descobrimos que o consentimento é significativo quando oferece o seguinte aos usuários que se registram na tecnologia:

* Conscientização: os usuários não devem ter dúvidas de quando estão sendo solicitados a fornecer o modelo de rosto deles ou as fotos do registro. 
* Entendimento: os usuários devem conseguir descrever com precisão usando palavras próprias o que estava sendo solicitado deles, por quem, para que finalidade e com quais garantias. 
* Liberdade de escolha: os usuários não devem se sentir forçados nem manipulados ao escolher se devem fornecer consentimento e se registrar no reconhecimento facial. 
* Controle: os usuários devem conseguir revogar o consentimento e excluir os respectivos dados a qualquer momento. 

Esta seção oferece diretrizes para o desenvolvimento de um aplicativo de registro para reconhecimento facial. Essas diretrizes foram desenvolvidas com base na pesquisa de usuários da Microsoft no contexto de registro de indivíduos no reconhecimento facial para a criação de entrada. Portanto, essas recomendações podem não se aplicar a todas as soluções de reconhecimento facial. O uso responsável da API de Detecção Facial depende fortemente do contexto específico no qual ela está integrada. Portanto, a priorização e a aplicação dessas recomendações devem ser adaptadas ao seu cenário. 

> [!NOTE]
> É sua responsabilidade alinhar seu aplicativo de registro com os requisitos legais aplicáveis na sua jurisdição e refletir com precisão todas as suas práticas de coleta e processamento de dados.

## <a name="application-development"></a>Desenvolvimento de aplicativo 

Antes de criar um fluxo de registro, pense em como o aplicativo que você está criando pode manter as promessas feitas aos usuários sobre como os dados deles são protegidos. As recomendações a seguir podem ajudar você a criar uma experiência de registro que inclua abordagens responsáveis para proteger dados pessoais, gerenciar a privacidade dos usuários e garantir que o aplicativo seja acessível a todos os usuários.  

|Categoria | Recomendações |
|---|---|
|Hardware | Considere a qualidade da câmera do dispositivo de registro. |
|Recursos de registro recomendados | Inclua uma etapa de logon com a autenticação multifator.</br></br>Vincule as informações de usuário como um alias ou um número de identificação às respectivas IDs de modelo de rosto por meio da API de Detecção Facial (conhecida como ID da pessoa). Esse mapeamento é necessário para recuperar e gerenciar o registro de um usuário. Observação: a ID da pessoa deve ser tratada como um segredo no aplicativo.</br></br>Configure um processo automatizado para excluir todos os dados de registro, incluindo os modelos de rosto e as fotos de registro de pessoas que não são mais usuários da tecnologia de reconhecimento facial, como ex-funcionários.</br></br>Evite o registro automático, pois ele não dá ao usuário a conscientização, o entendimento, a liberdade de escolha nem o controle recomendado para a obtenção de consentimento. </br></br>Peça aos usuários permissão para salvar as imagens usadas para o registro. Isso é útil quando há uma atualização de modelo, pois serão necessárias novas fotos de registro para se registrar novamente no novo modelo a cada 10 meses. Se as imagens originais não forem salvas, os usuários precisarão passar pelo processo de registro desde o início.</br></br>Permita que os usuários recusem o armazenamento de fotos no sistema. Para tornar a opção mais clara, você pode adicionar uma segunda tela de solicitação de consentimento para salvar as fotos de registro. </br></br>Se as fotos forem salvas, crie um processo automatizado para registrar novamente todos os usuários quando houver uma atualização de modelo. Aqueles que salvaram as fotos de registro não precisarão se registrar novamente. </br></br>Crie um recurso do aplicativo que permita que os administradores designados substituam determinados filtros de qualidade se um usuário tiver problemas de registro. |
|Segurança | Os Serviços Cognitivos seguem as [melhores práticas](../cognitive-services-virtual-networks.md?tabs=portal) para criptografar os dados de usuário em repouso e em trânsito. Veja a seguir práticas adicionais que podem ajudar a manter as promessas de segurança feitas aos usuários durante a experiência de registro. </br></br>Tome medidas de segurança para garantir que ninguém tenha acesso à ID da pessoa em nenhum momento durante o registro. Observação: a PersonID deve ser tratada como um segredo no sistema de registro. </br></br>Use o [controle de acesso baseado em função](../../role-based-access-control/overview.md) com os Serviços Cognitivos. </br></br>Use a autenticação baseada em token e/ou SAS (Assinaturas de Acesso Compartilhado) em chaves e segredos para acessar recursos como bancos de dados. Ao usar tokens de solicitação ou SAS, você pode permitir acesso limitado aos dados sem comprometer suas chaves de conta e pode especificar um tempo de expiração no token. </br></br>Nunca armazene segredos, chaves ou senhas no seu aplicativo. |
|Privacidade do usuário |Forneça uma variedade de opções de registro para lidar com diferentes níveis de preocupações de privacidade. Não exija que as pessoas usem os respectivos dispositivos pessoais para se registrarem em um sistema de reconhecimento facial. </br></br>Permita que os usuários se registrem novamente, revoguem o consentimento e excluam dados do aplicativo de registro a qualquer momento e por qualquer motivo. |
|Acessibilidade |Siga os padrões de acessibilidade (por exemplo, [ADA](https://www.ada.gov/regs2010/2010ADAStandards/2010ADAstandards.htm) ou [W3C](https://www.w3.org/TR/WCAG21/)) para garantir que o aplicativo possa ser utilizado por pessoas com deficiências visuais ou de mobilidade. |

## <a name="next-steps"></a>Próximas etapas  

Siga o guia [Criar um aplicativo de registro](build-enrollment-app.md) para começar a usar um exemplo de aplicativo de registro. Em seguida, personalize-o ou escreva seu aplicativo para atender às necessidades do seu produto.