---
title: Lançamentos de análise de código de segurança da Microsoft
description: Este artigo descreve as versões futuras para a extensão de análise de código de segurança da Microsoft
author: sukhans
manager: sukhans
ms.author: terrylan
ms.date: 04/24/2020
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 33ce2a496caa52609d8bdf8c92e29064ca4ae349
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85362033"
---
# <a name="microsoft-security-code-analysis-releases-and-roadmap"></a>Roteiros e lançamentos de análise de código de segurança da Microsoft

A equipe de análise de código de segurança da Microsoft em parceria com Suporte Developer tem o orgulho de anunciar aprimoramentos recentes e futuros em nossa extensão MSCA.


## <a name="credential-scanner-v20-released-in-april-2020"></a>Verificador de credenciais v 2.0: lançado em abril de 2020

### <a name="innovations--improvements"></a>Inovações & aprimoramentos

- **Mecanismo principal**

   - Upgrade de desempenho médio de 25% com tempos de execução quase lineares
   - Pesquisa e classificação com base em contexto/evidência para maior precisão
   - Melhorias em detecções gerais de senha e lógica de correspondência para espaços reservados óbvios (por exemplo, fakePassword)

- **Cobertura** -suporte para mais de 25 tipos secretos, incluindo o seguinte principal solicitado:

   - Senha do certificado da conta de malha
   - Chave de API/segredo do cliente
   - Cabeçalho de autorização HTTP
   - Chave de acesso de segredo do cliente do Amazon S3
   - Azure Active Directory token de acesso de cliente
   - Chave mestra/API de função do Azure
   - Chave de acesso de Power BI
   - Padrão de senha do modelo de Azure Resource Manager

- **Saídas**

   - Suporte para formatos de arquivo de saída de arquivo SARIF 2,1 e CSV

## <a name="binskim-v160-released-in-april-2020"></a>BinSkim v 1.6.0: lançado em abril de 2020

### <a name="improvements"></a>Aprimoramentos

- RECURSO: Atualize para o final SARIF v2 (versão 2.1.16). Essa atualização permite o cache de resultados ao passar--hashes na linha de comando, uma melhoria significativa no desempenho ao analisar recursivamente os diretórios com várias cópias de destinos de verificação.
- CORREÇÃO de BUG: corrija o erro de digitação em BA2021. Saída de DoNotMarkWritableSectionsAsExecutable.
- DESEMPENHO: eliminar o carregamento de PDB para todos os modos não mistos para assemblies gerenciados, incluindo os binários da biblioteca de IL (antecipada de tempo compilado).
- CORREÇÃO falsa negativa: Verifique se um PDB colocado junto com um binário realmente corresponde ao binário em análise
- RECURSO: forneça o argumento--local-Symbol-diretórios para especificar locais de pesquisa de PDB (local, não-símbolo-servidor) adicionais
- CORREÇÃO falsa positiva: ignorar a análise controlada por PDB para o exe de inicialização nativa do .NET Core gerado (que não é um código controlável pelo usuário).

## <a name="whats-next-in-q3-cy20"></a>O que vem a seguir no Q3 CY20?

- Ferramenta de análise de segurança do Java
- Ferramenta de análise de segurança do Python
- S fiapo para substituir TS fiapo para TypeScript e JavaScript
- Ferramenta de análise de modelos do Resource Manager

## <a name="tool-deprecation-notification"></a>Notificação de substituição de ferramenta

### <a name="microsoft-security-risk-detection-msrd-is-deprecated-on-june-26-2020"></a>A MSRD (detecção de riscos de segurança da Microsoft) foi preterida em junho de 26 2020.

O serviço de fuzzing de MSRD preterido será substituído por uma plataforma de fuzzing do desenvolvedor de software livre auto-Hosted para o Azure. Atualmente, essa plataforma está sendo desenvolvida e testada em parceria com muitas das principais equipes de produtos da Microsoft. Essa plataforma difusa integrará os limpezas e permitirá testes de fuzzing de aprendizado adaptativo incorporados a pipelines de CI/CD que crescem com o passar do tempo com projetos de software. A versão de código-fonte aberto desta plataforma está agendada para a última metade de 2020.

## <a name="next-steps"></a>Próximas etapas

Para obter instruções sobre como carregar e instalar a análise de código de segurança da Microsoft, consulte nosso [Guia de integração e instalação](security-code-analysis-onboard.md).

Se você tiver mais perguntas sobre a extensão e as ferramentas oferecidas, Confira nossa [página de perguntas frequentes](security-code-analysis-faq.md).
