---
title: 'Início Rápido: Biblioteca de clientes do Reconhecimento de Formulários para .NET'
description: Neste início rápido, comece a usar a biblioteca de clientes do Reconhecimento de Formulários para .NET.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 08/17/2020
ms.author: pafarley
ms.openlocfilehash: 480c1e991225f707b6497849b6e8d8b5c4124f21
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88505387"
---
[Documentação de referência](https://docs.microsoft.com/dotnet/api/overview/azure/ai.formrecognizer-readme-pre) | [Código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/src) | [Pacote (NuGet)](https://www.nuget.org/packages/Azure.AI.FormRecognizer) | [Exemplos](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md)

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure – [crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services).
* Um blob do Armazenamento do Azure contendo um conjunto de dados de treinamento. Confira [Criar um conjunto de dados de treinamento para um modelo personalizado](../../build-training-data-set.md) para obter dicas e opções para compilar o conjunto de dados de treinamento. Para este guia de início rápido, você pode usar os arquivos na pasta **Train** do [conjunto de dados de exemplo](https://go.microsoft.com/fwlink/?linkid=2090451).
* A versão atual do [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).

## <a name="setting-up"></a>Configurando

### <a name="create-a-form-recognizer-azure-resource"></a>Criar um recurso do Azure do Reconhecimento de Formulários

[!INCLUDE [create resource](../create-resource.md)]

### <a name="create-environment-variables"></a>Criar variáveis de ambiente

[!INCLUDE [environment-variables](../environment-variables.md)]

### <a name="create-a-new-c-application"></a>Criar um aplicativo em C#

Em uma janela de console (como cmd, PowerShell ou Bash), use o comando `dotnet new` para criar um novo aplicativo do console com o nome `formrecognizer-quickstart`. Esse comando cria um projeto simples C# "Olá, Mundo" com um arquivo de origem único: _Program.cs_. 

```console
dotnet new console -n formrecognizer-quickstart
```

Altere o diretório para a pasta do aplicativo recém-criado. Em seguida, compile o aplicativo com:

```console
dotnet build
```

A saída de compilação não deve conter nenhum aviso ou erro. 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

No diretório do projeto, abra o arquivo *Program.cs* no IDE ou no editor de sua preferência. Adicione as seguintes diretivas `using`:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_using)]

Em seguida, adicione o código a seguir ao método **Main** do aplicativo. Você definirá essa tarefa assíncrona posteriormente. 

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_main)]

### <a name="install-the-client-library"></a>Instalar a biblioteca de clientes

Dentro do diretório do aplicativo, instale a biblioteca de clientes do Reconhecimento de Formulários para .NET com o seguinte comando:

```console
dotnet add package Azure.AI.FormRecognizer --version 1.0.0-preview.3
```

Se você estiver usando o IDE do Visual Studio, a biblioteca de clientes estará disponível como um pacote baixável do NuGet.


<!-- Objet model TBD -->



## <a name="code-examples"></a>Exemplos de código

Estes snippets de códigos mostram como realizar as seguintes tarefas com a biblioteca de clientes do Reconhecimento de Formulários para .NET:

* [Autenticar o cliente](#authenticate-the-client)
* [Reconhecer o conteúdo do formulário](#recognize-form-content)
* [Reconhecer recibos](#recognize-receipts)
* [Treinar um modelo personalizado](#train-a-custom-model)
* [Analisar formulários com um modelo personalizado](#analyze-forms-with-a-custom-model)
* [Gerenciar seus modelos personalizados](#manage-your-custom-models)


## <a name="authenticate-the-client"></a>Autenticar o cliente

Abaixo do método `Main`, defina a tarefa que é referenciada em `Main`. Aqui, você autenticará os objetos de cliente usando as variáveis de assinatura definidas acima. Você usará um objeto **AzureKeyCredential**, para, se necessário, atualizar a chave de API sem criar objetos de cliente.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_auth)]


### <a name="call-client-specific-methods"></a>Chamar métodos específicos do cliente

O próximo bloco de código usa os objetos de cliente para chamar métodos para cada uma das principais tarefas no SDK do Reconhecimento de Formulários. Você definirá esses métodos mais tarde.

Você também precisará adicionar referências às URLs para os dados de treinamento e teste. 
* Para recuperar a URL de SAS para os dados de treinamento do modelo personalizado, abra o Gerenciador de Armazenamento do Microsoft Azure, clique com o botão direito do mouse no contêiner e selecione **Obter Assinatura de Acesso Compartilhado**. Verifique se as permissões de **Leitura** e **Lista** estão marcadas e clique em **Criar**. Em seguida, copie o valor na seção **URL**. Deve ter o formato: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.
* Para obter uma URL de um formulário para teste, use as etapas acima para obter a URL de SAS de um documento individual no armazenamento de blobs. Ou então, selecione a URL de um documento localizado em outro lugar.
* Use o método acima para obter a URL de uma imagem de recibo também ou use a URL da imagem de exemplo fornecida.

> [!NOTE]
> Os snippets de código deste guia usam formulários remotos acessados por URLs. Caso deseje processar documentos de formulário local, confira os métodos relacionados na [documentação de referência](https://docs.microsoft.com/dotnet/api/overview/azure/ai.formrecognizer-readme-pre).

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_calls)]

## <a name="recognize-form-content"></a>Reconhecer o conteúdo do formulário

Use o Reconhecimento de Formulários para reconhecer tabelas, linhas e palavras em documentos, sem a necessidade de treinar um modelo.

Para reconhecer o conteúdo de um arquivo em um URI especificado, use o método **StartRecognizeContentFromUri**.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_getcontent_call)]


O valor retornado é uma coleção de objetos **FormPage**: um para cada página no documento enviado. O código a seguir itera nesses objetos e imprime os pares de chave/valor extraídos e os dados da tabela.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_getcontent_print)]


