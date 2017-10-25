---
title: "Certificar seus conectores personalizados – Aplicativos Lógicos do Azure | Microsoft Docs"
description: "Disponibilizar os conectores para todos os usuários no Microsoft PowerApps, Microsoft Flow e nos Aplicativos Lógicos do Azure"
author: ecfan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2017
ms.author: LADocs; estfan
ms.openlocfilehash: 9bcb138c181df0c12548cc5dc721542fd9c9ba68
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="submit-your-connectors-for-microsoft-certification"></a>Enviar seus conectores para certificação da Microsoft

Para disponibilizar publicamente conectores personalizados a todos os usuários nos Aplicativos Lógicos do Azure, no Microsoft Flow e no Microsoft PowerApps, envie seu conector à Microsoft para análise, validação e aprovação para publicaçã. 

## <a name="certification-criteria"></a>Critérios de certificação

| Recurso | Detalhes | Necessários ou recomendados |
|------------|---------|-------------------------|
| Aplicativo de software como um serviço (SaaS) | Atende ao cenário de um usuário que se ajusta bem com Aplicativos Lógicos, com o Flow e o PowerApps. | Obrigatório |
| Tipo de autenticação. | Sua API deve dar suporte para o OAuth2, a chave de API ou a autenticação básica. | Obrigatório | 
| Suporte | Você deve fornecer um contato de suporte para que os clientes possam obter ajuda. | Obrigatório | 
| Disponibilidade e Tempo de Atividade | Seu aplicativo tem, pelo menos, 99,9% de tempo de atividade. | Recomendadas | 
|||| 

Além disso, se você não for um parceiro da Microsoft ou Fornecedor de software independente (ISV) e desejar verificar e lançar publicamente um conector, será necessário possuir o serviço subjacente ou apresentar direitos explícitos para usar a API.

## <a name="validation-phases"></a>Fases da validação

A Microsoft usa estas fases de validação para avaliar seu conector:

| Fase da validação | Descrição | 
| ----- | ----------- |
| Funcionalidade | A Microsoft testa seu conector com os Aplicativos Lógicos, o Flow e o PowerApps quanto a estes erros: <p>- Erros de OpenAPI (Swagger) ou JSON inválido que aparecem na seção de Definição do assistente de conector personalizado <p>-Erros de validação de esquema e tempo de execução que aparecem na seção de Teste do assistente de conector personalizado | 
| Conteúdo | A Microsoft verifica se o conector usa nomes e descrições fáceis para cada entidade. Certifique-se de que cada operação, parâmetro de entrada e atributo de resposta no Swagger do conector tenha estes elementos: <p>- [Resumo](../logic-apps/custom-connector-openapi-extensions.md#summary) <br>- [Descrição](../logic-apps/custom-connector-openapi-extensions.md#description) </br>- [Informações de visibilidade](../logic-apps/custom-connector-openapi-extensions.md#visibility) | 
||| 

## <a name="nominate-and-submit-your-connector-to-microsoft-for-certification"></a>Nomear e enviar seu conector à Microsoft para certificação

Para se candidatar para a certificação, siga estas etapas:

1. **Nomear**

   1. [Enviar sua nomeação](https://go.microsoft.com/fwlink/?linkid=848754).

      1. Na parte inferior da página, escolha **Entre em contato conosco**.
      2. Preencha o formulário e selecione **Perguntas sobre o programa de conector ISV e co-marketing**.
      3. Escolha **Enviar**.

   2. Assine o Acordo de confidencialidade mútua e o Acordo de parceiro que você receber. 

      A Microsoft exige esses contratos assinados antes de continuar. 
      Em seguida, poderemos verificar se o seu conector atende aos critérios de certificação. 
   
   3. Se o conector for aprovado, a Microsoft o notificará com instruções para integração.
    
2. **Análise**

   1. Envie estas informações para seu contato de nomeação para análise:

      * O arquivo OpenAPI que descreve sua API
      * O arquivo de ícone (.png ou .jpg) que representa seu conector 
      
        Seu ícone deve ter um logotipo de ~ 160 pixels dentro de um quadrado 230 pixels. 
        Um logotipo branco em uma tela de fundo colorida é preferencial.
      
      * A cor da marca do ícone no formato hexadecimal, que deve corresponder à tela de fundo colorida no arquivo de ícone

      * Uma conta de teste para validação
      * Um contato de suporte

   2. Se precisarmos de mais informações, entraremos em contato.

3. **Publicar**

    Depois de validarmos a funcionalidade e o conteúdo do conector, prepararemos o conector para implantação em todos os produtos e regiões.
    
    Por padrão, todos os conectores são publicados como conectores "premium". 
    Se você criou seu aplicativo com o Azure, poderá se candidatar para listar seu conector como um conector "padrão" que estará disponível a todos os usuários com planos do Office 365 Enterprise. 
    Para obter mais detalhes, consulte seu contato de nomeação.

## <a name="next-steps"></a>Próximas etapas

* [Perguntas frequentes do conector personalizado](../logic-apps/custom-connector-faq.md)
* [Visão geral do conector personalizado](../logic-apps/custom-connector-overview.md)