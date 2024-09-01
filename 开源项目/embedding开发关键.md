
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