## <a name="recognize-receipts"></a>Reconhecer recibos

Esta seção demonstra como reconhecer e extrair campos comuns de recibos dos EUA usando um modelo de recibo pré-treinado.

Para reconhecer recibos de um URI, use o método **StartRecognizeReceiptsFromUri**. O valor retornado é uma coleção de objetos **RecognizedReceipt**: um para cada página no documento enviado. O código a seguir processa um recibo no URI fornecido e imprime os valores e os campos principais no console.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_receipt_call)]


O próximo bloco de código itera em itens individuais detectados no recibo e imprime os detalhes no console.
[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_receipt_item_print)]

Por fim, o último bloco de código imprime o valor total no recibo.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_receipt_total_print)]

## <a name="train-a-custom-model"></a>Treinar um modelo personalizado

Esta seção demonstra como treinar um modelo com os próprios dados. Um modelo treinado pode gerar dados estruturados que incluem as relações de chave/valor no documento de formulário original. Depois de treinar no modelo, você pode testá-lo, treinar novamente e, por fim, usá-lo para extrair, de forma confiável, dados de mais formulários de acordo com suas necessidades.

> [!NOTE]
> Você também pode treinar modelos com uma interface gráfica do usuário, como a [ferramenta de rotulagem de exemplo Reconhecimento de Formulários](../../quickstarts/label-tool.md).

### <a name="train-a-model-without-labels"></a>Treinar um modelo sem rótulos

Treine modelos personalizados para reconhecer todos os campos e valores encontrados nos formulários personalizados sem rotular manualmente os documentos de treinamento.

O método a seguir treina um modelo em um especificado conjunto de documentos e imprime o status do modelo no console. 

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_train)]

O objeto **CustomFormModel** retornado contém informações sobre os tipos de formulários que o modelo pode reconhecer e os campos que ele pode extrair de cada tipo de formulário. O bloco de código a seguir imprime essas informações no console.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_train_response)]

Por fim, esse método retorna a ID exclusiva do modelo.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_train_return)]

### <a name="train-a-model-with-labels"></a>Treinar um modelo com rótulos

Você também pode treinar modelos personalizados rotulando manualmente os documentos de treinamento. O treinamento com rótulos leva a um melhor desempenho em alguns cenários. Para o treinamento com rótulos, você precisará ter arquivos de informações de rótulo especiais ( *\<filename\>.pdf.labels.json*) no contêiner de armazenamento de blobs junto com os documentos de treinamento. A [ferramenta de rotulagem de exemplo Reconhecimento de Formulários](../../quickstarts/label-tool.md) fornece uma interface do usuário para ajudar você a criar esses arquivos de rótulo. Depois de obtê-los, chame o método **StartTrainingAsync** com o parâmetro *uselabels* definido como `true`.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_trainlabels)]

O **CustomFormModel** retornado indica os campos que o modelo pode extrair, juntamente com a precisão estimada em cada campo. O bloco de código a seguir imprime essas informações no console.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_trainlabels_response)]

## <a name="analyze-forms-with-a-custom-model"></a>Analisar formulários com um modelo personalizado

Esta seção demonstra como extrair informações de chave/valor e outro conteúdo dos tipos de formulário personalizados, usando modelos treinados com os próprios formulários.

