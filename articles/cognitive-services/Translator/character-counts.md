---
title: Contagens de caracteres-Tradutor
titleSuffix: Azure Cognitive Services
description: Este artigo explica como o tradutor de serviços cognitivas do Azure conta os caracteres para que você possa entender como ele consome conteúdo.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: swmachan
ms.openlocfilehash: 7fad90cf47591b5ffab4232c2be3180138738f01
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/27/2020
ms.locfileid: "83992820"
---
# <a name="how-the-translator-counts-characters"></a>Como o tradutor conta caracteres

O tradutor conta cada ponto de código Unicode de texto de entrada como um caractere. Cada tradução de um texto para um idioma conta como uma tradução separada, mesmo que a solicitação tenha sido feita em uma única chamada à API que traduziu para vários idiomas. O tamanho da resposta não é importante.

O que conta é:

* Texto passado para o tradutor no corpo da solicitação
   * `Text` ao usar os métodos Tradução, Transliteração e Pesquisa no dicionário
   * `Text` e `Translation` ao usar o método Exemplos de dicionário
* Todas as marcações: HTML, marcas XML, etc. no campo de texto do corpo da solicitação. A notação JSON usada para construir a solicitação (por exemplo "Text:") não é contabilizada.
* Uma letra individual
* Pontuação
* Espaço, tabulação, marcação e qualquer tipo de caractere de espaço em branco
* Cada ponto de código definido no Unicode
* Uma tradução repetida, mesmo que você já tenha traduzido o mesmo texto anteriormente

Para scripts baseados em ideograms como chinês e japonês kanji, o serviço de Tradutor ainda conta o número de pontos de código Unicode, um caractere por ideogram. Exceção: os substitutos Unicode contam como dois caracteres.

O número de solicitações, palavras, bytes ou frases é irrelevante na contagem de caracteres.

Chamadas para os métodos Detect e BreakSentence não são contadas no consumo de caractere. No entanto, esperamos que as chamadas para os métodos Detect e BreakSentence sejam de uma proporção razoável para o uso de outras funções que são contadas. Se o número de chamadas Detect ou BreakSentence feitas exceder 100 vezes o número de outros métodos contabilizados, a Microsoft se reservará o direito de restringir o uso dos métodos Detect e BreakSentence.

Mais informações sobre contagens de caracteres estão nas [perguntas frequentes do tradutor](https://www.microsoft.com/en-us/translator/faq.aspx).
