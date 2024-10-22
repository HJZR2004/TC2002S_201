# Completion and image functions

### 1. Deployment de las funciones.

- Como primera parte de la clase subimos las funciones a la Azure, de forma que ahora las podemos usar en cualquier momento.

![funciones](./imagenes/segundo_dia/funciones.png)
![funciones](./imagenes/segundo_dia/functionAzure.png)

### 2. Agregar `completionAPI`.

- Tuvimos que crear una nueva funcion que nos ayudara a genera texto:

```python  
@app.route(route="completationAPI", auth_level=func.AuthLevel.ANONYMOUS)
  def completationAPI(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    client=OpenAI(
        api_key=secret_key,
    )

    req_body = req.get_json()

    completion = client.chat.completions.create(

          model=req_body["model"],
        messages=[
            {"role": "system", "content": "You are a helpful assistant."},
            {"role": "user", "content":req_body["prompt"]},],

         max_tokens=req_body["max_tokens"],
        temperature=req_body["temperature"]
        )
    return func.HttpResponse(completion.choices[0].message.content, status_code=200)

```
- Despues le teniamos que mandar un json de forma POST para que nos pudiera retornar un texto para saber que fue configurado de forma correcta.
![respuestaCompletion](./imagenes/segundo_dia/completionAPI.png)

### 3. Agregar `imageAPI`.
- Como segunda parte de la clase tuvimos que agregar una funcion que nos ayude a generar imagenes, e implementamos este codigo.

```python
@app.route(route="imageAPI", auth_level=func.AuthLevel.ANONYMOUS)
def imageAPI(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')
    client = OpenAI(api_key=secret_key)


    req_body=req.get_json()
    response = client.images.generate(
        model="dall-e-3",
        prompt=req_body["prompt"],
        size="1024x1024",
        quality="standard",
        n=1,
    )
   
    return func.HttpResponse(response.data[0].url,status_code=200)

```

- Para comprobar el correcto funcionamiento de la nueva funcion, hicimos un request a Thunder Client, el cual nos devolvivio un url de una imagen.

![ThunderClientImage](./imagenes/segundo_dia/thunderClientImageAPI.png)

![imagenGenerada](./imagenes/segundo_dia/chavezBate.png)

- [URL de la imagen](https://oaidalleapiprodscus.blob.core.windows.net/private/org-lmMuYnxJeKRH6AsIbw2ZoknU/user-etKXkieg8P0nAddtMrUKGJA6/img-lxdREeOFvZxJD1pgEGgK0ZoR.png?st=2024-10-22T16%3A57%3A17Z&se=2024-10-22T18%3A57%3A17Z&sp=r&sv=2024-08-04&sr=b&rscd=inline&rsct=image/png&skoid=d505667d-d6c1-4a0a-bac7-5c84a87759f8&sktid=a48cca56-e6da-484e-a814-9c849652bcb3&skt=2024-10-21T18%3A40%3A08Z&ske=2024-10-22T18%3A40%3A08Z&sks=b&skv=2024-08-04&sig=r9zd95/i4t2CZogqqteME5Y2o6RCqLh7enLLB1Gl428%3D)