> [!IMPORTANT]
> Para implementar esse cenário, você já precisará ter treinado um modelo para passar a ID para o método abaixo. Confira a seção [Treinar um modelo](#train-a-model-without-labels).

Você usará o método **StartRecognizeCustomFormsFromUri**. O valor retornado é uma coleção de objetos **RecognizedForm**: um para cada página no documento enviado.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_analyze)]

O código a seguir imprime os resultados da análise no console. Ele imprime cada campo reconhecido e o valor correspondente, juntamente com uma pontuação de confiança.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_analyze_response)]

## <a name="manage-your-custom-models"></a>Gerenciar seus modelos personalizados

Esta seção demonstra como gerenciar os modelos personalizados armazenados na sua conta. O código a seguir executa todas as tarefas de gerenciamento de modelos em um só método, como um exemplo. Comece copiando a assinatura de método abaixo:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_manage)]

### <a name="check-the-number-of-models-in-the-formrecognizer-resource-account"></a>Verificar o número de modelos na conta do recurso do FormRecognizer

O bloco de código a seguir verifica quantos modelos você salvou na sua conta do Reconhecimento de Formulários e os compara com o limite da conta.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_manage_model_count)]

### <a name="list-the-models-currently-stored-in-the-resource-account"></a>Listar os modelos atualmente armazenados na conta do recurso

O bloco de código a seguir lista os modelos atuais na sua conta e imprime os detalhes no console.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_manage_model_list)]

### <a name="get-a-specific-model-using-the-models-id"></a>Obter um modelo específico usando a ID do modelo

O bloco de código a seguir treina um novo modelo (assim como na seção [Treinar um modelo](#train-a-model-without-labels)) e recupera uma segunda referência a ele usando a respectiva ID.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_manage_model_get)]

### <a name="delete-a-model-from-the-resource-account"></a>Excluir um modelo da conta do recurso

Exclua também um modelo da sua conta referenciando a ID.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_manage_model_delete)]

## <a name="run-the-application"></a>Executar o aplicativo

Execute o aplicativo do seu diretório de aplicativo com o comando `dotnet run`.

```dotnet
dotnet run
```

## <a name="clean-up-resources"></a>Limpar os recursos

Se quiser limpar e remover uma assinatura dos Serviços Cognitivos, você poderá excluir o recurso ou grupo de recursos. Excluir o grupo de recursos também exclui todos os recursos associados a ele.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [CLI do Azure](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="troubleshooting"></a>Solução de problemas

Quando você interagir com a biblioteca de clientes do Reconhecimento de Formulários de Serviços Cognitivos usando o SDK do .NET, os erros retornados pelo serviço resultarão em uma `RequestFailedException`. Eles incluirão o mesmo código de status HTTP que teria sido retornado por uma solicitação da API REST.

Por exemplo, se você enviar uma imagem de recibo com um URI inválido, um erro `400` será retornado, indicando "Solicitação inválida".

```csharp
try
{
    RecognizedReceiptCollection receipts = await client.StartRecognizeReceiptsFromUri(new Uri(receiptUri)).WaitForCompletionAsync();
}
catch (RequestFailedException e)
{
    Console.WriteLine(e.ToString());
}
```

Você observará que as informações adicionais, como a ID de solicitação do cliente da operação, são registradas em log.

```
Message:
    Azure.RequestFailedException: Service request failed.
    Status: 400 (Bad Request)

Content:
    {"error":{"code":"FailedToDownloadImage","innerError":
    {"requestId":"8ca04feb-86db-4552-857c-fde903251518"},
    "message":"Failed to download image from input URL."}}

Headers:
    Transfer-Encoding: chunked
    x-envoy-upstream-service-time: REDACTED
    apim-request-id: REDACTED
    Strict-Transport-Security: REDACTED
    X-Content-Type-Options: REDACTED
    Date: Mon, 20 Apr 2020 22:48:35 GMT
    Content-Type: application/json; charset=utf-8
```

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você usou a biblioteca de clientes .NET do Reconhecimento de Formulários para treinar modelos personalizado e analisar formulários de diferentes maneiras. Em seguida, aprenda dicas para criar um conjunto de dados de treinamento melhor e produzir modelos mais precisos.

> [!div class="nextstepaction"]
> [Criar um conjunto de dados de treinamento](../../build-training-data-set.md)

* [O que é o Reconhecimento de Formulários?](../../overview.md)
* Encontre o código de exemplo deste guia (e outros) no [GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md).
