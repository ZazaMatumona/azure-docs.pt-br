---
title: Entidades predefinidas da dimensão – LUIS
titleSuffix: Azure Cognitive Services
description: Este artigo contém informações de entidade predefinida de dimensão em LUIS (Serviço Inteligente de Reconhecimento Vocal).
services: cognitive-services
ms.custom: seodec18
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: 3c7bebb38d0331c76eeaaa1d9d6464c568a61b83
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2020
ms.locfileid: "78270642"
---
# <a name="dimension-prebuilt-entity-for-a-luis-app"></a>Entidade de dimensão predefinida para um aplicativo LUIS
A entidade de dimensão predefinida detecta vários tipos de dimensões, independentemente da cultura do aplicativo do LUIS. Como essa entidade já está treinada, não é necessário adicionar expressões contendo dimensões às intenções do aplicativo. A entidade de dimensão tem suporte em [muitas culturas](luis-reference-prebuilt-entities.md).

## <a name="types-of-dimension"></a>Tipos de dimensão

A dimensão é gerenciada do repositório GitHub de [texto de reconhecedores](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-NumbersWithUnit.yaml) .

## <a name="resolution-for-dimension-entity"></a>Resolução para entidade de dimensão

Os seguintes objetos de entidade são retornados para a consulta:

`10 1/2 miles of cable`

#### <a name="v3-response"></a>[Resposta v3](#tab/V3)

O JSON a seguir é com `verbose` o parâmetro definido `false`como:

```json
"entities": {
    "dimension": [
        {
            "number": 10.5,
            "units": "Mile"
        }
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3, resposta detalhada](#tab/V3-verbose)
O JSON a seguir é com `verbose` o parâmetro definido `true`como:

```json
"entities": {
    "dimension": [
        {
            "number": 10.5,
            "units": "Mile"
        }
    ],
    "$instance": {
        "dimension": [
            {
                "type": "builtin.dimension",
                "text": "10 1/2 miles",
                "startIndex": 0,
                "length": 12,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

#### <a name="v2-response"></a>[Resposta v2](#tab/V2)

O exemplo a seguir mostra a resolução da entidade **builtin.dimension**.

```json
{
    "entity": "10 1/2 miles",
    "type": "builtin.dimension",
    "startIndex": 0,
    "endIndex": 11,
    "resolution": {
    "unit": "Mile",
    "value": "10.5"
    }
}
```
* * *

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o [ponto de extremidade de previsão V3](luis-migration-api-v3.md).

Saiba mais sobre as entidades [email](luis-reference-prebuilt-email.md), [número](luis-reference-prebuilt-number.md) e [ordinal](luis-reference-prebuilt-ordinal.md).
