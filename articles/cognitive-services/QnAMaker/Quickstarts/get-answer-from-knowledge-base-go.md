---
title: 'Início Rápido: Obter respostas da base de dados de conhecimento – REST, Go – QnA Maker'
description: Este início rápido de Go baseado em REST fornece uma orientação para obtenção de uma resposta de uma base de dados de conhecimento de forma programática.
ms.date: 02/08/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27
ms.topic: how-to
ms.openlocfilehash: 3899fdb84e2263ea78aa70669a5fde88088b6fc5
ms.sourcegitcommit: 61d850bc7f01c6fafee85bda726d89ab2ee733ce
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/03/2020
ms.locfileid: "84342809"
---
# <a name="quickstart-get-answers-to-a-question-from-a-knowledge-base-with-go"></a>Início rápido: Obtenha respostas para uma pergunta de uma base de dados de conhecimento com go

Este início rápido o orienta a obter programaticamente uma resposta de uma base de dados de conhecimento publicada do QnA Maker. A base de conhecimento contém perguntas e respostas de [fontes de dados](../Concepts/knowledge-base.md), como perguntas frequentes. A [pergunta](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration) é enviada para o serviço QnA Maker. A [resposta](../how-to/metadata-generateanswer-usage.md#generateanswer-response-properties) inclui a resposta prevista na parte superior.

[Documentação](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime)  |  de referência [Exemplo](https://github.com/Azure-Samples/cognitive-services-qnamaker-go/blob/master/documentation-samples/quickstarts/get-answer/get-answer.go) de

## <a name="prerequisites"></a>Pré-requisitos

* [Go 1.10.1](https://golang.org/dl/)
* [Visual Studio Code](https://code.visualstudio.com/)
* Você precisa ter um [serviço QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Para recuperar sua chave, selecione **Chaves** em **Gerenciamento de Recursos** no painel do Azure para seu recurso do QnA Maker.
* Configurações da página **Publicar**. Caso não tenha uma base de dados de conhecimento publicada, crie uma base de dados de conhecimento vazia, importe-a na página **Configurações** e depois publique-a. Você pode baixar e usar [essa base de dados de conhecimento básico](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/knowledge-bases/basic-kb.tsv).

    As configurações da página de publicação incluem o valor da rota, o valor do Host e o valor do EndpointKey.

    ![Configurações de publicação](../media/qnamaker-quickstart-get-answer/publish-settings.png)

## <a name="create-a-go-file"></a>Criar um arquivo Go

Abra o VSCode, crie um novo arquivo chamado `get-answer.go` e adicione a seguinte classe:

```Go
package main

func main() {

}
```

## <a name="add-the-required-dependencies"></a>Adicione as dependências necessárias

Acima da função `main`, na parte superior do arquivo `get-answer.go`, adicione as dependências necessárias ao projeto:

[!code-go[Add the required dependencies](~/samples-qnamaker-go/documentation-samples/quickstarts/get-answer/get-answer.go?range=3-9 "Add the required dependencies")]

## <a name="add-the-required-constants"></a>Adicionar as constantes necessárias

Na parte superior da função `main`, adicione as constantes necessárias para acessar o QnA Maker. Esses valores ficam na página **Publicar** após a publicação da base de dados de conhecimento.

[!code-go[Add the required constants](~/samples-qnamaker-go/documentation-samples/quickstarts/get-answer/get-answer.go?range=17-33 "Add the required constants")]

## <a name="add-a-post-request-to-send-question-and-get-answer"></a>Adicionar uma solicitação POST para enviar perguntas e obter uma resposta

O código a seguir faz uma solicitação HTTPS à API de QnA Maker para enviar a pergunta à base de dados de conhecimento e recebe a resposta:

[!code-go[Add a POST request to send question to knowledge base](~/samples-qnamaker-go/documentation-samples/quickstarts/get-answer/get-answer.go?range=35-48 "Add a POST request to send question to knowledge base")]

O valor do cabeçalho de `Authorization` inclui a cadeia de caracteres `EndpointKey`.

Saiba mais sobre a [solicitação](../how-to/metadata-generateanswer-usage.md#generateanswer-request) e a [resposta](../how-to/metadata-generateanswer-usage.md#generateanswer-response).

## <a name="build-and-run-the-program"></a>Compilar e executar o programa

Compilar e executar o programa da linha de comando. Ela enviará automaticamente a solicitação para a API de QnA Maker, depois a resposta será impressa na janela do console.

1. Compile o arquivo:

    ```bash
    go build get-answer.go
    ```

1. Execute o arquivo:

    ```bash
    ./get-answer
    ```

[!INCLUDE [JSON request and response](../../../../includes/cognitive-services-qnamaker-quickstart-get-answer-json.md)]


[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)]

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Referência da API REST do QnA Maker (V4)](https://go.microsoft.com/fwlink/?linkid=2092179)