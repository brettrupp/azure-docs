---
title: "Quickstart: Convert text script, Go - Translator Text API"
titleSuffix: Azure Cognitive Services
description: In this quickstart, you convert text in one language from one script to another using the Translator Text API with Go.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 12/05/2018
ms.author: erhopf
---
# Quickstart: Use the Translator Text API to transliterate text using Go

In this quickstart, you convert text in one language from one script to another using the Translator Text API.

This quickstart requires an [Azure Cognitive Services account](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) with a Translator Text resource. If you don't have an account, you can use the [free trial](https://azure.microsoft.com/try/cognitive-services/) to get a subscription key.

## Prerequisites

This quickstart requires:

* [Go](https://golang.org/doc/install)
* An Azure subscription key for Translator Text

## Create a project and import required modules

Create a new Go project using your favorite IDE or editor. Then copy this code snippet into your project in a file named `transliterate-text.go`.

```go
package main

import (
    "bytes"
    "encoding/json"
    "fmt"
    "log"
    "net/http"
    "net/url"
    "os"
)
```

## Create the main function

This sample will try to read your Translator Text subscription key from the environment variable `TRANSLATOR_TEXT_KEY`. If you're not familiar with environment variables, you can set `subscriptionKey` as a string and comment out the conditional statement.

Copy this code into your project:

```go
func main() {
    /*
     * Read your subscription key from an env variable.
     * Please note: You can replace this code block with
     * var subscriptionKey = "YOUR_SUBSCRIPTION_KEY" if you don't
     * want to use env variables.
     */
    subscriptionKey := os.Getenv("TRANSLATOR_TEXT_KEY")
    if subscriptionKey == "" {
       log.Fatal("Environment variable TRANSLATOR_TEXT_KEY is not set.")
    }
    /*
     * This calls our transliterate function, which we'll
     * create in the next section. It takes a single argument,
     * the subscription key.
     */
    transliterate(subscriptionKey)
}
```

## Create a function to transliterate text

Let's create a function to transliterate text. This function will take a single argument, your Translator Text subscription key.

```go
func transliterate(subscriptionKey string) {
    /*  
     * In the next few sections, we'll add code to this
     * function to make a request and handle the response.
     */
}
```

Next, let's construct the URL. The URL is built using the `Parse()` and `Query()` methods. You'll notice that parameters are added with the `Add()` method. In this sample, we're transliterating from Japanese to the Latin alphabet.

Copy this code into the `transliterate` function.

```go
// Build the request URL. See: https://golang.org/pkg/net/url/#example_URL_Parse
u, _ := url.Parse("https://api.cognitive.microsofttranslator.com/transliterate?api-version=3.0")
q := u.Query()
q.Add("language", "ja")
q.Add("fromScript", "jpan")
q.Add("toScript", "latn")
u.RawQuery = q.Encode()
```

>[!NOTE]
> For more information about endpoints, routes, and request parameters, see [Translator Text API 3.0: Transliterate](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-transliterate).

## Create a struct for your request body

Next, create an anonymous structure for the request body and encode it as JSON with `json.Marshal()`. Add this code to the `transliterate` function.

```go
// Create an anonymous struct for your request body and encode it to JSON
body := []struct {
	Text string
}{
	{Text: "こんにちは"},
}
b, _ := json.Marshal(body)
```

## Build the request

Now that you've encoded the request body as JSON, you can build your POST request, and call the Translator Text API.

```go
// Build the HTTP POST request
req, err := http.NewRequest("POST", u.String(), bytes.NewBuffer(b))
if err != nil {
    log.Fatal(err)
}
// Add required headers to the request
req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)
req.Header.Add("Content-Type", "application/json")

// Call the Translator Text API
res, err := http.DefaultClient.Do(req)
if err != nil {
    log.Fatal(err)
}
```

## Handle and print the response

Add this code to the `transliterate` function to decode the JSON response, and then format and print the result.

```go
// Decode the JSON response
var result interface{}
if err := json.NewDecoder(res.Body).Decode(&result); err != nil {
	log.Fatal(err)
}
// Format and print the response to terminal
prettyJSON, _ := json.MarshalIndent(result, "", "  ")
fmt.Printf("%s\n", prettyJSON)
```

## Put it all together

That's it, you've put together a simple program that will call the Translator Text API and return a JSON response. Now it's time to run your program:

```console
go run transliterate-text.go
```

If you'd like to compare your code against ours, the complete sample is available on [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Go).

## Sample response

```json
[
  {
    "script": "latn",
    "text": "konnnichiha"
  }
]
```

## Next steps

Explore Go packages for Cognitive Services APIs from the [Azure SDK for Go](https://github.com/Azure/azure-sdk-for-go) on GitHub.

> [!div class="nextstepaction"]
> [Explore Go packages on GitHub](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices)

## See also

Learn how to use the Translator Text API to:

* [Translate text](quickstart-go-translate.md)
* [Identify the language by input](quickstart-go-detect.md)
* [Get alternate translations](quickstart-go-dictionary.md)
* [Get a list of supported languages](quickstart-go-languages.md)
* [Determine sentence lengths from an input](quickstart-go-sentences.md)
