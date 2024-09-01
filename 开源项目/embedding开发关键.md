
### Ollama official rest api document
https://github.com/ollama/ollama/blob/main/docs/api.md

Generate Embeddings: 

Parameters
- `model`: name of model to generate embeddings from
- `input`: text or list of text to generate embeddings for


Request:

```shell
curl http://localhost:11434/api/embed -d '{
  "model": "llama3",
  "input": "Why is the sky blue?"
}'
```


Description:
1. curl
curl is a command-line tool used to send HTTP requests. It supports various protocols including HTTP and HTTPS.

2. http://localhost:11434/api/embed
This is the URL of the request, composed of:
- http:// – the protocol.
- localhost – the hostname, indicating that the request is sent to the local machine.
- 11434 – the port number on which the server is listening.
- /api/embed – the path of the API endpoint being accessed.

1. -d
The -d option specifies the data to be sent in the request body. This is used with POST requests to send data to the server, often for creating or updating resources.

4. Request Body Data
The data sent in the request body is a JSON object:
```json
{
  "model": "llama3",
  "input": "Why is the sky blue?"
}
```

response body :
```json
{
"model":"llama3",
"embeddings":[
[4096]
],
"total_duration":7472437416,
"load_duration":7335258708,
"prompt_eval_count":6
}
```


### Interacts with Ollama REST API in Golang

My code interacts with a REST API by sending a POST request with JSON data, and it processes the JSON response to extract embeddings

#### 1. json.Marshal

json.Marshal is a function in the Go standard library's encoding/json package. It is used to encode Go data structures (such as structs, slices, and maps) into JSON format. This process is known as "serialization" or "encoding."

Note the struct tag like `json:"model` has a significant role when encoding and decoding JSON data. When you use json.Marshal to convert a Go struct to JSON, the json:"model" tag specifies that the Model field in the JSON output should be named "model".

```go
type OllamaSingleEmbeddingRequest struct {
	Model string `json:"model"`
	Input string `json:"input"`
}

// take single input, make a POST request to Ollama API and return embedding
func getOllamaSingleEmbedding(input string, model string) ([]float32, error) {
	payload := OllamaSingleEmbeddingRequest{
		Model: model,
		Input: input,
	}

	// Marshal the payload to JSON
	requestBody, err := json.Marshal(payload)
	
	// ...
}
```

#### 2. Prepare & send the HTTP request

```go
func callOllamaService(requestBody []byte) ([][]float32, error) {
	// Prepare & send the HTTP request
	req, err := http.NewRequest("POST", "http://localhost:11434/api/embed", bytes.NewBuffer(requestBody))

	req.Header.Set("Content-Type", "application/json")

	client := &http.Client{}
	resp, err := client.Do(req)
	defer resp.Body.Close()
}

```

#### 3. Read response body & json.Unmarshal

```go
type OllamaEmbeddingResponse struct {
	Model           string      `json:"model"`
	Embeddings      [][]float32 `json:"embeddings"`
	TotalDuration   int64       `json:"total_duration"`
	LoadDuration    int64       `json:"load_duration"`
	PromptEvalCount int         `json:"prompt_eval_count"`
}

func callOllamaService(requestBody []byte) ([][]float32, error) {
	// ...
	
	// Check the status code
	if resp.StatusCode != http.StatusOK {
		bodyBytes, _ := io.ReadAll(resp.Body)
		return nil, fmt.Errorf("received non-200 response: %d, body: %s", resp.StatusCode, string(bodyBytes))
	}

	// Read the response body
	body, err := io.ReadAll(resp.Body)

	var embeddingResponse OllamaEmbeddingResponse
	err = json.Unmarshal(body, &embeddingResponse)
	if err != nil {
		return nil, fmt.Errorf("failed to unmarshal response body: %v", err)
	}

	return embeddingResponse.Embeddings, nil
}



```




#### 4. 实现接口


##### 4.1 接口实现

在Go语言中，一个类型实现了一个接口的标准方式是通过提供接口中定义的所有方法的具体实现。接口并不需要显式地声明它的实现，Go语言使用隐式实现。这意味着只要一个类型（如 OllamaEmbeddingService）实现了接口中定义的所有方法，它就自动实现了这个接口。
```go
package function

type EmbeddingService interface {
	GetEmbedding(input string, model string, proxy string) ([]float32, error)
}

type OllamaEmbeddingService struct{}

func (o *OllamaEmbeddingService) GetEmbedding(input string, model string, proxy string) ([]float32, error) {
	return getOllamaSingleEmbedding(input, model, proxy)
}

```

OllamaEmbeddingService 类型定义了一个方法 GetEmbedding，它的签名与 EmbeddingService 接口中的方法完全一致。这使得 OllamaEmbeddingService 自动实现了 EmbeddingService 接口。

##### 4.2 指针接收者
在Go中，方法接收者的类型决定了方法是否可以修改接收者的状态，以及如何在方法内部操作这个接收者。接收者可以是值接收者或指针接收者。\

指针接收者：方法接收者是类型的指针，方法可以直接修改原始对象的状态
```go
func (e *Example) MethodName() {
    // 可以修改e的内容
}
```



在我的示例中，*OllamaEmbeddingService 是 OllamaEmbeddingService 类型的指针：
```go
type OllamaEmbeddingService struct{}

func (o *OllamaEmbeddingService) GetEmbedding(input string, model string, proxy string) ([]float32, error) {
    return getOllamaSingleEmbedding(input, model, proxy)
}

```

o 是接收者变量：在方法 GetEmbedding 中，o 是 OllamaEmbeddingService 类型的指针接收者。它代表了调用该方法的实例（即 *OllamaEmbeddingService 类型的值）。





