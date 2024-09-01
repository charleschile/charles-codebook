
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

// take single input, make a POST request to Ollama API and return embedding
func getOllamaSingleEmbedding(input string, model string) ([]float32, error) {
	payload := OllamaSingleEmbeddingRequest{
		Model: model,
		Input: input,
	}

	// Marshal the payload to JSON
	requestBody, err := json.Marshal(payload)
	if err != nil {
		return nil, fmt.Errorf("failed to marshal request body: %v", err)
	}

	embeddings, err := callOllamaService(requestBody)

	return embeddings[0], nil
}



```