# Documentation version `V1`

DerxAPI makes it easy to validate your customers' email addresses. With just two endpoints, you can create and check confirmation codes in your product. In this documentation, you will learn how to use our endpoints and implement them in your product.

## Using DerxAPI

To start using, follow the steps below to create your account and obtain your access token:

1. [Create your account and activate your plan](https://derxapi.com/register)
2. [Log in to your account](https://derxapi.com/login)
3. [Get your access token](https://derxapi.com/token) (copy to clipboard)

### DerxAPI plans

DerxAPI currently has 3 plans available for users, including a free plan for those who want to test our services. See below the list of characteristics of each plan:

- **Free Plan**:
  - Send up to 150 codes per month
  - Only one HTML template available
  - No template translation
  - Only `letters_numbers` code format available
- **Basic Plan**:
  - Send up to 1,000 codes per month
  - Access to all available templates
  - No template translation
  - All code formats availables
- **Inter Plan**:
  - Send up to 10,000 codes per month
  - Access to all available templates
  - Template translation into 3 languages ​​(Portuguese, English and Spanish)
  - All code formats availables

### Sending confirmation codes

To send confirmation codes to your customers, use the `service.derxapi.com/send-code` endpoint with the `POST` method with the following data:

```json
{
  "email": "usermail@domain.com",
  "template": {
    "name": "001",
    "lang": "<en/es/pt-br>",
    "placeholder": {
      "company": "company_name"
    }
  } 
}
```

- `email`: Your user's email
- `code_type` (optional): Format of the code sent. You can choose between `letters_numbers` (letters and numbers), `only_letters` (only letters) and `only_numbers` (only numbers). If the field is not specified, the default value is `letters_numbers`
- `template`: Email template information
  - `name`: Name of the template to be used (see [available templates](https://github.com/firlast/derxapi-docs/blob/main/docs/templates.md))
  - `lang` (optional): Template language (check if your plan supports language changes)
  - `placeholder`: Object with data required by the chosen template (see placeholders in [available templates](https://github.com/firlast/derxapi-docs/blob/main/docs/templates.md))

```python
import requests

DERXAPI_TOKEN = '<your_token>'

req = requests.post(
    url='https://service.derxapi.com/send-code',
    headers={'X-API-Token': DERXAPI_TOKEN},
    json={
        'email': 'useremail@domain.com',
        'template': {
            'name': '001',
            'placeholder': {
                'company': 'company_name'
            }
        } 
    }
)

# Get your code request ID
req.json()['request_id']
```

See also an example with the template language selection with DerxAPI (check if your plan accepts the language change):

```python
import requests

DERXAPI_TOKEN = '<your_token>'

req = requests.post(
    url='https://service.derxapi.com/send-code',
    headers={'X-API-Token': DERXAPI_TOKEN},
    json={
        'email': 'usermail@domain.com',
        'template': {
            'name': '001',
            'lang': 'pt-br',  # Select Portuguese (BR)
            'placeholder': {
                'company': 'company_name'
            }
        } 
    }
)
```

After that, the confirmation code is created and sent to your user's email. It is necessary to store the `request_id` received from the `service.derxapi.com/send-code` endpoint **to check the code** entered by the user later.

## Confirming the codes

To confirm codes sent to your customers, use the `service.derxapi.com/check-code` endpoint with the `GET` method with the following URL parameters:

- `request_id`: Request ID (obtained when creating the code in `/send-code`)
- `code`: Code entered by your user

```python
import requests

DERXAPI_TOKEN = '<your_token>'

req = requests.get(
    url='https://service.derxapi.com/check-code',
    headers={'X-API-Token': DERXAPI_TOKEN},
    params={
        'request_id': '<request_id>',
        'code': '<code_inserted_by_user>'
    }
)

# Check that the code entered is correct

if req.status_code == 418:  # The code entered is incorrect or has expired
    print('The code is invalid or expired!')
else:
    print('Email confirmed!')
```

With this, you can confirm the code entered by the user and check whether it is correct or not. You will always receive HTTP status `418` if the code is incorrect or has expired

> The expiration time for a code in this version of the API is 5 minutes. Future versions will allow you to change the expiration time.
