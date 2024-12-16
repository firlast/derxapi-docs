# Documentação versão `v1_test`

A DerxAPI facilita a validação de endereços de email dos seus clientes. Com apenas dois endpoints, você pode criar e checar os códigos de confirmação no seu produto. Nessa documentação, você aprenderá a utilizar nossos endpoints e implementar no seu produto.

> Documentação criada apenas para fins de desenvolvimento.

## Utilizando a DerxAPI

Para começar a utilizar siga os passos a seguir para criar sua conta e obter seu token de acesso:

1. [Crie sua conta e ative seu plano](https://derxapi.com/register)
2. [Faça login na sua conta](https://derxapi.com/login)
3. [Obter seu token de acesso](https://derxapi.com/token) (copie para a área de transferência)

### Enviando códigos de confirmação

Para enviar códigos de confirmação para seus clientes, utilize o endpoint `service.derxapi.com/send-code` com o método `POST` com os seguintes dados:

```json
{
  "email": "emaildousuario@domain.com",
  "template": {
    "name": "001",
    "placeholder": {
      "company": "company_name"
    }
  } 
}
```

- `email`: Email do seu usuário
- `template`: Informações do template do email
  - `name`: Nome do template a ser utilizado (consulte os [templates disponíveis](https://github.com/firlast/derxapi-docs/blob/main/docs/templates.md))
  - `placeholder`: Objeto com dados requeridos pelo template escolhido (consulte os placeholders nos [templates disponíveis](https://github.com/firlast/derxapi-docs/blob/main/docs/templates.md))

```python3
import requests

DERXAPI_TOKEN = '<seu_token_aqui>'

req = requests.post(
    url='https://service.derxapi.com/send-code',
    headers={'X-API-Token': DERXAPI_TOKEN},
    json={
        'email': 'emaildousuario@domain.com',
        'template': {
            'name': '001',
            'placeholder': {
            'company': 'company_name'
            }
        } 
    }
)

# Obtenha o ID da sua requisição
req.json()['request_id']
```

Após isso, o codígo de confirmação é criado e enviado ao email do seu usuário. É necessário armazenar o `request_id` recebido do endpoint `service.derxapi.com/send-code` para checar o código inserido pelo usuário posteriormente.

## Confirmando os códigos

Para confirmar códigos enviados aos seus clientes, utilize o endpoint `service.derxapi.com/check-code` com o método `GET` com os seguintes parâmetros de URL:

- `request_id`: ID da requisição (obtido ao criar o código em `/send-code`)
- `code`: Código inserindo pelo seu usuário

```python3
import requests

DERXAPI_TOKEN = '<seu_token_aqui>'

req = requests.post(
    url='https://service.derxapi.com/check-code',
    headers={'X-API-Token': DERXAPI_TOKEN},
    params={
        'request_id': '<id_da_requisição>',
        'code': '<codigo_inserido_pelo_usuario>'
    }
)

# Verifique se o código inserido está correto

if req.status_code == 418:  # O código inserido está incorreto ou expirou
    print('O código é inválido ou expirou!')
else:
    print('Email confirmado!')
```

Com isso, você pode confirmar o código inserido pelo usuário e verificar se está correto ou não. Você sempre receberá o status HTTP `418` caso o código esteja incorreto ou tenha expirado

> O tempo de expiração de um código é de 5 minutos
