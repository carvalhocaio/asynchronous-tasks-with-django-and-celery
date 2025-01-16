# Django Celery Feedback

Este projeto é um exemplo de como integrar o Celery com Django para processar tarefas assíncronas, como o envio de e-mails de feedback.


## Configuração

### Requisitos

- Python 3.x
- Redis

### Instalação

1. Clone o repositório:
    ```sh
    git clone <URL_DO_REPOSITORIO>
    cd django-celery-feedback
    ```

2. Crie e ative um ambiente virtual:
    ```sh
    python -m venv venv
    source venv/bin/activate  # No Windows use `venv\Scripts\activate`
    ```

3. Instale as dependências:
    ```sh
    pip install -r requirements.txt
    ```

4. Configure o Redis e inicie o servidor Redis.

### Configuração do Celery

O Celery é configurado no arquivo `celery.py`e integrado ao Django no arquivo `__init__.py`.

### Executando o Projeto

1. Inicie o servidor Django:
    ```sh
    python manage.py runserver
    ```

2. Inicie o worker do Celery:
    ```sh
    python -m celery -A django_celery worker -l info
    ```

## Funcionalidades

- Envio de e-mails de feedback de forma assíncrona utilizando Celery.
- Formulário de feedback com validação de e-mail e mensagem.

## Estrutura de Arquivos

- **django_celery**: Diretório principal do projeto Django.
- **feedback**: Aplicação Django responsável pelo formulário de feedback e envio de e-mails.
- `templates/`: Diretório contendo os templates HTML.

