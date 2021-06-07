---
title: Entidades nomeadas para saúde
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 03/11/2021
ms.author: aahi
ms.openlocfilehash: 805c726d33f2050f6f2797c0689069aa5ec4ee71
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104599283"
---
[Análise de texto para processos de integridade](../../how-tos/text-analytics-for-health.md) e extrai informações de dados médicos não estruturados. O serviço detecta e superfícies conceitos médicos, atribui asserções a conceitos, infere relações semânticas entre os conceitos e os vincula a ontologies médicos comuns.

Análise de Texto para integridade detecta os conceitos médicos nas categorias a seguir. Somente o texto em inglês tem suporte nesta visualização e apenas uma única versão de modelo está disponível.

| Categoria  | Descrição  |
|---------|---------|
| [Anatomia](#anatomy) | conceitos que capturam informações sobre o corpo e sistemas, sites, locais ou regiões. |
 | [DADOS demográficos](#demographics) | conceitos que capturam informações sobre sexo e idade. |
 | [EXAME](#examinations) | conceitos que capturam informações sobre testes e procedimentos de diagnóstico. |
 | [ATRIBUTOS GERAIS](#general-attributes) | conceitos que fornecem mais informações sobre outros conceitos das categorias acima. |
 | [GENOMICS](#genomics) | conceitos que capturam informações sobre genes e variantes. |
 | [EMPRESAS](#healthcare) | conceitos que capturam informações sobre eventos administrativos, ambientes de atendimento e prestações de saúde. |
 | [CONDIÇÃO MÉDICA](#medical-condition) | conceitos que capturam informações sobre diagnósticos, sintomas ou sinais. |
 | [MEDICAMENTOS](#medication) | conceitos que capturam informações sobre medicamentos, incluindo nomes medicamentos, classes, dosage e rota de administração. |
 | [RG](#social) | conceitos que capturam informações sobre aspectos sociais de medicinaidade relevantes, como a relação de família. |
 | [TRATAMENTOS](#treatment) | conceitos que capturam informações sobre procedimentos therapeutic. |

Você encontrará mais informações e exemplos abaixo.

## <a name="anatomy"></a>Anatomia

### <a name="entities"></a>Entidades

Sistemas de **BODY_STRUCTURE** corpo, locais ou regiões de anatômicas e sites de corpo. Por exemplo, ARM, joelho, abdomen, nariz, liver, Head, respiratory System, lymphocytes.

:::image type="content" source="../../media/ta-for-health/anatomy-entities-body-structure.png" alt-text="Um exemplo da entidade de estrutura do corpo.":::


:::image type="content" source="../../media/ta-for-health/anatomy-entities-body-structure-2.png" alt-text="Um exemplo expandido da entidade de estrutura do corpo.":::

## <a name="demographics"></a>Dados demográficos

### <a name="entities"></a>Entidades

**Idade** – todos os termos e frases do Age, incluindo aqueles para pacientes, membros da família e outros. Por exemplo, 40-ano-antigo, 51 Yo, 3 meses antigos, adulto, Infant, idosa, Young, menor, médio.

:::image type="content" source="../../media/ta-for-health/age-entity.png" alt-text="Um exemplo de uma entidade etária.":::

:::image type="content" source="../../media/ta-for-health/age-entity-2.png" alt-text="Outro exemplo de uma entidade etária.":::


**Gênero** -termos que divulgam o gênero do assunto. Por exemplo, masculino, feminino, mulher, cavalheiro, senhora.

:::image type="content" source="../../media/ta-for-health/gender-entity.png" alt-text="Um exemplo de uma entidade de gênero.":::

## <a name="examinations"></a>Análises

### <a name="entities"></a>Entidades

**EXAMINATION_NAME** – procedimentos e testes de diagnóstico, incluindo sinais vitais e medidas de corpo. Por exemplo, MRI, ECG, teste de HIV, Hemoglobin, contagem de platelets, sistemas de escala como *Bristol Stool Scale*.

:::image type="content" source="../../media/ta-for-health/exam-name-entities.png" alt-text="Um exemplo de uma entidade de exame.":::

:::image type="content" source="../../media/ta-for-health/exam-name-entities-2.png" alt-text="Outro exemplo de uma entidade de nome de exame.":::

## <a name="general-attributes"></a>Atributos gerais

### <a name="entities"></a>Entidades

**Data** -data completa relacionada a uma condição médica, exame, tratamento, medicamentos ou evento administrativo.

**Direção** – termos direcionais que podem estar relacionados a uma estrutura de corpo, condição médica, exame ou tratamento, como: esquerda, lateral, superior, posteriores.

**Frequência** – descreve com que frequência uma condição médica, exame, tratamento ou medicamentos ocorreu, ocorre ou deve ocorrer.

**MEASUREMENT_VALUE** – o valor relacionado a uma medição de condições médicas ou de exame.

**MEASUREMENT_UNIT** – a unidade de medida relacionada a um exame ou uma medida de condição médica.

**RELATIONAL_OPERATOR** -frases que expressam a relação quantitativa entre uma entidade e algumas informações adicionais.

**Tempo** -termos temporais relacionados ao início e/ou comprimento (duração) de uma condição médica, exame, tratamento, medicamentos ou evento administrativo. 

## <a name="genomics"></a>Genomics

### <a name="entities"></a>Entidades

**GENE_OR_PROTEIN** – todas as menção de nomes e símbolos de genes humana, bem como os cromossomas e partes de cromossomas e proteínas. Por exemplo, MTRR, F2.

:::image type="content" source="../../media/ta-for-health/genomics-entities.png" alt-text="Um exemplo de uma entidade de Gene.":::

**Variante** – todas as menção de variações de genes e mutações. Por `c.524C>T` exemplo, `(MTRR):r.1462_1557del96`
  
## <a name="healthcare"></a>Serviços de saúde

### <a name="entities"></a>Entidades
  
**ADMINISTRATIVE_EVENT** – eventos relacionados ao sistema de saúde, mas de natureza administrativa/semiadministrativa. Por exemplo, registro, admissão, avaliação, entrada de estudo, transferência, descarga, hospitalização, hospital permanecer. 

:::image type="content" source="../../media/ta-for-health/healthcare-event-entity.png" alt-text="Um exemplo de uma entidade de evento de saúde.":::

**CARE_ENVIRONMENT** – um ambiente ou local onde os pacientes são considerados cuidados. Por exemplo, sala de emergência, escritório do médico, unidade cardio, Hospice, hospital.

:::image type="content" source="../../media/ta-for-health/healthcare-environment-entity.png" alt-text="Esta captura de tela mostra um exemplo de uma entidade de ambiente de saúde.":::

**HEALTHCARE_PROFESSION** – um profissional de saúde licenciado ou não licenciado. Por exemplo, dentista, Pathologist, neurologist, radiologist, Pharmacist, nutritionist, Physical therapist, chiropractor.

:::image type="content" source="../../media/ta-for-health/healthcare-profession-entity.png" alt-text="Esta captura de tela mostra outro exemplo de uma entidade de ambiente de saúde.":::

:::image type="content" source="../../media/ta-for-health/healthcare-profession-entity-2.png" alt-text="Outro exemplo de uma entidade de ambiente de saúde.":::

## <a name="medical-condition"></a>Condição médica

### <a name="entities"></a>Entidades

**Diagnóstico** – doença, síndrome, envenenamento. Por exemplo, mama câncer, Alzheimer, HTN, CHF, lesão de cabo de giro.

:::image type="content" source="../../media/ta-for-health/medical-condition-entity.png" alt-text="Um exemplo de uma entidade de condição médica.":::

:::image type="content" source="../../media/ta-for-health/medical-condition-entity-2.png" alt-text="Outro exemplo de uma entidade de condição médica.":::

**SYMPTOM_OR_SIGN** – evidência subjetiva ou objetiva de doenças ou outros diagnósticos. Por exemplo, conjunto dor, dor de cabeça, desmaios, irritação, SOB, abdomen era flexível, boa bowel Sounds, bem nourished.

:::image type="content" source="../../media/ta-for-health/medical-condition-symptom-entity.png" alt-text="Um exemplo de um sinal de condição médica ou uma entidade de sintoma.":::

:::image type="content" source="../../media/ta-for-health/medical-condition-symptom-entity-2.png" alt-text="Outro exemplo de uma entidade de sinal de condição médica ou de sintoma.":::

**CONDITION_QUALIFIER** -termos qualitativos que são usados para descrever uma condição médica. Todas as subcategorias a seguir são consideradas qualificadores:

1.  Expressões relacionadas a tempo: são termos que descrevem a dimensão de tempo qualitativamente, como repentina, acento agudo, crônicos, permanente. 
2.  Expressões de qualidade: são termos que descrevem a "natureza" da condição médica, como a gravação, nítida.
3.  Expressões de severidade: grave, leve, um pouco, não controlado.
4.  Expressões Extensivity: local, focal, difuso.
5.  Expressões de radiação: radiates, radiação.
6.  Escala de condição: em alguns casos, uma condição é caracterizada por uma escala, que é uma lista de valores ordenada finito. Por exemplo, pacientes com o Stage III pancreatic câncer.
7.  Curso de condição: um termo relacionado ao curso ou à progressão de uma condição, como aprimoramento, deterioração, resolução, remissidade. 

:::image type="content" source="../../media/ta-for-health/condition-qualifier-diagnosis.png" alt-text="Um exemplo de um atributo de qualificador de condição e uma entidade de diagnóstico.":::

:::image type="content" source="../../media/ta-for-health/condition-qualifier-diagnosis-2.png" alt-text="Outro exemplo de um atributo de qualificador de condição e uma entidade de diagnóstico.":::

:::image type="content" source="../../media/ta-for-health/conditional-qualifier-symptom-medication.png" alt-text="Um exemplo de um atributo de qualificador de condição com entidades sintoma e medicamentos.":::

:::image type="content" source="../../media/ta-for-health/condition-qualifier-diagnosis-3.png" alt-text="Esta captura de tela mostra outro exemplo de um atributo de qualificador de condição com uma entidade de diagnóstico.":::

:::image type="content" source="../../media/ta-for-health/condition-qualifier-symptom.png" alt-text="Esta captura de tela mostra um exemplo adicional de um atributo de qualificador de condição com uma entidade de diagnóstico.":::

## <a name="medication"></a>Medicamentos

### <a name="entities"></a>Entidades

**MEDICATION_CLASS** – um conjunto de medicamentos que têm um mecanismo de ação semelhante, um modo de ação relacionado, uma estrutura química semelhante e/ou são usados para tratar a mesma doença. Por exemplo, ACE Inhibitor, opioid, antibióticos, aliviadores problemáticos.

:::image type="content" source="../../media/ta-for-health/medication-entities-class.png" alt-text="Um exemplo de uma entidade de classe medicamentos.":::

**MEDICATION_NAME** – medicamentos menciona, incluindo nomes de marca com direitos autorais e nomes que não são da marca. Por exemplo, Ibuprofen.

:::image type="content" source="../../media/ta-for-health/medication-entities-name.png" alt-text="Um exemplo de uma entidade de nome de medicamentos.":::

**DOSAGE** -quantidade de medicamentos ordenados. Por exemplo, Turbine sodium Chloride Solution *1000 ml*.

:::image type="content" source="../../media/ta-for-health/medication-dosage.png" alt-text="Um exemplo de um atributo medicamentos dosage.":::

**MEDICATION_FORM** -a forma do medicamentos. Por exemplo, solução, Pill, cápsula, Tablet, patch, gel, colar, espuma, spray, quedas, sorvete, Syrup.

:::image type="content" source="../../media/ta-for-health/medication-form.png" alt-text="Um exemplo de um atributo de formulário medicamentos.":::

**MEDICATION_ROUTE** -o método de administração de medicamentos. Por exemplo, oral, vaginal, IV, epidural, tópicas, Inhaled.

:::image type="content" source="../../media/ta-for-health/medication-route.png" alt-text="Um exemplo de um atributo de rota medicamentos.":::

## <a name="social"></a>Social

### <a name="entities"></a>Entidades

**FAMILY_RELATION** – menciona os parentes da família do assunto. Por exemplo, pai, filha, irmãos, pais.

:::image type="content" source="../../media/ta-for-health/family-relation.png" alt-text="Captura de tela mostra outro exemplo de um atributo de tempo de tratamento.":::

## <a name="treatment"></a>Tratamento

### <a name="entities"></a>Entidades

**TREATMENT_NAME** – procedimentos therapeutic. Por exemplo, joelho Replacement cirurgia, Bone Marrow transplant, TAVI, dieta.

:::image type="content" source="../../media/ta-for-health/treatment-entities-name.png" alt-text="Um exemplo de uma entidade de nome de tratamento.":::
